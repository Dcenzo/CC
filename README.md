<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hộp Quà Bí Ẩn</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        .container {
            text-align: center;
            position: relative;
        }

        .title {
            color: white;
            font-size: 3em;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
            animation: glow 2s ease-in-out infinite alternate;
        }

        @keyframes glow {
            from { text-shadow: 2px 2px 4px rgba(0,0,0,0.3), 0 0 10px rgba(255,255,255,0.2); }
            to { text-shadow: 2px 2px 4px rgba(0,0,0,0.3), 0 0 20px rgba(255,255,255,0.4); }
        }

        .gift-box {
            position: relative;
            width: 200px;
            height: 200px;
            margin: 0 auto;
            cursor: pointer;
            transition: transform 0.3s ease;
        }

        .gift-box:hover {
            transform: scale(1.1);
        }

        .box {
            width: 200px;
            height: 150px;
            background: linear-gradient(45deg, #ff6b6b, #ee5a52);
            border-radius: 10px;
            position: absolute;
            bottom: 0;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            transition: all 0.5s ease;
        }

        .box::before {
            content: '';
            position: absolute;
            top: -10px;
            left: -10px;
            right: -10px;
            bottom: -10px;
            background: linear-gradient(45deg, #ff8a80, #ff5722);
            border-radius: 15px;
            z-index: -1;
        }

        .lid {
            width: 200px;
            height: 50px;
            background: linear-gradient(45deg, #4ecdc4, #44a08d);
            border-radius: 10px;
            position: absolute;
            top: 0;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            transition: all 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55);
            transform-origin: bottom;
        }

        .ribbon-v {
            width: 20px;
            height: 200px;
            background: linear-gradient(to bottom, #ffd700, #ffb300);
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.2);
        }

        .ribbon-h {
            width: 200px;
            height: 20px;
            background: linear-gradient(to right, #ffd700, #ffb300);
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.2);
        }

        .bow {
            width: 60px;
            height: 40px;
            background: #ff1744;
            position: absolute;
            top: -20px;
            left: 50%;
            transform: translateX(-50%);
            border-radius: 50px 50px 0 0;
            box-shadow: 0 3px 15px rgba(0,0,0,0.3);
        }

        .bow::before,
        .bow::after {
            content: '';
            position: absolute;
            width: 25px;
            height: 25px;
            background: #ff1744;
            border-radius: 50%;
            top: 5px;
        }

        .bow::before {
            left: 5px;
        }

        .bow::after {
            right: 5px;
        }

        .gift-box.opened .lid {
            transform: rotateX(-180deg) translateY(-50px);
        }

        .gift-box.opened .bow {
            opacity: 0;
            transform: translateX(-50%) scale(0);
        }

        .surprise {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) scale(0);
            opacity: 0;
            transition: all 0.5s ease;
            z-index: 10;
        }

        .gift-box.opened .surprise {
            transform: translate(-50%, -50%) scale(1);
            opacity: 1;
            animation: bounce 0.6s ease;
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% {
                transform: translate(-50%, -50%) scale(1) translateY(0);
            }
            40% {
                transform: translate(-50%, -50%) scale(1) translateY(-30px);
            }
            60% {
                transform: translate(-50%, -50%) scale(1) translateY(-15px);
            }
        }

        .heart {
            font-size: 4em;
            color: #ff1744;
            filter: drop-shadow(0 0 10px rgba(255, 23, 68, 0.5));
        }

        .message {
            color: white;
            font-size: 1.5em;
            margin-top: 20px;
            opacity: 0;
            transition: opacity 0.5s ease;
        }

        .gift-box.opened ~ .message {
            opacity: 1;
        }

        .particles {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 5;
        }

        .particle {
            position: absolute;
            width: 10px;
            height: 10px;
            background: radial-gradient(circle, #ffd700, #ffb300);
            border-radius: 50%;
            opacity: 0;
            animation: explode 1s ease-out forwards;
        }

        @keyframes explode {
            0% {
                opacity: 1;
                transform: scale(0) translate(0, 0);
            }
            100% {
                opacity: 0;
                transform: scale(1) translate(var(--x), var(--y));
            }
        }

        .sparkle {
            position: absolute;
            width: 4px;
            height: 4px;
            background: white;
            border-radius: 50%;
            opacity: 0;
            animation: sparkle 2s ease-in-out infinite;
        }

        @keyframes sparkle {
            0%, 100% { opacity: 0; transform: scale(0); }
            50% { opacity: 1; transform: scale(1); }
        }

        .instruction {
            color: rgba(255, 255, 255, 0.8);
            font-size: 1.2em;
            margin-top: 30px;
            animation: pulse 2s ease-in-out infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 0.6; }
            50% { opacity: 1; }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="title">🎁 Hộp Quà Bí Ẩn 🎁</h1>
        
        <div class="gift-box" id="giftBox">
            <div class="box">
                <div class="ribbon-v"></div>
                <div class="ribbon-h"></div>
            </div>
            <div class="lid"></div>
            <div class="bow"></div>
            <div class="surprise">
                <div class="heart">💝</div>
            </div>
            <div class="particles" id="particles"></div>
        </div>

        <div class="message" id="message">
            Chúc mừng! Bạn đã mở được hộp quà! 🎉
        </div>

        <div class="instruction">
            👆 Nhấn vào hộp quà để mở nó!
        </div>

        <!-- Sparkles -->
        <div class="sparkle" style="top: 20%; left: 10%; animation-delay: 0s;"></div>
        <div class="sparkle" style="top: 30%; left: 85%; animation-delay: 0.5s;"></div>
        <div class="sparkle" style="top: 70%; left: 15%; animation-delay: 1s;"></div>
        <div class="sparkle" style="top: 80%; left: 80%; animation-delay: 1.5s;"></div>
        <div class="sparkle" style="top: 15%; left: 60%; animation-delay: 2s;"></div>
        <div class="sparkle" style="top: 90%; left: 40%; animation-delay: 2.5s;"></div>
    </div>

    <script>
        const giftBox = document.getElementById('giftBox');
        const particles = document.getElementById('particles');
        let isOpened = false;

        giftBox.addEventListener('click', function() {
            if (!isOpened) {
                this.classList.add('opened');
                isOpened = true;
                createParticles();
                
                // Âm thanh mở hộp quà (simulation)
                setTimeout(() => {
                    document.querySelector('.instruction').style.display = 'none';
                }, 500);
            } else {
                // Reset hộp quà
                this.classList.remove('opened');
                isOpened = false;
                particles.innerHTML = '';
                document.querySelector('.instruction').style.display = 'block';
            }
        });

        function createParticles() {
            for (let i = 0; i < 20; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                
                const angle = (Math.PI * 2 * i) / 20;
                const distance = 100 + Math.random() * 50;
                const x = Math.cos(angle) * distance;
                const y = Math.sin(angle) * distance;
                
                particle.style.setProperty('--x', x + 'px');
                particle.style.setProperty('--y', y + 'px');
                particle.style.left = '50%';
                particle.style.top = '50%';
                particle.style.animationDelay = Math.random() * 0.3 + 's';
                
                particles.appendChild(particle);
                
                // Xóa particle sau khi animation kết thúc
                setTimeout(() => {
                    if (particle.parentNode) {
                        particle.parentNode.removeChild(particle);
                    }
                }, 1000);
            }
        }

        // Tạo sparkles ngẫu nhiên
        function createRandomSparkles() {
            for (let i = 0; i < 8; i++) {
                const sparkle = document.createElement('div');
                sparkle.className = 'sparkle';
                sparkle.style.top = Math.random() * 100 + '%';
                sparkle.style.left = Math.random() * 100 + '%';
                sparkle.style.animationDelay = Math.random() * 2 + 's';
                document.body.appendChild(sparkle);
            }
        }

        // Tạo sparkles khi trang load
        window.addEventListener('load', createRandomSparkles);
    </script>
</body>
</html>
