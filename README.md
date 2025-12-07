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
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24"><path fill-rule="evenodd" d="M11.085 1.77a.75.75 0 0 0-.83 0l-7.5 4.5a.75.75 0 0 0-.31 1.05 13.5 
