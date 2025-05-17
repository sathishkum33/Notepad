<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Server Monitor</title>
  <style>
    body {
      background-color: #0d1117;
      color: white;
      font-family: Arial, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    .server {
      background-color: #161b22;
      border-radius: 16px;
      padding: 24px;
      width: 320px;
      box-shadow: 0 0 15px rgba(0, 255, 150, 0.2);
    }

    .resource {
      margin-bottom: 20px;
      display: flex;
      align-items: center;
    }

    .resource:last-child {
      margin-bottom: 0;
    }

    .icon {
      width: 28px;
      height: 28px;
      margin-right: 10px;
    }

    .details {
      flex: 1;
    }

    .label {
      margin-bottom: 4px;
    }

    .bar-container {
      background-color: #2f363d;
      border-radius: 8px;
      height: 16px;
      overflow: hidden;
    }

    .bar {
      height: 100%;
      width: 0;
      background: linear-gradient(to right, #00ff99, #00ccff);
      transition: width 0.5s ease-in-out;
    }

    .os-section {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .os-icons {
      display: flex;
      gap: 8px;
    }

    .os-icons img {
      width: 24px;
      height: 24px;
    }

    .os-name {
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div class="server">
    <div class="resource">
      <img src="https://img.icons8.com/ios-filled/50/microchip.png" class="icon" alt="RAM">
      <div class="details">
        <div class="label">RAM: <span id="ram-value">0</span>/16 GB</div>
        <div class="bar-container"><div class="bar" id="ram-bar"></div></div>
      </div>
    </div>

    <div class="resource">
      <img src="https://img.icons8.com/ios-filled/50/cpu.png" class="icon" alt="CPU">
      <div class="details">
        <div class="label">CPU: <span id="cpu-value">0</span>/4 Cores</div>
        <div class="bar-container"><div class="bar" id="cpu-bar"></div></div>
      </div>
    </div>

    <div class="resource">
      <img src="https://img.icons8.com/ios-filled/50/hdd.png" class="icon" alt="Disk">
      <div class="details">
        <div class="label">Disk: <span id="disk-value">0</span>/300 GB</div>
        <div class="bar-container"><div class="bar" id="disk-bar"></div></div>
      </div>
    </div>

    <div class="resource os-section">
      <div class="os-icons">
        <img src="https://img.icons8.com/ios-filled/50/windows8.png" alt="Windows">
        <img src="https://img.icons8.com/ios-filled/50/linux.png" alt="Linux">
        <img src="https://img.icons8.com/ios-filled/50/mac-os.png" alt="macOS">
      </div>
      <div class="os-name" id="os-name">RHEL</div>
    </div>
  </div>

  <script>
    function updateResources() {
      const ramUsed = Math.floor(Math.random() * 17);  // 0–16
      const cpuUsed = Math.floor(Math.random() * 5);   // 0–4
      const diskUsed = Math.floor(Math.random() * 301); // 0–300

      document.getElementById('ram-value').textContent = ramUsed;
      document.getElementById('cpu-value').textContent = cpuUsed;
      document.getElementById('disk-value').textContent = diskUsed;

      document.getElementById('ram-bar').style.width = (ramUsed / 16) * 100 + '%';
      document.getElementById('cpu-bar').style.width = (cpuUsed / 4) * 100 + '%';
      document.getElementById('disk-bar').style.width = (diskUsed / 300) * 100 + '%';

      // Random OS selector
      const osOptions = ['RHEL', 'Debian', 'Ubuntu', 'Windows 10', 'macOS'];
      const randomOS = osOptions[Math.floor(Math.random() * osOptions.length)];
      document.getElementById('os-name').textContent = randomOS;
    }

    setInterval(updateResources, 1000);
    updateResources();
  </script>
</body>
</html>