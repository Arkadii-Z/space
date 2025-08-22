<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>3D Солнечная система для смартфонов</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
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
            font-family: 'Arial', sans-serif;
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
            touch-action: none;
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
            touch-action: none;
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
        
        .planet-btn:hover {
            transform: scale(1.2);
            box-shadow: 0 0 15px rgba(100, 200, 255, 0.8);
        }
        
        .planet-btn.active {
            transform: scale(1.3);
            box-shadow: 0 0 20px rgba(0, 200, 255, 1);
            border-color: white;
        }
        
        #title {
            position: absolute;
            top: 10px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 18px;
            text-shadow: 0 0 10px rgba(0, 200, 255, 0.8);
            letter-spacing: 1px;
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
        
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            z-index: 1000;
            backdrop-filter: blur(5px);
        }
        
        .modal-content {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(10, 30, 50, 0.95);
            padding: 15px;
            border-radius: 12px;
            width: 95%;
            max-width: 500px;
            height: 80%;
            display: flex;
            flex-direction: column;
            box-shadow: 0 0 20px rgba(0, 150, 255, 0.5);
            border: 1px solid rgba(100, 200, 255, 0.3);
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 12px;
            padding-bottom: 8px;
            border-bottom: 1px solid rgba(100, 200, 255, 0.3);
        }
        
        .modal-header h2 {
            color: #4db8ff;
            font-size: 18px;
        }
        
        .close-btn {
            background: none;
            border: none;
            color: #4db8ff;
            font-size: 20px;
            cursor: pointer;
            transition: all 0.3s ease;
            padding: 5px;
        }
        
        .close-btn:hover {
            color: #ff4d4d;
            transform: scale(1.2);
        }
        
        .modal-body {
            flex: 1;
            display: flex;
            flex-direction: column;
        }
        
        .search-container {
            display: flex;
            margin-bottom: 12px;
        }
        
        #planet-search {
            flex: 1;
            padding: 8px;
            border-radius: 5px 0 0 5px;
            border: 1px solid rgba(100, 200, 255, 0.3);
            background: rgba(0, 20, 40, 0.8);
            color: white;
            outline: none;
            font-size: 14px;
        }
        
        #search-btn {
            padding: 8px 12px;
            background: #4db8ff;
            border: none;
            border-radius: 0 5px 5px 0;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 14px;
        }
        
        #search-btn:hover {
            background: #3399ff;
        }
        
        #google-frame {
            flex: 1;
            border: none;
            border-radius: 5px;
            background: white;
        }
        
        .camera-hint {
            position: absolute;
            bottom: 100px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 12px;
            color: #a0d0ff;
            text-shadow: 0 0 5px rgba(0, 100, 255, 0.5);
            opacity: 0.8;
            pointer-events: none;
            padding: 0 10px;
        }
        
        .controls-hint {
            position: absolute;
            bottom: 120px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 12px;
            color: #a0d0ff;
            text-shadow: 0 0 5px rgba(0, 100, 255, 0.5);
            opacity: 0.8;
            pointer-events: none;
            padding: 0 10px;
        }
        
        .ui-draggable {
            cursor: move;
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
        
        .mobile-controls {
            position: absolute;
            right: 10px;
            bottom: 100px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 90;
            touch-action: none;
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
            touch-action: none;
            user-select: none;
        }
        
        .mobile-btn:active {
            background: rgba(0, 50, 100, 0.9);
            transform: scale(0.95);
        }
        
        .touch-joystick {
            position: absolute;
            left: 10px;
            bottom: 100px;
            width: 100px;
            height: 100px;
            background: rgba(0, 30, 60, 0.5);
            border-radius: 50%;
            border: 2px solid rgba(100, 200, 255, 0.3);
            touch-action: none;
            z-index: 90;
        }
        
        .joystick-handle {
            position: absolute;
            width: 40px;
            height: 40px;
            background: rgba(100, 200, 255, 0.7);
            border-radius: 50%;
            top: 30px;
            left: 30px;
            transition: transform 0.1s ease;
        }
        
        .touch-controls-hint {
            position: absolute;
            bottom: 60px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 12px;
            color: #a0d0ff;
            opacity: 0.8;
            pointer-events: none;
        }
        
        @media (max-width: 768px) {
            .control-panel {
                max-width: 100%;
                margin: 0 10px;
            }
            
            .planet-btn {
                width: 32px;
                height: 32px;
                font-size: 10px;
            }
            
            #title {
                font-size: 16px;
            }
            
            #instructions {
                font-size: 11px;
                top: 35px;
            }
        }
        
        @media (max-width: 480px) {
            .control-panel {
                padding: 10px;
            }
            
            .panel-header h3 {
                font-size: 14px;
            }
            
            .planet-selector {
                gap: 5px;
            }
            
            .planet-btn {
                width: 28px;
                height: 28px;
            }
            
            .mobile-btn {
                width: 45px;
                height: 45px;
                font-size: 18px;
            }
            
            .touch-joystick {
                width: 80px;
                height: 80px;
            }
            
            .joystick-handle {
                width: 30px;
                height: 30px;
                top: 25px;
                left: 25px;
            }
        }

        /* Стили для предотвращения выделения текста на мобильных */
        .no-select {
            -webkit-user-select: none;
            -moz-user-select: none;
            -ms-user-select: none;
            user-select: none;
        }
    </style>
</head>
<body class="no-select">
    <div id="container"></div>
    
    <div id="title">СОЛНЕЧНАЯ СИСТЕМА</div>
    <div id="instructions">Коснитесь планеты для информации, нажмите для поиска</div>
    <div class="controls-hint">Два пальца для масштабирования, один палец для вращения</div>
    <div class="camera-hint">Используйте кнопки для управления камерой</div>
    <div class="touch-controls-hint">Используйте джойстик для перемещения</div>
    
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
                    <div class="planet-btn active" data-planet="sun" title="Солнце"><i class="fas fa-sun"></i></div>
                    <div class="planet-btn" data-planet="mercury" title="Меркурий"><i class="fas fa-circle"></i></div>
                    <div class="planet-btn" data-planet="venus" title="Венера"><i class="fas fa-circle"></i></div>
                    <div class="planet-btn" data-planet="earth" title="Земля"><i class="fas fa-globe-americas"></i></div>
                    <div class="planet-btn" data-planet="mars" title="Марс"><i class="fas fa-circle"></i></div>
                    <div class="planet-btn" data-planet="jupiter" title="Юпитер"><i class="fas fa-circle"></i></div>
                    <div class="planet-btn" data-planet="saturn" title="Сатурн"><i class="fas fa-ring"></i></div>
                    <div class="planet-btn" data-planet="uranus" title="Уран"><i class="fas fa-circle"></i></div>
                    <div class="planet-btn" data-planet="neptune" title="Нептун"><i class="fas fa-circle"></i></div>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Мобильные элементы управления -->
    <div class="mobile-controls">
        <button class="mobile-btn" id="zoom-in"><i class="fas fa-plus"></i></button>
        <button class="mobile-btn" id="zoom-out"><i class="fas fa-minus"></i></button>
        <button class="mobile-btn" id="reset-camera"><i class="fas fa-home"></i></button>
    </div>
    
    <div class="touch-joystick" id="joystick-area">
        <div class="joystick-handle" id="joystick-handle"></div>
    </div>
    
    <div class="modal" id="google-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2 id="modal-title">Поиск изображений</h2>
                <button class="close-btn">&times;</button>
            </div>
            <div class="modal-body">
                <div class="search-container">
                    <input type="text" id="planet-search" placeholder="Введите название...">
                    <button id="search-btn"><i class="fas fa-search"></i></button>
                </div>
                <iframe id="google-frame" src="about:blank"></iframe>
            </div>
        </div>
    </div>
    
    <div class="loading" id="loading">Загрузка солнечной системы...</div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.min.js"></script>
    <script>
        // Основные переменные
        let scene, camera, renderer, controls;
        let planets = {};
        let rotationSpeed = 1;
        let planetSize = 1;
        let currentFocus = 'sun';
        let planetLabels = {};
        let infoCard = null;
        let raycaster, mouse;
        let isDragging = false;
        let dragOffset = { x: 0, y: 0 };
        let isMobile = /iPhone|iPad|iPod|Android/i.test(navigator.userAgent);
        let joystickActive = false;
        let joystickDirection = { x: 0, y: 0 };
        
        // Информация о планетах
        const planetInfo = {
            sun: {
                name: "Солнце",
                description: "Звезда, вокруг которой обращаются все планеты системы.",
                diameter: "1 391 000 км",
                mass: "1.989 × 10^30 кг",
                wiki: "https://ru.wikipedia.org/wiki/Солнце",
                search: "Sun star"
            },
            mercury: {
                name: "Меркурий",
                description: "Ближайшая к Солнцу планета. Не имеет спутников.",
                diameter: "4 879 км",
                mass: "3.301 × 10^23 кг",
                wiki: "https://ru.wikipedia.org/wiki/Меркурий",
                search: "Mercury planet"
            },
            venus: {
                name: "Венера",
                description: "Вторая планета от Солнца. Плотная атмосфера из CO2.",
                diameter: "12 104 км",
                mass: "4.867 × 10^24 кг",
                wiki: "https://ru.wikipedia.org/wiki/Венера",
                search: "Venus planet"
            },
            earth: {
                name: "Земля",
                description: "Третья планета от Солнца. Единственная обитаемая планета.",
                diameter: "12 742 км",
                mass: "5.972 × 10^24 кг",
                wiki: "https://ru.wikipedia.org/wiki/Земля",
                search: "Earth planet"
            },
            mars: {
                name: "Марс",
                description: "Четвёртая планета от Солнца. 'Красная планета'.",
                diameter: "6 779 км",
                mass: "6.417 × 10^23 кг",
                wiki: "https://ru.wikipedia.org/wiki/Марс",
                search: "Mars planet"
            },
            jupiter: {
                name: "Юпитер",
                description: "Крупнейшая планета. Газовый гигант с большим красным пятном.",
                diameter: "139 820 км",
                mass: "1.898 × 10^27 кг",
                wiki: "https://ru.wikipedia.org/wiki/Юпитер",
                search: "Jupiter planet"
            },
            saturn: {
                name: "Сатурн",
                description: "Шестая планета. Известна своими кольцами.",
                diameter: "116 460 км",
                mass: "5.683 × 10^26 кг",
                wiki: "https://ru.wikipedia.org/wiki/Сатурн",
                search: "Saturn planet"
            },
            uranus: {
                name: "Уран",
                description: "Седьмая планета. Имеет боковое вращение.",
                diameter: "50 724 км",
                mass: "8.681 × 10^25 кг",
                wiki: "https://ru.wikipedia.org/wiki/Уран",
                search: "Uranus planet"
            },
            neptune: {
                name: "Нептун",
                description: "Восьмая и самая дальняя планета Солнечной системы.",
                diameter: "49 244 км",
                mass: "1.024 × 10^26 кг",
                wiki: "https://ru.wikipedia.org/wiki/Нептун",
                search: "Neptune planet"
            },
            moon: {
                name: "Луна",
                description: "Естественный спутник Земли.",
                diameter: "3 474 км",
                mass: "7.342 × 10^22 кг",
                wiki: "https://ru.wikipedia.org/wiki/Луна",
                search: "Moon"
            }
        };

        // Параметры планет
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
            moon: { radius: 0.4, distance: 2.5, rotationSpeed: 0.003, color: 0xdddddd }
        };

        // Инициализация сцены
        function init() {
            // Создание сцены
            scene = new THREE.Scene();
            
            // Создание камеры с учетом мобильных устройств
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.z = isMobile ? 70 : 50;
            
            // Создание рендерера
            renderer = new THREE.WebGLRenderer({ 
                antialias: true, 
                alpha: true,
                powerPreference: "high-performance"
            });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2)); // Ограничиваем для производительности
            document.getElementById('container').appendChild(renderer.domElement);
            
            // Настройка управления для мобильных
            if (isMobile) {
                setupMobileControls();
                setupTouchJoystick();
            } else {
                // Добавление управления камерой для десктопа
                controls = new THREE.OrbitControls(camera, renderer.domElement);
                controls.enableDamping = true;
                controls.dampingFactor = 0.05;
                controls.minDistance = 5;
                controls.maxDistance = 200;
            }
            
            // Инициализация Raycaster
            raycaster = new THREE.Raycaster();
            mouse = new THREE.Vector2();
            
            // Создание информационной карточки
            createInfoCard();
            
            // Создание звездного фона
            createStars();
            
            // Создание Солнца и планет
            createSun();
            createPlanets();
            createMoon();
            
            // Добавление освещения
            const ambientLight = new THREE.AmbientLight(0x333333);
            scene.add(ambientLight);
            
            const sunLight = new THREE.PointLight(0xffcc00, 1.5, 300);
            scene.add(sunLight);
            
            // Обработка изменений размера окна
            window.addEventListener('resize', onWindowResize);
            
            // Настройка элементов управления
            setupUIEvents();
            
            // Скрыть загрузочный экран
            document.getElementById('loading').style.display = 'none';
            
            // Запуск анимации
            animate();
        }

        // Настройка элементов управления UI
        function setupUIEvents() {
            document.getElementById('rotation-speed').addEventListener('input', function(e) {
                rotationSpeed = parseFloat(e.target.value);
                document.getElementById('speed-value').textContent = rotationSpeed.toFixed(1) + 'x';
            });
            
            document.getElementById('planet-size').addEventListener('input', function(e) {
                planetSize = parseFloat(e.target.value);
                document.getElementById('size-value').textContent = planetSize.toFixed(1) + 'x';
                updatePlanetSizes();
            });
            
            // Обработка выбора планет
            document.querySelectorAll('.planet-btn').forEach(btn => {
                btn.addEventListener('click', function(e) {
                    e.stopPropagation();
                    document.querySelectorAll('.planet-btn').forEach(b => b.classList.remove('active'));
                    this.classList.add('active');
                    currentFocus = this.getAttribute('data-planet');
                    focusOnPlanet(currentFocus);
                });
            });
            
            // Обработка сворачивания/разворачивания панели
            document.getElementById('toggle-panel').addEventListener('click', function(e) {
                e.stopPropagation();
                const panel = document.getElementById('main-panel');
                panel.classList.toggle('collapsed');
                this.textContent = panel.classList.contains('collapsed') ? '+' : '−';
            });
            
            // Добавляем возможность перетаскивания панели
            setupPanelDragging();
            
            // Обработка закрытия модального окна
            document.querySelector('.close-btn').addEventListener('click', closeModal);
            document.getElementById('search-btn').addEventListener('click', performSearch);
            
            // Обработка касаний для информации о планетах
            if (isMobile) {
                document.addEventListener('touchstart', onTouchStart, { passive: false });
                document.addEventListener('touchmove', onTouchMove, { passive: false });
                document.addEventListener('touchend', onTouchEnd);
            } else {
                document.addEventListener('mousemove', onMouseMove);
                document.addEventListener('click', onPlanetClick);
            }
        }

        // Настройка мобильного управления
        function setupMobileControls() {
            // Кнопки масштабирования
            document.getElementById('zoom-in').addEventListener('touchstart', function(e) {
                e.preventDefault();
                camera.position.z -= 5;
                if (camera.position.z < 10) camera.position.z = 10;
            });
            
            document.getElementById('zoom-out').addEventListener('touchstart', function(e) {
                e.preventDefault();
                camera.position.z += 5;
                if (camera.position.z > 200) camera.position.z = 200;
            });
            
            // Кнопка сброса камеры
            document.getElementById('reset-camera').addEventListener('touchstart', function(e) {
                e.preventDefault();
                focusOnPlanet('sun');
            });
        }

        // Настройка джойстика для мобильных
        function setupTouchJoystick() {
            const joystickArea = document.getElementById('joystick-area');
            const joystickHandle = document.getElementById('joystick-handle');
            let touchId = null;
            
            joystickArea.addEventListener('touchstart', function(e) {
                e.preventDefault();
                if (touchId !== null) return;
                
                const touch = e.touches[0];
                touchId = touch.identifier;
                joystickActive = true;
                updateJoystickPosition(touch);
            });
            
            joystickArea.addEventListener('touchmove', function(e) {
                e.preventDefault();
                if (!joystickActive) return;
                
                for (let i = 0; i < e.touches.length; i++) {
                    if (e.touches[i].identifier === touchId) {
                        updateJoystickPosition(e.touches[i]);
                        break;
                    }
                }
            });
            
            joystickArea.addEventListener('touchend', function(e) {
                e.preventDefault();
                if (!joystickActive) return;
                
                for (let i = 0; i < e.changedTouches.length; i++) {
                    if (e.changedTouches[i].identifier === touchId) {
                        resetJoystick();
                        break;
                    }
                }
            });
            
            function updateJoystickPosition(touch) {
                const rect = joystickArea.getBoundingClientRect();
                const centerX = rect.left + rect.width / 2;
                const centerY = rect.top + rect.height / 2;
                
                const deltaX = touch.clientX - centerX;
                const deltaY = touch.clientY - centerY;
                
                // Ограничиваем перемещение джойстика
                const distance = Math.min(Math.sqrt(deltaX * deltaX + deltaY * deltaY), rect.width / 2);
                const angle = Math.atan2(deltaY, deltaX);
                
                const limitedX = Math.cos(angle) * distance;
                const limitedY = Math.sin(angle) * distance;
                
                // Обновляем позицию ручки джойстика
                joystickHandle.style.transform = `translate(${limitedX}px, ${limitedY}px)`;
                
                // Нормализуем значения для управления камерой
                joystickDirection.x = limitedX / (rect.width / 2);
                joystickDirection.y = -limitedY / (rect.height / 2);
            }
            
            function resetJoystick() {
                joystickHandle.style.transform = 'translate(0, 0)';
                joystickDirection.x = 0;
                joystickDirection.y = 0;
                joystickActive = false;
                touchId = null;
            }
        }

        // Обработка касаний
        function onTouchStart(e) {
            if (e.target.tagName === 'A' || e.target.closest('a')) {
                return; // Позволяем кликать по ссылкам
            }
            
            if (e.touches.length === 1) {
                // Одиночное касание - проверяем, не нажали ли на планету
                const touch = e.touches[0];
                mouse.x = (touch.clientX / window.innerWidth) * 2 - 1;
                mouse.y = -(touch.clientY / window.innerHeight) * 2 + 1;
                
                checkPlanetHover();
            }
            e.preventDefault();
        }

        function onTouchMove(e) {
            if (e.touches.length === 1 && !joystickActive) {
                // Перемещение камеры одним пальцем
                const touch = e.touches[0];
                mouse.x = (touch.clientX / window.innerWidth) * 2 - 1;
                mouse.y = -(touch.clientY / window.innerHeight) * 2 + 1;
                
                // Вращение камеры
                camera.rotation.y -= e.movementX * 0.01;
                camera.rotation.x -= e.movementY * 0.01;
                
                // Ограничение вращения
                camera.rotation.x = Math.max(-Math.PI/2, Math.min(Math.PI/2, camera.rotation.x));
                
                checkPlanetHover();
            }
            e.preventDefault();
        }

        function onTouchEnd(e) {
            // Проверяем, было ли это короткое касание (не перетаскивание)
            const touch = e.changedTouches[0];
            mouse.x = (touch.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(touch.clientY / window.innerHeight) * 2 + 1;
            
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
                openGoogleModal(planetName);
            }
        }

        // Остальной код (createSun, createPlanets, createMoon и т.д.) остается таким же, как в предыдущей версии
        // Для экономии места я не дублирую эти функции, так как они не изменились

        // Создание Солнца
        function createSun() {
            const geometry = new THREE.SphereGeometry(planetParams.sun.radius, 64, 64);
            const material = new THREE.MeshBasicMaterial({ 
                color: planetParams.sun.color,
                emissive: 0xff9900,
                emissiveIntensity: 0.8
            });
            
            const sun = new THREE.Mesh(geometry, material);
            scene.add(sun);
            
            // Добавляем свечение
            const glowGeometry = new THREE.SphereGeometry(planetParams.sun.radius * 1.2, 32, 32);
            const glowMaterial = new THREE.ShaderMaterial({
                uniforms: {
                    glowColor: { value: new THREE.Color(0xff9900) },
                    viewVector: { value: camera.position }
                },
                vertexShader: `
                    uniform vec3 viewVector;
                    varying float intensity;
                    void main() {
                        vec3 vNormal = normalize(normalMatrix * normal);
                        vec3 vNormel = normalize(normalMatrix * viewVector);
                        intensity = pow(0.4 - dot(vNormal, vNormel), 2.0);
                        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                    }
                `,
                fragmentShader: `
                    uniform vec3 glowColor;
                    varying float intensity;
                    void main() {
                        vec3 glow = glowColor * intensity;
                        gl_FragColor = vec4(glow, 1.0);
                    }
                `,
                side: THREE.BackSide,
                blending: THREE.AdditiveBlending,
                transparent: true
            });
            
            const glow = new THREE.Mesh(glowGeometry, glowMaterial);
            glow.scale.set(1.1, 1.1, 1.1);
            scene.add(glow);
            
            createPlanetLabel('sun', sun);
            
            planets.sun = { 
                mesh: sun, 
                glow: glow, 
                rotationSpeed: planetParams.sun.rotationSpeed,
                baseRadius: planetParams.sun.radius
            };
        }

        // Создание планет
        function createPlanets() {
            for (const [name, params] of Object.entries(planetParams)) {
                if (name === 'sun' || name === 'moon') continue;
                
                const geometry = new THREE.SphereGeometry(params.radius, 64, 64);
                const material = new THREE.MeshPhongMaterial({ 
                    color: params.color,
                    specular: 0xffffff,
                    shininess: 30
                });
                
                const mesh = new THREE.Mesh(geometry, material);
                mesh.position.x = params.distance;
                
                // Создание орбиты
                const orbitGeometry = new THREE.RingGeometry(params.distance - 0.1, params.distance + 0.1, 64);
                const orbitMaterial = new THREE.MeshBasicMaterial({ 
                    color: 0x4488ff, 
                    side: THREE.DoubleSide,
                    transparent: true,
                    opacity: 0.2
                });
                const orbit = new THREE.Mesh(orbitGeometry, orbitMaterial);
                orbit.rotation.x = Math.PI / 2;
                scene.add(orbit);
                
                scene.add(mesh);
                
                // Кольца для Сатурна
                if (name === 'saturn') {
                    const ringGeometry = new THREE.RingGeometry(params.radius * 1.2, params.radius * 2, 32);
                    const ringMaterial = new THREE.MeshBasicMaterial({
                        color: 0xffdbac,
                        side: THREE.DoubleSide,
                        transparent: true,
                        opacity: 0.8
                    });
                    const ring = new THREE.Mesh(ringGeometry, ringMaterial);
                    ring.rotation.x = Math.PI / 2;
                    mesh.add(ring);
                }
                
                createPlanetLabel(name, mesh);
                
                planets[name] = { 
                    mesh: mesh, 
                    angle: Math.random() * Math.PI * 2,
                    speed: params.rotationSpeed,
                    distance: params.distance,
                    baseRadius: params.radius
                };
            }
        }

        // Создание Луны
        function createMoon() {
            const params = planetParams.moon;
            const geometry = new THREE.SphereGeometry(params.radius, 32, 32);
            const material = new THREE.MeshPhongMaterial({ 
                color: params.color,
                specular: 0xffffff,
                shininess: 30
            });
            
            const mesh = new THREE.Mesh(geometry, material);
            
            // Луна вращается вокруг Земли
            const earth = planets.earth.mesh;
            earth.add(mesh);
            mesh.position.x = params.distance;
            
            createPlanetLabel('moon', mesh);
            
            planets.moon = { 
                mesh: mesh, 
                angle: Math.random() * Math.PI * 2,
                speed: params.rotationSpeed,
                distance: params.distance,
                baseRadius: params.radius,
                parent: earth
            };
        }

        // Анимация
        function animate() {
            requestAnimationFrame(animate);
            
            // Обновление камеры для мобильных
            if (isMobile && joystickActive) {
                camera.position.x += joystickDirection.x * 2;
                camera.position.y += joystickDirection.y * 2;
            }
            
            // Вращение Солнца и планет
            planets.sun.mesh.rotation.y += planets.sun.rotationSpeed * rotationSpeed;
            planets.sun.glow.rotation.y += planets.sun.rotationSpeed * rotationSpeed * 0.5;
            
            for (const [name, planet] of Object.entries(planets)) {
                if (name !== 'sun' && name !== 'moon') {
                    planet.angle += planet.speed * rotationSpeed * 0.1;
                    planet.mesh.position.x = Math.cos(planet.angle) * planet.distance;
                    planet.mesh.position.z = Math.sin(planet.angle) * planet.distance;
                    planet.mesh.rotation.y += planet.speed * rotationSpeed;
                }
            }
            
            // Вращение Луны
            if (planets.moon) {
                planets.moon.angle += planets.moon.speed * rotationSpeed * 2;
                planets.moon.mesh.position.x = Math.cos(planets.moon.angle) * planets.moon.distance;
                planets.moon.mesh.position.z = Math.sin(planets.moon.angle) * planets.moon.distance;
                planets.moon.mesh.rotation.y += planets.moon.speed * rotationSpeed;
            }
            
            // Обновление элементов управления
            if (!isMobile) {
                controls.update();
            }
            
            updateLabelPositions();
            renderer.render(scene, camera);
        }

        // Запуск приложения
        window.addEventListener('load', init);
    </script>
</body>
</html>
