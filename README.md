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
    <meta property="og:image" content="https://raw.githubusercontent.com/username/repository/main/preview.jpg">
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
        
        .github-corner {
            position: absolute;
            top: 0;
            right: 0;
            z-index: 1000;
        }
        
        .github-corner svg {
            fill: #4db8ff;
            color: #000;
            position: absolute;
            top: 0;
            border: 0;
            right: 0;
        }
        
        .github-corner:hover .octo-arm {
            animation: octocat-wave 560ms ease-in-out;
        }
        
        @keyframes octocat-wave {
            0%, 100% { transform: rotate(0); }
            20%, 60% { transform: rotate(-25deg); }
            40%, 80% { transform: rotate(10deg); }
        }
        
        @media (max-width: 500px) {
            .github-corner:hover .octo-arm {
                animation: none;
            }
            .github-corner .octo-arm {
                animation: octocat-wave 560ms ease-in-out;
            }
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
    </style>
</head>
<body>
    <div id="container"></div>
    
    <a href="https://github.com/your-username/solar-system" class="github-corner" aria-label="View source on GitHub">
        <svg width="80" height="80" viewBox="0 0 250 250" aria-hidden="true">
            <path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
            <path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path>
            <path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path>
        </svg>
    </a>
    
    <div id="title">СОЛНЕЧНАЯ СИСТЕМА</div>
    <div id="instructions">Наведите на планету для информации, кликните для поиска</div>
    
    <div id="ui">
        <div class="control-panel">
            <div class="panel-header">
                <h3>Управление системой</h3>
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
        Создано с Three.js • <span id="fps-counter">60 FPS</span>
    </div>
    
    <div class="loading" id="loading">Загрузка солнечной системы...</div>

    <script>
        // Конфигурация для GitHub Pages
        const CONFIG = {
            useCDN: true,
            enableStats: true,
            defaultSpeed: 1.0,
            mobileOptimized: true
        };

        // Основные переменные
        let scene, camera, renderer, controls;
        let planets = {};
        let rotationSpeed = CONFIG.defaultSpeed;
        let planetSize = 1;
        let currentFocus = 'sun';
        let raycaster, mouse;
        let isMobile = /iPhone|iPad|iPod|Android/i.test(navigator.userAgent);
        let stats;
        let frameCount = 0;
        let lastTime = performance.now();
        let fps = 60;

        // Информация о планетах
        const planetInfo = {
            sun: { name: "Солнце", description: "Звезда солнечной системы", diameter: "1,391,000 км", wiki: "https://ru.wikipedia.org/wiki/Солнце", search: "Sun" },
            mercury: { name: "Меркурий", description: "Ближайшая к Солнцу планета", diameter: "4,879 км", wiki: "https://ru.wikipedia.org/wiki/Меркурий", search: "Mercury" },
            venus: { name: "Венера", description: "Вторая планета от Солнца", diameter: "12,104 км", wiki: "https://ru.wikipedia.org/wiki/Венера", search: "Venus" },
            earth: { name: "Земля", description: "Наш дом в космосе", diameter: "12,742 км", wiki: "https://ru.wikipedia.org/wiki/Земля", search: "Earth" },
            mars: { name: "Марс", description: "Красная планета", diameter: "6,779 км", wiki: "https://ru.wikipedia.org/wiki/Марс", search: "Mars" },
            jupiter: { name: "Юпитер", description: "Крупнейшая планета", diameter: "139,820 км", wiki: "https://ru.wikipedia.org/wiki/Юпитер", search: "Jupiter" },
            saturn: { name: "Сатурн", description: "Планета с кольцами", diameter: "116,460 км", wiki: "https://ru.wikipedia.org/wiki/Сатурн", search: "Saturn" },
            uranus: { name: "Уран", description: "Ледяной гигант", diameter: "50,724 км", wiki: "https://ru.wikipedia.org/wiki/Уран", search: "Uranus" },
            neptune: { name: "Нептун", description: "Самая дальняя планета", diameter: "49,244 км", wiki: "https://ru.wikipedia.org/wiki/Нептун", search: "Neptune" }
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
            neptune: { radius: 1.8, distance: 65, rotationSpeed: 0.006, color: 0x3366ff }
        };

        // Загрузка скриптов
        function loadScript(src) {
            return new Promise((resolve, reject) => {
                const script = document.createElement('script');
                script.src = src;
                script.onload = resolve;
                script.onerror = reject;
                document.head.appendChild(script);
            });
        }

        // Инициализация
        async function init() {
            try {
                // Загрузка Three.js из CDN
                await loadScript('https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js');
                await loadScript('https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.min.js');
                
                if (CONFIG.enableStats) {
                    await loadScript('https://cdn.jsdelivr.net/npm/stats.js@0.17.0/build/stats.min.js');
                    setupStats();
                }

                setupScene();
                setupEventListeners();
                createSolarSystem();
                
                document.getElementById('loading').style.display = 'none';
                animate();

            } catch (error) {
                console.error('Error loading scripts:', error);
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
            controls.maxDistance = 200;
            
            // Raycaster для взаимодействия
            raycaster = new THREE.Raycaster();
            mouse = new THREE.Vector2();
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
            
            // Мобильное управление
            if (isMobile) {
                document.getElementById('zoom-in').addEventListener('click', () => {
                    camera.position.z -= 5;
                    if (camera.position.z < 10) camera.position.z = 10;
                });
                
                document.getElementById('zoom-out').addEventListener('click', () => {
                    camera.position.z += 5;
                    if (camera.position.z > 200) camera.position.z = 200;
                });
                
                document.getElementById('reset-camera').addEventListener('click', () => {
                    focusOnPlanet('sun');
                });
            }
            
            // Взаимодействие с планетами
            document.addEventListener('mousemove', onMouseMove);
            document.addEventListener('click', onPlanetClick);
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
            scene.add(new THREE.Points(geometry, material));
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
                
                // Орбита
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
            }
            
            scene.add(mesh);
            
            planets[name] = { 
                mesh: mesh, 
                angle: Math.random() * Math.PI * 2,
                speed: params.rotationSpeed,
                distance: params.distance,
                baseRadius: params.radius
            };
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function onMouseMove(event) {
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
        }

        function onPlanetClick() {
            raycaster.setFromCamera(mouse, camera);
            const planetMeshes = Object.values(planets).map(p => p.mesh);
            const intersects = raycaster.intersectObjects(planetMeshes);
            
            if (intersects.length > 0) {
                const planetName = findPlanetName(intersects[0].object);
                if (planetName) {
                    window.open(planetInfo[planetName].wiki, '_blank');
                }
            }
        }

        function findPlanetName(mesh) {
            for (const [name, planet] of Object.entries(planets)) {
                if (planet.mesh === mesh) return name;
            }
            return null;
        }

        function updatePlanetSizes() {
            for (const [name, planet] of Object.entries(planets)) {
                planet.mesh.scale.set(planetSize, planetSize, planetSize);
            }
        }

        function focusOnPlanet(planetName) {
            const planet = planets[planetName];
            if (planet) {
                controls.target.copy(planet.mesh.position);
                camera.position.set(
                    planet.mesh.position.x,
                    planet.mesh.position.y,
                    planet.mesh.position.z + planet.baseRadius * 3
                );
            }
        }

        function setupStats() {
            stats = new Stats();
            stats.showPanel(0);
            document.body.appendChild(stats.dom);
            stats.dom.style.left = 'auto';
            stats.dom.style.right = '0px';
        }

        function updateFPS() {
            frameCount++;
            const currentTime = performance.now();
            if (currentTime - lastTime >= 1000) {
                fps = Math.round((frameCount * 1000) / (currentTime - lastTime));
                document.getElementById('fps-counter').textContent = `${fps} FPS`;
                frameCount = 0;
                lastTime = currentTime;
            }
        }

        function animate() {
            requestAnimationFrame(animate);
            
            if (CONFIG.enableStats) stats.begin();
            
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
            
            controls.update();
            renderer.render(scene, camera);
            
            updateFPS();
            if (CONFIG.enableStats) stats.end();
        }

        // Запуск приложения
        window.addEventListener('load', init);

        // Service Worker для оффлайн работы (опционально)
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('/sw.js').catch(console.error);
        }
    </script>
</body>
</html>
