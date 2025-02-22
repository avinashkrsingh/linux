<!DOCTYPE html>
<html>
<head>
  <style>
    /* Animation for Deployment Status */
    @keyframes blink {
      50% { opacity: 0.5; }
    }
    .status-blink {
      color: green;
      font-weight: bold;
      animation: blink 1.5s infinite;
    }
    .release-container {
      font-family: Arial, sans-serif;
      padding: 20px;
      background-color: #f9f9f9;
      border-radius: 10px;
      max-width: 700px;
      margin: auto;
    }
    h1 {
      color: #007bff;
    }
    .highlight {
      background-color: #e7f4ff;
      padding: 5px 10px;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <div class="release-container">
    <h1>🚀 Release & Deployment Notification</h1>
    <p>Dear Team,</p>
    <p>The deployment for <strong>[Application/Service Name]</strong> with <strong>Release Version [X.X.X]</strong> has been successfully completed. Here are the details:</p>

    <!-- Animated GIF Section -->
    <h2>✅ Deployment Status:</h2>
    <p class="status-blink">
      <img src="https://media.giphy.com/media/3o7aD2saalBwwftBIY/giphy.gif" alt="Deployment Success" width="100">
      ✔ Deployment Successful
    </p>

    <h2>📦 Release Details:</h2>
    <ul>
      <li><strong>Application/Service:</strong> [Application/Service Name]</li>
      <li><strong>Release Version:</strong> <span class="highlight">[X.X.X]</span></li>
      <li><strong>Environment:</strong> [Development / Staging / Production]</li>
      <li><strong>Deployment Date & Time:</strong> [YYYY-MM-DD, HH:MM Timezone]</li>
      <li><strong>Deployed By:</strong> [Deployer's Name / Team]</li>
    </ul>

    <h2>📝 Key Changes:</h2>
    <ul>
      <li>✨ Feature 1: [Brief description]</li>
      <li>🐞 Bug Fix 1: [Brief description]</li>
      <li>🚀 Enhancement: [Brief description]</li>
    </ul>

    <h2>📄 Post-Deployment Checks:</h2>
    <ul>
      <li><strong>Health Check:</strong> Pass ✅</li>
      <li><strong>Smoke Testing:</strong> Completed 🟢</li>
      <li><strong>Rollback Plan:</strong> Not Required 🚫</li>
    </ul>

    <h2>📞 Support Contact:</h2>
    <p>For any issues, please reach out to:</p>
    <ul>
      <li>👨‍💻 <strong>On-Call Engineer:</strong> [Name, Contact Info]</li>
      <li>📧 <strong>Support Email:</strong> [Support Email ID]</li>
    </ul>

    <p>Thank you for your support and collaboration.</p>
    <p><strong>Best regards,</strong><br/>
    [Your Name] <br/>
    [Team/Organization Name] <br/>
    [Contact Information]</p>
  </div>
</body>
</html>
