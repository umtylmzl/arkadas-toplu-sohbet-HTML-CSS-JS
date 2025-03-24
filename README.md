<!DOCTYPE html>
<html lang="tr">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ekip Sohbet Uygulaması</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.0/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --light-bg: #f8f9fa;
            --light-sidebar: #ffffff;
            --light-text: #212529;
            --dark-bg: #212529;
            --dark-sidebar: #343a40;
            --dark-text: #f8f9fa;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            transition: background-color 0.3s, color 0.3s;
            background-color: var(--light-bg);
            color: var(--light-text);
            height: 100vh;
            overflow: auto;
            /* Kaydırma çubuğunu açar */
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        }

        body.dark-mode {
            background-color: var(--dark-bg);
            color: var(--dark-text);
        }

        .chat-wrapper {
            height: 100vh;
            display: flex;
            width: 100%;
        }

        .sidebar {
            width: 320px;
            min-width: 320px;
            background: var(--light-sidebar);
            border-right: 1px solid rgba(0, 0, 0, 0.1);
            transition: all 0.3s ease;
            height: 100vh;
            overflow-y: auto;
        }

        .dark-mode .sidebar {
            background: var(--dark-sidebar);
            border-right-color: rgba(255, 255, 255, 0.1);
        }

        .chat-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            min-width: 0;
            /* Important for text truncation */
        }

        .header {
            padding: 1rem;
            border-bottom: 1px solid rgba(0, 0, 0, 0.1);
            background: var(--light-sidebar);
            display: flex;
            justify-content: space-between;
            align-items: center;
            min-height: 60px;
        }

        .dark-mode .header {
            background: var(--dark-sidebar);
            border-bottom-color: rgba(255, 255, 255, 0.1);
        }

        .header-controls {
            display: flex;
            align-items: center;
            gap: 1rem;
        }

        .control-button {
            background: transparent;
            border: none;
            color: inherit;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: background-color 0.2s;
        }

        .control-button:hover {
            background-color: rgba(0, 0, 0, 0.05);
        }

        .dark-mode .control-button:hover {
            background-color: rgba(255, 255, 255, 0.05);
        }

        .menu-toggle {
            display: none;
        }

        .messages-container {
            flex: 1;
            overflow-y: auto;
            padding: 1rem;
            scroll-behavior: smooth;
        }

        .message-group {
            margin-bottom: 1.5rem;
            display: flex;
            gap: 1rem;
            max-width: 100%;
        }

        .avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            overflow: hidden;
            flex-shrink: 0;
        }

        .avatar img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .message-content {
            flex: 1;
            min-width: 0;
            /* Important for text truncation */
        }

        .message-bubble {
            padding: 0.75rem 1rem;
            border-radius: 1rem;
            max-width: 100%;
            word-wrap: break-word;
            margin-top: 0.25rem;
        }

        .message-input-container {
            padding: 1rem;
            background: var(--light-sidebar);
            border-top: 1px solid rgba(0, 0, 0, 0.1);
        }

        .dark-mode .message-input-container {
            background: var(--dark-sidebar);
            border-top-color: rgba(255, 255, 255, 0.1);
        }

        .input-wrapper {
            display: flex;
            gap: 1rem;
            align-items: center;
            position: fixed;
            bottom: 10px;
            /* Ekranın altına sabitler */
            left: 50%;
            transform: translateX(-50%);
            width: 90%;
            max-width: 500px;
            /* İdeal genişlik */
            background: white;
            /* Arka plan rengi */
            padding: 10px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            background: var(--light-sidebar);
            /* Light mode için */
        }

        .dark-mode .input-wrapper {
            background: var(--dark-sidebar);
            /* Dark mode için */
        }

        .message-input {
            flex: 1;
            border-radius: 20px;
            padding: 0.75rem 1.25rem;
            border: 1px solid rgba(0, 0, 0, 0.1);
            background: white;
            transition: all 0.3s ease;
        }

        .dark-mode .message-input {
            background: var(--dark-bg);
            border-color: rgba(255, 255, 255, 0.1);
            color: var(--dark-text);
        }

        .message-input:focus {
            outline: none;
            border-color: #0d6efd;
            box-shadow: 0 0 0 2px rgba(13, 110, 253, 0.25);
        }

        .send-button {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            padding: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            border: none;
            background: #0d6efd;
            color: white;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .send-button:hover {
            background: #0b5ed7;
        }

        /* User list styling */
        .user-item {
            padding: 0.75rem 1rem;
            display: flex;
            align-items: center;
            gap: 1rem;
            cursor: pointer;
            transition: background-color 0.2s;
            border-bottom: 1px solid rgba(0, 0, 0, 0.05);
        }

        .user-item:hover {
            background-color: rgba(0, 0, 0, 0.05);
        }

        .dark-mode .user-item:hover {
            background-color: rgba(255, 255, 255, 0.05);
        }

        .user-info {
            flex: 1;
            min-width: 0;
        }

        .user-name {
            font-weight: 600;
            margin-bottom: 0.25rem;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .user-status {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            font-size: 0.875rem;
            color: #6c757d;
        }

        .online-indicator {
            width: 8px;
            height: 8px;
            background-color: #28a745;
            border-radius: 50%;
        }

        /* Message colors */
        .umut-message {
            background-color: #E3F2FD;
        }

        .batuhan-message {
            background-color: #FFF3E0;
        }

        .semih-message {
            background-color: #E8F5E9;
        }

        .ali-message {
            background-color: #F3E5F5;
        }

        .dark-mode .umut-message {
            background-color: #1565C0;
            color: white;
        }

        .dark-mode .batuhan-message {
            background-color: #E65100;
            color: white;
        }

        .dark-mode .semih-message {
            background-color: #2E7D32;
            color: white;
        }

        .dark-mode .ali-message {
            background-color: #6A1B9A;
            color: white;
        }

        /* Responsive design */
        @media (max-width: 1024px) {
            .sidebar {
                width: 280px;
                min-width: 280px;
            }
        }

        @media (max-width: 768px) {
            .menu-toggle {
                display: flex;
                align-items: center;
                justify-content: center;
                width: 40px;
                height: 40px;
                background: transparent;
                border: none;
                color: inherit;
                font-size: 1.25rem;
                cursor: pointer;
                border-radius: 50%;
            }

            .menu-toggle:hover {
                background-color: rgba(0, 0, 0, 0.05);
            }

            .dark-mode .menu-toggle:hover {
                background-color: rgba(255, 255, 255, 0.05);
            }

            .sidebar {
                position: fixed;
                left: 0;
                top: 0;
                z-index: 1000;
                transform: translateX(-100%);
                width: 100%;
                max-width: 320px;
            }

            .sidebar.active {
                transform: translateX(0);
            }

            .overlay {
                display: none;
                position: fixed;
                top: 0;
                left: 0;
                right: 0;
                bottom: 0;
                background: rgba(0, 0, 0, 0.5);
                z-index: 999;
                backdrop-filter: blur(2px);
            }

            .overlay.active {
                display: block;
            }
        }

        @media (max-width: 480px) {
            .header {
                padding: 0.75rem;
            }

            .message-input-container {
                padding: 0.75rem;
            }

            .messages-container {
                padding: 0.75rem;
            }

            .message-bubble {
                padding: 0.625rem 0.875rem;
            }

            .user-item {
                padding: 0.625rem 0.875rem;
            }
        }

        @media (max-width: 768px) {
            .input-wrapper {
                width: 100%;
                max-width: 100%;
                padding: 0.5rem;
            }
        }

        /* Fix for iOS devices */
        @supports (-webkit-touch-callout: none) {
            .chat-wrapper {
                height: -webkit-fill-available;
            }
        }

        .text-muted {
            color: #6c757d !important;
            /* Light mode için */
        }

        .dark-mode .text-muted {
            color: var(--dark-text) !important;
            /* Dark mode için beyaz yapar */
        }

        /* theme-toggle ve fas fa-sign-out-alt için ortak stil */
        .theme-toggle,
        .fas.fa-sign-out-alt {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: transparent;
            border: none;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: background-color 0.2s ease;
            padding: 0;
            text-decoration: none;
            /* Alt çizgi yok */
            color: inherit;
            /* Yazı rengi miras alır */
            outline: none;
            /* Focus durumundaki çizgiyi engeller */
            vertical-align: middle;
            /* Dikey hizalamayı ortalar */
        }

        /* İçeriğe eklenen simgenin boyutunu ayarlıyoruz */
        .theme-toggle i,
        .fas.fa-sign-out-alt {
            font-size: 20px;
            /* Simge boyutunu ayarlıyoruz */
        }

        /* Hover durumunda renk değişimi */
        .theme-toggle:hover,
        .fas.fa-sign-out-alt:hover {
            background-color: rgba(0, 0, 0, 0.05);
            /* Light mode için */
        }

        /* Dark mode için hover rengi */
        .dark-mode .theme-toggle:hover,
        .dark-mode .fas.fa-sign-out-alt:hover {
            background-color: rgba(255, 255, 255, 0.05);
            /* Dark mode için */
        }

        /* btn-link, border-0, bg-transparent ekleyen stil */
        .theme-toggle.btn-link,
        .fas.fa-sign-out-alt.btn-link {
            padding: 0;
            /* Paddingi sıfırlıyoruz */
            border: none;
            /* Border'ı kaldırıyoruz */
            background-color: transparent;
            /* Arka planı şeffaf yapıyoruz */
            color: inherit;
            /* Rengi inherit ediyoruz */
        }
    </style>
</head>
<body>
    <div class="header">
        <button class="menu-toggle control-button" id="menu-toggle">
            <i class="fas fa-bars"></i>
        </button>
        <h5 class="mb-0">Ekip Sohbet V1</h5>
        <div class="header-controls">
        </div>
    </div>
    <div class="chat-wrapper">
        <!-- Overlay for mobile -->
        <div class="overlay" id="overlay"></div>

        <!-- Sidebar -->
        <div class="sidebar" id="sidebar">
            <div class="header">
                <h5 class="mb-0">Kullanıcılar</h5>
                <div class="d-flex gap-3">
                    <button class="theme-toggle" id="theme-toggle">
                        <i class="fas fa-moon"></i>
                    </button>
                    <button class="btn-link border-0 bg-transparent">
                        <i class="fas fa-sign-out-alt"></i>
                    </button>
                </div>
            </div>

            <div class="users-list">
                <div class="user-item">
                    <div class="avatar">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Umut" alt="Umut">
                    </div>
                    <div>
                        <div class="d-flex align-items-center">
                            <div class="online-indicator"></div>
                            <span class="fw-bold">Umut Yılmaz</span>
                        </div>
                        <small class="text-muted">Mobile Developer</small>
                    </div>
                </div>

                <div class="user-item">
                    <div class="avatar">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Batuhan" alt="Batuhan">
                    </div>
                    <div>
                        <div class="d-flex align-items-center">
                            <div class="online-indicator"></div>
                            <span class="fw-bold">Batuhan</span>
                        </div>
                        <small class="text-muted">C# Developer</small>
                    </div>
                </div>

                <div class="user-item">
                    <div class="avatar">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Semih" alt="Semih">
                    </div>
                    <div>
                        <div class="d-flex align-items-center">
                            <div class="online-indicator"></div>
                            <span class="fw-bold">Semih</span>
                        </div>
                        <small class="text-muted">Backend Developer</small>
                    </div>
                </div>

                <div class="user-item">
                    <div class="avatar">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Ahmet" alt="Ahmet">
                    </div>
                    <div>
                        <div class="d-flex align-items-center">
                            <div class="online-indicator"></div>
                            <span class="fw-bold">Ali</span>
                        </div>
                        <small class="text-muted">Backend Developer</small>
                    </div>
                </div>
            </div>
        </div>

        <!-- Main Chat Content -->
        <div class="chat-content">
            <div class="header">
                <h5 class="mb-0">Sohbet</h5>
                <div></div>
            </div>

            <div class="messages-container">
                <div class="d-flex mb-4">
                    <div class="avatar me-2">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Umut" alt="Umut">
                    </div>
                    <div>
                        <div class="small text-muted mb-1">Umut Yılmaz</div>
                        <div class="message-bubble umut-message">Merhaba ekip, yeni mobil uygulama güncellemesi hazır!
                        </div>
                    </div>
                </div>

                <div class="d-flex mb-4">
                    <div class="avatar me-2">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Batuhan" alt="Batuhan">
                    </div>
                    <div>
                        <div class="small text-muted mb-1">Batuhan</div>
                        <div class="message-bubble batuhan-message">Harika! Backend entegrasyonunu hemen kontrol
                            edeceğim.</div>
                    </div>
                </div>

                <div class="d-flex mb-4">
                    <div class="avatar me-2">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Semih" alt="Semih">
                    </div>
                    <div>
                        <div class="small text-muted mb-1">Semih</div>
                        <div class="message-bubble semih-message">API endpoint'leri hazır, test edebilirsiniz.</div>
                    </div>
                </div>

                <div class="d-flex mb-4">
                    <div class="avatar me-2">
                        <img src="https://api.dicebear.com/6.x/avataaars/svg?seed=Ahmet" alt="Ahmet">
                    </div>
                    <div>
                        <div class="small text-muted mb-1">Ali</div>
                        <div class="message-bubble ali-message">Performans optimizasyonları tamamlandı.</div>
                    </div>
                </div>
            </div>
            <div class="message-input-container">
                <div class="input-wrapper">
                    <input type="text" class="message-input" placeholder="Mesajınızı yazın...">
                    <button class="send-button">
                        <i class="fas fa-paper-plane"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.0/js/bootstrap.bundle.min.js"></script>
    <script>
        const themeToggle = document.getElementById('theme-toggle');
        const menuToggle = document.getElementById('menu-toggle');
        const sidebar = document.getElementById('sidebar');
        const overlay = document.getElementById('overlay');

        // Theme toggle
        themeToggle.addEventListener('click', () => {
            document.body.classList.toggle('dark-mode');
            themeToggle.innerHTML = document.body.classList.contains('dark-mode')
                ? '<i class="fas fa-sun"></i>'
                : '<i class="fas fa-moon"></i>';
        });

        // Mobile menu toggle
        function toggleSidebar() {
            sidebar.classList.toggle('active');
            overlay.classList.toggle('active');
        }

        menuToggle.addEventListener('click', toggleSidebar);
        overlay.addEventListener('click', toggleSidebar);

        // Close sidebar on window resize if screen becomes large
        window.addEventListener('resize', () => {
            if (window.innerWidth > 768) {
                sidebar.classList.remove('active');
                overlay.classList.remove('active');
            }
        });
    </script>
</body>
</html>



🖤 Dark Mode Grup Sohbet Uygulaması
Bu proje, HTML, CSS ve Bootstrap kullanılarak oluşturulmuş, dark mode destekli, responsive bir grup sohbet arayüzüdür.

✨ Özellikler
Dark Mode tasarım
Responsive (mobil ve masaüstü uyumlu)
Özel grup sohbetleri için optimize edilmiş
Emoji kütüphanesi ile kişisel ifadeler ekleyebilme
Bootstrap 5 ile modern ve şık tasarım
![arkadas-chat](https://github.com/user-attachments/assets/3a218809-41b4-46bc-8ccd-3a0ce6a73ffa)


![arkadas-chat2](https://github.com/user-attachments/assets/11b58b32-ffe0-4af9-a980-162d8f1a0b38)
