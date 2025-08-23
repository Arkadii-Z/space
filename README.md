<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>3D Солнечная система | Интерактивная модель</title>
    <meta name="description" content="Интерактивная 3D модель Солнечной системы с реалистичными планетами. Исследуйте космос, управляйте скоростью вращения и изучайте планеты!">
    <meta name="keywords" content="солнечная система, 3d модель, планеты, космос, интерактивная, three.js">
    <meta name="author" content="Ваше Имя">
    <meta property="og:title" content="3D Солнечная система | Интерактивная модель">
    <meta property="og:description" content="Исследуйте нашу Солнечную систему в интерактивной 3D модели. Управляйте планетами и изучайте космос!">
    <meta property="og:type" content="website">
    <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🌎</text></svg>">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
            touch-action: manipulation;
        }
        
        body {
            overflow: hidden;
            background: #000;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            color: white;
            touch-action: none;
        }
        
        #container {
            position: absolute;
            width: 100%;
            height: 100%;
            touch-action: none;
        }
        
        #ui {
            position: absolute;
            bottom: 10px;
            left: 0;
            width: 100%;
            display: flex;
            justify-content: center;
            z-index: 100;
            padding: 0 10px;
        }
        
        .control-panel {
            background: rgba(0, 30, 60, 0.85);
            padding: 12px;
            border-radius: 12px;
            backdrop-filter: blur(5px);
            box-shadow: 0 0 15px rgba(0, 100, 255, 0.5);
            display: flex;
            flex-direction: column;
            gap: 12px;
            width: 100%;
            max-width: 500px;
            transition: all 0.3s ease;
        }
        
        .control-panel.collapsed {
            height: 40px;
            overflow: hidden;
            padding: 10px;
        }
        
        .panel-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            user-select: none;
        }
        
        .panel-header h3 {
            color: #4db8ff;
            margin: 0;
            font-size: 16px;
        }
        
        .panel-content {
            transition: all 0.3s ease;
        }
        
        .control-panel.collapsed .panel-content {
            opacity: 0;
            height: 0;
            overflow: hidden;
        }
        
        .slider-container {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        
        .slider-container label {
            display: flex;
            justify-content: space-between;
            font-size: 14px;
        }
        
        input[type="range"] {
            width: 100%;
            height: 6px;
            -webkit-appearance: none;
            background: rgba(100, 150, 255, 0.3);
            border-radius: 3px;
            outline: none;
        }
        
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background: #4db8ff;
            cursor: pointer;
            box-shadow: 0 0 10px rgba(0, 200, 255, 0.8);
        }
        
        .planet-selector {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            justify-content: center;
            margin-top: 8px;
        }
        
        .planet-btn {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            border: 2px solid rgba(255, 255, 255, 0.3);
            background: rgba(50, 50, 100, 0.5);
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: white;
            font-size: 12px;
        }
        
        .planet-btn.active {
            transform: scale(1.3);
            box-shadow: 0 0 20px rgba(0, 200, 255, 1);
            border-color: white;
        }
        
        .planet-btn.dwarf {
            border-style: dashed;
            opacity: 0.8;
        }
        
        #title {
            position: absolute;
            top: 10px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 18px;
            text-shadow: 0 0 10px rgba(0, 200, 255, 0.8);
            pointer-events: none;
            padding: 0 10px;
        }
        
        #instructions {
            position: absolute;
            top: 40px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 12px;
            color: #a0d0ff;
            text-shadow: 0 0 5px rgba(0, 100, 255, 0.5);
            pointer-events: none;
            padding: 0 10px;
        }
        
        .loading {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 18px;
            color: #4db8ff;
            text-align: center;
            padding: 0 20px;
        }
        
        .planet-label {
            position: absolute;
            background: rgba(0, 0, 0, 0.7);
            padding: 4px 8px;
            border-radius: 8px;
            font-size: 12px;
            pointer-events: none;
            transform: translate(-50%, -50%);
            transition: all 0.3s ease;
            border: 1px solid rgba(100, 200, 255, 0.5);
            white-space: nowrap;
            z-index: 10;
        }
        
        .planet-label.dwarf {
            border-style: dashed;
            color: #aaa;
        }
        
        .info-card {
            position: absolute;
            background: rgba(0, 30, 60, 0.95);
            border-radius: 10px;
            padding: 12px;
            max-width: 280px;
            backdrop-filter: blur(5px);
            box-shadow: 0 0 15px rgba(0, 100, 255, 0.5);
            border: 1px solid rgba(100, 200, 255, 0.3);
            opacity: 0;
            transition: opacity 0.3s ease;
            z-index: 200;
            pointer-events: auto;
            font-size: 14px;
        }
        
        .info-card.visible {
            opacity: 1;
        }
        
        .info-card h3 {
            margin-bottom: 8px;
            color: #4db8ff;
            border-bottom: 1px solid rgba(100, 200, 255, 0.3);
            padding-bottom: 4px;
            font-size: 16px;
        }
        
        .info-card p {
            margin-bottom: 8px;
            line-height: 1.4;
        }
        
        .info-card a {
            color: #4db8ff;
            text-decoration: none;
            font-weight: bold;
            display: inline-block;
            margin-top: 5px;
            pointer-events: auto;
            font-size: 14px;
        }

        .info-card a:hover {
            text-decoration: underline;
            color: #ffcc00;
        }
        
        .mobile-controls {
            position: absolute;
            right: 10px;
            bottom: 100px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 90;
        }
        
        .mobile-btn {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background: rgba(0, 30, 60, 0.7);
            border: 2px solid rgba(100, 200, 255, 0.5);
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            cursor: pointer;
        }

        .footer {
            position: absolute;
            bottom: 5px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 11px;
            color: #88c0ff;
            opacity: 0.7;
            pointer-events: none;
        }
        
        .toggle-btn {
            background: none;
            border: none;
            color: #4db8ff;
            font-size: 16px;
            cursor: pointer;
            transition: all 0.3s ease;
            padding: 5px;
        }
        
        .toggle-btn:hover {
            color: #ffcc00;
            transform: scale(1.2);
        }
        
        .music-controls {
            position: absolute;
            top: 10px;
            right: 10px;
            z-index: 1000;
        }
        
        .music-btn {
            background: rgba(0, 30, 60, 0.7);
            border: 2px solid rgba(100, 200, 255, 0.5);
            color: white;
            padding: 8px 12px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 14px;
            backdrop-filter: blur(5px);
            transition: all 0.3s ease;
        }
        
        .music-btn:hover {
            background: rgba(0, 50, 100, 0.9);
        }
        
        .music-btn.muted {
            opacity: 0.6;
        }
    </style>
</head>
<body>
    <div id="container"></div>
    
    <div class="music-controls">
        <button class="music-btn" id="music-toggle">🔊 Музыка ВКЛ</button>
    </div>
    
    <div id="title">СОЛНЕЧНАЯ СИСТЕМА</div>
    <div id="instructions">Наведите на планету для информации, кликните для поиска в Google</div>
    
    <div id="ui">
        <div class="control-panel" id="main-panel">
            <div class="panel-header">
                <h3>Управление системой</h3>
                <button class="toggle-btn" id="toggle-panel">−</button>
            </div>
            <div class="panel-content">
                <div class="slider-container">
                    <label>
                        <span>Скорость:</span>
                        <span id="speed-value">1.0x</span>
                    </label>
                    <input type="range" id="rotation-speed" min="0" max="2" step="0.1" value="1">
                </div>
                
                <div class="slider-container">
                    <label>
                        <span>Размер:</span>
                        <span id="size-value">1.0x</span>
                    </label>
                    <input type="range" id="planet-size" min="0.5" max="3" step="0.1" value="1">
                </div>
                
                <div class="planet-selector">
                    <div class="planet-btn active" data-planet="sun" title="Солнце">☀️</div>
                    <div class="planet-btn" data-planet="mercury" title="Меркурий">☿</div>
                    <div class="planet-btn" data-planet="venus" title="Венера">♀</div>
                    <div class="planet-btn" data-planet="earth" title="Земля">🌎</div>
                    <div class="planet-btn" data-planet="mars" title="Марс">♂</div>
                    <div class="planet-btn" data-planet="jupiter" title="Юпитер">♃</div>
                    <div class="planet-btn" data-planet="saturn" title="Сатурн">♄</div>
                    <div class="planet-btn" data-planet="uranus" title="Уран">♅</div>
                    <div class="planet-btn" data-planet="neptune" title="Нептун">♆</div>
                    <div class="planet-btn dwarf" data-planet="pluto" title="Плутон (карликовая планета)">♇</div>
                </div>
            </div>
        </div>
    </div>

    <div class="mobile-controls">
        <button class="mobile-btn" id="zoom-in">+</button>
        <button class="mobile-btn" id="zoom-out">-</button>
        <button class="mobile-btn" id="reset-camera">⌂</button>
    </div>

    <div class="footer">
        Создано с Three.js • Интерактивная 3D модель Солнечной системы
    </div>
    
    <div class="loading" id="loading">Загрузка солнечной системы...</div>

    <!-- Аудио элемент для музыки -->
    <audio id="background-music" loop>
        <source src="https://cdn.pixabay.com/download/audio/2022/01/20/audio_dc6c2f9692.mp3?filename=space-ambience-97614.mp3" type="audio/mpeg">
    </audio>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.min.js"></script>
    <script>
        // Основные переменные
        let scene, camera, renderer, controls;
        let planets = {};
        let planetLabels = {};
        let rotationSpeed = 1.0;
        let planetSize = 1;
        let currentFocus = 'sun';
        let raycaster, mouse;
        let infoCard = null;
        let isMobile = /iPhone|iPad|iPod|Android/i.test(navigator.userAgent);
        let backgroundMusic;
        let isMusicPlaying = false;

        // Информация о планетах (добавлен Плутон)
        const planetInfo = {
            sun: {
                name: "Солнце",
                description: "Звезда, вокруг которой обращаются все планеты системы. Состоит в основном из водорода и гелия.",
                diameter: "1 391 000 км",
                mass: "1.989 × 10^30 кг",
                wiki: "https://ru.wikipedia.org/wiki/Солнце",
                search: "Sun star"
            },
            mercury: {
                name: "Меркурий",
                description: "Ближайшая к Солнцу планета. Не имеет естественных спутников.",
                diameter: "4 879 км",
                mass: "3.301 × 10^23 кг",
                wiki: "https://ru.wikipedia.org/wiki/Меркурий",
                search: "Mercury planet"
            },
            venus: {
                name: "Венера",
                description: "Вторая планета от Солнца. Имеет плотную атмосферу из углекислого газа.",
                diameter: "12 104 км",
                mass: "4.867 × 10^24 кг",
                wiki: "https://ru.wikipedia.org/wiki/Венера",
                search: "Venus planet"
            },
            earth: {
                name: "Земля",
                description: "Третья планета от Солнца. Единственное известное тело во Вселенной, населённое живыми организмами.",
                diameter: "12 742 км",
                mass: "5.972 × 10^24 кг",
                wiki: "https://ru.wikipedia.org/wiki/Земля",
                search: "Earth planet"
            },
            mars: {
                name: "Марс",
                description: "Четвёртая планета от Солнца. Известен как 'Красная планета' из-за оксида железа на поверхности.",
                diameter: "6 779 км",
                mass: "6.417 × 10^23 кг",
                wiki: "https://ru.wikipedia.org/wiki/Марс",
                search: "Mars planet"
            },
            jupiter: {
                name: "Юпитер",
                description: "Крупнейшая планета Солнечной системы. Газовый гигант с заметным Большим красным пятном.",
                diameter: "139 820 км",
                mass: "1.898 × 10^27 кг",
                wiki: "https://ru.wikipedia.org/wiki/Юпитер",
                search: "Jupiter planet"
            },
            saturn: {
                name: "Сатурн",
                description: "Шестая планета от Солнца. Известна своими кольцами, состоящими из льда и камней.",
                diameter: "116 460 км",
                mass: "5.683 × 10^26 кг",
                wiki: "https://ru.wikipedia.org/wiki/Сатурн",
                search: "Saturn planet"
            },
            uranus: {
                name: "Уран",
                description: "Седьмая планета от Солнца. Имеет уникальное боковое вращение вокруг своей оси.",
                diameter: "50 724 км",
                mass: "8.681 × 10^25 кг",
                wiki: "https://ru.wikipedia.org/wiki/Уран",
                search: "Uranus planet"
            },
            neptune: {
                name: "Нептун",
                description: "Восьмая и самая дальняя планета Солнечной системы. Открыта благодаря математическим расчётам.",
                diameter: "49 244 км",
                mass: "1.024 × 10^26 кг",
                wiki: "https://ru.wikipedia.org/wiki/Нептун",
                search: "Neptune planet"
            },
            pluto: {
                name: "Плутон",
                description: "Карликовая планета в поясе Койпера. Ранее считался девятой планетой Солнечной системы.",
                diameter: "2 377 км",
                mass: "1.303 × 10^22 кг",
                wiki: "https://ru.wikipedia.org/wiki/Плутон",
                search: "Pluto dwarf planet",
                isDwarf: true
            }
        };

        // Параметры планет (добавлен Плутон)
        const planetParams = {
            sun: { radius: 5, distance: 0, rotationSpeed: 0.001, color: 0xffcc00 },
            mercury: { radius: 0.8, distance: 10, rotationSpeed: 0.004, color: 0xa9a9a9 },
            venus: { radius: 1.2, distance: 15, rotationSpeed: 0.002, color: 0xffb366 },
            earth: { radius: 1.3, distance: 20, rotationSpeed: 0.005, color: 0x3399ff },
            mars: { radius: 1.1, distance: 25, rotationSpeed: 0.004, color: 0xff6633 },
            jupiter: { radius: 2.8, distance: 35, rotationSpeed: 0.009, color: 0xffcc99 },
            saturn: { radius: 2.4, distance: 45, rotationSpeed: 0.008, color: 0xffdbac },
            uranus: { radius: 1.8, distance: 55, rotationSpeed: 0.007, color: 0x99ccff },
            neptune: { radius: 1.8, distance: 65, rotationSpeed: 0.006, color: 0x3366ff },
            pluto: { radius: 0.6, distance: 75, rotationSpeed: 0.005, color: 0xcc9966, isDwarf: true }
        };

        // Инициализация
        function init() {
            try {
                setupScene();
                setupEventListeners();
                createSolarSystem();
                
                // Создаем информационную карточку
                createInfoCard();
                
                // Инициализация музыки
                setupMusic();
                
                document.getElementById('loading').style.display = 'none';
                animate();

            } catch (error) {
                console.error('Error initializing:', error);
                document.getElementById('loading').textContent = 'Ошибка загрузки. Пожалуйста, обновите страницу.';
            }
        }

        function setupScene() {
            // Сцена
            scene = new THREE.Scene();
            
            // Камера
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.z = isMobile ? 70 : 50;
            
            // Рендерер
            renderer = new THREE.WebGLRenderer({ 
                antialias: true, 
                alpha: true,
                powerPreference: "high-performance"
            });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            document.getElementById('container').appendChild(renderer.domElement);
            
            // Управление камерой
            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.minDistance = 5;
            controls.maxDistance = 250; // Увеличено для Плутона
            
            // Raycaster для взаимодействия
            raycaster = new THREE.Raycaster();
            mouse = new THREE.Vector2();
        }

        function setupMusic() {
            backgroundMusic = document.getElementById('background-music');
            backgroundMusic.volume = 0.3;
            
            // Автозапуск музыки с разрешения пользователя
            document.addEventListener('click', function initMusic() {
                if (!isMusicPlaying) {
                    backgroundMusic.play().then(() => {
                        isMusicPlaying = true;
                        updateMusicButton();
                    }).catch(console.error);
                }
                document.removeEventListener('click', initMusic);
            }, { once: true });
        }

        function toggleMusic() {
            if (isMusicPlaying) {
                backgroundMusic.pause();
                isMusicPlaying = false;
            } else {
                backgroundMusic.play().catch(console.error);
                isMusicPlaying = true;
            }
            updateMusicButton();
        }

        function updateMusicButton() {
            const btn = document.getElementById('music-toggle');
            if (isMusicPlaying) {
                btn.textContent = '🔊 Музыка ВКЛ';
                btn.classList.remove('muted');
            } else {
                btn.textContent = '🔇 Музыка ВЫКЛ';
                btn.classList.add('muted');
            }
        }

        function setupEventListeners() {
            // Обработка изменения размера
            window.addEventListener('resize', onWindowResize);
            
            // Управление
            document.getElementById('rotation-speed').addEventListener('input', (e) => {
                rotationSpeed = parseFloat(e.target.value);
                document.getElementById('speed-value').textContent = rotationSpeed.toFixed(1) + 'x';
            });
            
            document.getElementById('planet-size').addEventListener('input', (e) => {
                planetSize = parseFloat(e.target.value);
                document.getElementById('size-value').textContent = planetSize.toFixed(1) + 'x';
                updatePlanetSizes();
            });
            
            // Выбор планет
            document.querySelectorAll('.planet-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    e.stopPropagation();
                    document.querySelectorAll('.planet-btn').forEach(b => b.classList.remove('active'));
                    btn.classList.add('active');
                    currentFocus = btn.dataset.planet;
                    focusOnPlanet(currentFocus);
                });
            });
            
            // Сворачивание панели
            document.getElementById('toggle-panel').addEventListener('click', function(e) {
                e.stopPropagation();
                const panel = document.getElementById('main-panel');
                panel.classList.toggle('collapsed');
                this.textContent = panel.classList.contains('collapsed') ? '+' : '−';
            });
            
            // Управление музыкой
            document.getElementById('music-toggle').addEventListener('click', toggleMusic);
            
            // Мобильное управление
            if (isMobile) {
                document.getElementById('zoom-in').addEventListener('click', () => {
                    camera.position.z -= 5;
                    if (camera.position.z < 10) camera.position.z = 10;
                });
                
                document.getElementById('zoom-out').addEventListener('click', () => {
                    camera.position.z += 5;
                    if (camera.position.z > 250) camera.position.z = 250;
                });
                
                document.getElementById('reset-camera').addEventListener('click', () => {
                    focusOnPlanet('sun');
                });
                
                // Обработка касаний для мобильных устройств
                setupTouchControls();
            }
            
            // Взаимодействие с планетами
            document.addEventListener('mousemove', onMouseMove);
            document.addEventListener('click', onPlanetClick);
        }

        function setupTouchControls() {
            let touchStartX, touchStartY;
            let isTouchMoving = false;
            
            document.addEventListener('touchstart', (e) => {
                touchStartX = e.touches[0].clientX;
                touchStartY = e.touches[0].clientY;
                isTouchMoving = false;
                
                // Обновляем позицию мыши для Raycaster
                mouse.x = (touchStartX / window.innerWidth) * 2 - 1;
                mouse.y = -(touchStartY / window.innerHeight) * 2 + 1;
                
                // Проверяем, не нажали ли на планету
                checkPlanetHover();
            });
            
            document.addEventListener('touchmove', (e) => {
                isTouchMoving = true;
                const touchX = e.touches[0].clientX;
                const touchY = e.touches[0].clientY;
                
                // Обновляем позицию мыши
                mouse.x = (touchX / window.innerWidth) * 2 - 1;
                mouse.y = -(touchY / window.innerHeight) * 2 + 1;
                
                // Обновляем подписи
                updateLabelPositions();
            });
            
            document.addEventListener('touchend', (e) => {
                if (!isTouchMoving) {
                    // Это было короткое касание (тап), а не перемещение
                    onPlanetClick();
                }
            });
        }

        function createSolarSystem() {
            // Звездный фон
            createStars();
            
            // Создание планет
            for (const [name, params] of Object.entries(planetParams)) {
                createPlanet(name, params);
            }
            
            // Освещение
            const ambientLight = new THREE.AmbientLight(0x333333);
            scene.add(ambientLight);
            
            const sunLight = new THREE.PointLight(0xffcc00, 1.5, 300);
            scene.add(sunLight);
        }

        function createStars() {
            const geometry = new THREE.BufferGeometry();
            const material = new THREE.PointsMaterial({
                color: 0xffffff,
                size: 0.2,
                sizeAttenuation: true
            });
            
            const vertices = [];
            for (let i = 0; i < 5000; i++) {
                vertices.push(
                    (Math.random() - 0.5) * 2000,
                    (Math.random() - 0.5) * 2000,
                    (Math.random() - 0.5) * 2000
                );
            }
            
            geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3));
            const stars = new THREE.Points(geometry, material);
            scene.add(stars);
        }

        function createPlanet(name, params) {
            const geometry = new THREE.SphereGeometry(params.radius, 32, 32);
            const material = new THREE.MeshPhongMaterial({ 
                color: params.color,
                specular: 0xffffff,
                shininess: 30
            });
            
            const mesh = new THREE.Mesh(geometry, material);
            
            if (name !== 'sun') {
                mesh.position.x = params.distance;
                
                // Орбита (пунктирная для карликовых планет)
                const orbitGeometry = new THREE.RingGeometry(params.distance - 0.1, params.distance + 0.1, 64);
                const orbitMaterial = new THREE.MeshBasicMaterial({ 
                    color: params.isDwarf ? 0x888888 : 0x4488ff, 
                    side: THREE.DoubleSide,
                    transparent: true,
                    opacity: params.isDwarf ? 0.1 : 0.2,
                    wireframe: params.isDwarf // Пунктирная орбита для карликовых планет
                });
                const orbit = new THREE.Mesh(orbitGeometry, orbitMaterial);
                orbit.rotation.x = Math.PI / 2;
                scene.add(orbit);
            }
            
            scene.add(mesh);
            
            // Создаем подпись для планеты
            createPlanetLabel(name, mesh, params.isDwarf);
            
            planets[name] = { 
                mesh: mesh, 
                angle: Math.random() * Math.PI * 2,
                speed: params.rotationSpeed,
                distance: params.distance,
                baseRadius: params.radius,
                isDwarf: params.isDwarf || false
            };
        }

        function createPlanetLabel(planetName, mesh, isDwarf) {
            const label = document.createElement('div');
            label.className = 'planet-label' + (isDwarf ? ' dwarf' : '');
            label.textContent = planetInfo[planetName].name;
            label.id = `label-${planetName}`;
            document.body.appendChild(label);
            
            planetLabels[planetName] = {
                element: label,
                mesh: mesh
            };
        }

        function createInfoCard() {
            infoCard = document.createElement('div');
            infoCard.className = 'info-card';
            document.body.appendChild(infoCard);
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
            updateLabelPositions();
        }

        function onMouseMove(event) {
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
            
            // Позиционируем информационную карточку рядом с курсором
            infoCard.style.left = (event.clientX + 15) + 'px';
            infoCard.style.top = (event.clientY + 15) + 'px';
            
            // Обновляем позиции подписей
            updateLabelPositions();
            
            // Проверяем наведение на планету
            checkPlanetHover();
        }

        function updateLabelPositions() {
            for (const [name, label] of Object.entries(planetLabels)) {
                const mesh = label.mesh;
                const vector = new THREE.Vector3();
                
                // Получаем позицию планеты в экранных координатах
                vector.setFromMatrixPosition(mesh.matrixWorld);
                vector.project(camera);
                
                const x = (vector.x * 0.5 + 0.5) * window.innerWidth;
                const y = -(vector.y * 0.5 - 0.5) * window.innerHeight;
                
                // Обновляем позицию подписи
                label.element.style.left = x + 'px';
                label.element.style.top = y + 'px';
            }
        }

        function checkPlanetHover() {
            // Обновляем Raycaster с текущей позицией мыши и камерой
            raycaster.setFromCamera(mouse, camera);
            
            // Собираем все меши планет для проверки пересечения
            const planetMeshes = [];
            const planetMeshMap = new Map();
            
            for (const [name, planet] of Object.entries(planets)) {
                planetMeshes.push(planet.mesh);
                planetMeshMap.set(planet.mesh, name);
            }
            
            // Проверяем пересечения
            const intersects = raycaster.intersectObjects(planetMeshes);
            
            if (intersects.length > 0) {
                const planetMesh = intersects[0].object;
                const planetName = planetMeshMap.get(planetMesh);
                
                // Показываем информацию о планете
                showPlanetInfo(planetName);
            } else {
                // Скрываем информационную карточку
                hidePlanetInfo();
            }
        }

        function showPlanetInfo(planetName) {
            const info = planetInfo[planetName];
            infoCard.innerHTML = `
                <h3>${info.name}${info.isDwarf ? ' (карликовая планета)' : ''}</h3>
                <p>${info.description}</p>
                <p><strong>Диаметр:</strong> ${info.diameter}</p>
                <p><strong>Масса:</strong> ${info.mass}</p>
                <a href="${info.wiki}" target="_blank">Узнать больше в Википедии</a>
            `;
            infoCard.classList.add('visible');
            
            // Подсвечиваем подпись планеты
            const label = document.getElementById(`label-${planetName}`);
            if (label) {
                label.style.background = planets[planetName].isDwarf ? 
                    'rgba(50, 50, 50, 0.9)' : 'rgba(0, 50, 100, 0.9)';
                label.style.borderColor = 'rgba(100, 200, 255, 0.8)';
                label.style.boxShadow = '0 0 10px rgba(100, 200, 255, 0.5)';
            }
        }

        function hidePlanetInfo() {
            infoCard.classList.remove('visible');
            
            // Убираем подсветку со всех подписей
            document.querySelectorAll('.planet-label').forEach(label => {
                const planetName = label.id.replace('label-', '');
                const isDwarf = planets[planetName]?.isDwarf;
                label.style.background = isDwarf ? 'rgba(0, 0, 0, 0.5)' : 'rgba(0, 0, 0, 0.7)';
                label.style.borderColor = 'rgba(100, 200, 255, 0.5)';
                label.style.boxShadow = 'none';
            });
        }

        function onPlanetClick() {
            raycaster.setFromCamera(mouse, camera);
            
            const planetMeshes = [];
            const planetMeshMap = new Map();
            
            for (const [name, planet] of Object.entries(planets)) {
                planetMeshes.push(planet.mesh);
                planetMeshMap.set(planet.mesh, name);
            }
            
            const intersects = raycaster.intersectObjects(planetMeshes);
            
            if (intersects.length > 0) {
                const planetMesh = intersects[0].object;
                const planetName = planetMeshMap.get(planetMesh);
                
                // Открываем Google поиск для планеты
                const searchQuery = encodeURIComponent(planetInfo[planetName].search);
                window.open(`https://www.google.com/search?q=${searchQuery}&tbm=isch`, '_blank');
            }
        }

        function updatePlanetSizes() {
            for (const [name, planet] of Object.entries(planets)) {
                planet.mesh.scale.set(planetSize, planetSize, planetSize);
            }
            updateLabelPositions();
        }

        function focusOnPlanet(planetName) {
            const planet = planets[planetName];
            if (planet) {
                controls.target.copy(planet.mesh.position);
                
                if (planetName === 'sun') {
                    camera.position.set(0, 0, planet.baseRadius * 3 * planetSize);
                } else {
                    const distance = planet.baseRadius * 5 * planetSize;
                    camera.position.set(
                        planet.mesh.position.x + distance,
                        planet.mesh.position.y,
                        planet.mesh.position.z + distance
                    );
                }
            }
        }

        function animate() {
            requestAnimationFrame(animate);
            
            // Вращение планет
            for (const [name, planet] of Object.entries(planets)) {
                if (name !== 'sun') {
                    planet.angle += planet.speed * rotationSpeed * 0.1;
                    planet.mesh.position.x = Math.cos(planet.angle) * planet.distance;
                    planet.mesh.position.z = Math.sin(planet.angle) * planet.distance;
                    planet.mesh.rotation.y += planet.speed * rotationSpeed;
                } else {
                    planet.mesh.rotation.y += planet.speed * rotationSpeed;
                }
            }
            
            // Обновляем позиции подписей
            updateLabelPositions();
            
            controls.update();
            renderer.render(scene, camera);
        }

        // Запуск приложения
        window.addEventListener('load', init);
    </script>
</body>
</html>
