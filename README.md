# Index.html
ASCII CAMERA FOR MOBILE 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>ASCII CAM</title>
<link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=VT323:wght@400&display=swap" rel="stylesheet">
<style>
  :root {
    --green: #00ff41;
    --dim-green: #00aa2a;
    --dark-green: #003b10;
    --black: #0a0a0a;
    --bg: #050505;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--green);
    font-family: 'Share Tech Mono', monospace;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    overflow: hidden;
    touch-action: manipulation;
  }

  /* Scanline overlay */
  body::after {
    content: '';
    position: fixed;
    inset: 0;
    background: repeating-linear-gradient(
      to bottom,
      transparent,
      transparent 2px,
      rgba(0,0,0,0.08) 2px,
      rgba(0,0,0,0.08) 4px
    );
    pointer-events: none;
    z-index: 999;
  }

  header {
    width: 100%;
    padding: 10px 16px 6px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid var(--dark-green);
    background: rgba(0,0,0,0.6);
    backdrop-filter: blur(4px);
    position: sticky;
    top: 0;
    z-index: 10;
  }

  .logo {
    font-family: 'VT323', monospace;
    font-size: 28px;
    letter-spacing: 4px;
    color: var(--green);
    text-shadow: 0 0 10px var(--green);
  }

  .status-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--green);
    box-shadow: 0 0 8px var(--green);
    animation: blink 1.2s step-end infinite;
  }

  @keyframes blink { 50% { opacity: 0; } }

  .cam-info {
    font-size: 11px;
    color: var(--dim-green);
    letter-spacing: 1px;
  }

  #viewfinder-wrap {
    width: 100%;
    flex: 1;
    position: relative;
    display: flex;
    align-items: flex-start;
    justify-content: center;
    overflow: hidden;
    padding: 8px 4px 0;
  }

  #ascii-output {
    font-family: 'Share Tech Mono', monospace;
    font-size: 5.2px;
    line-height: 1.05;
    letter-spacing: 0.5px;
    white-space: pre;
    color: var(--green);
    text-shadow: 0 0 4px rgba(0,255,65,0.4);
    width: 100%;
    word-break: break-all;
    overflow: hidden;
    transition: color 0.1s;
  }

  /* Corner brackets */
  .corner {
    position: absolute;
    width: 20px; height: 20px;
    border-color: var(--green);
    border-style: solid;
    opacity: 0.7;
  }
  .corner.tl { top: 12px; left: 8px; border-width: 2px 0 0 2px; }
  .corner.tr { top: 12px; right: 8px; border-width: 2px 2px 0 0; }
  .corner.bl { bottom: 0; left: 8px; border-width: 0 0 2px 2px; }
  .corner.br { bottom: 0; right: 8px; border-width: 0 2px 2px 0; }

  /* Controls */
  #controls {
    width: 100%;
    padding: 12px 16px 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    background: rgba(0,0,0,0.7);
    border-top: 1px solid var(--dark-green);
  }

  .btn-row {
    display: flex;
    gap: 12px;
    align-items: center;
    justify-content: center;
    width: 100%;
  }

  .btn {
    font-family: 'Share Tech Mono', monospace;
    font-size: 13px;
    letter-spacing: 2px;
    color: var(--black);
    background: var(--green);
    border: none;
    padding: 10px 18px;
    cursor: pointer;
    text-transform: uppercase;
    transition: all 0.15s;
    -webkit-tap-highlight-color: transparent;
    clip-path: polygon(6px 0%, 100% 0%, calc(100% - 6px) 100%, 0% 100%);
    user-select: none;
  }

  .btn:active { transform: scale(0.95); background: var(--dim-green); }

  .btn.shutter {
    width: 72px; height: 72px;
    border-radius: 50%;
    clip-path: none;
    font-size: 22px;
    padding: 0;
    box-shadow: 0 0 0 3px var(--black), 0 0 0 5px var(--green), 0 0 20px rgba(0,255,65,0.4);
    display: flex; align-items: center; justify-content: center;
  }

  .btn.shutter:active {
    box-shadow: 0 0 0 3px var(--black), 0 0 0 5px var(--dim-green), 0 0 8px rgba(0,255,65,0.2);
  }

  .btn.secondary {
    background: transparent;
    color: var(--green);
    border: 1px solid var(--dim-green);
    font-size: 11px;
    padding: 8px 14px;
  }

  .btn.secondary:active { background: var(--dark-green); }

  /* Slider row */
  .slider-row {
    display: flex;
    align-items: center;
    gap: 10px;
    width: 100%;
    font-size: 11px;
    color: var(--dim-green);
  }

  input[type=range] {
    flex: 1;
    -webkit-appearance: none;
    height: 3px;
    background: var(--dark-green);
    outline: none;
    border-radius: 2px;
  }

  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 14px; height: 14px;
    background: var(--green);
    border-radius: 50%;
    box-shadow: 0 0 6px var(--green);
  }

  /* Flash effect */
  #flash {
    position: fixed;
    inset: 0;
    background: var(--green);
    opacity: 0;
    pointer-events: none;
    z-index: 1000;
    transition: opacity 0.05s;
  }

  /* Captured overlay */
  #captured-overlay {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.92);
    z-index: 200;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 16px;
    gap: 14px;
  }

  #captured-overlay.show { display: flex; }

  .captured-label {
    font-family: 'VT323', monospace;
    font-size: 22px;
    letter-spacing: 4px;
    color: var(--green);
    text-shadow: 0 0 8px var(--green);
  }

  #captured-art {
    font-family: 'Share Tech Mono', monospace;
    font-size: 4.5px;
    line-height: 1.05;
    white-space: pre;
    color: var(--green);
    max-width: 100%;
    overflow: hidden;
    border: 1px solid var(--dark-green);
    padding: 6px;
    background: #020202;
    max-height: 65vh;
    overflow-y: auto;
  }

  #start-screen {
    position: fixed;
    inset: 0;
    background: var(--bg);
    z-index: 100;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 20px;
    padding: 30px;
  }

  .start-logo {
    font-family: 'VT323', monospace;
    font-size: 52px;
    letter-spacing: 8px;
    color: var(--green);
    text-shadow: 0 0 20px var(--green), 0 0 40px rgba(0,255,65,0.3);
    animation: flicker 4s infinite;
  }

  @keyframes flicker {
    0%,19%,21%,23%,25%,54%,56%,100% { opacity: 1; }
    20%,24%,55% { opacity: 0.4; }
  }

  .start-subtitle {
    font-size: 12px;
    letter-spacing: 3px;
    color: var(--dim-green);
    text-align: center;
    line-height: 2;
  }

  #error-msg {
    display: none;
    font-size: 12px;
    color: #ff4141;
    text-align: center;
    padding: 10px;
    border: 1px solid #440000;
    background: #1a0000;
    letter-spacing: 1px;
    line-height: 1.8;
  }

  canvas { display: none; }
</style>
</head>
<body>

<div id="flash"></div>
<canvas id="canvas"></canvas>
<video id="video" playsinline autoplay muted style="display:none"></video>

<!-- Start Screen -->
<div id="start-screen">
  <div class="start-logo">ASCII<br>CAM</div>
  <div class="start-subtitle">
    TERMINAL PHOTOGRAPHY SYSTEM<br>
    v1.0 &nbsp;●&nbsp; MOBILE EDITION
  </div>
  <div id="error-msg"></div>
  <button class="btn" id="start-btn" onclick="startCamera()" style="font-size:14px;padding:14px 30px;letter-spacing:4px;">
    ▶ INITIALIZE
  </button>
  <div style="font-size:10px;color:#004a15;letter-spacing:2px;text-align:center;">
    CAMERA ACCESS REQUIRED
  </div>
</div>

<!-- Main UI -->
<header>
  <div class="logo">ASCII CAM</div>
  <div class="cam-info" id="cam-info">LIVE</div>
  <div class="status-dot" id="status-dot"></div>
</header>

<div id="viewfinder-wrap">
  <pre id="ascii-output"></pre>
  <div class="corner tl"></div>
  <div class="corner tr"></div>
  <div class="corner bl"></div>
  <div class="corner br"></div>
</div>

<div id="controls">
  <div class="slider-row">
    <span>DENSITY</span>
    <input type="range" id="density-slider" min="0" max="2" step="1" value="1">
    <span id="density-label">MED</span>
  </div>
  <div class="btn-row">
    <button class="btn secondary" onclick="flipCamera()">⟳ FLIP</button>
    <button class="btn shutter" id="shutter-btn" onclick="takePicture()">◉</button>
    <button class="btn secondary" onclick="toggleInvert()">◐ INVERT</button>
  </div>
</div>

<!-- Captured Overlay -->
<div id="captured-overlay">
  <div class="captured-label">// CAPTURED //</div>
  <pre id="captured-art"></pre>
  <div class="btn-row">
    <button class="btn" onclick="saveAsPNG()" style="font-size:12px;padding:10px 18px;">⬇ SAVE IMAGE</button>
    <button class="btn secondary" onclick="copyAscii()">⎘ COPY</button>
    <button class="btn secondary" onclick="closeCaptured()">✕ CLOSE</button>
  </div>
</div>

<script>
  const video = document.getElementById('video');
  const canvas = document.getElementById('canvas');
  const ctx = canvas.getContext('2d', { willReadFrequently: true });
  const output = document.getElementById('ascii-output');
  const flash = document.getElementById('flash');

  // ASCII char sets
  const CHARSETS = [
    ' .:-=+*#%@',            // sparse
    ' .,:;i1tfLCG08@',       // medium
    ' `.-_:,^~=+<>i!lI;:,"^`\'.', // dense
  ];

  let animFrame = null;
  let inverted = false;
  let facingMode = 'environment';
  let stream = null;
  let currentCharset = CHARSETS[1];
  let capturedText = '';
  let cols = 80;

  // Density slider
  const densitySlider = document.getElementById('density-slider');
  const densityLabel = document.getElementById('density-label');
  const densityNames = ['LOW', 'MED', 'HIGH'];
  densitySlider.addEventListener('input', () => {
    const v = parseInt(densitySlider.value);
    currentCharset = CHARSETS[v];
    densityLabel.textContent = densityNames[v];
  });

  function getAsciiChar(brightness) {
    const chars = inverted
      ? currentCharset.split('').reverse().join('')
      : currentCharset;
    const idx = Math.floor((brightness / 255) * (chars.length - 1));
    return chars[idx] || ' ';
  }

  function calculateCols() {
    // based on screen width and font size ~5.2px with ~3.2px char width
    const charW = 3.3;
    cols = Math.floor(window.innerWidth / charW);
    cols = Math.max(40, Math.min(cols, 120));
  }

  function renderFrame() {
    if (video.readyState < 2) {
      animFrame = requestAnimationFrame(renderFrame);
      return;
    }

    calculateCols();
    const aspect = video.videoHeight / video.videoWidth;
    const rows = Math.floor(cols * aspect * 0.5); // chars are taller than wide

    canvas.width = cols;
    canvas.height = rows;
    ctx.drawImage(video, 0, 0, cols, rows);

    const imageData = ctx.getImageData(0, 0, cols, rows);
    const data = imageData.data;

    let result = '';
    for (let r = 0; r < rows; r++) {
      for (let c = 0; c < cols; c++) {
        const i = (r * cols + c) * 4;
        const brightness = 0.299 * data[i] + 0.587 * data[i+1] + 0.114 * data[i+2];
        result += getAsciiChar(brightness);
      }
      result += '\n';
    }

    output.textContent = result;
    animFrame = requestAnimationFrame(renderFrame);
  }

  async function startCamera() {
    const errorEl = document.getElementById('error-msg');
    errorEl.style.display = 'none';

    try {
      if (stream) {
        stream.getTracks().forEach(t => t.stop());
      }

      stream = await navigator.mediaDevices.getUserMedia({
        video: {
          facingMode: facingMode,
          width: { ideal: 320 },
          height: { ideal: 240 }
        },
        audio: false
      });

      video.srcObject = stream;
      await video.play();

      document.getElementById('start-screen').style.display = 'none';
      document.getElementById('cam-info').textContent = facingMode === 'environment' ? 'REAR' : 'FRONT';

      if (animFrame) cancelAnimationFrame(animFrame);
      renderFrame();

    } catch (err) {
      errorEl.style.display = 'block';
      if (err.name === 'NotAllowedError') {
        errorEl.textContent = '⚠ CAMERA ACCESS DENIED\nPlease allow camera permission\nand reload the page.';
      } else if (err.name === 'NotFoundError') {
        errorEl.textContent = '⚠ NO CAMERA FOUND\nThis device has no camera.';
      } else {
        errorEl.textContent = '⚠ ERROR: ' + err.message;
      }
    }
  }

  async function flipCamera() {
    facingMode = facingMode === 'environment' ? 'user' : 'environment';
    await startCamera();
  }

  function toggleInvert() {
    inverted = !inverted;
  }

  function takePicture() {
    // Flash effect
    flash.style.opacity = '0.7';
    setTimeout(() => { flash.style.opacity = '0'; }, 120);

    // Capture current frame
    capturedText = output.textContent;
    document.getElementById('captured-art').textContent = capturedText;
    document.getElementById('captured-overlay').classList.add('show');
  }

  function closeCaptured() {
    document.getElementById('captured-overlay').classList.remove('show');
  }

  function downloadAscii() {
    const blob = new Blob([capturedText], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'ascii_photo_' + Date.now() + '.txt';
    a.click();
    URL.revokeObjectURL(url);
  }

  function saveAsPNG() {
    const lines = capturedText.split('\n').filter(l => l.length > 0);
    if (!lines.length) return;

    const fontSize = 8;
    const lineHeight = fontSize * 1.15;
    const charWidth = fontSize * 0.6;
    const padding = 16;

    const imgW = Math.max(...lines.map(l => l.length)) * charWidth + padding * 2;
    const imgH = lines.length * lineHeight + padding * 2;

    const offscreen = document.createElement('canvas');
    offscreen.width = imgW;
    offscreen.height = imgH;
    const c = offscreen.getContext('2d');

    // Background
    c.fillStyle = '#050505';
    c.fillRect(0, 0, imgW, imgH);

    // ASCII text in green
    c.fillStyle = '#00ff41';
    c.font = `${fontSize}px 'Courier New', monospace`;
    c.textBaseline = 'top';

    lines.forEach((line, i) => {
      c.fillText(line, padding, padding + i * lineHeight);
    });

    // Export as PNG
    offscreen.toBlob(blob => {
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'ascii_photo_' + Date.now() + '.png';
      a.click();
      URL.revokeObjectURL(url);
    }, 'image/png');
  }

  async function copyAscii() {
    try {
      await navigator.clipboard.writeText(capturedText);
      const btn = event.target;
      const orig = btn.textContent;
      btn.textContent = '✓ COPIED';
      setTimeout(() => { btn.textContent = orig; }, 1500);
    } catch {
      // fallback
      const ta = document.createElement('textarea');
      ta.value = capturedText;
      document.body.appendChild(ta);
      ta.select();
      document.execCommand('copy');
      ta.remove();
    }
  }

  // Prevent zoom on double tap
  let lastTap = 0;
  document.addEventListener('touchend', (e) => {
    const now = Date.now();
    if (now - lastTap < 300) e.preventDefault();
    lastTap = now;
  }, { passive: false });
</script>
</body>
</html>

