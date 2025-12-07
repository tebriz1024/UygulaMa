<!doctype html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Odak Öğrenci Uygulaması</title>
    <style>
        /* Ana Renk Temelleri */
        :root {
            --bg-light: #ffffff;
            --text-light: #1f2937;
            --card-light: #f3f4f6;
            --border-light: #e5e7eb;
            --shadow-light: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            /* Başlangıç Renkleri (Mavi) */
            --primary-base: 75 85 99; /* Tailwind gray-700 / Dark mode primary için */
            --primary-light: #4f46e5; /* Indigo-600 */
            --accent-light: #10b981; /* Emerald-500 */
        }
        [data-theme="dark"] {
            --bg-light: #1f2937;
            --text-light: #f3f4f6;
            --card-light: #374151;
            --border-light: #4b5563;
            --shadow-light: 0 4px 6px -1px rgba(0, 0, 0, 0.4);
            /* Dark mode'da primary rengi daha parlak yap */
            --primary-light: var(--primary-dark-override, #a78bfa); /* Varsayılan Mor (Violet-400) */
            --accent-light: var(--accent-dark-override, #34d399); /* Varsayılan Yeşil (Emerald-400) */
        }

        /* Renk Temaları */
        [data-color="red"] {
            --primary-light: #ef4444; /* Kırmızı-500 */
            --accent-light: #f97316; /* Turuncu-500 */
            --primary-dark-override: #fca5a5; /* Kırmızı-300 */
            --accent-dark-override: #fdb076; /* Turuncu-300 */
        }
        [data-color="blue"] {
            --primary-light: #3b82f6; /* Mavi-500 */
            --accent-light: #06b6d4; /* Turkuaz-500 */
            --primary-dark-override: #93c5fd; /* Mavi-300 */
            --accent-dark-override: #67e8f9; /* Turkuaz-300 */
        }
        [data-color="green"] {
            --primary-light: #10b981; /* Zümrüt-500 */
            --accent-light: #84cc16; /* Limon-500 */
            --primary-dark-override: #6ee7b7; /* Zümrüt-300 */
            --accent-dark-override: #bef264; /* Limon-300 */
        }
        [data-color="purple"] {
            --primary-light: #8b5cf6; /* Mor-500 */
            --accent-light: #ec4899; /* Pembe-500 */
            --primary-dark-override: #c4b5fd; /* Mor-300 */
            --accent-dark-override: #f472b6; /* Pembe-300 */
        }

        /* Mevcut CSS kodu (önceki koddan gelenler) */
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            background-color: var(--bg-light);
            color: var(--text-light);
            transition: background-color 0.3s, color 0.3s;
            line-height: 1.6;
        }
        #app { max-width: 600px; margin: 0 auto; padding-bottom: 70px; }
        header { display: flex; justify-content: space-between; align-items: center; padding: 1rem; border-bottom: 1px solid var(--border-light); background-color: var(--card-light); }
        header h1 { font-size: 1.5rem; font-weight: 700; }
        .theme-toggle { cursor: pointer; padding: 0.5rem; border: none; background: none; color: var(--text-light); border-radius: 50%; transition: background 0.2s; }
        .theme-toggle:hover { background-color: var(--border-light); }
        .content-section { padding: 1rem; display: none; }
        .content-section.active { display: block; }
        .card { background-color: var(--card-light); border: 1px solid var(--border-light); border-radius: 8px; padding: 1rem; margin-bottom: 1rem; box-shadow: var(--shadow-light); }

        /* Pomodoro */
        #timer-display { font-size: 4rem; font-weight: 700; text-align: center; margin: 1rem 0; color: var(--primary-light); }
        .timer-presets button { background-color: var(--primary-light); color: white; border: none; padding: 0.5rem 1rem; margin: 0 0.25rem; border-radius: 4px; cursor: pointer; transition: opacity 0.2s; }
        .timer-presets button:hover { opacity: 0.8; }
        .timer-controls { display: flex; justify-content: center; gap: 1rem; margin-top: 1rem; }
        .timer-controls button { background-color: var(--accent-light); color: white; border: none; padding: 0.75rem 1.5rem; border-radius: 9999px; cursor: pointer; font-size: 1rem; font-weight: 600; }
        .timer-controls button:nth-child(2) { background-color: #ef4444; } /* Stop */
        .timer-controls button:nth-child(3) { background-color: #6b7280; } /* Reset */

        /* İstatistik */
        .stat-item { display: flex; justify-content: space-between; padding: 0.5rem 0; border-bottom: 1px dashed var(--border-light); }
        .stat-item:last-child { border-bottom: none; }
        #weekly-chart { height: 100px; display: flex; align-items: flex-end; gap: 4px; padding-top: 10px; }
        .chart-bar { flex-grow: 1; background-color: var(--accent-light); border-radius: 2px 2px 0 0; transition: height 0.5s; position: relative; }
        .chart-bar span { position: absolute; top: -1.2em; left: 50%; transform: translateX(-50%); font-size: 0.75rem; }

        /* Notlar/Görevler */
        #new-note-form input, #new-note-form textarea, #note-search, #new-task-form input {
            width: 100%; padding: 0.5rem; margin-bottom: 0.5rem; border: 1px solid var(--border-light); border-radius: 4px; background-color: var(--bg-light); color: var(--text-light);
        }
        #tasks-list .task-item { display: flex; align-items: center; justify-content: space-between; padding: 0.75rem 0; border-bottom: 1px solid var(--border-light); }
        .task-item:last-child { border-bottom: none; }
        .task-item.completed span { text-decoration: line-through; color: #6b7280; }

        /* Ayarlar */
        .settings-grid { display: grid; grid-template-columns: 1fr; gap: 1rem; }
        .color-preset {
            display: flex; gap: 0.5rem; margin-top: 0.5rem;
            justify-content: center;
        }
        .color-preset button {
            width: 30px; height: 30px; border-radius: 50%; border: 3px solid transparent; cursor: pointer; transition: border-color 0.2s;
        }
        .color-preset button.active { border-color: var(--text-light); }

        /* Footer / Navigasyon (Ayarlar butonu eklendi) */
        nav { position: fixed; bottom: 0; left: 0; right: 0; height: 60px; background-color: var(--card-light); border-top: 1px solid var(--border-light); display: flex; justify-content: space-around; align-items: center; z-index: 10; }
        nav button { background: none; border: none; color: var(--text-light); cursor: pointer; display: flex; flex-direction: column; align-items: center; padding: 0.5rem; font-size: 0.75rem; opacity: 0.7; transition: opacity 0.2s, color 0.2s; }
        nav button.active { color: var(--primary-light); opacity: 1; }
        nav button svg { width: 24px; height: 24px; margin-bottom: 2px; }

        /* Odak Modu ve Konfeti (Mevcut) */
        #focus-mode { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.95); display: none; flex-direction: column; justify-content: center; align-items: center; color: white; z-index: 20; }
        #focus-mode.active { display: flex; }
        .confetti { position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: 100; pointer-events: none; }
        .confetti-piece { position: absolute; width: 10px; height: 20px; background: var(--primary-light); opacity: 0; animation: confetti-fall 3s ease-in-out infinite; }
        @keyframes confetti-fall { 0% { transform: translateY(-100vh) rotate(0deg); opacity: 1; } 100% { transform: translateY(100vh) rotate(720deg); opacity: 0; } }

        /* Yardımcı Sınıflar (Mevcut) */
        .text-center { text-align: center; }
        .mt-1 { margin-top: 0.5rem; }
        .mb-1 { margin-bottom: 0.5rem; }
        .flex-grow { flex-grow: 1; }
        button:disabled { opacity: 0.5; cursor: not-allowed; }
    </style>
</head>
<body data-theme="light" data-color="blue">
    <div id="app">
        <header>
            <h1 id="header-title">Odak Merkezi</h1>
            <button class="theme-toggle" aria-label="Tema Değiştir" onclick="toggleTheme()">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24">
                    <path d="M12 2.25a.75.75 0 0 1 .75.75v2.25a.75.75 0 0 1-1.5 0V3a.75.75 0 0 1 .75-.75ZM7.5 12a4.5 4.5 0 1 1 9 0 4.5 4.5 0 0 1-9 0ZM18.894 6.166a.75.75 0 0 0-1.06-1.06l-1.597 1.597a.75.75 0 1 0 1.06 1.06l1.596-1.597ZM3.27 6.166l1.597 1.597a.75.75 0 0 0 1.06-1.06L4.33 5.106a.75.75 0 0 0-1.06 1.06Zm12.062 14.332a.75.75 0 1 0 1.06-1.06l-1.597-1.597a.75.75 0 0 0-1.06 1.06l1.597 1.597ZM4.33 18.894l1.597-1.597a.75.75 0 1 0-1.06-1.06L3.27 17.834a.75.75 0 0 0 1.06 1.06ZM12 18a.75.75 0 0 1 .75.75v2.25a.75.75 0 0 1-1.5 0V18.75a.75.75 0 0 1 .75-.75Z"/>
                </svg>
            </button>
        </header>

        <main id="main-content">
            <section id="tab-focus" class="content-section active">
                <div class="card">
                    <h2 class="text-center mb-1" id="pomodoro-title">Pomodoro Zamanlayıcı</h2>
                    <div id="timer-display">25:00</div>
                    <div class="timer-presets text-center mb-1">
                        <button onclick="setPreset(25, 5)">25/5</button>
                        <button onclick="setPreset(50, 10)">50/10</button>
                        <input type="number" id="custom-minutes" placeholder="Özel dk" min="1" max="180" style="width: 80px; padding: 0.4rem; border-radius: 4px;">
                        <button onclick="setCustomTime()" id="set-time-btn">Ayarla</button>
                        <input type="number" id="cycle-count-input" placeholder="Döngü Sayısı" min="1" max="10" value="4" style="width: 110px; padding: 0.4rem; border-radius: 4px; margin-top: 0.5rem;">
                        <span id="cycle-count-label" class="mt-1" style="display: block; font-size: 0.8rem;">Döngü Sayısı: 4</span>
                    </div>
                    <div class="timer-controls">
                        <button id="start-pause-btn" onclick="toggleTimer()">Başlat</button>
                        <button id="stop-btn" onclick="resetTimer()" disabled>Sıfırla</button>
                        <button id="focus-mode-btn" onclick="toggleFocusMode()" disabled>Odak Modu</button>
                    </div>
                    <p class="text-center mt-1" id="timer-status">Hazır (25 dk Odak)</p>
                    <label class="mt-1" style="display: block; text-align: center;">
                        <input type="checkbox" id="silent-mode-checkbox"> <span id="silent-mode-label">Sessiz Mod</span>
                    </label>
                </div>
                <div class="card">
                    <h2 class="mb-1" id="today-progress-title">Bugünkü İlerleme</h2>
                    <div class="stat-item">
                        <span id="completed-session-label">Tamamlanan Odak Seansı</span>
                        <span id="session-count">0</span>
                    </div>
                </div>
            </section>

            <section id="tab-notes" class="content-section">
                <div class="card">
                    <h2 class="mb-1" id="notes-title">Not Defteri</h2>
                    <input type="text" id="note-search" placeholder="Notlarda ara...">
                    <form id="new-note-form" onsubmit="addNote(event)">
                        <input type="text" id="note-title" placeholder="Başlık" required>
                        <textarea id="note-content" placeholder="Not içeriği..." rows="4" required></textarea>
                        <button type="submit" class="mt-1" style="width: 100%; background-color: var(--primary-light); color: white; padding: 0.5rem; border: none; border-radius: 4px; cursor: pointer;" id="add-note-btn">Not Ekle</button>
                    </form>
                    <div id="notes-list" class="mt-1"></div>
                    <button onclick="exportNotes()" class="mt-1" style="width: 100%; background-color: #6b7280; color: white; padding: 0.5rem; border: none; border-radius: 4px; cursor: pointer;" id="download-notes-btn">Tüm Notları İndir (.txt)</button>
                </div>
            </section>

            <section id="tab-tasks" class="content-section">
                <div class="card">
                    <h2 class="mb-1" id="tasks-title">Yapılacaklar Listesi</h2>
                    <form id="new-task-form" style="display: flex;" onsubmit="addTask(event)">
                        <input type="text" id="task-text" placeholder="Yeni görev ekle..." required>
                        <button type="submit" id="add-task-btn">Ekle</button>
                    </form>
                    <div id="tasks-list" class="mt-1"></div>
                </div>
            </section>

            <section id="tab-stats" class="content-section">
                <div class="card">
                    <h2 class="mb-1" id="stats-title">Çalışma İstatistikleri</h2>
                    <div class="stat-item">
                        <span id="daily-sessions-label">Bugün Tamamlanan Seans</span>
                        <span id="daily-sessions-stat">0</span>
                    </div>
                    <h3 class="mt-1 mb-1" id="last-7-days-title">Son 7 Günlük Seans</h3>
                    <div id="weekly-chart"></div>
                    <p style="font-size: 0.75rem; text-align: center;" id="chart-desc">(Soldan sağa: 6 gün önce - Bugün)</p>
                </div>
                <div class="card">
                    <h2 class="mb-1" id="data-management-title">Veri Yönetimi</h2>
                    <button onclick="resetAllDataConfirmation()" style="width: 100%; background-color: #dc2626; color: white; padding: 0.75rem; border: none; border-radius: 4px; cursor: pointer;" id="reset-data-btn">Tüm Verileri Sıfırla (Onay Gerekir)</button>
                </div>
            </section>

            <section id="tab-settings" class="content-section">
                <div class="card">
                    <h2 class="mb-1" id="settings-title">Ayarlar</h2>
                    <div class="settings-grid">
                        <div>
                            <h3 id="language-setting-title">Dil Ayarı</h3>
                            <select id="language-select" onchange="setLanguage(this.value)">
                                <option value="tr">Türkçe</option>
                                <option value="en">English</option>
                            </select>
                        </div>
                        <div>
                            <h3 id="color-setting-title">Uygulama Rengi</h3>
                            <div class="color-preset">
                                <button data-color="blue" style="background-color: #3b82f6;" onclick="setPrimaryColor('blue', this)"></button>
                                <button data-color="red" style="background-color: #ef4444;" onclick="setPrimaryColor('red', this)"></button>
                                <button data-color="green" style="background-color: #10b981;" onclick="setPrimaryColor('green', this)"></button>
                                <button data-color="purple" style="background-color: #8b5cf6;" onclick="setPrimaryColor('purple', this)"></button>
                            </div>
                            <p id="color-info" style="font-size: 0.75rem; margin-top: 0.5rem;">Renk, vurgu ve butonları etkiler.</p>
                        </div>
                        <div>
                            <h3 id="theme-setting-title">Tema</h3>
                            <button onclick="toggleTheme()" id="toggle-theme-btn">Koyu/Açık Tema Değiştir</button>
                        </div>
                        <div>
                            <h3 id="cycle-limit-title">Döngü Sınırı</h3>
                            <input type="number" id="cycle-limit-setting" min="1" max="10" value="4" onchange="setCycleLimit(this.value)" style="width: 100px; padding: 0.4rem; border-radius: 4px;">
                            <p id="cycle-limit-desc" style="font-size: 0.75rem; margin-top: 0.5rem;">Uzun mola öncesi tamamlanacak odak seansı sayısı.</p>
                        </div>
                    </div>
                </div>
            </section>
        </main>

        <div id="focus-mode">
            <div id="focus-timer-mini">25:00</div>
            <div id="fake-visualizer">
                <p id="focus-message">Odaklan! Sadece sen ve dersin var.<br>(Basit Animasyon/Video Alanı)</p>
            </div>
            <button onclick="toggleFocusMode()" style="position: absolute; bottom: 20px; padding: 10px 20px; background-color: rgba(255, 255, 255, 0.2); color: white; border: none; border-radius: 5px;" id="exit-focus-btn">Odak Modundan Çık</button>
        </div>

        <div id="confetti-container" class="confetti"></div>

        <nav>
            <button onclick="changeTab('focus', this)" class="active" aria-label="Odak Zamanlayıcı" data-tab-id="focus">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24"><path fill-rule="evenodd" d="M12 2.25c-5.385 0-9.75 4.365-9.75 9.75s4.365 9.75 9.75 9.75 9.75-4.365 9.75-9.75S17.385 2.25 12 2.25ZM12.75 6a.75.75 0 0 0-1.5 0v6.75a.75.75 0 0 0 .75.75h4.5a.75.75 0 0 0 0-1.5h-3.75V6Z" clip-rule="evenodd" /></svg>
                <span data-lang-key="navFocus">Odak</span>
            </button>
            <button onclick="changeTab('notes', this)" aria-label="Notlar" data-tab-id="notes">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24"><path d="M19.5 7.5a3 3 0 0 0-3-3h-8.25L4.774 2.228a.75.75 0 0 0-.53.228L3.22 3.22a.75.75 0 0 0-.228.53l.968 4.773a.75.75 0 0 0 .75.69h8.341a3 3 0 0 1 3 3v3.75M19.5 7.5a3 3 0 0 0 3 3h-3a3 3 0 0 0-3 3v.375c.026.048.053.096.082.144l2.25 3.375a.75.75 0 1 0 1.259-.839l-2.06-3.09h1.968a3 3 0 0 0 3-3V7.5Zm-14.625.5H4.125a.75.75 0 0 0 0 1.5h.75a.75.75 0 0 0 0-1.5ZM5.625 10.25h.75a.75.75 0 0 1 0 1.5h-.75a.75.75 0 0 1 0-1.5Z" /></svg>
                <span data-lang-key="navNotes">Notlar</span>
            </button>
            <button onclick="changeTab('tasks', this)" aria-label="Görevler" data-tab-id="tasks">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24"><path fill-rule="evenodd" d="M3 5.25a.75.75 0 0 1 .75-.75h16.5a.75.75 0 0 1 0 1.5H3.75A.75.75 0 0 1 3 5.25Zm0 4.5a.75.75 0 0 1 .75-.75h16.5a.75.75 0 0 1 0 1.5H3.75A.75.75 0 0 1 3 9.75Zm0 4.5a.75.75 0 0 1 .75-.75h16.5a.75.75 0 0 1 0 1.5H3.75A.75.75 0 0 1 3 14.25Zm0 4.5a.75.75 0 0 1 .75-.75h16.5a.75.75 0 0 1 0 1.5H3.75A.75.75 0 0 1 3 18.75Z" clip-rule="evenodd" /></svg>
                <span data-lang-key="navTasks">Görev</span>
            </button>
            <button onclick="changeTab('stats', this)" aria-label="İstatistikler" data-tab-id="stats">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24"><path d="M4.5 3.75a.75.75 0 0 0-1.5 0v16.5a.75.75 0 0 0 1.5 0V3.75ZM6 18a.75.75 0 0 1 .75-.75h3.75a.75.75 0 0 1 .75.75V21c0 .414-.336.75-.75.75H6.75A.75.75 0 0 1 6 21v-3ZM10.5 12a.75.75 0 0 1 .75-.75h3.75a.75.75 0 0 1 .75.75V21c0 .414-.336.75-.75.75h-3.75a.75.75 0 0 1-.75-.75v-9ZM15 6a.75.75 0 0 1 .75-.75h3.75a.75.75 0 0 1 .75.75V21c0 .414-.336.75-.75.75h-3.75a.75.75 0 0 1-.75-.75V6Z" /></svg>
                <span data-lang-key="navStats">İstatistik</span>
            </button>
            <button onclick="changeTab('settings', this)" aria-label="Ayarlar" data-tab-id="settings">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24"><path fill-rule="evenodd" d="M11.085 1.77a.75.75 0 0 0-.83 0l-7.5 4.5a.75.75 0 0 0-.31 1.05 13.5 0 0 0 5.4 6.745c.895.52 1.948.52 2.843 0a13.5 13.5 0 0 0 5.4-6.745.75.75 0 0 0-.31-1.05l-7.5-4.5ZM7.5 15.75a.75.75 0 0 0 0 1.5h9a.75.75 0 0 0 0-1.5h-9ZM7.5 19.5a.75.75 0 0 0 0 1.5h9a.75.75 0 0 0 0-1.5h-9Z" clip-rule="evenodd" /></svg>
                <span data-lang-key="navSettings">Ayarlar</span>
            </button>
        </nav>
    </div>

    <script>
        // *** 0. Dil Desteği (i18n) ***
        const translations = {
            tr: {
                headerTitle: "Odak Merkezi",
                navFocus: "Odak",
                navNotes: "Notlar",
                navTasks: "Görev",
                navStats: "İstatistik",
                navSettings: "Ayarlar",
                pomodoroTitle: "Pomodoro Zamanlayıcı",
                customMinPlaceholder: "Özel dk",
                setTimeBtn: "Ayarla",
                cycleCountLabel: "Döngü Sayısı:",
                startBtn: "Başlat",
                pauseBtn: "Durdur",
                resetBtn: "Sıfırla",
                focusModeBtn: "Odak Modu",
                silentModeLabel: "Sessiz Mod",
                readyFocus: (minutes) => `Hazır (${minutes} dk Odak)`,
                focusTime: (f, b, c) => `Odak Süresi (${f}/${b} | Döngü: ${c})`,
                breakTime: (f, b, c) => `Mola Süresi (${f}/${b} | Döngü: ${c})`,
                todayProgressTitle: "Bugünkü İlerleme",
                completedSessionLabel: "Tamamlanan Odak Seansı",
                notesTitle: "Not Defteri",
                noteSearchPlaceholder: "Notlarda ara...",
                noteTitlePlaceholder: "Başlık",
                noteContentPlaceholder: "Not içeriği...",
                addNoteBtn: "Not Ekle",
                downloadNotesBtn: "Tüm Notları İndir (.txt)",
                tasksTitle: "Yapılacaklar Listesi",
                newTaskPlaceholder: "Yeni görev ekle...",
                addTaskBtn: "Ekle",
                statsTitle: "Çalışma İstatistikleri",
                dailySessionsLabel: "Bugün Tamamlanan Seans",
                last7DaysTitle: "Son 7 Günlük Seans",
                chartDesc: "(Soldan sağa: 6 gün önce - Bugün)",
                dataManagementTitle: "Veri Yönetimi",
                resetDataBtn: "Tüm Verileri Sıfırla (Onay Gerekir)",
                settingsTitle: "Ayarlar",
                languageSettingTitle: "Dil Ayarı",
                colorSettingTitle: "Uygulama Rengi",
                colorInfo: "Renk, vurgu ve butonları etkiler.",
                themeSettingTitle: "Tema",
                toggleThemeBtn: "Koyu/Açık Tema Değiştir",
                cycleLimitTitle: "Döngü Sınırı",
                cycleLimitDesc: "Uzun mola öncesi tamamlanacak odak seansı sayısı.",
                focusMessage: "Odaklan! Sadece sen ve dersin var.",
                exitFocusBtn: "Odak Modundan Çık",
                alertValidMinutes: "Lütfen geçerli bir dakika değeri girin.",
                confirmResetTimer: "Zamanlayıcıyı sıfırlamak istediğinizden emin misiniz?",
                confirmDeleteNote: "Bu notu silmek istediğinizden emin misiniz?",
                confirmDeleteTask: "Bu görevi silmek istediğinizden emin misiniz?",
                promptEditTask: "Görevi düzenle:",
                alertStartTimer: "Lütfen zamanlayıcıyı başlatın.",
                confirmSessionEndFocus: (isFocus) => `${isFocus ? 'Odak' : 'Mola'} süresi bitti. Yeni seansı hemen başlatmak ister misiniz?`,
                alertDataReset: "Tüm veriler sıfırlandı. Uygulama yeniden yüklenecek.",
                confirmResetData: "UYARI: Tüm uygulama verilerinizi (Pomodoro, İstatistikler, Notlar, Görevler) kalıcı olarak silmek istediğinizden emin misiniz? Bu işlem geri alınamaz."
            },
            en: {
                headerTitle: "Focus Center",
                navFocus: "Focus",
                navNotes: "Notes",
                navTasks: "Tasks",
                navStats: "Stats",
                navSettings: "Settings",
                pomodoroTitle: "Pomodoro Timer",
                customMinPlaceholder: "Custom min",
                setTimeBtn: "Set",
                cycleCountLabel: "Cycle Count:",
                startBtn: "Start",
                pauseBtn: "Pause",
                resetBtn: "Reset",
                focusModeBtn: "Focus Mode",
                silentModeLabel: "Silent Mode",
                readyFocus: (minutes) => `Ready (${minutes} min Focus)`,
                focusTime: (f, b, c) => `Focus Time (${f}/${b} | Cycle: ${c})`,
                breakTime: (f, b, c) => `Break Time (${f}/${b} | Cycle: ${c})`,
                todayProgressTitle: "Today's Progress",
                completedSessionLabel: "Completed Focus Sessions",
                notesTitle: "Notebook",
                noteSearchPlaceholder: "Search notes...",
                noteTitlePlaceholder: "Title",
                noteContentPlaceholder: "Note content...",
                addNoteBtn: "Add Note",
                downloadNotesBtn: "Download All Notes (.txt)",
                tasksTitle: "To-Do List",
                newTaskPlaceholder: "Add new task...",
                addTaskBtn: "Add",
                statsTitle: "Work Statistics",
                dailySessionsLabel: "Sessions Completed Today",
                last7DaysTitle: "Last 7 Days Sessions",
                chartDesc: "(Left to right: 6 days ago - Today)",
                dataManagementTitle: "Data Management",
                resetDataBtn: "Reset All Data (Confirmation Required)",
                settingsTitle: "Settings",
                languageSettingTitle: "Language Setting",
                colorSettingTitle: "App Color",
                colorInfo: "Color affects accent and buttons.",
                themeSettingTitle: "Theme",
                toggleThemeBtn: "Toggle Dark/Light Theme",
                cycleLimitTitle: "Cycle Limit",
                cycleLimitDesc: "Number of focus sessions before a long break.",
                focusMessage: "Focus! It's just you and your studies.",
                exitFocusBtn: "Exit Focus Mode",
                alertValidMinutes: "Please enter a valid minute value.",
                confirmResetTimer: "Are you sure you want to reset the timer?",
                confirmDeleteNote: "Are you sure you want to delete this note?",
                confirmDeleteTask: "Are you sure you want to delete this task?",
                promptEditTask: "Edit task:",
                alertStartTimer: "Please start the timer.",
                confirmSessionEndFocus: (isFocus) => `${isFocus ? 'Focus' : 'Break'} time is over. Would you like to start the new session immediately?`,
                alertDataReset: "All data has been reset. The application will reload.",
                confirmResetData: "WARNING: Are you sure you want to permanently delete all application data (Pomodoro, Stats, Notes, Tasks)? This action is irreversible."
            }
        };

        function setLanguage(lang) {
            const t = translations[lang];
            if (!t) return;
            appData.language = lang;
            if (checkLocalStorage()) saveData();

            // Genel başlık
            document.getElementById('header-title').textContent = t.headerTitle;

            // Navigasyon butonları
            document.querySelector('[data-tab-id="focus"] span').textContent = t.navFocus;
            document.querySelector('[data-tab-id="notes"] span').textContent = t.navNotes;
            document.querySelector('[data-tab-id="tasks"] span').textContent = t.navTasks;
            document.querySelector('[data-tab-id="stats"] span').textContent = t.navStats;
            document.querySelector('[data-tab-id="settings"] span').textContent = t.navSettings;
            
            // Focus Sekmesi
            document.getElementById('pomodoro-title').textContent = t.pomodoroTitle;
            document.getElementById('custom-minutes').placeholder = t.customMinPlaceholder;
            document.getElementById('set-time-btn').textContent = t.setTimeBtn;
            document.getElementById('start-pause-btn').textContent = isRunning ? t.pauseBtn : t.startBtn;
            document.getElementById('stop-btn').textContent = t.resetBtn;
            document.getElementById('focus-mode-btn').textContent = t.focusModeBtn;
            document.getElementById('silent-mode-label').textContent = t.silentModeLabel;
            document.getElementById('today-progress-title').textContent = t.todayProgressTitle;
            document.getElementById('completed-session-label').textContent = t.completedSessionLabel;
            document.getElementById('cycle-count-label').textContent = `${t.cycleCountLabel} ${appData.timer.cycleLimit}`;

            // Notes Sekmesi
            document.getElementById('notes-title').textContent = t.notesTitle;
            document.getElementById('note-search').placeholder = t.noteSearchPlaceholder;
            document.getElementById('note-title').placeholder = t.noteTitlePlaceholder;
            document.getElementById('note-content').placeholder = t.noteContentPlaceholder;
            document.getElementById('add-note-btn').textContent = t.addNoteBtn;
            document.getElementById('download-notes-btn').textContent = t.downloadNotesBtn;

            // Tasks Sekmesi
            document.getElementById('tasks-title').textContent = t.tasksTitle;
            document.getElementById('task-text').placeholder = t.newTaskPlaceholder;
            document.getElementById('add-task-btn').textContent = t.addTaskBtn;

            // Stats Sekmesi
            document.getElementById('stats-title').textContent = t.statsTitle;
            document.getElementById('daily-sessions-label').textContent = t.dailySessionsLabel;
            document.getElementById('last-7-days-title').textContent = t.last7DaysTitle;
            document.getElementById('chart-desc').textContent = t.chartDesc;
            document.getElementById('data-management-title').textContent = t.dataManagementTitle;
            document.getElementById('reset-data-btn').textContent = t.resetDataBtn;
            
            // Settings Sekmesi
            document.getElementById('settings-title').textContent = t.settingsTitle;
            document.getElementById('language-setting-title').textContent = t.languageSettingTitle;
            document.getElementById('color-setting-title').textContent = t.colorSettingTitle;
            document.getElementById('color-info').textContent = t.colorInfo;
            document.getElementById('theme-setting-title').textContent = t.themeSettingTitle;
            document.getElementById('toggle-theme-btn').textContent = t.toggleThemeBtn;
            document.getElementById('cycle-limit-title').textContent = t.cycleLimitTitle;
            document.getElementById('cycle-limit-desc').textContent = t.cycleLimitDesc;

            // Focus Mode
            document.getElementById('focus-message').innerHTML = t.focusMessage;
            document.getElementById('exit-focus-btn').textContent = t.exitFocusBtn;


            updateDisplay(); // Zamanlayıcı durumunu güncelle
            renderNotes(); // Notları yeniden render et (Sil/İndir butonları için)
            renderTasks(); // Görevleri yeniden render et (Sil/Düzenle butonları için)
        }

        // *** 1. Veri Yönetimi ve Başlangıç Ayarları ***
        const STORAGE_KEY = 'ogrenci_app_v2'; // Versiyonu yükselttim
        let appData = {};

        function checkLocalStorage() {
            try {
                localStorage.setItem('test', 'test');
                localStorage.removeItem('test');
                return true;
            } catch (e) {
                return false;
            }
        }
        
        const isLocalStorageAvailable = checkLocalStorage();

        function loadData() {
            if (!isLocalStorageAvailable) {
                 appData = {
                    timer: { initialMinutes: 25, isFocus: true, breakMinutes: 5, cycle: 1, cycleLimit: 4 },
                    stats: { todaySessions: 0, dailySessions: Array(7).fill(0) },
                    notes: [],
                    tasks: [],
                    language: 'tr',
                    color: 'blue'
                 };
                 return;
            }
            
            const storedData = localStorage.getItem(STORAGE_KEY);
            if (storedData) {
                appData = JSON.parse(storedData);
                // Varsayılan değerleri kontrol et ve yeni alanları ekle
                if (!appData.timer) appData.timer = { initialMinutes: 25, isFocus: true, breakMinutes: 5, cycle: 1, cycleLimit: 4 };
                if (!appData.timer.cycleLimit) appData.timer.cycleLimit = 4;
                if (!appData.stats) appData.stats = { todaySessions: 0, dailySessions: Array(7).fill(0) };
                if (!appData.notes) appData.notes = [];
                if (!appData.tasks) appData.tasks = [];
                if (!appData.language) appData.language = 'tr';
                if (!appData.color) appData.color = 'blue';

                // Günlük veriyi sıfırlama/kaydırma kontrolü
                const lastLogin = appData.lastLogin || new Date().toISOString().split('T')[0];
                const today = new Date().toISOString().split('T')[0];

                if (lastLogin !== today) {
                    // Yeni gün, bugünün seansını sıfırla ve istatistikleri kaydır
                    appData.stats.dailySessions.shift(); // En eski günü çıkar
                    appData.stats.dailySessions.push(appData.stats.todaySessions); // Dünkü seansı ekle
                    appData.stats.todaySessions = 0; // Bugün sıfırla
                    appData.lastLogin = today;
                    saveData();
                }
            } else {
                // İlk açılış
                appData = {
                    timer: { initialMinutes: 25, isFocus: true, breakMinutes: 5, cycle: 1, cycleLimit: 4 },
                    stats: { todaySessions: 0, dailySessions: Array(7).fill(0) },
                    notes: [],
                    tasks: [],
                    lastLogin: new Date().toISOString().split('T')[0],
                    language: 'tr',
                    color: 'blue'
                };
            }
        }

        function saveData() {
            if (isLocalStorageAvailable) {
                 localStorage.setItem(STORAGE_KEY, JSON.stringify(appData));
            }
        }

        loadData();

        // *** 2. Tema ve Renk Yönetimi ***
        function toggleTheme() {
            const body = document.body;
            const currentTheme = body.getAttribute('data-theme');
            const newTheme = currentTheme === 'light' ? 'dark' : 'light';
            body.setAttribute('data-theme', newTheme);
            if (isLocalStorageAvailable) localStorage.setItem('theme', newTheme);
        }

        function setPrimaryColor(color, button) {
            document.body.setAttribute('data-color', color);
            appData.color = color;
            if (isLocalStorageAvailable) saveData();

            document.querySelectorAll('.color-preset button').forEach(btn => {
                btn.classList.remove('active');
            });
            if (button) button.classList.add('active');
        }

        // Tema ve Renk yükleme
        const storedTheme = isLocalStorageAvailable ? localStorage.getItem('theme') : null;
        if (storedTheme) {
            document.body.setAttribute('data-theme', storedTheme);
        } else {
            document.body.setAttribute('data-theme', 'light');
        }
        
        // Renk yükleme
        setPrimaryColor(appData.color, null);

        // *** 3. Navigasyon / Sekme Yönetimi ***
        function changeTab(tabId, button) {
            document.querySelectorAll('.content-section').forEach(section => {
                section.classList.remove('active');
            });
            document.getElementById(`tab-${tabId}`).classList.add('active');

            document.querySelectorAll('nav button').forEach(btn => {
                btn.classList.remove('active');
            });
            button.classList.add('active');

            if (tabId === 'stats') {
                renderStats();
            } else if (tabId === 'settings') {
                // Ayarlar yüklendiğinde mevcut değerleri göster
                document.getElementById('language-select').value = appData.language;
                document.getElementById('cycle-limit-setting').value = appData.timer.cycleLimit;
                document.getElementById('cycle-count-label').textContent = `${translations[appData.language].cycleCountLabel} ${appData.timer.cycleLimit}`;
                document.querySelectorAll('.color-preset button').forEach(btn => {
                    if (btn.getAttribute('data-color') === appData.color) {
                        btn.classList.add('active');
                    } else {
                        btn.classList.remove('active');
                    }
                });
            }
        }

        // *** 4. Pomodoro Zamanlayıcı ***
        let timerInterval;
        let totalSeconds;
        let remainingSeconds;
        let isRunning = false;
        let lastTimestamp;

        const timerDisplay = document.getElementById('timer-display');
        const startPauseBtn = document.getElementById('start-pause-btn');
        const stopBtn = document.getElementById('stop-btn');
        const focusModeBtn = document.getElementById('focus-mode-btn');
        const timerStatus = document.getElementById('timer-status');
        const focusTimerMini = document.getElementById('focus-timer-mini');
        const silentModeCheckbox = document.getElementById('silent-mode-checkbox');

        function formatTime(totalSec) {
            const minutes = Math.floor(totalSec / 60);
            const seconds = totalSec % 60;
            return `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
        }

        function setPreset(focusMins, breakMins) {
            appData.timer.initialMinutes = focusMins;
            appData.timer.breakMinutes = breakMins;
            appData.timer.isFocus = true;
            appData.timer.cycle = 1;
            resetTimer(false);
            if (isLocalStorageAvailable) saveData();
        }

        function setCustomTime() {
            const customMinsInput = document.getElementById('custom-minutes');
            const customMins = parseInt(customMinsInput.value);
            if (customMins > 0) {
                appData.timer.initialMinutes = customMins;
                appData.timer.breakMinutes = Math.max(5, Math.floor(customMins / 5)); // Özel arayı otomatik hesapla, min 5
                appData.timer.isFocus = true;
                appData.timer.cycle = 1;
                resetTimer(false);
                customMinsInput.value = ''; // Girişi temizle
                if (isLocalStorageAvailable) saveData();
            } else {
                alert(translations[appData.language].alertValidMinutes);
            }
        }
        
        function setCycleLimit(limit) {
            const cycleLimit = parseInt(limit);
            if (cycleLimit >= 1 && cycleLimit <= 10) {
                appData.timer.cycleLimit = cycleLimit;
                document.getElementById('cycle-count-input').value = cycleLimit;
                document.getElementById('cycle-limit-setting').value = cycleLimit;
                document.getElementById('cycle-count-label').textContent = `${translations[appData.language].cycleCountLabel} ${cycleLimit}`;
                if (isLocalStorageAvailable) saveData();
            }
        }

        function updateDisplay() {
            const t = translations[appData.language];
            timerDisplay.textContent = formatTime(remainingSeconds);
            focusTimerMini.textContent = formatTime(remainingSeconds);
            document.title = `${formatTime(remainingSeconds)} - ${appData.timer.isFocus ? t.navFocus : 'Mola'} | ${t.headerTitle}`;
     const initialMins = appData.timer.initialMinutes;
            const breakMins = appData.timer.breakMinutes;
            const currentCycle = appData.timer.cycle;

            if (appData.timer.isFocus) {
                timerStatus.textContent = t.focusTime(initialMins, breakMins, currentCycle);
            } else {
                timerStatus.textContent = t.breakTime(initialMins, breakMins, currentCycle);
            }
        }

        function timerLoop(timestamp) {
            if (!isRunning) return;

            if (!lastTimestamp) lastTimestamp = timestamp;
            const elapsed = timestamp - lastTimestamp;
            
            // 1 saniyeden fazla zaman geçtiyse kalan süreyi düşür
            if (elapsed >= 1000) {
                remainingSeconds -= Math.floor(elapsed / 1000);
                lastTimestamp += Math.floor(elapsed / 1000) * 1000;
            }

            if (remainingSeconds <= 0) {
                endSession();
                return;
            }

            updateDisplay();
            requestAnimationFrame(timerLoop);
        }

        function toggleTimer() {
            const t = translations[appData.language];
            if (isRunning) {
                // Durdur
                isRunning = false;
                cancelAnimationFrame(timerInterval);
                startPauseBtn.textContent = t.startBtn;
                stopBtn.disabled = false;
                focusModeBtn.disabled = false;
                lastTimestamp = null; // Zamanlayıcının kaymasını engelle
            } else {
                // Başlat
                if (remainingSeconds === undefined || remainingSeconds <= 0) {
                    remainingSeconds = appData.timer.initialMinutes * 60;
                }
                isRunning = true;
                startPauseBtn.textContent = t.pauseBtn;
                stopBtn.disabled = false;
                focusModeBtn.disabled = false;
                // requestAnimationFrame ile zamanlayıcıyı başlat
                timerInterval = requestAnimationFrame(timerLoop);
                if (isLocalStorageAvailable) saveData();
            }
        }

        function resetTimer(confirm = true) {
            const t = translations[appData.language];
            if (confirm && isRunning && !window.confirm(t.confirmResetTimer)) {
                return;
            }

            isRunning = false;
            cancelAnimationFrame(timerInterval);
            lastTimestamp = null;
            
            // Başlangıç süresini ayarla (Odak süresi)
            const initialTime = appData.timer.initialMinutes * 60;
            remainingSeconds = initialTime;
            
            startPauseBtn.textContent = t.startBtn;
            stopBtn.disabled = true;
            focusModeBtn.disabled = true;

            // Odak modu açıksa kapat
            if (document.getElementById('focus-mode').classList.contains('active')) {
                toggleFocusMode();
            }

            // Durumu güncelleyip kaydet
            appData.timer.isFocus = true;
            appData.timer.cycle = 1;
            
            // Hazır durum mesajını güncelleyin
            timerStatus.textContent = t.readyFocus(appData.timer.initialMinutes);
            timerDisplay.textContent = formatTime(initialTime);
            focusTimerMini.textContent = formatTime(initialTime);
            document.title = `${formatTime(initialTime)} - ${t.navFocus} | ${t.headerTitle}`;

            if (isLocalStorageAvailable) saveData();
        }

        function endSession() {
            isRunning = false;
            cancelAnimationFrame(timerInterval);
            lastTimestamp = null;
            playSound(!silentModeCheckbox.checked);

            if (appData.timer.isFocus) {
                // Odak bitti
                appData.stats.todaySessions += 1; // İlerlemeyi artır
                appData.timer.isFocus = false;
                
                // Mola süresini ayarla
                let breakTime;
                if (appData.timer.cycle < appData.timer.cycleLimit) {
                    breakTime = appData.timer.breakMinutes * 60; // Kısa Mola
                    appData.timer.cycle++;
                } else {
                    breakTime = 20 * 60; // Uzun Mola (Döngü Sınırı sonrası)
                    appData.timer.cycle = 1; // Döngüyü sıfırla
                    triggerConfetti();
                }
                remainingSeconds = breakTime;

                renderStats(); // İstatistikleri güncelle
            } else {
                // Mola bitti, Odak'a dön
                appData.timer.isFocus = true;
                remainingSeconds = appData.timer.initialMinutes * 60;
            }
            
            updateDisplay();
            if (isLocalStorageAvailable) saveData();

            const t = translations[appData.language];
            // Yeni seansı otomatik başlat
            if (window.confirm(t.confirmSessionEndFocus(appData.timer.isFocus))) {
                toggleTimer();
            } else {
                 startPauseBtn.textContent = t.startBtn;
                 stopBtn.disabled = true;
                 focusModeBtn.disabled = true;
                 // Manuel başlatmayı beklerken display'i güncelle
                 updateDisplay(); 
            }
            
             // Odak modu açıksa güncelle
            if (document.getElementById('focus-mode').classList.contains('active')) {
                toggleFocusMode(); // Kapat
                if (isRunning) toggleFocusMode(); // Yeniden başlatıldıysa tekrar aç
            }

        }

        // Başlangıç yüklemesi
        setLanguage(appData.language);
        document.getElementById('language-select').value = appData.language;
        document.getElementById('cycle-count-input').value = appData.timer.cycleLimit;
        document.getElementById('cycle-limit-setting').value = appData.timer.cycleLimit;
        
        // Timer'ı başlatma (resetTimer içinde dil ayarı yapıldığı için en sona)
        setTimeout(() => resetTimer(false), 0); 


        // *** 5. Odak Modu ***
        function toggleFocusMode() {
            const focusMode = document.getElementById('focus-mode');
            const t = translations[appData.language];
            if (focusMode.classList.contains('active')) {
                focusMode.classList.remove('active');
                document.body.style.overflow = '';
            } else {
                if (!isRunning) {
                    alert(t.alertStartTimer);
                    return;
                }
                focusMode.classList.add('active');
                document.body.style.overflow = 'hidden';
            }
        }

        // *** 6. İstatistikler (Grafik ve Sayılar) ***
        function renderStats() {
            if (!isLocalStorageAvailable) return;
            document.getElementById('session-count').textContent = appData.stats.todaySessions;
            document.getElementById('daily-sessions-stat').textContent = appData.stats.todaySessions;

            const chartContainer = document.getElementById('weekly-chart');
            chartContainer.innerHTML = '';
            const data = appData.stats.dailySessions; // Son 7 gün (0. index = 6 gün önce, 6. index = Dün)
            const todaySessions = appData.stats.todaySessions;
            const fullData = [...data.slice(1), todaySessions]; // 6 gün önceki veriden başla, sonuncusu bugünün verisi

            const maxSessions = Math.max(...fullData, 1); // En az 1

            fullData.forEach((sessions, index) => {
                const bar = document.createElement('div');
                bar.className = 'chart-bar';
                const heightPercentage = (sessions / maxSessions) * 100;
                bar.style.height = `${Math.max(5, heightPercentage)}%`; // Minimum yükseklik 5%

                const sessionsSpan = document.createElement('span');
                sessionsSpan.textContent = sessions;
                bar.appendChild(sessionsSpan);
                chartContainer.appendChild(bar);
            });
        }

        // *** 7. Notlar ***
        function renderNotes(filter = '') {
            if (!isLocalStorageAvailable) return;
            const list = document.getElementById('notes-list');
            list.innerHTML = '';
            const t = translations[appData.language];

            const filteredNotes = appData.notes.filter(note => 
                note.title.toLowerCase().includes(filter.toLowerCase()) || 
                note.content.toLowerCase().includes(filter.toLowerCase())
            );

            filteredNotes.reverse().forEach(note => { // Yenileri üstte göster
                const noteDiv = document.createElement('div');
                noteDiv.className = 'note-item';
                noteDiv.dataset.id = note.id;
                noteDiv.innerHTML = `
                    <h4>${note.title}</h4>
                    <p>${note.content}</p>
                    <div class="note-actions">
                        <button onclick="deleteNote(${note.id})" aria-label="${t.confirmDeleteNote.slice(0, 10)}">Sil</button>
                        <button onclick="exportSingleNote(${note.id})" style="color: var(--accent-light);" aria-label="Notu İndir">İndir</button>
                    </div>
                `;
                list.appendChild(noteDiv);
            });
        }

        function addNote(event) {
            event.preventDefault();
            if (!isLocalStorageAvailable) return;
            const titleInput = document.getElementById('note-title');
            const contentInput = document.getElementById('note-content');

            const newNote = {
                id: Date.now(),
                title: titleInput.value.trim(),
                content: contentInput.value.trim()
            };

            appData.notes.push(newNote);
            saveData();
            renderNotes();
            titleInput.value = '';
            contentInput.value = '';
        }

        function deleteNote(id) {
            const t = translations[appData.language];
            if (!isLocalStorageAvailable || !confirm(t.confirmDeleteNote)) return;
            appData.notes = appData.notes.filter(note => note.id !== id);
            saveData();
            renderNotes();
        }
        
        document.getElementById('note-search').addEventListener('input', (e) => {
            renderNotes(e.target.value);
        });
        
        function exportNotes() {
            if (!isLocalStorageAvailable) return;
            const text = appData.notes.map(n => `--- ${n.title} ---\n${n.content}\n`).join('\n\n');
            downloadFile('all_notes.txt', text);
        }
        
        function exportSingleNote(id) {
            if (!isLocalStorageAvailable) return;
            const note = appData.notes.find(n => n.id === id);
            if(note) {
                 const text = `--- ${note.title} ---\n${note.content}\n`;
                 downloadFile(`${note.title.replace(/[^a-z0-9]/gi, '_')}.txt`, text);
            }
        }
        
        function downloadFile(filename, text) {
            const element = document.createElement('a');
            element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
            element.setAttribute('download', filename);
            element.style.display = 'none';
            document.body.appendChild(element);
            element.click();
            document.body.removeChild(element);
        }

        // *** 8. Görevler ***
        function renderTasks() {
            if (!isLocalStorageAvailable) return;
            const list = document.getElementById('tasks-list');
            list.innerHTML = '';
            const t = translations[appData.language];

            appData.tasks.forEach(task => {
                const taskDiv = document.createElement('div');
                taskDiv.className = `task-item ${task.completed ? 'completed' : ''}`;
                taskDiv.dataset.id = task.id;
                taskDiv.innerHTML = `
                    <label>
                        <input type="checkbox" ${task.completed ? 'checked' : ''} onchange="toggleTaskCompleted(${task.id})">
                        <span class="flex-grow">${task.text}</span>
                    </label>
                    <div class="task-actions">
                        <button onclick="editTask(${task.id})" aria-label="Görevi Düzenle">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" width="16" height="16"><path d="M5.433 13.917V15a.75.75 0 0 0 .75.75h1.083l6.588-6.588a1.5 1.5 0 0 0-2.121-2.121l-6.588 6.588Z" /><path d="M15.113 4.887a2.25 2.25 0 0 0-3.182-3.182l-3.23 3.23a.75.75 0 0 0-.175.247L6.474 8.271a.75.75 0 0 0 .991.991l2.499-.582a.75.75 0 0 0 .247-.175l3.23-3.23ZM12 9a.75.75 0 0 1 .75-.75h4.5a.75.75 0 0 1 0 1.5h-4.5A.75.75 0 0 1 12 9Z" /></svg>
                        </button>
                        <button onclick="deleteTask(${task.id})" aria-label="Görevi Sil">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" width="16" height="16"><path fill-rule="evenodd" d="M8.75 1A.75.75 0 0 0 8 1.75V2h4v-.25A.75.75 0 0 0 11.25 1h-2.5ZM13.5 3.25a.75.75 0 0 0-.75-.75h-5.5a.75.75 0 0 0-.75.75v1.75h7V3.25Z" clip-rule="evenodd" /><path fill-rule="evenodd" d="M19.5 5.5a.75.75 0 0 0-.75-.75h-15a.75.75 0 0 0 0 1.5h15A.75.75 0 0 0 19.5 5.5Zm-1.75 2.25H2.25V18a2.25 2.25 0 0 0 2.25 2.25h11a2.25 2.25 0 0 0 2.25-2.25V7.75Zm-7.25 2.5a.75.75 0 0 0-1.5 0v5a.75.75 0 0 0 1.5 0v-5ZM10 10.5a.75.75 0 0 1 .75.75v5a.75.75 0 0 1-1.5 0v-5a.75.75 0 0 1 .75-.75ZM15.75 10.5a.75.75 0 0 0-1.5 0v5a.75.75 0 0 0 1.5 0v-5Z" clip-rule="evenodd" /></svg>
                        </button>
                    </div>
                `;
                list.appendChild(taskDiv);
            });
        }

        function addTask(event) {
            event.preventDefault();
            if (!isLocalStorageAvailable) return;
            const taskInput = document.getElementById('task-text');
            const taskText = taskInput.value.trim();

            if (taskText) {
                const newTask = {
                    id: Date.now(),
                    text: taskText,
                    completed: false
                };
                appData.tasks.push(newTask);
                saveData();
                renderTasks();
                taskInput.value = '';
            }
        }

        function toggleTaskCompleted(id) {
            if (!isLocalStorageAvailable) return;
            const task = appData.tasks.find(t => t.id === id);
            if (task) {
                task.completed = !task.completed;
                saveData();
                renderTasks();
            }
        }

        function editTask(id) {
            if (!isLocalStorageAvailable) return;
            const t = translations[appData.language];
            const task = appData.tasks.find(t => t.id === id);
            if (task) {
                const newText = prompt(t.promptEditTask, task.text);
                if (newText !== null && newText.trim() !== '') {
                    task.text = newText.trim();
                    saveData();
                    renderTasks();
                }
            }
        }

        function deleteTask(id) {
            const t = translations[appData.language];
            if (!isLocalStorageAvailable || !confirm(t.confirmDeleteTask)) return;
            appData.tasks = appData.tasks.filter(t => t.id !== id);
            saveData();
            renderTasks();
        }

        // *** 9. Ekstralar ***

        // Ses Efekti (Mevcut)
        function playSound(enabled) {
            if (!enabled) return;
            try {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                                const audioCtx = new AudioContext();
                const oscillator = audioCtx.createOscillator();
                const gainNode = audioCtx.createGain();

                oscillator.connect(gainNode);
                gainNode.connect(audioCtx.destination);

                oscillator.type = 'sine';
                oscillator.frequency.setValueAtTime(440, audioCtx.currentTime); // 440 Hz (A4)
                gainNode.gain.setValueAtTime(0.5, audioCtx.currentTime);

                oscillator.start();
                gainNode.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.5); // 0.5 saniyede sönme
                oscillator.stop(audioCtx.currentTime + 0.5);
            } catch (e) {
                console.error("Ses çalınamadı:", e);
            }
        }

        // Konfeti (Mevcut)
        function triggerConfetti() {
            const container = document.getElementById('confetti-container');
            container.innerHTML = '';
            const colors = [
                document.body.getAttribute('data-color') === 'red' ? '#dc2626' : '#4f46e5', // Kırmızı veya Mavi
                document.body.getAttribute('data-color') === 'red' ? '#fca5a5' : '#a78bfa',
                document.body.getAttribute('data-color') === 'green' ? '#10b981' : '#10b981', // Yeşil
                document.body.getAttribute('data-color') === 'green' ? '#34d399' : '#34d399',
                '#facc15' // Sarı
            ];

            for (let i = 0; i < 50; i++) {
                const piece = document.createElement('div');
                piece.className = 'confetti-piece';
                piece.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                piece.style.left = `${Math.random() * 100}vw`;
                piece.style.animationDelay = `${Math.random() * 2}s`;
                piece.style.animationDuration = `${Math.random() * 2 + 3}s`;
                container.appendChild(piece);
            }

            // Animasyon bitince temizle
            setTimeout(() => {
                container.innerHTML = '';
            }, 5000);
        }

        // Klavye Kısayolları (Mevcut)
        document.addEventListener('keydown', (e) => {
            if (document.getElementById('tab-focus').classList.contains('active')) {
                if (e.key === ' ') {
                    e.preventDefault();
                    toggleTimer();
                } else if (e.key === 'r') {
                    e.preventDefault();
                    resetTimer();
                }
            } else if (e.key === 'n' && document.getElementById('tab-notes').classList.contains('active')) {
                 e.preventDefault();
                 document.getElementById('note-title').focus();
            }
        });

        // Tüm Verileri Sıfırla (Mevcut)
        function resetAllDataConfirmation() {
            const t = translations[appData.language];
            if (window.confirm(t.confirmResetData)) {
                if (isLocalStorageAvailable) {
                    localStorage.removeItem(STORAGE_KEY);
                    localStorage.removeItem('theme'); // Temayı da sıfırla
                }
                alert(t.alertDataReset);
                window.location.reload();
            }
        }

        // *** Başlangıç Yüklemeleri ***
        document.addEventListener('DOMContentLoaded', () => {
            // Renk düğmelerini başlangıçta aktif yap
            document.querySelectorAll('.color-preset button').forEach(btn => {
                if (btn.getAttribute('data-color') === appData.color) {
                    btn.classList.add('active');
                }
            });

            renderTasks();
            renderNotes();
            renderStats(); // Başlangıçta da istatistikleri yükle
        });
    </script>
</body>
</html>
