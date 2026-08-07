<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Симулятор Jerry</title>
<style>
  html, body {
    margin: 0;
    padding: 0;
    background: #304F6B;
    overflow: hidden;
    height: 100%;
    width: 100%;
    touch-action: none;
    font-family: monospace;
  }
  #gameCanvas {
    display: block;
    background: #304F6B;
  }
  #consoleBtn {
    position: fixed;
    bottom: 8px;
    right: 8px;
    width: 24px;
    height: 24px;
    background: rgba(255,255,255,0.12);
    border: 1px solid rgba(255,255,255,0.25);
    border-radius: 5px;
    color: rgba(255,255,255,0.5);
    font-size: 11px;
    font-weight: bold;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    user-select: none;
    z-index: 100;
    -webkit-tap-highlight-color: transparent;
    outline: none;
  }
  #consoleBtn:active {
    background: rgba(255,255,255,0.25);
  }
  #consolePanel {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 280px;
    max-height: 80vh;
    overflow-y: auto;
    background: rgba(30, 40, 55, 0.95);
    border: 1px solid rgba(255,255,255,0.2);
    border-radius: 12px;
    padding: 16px;
    display: none;
    z-index: 200;
    box-shadow: 0 8px 32px rgba(0,0,0,0.5);
  }
  #consolePanel.active {
    display: block;
  }
  #consoleOverlay {
    position: fixed;
    top: 0; left: 0; right: 0; bottom: 0;
    background: rgba(0,0,0,0.4);
    display: none;
    z-index: 150;
  }
  #consoleOverlay.active {
    display: block;
  }
  .console-row {
    margin-bottom: 14px;
  }
  .console-label {
    color: rgba(255,255,255,0.7);
    font-size: 12px;
    margin-bottom: 6px;
    display: block;
  }
  .console-input {
    width: 100%;
    box-sizing: border-box;
    background: rgba(255,255,255,0.1);
    border: 1px solid rgba(255,255,255,0.2);
    border-radius: 6px;
    color: #fff;
    font-size: 14px;
    padding: 8px 10px;
    font-family: monospace;
    outline: none;
  }
  .console-input:focus {
    border-color: rgba(255,255,255,0.5);
  }
  .console-input.error {
    border-color: #F22C39;
    background: rgba(242,44,57,0.1);
  }
  .size-row {
    display: flex;
    gap: 8px;
  }
  .size-row .console-input {
    width: 50%;
  }
  .color-btns {
    display: flex;
    gap: 8px;
    align-items: center;
  }
  .color-btn {
    width: 36px;
    height: 36px;
    border-radius: 8px;
    border: 2px solid transparent;
    cursor: pointer;
    transition: transform 0.1s, border-color 0.2s;
    -webkit-tap-highlight-color: transparent;
    outline: none;
    flex-shrink: 0;
  }
  .color-btn:active {
    transform: scale(0.9);
  }
  .color-btn.selected {
    border-color: #fff;
    box-shadow: 0 0 8px rgba(255,255,255,0.4);
  }
  .color-btn.random {
    background: rgba(255,255,255,0.15);
    color: rgba(255,255,255,0.8);
    font-size: 18px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 2px solid rgba(255,255,255,0.2);
  }
  .color-btn.random.selected {
    border-color: #fff;
    background: rgba(255,255,255,0.3);
  }
  .console-close {
    position: absolute;
    top: 8px;
    right: 12px;
    color: rgba(255,255,255,0.5);
    font-size: 18px;
    cursor: pointer;
    line-height: 1;
    -webkit-tap-highlight-color: transparent;
    outline: none;
  }
  .console-close:active {
    color: #fff;
  }
  .console-title {
    color: #fff;
    font-size: 14px;
    font-weight: bold;
    margin-bottom: 16px;
    text-align: center;
  }
  .chance-row {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 6px;
  }
  .chance-dot {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    flex-shrink: 0;
  }
  .chance-input {
    width: 60px;
    background: rgba(255,255,255,0.1);
    border: 1px solid rgba(255,255,255,0.2);
    border-radius: 4px;
    color: #fff;
    font-size: 13px;
    padding: 4px 6px;
    font-family: monospace;
    outline: none;
  }
  .chance-input:focus {
    border-color: rgba(255,255,255,0.5);
  }
  .chance-input.error {
    border-color: #F22C39;
    background: rgba(242,44,57,0.1);
  }
  .chance-label {
    color: rgba(255,255,255,0.6);
    font-size: 11px;
  }
  .chance-total {
    color: rgba(255,255,255,0.5);
    font-size: 11px;
    text-align: center;
    margin-top: 4px;
  }
  .chance-total.error {
    color: #F22C39;
  }
  .reset-btn {
    width: 100%;
    background: rgba(255,255,255,0.1);
    border: 1px solid rgba(255,255,255,0.2);
    border-radius: 6px;
    color: rgba(255,255,255,0.8);
    font-size: 12px;
    padding: 10px;
    font-family: monospace;
    cursor: pointer;
    margin-top: 4px;
    -webkit-tap-highlight-color: transparent;
    outline: none;
  }
  .reset-btn:active {
    background: rgba(255,255,255,0.2);
  }
  .divider {
    height: 1px;
    background: rgba(255,255,255,0.1);
    margin: 12px 0;
  }
  .stats-row {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 4px;
  }
  .stats-dot {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    flex-shrink: 0;
  }
  .stats-label {
    color: rgba(255,255,255,0.6);
    font-size: 11px;
    flex: 1;
  }
  .stats-value {
    color: rgba(255,255,255,0.9);
    font-size: 12px;
    font-weight: bold;
  }
  .visual-btn {
    width: 100%;
    background: rgba(255,255,255,0.08);
    border: 1px solid rgba(255,255,255,0.15);
    border-radius: 6px;
    color: rgba(255,255,255,0.6);
    font-size: 11px;
    padding: 8px;
    font-family: monospace;
    cursor: pointer;
    margin-top: 8px;
    -webkit-tap-highlight-color: transparent;
    outline: none;
  }
  .visual-btn.active {
    background: rgba(255,255,255,0.2);
    border-color: rgba(255,255,255,0.4);
    color: #fff;
  }
  .visual-btn:active {
    background: rgba(255,255,255,0.15);
  }
</style>
</head>
<body>

<canvas id="gameCanvas"></canvas>
<div id="consoleBtn">к</div>
<div id="consoleOverlay"></div>
<div id="consolePanel">
  <div class="console-close" onclick="closeConsole()">×</div>
  <div class="console-title">Консоль</div>
  
  <div class="console-row">
    <label class="console-label">Количество очков</label>
    <input type="number" class="console-input" id="scoreInput" placeholder="Введите число">
  </div>
  
  <div class="console-row">
    <label class="console-label">Шариков в секунду</label>
    <input type="number" class="console-input" id="freqInput" placeholder="Например: 1.5" min="0.1" step="0.1">
  </div>
  
  <div class="console-row">
    <label class="console-label">Размер шарика (пиксели)</label>
    <div class="size-row">
      <input type="number" class="console-input" id="widthInput" placeholder="Ширина" min="10">
      <input type="number" class="console-input" id="heightInput" placeholder="Высота" min="10">
    </div>
  </div>
  
  <div class="divider"></div>
  
  <div class="console-row">
    <label class="console-label">Шансы цветов (%)</label>
    <div class="chance-row">
      <div class="chance-dot" style="background:#F22C39"></div>
      <input type="number" class="chance-input" id="chance0" value="25" min="0" max="100">
      <span class="chance-label">Красный</span>
    </div>
    <div class="chance-row">
      <div class="chance-dot" style="background:#2ED38E"></div>
      <input type="number" class="chance-input" id="chance1" value="25" min="0" max="100">
      <span class="chance-label">Зелёный</span>
    </div>
    <div class="chance-row">
      <div class="chance-dot" style="background:#04CAE5"></div>
      <input type="number" class="chance-input" id="chance2" value="25" min="0" max="100">
      <span class="chance-label">Голубой</span>
    </div>
    <div class="chance-row">
      <div class="chance-dot" style="background:#CB53F2"></div>
      <input type="number" class="chance-input" id="chance3" value="25" min="0" max="100">
      <span class="chance-label">Фиолетовый</span>
    </div>
    <div class="chance-total" id="chanceTotal">Всего: 100%</div>
  </div>
  
  <div class="divider"></div>
  
  <div class="console-row">
    <label class="console-label">Цвет шариков</label>
    <div class="color-btns">
      <div class="color-btn" style="background:#F22C39" data-color="#F22C39" onclick="pickColor(this)"></div>
      <div class="color-btn" style="background:#2ED38E" data-color="#2ED38E" onclick="pickColor(this)"></div>
      <div class="color-btn" style="background:#04CAE5" data-color="#04CAE5" onclick="pickColor(this)"></div>
      <div class="color-btn" style="background:#CB53F2" data-color="#CB53F2" onclick="pickColor(this)"></div>
      <div class="color-btn random selected" data-color="random" onclick="pickColor(this)">🎲</div>
    </div>
  </div>
  
  <div class="divider"></div>
  
  <button class="reset-btn" onclick="resetToDefault()">Вернуть к стандарту</button>
  
  <div class="divider"></div>
  
  <div class="console-row">
    <label class="console-label">Статистика</label>
    <div class="stats-row">
      <div class="stats-dot" style="background:#F22C39"></div>
      <span class="stats-label">Красный</span>
      <span class="stats-value" id="stat0">0</span>
    </div>
    <div class="stats-row">
      <div class="stats-dot" style="background:#2ED38E"></div>
      <span class="stats-label">Зелёный</span>
      <span class="stats-value" id="stat1">0</span>
    </div>
    <div class="stats-row">
      <div class="stats-dot" style="background:#04CAE5"></div>
      <span class="stats-label">Голубой</span>
      <span class="stats-value" id="stat2">0</span>
    </div>
    <div class="stats-row">
      <div class="stats-dot" style="background:#CB53F2"></div>
      <span class="stats-label">Фиолетовый</span>
      <span class="stats-value" id="stat3">0</span>
    </div>
    <div class="stats-row">
      <div class="stats-dot" style="background:rgba(255,255,255,0.3)"></div>
      <span class="stats-label">Накручено</span>
      <span class="stats-value" id="statCheated">0</span>
    </div>
    <button class="visual-btn" id="visualBtn" onclick="toggleVisual()">Точный визуал</button>
  </div>
</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

let W, H;
function resize() {
  W = window.innerWidth;
  H = window.innerHeight;
  canvas.width = W;
  canvas.height = H;
}
resize();
window.addEventListener('resize', resize);

function rand(min, max) {
  return Math.random() * (max - min) + min;
}

function shade(hex, f) {
  const n = parseInt(hex.slice(1), 16);
  const r = Math.min(255, Math.round(((n >> 16) & 255) * f));
  const g = Math.min(255, Math.round(((n >> 8) & 255) * f));
  const b = Math.min(255, Math.round((n & 255) * f));
  return 'rgb(' + r + ',' + g + ',' + b + ')';
}

function formatScore(n) {
  if (n >= 1000000) return (Math.floor(n / 100000) / 10).toFixed(1).replace(/\.0$/, '') + 'м';
  if (n >= 1000) return (Math.floor(n / 100) / 10).toFixed(1).replace(/\.0$/, '') + 'к';
  return String(n);
}

// Стандартные значения
const DEFAULT_SPAWN_RATE = 1.5;
const DEFAULT_CHANCES = [25, 25, 25, 25];
const DEFAULT_BALLOON_W = 115;
const DEFAULT_BALLOON_H = 132;
const ALL_COLORS = ['#F22C39', '#2ED38E', '#04CAE5', '#CB53F2'];

let totalCount = 0;
let colorStats = [0, 0, 0, 0];
let cheatedCount = 0;
let exactVisual = false;

try {
  totalCount = parseInt(localStorage.getItem('jerry_totalCount') || '0', 10) || 0;
  const savedStats = localStorage.getItem('jerry_colorStats');
  if (savedStats) colorStats = JSON.parse(savedStats);
  cheatedCount = parseInt(localStorage.getItem('jerry_cheated') || '0', 10) || 0;
} catch (e) {}

let balloons = [];
let pops = [];
let lastSpawn = 0;
let spawnRate = DEFAULT_SPAWN_RATE;
let forcedColor = null;
let colorChances = [...DEFAULT_CHANCES];
let balloonW = DEFAULT_BALLOON_W;
let balloonH = DEFAULT_BALLOON_H;

function getSpawnInterval() {
  return 1000 / spawnRate;
}

function getRandomColor() {
  if (forcedColor) return forcedColor;
  const roll = Math.random() * 100;
  let sum = 0;
  for (let i = 0; i < ALL_COLORS.length; i++) {
    sum += colorChances[i];
    if (roll < sum) return ALL_COLORS[i];
  }
  return ALL_COLORS[ALL_COLORS.length - 1];
}

function getColorIndex(color) {
  return ALL_COLORS.indexOf(color);
}

function findFreeBaseX(w) {
  const padding = w / 2 + 6;
  const safeDist = w * 1.1;
  const attempts = 40;
  let bestX = null;
  let bestMinDist = -1;

  for (let a = 0; a < attempts; a++) {
    const candidateX = rand(padding, W - padding);
    let minDist = Infinity;

    for (const b of balloons) {
      const dx = Math.abs(candidateX - b.baseX);
      if (dx < minDist) minDist = dx;
    }

    if (minDist > bestMinDist) {
      bestMinDist = minDist;
      bestX = candidateX;
    }

    if (minDist >= safeDist) break;
  }

  return bestX !== null ? bestX : rand(padding, W - padding);
}

class Balloon {
  constructor() {
    this.w = balloonW;
    this.h = balloonH;
    this.baseX = findFreeBaseX(this.w);
    this.x = this.baseX;
    this.y = H + this.h;
    this.speed = rand(1.0, 1.5);
    this.sway = rand(0.4, 1.1);
    this.swayOffset = rand(0, Math.PI * 2);
    this.color = getRandomColor();
    this.t = 0;
  }

  update() {
    this.t += 0.02;
    this.y -= this.speed;
    this.x = this.baseX + Math.sin(this.t + this.swayOffset) * this.sway * 15;
  }

  draw() {
    const w = this.w;
    const h = this.h;

    ctx.save();
    ctx.translate(this.x, this.y);

    ctx.fillStyle = this.color;

    ctx.beginPath();
    ctx.ellipse(0, 0, w / 2, h / 2, 0, 0, Math.PI * 2);
    ctx.fill();

    const topHalf = w * 0.06;
    const botHalf = w * 0.15;
    const kh = w * 0.13;
    const bulge = w * 0.06;
    const base = h / 2 - w * 0.01;

    ctx.beginPath();
    ctx.moveTo(-topHalf, base);
    ctx.quadraticCurveTo(-topHalf - w * 0.02, base + kh * 0.6, -botHalf, base + kh);
    ctx.quadraticCurveTo(0, base + kh + bulge, botHalf, base + kh);
    ctx.quadraticCurveTo(topHalf + w * 0.02, base + kh * 0.6, topHalf, base);
    ctx.closePath();
    ctx.fill();

    ctx.strokeStyle = '#ffffff';
    ctx.lineWidth = w * 0.055;
    ctx.lineCap = 'round';
    ctx.beginPath();
    ctx.ellipse(0, 0, w * 0.36, h * 0.36, 0, Math.PI * 1.08, Math.PI * 1.42);
    ctx.stroke();

    ctx.restore();
  }

  isOffscreen() {
    return this.y < -this.h;
  }

  hitTest(px, py) {
    const dx = (px - this.x) / (this.w / 2);
    const dy = (py - this.y) / (this.h / 2);
    return dx * dx + dy * dy <= 1;
  }
}

class Pop {
  constructor(x, y, w, h, color) {
    this.x = x;
    this.y = y;
    this.color = color;
    this.dark = shade(color, 0.75);
    this.r = Math.max(w, h) / 2;
    this.t = 0;
    this.starLife = 7;

    this.spikes = [];
    const m = 10 + Math.floor(rand(0, 4));
    for (let i = 0; i < m; i++) {
      this.spikes.push({
        a: (i / m) * Math.PI * 2 + rand(-0.2, 0.2),
        len: rand(1.0, 1.7)
      });
    }

    this.shards = [];
    const n = 7 + Math.floor(rand(0, 3));
    for (let i = 0; i < n; i++) {
      this.shards.push({
        a: (i / n) * Math.PI * 2 + rand(-0.4, 0.4),
        dist: this.r * rand(0.2, 0.5),
        speed: rand(2.5, 6.5),
        rot: rand(0, Math.PI * 2),
        vrot: rand(-0.3, 0.3),
        size: this.r * rand(0.4, 0.7),
        life: 1
      });
    }
  }

  update() {
    this.t++;
    for (const s of this.shards) {
      s.dist += s.speed;
      s.speed *= 0.93;
      s.rot += s.vrot;
      s.life -= 0.055;
    }
  }

  get done() {
    return this.t > this.starLife && this.shards.every(s => s.life <= 0);
  }

  drawStar() {
    if (this.t > this.starLife) return;
    const k = this.t / this.starLife;
    ctx.save();
    ctx.translate(this.x, this.y);
    const sc = 0.75 + 0.45 * k;
    ctx.scale(sc, sc);
    ctx.globalAlpha = 1 - k * 0.5;
    ctx.fillStyle = '#8E96A6';
    ctx.beginPath();
    const R = this.r * 1.05;
    const step = Math.PI / this.spikes.length;
    for (let i = 0; i < this.spikes.length; i++) {
      const sp = this.spikes[i];
      const ox = Math.cos(sp.a) * R * sp.len;
      const oy = Math.sin(sp.a) * R * sp.len;
      const ia = sp.a + step;
      const ix = Math.cos(ia) * R * 0.38;
      const iy = Math.sin(ia) * R * 0.38;
      if (i === 0) ctx.moveTo(ox, oy);
      else ctx.lineTo(ox, oy);
      ctx.lineTo(ix, iy);
    }
    ctx.closePath();
    ctx.fill();
    ctx.restore();
    ctx.globalAlpha = 1;
  }

  drawShards() {
    for (const s of this.shards) {
      if (s.life <= 0) continue;
      const x = this.x + Math.cos(s.a) * s.dist;
      const y = this.y + Math.sin(s.a) * s.dist;
      const sz = s.size;
      ctx.save();
      ctx.translate(x, y);
      ctx.rotate(s.rot);
      ctx.globalAlpha = Math.max(s.life, 0);
      ctx.fillStyle = this.dark;
      ctx.beginPath();
      ctx.moveTo(-sz * 0.5, 0);
      ctx.quadraticCurveTo(0, -sz * 0.5, sz * 0.45, -sz * 0.3);
      ctx.quadraticCurveTo(sz * 0.3, sz * 0.05, sz * 0.2, sz * 0.45);
      ctx.quadraticCurveTo(-sz * 0.15, sz * 0.25, -sz * 0.5, 0);
      ctx.closePath();
      ctx.fill();
      ctx.restore();
    }
    ctx.globalAlpha = 1;
  }

  draw() {
    this.drawShards();
    this.drawStar();
  }
}

function popBalloon(b) {
  pops.push(new Pop(b.x, b.y, b.w, b.h, b.color));
  totalCount += 1;
  
  const idx = getColorIndex(b.color);
  if (idx >= 0) colorStats[idx]++;
  
  balloons = balloons.filter(item => item !== b);

  try { 
    localStorage.setItem('jerry_totalCount', String(totalCount)); 
    localStorage.setItem('jerry_colorStats', JSON.stringify(colorStats));
  } catch (e) {}
}

function handlePointer(e) {
  const rect = canvas.getBoundingClientRect();
  const px = e.clientX - rect.left;
  const py = e.clientY - rect.top;
  for (let i = balloons.length - 1; i >= 0; i--) {
    if (balloons[i].hitTest(px, py)) {
      popBalloon(balloons[i]);
      break;
    }
  }
}
canvas.addEventListener('pointerdown', handlePointer);

// Консоль
const consoleBtn = document.getElementById('consoleBtn');
const consolePanel = document.getElementById('consolePanel');
const consoleOverlay = document.getElementById('consoleOverlay');
const scoreInput = document.getElementById('scoreInput');
const freqInput = document.getElementById('freqInput');
const widthInput = document.getElementById('widthInput');
const heightInput = document.getElementById('heightInput');
const chanceInputs = [
  document.getElementById('chance0'),
  document.getElementById('chance1'),
  document.getElementById('chance2'),
  document.getElementById('chance3')
];
const chanceTotalEl = document.getElementById('chanceTotal');
const statEls = [
  document.getElementById('stat0'),
  document.getElementById('stat1'),
  document.getElementById('stat2'),
  document.getElementById('stat3')
];
const statCheatedEl = document.getElementById('statCheated');
const visualBtn = document.getElementById('visualBtn');

consoleBtn.addEventListener('click', (e) => {
  e.stopPropagation();
  openConsole();
});

consoleOverlay.addEventListener('click', closeConsole);

function updateStats() {
  for (let i = 0; i < 4; i++) {
    statEls[i].textContent = colorStats[i];
  }
  statCheatedEl.textContent = cheatedCount;
}

function openConsole() {
  scoreInput.value = totalCount;
  freqInput.value = spawnRate;
  widthInput.value = balloonW;
  heightInput.value = balloonH;
  for (let i = 0; i < 4; i++) {
    chanceInputs[i].value = colorChances[i];
    chanceInputs[i].classList.remove('error');
  }
  updateChanceTotal();
  updateStats();
  consolePanel.classList.add('active');
  consoleOverlay.classList.add('active');
}

function closeConsole() {
  consolePanel.classList.remove('active');
  consoleOverlay.classList.remove('active');
}

scoreInput.addEventListener('change', () => {
  const val = parseInt(scoreInput.value, 10);
  if (!isNaN(val) && val >= 0) {
    const oldTotal = totalCount;
    totalCount = val;
    const diff = totalCount - oldTotal;
        if (diff > 0) {
         cheatedCount += diff;
      try { localStorage.setItem('jerry_cheated', String(cheatedCount)); } catch (e) {}
    }
    try { localStorage.setItem('jerry_totalCount', String(totalCount)); } catch (e) {}
    updateStats();
  }
});

freqInput.addEventListener('change', () => {
  let val = parseFloat(freqInput.value);
  if (isNaN(val)) return;
  if (val < 0.1) val = 0.1;
  spawnRate = val;
  freqInput.value = val;
});

widthInput.addEventListener('change', () => {
  const val = parseInt(widthInput.value, 10);
  if (!isNaN(val) && val >= 10) {
    balloonW = val;
  }
});

heightInput.addEventListener('change', () => {
  const val = parseInt(heightInput.value, 10);
  if (!isNaN(val) && val >= 10) {
    balloonH = val;
  }
});

function validateChances() {
  let total = 0;
  let valid = true;
  const values = [];
  
  for (let i = 0; i < 4; i++) {
    const val = parseFloat(chanceInputs[i].value);
    if (isNaN(val) || val < 0) {
      chanceInputs[i].classList.add('error');
      valid = false;
    } else {
      chanceInputs[i].classList.remove('error');
      values.push(val);
      total += val;
    }
  }
  
  if (valid) {
    if (total !== 100) {
      chanceTotalEl.textContent = 'Ошибка: всего ' + total + '%, нужно ровно 100%';
      chanceTotalEl.classList.add('error');
      for (let i = 0; i < 4; i++) chanceInputs[i].classList.add('error');
      return null;
    } else {
      chanceTotalEl.textContent = 'Всего: 100%';
      chanceTotalEl.classList.remove('error');
      return values;
    }
  } else {
    chanceTotalEl.textContent = 'Ошибка: введите корректные числа';
    chanceTotalEl.classList.add('error');
    return null;
  }
}

function updateChanceTotal() {
  let total = 0;
  let hasError = false;
  for (let i = 0; i < 4; i++) {
    const val = parseFloat(chanceInputs[i].value);
    if (isNaN(val) || val < 0) hasError = true;
    else total += val;
  }
  if (hasError) {
    chanceTotalEl.textContent = 'Ошибка: введите корректные числа';
    chanceTotalEl.classList.add('error');
  } else if (total !== 100) {
    chanceTotalEl.textContent = 'Всего: ' + total + '% (нужно 100%)';
    chanceTotalEl.classList.add('error');
  } else {
    chanceTotalEl.textContent = 'Всего: 100%';
    chanceTotalEl.classList.remove('error');
  }
}

for (let i = 0; i < 4; i++) {
  chanceInputs[i].addEventListener('input', updateChanceTotal);
  chanceInputs[i].addEventListener('change', () => {
    const values = validateChances();
    if (values) {
      colorChances = values;
          }
  });
}

function pickColor(el) {
  document.querySelectorAll('.color-btn').forEach(b => b.classList.remove('selected'));
  el.classList.add('selected');
  const color = el.getAttribute('data-color');
  if (color === 'random') {
    forcedColor = null;
  } else {
    forcedColor = color;
  }
}

function toggleVisual() {
  exactVisual = !exactVisual;
  visualBtn.classList.toggle('active', exactVisual);
  visualBtn.textContent = exactVisual ? 'Точный визуал: ВКЛ' : 'Точный визуал';
}

function resetToDefault() {
  spawnRate = DEFAULT_SPAWN_RATE;
  colorChances = [...DEFAULT_CHANCES];
  forcedColor = null;
  balloonW = DEFAULT_BALLOON_W;
  balloonH = DEFAULT_BALLOON_H;
  
  scoreInput.value = totalCount;
  freqInput.value = spawnRate;
  widthInput.value = balloonW;
  heightInput.value = balloonH;
  for (let i = 0; i < 4; i++) {
    chanceInputs[i].value = colorChances[i];
    chanceInputs[i].classList.remove('error');
  }
  updateChanceTotal();
  
  document.querySelectorAll('.color-btn').forEach(b => b.classList.remove('selected'));
  document.querySelector('.color-btn.random').classList.add('selected');
}

function loop(ts) {
  ctx.clearRect(0, 0, W, H);

  if (ts - lastSpawn > getSpawnInterval()) {
    balloons.push(new Balloon());
    lastSpawn = ts;
  }

  for (let i = balloons.length - 1; i >= 0; i--) {
    const b = balloons[i];
    b.update();
    if (b.isOffscreen()) balloons.splice(i, 1);
  }

  for (let i = 0; i < balloons.length; i++) {
    balloons[i].draw();
  }

  for (let i = pops.length - 1; i >= 0; i--) {
    const p = pops[i];
    p.update();
    p.draw();
    if (p.done) pops.splice(i, 1);
  }

  ctx.fillStyle = 'rgba(255,255,255,0.85)';
  ctx.font = 'bold 14px monospace';
  ctx.textAlign = 'left';
  ctx.textBaseline = 'top';
  ctx.fillText(exactVisual ? String(totalCount) : formatScore(totalCount), 8, 6);

  requestAnimationFrame(loop);
}
requestAnimationFrame(loop);
</script>

</body>
</html>
