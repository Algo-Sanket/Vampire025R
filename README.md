<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Profile Animation - Record as GIF</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 50%, #7e22ce 100%);
            overflow: hidden;
            font-family: 'Courier New', monospace;
        }

        #canvas-container {
            width: 100vw;
            height: 100vh;
            position: relative;
        }

        .info {
            position: absolute;
            top: 20px;
            left: 20px;
            color: white;
            background: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 10px;
            font-size: 14px;
            z-index: 10;
            backdrop-filter: blur(10px);
        }

        .info h2 {
            margin-bottom: 10px;
            color: #60a5fa;
        }

        .info p {
            margin: 5px 0;
            line-height: 1.6;
        }

        .record-btn {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            padding: 15px 30px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 50px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            transition: transform 0.3s;
            z-index: 10;
        }

        .record-btn:hover {
            transform: translateX(-50%) scale(1.05);
        }

        .floating-text {
            position: absolute;
            color: white;
            font-size: 48px;
            font-weight: bold;
            text-shadow: 0 0 20px rgba(96, 165, 250, 0.8);
            pointer-events: none;
            animation: fadeInOut 4s infinite;
        }

        @keyframes fadeInOut {
            0%, 100% { opacity: 0; transform: translateY(20px); }
            10%, 90% { opacity: 1; transform: translateY(0); }
        }

        .name-text {
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 64px;
            background: linear-gradient(45deg, #60a5fa, #c084fc, #f472b6);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
    </style>
</head>
<body>
    <div id="canvas-container"></div>
    
    <div class="info">
        <h2>🎬 How to Create Your GIF</h2>
        <p>1. <strong>Windows:</strong> Use ScreenToGif (free)</p>
        <p>2. <strong>Mac:</strong> Use Gifski or Kap</p>
        <p>3. <strong>Online:</strong> Use Screentogif.com</p>
        <p><br>📹 Record this animation for 5-10 seconds</p>
        <p>💾 Save as GIF and upload to your repo</p>
    </div>

    <div class="name-text">SANKET SRIVASTAVA</div>

    <button class="record-btn" onclick="changeColors()">🎨 Change Colors</button>

    <script>
        let scene, camera, renderer, particles, cube, torus;
        let colorScheme = 0;

        function init() {
            scene = new THREE.Scene();
            
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.z = 30;

            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setClearColor(0x000000, 0);
            document.getElementById('canvas-container').appendChild(renderer.domElement);

            // Create particle system
            const particleGeometry = new THREE.BufferGeometry();
            const particleCount = 1000;
            const positions = new Float32Array(particleCount * 3);

            for(let i = 0; i < particleCount * 3; i++) {
                positions[i] = (Math.random() - 0.5) * 100;
            }

            particleGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            const particleMaterial = new THREE.PointsMaterial({
                color: 0x60a5fa,
                size: 0.3,
                transparent: true,
                opacity: 0.8
            });

            particles = new THREE.Points(particleGeometry, particleMaterial);
            scene.add(particles);

            // Create rotating cube
            const cubeGeometry = new THREE.BoxGeometry(8, 8, 8);
            const cubeMaterial = new THREE.MeshPhongMaterial({
                color: 0x667eea,
                transparent: true,
                opacity: 0.7,
                wireframe: true
            });
            cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
            scene.add(cube);

            // Create torus
            const torusGeometry = new THREE.TorusGeometry(12, 2, 16, 100);
            const torusMaterial = new THREE.MeshPhongMaterial({
                color: 0xc084fc,
                transparent: true,
                opacity: 0.5,
                wireframe: true
            });
            torus = new THREE.Mesh(torusGeometry, torusMaterial);
            scene.add(torus);

            // Lights
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
            scene.add(ambientLight);

            const pointLight = new THREE.PointLight(0x60a5fa, 1, 100);
            pointLight.position.set(10, 10, 10);
            scene.add(pointLight);

            const pointLight2 = new THREE.PointLight(0xc084fc, 1, 100);
            pointLight2.position.set(-10, -10, 10);
            scene.add(pointLight2);

            window.addEventListener('resize', onWindowResize, false);
        }

        function animate() {
            requestAnimationFrame(animate);

            particles.rotation.y += 0.001;
            particles.rotation.x += 0.0005;

            cube.rotation.x += 0.01;
            cube.rotation.y += 0.01;

            torus.rotation.x += 0.005;
            torus.rotation.y += 0.008;

            renderer.render(scene, camera);
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function changeColors() {
            colorScheme = (colorScheme + 1) % 3;
            
            if(colorScheme === 0) {
                particles.material.color.setHex(0x60a5fa);
                cube.material.color.setHex(0x667eea);
                torus.material.color.setHex(0xc084fc);
            } else if(colorScheme === 1) {
                particles.material.color.setHex(0x10b981);
                cube.material.color.setHex(0x06b6d4);
                torus.material.color.setHex(0x8b5cf6);
            } else {
                particles.material.color.setHex(0xf59e0b);
                cube.material.color.setHex(0xef4444);
                torus.material.color.setHex(0xec4899);
            }
        }

        init();
        animate();
    </script>
</body>
</html>
