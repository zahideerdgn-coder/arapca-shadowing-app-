# arapca-shadowing-app-
streamlit run app.py
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Arapça YouTube Shadowing & Dublaj Asistanı</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts for Arabic & Modern UI -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
        }
        .font-arabic {
            font-family: 'Amiri', serif;
            direction: rtl;
        }
        /* Custom scrollbars */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #1e293b;
        }
        ::-webkit-scrollbar-thumb {
            background: #334155;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #475569;
        }
        .pulse-recording {
            animation: pulse-red 1.5s infinite;
        }
        @keyframes pulse-red {
            0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.7); }
            70% { transform: scale(1.05); box-shadow: 0 0 0 10px rgba(239, 68, 68, 0); }
            100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(239, 68, 68, 0); }
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col">

    <!-- Header Navbar -->
    <header class="border-b border-slate-800 bg-slate-900/80 backdrop-blur sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-4">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-emerald-500 to-teal-400 flex items-center justify-center text-slate-950 font-bold shadow-lg shadow-emerald-500/20">
                    <i data-lucide="mic" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight flex items-center gap-2">
                        Arapça Shadowing & Dublaj
                        <span class="text-xs font-semibold px-2 py-0.5 rounded-full bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">TÜBİTAK Modülü</span>
                    </h1>
                    <p class="text-xs text-slate-400">YouTube Destekli Akıllı Dil Eğitimi ve Ses Analizi</p>
                </div>
            </div>

            <!-- Mode Selector Tabs -->
            <div class="flex bg-slate-950 p-1 rounded-xl border border-slate-800 text-xs font-medium">
                <button id="mode-free-btn" onclick="switchMode('free')" class="px-4 py-2 rounded-lg flex items-center gap-2 transition-all bg-emerald-500 text-slate-950 font-semibold shadow">
                    <i data-lucide="play-circle" class="w-4 h-4"></i>
                    1. Aşama: Serbest Dinleme
                </button>
                <button id="mode-shadow-btn" onclick="switchMode('shadow')" class="px-4 py-2 rounded-lg text-slate-400 hover:text-slate-200 flex items-center gap-2 transition-all">
                    <i data-lucide="pause-circle" class="w-4 h-4"></i>
                    2. Aşama: Cümle Cümle Shadowing
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content Container -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 md:p-6 grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- Left Column: Video Player & Settings (7 Cols) -->
        <div class="lg:col-span-7 flex flex-col gap-6">
            
            <!-- YouTube URL Input Card -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 shadow-xl">
                <label class="block text-xs font-semibold text-slate-400 mb-2 flex items-center justify-between">
                    <span>YOUTUBE VIDEO LINKI</span>
                    <span class="text-emerald-400 font-normal hover:underline cursor-pointer" onclick="loadSampleVideo()">Örnek Ders Videosu Yükle</span>
                </label>
                <div class="flex gap-2">
                    <div class="relative flex-1">
                        <i data-lucide="youtube" class="w-5 h-5 absolute left-3 top-1/2 -translate-y-1/2 text-slate-500"></i>
                        <input type="text" id="youtube-url-input" 
                            value="https://www.youtube.com/watch?v=dQw4w9WgXcQ"
                            placeholder="https://www.youtube.com/watch?v=..." 
                            class="w-full bg-slate-950 border border-slate-800 rounded-xl pl-10 pr-4 py-2.5 text-sm focus:outline-none focus:border-emerald-500 text-slate-200 transition">
                    </div>
                    <button onclick="loadYouTubeVideo()" class="bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-semibold px-4 py-2.5 rounded-xl text-sm flex items-center gap-1.5 transition">
                        <i data-lucide="download" class="w-4 h-4"></i>
                        Yükle
                    </button>
                </div>
            </div>

            <!-- YouTube Embedded Player Container -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl overflow-hidden shadow-2xl relative">
                <!-- Video Container Ratio 16:9 -->
                <div class="relative aspect-video bg-black flex items-center justify-center">
                    <div id="player"></div>
                    
                    <!-- Auto Pause Overlay Banner -->
                    <div id="pause-overlay" class="hidden absolute inset-0 bg-slate-950/90 backdrop-blur-sm flex flex-col items-center justify-center p-6 text-center z-10 transition-all">
                        <div class="w-16 h-16 rounded-full bg-amber-500/20 text-amber-400 flex items-center justify-center mb-3 border border-amber-500/30 animate-bounce">
                            <i data-lucide="pause" class="w-8 h-8"></i>
                        </div>
                        <h3 class="text-lg font-bold text-slate-100 mb-1">Video Duraklatıldı!</h3>
                        <p class="text-sm text-slate-400 max-w-md mb-4">Şimdi sıra sizde. Cümleyi yüksek sesle tekrar edip ses kaydınızı yapın.</p>
                        <button onclick="resumePlayer()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 border border-slate-700 text-xs font-semibold px-4 py-2 rounded-lg flex items-center gap-2 transition">
                            <i data-lucide="play" class="w-4 h-4"></i> Videoyu Devam Ettir
                        </button>
                    </div>
                </div>

                <!-- Video Status & Scrub Bar Info -->
                <div class="p-3 bg-slate-900/90 border-t border-slate-800/80 flex items-center justify-between text-xs text-slate-400">
                    <div class="flex items-center gap-2">
                        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-ping"></span>
                        <span id="player-status-text">Hazır</span>
                    </div>
                    <div class="flex items-center gap-4">
                        <span>Zaman: <strong id="current-time-display" class="text-slate-200">00:00</strong></span>
                        <span id="checkpoint-limit-display" class="hidden text-amber-400 font-medium">Duraklama: 00:10</span>
                    </div>
                </div>
            </div>

            <!-- Sentence Checkpoints / Timestamps List -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl flex-1 flex flex-col">
                <div class="flex items-center justify-between mb-4">
                    <div>
                        <h2 class="font-bold text-sm text-slate-200 flex items-center gap-2">
                            <i data-lucide="list-ordered" class="w-4 h-4 text-emerald-400"></i>
                            Cümle / Duraklama Listesi
                        </h2>
                        <p class="text-xs text-slate-400">Videoyu istediğiniz zaman aralıklarına bölüp çalışabilirsiniz.</p>
                    </div>
                    <button onclick="addNewCheckpoint()" class="bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs text-emerald-400 font-semibold px-3 py-1.5 rounded-lg flex items-center gap-1 transition">
                        <i data-lucide="plus" class="w-3.5 h-3.5"></i> Yeni Cümle Ekle
                    </button>
                </div>

                <!-- Scrollable Sentence List -->
                <div id="checkpoint-list" class="space-y-3 max-h-72 overflow-y-auto pr-1">
                    <!-- Checkpoint Items injected by JS -->
                </div>
            </div>

        </div>

        <!-- Right Column: Shadowing & Dubbing Recording Studio (5 Cols) -->
        <div class="lg:col-span-5 flex flex-col gap-6">
            
            <!-- Active Target Sentence Display Card -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl relative overflow-hidden">
                <div class="absolute top-0 right-0 w-32 h-32 bg-emerald-500/5 rounded-full blur-2xl pointer-events-none"></div>

                <div class="flex items-center justify-between mb-3">
                    <span class="text-xs font-semibold uppercase tracking-wider text-emerald-400 bg-emerald-500/10 border border-emerald-500/20 px-2.5 py-1 rounded-md">
                        Aktif Cümle Hedefi
                    </span>
                    <span id="active-sentence-timer" class="text-xs text-slate-400 font-mono">00:00 - 00:08</span>
                </div>

                <!-- Arabic Main Text -->
                <div class="my-4 p-4 bg-slate-950/80 border border-slate-800/80 rounded-xl text-center">
                    <p id="active-arabic-text" class="font-arabic text-2xl md:text-3xl text-emerald-300 leading-relaxed font-bold">
                        مَرْحَبًا، كَيْفَ حَالُكَ اليَوْم؟
                    </p>
                </div>

                <!-- Transliteration & Meaning -->
                <div class="space-y-1.5 text-xs">
                    <p class="text-slate-300">
                        <span class="text-slate-500 font-semibold">Okunuş:</span> 
                        <span id="active-translit-text">Merhaba, keyfe halukel-yevm?</span>
                    </p>
                    <p class="text-slate-300">
                        <span class="text-slate-500 font-semibold">Anlamı:</span> 
                        <span id="active-meaning-text">Merhaba, bugün nasılsın?</span>
                    </p>
                </div>
            </div>

            <!-- Live Microphone Recording Panel -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl flex flex-col items-center justify-center text-center relative">
                
                <h3 class="text-sm font-bold text-slate-200 mb-1">Gölgeleme Kaydı Yap</h3>
                <p class="text-xs text-slate-400 mb-6">Mikrofon ikonuna basıp Arapça cümleyi yüksek sesle okuyun.</p>

                <!-- Record Button with Animation -->
                <button id="record-btn" onclick="toggleRecording()" class="w-20 h-20 rounded-full bg-slate-800 border-2 border-emerald-500/50 hover:border-emerald-400 text-emerald-400 flex items-center justify-center text-slate-100 transition-all shadow-xl hover:scale-105 active:scale-95 mb-4 group relative">
                    <i id="record-icon" data-lucide="mic" class="w-8 h-8 text-emerald-400 group-hover:text-emerald-300 transition"></i>
                </button>

                <p id="record-status-text" class="text-xs font-semibold text-slate-400">Kayda Başlamak İçin Tıklayın</p>
                <span id="record-timer" class="text-xs font-mono text-emerald-400 mt-1 hidden">00:00</span>

                <!-- Recorded Audio Player & Comparison -->
                <div id="audio-result-card" class="w-full mt-6 pt-5 border-t border-slate-800 hidden text-left">
                    <h4 class="text-xs font-bold text-slate-300 mb-2 flex items-center gap-1.5">
                        <i data-lucide="volume-2" class="w-4 h-4 text-emerald-400"></i>
                        Kaydettiğiniz Ses (Dublaj Denemeniz):
                    </h4>
                    
                    <audio id="audio-playback" controls class="w-full h-10 rounded-lg mb-3"></audio>

                    <div class="flex items-center justify-between gap-2">
                        <button onclick="playTargetSegment()" class="flex-1 bg-slate-800 hover:bg-slate-700 border border-slate-700 text-xs font-semibold py-2 px-3 rounded-lg flex items-center justify-center gap-1.5 text-slate-200 transition">
                            <i data-lucide="rotate-ccw" class="w-3.5 h-3.5 text-amber-400"></i>
                            Orijinal Sesi Tekrar Dinle
                        </button>
                    </div>
                </div>
            </div>

            <!-- TÜBİTAK Öz Değerlendirme & Rubrik Kutusu -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-5 shadow-xl">
                <h3 class="text-xs font-bold text-slate-300 mb-3 uppercase tracking-wider flex items-center justify-between">
                    <span>Öz Değerlendirme Rubriği</span>
                    <span class="text-slate-500 text-[10px]">TÜBİTAK Veri Toplama</span>
                </h3>
                <div class="space-y-3 text-xs">
                    <div class="flex items-center justify-between">
                        <span class="text-slate-400">Telaffuz Doğruluğu:</span>
                        <div class="flex gap-1 text-amber-400">
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 text-slate-700"></i>
                        </div>
                    </div>
                    <div class="flex items-center justify-between">
                        <span class="text-slate-400">Konuşma Akıcılığı (Vurgu/Tonlama):</span>
                        <div class="flex gap-1 text-amber-400">
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 fill-amber-400"></i>
                            <i data-lucide="star" class="w-4 h-4 text-slate-700"></i>
                            <i data-lucide="star" class="w-4 h-4 text-slate-700"></i>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </main>

    <!-- JavaScript Application Logic -->
    <script>
        // App State
        let player = null;
        let isPlayerReady = false;
        let currentMode = 'free'; // 'free' or 'shadow'
        let currentVideoId = 'dQw4w9WgXcQ';
        let activeCheckpointIndex = 0;
        let timeCheckInterval = null;

        // Recording State
        let mediaRecorder = null;
        let audioChunks = [];
        let isRecording = false;
        let recordStartTime = 0;
        let recordTimerInterval = null;

        // Initial Checkpoint Data
        let checkpoints = [
            {
                start: 0,
                end: 5,
                arabic: "مَرْحَبًا، كَيْفَ حَالُكَ اليَوْم؟",
                translit: "Merhaba, keyfe halukel-yevm?",
                meaning: "Merhaba, bugün nasılsın?"
            },
            {
                start: 5,
                end: 11,
                arabic: "أَنَا أَتَعَلَّمُ اللُّغَةَ الْعَرَبِيَّةَ بِشَغَفٍ.",
                translit: "Ene ete'allemul-lugatel-arabiyyete bi-sagaf.",
                meaning: "Arapça dilini tutkuyla öğreniyorum."
            },
            {
                start: 11,
                end: 18,
                arabic: "شُكْرًا جَزِيلًا عَلَى مُسَاعَدَتِك.",
                translit: "Şukran cazilan 'ala musa'adetik.",
                meaning: "Yardımın için çok teşekkür ederim."
            }
        ];

        // Initialize Lucide Icons
        lucide.createIcons();

        // Load YouTube IFrame API Script dynamically
        const tag = document.createElement('script');
        tag.src = "https://www.youtube.com/iframe_api";
        const firstScriptTag = document.getElementsByTagName('script')[0];
        firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

        function onYouTubeIframeAPIReady() {
            createPlayer(currentVideoId);
        }

        function createPlayer(videoId) {
            player = new YT.Player('player', {
                height: '100%',
                width: '100%',
                videoId: videoId,
                playerVars: {
                    'playsinline': 1,
                    'controls': 1,
                    'rel': 0
                },
                events: {
                    'onReady': onPlayerReady,
                    'onStateChange': onPlayerStateChange
                }
            });
        }

        function onPlayerReady(event) {
            isPlayerReady = true;
            document.getElementById('player-status-text').innerText = "Video Hazır";
            renderCheckpoints();
            updateActiveSentenceUI();
            startTimeTracker();
        }

        function onPlayerStateChange(event) {
            if (event.data === YT.PlayerState.PLAYING) {
                document.getElementById('player-status-text').innerText = "Oynatılıyor";
                document.getElementById('pause-overlay').classList.add('hidden');
            } else if (event.data === YT.PlayerState.PAUSED) {
                document.getElementById('player-status-text').innerText = "Duraklatıldı";
            }
        }

        // Time tracking loop for auto-pause logic
        function startTimeTracker() {
            if (timeCheckInterval) clearInterval(timeCheckInterval);
            timeCheckInterval = setInterval(() => {
                if (player && player.getCurrentTime) {
                    const currentTime = player.getCurrentTime();
                    document.getElementById('current-time-display').innerText = formatTime(currentTime);

                    // Check auto pause condition in Shadowing Mode
                    if (currentMode === 'shadow' && checkpoints[activeCheckpointIndex]) {
                        const targetEnd = checkpoints[activeCheckpointIndex].end;
                        if (currentTime >= targetEnd && player.getPlayerState() === YT.PlayerState.PLAYING) {
                            player.pauseVideo();
                            document.getElementById('pause-overlay').classList.remove('hidden');
                            document.getElementById('player-status-text').innerText = "Duraklama Noktası";
                        }
                    }
                }
            }, 300);
        }

        // Switch between Free Listening and Shadowing Mode
        function switchMode(mode) {
            currentMode = mode;
            const freeBtn = document.getElementById('mode-free-btn');
            const shadowBtn = document.getElementById('mode-shadow-btn');
            const limitDisplay = document.getElementById('checkpoint-limit-display');

            if (mode === 'free') {
                freeBtn.className = "px-4 py-2 rounded-lg flex items-center gap-2 transition-all bg-emerald-500 text-slate-950 font-semibold shadow";
                shadowBtn.className = "px-4 py-2 rounded-lg text-slate-400 hover:text-slate-200 flex items-center gap-2 transition-all";
                limitDisplay.classList.add('hidden');
                document.getElementById('pause-overlay').classList.add('hidden');
            } else {
                shadowBtn.className = "px-4 py-2 rounded-lg flex items-center gap-2 transition-all bg-emerald-500 text-slate-950 font-semibold shadow";
                freeBtn.className = "px-4 py-2 rounded-lg text-slate-400 hover:text-slate-200 flex items-center gap-2 transition-all";
                limitDisplay.classList.remove('hidden');
                playTargetSegment();
            }
        }

        function loadYouTubeVideo() {
            const inputUrl = document.getElementById('youtube-url-input').value.trim();
            const extractedId = extractYouTubeId(inputUrl);
            if (extractedId) {
                currentVideoId = extractedId;
                if (player && player.loadVideoById) {
                    player.loadVideoById(currentVideoId);
                } else {
                    createPlayer(currentVideoId);
                }
            } else {
                alert("Lütfen geçerli bir YouTube video linki girin!");
            }
        }

        function extractYouTubeId(url) {
            const regExp = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/;
            const match = url.match(regExp);
            return (match && match[2].length === 11) ? match[2] : null;
        }

        function loadSampleVideo() {
            document.getElementById('youtube-url-input').value = "https://www.youtube.com/watch?v=dQw4w9WgXcQ";
            loadYouTubeVideo();
        }

        function playTargetSegment() {
            if (!isPlayerReady || !checkpoints[activeCheckpointIndex]) return;
            const target = checkpoints[activeCheckpointIndex];
            player.seekTo(target.start, true);
            player.playVideo();
            document.getElementById('pause-overlay').classList.add('hidden');
        }

        function resumePlayer() {
            document.getElementById('pause-overlay').classList.add('hidden');
            if (player) player.playVideo();
        }

        function renderCheckpoints() {
            const container = document.getElementById('checkpoint-list');
            container.innerHTML = '';

            checkpoints.forEach((cp, index) => {
                const isActive = index === activeCheckpointIndex;
                const item = document.createElement('div');
                item.className = `p-3.5 rounded-xl border transition-all cursor-pointer flex items-center justify-between gap-3 ${
                    isActive 
                        ? 'bg-emerald-500/10 border-emerald-500/40 shadow-lg' 
                        : 'bg-slate-950/60 border-slate-800 hover:border-slate-700'
                }`;

                item.innerHTML = `
                    <div class="flex items-center gap-3 overflow-hidden" onclick="selectCheckpoint(${index})">
                        <span class="w-6 h-6 rounded-lg text-xs font-bold flex items-center justify-center ${isActive ? 'bg-emerald-500 text-slate-950' : 'bg-slate-800 text-slate-400'}">
                            ${index + 1}
                        </span>
                        <div class="truncate">
                            <p class="font-arabic text-sm ${isActive ? 'text-emerald-300 font-bold' : 'text-slate-200'} truncate">${cp.arabic}</p>
                            <p class="text-[11px] text-slate-400 truncate">${cp.meaning}</p>
                        </div>
                    </div>
                    <div class="flex items-center gap-2">
                        <span class="text-[10px] font-mono px-2 py-0.5 rounded bg-slate-900 border border-slate-800 text-slate-400 whitespace-nowrap">
                            ${formatTime(cp.start)} - ${formatTime(cp.end)}
                        </span>
                        <button onclick="removeCheckpoint(${index})" class="text-slate-600 hover:text-red-400 p-1 transition">
                            <i data-lucide="trash-2" class="w-3.5 h-3.5"></i>
                        </button>
                    </div>
                `;
                container.appendChild(item);
            });
            lucide.createIcons();
        }

        function selectCheckpoint(index) {
            activeCheckpointIndex = index;
            renderCheckpoints();
            updateActiveSentenceUI();
            if (currentMode === 'shadow') {
                playTargetSegment();
            }
        }

        function updateActiveSentenceUI() {
            const cp = checkpoints[activeCheckpointIndex];
            if (!cp) return;
            document.getElementById('active-arabic-text').innerText = cp.arabic;
            document.getElementById('active-translit-text').innerText = cp.translit;
            document.getElementById('active-meaning-text').innerText = cp.meaning;
            document.getElementById('active-sentence-timer').innerText = `${formatTime(cp.start)} - ${formatTime(cp.end)}`;
            document.getElementById('checkpoint-limit-display').innerText = `Duraklama: ${formatTime(cp.end)}`;
        }

        function addNewCheckpoint() {
            const currentSec = player && player.getCurrentTime ? Math.floor(player.getCurrentTime()) : 0;
            const newCp = {
                start: currentSec,
                end: currentSec + 6,
                arabic: "جُمْلَةٌ جَدِيدَةٌ لِلتَّدْرِيبِ",
                translit: "Cumletun cedidetun lit-tedrib.",
                meaning: "Pratik için yeni bir cümle."
            };
            checkpoints.push(newCp);
            activeCheckpointIndex = checkpoints.length - 1;
            renderCheckpoints();
            updateActiveSentenceUI();
        }

        function removeCheckpoint(index) {
            if (checkpoints.length <= 1) return;
            checkpoints.splice(index, 1);
            activeCheckpointIndex = Math.max(0, index - 1);
            renderCheckpoints();
            updateActiveSentenceUI();
        }

        // Audio Recorder Implementation using Web Audio MediaRecorder API
        async function toggleRecording() {
            const recordBtn = document.getElementById('record-btn');
            const recordIcon = document.getElementById('record-icon');
            const recordStatus = document.getElementById('record-status-text');
            const recordTimer = document.getElementById('record-timer');

            if (!isRecording) {
                // Start Recording
                try {
                    const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                    mediaRecorder = new MediaRecorder(stream);
                    audioChunks = [];

                    mediaRecorder.ondataavailable = (e) => {
                        if (e.data.size > 0) audioChunks.push(e.data);
                    };

                    mediaRecorder.onstop = () => {
                        const audioBlob = new Blob(audioChunks, { type: 'audio/wav' });
                        const audioUrl = URL.createObjectURL(audioBlob);
                        const playback = document.getElementById('audio-playback');
                        playback.src = audioUrl;
                        document.getElementById('audio-result-card').classList.remove('hidden');
                    };

                    mediaRecorder.start();
                    isRecording = true;

                    // UI Updates for Recording State
                    recordBtn.classList.add('pulse-recording', 'bg-red-500', 'border-red-400');
                    recordBtn.classList.remove('bg-slate-800', 'border-emerald-500/50');
                    recordIcon.setAttribute('data-lucide', 'square');
                    recordStatus.innerText = "Kayıt Yapılıyor... Stop İçin Tekrar Basın";
                    recordStatus.classList.add('text-red-400');
                    recordTimer.classList.remove('hidden');

                    recordStartTime = Date.now();
                    recordTimerInterval = setInterval(() => {
                        const elapsed = Math.floor((Date.now() - recordStartTime) / 1000);
                        recordTimer.innerText = formatTime(elapsed);
                    }, 1000);

                    lucide.createIcons();

                } catch (err) {
                    alert("Mikrofona erişim izni verilmedi veya mikrofon bulunamadı!");
                    console.error(err);
                }

            } else {
                // Stop Recording
                if (mediaRecorder) mediaRecorder.stop();
                isRecording = false;

                clearInterval(recordTimerInterval);

                // Reset Recording Button UI
                recordBtn.classList.remove('pulse-recording', 'bg-red-500', 'border-red-400');
                recordBtn.classList.add('bg-slate-800', 'border-emerald-500/50');
                recordIcon.setAttribute('data-lucide', 'mic');
                recordStatus.innerText = "Kayıt Tamamlandı! Aşağıdan Dinleyin";
                recordStatus.classList.remove('text-red-400');
                recordTimer.classList.add('hidden');

                lucide.createIcons();
            }
        }

        // Helper function to format seconds into MM:SS
        function formatTime(seconds) {
            const sec = Math.floor(seconds || 0);
            const m = Math.floor(sec / 60);
            const s = sec % 60;
            return `${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
        }
    </script>
</body>
</html>

