<template>
  <div class="log-sense-container">
    <!-- VISUAL NOISE LAYERS (背景噪音层) -->
    <div class="noise-overlay"></div>
    <div class="vignette-overlay"></div>
    <div class="scanlines"></div>

    <!-- MAIN CONTENT -->
    <div class="content-wrapper">

      <!-- 1. VISUAL CENTERPIECE: Wireframe Data Globe -->
      <!-- 动态绑定 'analyzing' 类来触发故障/快速旋转动画 -->
      <div class="brain-container" :class="{ analyzing: isAnalyzing }">
        <svg class="wireframe-svg" viewBox="0 0 100 100" overflow="visible">
          <!-- Core Processor -->
          <circle cx="50" cy="50" r="5" class="core" fill="white" />

          <!-- Orbit 1: Vertical (Longitude-ish) -->
          <ellipse cx="50" cy="50" rx="40" ry="10" class="orbital-ring ring-1 sketchy" />
          <ellipse cx="50" cy="50" rx="40" ry="15" class="orbital-ring ring-1" stroke-opacity="0.3" transform="rotate(90 50 50)" />

          <!-- Orbit 2: Tilted (Latitude-ish) -->
          <ellipse cx="50" cy="50" rx="45" ry="12" class="orbital-ring ring-2 sketchy" />

          <!-- Orbit 3: Wide Outer -->
          <ellipse cx="50" cy="50" rx="48" ry="48" class="orbital-ring ring-3 sketchy" stroke-dasharray="2 10" stroke-opacity="0.5" />
        </svg>
      </div>

      <!-- 2. TITLE SECTION -->
      <header class="header-section">
        <h1 class="main-title flicker-text">LogSense-AI</h1>
        <div class="subtitle">Heuristic Data Processing Unit</div>
      </header>

      <!-- 3. LOG INPUT TERMINAL -->
      <div class="terminal-wrapper">
        <div class="terminal-inner">

          <div class="input-area">
            <textarea
                v-model="logInput"
                placeholder="// Paste raw system logs here for analysis..."
                :disabled="isAnalyzing"
            ></textarea>
            <button
                class="btn-analyze"
                @click="analyzeLogs"
                :disabled="isAnalyzing"
            >
              {{ isAnalyzing ? 'PROC...' : 'ANALYZE' }}
            </button>
          </div>

          <!-- Log Display Window -->
          <div class="log-display" ref="logDisplayRef">
            <div
                v-for="(log, index) in logs"
                :key="index"
                class="log-entry"
                :class="log.type"
            >
              <span class="timestamp">{{ log.timestamp }}</span>
              <span>{{ log.message }}</span>
            </div>
          </div>

        </div>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue';

// --- State ---
const logInput = ref('');
const isAnalyzing = ref(false);
const logDisplayRef = ref(null);

// 初始日志数据
const logs = ref([
  { timestamp: '[SYS_INIT]', message: 'Logic Core Online. Awaiting input stream...', type: 'info' }
]);

// --- Methods ---

const addLogEntry = async (timestamp, message, type = '') => {
  logs.value.push({ timestamp, message, type });

  // 等待 DOM 更新后自动滚动到底部
  await nextTick();
  if (logDisplayRef.value) {
    logDisplayRef.value.scrollTop = logDisplayRef.value.scrollHeight;
  }
};

const analyzeLogs = () => {
  const inputVal = logInput.value.trim();

  if (!inputVal) {
    addLogEntry("[ERROR]", "No input detected. The void stares back.", "error");
    return;
  }

  // 开启视觉故障效果
  isAnalyzing.value = true;
  addLogEntry("[SYS]", "Parsing chaotic data stream...", "");

  // 模拟处理延迟
  setTimeout(() => {
    // 随机生成的“思维阁”风格回复
    const responses = [
      "Pattern detected: Anomalous recursion in sector 7.",
      "Sentiment analysis: The machine spirit is restless.",
      "CRITICAL: Memory leak resembles organic decay.",
      "Syntax validated, but the meaning is obscured by static.",
      "Optimizing heuristic pathways... Logic success.",
      "Trace complete. The logs whisper of unauthorized access."
    ];

    const randomResponse = responses[Math.floor(Math.random() * responses.length)];

    // 根据关键词决定样式
    let style = "success";
    if (randomResponse.includes("CRITICAL") || randomResponse.includes("decay")) {
      style = "error";
    }

    addLogEntry("[RESULT]", randomResponse, style);

    // 重置状态
    isAnalyzing.value = false;

  }, 1500); // 1.5s 思考时间
};
</script>

<style>
/*
  注意：为了确保背景和全局字体样式正确覆盖整个页面，
  这里没有使用 scoped。如果这影响了你项目中的其他组件，
  请将 body/html 样式移动到全局 css 文件中，或添加 scoped 并使用 :deep()。
*/

/* 引入字体 */
@import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700;900&family=Space+Mono:ital,wght@0,400;0,700;1,400&display=swap');

:root {
  --bg-color: #050505;
  --ink-white: #e0e0e0;
  --faded-white: #7a7a7a;
  --alert-red: #ff3333;
  --terminal-gold: #cfa863;
  --noise-opacity: 0.14;
  --border-style: 1px solid rgba(220, 220, 220, 0.25);
}

/* 确保容器填满屏幕 */
.log-sense-container {
  background-color: var(--bg-color);
  color: var(--ink-white);
  font-family: 'Space Mono', monospace;
  min-height: 100vh;
  width: 100%;
  position: relative;
  overflow-x: hidden;
}

* {
  box-sizing: border-box;
}

/* --- 2. HEAVY NOISE & TEXTURE --- */

/* Static Noise Overlay */
.noise-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 5;
  opacity: var(--noise-opacity);
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)' opacity='1'/%3E%3C/svg%3E");
}

/* Vignette */
.vignette-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 6;
  background: radial-gradient(circle at center, transparent 30%, #000000 110%);
}

/* Scanlines */
.scanlines {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 7;
  background: linear-gradient(
      to bottom,
      rgba(255,255,255,0),
      rgba(255,255,255,0) 50%,
      rgba(0,0,0,0.2) 50%,
      rgba(0,0,0,0.2)
  );
  background-size: 100% 4px;
}

/* --- LAYOUT --- */

.content-wrapper {
  position: relative;
  z-index: 10;
  max-width: 800px;
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding: 40px 20px;
}

.header-section {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* --- 1. THE WIREFRAME SPHERE --- */
.brain-container {
  width: 200px;
  height: 200px;
  margin-bottom: 20px;
  position: relative;
  transform-style: preserve-3d;
  perspective: 800px;
}

.wireframe-svg {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  animation: breathe 4s ease-in-out infinite;
}

.orbital-ring {
  fill: none;
  stroke: rgba(255, 255, 255, 0.6);
  stroke-width: 1;
  transform-origin: center;
  vector-effect: non-scaling-stroke;
}

.sketchy {
  stroke-dasharray: 10, 5;
  stroke: rgba(255, 255, 255, 0.4);
}

/* Animations */
.ring-1 { animation: spin3D 15s linear infinite; }
.ring-2 { animation: spin3D-rev 12s linear infinite; transform: rotate(45deg); }
.ring-3 { animation: spin3D 20s linear infinite; transform: rotate(-45deg); }
.core   { animation: pulse-core 2s ease-in-out infinite; fill: var(--ink-white); opacity: 0.8; }

/* Glitch State */
.brain-container.analyzing .orbital-ring {
  animation-duration: 0.2s; /* HYPER SPEED */
  stroke: var(--terminal-gold);
  stroke-width: 2;
}

.brain-container.analyzing {
  animation: shake 0.5s infinite;
}

/* --- TYPOGRAPHY --- */
h1.main-title {
  font-family: 'Cinzel', serif;
  font-size: 3rem;
  text-transform: uppercase;
  letter-spacing: 0.2em;
  color: var(--ink-white);
  margin: 0 0 10px 0;
  text-align: center;
  position: relative;
  text-shadow:
      2px 2px 4px rgba(0,0,0,0.8),
      0 0 10px rgba(255,255,255,0.1);
  border-bottom: 2px solid var(--ink-white);
  padding-bottom: 10px;
  width: 100%;
}

h1.main-title::after {
  content: '';
  position: absolute;
  bottom: -6px;
  left: 0;
  width: 100%;
  height: 1px;
  background: var(--faded-white);
}

.subtitle {
  font-family: 'Space Mono', monospace;
  font-size: 0.8rem;
  color: var(--faded-white);
  text-transform: uppercase;
  letter-spacing: 0.1em;
  margin-bottom: 40px;
}

/* --- TERMINAL SECTION --- */
.terminal-wrapper {
  width: 100%;
  border: 1px solid rgba(255,255,255,0.3);
  background: rgba(10, 10, 10, 0.8);
  box-shadow:
      0 0 0 4px rgba(0,0,0,0.5),
      0 0 20px rgba(0,0,0,1);
  padding: 2px;
  position: relative;
}

.terminal-inner {
  border: 1px solid rgba(255,255,255,0.1);
  padding: 20px;
  min-height: 400px;
  display: flex;
  flex-direction: column;
}

.input-area {
  display: flex;
  border-bottom: 1px dashed rgba(255,255,255,0.2);
  padding-bottom: 15px;
  margin-bottom: 15px;
}

textarea {
  width: 100%;
  background: transparent;
  border: none;
  color: var(--terminal-gold);
  font-family: 'Space Mono', monospace;
  font-size: 0.9rem;
  resize: none;
  outline: none;
  height: 80px;
  line-height: 1.4;
}

textarea::placeholder {
  color: rgba(207, 168, 99, 0.3);
  font-style: italic;
}

textarea:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-analyze {
  background: transparent;
  border: 1px solid var(--ink-white);
  color: var(--ink-white);
  font-family: 'Cinzel', serif;
  font-weight: 700;
  padding: 0 30px;
  cursor: pointer;
  text-transform: uppercase;
  transition: all 0.2s ease;
  margin-left: 10px;
  letter-spacing: 2px;
}

.btn-analyze:hover:not(:disabled) {
  background: var(--ink-white);
  color: black;
  box-shadow: 0 0 15px rgba(255,255,255,0.4);
}

.btn-analyze:disabled {
  opacity: 0.5;
  cursor: wait;
}

.log-display {
  flex-grow: 1;
  font-size: 0.85rem;
  color: var(--faded-white);
  overflow-y: auto;
  height: 300px;
  scrollbar-width: thin;
  scrollbar-color: var(--faded-white) transparent;
}

.log-display::-webkit-scrollbar { width: 6px; }
.log-display::-webkit-scrollbar-thumb { background: var(--faded-white); }
.log-display::-webkit-scrollbar-track { background: transparent; }

.log-entry {
  margin-bottom: 8px;
  display: flex;
  opacity: 0;
  animation: fadeIn 0.3s forwards;
  border-left: 1px solid transparent;
  padding-left: 10px;
}

.log-entry.error { border-left-color: var(--alert-red); color: #ff8888; }
.log-entry.success { border-left-color: var(--terminal-gold); color: var(--terminal-gold); }
.log-entry .timestamp { margin-right: 15px; opacity: 0.5; font-size: 0.75rem; }

/* --- KEYFRAMES --- */

@keyframes breathe {
  0%, 100% { opacity: 0.7; transform: scale(1); }
  50% { opacity: 1; transform: scale(1.02); }
}

@keyframes pulse-core {
  0%, 100% { r: 5; opacity: 0.5; }
  50% { r: 7; opacity: 1; box-shadow: 0 0 10px white; }
}

@keyframes spin3D {
  0% { transform: rotateX(70deg) rotateZ(0deg); }
  100% { transform: rotateX(70deg) rotateZ(360deg); }
}

@keyframes spin3D-rev {
  0% { transform: rotateY(60deg) rotateZ(0deg); }
  100% { transform: rotateY(60deg) rotateZ(-360deg); }
}

@keyframes shake {
  0% { transform: translate(1px, 1px) rotate(0deg); }
  10% { transform: translate(-1px, -2px) rotate(-1deg); }
  20% { transform: translate(-3px, 0px) rotate(1deg); }
  30% { transform: translate(3px, 2px) rotate(0deg); }
  40% { transform: translate(1px, -1px) rotate(1deg); }
  50% { transform: translate(-1px, 2px) rotate(-1deg); }
  60% { transform: translate(-3px, 1px) rotate(0deg); }
  70% { transform: translate(3px, 1px) rotate(-1deg); }
  80% { transform: translate(-1px, -1px) rotate(1deg); }
  90% { transform: translate(1px, 2px) rotate(0deg); }
  100% { transform: translate(1px, -2px) rotate(-1deg); }
}

@keyframes fadeIn {
  to { opacity: 1; }
}

.flicker-text {
  animation: textFlicker 4s infinite;
}

@keyframes textFlicker {
  0% { opacity: 0.9; }
  3% { opacity: 0.4; }
  6% { opacity: 0.9; }
  7% { opacity: 0.4; }
  8% { opacity: 0.9; }
  9% { opacity: 1; }
  100% { opacity: 0.9; }
}
</style>