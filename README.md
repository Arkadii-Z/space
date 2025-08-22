<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Хром Плеер</title>
    <style>
        :root {
            --primary-color: #4285f4;
            --secondary-color: #34a853;
            --background-color: #f9f9f9;
            --panel-color: #ffffff;
            --text-color: #333333;
            --border-color: #e0e0e0;
            --shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: var(--background-color);
            color: var(--text-color);
            overflow: hidden;
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        .header {
            background-color: var(--primary-color);
            color: white;
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            height: 50px;
        }

        .main-container {
            display: flex;
            flex: 1;
            overflow: hidden;
            position: relative;
        }

        .module {
            background-color: var(--panel-color);
            border-radius: 8px;
            box-shadow: var(--shadow);
            overflow: hidden;
            margin: 10px;
            display: flex;
            flex-direction: column;
            min-width: 300px;
            transition: all 0.3s ease;
        }

        .module-header {
            background-color: var(--primary-color);
            color: white;
            padding: 8px 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: move;
        }

        .module-content {
            padding: 15px;
            flex: 1;
            overflow: auto;
        }

        .module-controls {
            display: flex;
            gap: 10px;
        }

        .module-controls button {
            background: none;
            border: none;
            color: white;
            cursor: pointer;
            font-size: 16px;
        }

        .player-controls {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 15px;
        }

        .player-controls button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .progress-container {
            width: 100%;
            height: 8px;
            background-color: var(--border-color);
            border-radius: 4px;
            margin-top: 15px;
            cursor: pointer;
        }

        .progress-bar {
            height: 100%;
            background-color: var(--primary-color);
            border-radius: 4px;
            width: 0%;
        }

        .eq-container {
            display: flex;
            height: 150px;
            align-items: flex-end;
            gap: 8px;
            padding: 10px 0;
        }

        .eq-band {
            flex: 1;
            background-color: var(--primary-color);
            border-radius: 4px 4px 0 0;
            position: relative;
            cursor: pointer;
        }

        .eq-band-value {
            position: absolute;
            bottom: -20px;
            width: 100%;
            text-align: center;
            font-size: 10px;
        }

        .visualization-container {
            width: 100%;
            height: 200px;
            position: relative;
            overflow: hidden;
        }

        .visualization-mode {
            display: none;
        }

        #visualization-bars {
            display: flex;
            align-items: flex-end;
            justify-content: space-around;
            height: 100%;
            padding: 0 10px;
        }

        .visualization-bar {
            width: 10px;
            background-color: var(--primary-color);
            margin: 0 2px;
            transition: height 0.1s ease;
        }

        #visualization-wave {
            width: 100%;
            height: 100%;
        }

        #visualization-circles, #visualization-particles {
            width: 100%;
            height: 100%;
        }

        .playlist-item {
            padding: 8px;
            border-bottom: 1px solid var(--border-color);
            cursor: pointer;
        }

        .playlist-item:hover {
            background-color: #f0f0f0;
        }

        .playlist-item.playing {
            background-color: #e3f2fd;
            color: var(--primary-color);
        }

        .source-selector {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .source-selector button {
            padding: 8px 15px;
            border: 1px solid var(--border-color);
            background-color: white;
            border-radius: 4px;
            cursor: pointer;
        }

        .source-selector button.active {
            background-color: var(--primary-color);
            color: white;
        }

        .hidden {
            display: none;
        }

        .collapsed .module-content {
            display: none;
        }

        .minimized {
            width: auto !important;
            min-width: auto !important;
        }

        .minimized .module-content {
            display: none;
        }

        .dragging {
            opacity: 0.8;
            z-index: 1000;
        }

        .snapped {
            transition: all 0.3s ease;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>Хром Плеер</h1>
        <div class="current-track">Не воспроизводится</div>
    </div>

    <div class="main-container" id="main-container">
        <!-- Модуль плеера -->
        <div class="module" id="player-module">
            <div class="module-header">
                <span>Плеер</span>
                <div class="module-controls">
                    <button class="minimize-btn">—</button>
                    <button class="close-btn">✕</button>
                </div>
            </div>
            <div class="module-content">
                <div class="source-selector">
                    <button data-source="local" class="active">Локальные файлы</button>
                    <button data-source="radio">Радио</button>
                    <button data-source="url">URL поток</button>
                </div>

                <div id="local-source" class="source-content">
                    <input type="file" id="audio-file" accept="audio/*" multiple>
                    <div id="local-file-info"></div>
                </div>

                <div id="radio-source" class="source-content hidden">
                    <select id="radio-stations">
                        <option value="https://stream.radioparadise.com/rock-128">Radio Paradise (Rock)</option>
                        <option value="https://icecast.radiofrance.fr/fip-hifi.aac">FIP Radio</option>
                        <option value="https://strm112.1.fm/electronica_mobile_mp3">Electronica Radio</option>
                    </select>
                </div>

                <div id="url-source" class="source-content hidden">
                    <input type="text" id="stream-url" placeholder="Введите URL аудиопотока">
                    <button id="load-stream">Загрузить</button>
                </div>

                <div class="player-controls">
                    <button id="prev-btn">⏮</button>
                    <button id="play-btn">▶</button>
                    <button id="pause-btn">⏸</button>
                    <button id="next-btn">⏭</button>
                </div>

                <div class="progress-container" id="progress-container">
                    <div class="progress-bar" id="progress-bar"></div>
                </div>

                <div id="track-info">Выберите аудио для воспроизведения</div>
            </div>
        </div>

        <!-- Модуль плейлиста -->
        <div class="module" id="playlist-module">
            <div class="module-header">
                <span>Плейлист</span>
                <div class="module-controls">
                    <button class="minimize-btn">—</button>
                    <button class="close-btn">✕</button>
                </div>
            </div>
            <div class="module-content">
                <div id="playlist-items">
                    <!-- Плейлист будет заполняться динамически -->
                </div>
            </div>
        </div>

        <!-- Модуль эквалайзера -->
        <div class="module" id="equalizer-module">
            <div class="module-header">
                <span>Эквалайзер</span>
                <div class="module-controls">
                    <button class="minimize-btn">—</button>
                    <button class="close-btn">✕</button>
                </div>
            </div>
            <div class="module-content">
                <div class="eq-container" id="eq-container">
                    <!-- Полосы эквалайзера будут созданы динамически -->
                </div>
                <select id="eq-presets">
                    <option value="flat">Плоский</option>
                    <option value="pop">Поп</option>
                    <option value="rock">Рок</option>
                    <option value="jazz">Джаз</option>
                    <option value="classical">Классика</option>
                </select>
            </div>
        </div>

        <!-- Модуль визуализации -->
        <div class="module" id="visualization-module">
            <div class="module-header">
                <span>Визуализация</span>
                <div class="module-controls">
                    <button class="minimize-btn">—</button>
                    <button class="close-btn">✕</button>
                </div>
            </div>
            <div class="module-content">
                <select id="visualization-mode">
                    <option value="bars">Столбцы</option>
                    <option value="wave">Волна</option>
                    <option value="circles">Круги</option>
                    <option value="particles">Частицы</option>
                </select>
                <div class="visualization-container">
                    <div id="visualization-bars" class="visualization-mode">
                        <!-- Столбцы визуализации будут созданы динамически -->
                    </div>
                    <canvas id="visualization-wave" class="visualization-mode hidden"></canvas>
                    <canvas id="visualization-circles" class="visualization-mode hidden"></canvas>
                    <canvas id="visualization-particles" class="visualization-mode hidden"></canvas>
                </div>
            </div>
        </div>
    </div>

    <audio id="audio-element" crossorigin="anonymous"></audio>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Основные элементы
            const audioElement = document.getElementById('audio-element');
            const playBtn = document.getElementById('play-btn');
            const pauseBtn = document.getElementById('pause-btn');
            const prevBtn = document.getElementById('prev-btn');
            const nextBtn = document.getElementById('next-btn');
            const progressContainer = document.getElementById('progress-container');
            const progressBar = document.getElementById('progress-bar');
            const trackInfo = document.getElementById('track-info');
            const currentTrackElement = document.querySelector('.current-track');
            
            // Модули
            const modules = document.querySelectorAll('.module');
            const mainContainer = document.getElementById('main-container');
            
            // Плейлист
            const playlistItems = document.getElementById('playlist-items');
            let currentPlaylist = [];
            let currentTrackIndex = -1;
            
            // Эквалайзер
            const eqContainer = document.getElementById('eq-container');
            const eqPresets = document.getElementById('eq-presets');
            let audioContext;
            let equalizer;
            let filters = [];
            
            // Визуализация
            const visualizationMode = document.getElementById('visualization-mode');
            const visualizationBars = document.getElementById('visualization-bars');
            const visualizationWave = document.getElementById('visualization-wave');
            const visualizationCircles = document.getElementById('visualization-circles');
            const visualizationParticles = document.getElementById('visualization-particles');
            let analyser;
            let visualizationRaf;
            let visualizationDataArray;
            
            // Источники аудио
            const sourceButtons = document.querySelectorAll('.source-selector button');
            const sourceContents = document.querySelectorAll('.source-content');
            const audioFileInput = document.getElementById('audio-file');
            const radioStations = document.getElementById('radio-stations');
            const streamUrlInput = document.getElementById('stream-url');
            const loadStreamBtn = document.getElementById('load-stream');
            
            // Инициализация приложения
            initApp();
            
            function initApp() {
                initAudio();
                initEqualizer();
                initVisualization();
                initModules();
                initSources();
                setupEventListeners();
            }
            
            function initAudio() {
                // Создаем контекст для Web Audio API
                try {
                    audioContext = new (window.AudioContext || window.webkitAudioContext)();
                    
                    // Создаем анализатор для визуализации
                    analyser = audioContext.createAnalyser();
                    analyser.fftSize = 256;
                    visualizationDataArray = new Uint8Array(analyser.frequencyBinCount);
                    
                    // Подключаем анализатор к выходу
                    analyser.connect(audioContext.destination);
                    
                } catch (e) {
                    console.error('Web Audio API не поддерживается в этом браузере', e);
                    alert('Web Audio API не поддерживается в вашем браузере. Некоторые функции могут быть недоступны.');
                }
                
                // Обработчики событий для аудио элемента
                audioElement.addEventListener('loadedmetadata', function() {
                    updateTrackInfo();
                });
                
                audioElement.addEventListener('timeupdate', function() {
                    updateProgress();
                });
                
                audioElement.addEventListener('ended', function() {
                    playNext();
                });
            }
            
            function initEqualizer() {
                // Создаем 7 полос эквалайзера
                const frequencies = [60, 150, 400, 1000, 2400, 6000, 15000];
                
                // Создаем элементы интерфейса для эквалайзера
                for (let i = 0; i < frequencies.length; i++) {
                    const band = document.createElement('div');
                    band.className = 'eq-band';
                    band.dataset.freq = frequencies[i];
                    band.dataset.value = 0;
                    
                    const valueDisplay = document.createElement('div');
                    valueDisplay.className = 'eq-band-value';
                    valueDisplay.textContent = '0 dB';
                    
                    band.appendChild(valueDisplay);
                    eqContainer.appendChild(band);
                    
                    // Создаем фильтры для эквалайзера
                    if (audioContext) {
                        const filter = audioContext.createBiquadFilter();
                        filter.type = 'peaking';
                        filter.frequency.value = frequencies[i];
                        filter.Q.value = 1;
                        filter.gain.value = 0;
                        
                        filters.push(filter);
                    }
                    
                    // Добавляем обработчик событий для изменения полосы
                    band.addEventListener('click', function(e) {
                        const rect = this.getBoundingClientRect();
                        const height = rect.height;
                        const y = e.clientY - rect.top;
                        const value = ((height - y) / height) * 30 - 15; // От -15 до +15 dB
                        
                        this.dataset.value = value;
                        this.querySelector('.eq-band-value').textContent = value.toFixed(1) + ' dB';
                        
                        // Обновляем соответствующий фильтр
                        if (filters[i]) {
                            filters[i].gain.value = value;
                        }
                    });
                }
                
                // Обработчик для пресетов
                eqPresets.addEventListener('change', function() {
                    applyEqPreset(this.value);
                });
            }
            
            function applyEqPreset(presetName) {
                const presets = {
                    flat: [0, 0, 0, 0, 0, 0, 0],
                    pop: [4, 2, -1, 1, 2, 3, 4],
                    rock: [5, 3, -2, 1, 3, 5, 6],
                    jazz: [3, 2, 0, -1, 1, 2, 3],
                    classical: [4, 3, 1, 0, 1, 3, 4]
                };
                
                const values = presets[presetName] || presets.flat;
                const bands = document.querySelectorAll('.eq-band');
                
                for (let i = 0; i < values.length; i++) {
                    if (bands[i]) {
                        bands[i].dataset.value = values[i];
                        bands[i].querySelector('.eq-band-value').textContent = values[i].toFixed(1) + ' dB';
                    }
                    
                    if (filters[i]) {
                        filters[i].gain.value = values[i];
                    }
                }
            }
            
            function initVisualization() {
                // Инициализация canvas элементов
                const canvases = [visualizationWave, visualizationCircles, visualizationParticles];
                
                canvases.forEach(canvas => {
                    canvas.width = canvas.offsetWidth;
                    canvas.height = canvas.offsetHeight;
                });
                
                // Создаем бары для визуализации
                for (let i = 0; i < 32; i++) {
                    const bar = document.createElement('div');
                    bar.className = 'visualization-bar';
                    visualizationBars.appendChild(bar);
                }
                
                // Обработчик изменения режима визуализации
                visualizationMode.addEventListener('change', function() {
                    // Скрываем все режимы
                    document.querySelectorAll('.visualization-mode').forEach(mode => {
                        mode.classList.add('hidden');
                    });
                    
                    // Показываем выбранный режим
                    document.getElementById('visualization-' + this.value).classList.remove('hidden');
                    
                    // Если визуализация активна, перезапускаем её
                    if (!audioElement.paused && !audioElement.ended) {
                        stopVisualization();
                        startVisualization();
                    }
                });
            }
            
            function startVisualization() {
                if (!analyser) return;
                
                const bars = document.querySelectorAll('.visualization-bar');
                const waveCtx = visualizationWave.getContext('2d');
                const circlesCtx = visualizationCircles.getContext('2d');
                const particlesCtx = visualizationParticles.getContext('2d');
                
                function draw() {
                    visualizationRaf = requestAnimationFrame(draw);
                    
                    analyser.getByteFrequencyData(visualizationDataArray);
                    
                    const mode = visualizationMode.value;
                    
                    switch(mode) {
                        case 'bars':
                            drawBars(bars);
                            break;
                        case 'wave':
                            drawWave(waveCtx);
                            break;
                        case 'circles':
                            drawCircles(circlesCtx);
                            break;
                        case 'particles':
                            drawParticles(particlesCtx);
                            break;
                    }
                }
                
                function drawBars(bars) {
                    for (let i = 0; i < bars.length; i++) {
                        const value = visualizationDataArray[i];
                        const percent = value / 256;
                        const height = Math.max(2, percent * 100);
                        bars[i].style.height = height + '%';
                    }
                }
                
                function drawWave(ctx) {
                    ctx.clearRect(0, 0, visualizationWave.width, visualizationWave.height);
                    
                    ctx.beginPath();
                    ctx.lineWidth = 2;
                    ctx.strokeStyle = '#4285f4';
                    
                    const sliceWidth = visualizationWave.width / analyser.frequencyBinCount;
                    let x = 0;
                    
                    for (let i = 0; i < analyser.frequencyBinCount; i++) {
                        const v = visualizationDataArray[i] / 128.0;
                        const y = v * visualizationWave.height / 2;
                        
                        if (i === 0) {
                            ctx.moveTo(x, y);
                        } else {
                            ctx.lineTo(x, y);
                        }
                        
                        x += sliceWidth;
                    }
                    
                    ctx.stroke();
                }
                
                function drawCircles(ctx) {
                    ctx.clearRect(0, 0, visualizationCircles.width, visualizationCircles.height);
                    
                    const centerX = visualizationCircles.width / 2;
                    const centerY = visualizationCircles.height / 2;
                    const maxRadius = Math.min(centerX, centerY) - 10;
                    
                    for (let i = 0; i < analyser.frequencyBinCount; i += 4) {
                        const value = visualizationDataArray[i];
                        const percent = value / 256;
                        const radius = percent * maxRadius;
                        
                        ctx.beginPath();
                        ctx.arc(centerX, centerY, radius, 0, 2 * Math.PI);
                        ctx.fillStyle = `rgba(66, 133, 244, ${0.2 + percent * 0.8})`;
                        ctx.fill();
                    }
                }
                
                function drawParticles(ctx) {
                    ctx.clearRect(0, 0, visualizationParticles.width, visualizationParticles.height);
                    
                    const particleCount = 100;
                    const width = visualizationParticles.width;
                    const height = visualizationParticles.height;
                    
                    for (let i = 0; i < particleCount; i++) {
                        const index = i % analyser.frequencyBinCount;
                        const value = visualizationDataArray[index];
                        const size = 2 + (value / 256) * 8;
                        
                        const x = Math.random() * width;
                        const y = Math.random() * height;
                        
                        ctx.beginPath();
                        ctx.arc(x, y, size, 0, 2 * Math.PI);
                        ctx.fillStyle = `rgba(66, 133, 244, ${0.3 + (value / 256) * 0.7})`;
                        ctx.fill();
                    }
                }
                
                draw();
            }
            
            function stopVisualization() {
                if (visualizationRaf) {
                    cancelAnimationFrame(visualizationRaf);
                }
                
                // Очищаем canvas
                const canvases = [visualizationWave, visualizationCircles, visualizationParticles];
                
                canvases.forEach(canvas => {
                    const ctx = canvas.getContext('2d');
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                });
                
                // Сбрасываем бары
                const bars = document.querySelectorAll('.visualization-bar');
                bars.forEach(bar => {
                    bar.style.height = '2%';
                });
            }
            
            function initModules() {
                // Делаем модули перемещаемыми
                modules.forEach(module => {
                    const header = module.querySelector('.module-header');
                    const minimizeBtn = module.querySelector('.minimize-btn');
                    const closeBtn = module.querySelector('.close-btn');
                    
                    // Обработчики для кнопок управления модулями
                    minimizeBtn.addEventListener('click', function() {
                        module.classList.toggle('minimized');
                        this.textContent = module.classList.contains('minimized') ? '+' : '—';
                    });
                    
                    closeBtn.addEventListener('click', function() {
                        module.style.display = 'none';
                    });
                    
                    // Перетаскивание модулей
                    let isDragging = false;
                    let dragOffsetX, dragOffsetY;
                    
                    header.addEventListener('mousedown', function(e) {
                        isDragging = true;
                        module.classList.add('dragging');
                        
                        dragOffsetX = e.clientX - module.offsetLeft;
                        dragOffsetY = e.clientY - module.offsetTop;
                        
                        // Поднимаем модуль выше остальных
                        modules.forEach(m => {
                            m.style.zIndex = 1;
                        });
                        module.style.zIndex = 10;
                    });
                    
                    document.addEventListener('mousemove', function(e) {
                        if (!isDragging) return;
                        
                        const x = e.clientX - dragOffsetX;
                        const y = e.clientY - dragOffsetY;
                        
                        module.style.left = x + 'px';
                        module.style.top = y + 'px';
                        module.style.position = 'absolute';
                        
                        // Проверяем, находится ли модуль близко к краям
                        checkSnapping(module);
                    });
                    
                    document.addEventListener('mouseup', function() {
                        if (isDragging) {
                            isDragging = false;
                            module.classList.remove('dragging');
                            
                            // Применяем примагничивание, если необходимо
                            applySnapping(module);
                        }
                    });
                });
            }
            
            function checkSnapping(module) {
                const containerRect = mainContainer.getBoundingClientRect();
                const moduleRect = module.getBoundingClientRect();
                
                const snapThreshold = 20;
                
                // Проверяем близость к левому краю
                if (Math.abs(moduleRect.left - containerRect.left) < snapThreshold) {
                    module.classList.add('snapped-left');
                } else {
                    module.classList.remove('snapped-left');
                }
                
                // Проверяем близость к правому краю
                if (Math.abs(moduleRect.right - containerRect.right) < snapThreshold) {
                    module.classList.add('snapped-right');
                } else {
                    module.classList.remove('snapped-right');
                }
                
                // Проверяем близость к верхнему краю
                if (Math.abs(moduleRect.top - containerRect.top) < snapThreshold) {
                    module.classList.add('snapped-top');
                } else {
                    module.classList.remove('snapped-top');
                }
                
                // Проверяем близость к нижнему краю
                if (Math.abs(moduleRect.bottom - containerRect.bottom) < snapThreshold) {
                    module.classList.add('snapped-bottom');
                } else {
                    module.classList.remove('snapped-bottom');
                }
            }
            
            function applySnapping(module) {
                const containerRect = mainContainer.getBoundingClientRect();
                const moduleRect = module.getBoundingClientRect();
                
                if (module.classList.contains('snapped-left')) {
                    module.style.left = '0px';
                }
                
                if (module.classList.contains('snapped-right')) {
                    module.style.left = (containerRect.width - moduleRect.width) + 'px';
                }
                
                if (module.classList.contains('snapped-top')) {
                    module.style.top = '0px';
                }
                
                if (module.classList.contains('snapped-bottom')) {
                    module.style.top = (containerRect.height - moduleRect.height) + 'px';
                }
                
                // Убираем все классы snapping
                module.classList.remove('snapped-left', 'snapped-right', 'snapped-top', 'snapped-bottom');
            }
            
            function initSources() {
                // Обработчики для переключения источников аудио
                sourceButtons.forEach(button => {
                    button.addEventListener('click', function() {
                        // Делаем активной выбранную кнопку
                        sourceButtons.forEach(btn => btn.classList.remove('active'));
                        this.classList.add('active');
                        
                        // Показываем соответствующий контент
                        const sourceType = this.dataset.source;
                        sourceContents.forEach(content => {
                            content.classList.add('hidden');
                        });
                        document.getElementById(sourceType + '-source').classList.remove('hidden');
                    });
                });
                
                // Обработчик для выбора локальных файлов
                audioFileInput.addEventListener('change', function(e) {
                    const files = e.target.files;
                    if (files.length > 0) {
                        addFilesToPlaylist(files);
                    }
                });
                
                // Обработчик для загрузки потока по URL
                loadStreamBtn.addEventListener('click', function() {
                    const url = streamUrlInput.value.trim();
                    if (url) {
                        addStreamToPlaylist(url, 'Прямой поток');
                    }
                });
                
                // Обработчик для выбора радиостанции
                radioStations.addEventListener('change', function() {
                    const url = this.value;
                    const name = this.options[this.selectedIndex].text;
                    addStreamToPlaylist(url, name);
                });
            }
            
            function addFilesToPlaylist(files) {
                for (let i = 0; i < files.length; i++) {
                    const file = files[i];
                    const url = URL.createObjectURL(file);
                    
                    currentPlaylist.push({
                        name: file.name,
                        url: url,
                        type: 'local'
                    });
                    
                    addToPlaylistUI(file.name, url);
                }
                
                // Если это первый трек, начинаем воспроизведение
                if (currentPlaylist.length > 0 && currentTrackIndex === -1) {
                    currentTrackIndex = 0;
                    playTrack(currentTrackIndex);
                }
            }
            
            function addStreamToPlaylist(url, name) {
                currentPlaylist.push({
                    name: name,
                    url: url,
                    type: 'stream'
                });
                
                addToPlaylistUI(name, url);
                
                // Если это первый трек, начинаем воспроизведение
                if (currentPlaylist.length > 0 && currentTrackIndex === -1) {
                    currentTrackIndex = 0;
                    playTrack(currentTrackIndex);
                }
            }
            
            function addToPlaylistUI(name, url) {
                const item = document.createElement('div');
                item.className = 'playlist-item';
                item.dataset.url = url;
                item.textContent = name;
                
                item.addEventListener('click', function() {
                    const index = Array.from(playlistItems.children).indexOf(this);
                    currentTrackIndex = index;
                    playTrack(index);
                });
                
                playlistItems.appendChild(item);
            }
            
            function playTrack(index) {
                if (index < 0 || index >= currentPlaylist.length) return;
                
                const track = currentPlaylist[index];
                
                // Обновляем UI плейлиста
                document.querySelectorAll('.playlist-item').forEach((item, i) => {
                    if (i === index) {
                        item.classList.add('playing');
                    } else {
                        item.classList.remove('playing');
                    }
                });
                
                // Устанавливаем источник для аудио элемента
                audioElement.src = track.url;
                
                // Подключаем эквалайзер, если он инициализирован
                if (audioContext && equalizer) {
                    // Отключаем предыдущие соединения
                    if (audioElement.source) {
                        audioElement.source.disconnect();
                    }
                    
                    // Создаем новый источник
                    const source = audioContext.createMediaElementSource(audioElement);
                    audioElement.source = source;
                    
                    // Подключаем эквалайзер
                    source.connect(equalizer);
                    equalizer.connect(analyser);
                    
                    // Подключаем фильтры эквалайзера
                    if (filters.length > 0) {
                        // Отключаем предыдущие соединения
                        equalizer.disconnect();
                        
                        // Подключаем цепочку фильтров
                        let currentNode = equalizer;
                        
                        for (let i = 0; i < filters.length; i++) {
                            currentNode.connect(filters[i]);
                            currentNode = filters[i];
                        }
                        
                        // Подключаем к анализатору и выходу
                        currentNode.connect(analyser);
                    }
                }
                
                // Воспроизводим
                audioElement.play()
                    .then(() => {
                        updateTrackInfo();
                        startVisualization();
                    })
                    .catch(error => {
                        console.error('Ошибка воспроизведения:', error);
                    });
            }
            
            function updateTrackInfo() {
                const track = currentPlaylist[currentTrackIndex];
                if (track) {
                    trackInfo.textContent = `Сейчас играет: ${track.name}`;
                    currentTrackElement.textContent = track.name;
                }
            }
            
            function updateProgress() {
                if (audioElement.duration) {
                    const percent = (audioElement.currentTime / audioElement.duration) * 100;
                    progressBar.style.width = percent + '%';
                }
            }
            
            function playNext() {
                if (currentPlaylist.length === 0) return;
                
                currentTrackIndex = (currentTrackIndex + 1) % currentPlaylist.length;
                playTrack(currentTrackIndex);
            }
            
            function playPrev() {
                if (currentPlaylist.length === 0) return;
                
                currentTrackIndex = (currentTrackIndex - 1 + currentPlaylist.length) % currentPlaylist.length;
                playTrack(currentTrackIndex);
            }
            
            function setupEventListeners() {
                // Кнопки управления плеером
                playBtn.addEventListener('click', function() {
                    if (currentPlaylist.length > 0) {
                        audioElement.play()
                            .then(() => {
                                startVisualization();
                            })
                            .catch(error => {
                                console.error('Ошибка воспроизведения:', error);
                            });
                    }
                });
                
                pauseBtn.addEventListener('click', function() {
                    audioElement.pause();
                    stopVisualization();
                });
                
                nextBtn.addEventListener('click', playNext);
                prevBtn.addEventListener('click', playPrev);
                
                // Прогресс-бар
                progressContainer.addEventListener('click', function(e) {
                    if (!audioElement.duration) return;
                    
                    const rect = this.getBoundingClientRect();
                    const clickX = e.clientX - rect.left;
                    const percent = clickX / rect.width;
                    
                    audioElement.currentTime = percent * audioElement.duration;
                });
                
                // Создаем эквалайзер после инициализации audioContext
                if (audioContext) {
                    equalizer = audioContext.createGain();
                }
            }
        });
    </script>
</body>
</html>
