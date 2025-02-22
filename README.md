# 3d-simulating
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Manyetik Alan Simülasyonu</title>
    <style>
        body { margin: 0; overflow: hidden; }
        canvas { display: block; }
        #controls {
            position: absolute;
            top: 10px;
            left: 10px;
            background: rgba(255, 255, 255, 0.8);
            padding: 10px;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <div id="controls">
        <label>Manyetik Alan Gücü: <input type="range" id="BStrength" min="0.1" max="5" step="0.1" value="1"></label><br>
        <label>Başlangıç Hızı: <input type="range" id="velocity" min="0.01" max="0.1" step="0.01" value="0.02"></label>
    </div>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script>
        // Three.js Sahne Kurulumu
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Manyetik alanın yönünü temsil eden eksen
        const axesHelper = new THREE.AxesHelper(5);
        scene.add(axesHelper);

        // Parçacık (Küçük bir küre)
        const geometry = new THREE.SphereGeometry(0.2, 32, 32);
        const material = new THREE.MeshBasicMaterial({ color: 0xff0000 });
        const particle = new THREE.Mesh(geometry, material);
        scene.add(particle);

        camera.position.z = 5;

        // Manyetik alan ve hız vektörleri
        let velocity = new THREE.Vector3(0.02, 0, 0); // Başlangıç hızı
        let B_field = new THREE.Vector3(0, 0, 1); // Manyetik alan yönü (z ekseni)
        const charge = 1; // Varsayılan yük

        function lorentzForce(v, B, q) {
            return new THREE.Vector3().crossVectors(v, B).multiplyScalar(q * 0.001); // Küçük bir ölçeklendirme
        }

        // Kullanıcı girişlerini dinle
        document.getElementById('BStrength').addEventListener('input', (event) => {
            let strength = parseFloat(event.target.value);
            B_field.set(0, 0, strength);
        });

        document.getElementById('velocity').addEventListener('input', (event) => {
            let speed = parseFloat(event.target.value);
            velocity.set(speed, 0, 0);
        });

        // Animasyon Döngüsü
        function animate() {
            requestAnimationFrame(animate);
            
            // Lorentz kuvvetini uygula
            let force = lorentzForce(velocity, B_field, charge);
            velocity.add(force);
            particle.position.add(velocity);
            
            renderer.render(scene, camera);
        }
        animate();
    </script>
</body>
</html>
