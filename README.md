<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fea Class - Background Remover</title>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <!-- Font Awesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/all.min.css">
    
    <style>
        :root {
            --primary-color: #00d2ff;
            --secondary-color: #3a7bd5;
            --glass: rgba(255, 255, 255, 0.2);
            --text-color: #ffffff;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Poppins', sans-serif;
        }

        body {
            background: linear-gradient(135deg, #00d2ff 0%, #3a7bd5 100%);
            height: 100vh;
            overflow: hidden;
            color: var(--text-color);
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* Snowfall Canvas */
        #snow-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1;
        }

        /* Welcome Screen */
        #welcome-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            transition: 0.8s ease-in-out;
        }

        .welcome-box {
            text-align: center;
            padding: 40px;
            background: var(--glass);
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.3);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            animation: float 3s ease-in-out infinite;
        }

        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-20px); }
        }

        .btn-get-started {
            margin-top: 20px;
            padding: 12px 35px;
            border: none;
            border-radius: 50px;
            background: #fff;
            color: #3a7bd5;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
            font-size: 1.1rem;
        }

        .btn-get-started:hover {
            transform: scale(1.1);
            box-shadow: 0 0 20px rgba(255,255,255,0.6);
        }

        /* Main App Interface */
        #main-app {
            position: relative;
            width: 90%;
            max-width: 500px;
            height: 85vh;
            background: var(--glass);
            backdrop-filter: blur(15px);
            border-radius: 30px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            z-index: 10;
            box-shadow: 0 15px 35px rgba(0,0,0,0.2);
            overflow-y: auto;
        }

        .header {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .profile-icon {
            font-size: 1.5rem;
            cursor: pointer;
            background: rgba(255,255,255,0.2);
            padding: 10px;
            border-radius: 50%;
        }

        .user-greeting {
            font-size: 1.2rem;
            font-weight: 600;
            align-self: flex-start;
            margin-bottom: 20px;
        }

        .upload-container {
            width: 100%;
            border: 2px dashed rgba(255,255,255,0.5);
            border-radius: 20px;
            padding: 30px;
            text-align: center;
            cursor: pointer;
            transition: 0.3s;
        }

        .upload-container:hover {
            background: rgba(255,255,255,0.1);
        }

        #preview-img {
            max-width: 100%;
            max-height: 250px;
            margin-top: 20px;
            border-radius: 10px;
            display: none;
        }

        .action-buttons {
            display: flex;
            gap: 15px;
            margin-top: 20px;
            display: none;
        }

        .btn-action {
            padding: 10px 20px;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            color: white;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-download { background: #27ae60; }
        .btn-share { background: #e67e22; }

        /* Loader */
        .loader {
            border: 4px solid rgba(255,255,255,0.3);
            border-top: 4px solid #fff;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            display: none;
            margin-top: 10px;
        }

        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        /* Profile & History Modal */
        #profile-modal {
            position: fixed;
            right: -100%;
            top: 0;
            width: 300px;
            height: 100%;
            background: rgba(255, 255, 255, 0.95);
            color: #333;
            z-index: 101;
            padding: 20px;
            transition: 0.5s;
            box-shadow: -5px 0 15px rgba(0,0,0,0.2);
        }

        #profile-modal.active { right: 0; }

        .history-list {
            margin-top: 20px;
            max-height: 60vh;
            overflow-y: auto;
        }

        .history-item {
            width: 50px;
            height: 50px;
            object-fit: cover;
            margin: 5px;
            border-radius: 5px;
            border: 1px solid #ccc;
        }

        /* Developer Footer */
        .dev-footer {
            margin-top: auto;
            padding-top: 20px;
        }

        .btn-dev {
            background: rgba(255,255,255,0.2);
            border: 1px solid white;
            color: white;
            padding: 8px 20px;
            border-radius: 50px;
            cursor: pointer;
        }

        /* Developer Social Box */
        #dev-modal {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: white;
            color: #333;
            padding: 20px;
            border-radius: 20px;
            z-index: 102;
            text-align: center;
            width: 280px;
        }

        .social-btn {
            display: block;
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 10px;
            text-decoration: none;
            color: white;
            font-weight: bold;
        }

        .whatsapp { background: #25d366; }
        .instagram { background: #e1306c; }
        .youtube { background: #ff0000; }

        /* Overlay */
        #overlay {
            display: none;
            position: fixed;
            top:0; left:0; width:100%; height:100%;
            background: rgba(0,0,0,0.5);
            z-index: 101;
        }

    </style>
</head>
<body>

    <canvas id="snow-canvas"></canvas>

    <!-- Welcome Screen -->
    <div id="welcome-screen">
        <div class="welcome-box">
            <h1>Welcome to Fea Class</h1>
            <p>Remove Backgrounds Instantly with Style</p>
            <button class="btn-get-started" onclick="startApp()">Get Started</button>
        </div>
    </div>

    <!-- Main App Content -->
    <div id="main-app">
        <div class="header">
            <h1 style="font-weight: 600;">HabibiSir</h1>
            <div class="profile-icon" onclick="toggleProfile()">
                <i class="fas fa-user-circle"></i>
            </div>
        </div>

        <div class="user-greeting" id="display-name">Hey Guest</div>

        <div class="upload-container" onclick="document.getElementById('fileInput').click()">
            <i class="fas fa-cloud-upload-alt" style="font-size: 3rem; margin-bottom: 10px;"></i>
            <p>Click to Upload Photo</p>
            <input type="file" id="fileInput" hidden accept="image/*">
        </div>

        <div class="loader" id="loader"></div>
        
        <img id="preview-img" alt="Result">

        <div class="action-buttons" id="actions">
            <button class="btn-action btn-download" onclick="downloadImg()">
                <i class="fas fa-download"></i> Download
            </button>
            <button class="btn-action btn-share" onclick="shareImg()">
                <i class="fas fa-share-alt"></i> Share
            </button>
        </div>

        <div class="dev-footer">
            <button class="btn-dev" onclick="showDev()">Developer</button>
        </div>
    </div>

    <!-- Profile Sidebar -->
    <div id="profile-modal">
        <h3>Profile Settings</h3>
        <input type="text" id="nameInput" placeholder="Enter Your Name" style="width: 100%; padding: 10px; margin: 15px 0;">
        <button onclick="saveName()" style="width:100%; padding: 10px; background: #3a7bd5; color: white; border: none; border-radius: 5px; cursor: pointer;">Save Name</button>
        <hr style="margin: 20px 0;">
        <h4>History</h4>
        <div class="history-list" id="history-list">
            <!-- Saved images show here -->
        </div>
        <button onclick="toggleProfile()" style="margin-top: 20px; color: red; border: none; background: none; cursor: pointer;">Close</button>
    </div>

    <!-- Developer Social Modal -->
    <div id="overlay" onclick="closeDev()"></div>
    <div id="dev-modal">
        <h3>Connect with Me</h3>
        <a href="https://wa.me/919568417585" class="social-btn whatsapp"><i class="fab fa-whatsapp"></i> WhatsApp</a>
        <a href="https://www.instagram.com/habibishariksaqibi?igsh=MW9vbDlsczAxMXE1eg==" target="_blank" class="social-btn instagram"><i class="fab fa-instagram"></i> Instagram</a>
        <a href="https://youtube.com/@junaidsabbaq?si=kRKZwh4RMTO2a3aR" target="_blank" class="social-btn youtube"><i class="fab fa-youtube"></i> YouTube</a>
    </div>

    <script>
        // API Configuration
        const API_KEY = "sE9n6nqyPf1wjuS1iwBiK5KP";
        const API_URL = "https://api.remove.bg/v1.0/removebg";

        // Start App
        function startApp() {
            document.getElementById('welcome-screen').style.opacity = '0';
            setTimeout(() => {
                document.getElementById('welcome-screen').style.display = 'none';
            }, 800);
            loadData();
        }

        // Snowfall Effect
        const canvas = document.getElementById('snow-canvas');
        const ctx = canvas.getContext('2d');
        let particles = [];

        function initSnow() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            for (let i = 0; i < 100; i++) {
                particles.push({
                    x: Math.random() * canvas.width,
                    y: Math.random() * canvas.height,
                    r: Math.random() * 4 + 1,
                    d: Math.random() * 1
                });
            }
        }

        function drawSnow() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "white";
            ctx.beginPath();
            for (let p of particles) {
                ctx.moveTo(p.x, p.y);
                ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2, true);
            }
            ctx.fill();
            moveSnow();
        }

        function moveSnow() {
            for (let p of particles) {
                p.y += Math.cos(p.d) + 1 + p.r / 2;
                p.x += Math.sin(0) * 2;
                if (p.y > canvas.height) {
                    p.y = -10;
                    p.x = Math.random() * canvas.width;
                }
            }
        }

        setInterval(drawSnow, 30);
        window.addEventListener('resize', initSnow);
        initSnow();

        // Image Handling Logic
        const fileInput = document.getElementById('fileInput');
        const previewImg = document.getElementById('preview-img');
        const loader = document.getElementById('loader');
        const actionBtns = document.getElementById('actions');

        fileInput.addEventListener('change', async () => {
            const file = fileInput.files[0];
            if (!file) return;

            // Show Loader
            loader.style.display = 'block';
            previewImg.style.display = 'none';
            actionBtns.style.display = 'none';

            const formData = new FormData();
            formData.append('image_file', file);
            formData.append('size', 'auto');

            try {
                const response = await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'X-Api-Key': API_KEY },
                    body: formData
                });

                if (response.ok) {
                    const blob = await response.blob();
                    const url = URL.createObjectURL(blob);
                    
                    previewImg.src = url;
                    previewImg.style.display = 'block';
                    actionBtns.style.display = 'flex';
                    
                    // Save to History
                    saveToHistory(url);
                } else {
                    alert("Error: Background remove nahi ho paya. API Key check karein.");
                }
            } catch (err) {
                alert("Network error occurred!");
            } finally {
                loader.style.display = 'none';
            }
        });

        // Local Storage Logic
        function saveName() {
            const name = document.getElementById('nameInput').value;
            localStorage.setItem('feaClass_user', name);
            document.getElementById('display-name').innerText = "Hey " + (name || "Guest");
            alert("Name Saved!");
        }

        function loadData() {
            const savedName = localStorage.getItem('feaClass_user');
            if (savedName) {
                document.getElementById('display-name').innerText = "Hey " + savedName;
                document.getElementById('nameInput').value = savedName;
            }
            displayHistory();
        }

        function saveToHistory(imgUrl) {
            let history = JSON.parse(localStorage.getItem('feaClass_history')) || [];
            // Note: Storing many large base64 strings in localstorage might exceed limit.
            // For a real app, we usually save blobs in IndexedDB.
            // Here we just keep the last 10 session images for demo.
            history.push(imgUrl);
            if(history.length > 10) history.shift();
            localStorage.setItem('feaClass_history', JSON.stringify(history));
            displayHistory();
        }

        function displayHistory() {
            const list = document.getElementById('history-list');
            list.innerHTML = "";
            let history = JSON.parse(localStorage.getItem('feaClass_history')) || [];
            history.forEach(img => {
                const imgTag = document.createElement('img');
                imgTag.src = img;
                imgTag.className = "history-item";
                list.appendChild(imgTag);
            });
        }

        // Functions for UI Control
        function toggleProfile() {
            document.getElementById('profile-modal').classList.toggle('active');
        }

        function showDev() {
            document.getElementById('dev-modal').style.display = 'block';
            document.getElementById('overlay').style.display = 'block';
        }

        function closeDev() {
            document.getElementById('dev-modal').style.display = 'none';
            document.getElementById('overlay').style.display = 'none';
        }

        function downloadImg() {
            const link = document.createElement('a');
            link.href = previewImg.src;
            link.download = 'FeaClass_RemovedBG.png';
            link.click();
        }

        function shareImg() {
            if (navigator.share) {
                navigator.share({
                    title: 'Fea Class BG Remover',
                    text: 'Check out my photo background removed by Fea Class!',
                    url: previewImg.src
                });
            } else {
                alert("Sharing not supported on this browser.");
            }
        }

    </script>
</body>
</html>
