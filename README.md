<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <meta name="description" content="MEDORA Healthcare System - Secure portal for medical professionals and patients">
  <title>MEDORA Healthcare System</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.4/dist/chart.umd.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/lazysizes@5.3.2/lazysizes.min.js" async></script>
  <style>
    :root {
      --primary-color: #2c3e50;
      --secondary-color: #3498db;
      --accent-color: #e74c3c;
      --success-color: #2ecc71;
      --background: #f5f7fa;
      --text: #2c3e50;
    }

    .dark {
      --background: #1a202c;
      --text: #e2e8f0;
      --primary-color: #4a5568;
      --secondary-color: #63b3ed;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      line-height: 1.6;
      margin: 0;
      background-color: var(--background);
      color: var(--text);
    }

    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.5);
      align-items: center;
      justify-content: center;
      z-index: 1000;
    }

    .modal-content {
      background: white;
      padding: 2rem;
      border-radius: 8px;
      max-width: 400px;
      width: 90%;
      position: relative;
    }

    .error-message {
      color: var(--accent-color);
      font-size: 0.875rem;
      margin-top: 0.25rem;
    }

    .role-card {
      transition: transform 0.3s ease, box-shadow 0.3s ease;
    }

    .role-card:hover {
      transform: translateY(-5px);
      box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
    }

    .dashboard-table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 1rem;
    }

    .dashboard-table th,
    .dashboard-table td {
      padding: 0.75rem;
      text-align: left;
      border-bottom: 1px solid #e5e7eb;
    }

    .spinner {
      border: 4px solid #f3f3f3;
      border-top: 4px solid var(--secondary-color);
      border-radius: 50%;
      width: 24px;
      height: 24px;
      animation: spin 1s linear infinite;
      margin: 0 auto;
    }

    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }

    .fade-in {
      animation: fadeIn 0.5s ease-in;
    }

    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }

    .sidebar {
      width: 250px;
      background: var(--primary-color);
      color: white;
      height: 100vh;
      position: fixed;
      top: 0;
      left: 0;
      transition: width 0.3s ease;
      z-index: 999;
    }

    .sidebar.collapsed {
      width: 60px;
    }

    .sidebar.collapsed .sidebar-text {
      display: none;
    }

    .notification-badge {
      position: absolute;
      top: -10px;
      right: -10px;
      background: var(--accent-color);
      color: white;
      border-radius: 50%;
      padding: 2px 6px;
      font-size: 0.75rem;
    }

    .password-strength {
      height: 4px;
      background: #e5e7eb;
      margin-top: 0.5rem;
      transition: width 0.3s, background-color 0.3s;
    }

    .password-strength.weak {
      width: 33%;
      background: var(--accent-color);
    }

    .password-strength.medium {
      width: 66%;
      background: #f39c12;
    }

    .password-strength.strong {
      width: 100%;
      background: var(--success-color);
    }

    button:focus, a:focus, input:focus, select:focus, textarea:focus {
      outline: 2px solid var(--secondary-color);
      outline-offset: 2px;
    }

    .sr-only {
      position: absolute;
      width: 1px;
      height: 1px;
      padding: 0;
      margin: -1px;
      overflow: hidden;
      clip: rect(0, 0, 0, 0);
      border: 0;
    }

    img.lazyload {
      min-height: 100px;
      background: #e5e7eb;
    }
  </style>
</head>
<body>
  <header class="bg-gray-800 text-white">
    <nav class="container mx-auto px-4 py-4 flex justify-between items-center">
      <div class="flex items-center space-x-3">
        <img src="https://via.placeholder.com/40" class="lazyload" alt="MEDORA Logo" loading="lazy">
        <h1 class="text-xl font-bold">MEDORA Healthcare</h1>
      </div>
      <div class="hidden md:flex items-center space-x-4" id="nav-links">
        <a href="#home" class="hover:text-blue-400 nav-link" data-page="home">Home</a>
        <a href="#about" class="hover:text-blue-400 nav-link" data-page="about">About</a>
        <a href="#contact" class="hover:text-blue-400 nav-link" data-page="contact">Contact</a>
        <a href="#sign-in" class="hover:text-blue-400 nav-link" data-page="sign-in" id="sign-in">Sign In</a>
        <a href="#sign-up" class="hover:text-blue-400 nav-link" data-page="sign-up" id="sign-up">Sign Up</a>
        <a href="#logout" class="hover:text-blue-400 nav-link hidden" data-page="logout" id="logout">Logout</a>
        <div class="relative" id="notification-bell">
          <i class="fas fa-bell text-xl cursor-pointer"></i>
          <span id="notification-count" class="notification-badge hidden">0</span>
        </div>
        <button id="theme-toggle" class="p-2" aria-label="Toggle dark mode">
          <i class="fas fa-moon"></i>
        </button>
        <select id="languageSelect" class="bg-gray-700 text-white p-1 rounded">
          <option value="en">English</option>
          <option value="hi">Hindi</option>
          <option value="kn">Kannada</option>
        </select>
      </div>
      <button class="md:hidden focus:outline-none" id="mobile-menu-btn">
        <i class="fas fa-bars text-xl"></i>
      </button>
    </nav>
    <div class="md:hidden bg-gray-700" id="mobile-menu" style="display: none;">
      <a href="#home" class="block px-4 py-2 hover:bg-gray-600 nav-link" data-page="home">Home</a>
      <a href="#about" class="block px-4 py-2 hover:bg-gray-600 nav-link" data-page="about">About</a>
      <a href="#contact" class="block px-4 py-2 hover:bg-gray-600 nav-link" data-page="contact">Contact</a>
      <a href="#sign-in" class="block px-4 py-2 hover:bg-gray-600 nav-link" data-page="sign-in" id="mobile-sign-in">Sign In</a>
      <a href="#sign-up" class="block px-4 py-2 hover:bg-gray-600 nav-link" data-page="sign-up" id="mobile-sign-up">Sign Up</a>
      <a href="#logout" class="block px-4 py-2 hover:bg-gray-600 nav-link hidden" data-page="logout" id="mobile-logout">Logout</a>
    </div>
    <div class="text-center py-6">
      <p class="text-sm opacity-90">Secure Portal for Healthcare Professionals and Patients</p>
    </div>
  </header>

  <nav id="sidebar" class="sidebar hidden">
    <button id="sidebar-toggle" class="p-4 focus:outline-none">
      <i class="fas fa-bars text-white"></i>
    </button>
    <ul class="mt-4">
      <li><a href="#" class="block p-4 hover:bg-gray-600" data-page="dashboard"><i class="fas fa-tachometer-alt mr-2"></i><span class="sidebar-text">Dashboard</span></a></li>
      <li><a href="#" class="block p-4 hover:bg-gray-600" data-page="profile"><i class="fas fa-user mr-2"></i><span class="sidebar-text">Profile</span></a></li>
      <li><a href="#logout" class="block p-4 hover:bg-gray-600 nav-link" data-page="logout"><i class="fas fa-sign-out-alt mr-2"></i><span class="sidebar-text">Logout</span></a></li>
    </ul>
  </nav>

  <main class="container mx-auto px-4 py-8" id="main-content" style="margin-left: 0;">
    <!-- Content will be dynamically loaded here -->
  </main>

  <footer class="bg-gray-800 text-white py-6 mt-8">
    <div class="container mx-auto px-4 text-center">
      <p>© 2025 MEDORA Healthcare System. All rights reserved.</p>
      <div class="flex justify-center space-x-4 mt-4">
        <a href="#privacy" class="hover:text-blue-400">Privacy Policy</a>
        <a href="#terms" class="hover:text-blue-400">Terms of Service</a>
        <a href="#contact" class="hover:text-blue-400 nav-link" data-page="contact">Contact Support</a>
      </div>
    </div>
  </footer>

  <div id="signInModal" class="modal">
    <div class="modal-content">
      <h2 class="text-xl font-bold mb-4">Sign In</h2>
      <form id="signInForm">
        <div class="mb-4">
          <label for="signInUsername" class="block text-sm font-medium">Username</label>
          <input type="text" id="signInUsername" class="w-full p-2 border rounded" required aria-describedby="signInUsername-error">
          <p id="signInUsername-error" class="error-message hidden"></p>
        </div>
        <div class="mb-4">
          <label for="signInPassword" class="block text-sm font-medium">Password</label>
          <input type="password" id="signInPassword" class="w-full p-2 border rounded" required aria-describedby="signInPassword-error">
          <p id="signInPassword-error" class="error-message hidden"></p>
        </div>
        <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Sign In</button>
      </form>
      <button onclick="closeSignInModal()" class="absolute top-2 right-2 text-gray-500 hover:text-gray-700" aria-label="Close modal">
        <i class="fas fa-times"></i>
      </button>
    </div>
  </div>

  <div id="notificationModal" class="modal">
    <div class="modal-content">
      <h2 class="text-xl font-bold mb-4">Notifications</h2>
      <ul id="notificationList" class="space-y-2"></ul>
      <button onclick="document.getElementById('notificationModal').style.display='none'" class="absolute top-2 right-2 text-gray-500 hover:text-gray-700" aria-label="Close notifications">
        <i class="fas fa-times"></i>
      </button>
    </div>
  </div>

  <div aria-live="polite" id="live-region" class="sr-only"></div>

  <script>
    // State management
    const state = {
      currentRole: null,
      isAuthenticated: false,
      currentUser: null,
      currentPage: 'home',
      language: localStorage.getItem('language') || 'en'
    };

    // Translations
    const translations = {
      en: {
        welcome: 'Welcome to MEDORA',
        about: 'About',
        contact: 'Contact Us',
        signIn: 'Sign In',
        signUp: 'Sign Up',
        logout: 'Logout',
        dashboard: 'Dashboard',
        profile: 'Profile',
        appointmentScheduled: 'Appointment scheduled successfully!'
      },
      hi: {
        welcome: 'मेडोरा में आपका स्वागत है',
        about: 'मेडोरा हेल्थकेयर सिस्टम के बारे में',
        contact: 'हमसे संपर्क करें',
        signIn: 'साइन इन करें',
        signUp: 'साइन अप करें',
        logout: 'लॉगआउट करें',
        dashboard: 'डैशबोर्ड',
        profile: 'प्रोफाइल',
        appointmentScheduled: 'नियुक्ति सफलतापूर्वक निर्धारित की गई!'
      },
      kn: {
        welcome: 'ಮೆಡೋರಾಗೆ ಸ್ವಾಗತ',
        about: 'ಮೆಡೋರಾ ಆರೋಗ್ಯ ವ್ಯವಸ್ಥೆಯ ಬಗ್ಗೆ',
        contact: 'ನಮ್ಮನ್ನು ಸಂಪರ್ಕಿಸಿ',
        signIn: 'ಸೈನ್ ಇನ್ ಮಾಡಿ',
        signUp: 'ಸೈನ್ ಅಪ್ ಮಾಡಿ',
        logout: 'ಲಾಗೌಟ್ ಮಾಡಿ',
        dashboard: 'ಡ್ಯಾಶ್‌ಬೋರ್ಡ್',
        profile: 'ಪ್ರೊಫೈಲ್',
        appointmentScheduled: 'ನೇಮಕಾತಿ ಯಶಸ್ವಿಯಾಗಿ ನಿಗದಿಯಾಗಿದೆ!'
      }
    };

    // Sample data for dashboards
    const sampleData = {
      admin: [
        { id: 1, name: 'User Management', status: 'Active', users: 150 },
        { id: 2, name: 'System Health', status: 'Stable', uptime: '99.9%' }
      ],
      doctor: [
        { id: 1, name: 'John Smith', condition: 'Stable', nextVisit: '2025-06-20' },
        { id: 2, name: 'Jane Doe', condition: 'Monitoring', nextVisit: '2025-06-22' }
      ],
      nurse: [
        { id: 1, patient: 'John Smith', task: 'Vitals Check', time: '10:00 AM' },
        { id: 2, patient: 'Jane Doe', task: 'Medication', time: '12:00 PM' }
      ],
      patient: [
        { id: 1, date: '2025-06-20', type: 'Check-up', doctor: 'Dr. Brown' },
        { id: 2, date: '2025-07-01', type: 'Follow-up', doctor: 'Dr. Smith' }
      ]
    };

    // Page content
    const pages = {
      home: `
        <section class="auth-section text-center">
          <h2 class="text-2xl font-bold mb-2" data-translate="welcome">${translations[state.language].welcome}</h2>
          <p class="mb-6">Please sign in or sign up to access your dashboard</p>
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
            <div class="role-card bg-white p-6 rounded-lg shadow-md" data-role="admin">
              <div class="flex items-center space-x-2 mb-2">
                <i class="fas fa-user-shield text-blue-500 text-xl"></i>
                <h3 class="font-semibold">Admin</h3>
              </div>
              <p class="text-gray-600">System management and configuration</p>
              <button class="mt-4 bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors" onclick="showSignInModal('admin')" tabindex="0">Access</button>
            </div>
            <div class="role-card bg-white p-6 rounded-lg shadow-md" data-role="doctor">
              <div class="flex items-center space-x-2 mb-2">
                <i class="fas fa-user-md text-blue-500 text-xl"></i>
                <h3 class="font-semibold">Doctor</h3>
              </div>
              <p class="text-gray-600">Patient records and treatment plans</p>
              <button class="mt-4 bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors" onclick="showSignInModal('doctor')" tabindex="0">Access</button>
            </div>
            <div class="role-card bg-white p-6 rounded-lg shadow-md" data-role="nurse">
              <div class="flex items-center space-x-2 mb-2">
                <i class="fas fa-user-nurse text-blue-500 text-xl"></i>
                <h3 class="font-semibold">Nurse</h3>
              </div>
              <p class="text-gray-600">Patient care and monitoring</p>
              <button class="mt-4 bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors" onclick="showSignInModal('nurse')" tabindex="0">Access</button>
            </div>
            <div class="role-card bg-white p-6 rounded-lg shadow-md" data-role="patient">
              <div class="flex items-center space-x-2 mb-2">
                <i class="fas fa-procedures text-blue-500 text-xl"></i>
                <h3 class="font-semibold">Patient</h3>
              </div>
              <p class="text-gray-600">Medical records and appointments</p>
              <button class="mt-4 bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors" onclick="showSignInModal('patient')" tabindex="0">Access</button>
            </div>
          </div>
        </section>
      `,
      about: `
        <section class="about-section text-center fade-in">
          <h2 class="text-2xl font-bold mb-4" data-translate="about">${translations[state.language].about}</h2>
          <p class="text-gray-600 max-w-2xl mx-auto mb-6">
            MEDORA Healthcare System is dedicated to providing innovative healthcare solutions through our secure digital platform. Our mission is to enhance patient care and streamline operations for healthcare professionals.
          </p>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mt-8">
            <div class="bg-white p-6 rounded-lg shadow-md">
              <h3 class="text-lg font-semibold mb-2">Our Mission</h3>
              <p class="text-gray-600">
                To empower healthcare providers and patients with accessible, secure, and efficient tools for better health outcomes.
              </p>
            </div>
            <div class="bg-white p-6 rounded-lg shadow-md">
              <h3 class="text-lg font-semibold mb-2">Our Values</h3>
              <ul class="text-gray-600 list-disc list-inside">
                <li>Patient-Centric Care</li>
                <li>Security and Privacy</li>
                <li>Innovation in Healthcare</li>
                <li>Collaboration and Trust</li>
              </ul>
            </div>
          </div>
        </section>
      `,
      contact: `
        <section class="contact-section fade-in">
          <h2 class="text-2xl font-bold mb-4 text-center" data-translate="contact">${translations[state.language].contact}</h2>
          <p class="text-gray-600 max-w-2xl mx-auto mb-6 text-center">
            Have questions or need support? Reach out to our team, and we'll get back to you as soon as possible.
          </p>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div class="bg-white p-6 rounded-lg shadow-md">
              <h3 class="text-lg font-semibold mb-2">Contact Information</h3>
              <ul class="text-gray-600 space-y-2">
                <li><i class="fas fa-phone-alt mr-2"></i> +1 (800) 123-4567</li>
                <li><i class="fas fa-envelope mr-2"></i> support@medorahealth.com</li>
                <li><i class="fas fa-map-marker-alt mr-2"></i> 123 Health St, Wellness City, HC 12345</li>
              </ul>
            </div>
            <div class="bg-white p-6 rounded-lg shadow-md">
              <h3 class="text-lg font-semibold mb-2">Send a Message</h3>
              <form id="contactForm">
                <div class="mb-4">
                  <label for="name" class="block text-sm font-medium">Name</label>
                  <input type="text" id="name" class="w-full p-2 border rounded" required aria-describedby="name-error">
                  <p id="name-error" class="error-message hidden"></p>
                </div>
                <div class="mb-4">
                  <label for="email" class="block text-sm font-medium">Email</label>
                  <input type="email" id="email" class="w-full p-2 border rounded" required aria-describedby="email-error">
                  <p id="email-error" class="error-message hidden"></p>
                </div>
                <div class="mb-4">
                  <label for="message" class="block text-sm font-medium">Message</label>
                  <textarea id="message" class="w-full p-2 border rounded" rows="4" required aria-describedby="message-error"></textarea>
                  <p id="message-error" class="error-message hidden"></p>
                </div>
                <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Submit</button>
              </form>
            </div>
          </div>
        </section>
      `,
      'sign-up': `
        <section class="sign-up-section fade-in max-w-2xl mx-auto">
          <h2 class="text-2xl font-bold mb-4 text-center" data-translate="signUp">${translations[state.language].signUp}</h2>
          <p class="text-gray-600 mb-6 text-center">Create your account to access your dashboard</p>
          <div class="bg-white p-6 rounded-lg shadow-md">
            <div class="mb-4">
              <label for="signUpRole" class="block text-sm font-medium">Select Role</label>
              <select id="signUpRole" class="w-full p-2 border rounded" aria-describedby="signUpRole-error">
                <option value="">Select Role</option>
                <option value="admin">Admin</option>
                <option value="doctor">Doctor</option>
                <option value="nurse">Nurse</option>
                <option value="patient">Patient</option>
              </select>
              <p id="signUpRole-error" class="error-message hidden"></p>
            </div>
            <div id="signUpFormContainer"></div>
          </div>
        </section>
      `,
      'admin-dashboard': `
        <section class="dashboard-section fade-in">
          <h2 class="text-2xl font-bold mb-4" data-translate="dashboard">${translations[state.language].dashboard}</h2>
          <p class="text-gray-600 mb-6">Welcome to your system management portal</p>
          <input type="text" id="adminSearch" class="w-full p-2 border rounded mb-4" placeholder="Search records...">
          <div class="admin-stats">
            <canvas id="adminChart" class="mb-6"></canvas>
            <table class="dashboard-table" id="adminTable">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Status</th>
                  <th>Details</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                ${sampleData.admin.map(item => `
                  <tr>
                    <td>${item.id}</td>
                    <td>${item.name}</td>
                    <td>${item.status}</td>
                    <td>${item.users || item.uptime}</td>
                    <td><button class="text-blue-500 hover:underline">${item.name === 'User Management' ? 'Manage' : 'View Report'}</button></td>
                  </tr>
                `).join('')}
              </tbody>
            </table>
          </div>
        </section>
      `,
      'doctor-dashboard': `
        <section class="dashboard-section fade-in">
          <h2 class="text-2xl font-bold mb-4" data-translate="dashboard">${translations[state.language].dashboard}</h2>
          <p class="text-gray-600 mb-6">Manage your patient records and treatment plans</p>
          <input type="text" id="doctorSearch" class="w-full p-2 border rounded mb-4" placeholder="Search patients...">
          <div class="patient-list">
            <canvas id="doctorChart" class="mb-6"></canvas>
            <table class="dashboard-table" id="doctorTable">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Patient</th>
                  <th>Condition</th>
                  <th>Next Visit</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                ${sampleData.doctor.map(item => `
                  <tr>
                    <td>${item.id}</td>
                    <td>${item.name}</td>
                    <td>${item.condition}</td>
                    <td>${item.nextVisit}</td>
                    <td><button class="text-blue-500 hover:underline">View Record</button></td>
                  </tr>
                `).join('')}
              </tbody>
            </table>
          </div>
        </section>
      `,
      'nurse-dashboard': `
        <section class="dashboard-section fade-in">
          <h2 class="text-2xl font-bold mb-4" data-translate="dashboard">${translations[state.language].dashboard}</h2>
          <p class="text-gray-600 mb-6">Manage patient care and monitoring tasks</p>
          <input type="text" id="nurseSearch" class="w-full p-2 border rounded mb-4" placeholder="Search tasks...">
          <div class="patient-care">
            <canvas id="nurseChart" class="mb-6"></canvas>
            <table class="dashboard-table" id="nurseTable">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Patient</th>
                  <th>Task</th>
                  <th>Time</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                ${sampleData.nurse.map(item => `
                  <tr>
                    <td>${item.id}</td>
                    <td>${item.patient}</td>
                    <td>${item.task}</td>
                    <td>${item.time}</td>
                    <td><button class="text-blue-500 hover:underline">Update</button></td>
                  </tr>
                `).join('')}
              </tbody>
            </table>
          </div>
        </section>
      `,
      'patient-dashboard': `
        <section class="dashboard-section fade-in">
          <h2 class="text-2xl font-bold mb-4" data-translate="dashboard">${translations[state.language].dashboard}</h2>
          <p class="text-gray-600 mb-6">View your appointments and medical records</p>
          <div class="patient-portal">
            <canvas id="patientChart" class="mb-6"></canvas>
            <h3 class="text-lg font-semibold mb-2">Schedule Appointment</h3>
            <form id="appointmentForm" class="bg-white p-6 rounded-lg shadow-md mb-6">
              <div class="mb-4">
                <label for="appointmentDate" class="block text-sm font-medium">Date</label>
                <input type="date" id="appointmentDate" class="w-full p-2 border rounded" required aria-describedby="appointmentDate-error" min="${new Date().toISOString().split('T')[0]}">
                <p id="appointmentDate-error" class="error-message hidden"></p>
              </div>
              <div class="mb-4">
                <label for="doctorSelect" class="block text-sm font-medium">Doctor</label>
                <select id="doctorSelect" class="w-full p-2 border rounded" required aria-describedby="doctorSelect-error">
                  <option value="">Select Doctor</option>
                  <option value="Dr. Brown">Dr. Brown</option>
                  <option value="Dr. Smith">Dr. Smith</option>
                </select>
                <p id="doctorSelect-error" class="error-message hidden"></p>
              </div>
              <div class="mb-4">
                <label for="reason" class="block text-sm font-medium">Reason</label>
                <textarea id="reason" class="w-full p-2 border rounded" rows="3" required aria-describedby="reason-error"></textarea>
                <p id="reason-error" class="error-message hidden"></p>
              </div>
              <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Schedule</button>
            </form>
            <table class="dashboard-table">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Date</th>
                  <th>Type</th>
                  <th>Doctor</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                ${sampleData.patient.map(item => `
                  <tr>
                    <td>${item.id}</td>
                    <td>${item.date}</td>
                    <td>${item.type}</td>
                    <td>${item.doctor}</td>
                    <td><button class="text-blue-500 hover:underline">View Details</button></td>
                  </tr>
                `).join('')}
              </tbody>
            </table>
          </div>
        </section>
      `,
      profile: `
        <section class="profile-section fade-in max-w-2xl mx-auto">
          <h2 class="text-2xl font-bold mb-4" data-translate="profile">${translations[state.language].profile}</h2>
          <p class="text-gray-600 mb-6">Update your profile information</p>
          <div class="bg-white p-6 rounded-lg shadow-md">
            <form id="profileForm">
              <div class="mb-4">
                <label for="profileName" class="block text-sm font-medium">Full Name</label>
                <input type="text" id="profileName" class="w-full p-2 border rounded" required aria-describedby="profileName-error">
                <p id="profileName-error" class="error-message hidden"></p>
              </div>
              <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Save Changes</button>
            </form>
          </div>
        </section>
      `
    };

    // Role-specific sign-up forms
    const signUpForms = {
      admin: `
        <form id="signUpForm">
          <div class="mb-4">
            <label for="signUpUsername" class="block text-sm font-medium">Username</label>
            <input type="text" id="signUpUsername" class="w-full p-2 border rounded" required aria-describedby="signUpUsername-error">
            <p id="signUpUsername-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="signUpPassword" class="block text-sm font-medium">Password</label>
            <input type="password" id="signUpPassword" class="w-full p-2 border rounded" required aria-describedby="signUpPassword-error">
            <div id="password-strength" class="password-strength"></div>
            <p id="signUpPassword-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="adminName" class="block text-sm font-medium">Full Name</label>
            <input type="text" id="adminName" class="w-full p-2 border rounded" required aria-describedby="adminName-error">
            <p id="adminName-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="department" class="block text-sm font-medium">Department</label>
            <input type="text" id="department" class="w-full p-2 border rounded" required aria-describedby="department-error">
            <p id="department-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="employeeId" class="block text-sm font-medium">Employee ID</label>
            <input type="text" id="employeeId" class="w-full p-2 border rounded" required aria-describedby="employeeId-error">
            <p id="employeeId-error" class="error-message hidden"></p>
          </div>
          <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Sign Up</button>
        </form>
      `,
      doctor: `
        <form id="signUpForm">
          <div class="mb-4">
            <label for="signUpUsername" class="block text-sm font-medium">Username</label>
            <input type="text" id="signUpUsername" class="w-full p-2 border rounded" required aria-describedby="signUpUsername-error">
            <p id="signUpUsername-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="signUpPassword" class="block text-sm font-medium">Password</label>
            <input type="password" id="signUpPassword" class="w-full p-2 border rounded" required aria-describedby="signUpPassword-error">
            <div id="password-strength" class="password-strength"></div>
            <p id="signUpPassword-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="doctorName" class="block text-sm font-medium">Full Name</label>
            <input type="text" id="doctorName" class="w-full p-2 border rounded" required aria-describedby="doctorName-error">
            <p id="doctorName-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="specialization" class="block text-sm font-medium">Specialization</label>
            <select id="specialization" class="w-full p-2 border rounded" required aria-describedby="specialization-error">
              <option value="">Select Specialization</option>
              <option value="Cardiology">Cardiology</option>
              <option value="Neurology">Neurology</option>
              <option value="Pediatrics">Pediatrics</option>
              <option value="Orthopedics">Orthopedics</option>
              <option value="General Medicine">General Medicine</option>
            </select>
            <p id="specialization-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="licenseNumber" class="block text-sm font-medium">License Number</label>
            <input type="text" id="licenseNumber" class="w-full p-2 border rounded" required aria-describedby="licenseNumber-error">
            <p id="licenseNumber-error" class="error-message hidden"></p>
          </div>
          <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Sign Up</button>
        </form>
      `,
      nurse: `
        <form id="signUpForm">
          <div class="mb-4">
            <label for="signUpUsername" class="block text-sm font-medium">Username</label>
            <input type="text" id="signUpUsername" class="w-full p-2 border rounded" required aria-describedby="signUpUsername-error">
            <p id="signUpUsername-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="signUpPassword" class="block text-sm font-medium">Password</label>
            <input type="password" id="signUpPassword" class="w-full p-2 border rounded" required aria-describedby="signUpPassword-error">
            <div id="password-strength" class="password-strength"></div>
            <p id="signUpPassword-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="nurseName" class="block text-sm font-medium">Full Name</label>
            <input type="text" id="nurseName" class="w-full p-2 border rounded" required aria-describedby="nurseName-error">
            <p id="nurseName-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="certificationNumber" class="block text-sm font-medium">Certification Number</label>
            <input type="text" id="certificationNumber" class="w-full p-2 border rounded" required aria-describedby="certificationNumber-error">
            <p id="certificationNumber-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="shiftPreference" class="block text-sm font-medium">Shift Preference</label>
            <select id="shiftPreference" class="w-full p-2 border rounded" required aria-describedby="shiftPreference-error">
              <option value="">Select Shift</option>
              <option value="Morning">Morning</option>
              <option value="Afternoon">Afternoon</option>
              <option value="Night">Night</option>
            </select>
            <p id="shiftPreference-error" class="error-message hidden"></p>
          </div>
          <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Sign Up</button>
        </form>
      `,
      patient: `
        <form id="signUpForm">
          <div class="mb-4">
            <label for="signUpUsername" class="block text-sm font-medium">Username</label>
            <input type="text" id="signUpUsername" class="w-full p-2 border rounded" required aria-describedby="signUpUsername-error">
            <p id="signUpUsername-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="signUpPassword" class="block text-sm font-medium">Password</label>
            <input type="password" id="signUpPassword" class="w-full p-2 border rounded" required aria-describedby="signUpPassword-error">
            <div id="password-strength" class="password-strength"></div>
            <p id="signUpPassword-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="patientName" class="block text-sm font-medium">Full Name</label>
            <input type="text" id="patientName" class="w-full p-2 border rounded" required aria-describedby="patientName-error">
            <p id="patientName-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="bloodGroup" class="block text-sm font-medium">Blood Group</label>
            <select id="bloodGroup" class="w-full p-2 border rounded" required aria-describedby="bloodGroup-error">
              <option value="">Select Blood Group</option>
              <option value="A+">A+</option>
              <option value="A-">A-</option>
              <option value="B+">B+</option>
              <option value="B-">B-</option>
              <option value="AB+">AB+</option>
              <option value="AB-">AB-</option>
              <option value="O+">O+</option>
              <option value="O-">O-</option>
            </select>
            <p id="bloodGroup-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="dob" class="block text-sm font-medium">Date of Birth</label>
            <input type="date" id="dob" class="w-full p-2 border rounded" required aria-describedby="dob-error" max="${new Date().toISOString().split('T')[0]}">
            <p id="dob-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="age" class="block text-sm font-medium">Age</label>
            <input type="number" id="age" class="w-full p-2 border rounded" readonly aria-describedby="age-error">
            <p id="age-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label for="allergies" class="block text-sm font-medium">Allergies (if any)</label>
            <textarea id="allergies" class="w-full p-2 border rounded" rows="3" aria-describedby="allergies-error"></textarea>
            <p id="allergies-error" class="error-message hidden"></p>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium">Gender</label>
            <div class="flex space-x-4">
              <label><input type="radio" name="gender" value="male" required aria-describedby="gender-error"> Male</label>
              <label><input type="radio" name="gender" value="female"> Female</label>
              <label><input type="radio" name="gender" value="other"> Other</label>
            </div>
            <p id="gender-error" class="error-message hidden"></p>
          </div>
          <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 w-full">Sign Up</button>
        </form>
      `
    };

    function loadPage(page) {
      if (page.includes('dashboard') && !state.isAuthenticated) {
        state.currentPage = 'home';
        document.getElementById('main-content').innerHTML = pages.home;
        announce('Please sign in to access the dashboard.');
        return;
      }

      state.currentPage = page;
      const mainContent = document.getElementById('main-content');
      mainContent.innerHTML = pages[page] || pages.home;
      mainContent.classList.add('fade-in');

      const sidebar = document.getElementById('sidebar');
      sidebar.classList.toggle('hidden', !page.includes('dashboard') && page !== 'profile');
      mainContent.style.marginLeft = page.includes('dashboard') || page === 'profile' ? (sidebar.classList.contains('collapsed') ? '60px' : '250px') : '0';

      if (page === 'contact') {
        initializeContactForm();
      } else if (page === 'sign-up') {
        initializeSignUpPage();
      } else if (page.includes('dashboard')) {
        initializeDashboard(page.split('-')[0]);
      } else if (page === 'profile') {
        initializeProfileForm();
      }

      updateNavLinks();
      updateLanguage();
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    function updateNavLinks() {
      const isDashboard = state.currentPage.includes('dashboard') || state.currentPage === 'profile';
      document.querySelectorAll('.nav-link').forEach(link => {
        const page = link.getAttribute('data-page');
        link.classList.toggle('text-blue-400', page === state.currentPage);
        link.classList.toggle('text-white', page !== state.currentPage);
        if (page === 'logout') {
          link.classList.toggle('hidden', !state.isAuthenticated);
        } else if (page === 'sign-in' || page === 'sign-up') {
          link.classList.toggle('hidden', state.isAuthenticated);
        } else {
          link.classList.toggle('hidden', state.isAuthenticated && isDashboard && page !== 'logout');
        }
        link.textContent = translations[state.language][page] || link.textContent;
      });
      document.querySelectorAll('.sidebar a').forEach(link => {
        const page = link.getAttribute('data-page');
        link.querySelector('.sidebar-text').textContent = translations[state.language][page] || link.querySelector('.sidebar-text').textContent;
      });
    }

    function showSignInModal(role) {
      state.currentRole = role;
      document.getElementById('signInModal').style.display = 'flex';
      document.getElementById('signInUsername').focus();
      announce('Sign in modal opened.');
    }

    function closeSignInModal() {
      document.getElementById('signInModal').style.display = 'none';
      document.getElementById('signInForm').reset();
      document.getElementById('signInUsername-error').classList.add('hidden');
      document.getElementById('signInPassword-error').classList.add('hidden');
      announce('Sign in modal closed.');
    }

    function validateSignInForm(username, password) {
      let isValid = true;
      const usernameError = document.getElementById('signInUsername-error');
      const passwordError = document.getElementById('signInPassword-error');

      usernameError.classList.add('hidden');
      passwordError.classList.add('hidden');

      if (!username) {
        usernameError.textContent = 'Username is required';
        usernameError.classList.remove('hidden');
        isValid = false;
      }
      if (!password) {
        passwordError.textContent = 'Password is required';
        passwordError.classList.remove('hidden');
        isValid = false;
      }

      return isValid;
    }

    function validateSignUpForm(role, formData) {
      let isValid = true;
      const usernameError = document.getElementById('signUpUsername-error');
      const passwordError = document.getElementById('signUpPassword-error');

      usernameError.classList.add('hidden');
      passwordError.classList.add('hidden');

      if (!formData.username) {
        usernameError.textContent = 'Username is required';
        usernameError.classList.remove('hidden');
        isValid = false;
      }
      if (!formData.password || formData.password.length < 6) {
        passwordError.textContent = 'Password must be at least 6 characters';
        passwordError.classList.remove('hidden');
        isValid = false;
      }

      if (role === 'admin') {
        const adminNameError = document.getElementById('adminName-error');
        const departmentError = document.getElementById('department-error');
        const employeeIdError = document.getElementById('employeeId-error');

        adminNameError.classList.add('hidden');
        departmentError.classList.add('hidden');
        employeeIdError.classList.add('hidden');

        if (!formData.adminName) {
          adminNameError.textContent = 'Full name is required';
          adminNameError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.department) {
          departmentError.textContent = 'Department is required';
          departmentError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.employeeId) {
          employeeIdError.textContent = 'Employee ID is required';
          employeeIdError.classList.remove('hidden');
          isValid = false;
        }
      } else if (role === 'doctor') {
        const doctorNameError = document.getElementById('doctorName-error');
        const specializationError = document.getElementById('specialization-error');
        const licenseNumberError = document.getElementById('licenseNumber-error');

        doctorNameError.classList.add('hidden');
        specializationError.classList.add('hidden');
        licenseNumberError.classList.add('hidden');

        if (!formData.doctorName) {
          doctorNameError.textContent = 'Full name is required';
          doctorNameError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.specialization) {
          specializationError.textContent = 'Specialization is required';
          specializationError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.licenseNumber) {
          licenseNumberError.textContent = 'License number is required';
          licenseNumberError.classList.remove('hidden');
          isValid = false;
        }
      } else if (role === 'nurse') {
        const nurseNameError = document.getElementById('nurseName-error');
        const certificationNumberError = document.getElementById('certificationNumber-error');
        const shiftPreferenceError = document.getElementById('shiftPreference-error');

        nurseNameError.classList.add('hidden');
        certificationNumberError.classList.add('hidden');
        shiftPreferenceError.classList.add('hidden');

        if (!formData.nurseName) {
          nurseNameError.textContent = 'Full name is required';
          nurseNameError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.certificationNumber) {
          certificationNumberError.textContent = 'Certification number is required';
          certificationNumberError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.shiftPreference) {
          shiftPreferenceError.textContent = 'Shift preference is required';
          shiftPreferenceError.classList.remove('hidden');
          isValid = false;
        }
      } else if (role === 'patient') {
        const patientNameError = document.getElementById('patientName-error');
        const bloodGroupError = document.getElementById('bloodGroup-error');
        const dobError = document.getElementById('dob-error');
        const genderError = document.getElementById('gender-error');

        patientNameError.classList.add('hidden');
        bloodGroupError.classList.add('hidden');
        dobError.classList.add('hidden');
        genderError.classList.add('hidden');

        if (!formData.patientName) {
          patientNameError.textContent = 'Full name is required';
          patientNameError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.bloodGroup) {
          bloodGroupError.textContent = 'Blood group is required';
          bloodGroupError.classList.remove('hidden');
          isValid = false;
        }
        if (!formData.dob) {
          dobError.textContent = 'Date of birth is required';
          dobError.classList.remove('hidden');
          isValid = false;
        } else {
          const today = new Date();
          const dobDate = new Date(formData.dob);
          if (dobDate > today) {
            dobError.textContent = 'Date of birth cannot be in the future';
            dobError.classList.remove('hidden');
            isValid = false;
          }
        }
        if (!formData.gender) {
          genderError.textContent = 'Gender is required';
          genderError.classList.remove('hidden');
          isValid = false;
        }
      }

      return isValid;
    }

    function validateContactForm(name, email, message) {
      let isValid = true;
      const nameError = document.getElementById('name-error');
      const emailError = document.getElementById('email-error');
      const messageError = document.getElementById('message-error');

      nameError.classList.add('hidden');
      emailError.classList.add('hidden');
      messageError.classList.add('hidden');

      if (!name) {
        nameError.textContent = 'Name is required';
        nameError.classList.remove('hidden');
        isValid = false;
      }
      if (!email || !/\S+@\S+\.\S+/.test(email)) {
        emailError.textContent = 'Valid email is required';
        emailError.classList.remove('hidden');
        isValid = false;
      }
      if (!message) {
        messageError.textContent = 'Message is required';
        messageError.classList.remove('hidden');
        isValid = false;
      }

      return isValid;
    }

    function validateAppointmentForm(date, doctor, reason) {
      let isValid = true;
      const dateError = document.getElementById('appointmentDate-error');
      const doctorError = document.getElementById('doctorSelect-error');
      const reasonError = document.getElementById('reason-error');

      dateError.classList.add('hidden');
      doctorError.classList.add('hidden');
      reasonError.classList.add('hidden');

      if (!date) {
        dateError.textContent = 'Date is required';
        dateError.classList.remove('hidden');
        isValid = false;
      }
      if (!doctor) {
        doctorError.textContent = 'Doctor is required';
        doctorError.classList.remove('hidden');
        isValid = false;
      }
      if (!reason) {
        reasonError.textContent = 'Reason is required';
        reasonError.classList.remove('hidden');
        isValid = false;
      }

      return isValid;
    }

    function calculateAge(dob) {
      const today = new Date();
      const birthDate = new Date(dob);
      let age = today.getFullYear() - birthDate.getFullYear();
      const monthDiff = today.getMonth() - birthDate.getMonth();
      if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
      }
      return age;
    }

    function sanitizeInput(input) {
      const div = document.createElement('div');
      div.textContent = input;
      return div.innerHTML;
    }

    function announce(message) {
      document.getElementById('live-region').textContent = message;
    }

    function initializeSignUpPage() {
      const roleSelect = document.getElementById('signUpRole');
      const formContainer = document.getElementById('signUpFormContainer');

      roleSelect.addEventListener('change', () => {
        const role = roleSelect.value;
        formContainer.innerHTML = role ? signUpForms[role] : '';
        if (role === 'patient') {
          const dobInput = document.getElementById('dob');
          const ageInput = document.getElementById('age');
          if (dobInput && ageInput) {
            dobInput.addEventListener('change', () => {
              const dob = dobInput.value;
              if (dob) {
                ageInput.value = calculateAge(dob);
              } else {
                ageInput.value = '';
              }
            });
          }
        }
        initializeSignUpForm(role);
      });
    }

    function initializeSignUpForm(role) {
      const signUpForm = document.getElementById('signUpForm');
      const passwordInput = document.getElementById('signUpPassword');
      const strengthBar = document.getElementById('password-strength');

      if (passwordInput && strengthBar) {
        passwordInput.addEventListener('input', () => {
          const password = passwordInput.value;
          let strength = 'weak';
          if (password.length >= 8 && /[A-Z]/.test(password) && /[0-9]/.test(password)) strength = 'strong';
          else if (password.length >= 6) strength = 'medium';
          strengthBar.className = `password-strength ${strength}`;
        });
      }

      if (signUpForm) {
        signUpForm.addEventListener('submit', (e) => {
          e.preventDefault();
          let formData = {
            username: sanitizeInput(document.getElementById('signUpUsername').value),
            password: document.getElementById('signUpPassword').value
          };

          if (role === 'admin') {
            formData.adminName = sanitizeInput(document.getElementById('adminName').value);
            formData.department = sanitizeInput(document.getElementById('department').value);
            formData.employeeId = sanitizeInput(document.getElementById('employeeId').value);
          } else if (role === 'doctor') {
            formData.doctorName = sanitizeInput(document.getElementById('doctorName').value);
            formData.specialization = sanitizeInput(document.getElementById('specialization').value);
            formData.licenseNumber = sanitizeInput(document.getElementById('licenseNumber').value);
          } else if (role === 'nurse') {
            formData.nurseName = sanitizeInput(document.getElementById('nurseName').value);
            formData.certificationNumber = sanitizeInput(document.getElementById('certificationNumber').value);
            formData.shiftPreference = sanitizeInput(document.getElementById('shiftPreference').value);
          } else if (role === 'patient') {
            formData.patientName = sanitizeInput(document.getElementById('patientName').value);
            formData.bloodGroup = sanitizeInput(document.getElementById('bloodGroup').value);
            formData.dob = sanitizeInput(document.getElementById('dob').value);
            formData.allergies = sanitizeInput(document.getElementById('allergies').value);
            formData.gender = sanitizeInput(document.querySelector('input[name="gender"]:checked')?.value);
          }

          if (validateSignUpForm(role, formData)) {
            if (saveUser(formData.username, formData.password, role, formData)) {
              state.isAuthenticated = true;
              state.currentUser = formData.username;
              state.currentRole = role;
              loadPage(`${role}-dashboard`);
              announce('Sign up successful.');
            } else {
              document.getElementById('signUpUsername-error').textContent = 'Username already exists';
              document.getElementById('signUpUsername-error').classList.remove('hidden');
            }
          }
        });
      }
    }

    function initializeContactForm() {
      const contactForm = document.getElementById('contactForm');
      if (contactForm) {
        contactForm.addEventListener('submit', (e) => {
          e.preventDefault();
          const name = sanitizeInput(document.getElementById('name').value);
          const email = sanitizeInput(document.getElementById('email').value);
          const message = sanitizeInput(document.getElementById('message').value);

          if (validateContactForm(name, email, message)) {
            announce('Message sent successfully.');
            contactForm.reset();
          }
        });
      }
    }

    function initializeDashboard(role) {
      const searchInput = document.getElementById(`${role}Search`);
      const tableBody = document.getElementById(`${role}Table`).querySelector('tbody');
      const chartCanvas = document.getElementById(`${role}Chart`);

      if (searchInput) {
        searchInput.addEventListener('input', debounce((e) => {
          const query = sanitizeInput(e.target.value.toLowerCase());
          const data = sampleData[role];
          const filtered = data.filter(item => Object.values(item).some(val => val.toString().toLowerCase().includes(query)));
          tableBody.innerHTML = filtered.map(item => `
            <tr>
              <td>${item.id}</td>
              <td>${item.name || item.patient || item.date}</td>
              <td>${item.status || item.condition || item.task || item.type}</td>
              <td>${item.users || item.uptime || item.nextVisit || item.time || item.doctor}</td>
              <td><button class="text-blue-500 hover:underline">${role === 'admin' ? (item.name === 'User Management' ? 'Manage' : 'View Report') : role === 'doctor' ? 'View Record' : role === 'nurse' ? 'Update' : 'View Details'}</button></td>
            </tr>
          `).join('');
        }, 300));
      }

      if (chartCanvas) {
        new Chart(chartCanvas.getContext('2d'), {
          type: 'bar',
          data: {
            labels: ['Jan', 'Feb', 'Mar'],
            datasets: [{
              label: role === 'admin' ? 'Users' : role === 'doctor' ? 'Patients' : role === 'nurse' ? 'Tasks' : 'Appointments',
              data: [10, 15, 20],
              backgroundColor: '#3498db'
            }]
          },
          options: { responsive: true }
        });
      }

      if (role === 'patient') {
        const appointmentForm = document.getElementById('appointmentForm');
        if (appointmentForm) {
          appointmentForm.addEventListener('submit', (e) => {
            e.preventDefault();
            const date = sanitizeInput(document.getElementById('appointmentDate').value);
            const doctor = sanitizeInput(document.getElementById('doctorSelect').value);
            const reason = sanitizeInput(document.getElementById('reason').value);

            if (validateAppointmentForm(date, doctor, reason)) {
              const appointments = JSON.parse(localStorage.getItem('appointments') || '[]');
              appointments.push({ date, doctor, reason });
              localStorage.setItem('appointments', JSON.stringify(appointments));
              announce(translations[state.language].appointmentScheduled);
              appointmentForm.reset();
              addNotification(`New appointment scheduled for ${date} with ${doctor}`);
            }
          });
        }
      }
    }

    function initializeProfileForm() {
      const profileForm = document.getElementById('profileForm');
      const profileName = document.getElementById('profileName');
      const users = getUsers();
      const user = users.find(u => u.username === state.currentUser);
      if (profileName && user) {
        profileName.value = user[state.currentRole + 'Name'] || '';
      }

      if (profileForm) {
        profileForm.addEventListener('submit', (e) => {
          e.preventDefault();
          const name = sanitizeInput(document.getElementById('profileName').value);
          if (name) {
            const users = getUsers();
            const userIndex = users.findIndex(u => u.username === state.currentUser);
            users[userIndex][state.currentRole + 'Name'] = name;
            localStorage.setItem('users', JSON.stringify(users));
            announce('Profile updated successfully.');
          } else {
            document.getElementById('profileName-error').textContent = 'Name is required';
            document.getElementById('profileName-error').classList.remove('hidden');
          }
        });
      }
    }

    function getUsers() {
      return JSON.parse(localStorage.getItem('users') || '[]');
    }

    function saveUser(username, password, role, details = {}) {
      const users = getUsers();
      if (users.find(user => user.username === username)) {
        return false;
      }
      users.push({ username, password, role, ...details });
      localStorage.setItem('users', JSON.stringify(users));
      return true;
    }

    function authenticateUser(username, password) {
      const users = getUsers();
      const user = users.find(user => user.username === username && user.password === password);
      if (user) {
        state.currentRole = user.role;
        return true;
      }
      return false;
    }

    function debounce(func, wait) {
      let timeout;
      return (...args) => {
        clearTimeout(timeout);
        timeout = setTimeout(() => func.apply(this, args), wait);
      };
    }

    function addNotification(message) {
      const notifications = JSON.parse(localStorage.getItem('notifications') || '[]');
      notifications.push({ message, timestamp: new Date().toISOString() });
      localStorage.setItem('notifications', JSON.stringify(notifications));
      updateNotifications();
    }

    function updateNotifications() {
      const notificationCount = document.getElementById('notification-count');
      const notificationList = document.getElementById('notificationList');
      const notifications = JSON.parse(localStorage.getItem('notifications') || '[]');
      notificationCount.textContent = notifications.length;
      notificationCount.classList.toggle('hidden', notifications.length === 0);
      notificationList.innerHTML = notifications.map(n => `<li class="text-sm">${n.message} (${new Date(n.timestamp).toLocaleString()})</li>`).join('');
    }

    function updateLanguage() {
      document.querySelectorAll('[data-translate]').forEach(el => {
        const key = el.getAttribute('data-translate');
        el.textContent = translations[state.language][key] || el.textContent;
      });
    }

    // Event Listeners
    document.getElementById('signInForm').addEventListener('submit', (e) => {
      e.preventDefault();
      const username = sanitizeInput(document.getElementById('signInUsername').value);
      const password = document.getElementById('signInPassword').value;

      if (validateSignInForm(username, password)) {
        if (authenticateUser(username, password)) {
          state.isAuthenticated = true;
          state.currentUser = username;
          closeSignInModal();
          loadPage(`${state.currentRole}-dashboard`);
          announce('Signed in successfully.');
          resetSessionTimeout();
        } else {
          document.getElementById('signInUsername-error').textContent = 'Invalid username or password';
          document.getElementById('signInUsername-error').classList.remove('hidden');
        }
      }
    });

    document.getElementById('mobile-menu-btn').addEventListener('click', () => {
      const mobileMenu = document.getElementById('mobile-menu');
      mobileMenu.style.display = mobileMenu.style.display === 'none' ? 'block' : 'none';
      resetSessionTimeout();
    });

    document.getElementById('signInModal').addEventListener('click', (e) => {
      if (e.target === document.getElementById('signInModal')) {
        closeSignInModal();
      }
    });

    document.getElementById('notification-bell').addEventListener('click', () => {
      document.getElementById('notificationModal').style.display = 'flex';
      announce('Notifications modal opened.');
      resetSessionTimeout();
    });

    document.getElementById('notificationModal').addEventListener('click', (e) => {
      if (e.target === document.getElementById('notificationModal')) {
        document.getElementById('notificationModal').style.display = 'none';
        announce('Notifications modal closed.');
      }
    });

    document.getElementById('theme-toggle').addEventListener('click', () => {
      document.body.classList.toggle('dark');
      localStorage.setItem('theme', document.body.classList.contains('dark') ? 'dark' : 'light');
      resetSessionTimeout();
    });

    document.getElementById('languageSelect').addEventListener('change', (e) => {
      state.language = e.target.value;
      localStorage.setItem('language', state.language);
      updateLanguage();
      updateNavLinks();
      resetSessionTimeout();
    });

    document.getElementById('sidebar-toggle').addEventListener('click', () => {
      const sidebar = document.getElementById('sidebar');
      sidebar.classList.toggle('collapsed');
      document.getElementById('main-content').style.marginLeft = sidebar.classList.contains('collapsed') ? '60px' : '250px';
      localStorage.setItem('sidebar', sidebar.classList.contains('collapsed') ? 'collapsed' : 'open');
      resetSessionTimeout();
    });

    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape' && document.getElementById('signInModal').style.display === 'flex') {
        closeSignInModal();
      } else if (e.key === 'Enter' || e.key === ' ') {
        if (e.target.classList.contains('role-card')) {
          e.preventDefault();
          e.target.querySelector('button').click();
        }
      }
      resetSessionTimeout();
    });

    document.querySelectorAll('.role-card button').forEach(btn => {
      btn.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          e.preventDefault();
          btn.click();
        }
      });
    });

    document.querySelectorAll('.nav-link').forEach(link => {
      link.addEventListener('click', (e) => {
        e.preventDefault();
        const page = e.target.getAttribute('data-page');
        if (page === 'logout') {
          state.isAuthenticated = false;
          state.currentUser = null;
          state.currentRole = null;
          loadPage('home');
          history.pushState({ page: 'home' }, '', '#home');
          announce('Logged out successfully.');
        } else if (page === 'sign-in') {
          showSignInModal(null);
        } else {
          loadPage(page);
          history.pushState({ page }, '', `#${page}`);
        }
        document.getElementById('mobile-menu').style.display = 'none';
        resetSessionTimeout();
      });
    });

    document.querySelectorAll('.sidebar a').forEach(link => {
      link.addEventListener('click', (e) => {
        e.preventDefault();
        const page = e.target.closest('a').getAttribute('data-page');
        if (page === 'dashboard') {
          loadPage(`${state.currentRole}-dashboard`);
          history.pushState({ page: `${state.currentRole}-dashboard` }, '', `#${state.currentRole}-dashboard`);
        } else {
          loadPage(page);
          history.pushState({ page }, '', `#${page}`);
        }
        resetSessionTimeout();
      });
    });

    window.addEventListener('popstate', (e) => {
      const page = e.state ? e.state.page : 'home';
      loadPage(page);
      resetSessionTimeout();
    });

    function resetSessionTimeout() {
      clearTimeout(window.sessionTimeout);
      window.sessionTimeout = setTimeout(() => {
        if (state.isAuthenticated) {
          state.isAuthenticated = false;
          state.currentUser = null;
          state.currentRole = null;
          loadPage('home');
          announce('Session expired due to inactivity.');
        }
      }, 30 * 60 * 1000); // 30 minutes
    }

    // Initialize
    if (localStorage.getItem('theme') === 'dark') document.body.classList.add('dark');
    if (localStorage.getItem('sidebar') === 'collapsed') {
      document.getElementById('sidebar').classList.add('collapsed');
      document.getElementById('main-content').style.marginLeft = '60px';
    }
    document.getElementById('languageSelect').value = state.language;
    updateNotifications();
    setInterval(() => addNotification('System update available'), 60000); // Demo notification every minute
    const initialPage = window.location.hash.replace('#', '') || 'home';
    loadPage(initialPage);
    history.replaceState({ page: initialPage }, '', `#${initialPage}`);
    resetSessionTimeout();
    document.addEventListener('mousemove', resetSessionTimeout);
    document.addEventListener('keydown', resetSessionTimeout);
  </script>
</body>
</html>
