# Sheep Match — 技术架构文档（ARCHITECTURE.md）
**版本**: v1.0 | **日期**: 2026-03-29

---

## 1. 技术选型

### 1.1 核心原则

- **零依赖**：纯原生 HTML + CSS + JavaScript，无框架、无构建工具
- **单文件部署**：所有代码内联到 `index.html`，一个文件搞定一切
- **CDN外部依赖最小化**：仅加载 Google Fonts（可选）和 AdMob SDK
- **离线友好**：Service Worker 实现基本离线能力

### 1.2 技术栈

| 层级 | 技术 | 备注 |
|------|------|------|
| 结构 | HTML5 | 单文件 `index.html` |
| 样式 | CSS3 | 内联 `<style>` 标签，CSS 动画 |
| 逻辑 | Vanilla JS (ES6+) | 无框架，原生 DOM API |
| 渲染 | Canvas 2D | 主渲染，emoji 绘制 |
| 音频 | Web Audio API | 音效引擎 |
| 存储 | localStorage | 存档、设置、金币 |
| 广告 | Google AdMob | 激励视频 + 插屏 |
| 部署 | GitHub Pages | 免费静态托管 |
| 域名 | 可选 | 建议中东域名 `.com` 或 `.ae` |

### 1.3 性能目标

| 指标 | 目标值 |
|------|--------|
| 首屏加载 | < 2s（3G网络） |
| 交互响应 | < 100ms |
| 帧率 | 60fps（Canvas渲染） |
| 内存占用 | < 50MB |
| 包体积 | < 500KB（不含广告SDK） |

---

## 2. 项目文件结构

```
sheep-match/
├── index.html          # 唯一入口，所有代码内联
├── manifest.json       # PWA清单（可选）
├── sw.js               # Service Worker（可选）
├── README.md           # 部署说明
└── assets/             # （无assets目录，emoji即资源）
```

### 2.1 index.html 结构大纲

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Sheep Match</title>
  <meta name="description" content="The ultimate puzzle challenge! Match pairs to win.">
  <style>/* 所有CSS内联 */</style>
</head>
<body>
  <!-- 游戏挂载点 -->
  <div id="app"></div>

  <script>
  /* =========================================
     SHEEP MATCH - 单文件游戏引擎
     所有逻辑内联，无需外部资源
  ========================================= */
  (function() {
    'use strict';

    // ===== 常量 =====
    // ===== 游戏状态 =====
    // ===== 渲染器（Canvas） =====
    // ===== 音效引擎 =====
    // ===== 游戏逻辑 =====
    // ===== UI 控制器 =====
    // ===== 广告集成 =====
    // ===== 社交分享 =====
    // ===== 数据持久化 =====
    // ===== 初始化 =====
  })();
  </script>

  <!-- AdMob SDK（延迟加载） -->
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-app-pub-xxxxx"></script>
</body>
</html>
```

---

## 3. Canvas 渲染方案

### 3.1 Canvas 初始化

```javascript
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');

// 适配设备像素比（Retina屏）
const dpr = window.devicePixelRatio || 1;
canvas.width  = screenWidth  * dpr;
canvas.height = screenHeight * dpr;
canvas.style.width  = screenWidth  + 'px';
canvas.style.height = screenHeight + 'px';
ctx.scale(dpr, dpr);
document.getElementById('app').appendChild(canvas);
```

### 3.2 方块渲染函数

```javascript
function drawBlock(ctx, block, size) {
  const { x, y } = getBlockPosition(block.row, block.col, size);

  // 背景
  ctx.fillStyle = block.color;
  ctx.beginPath();
  roundRect(ctx, x, y, size.w, size.h, 8);
  ctx.fill();

  // 阴影
  ctx.shadowColor = 'rgba(0,0,0,0.3)';
  ctx.shadowBlur = 6;
  ctx.shadowOffsetY = 3;

  // Emoji 文字
  ctx.font = `${size.h * 0.55}px sans-serif`;
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(block.emoji, x + size.w/2, y + size.h/2);

  // 清除阴影
  ctx.shadowColor = 'transparent';

  // 遮挡遮罩（被遮挡方块半透明）
  if (!block.removable) {
    ctx.fillStyle = 'rgba(0,0,0,0.35)';
    ctx.fill();
  }
}

function roundRect(ctx, x, y, w, h, r) {
  ctx.moveTo(x + r, y);
  ctx.lineTo(x + w - r, y);
  ctx.quadraticCurveTo(x + w, y, x + w, y + r);
  ctx.lineTo(x + w, y + h - r);
  ctx.quadraticCurveTo(x + w, y + h, x + w - r, y + h);
  ctx.lineTo(x + r, y + h);
  ctx.quadraticCurveTo(x, y + h, x, y + h - r);
  ctx.lineTo(x, y + r);
  ctx.quadraticCurveTo(x, y, x + r, y);
  ctx.closePath();
}
```

### 3.3 渲染循环（60fps）

```javascript
let lastFrameTime = 0;
const TARGET_FPS = 60;
const FRAME_INTERVAL = 1000 / TARGET_FPS;

function gameLoop(timestamp) {
  const delta = timestamp - lastFrameTime;

  if (delta >= FRAME_INTERVAL) {
    lastFrameTime = timestamp - (delta % FRAME_INTERVAL);

    // 更新动画
    updateAnimations(delta);

    // 清空画布
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // 绘制底层方块
    drawLayer(blocks.filter(b => b.layer === 0));

    // 绘制顶层方块
    drawLayer(blocks.filter(b => b.layer === 1));

    // 绘制特效
    drawEffects();

    // 绘制UI叠加层
    drawOverlay();
  }

  requestAnimationFrame(gameLoop);
}

requestAnimationFrame(gameLoop);
```

---

## 4. 游戏状态机

### 4.1 状态定义

```javascript
const GameState = Object.freeze({
  MENU:     'menu',      // 主菜单
  LEVEL_SELECT: 'level_select', // 关卡选择
  PLAYING:  'playing',   // 游戏中
  PAUSED:   'paused',   // 暂停
  WIN:      'win',      // 通关
  LOSE:     'lose',     // 失败
  RESULT:   'result',   // 结果/分享页
  SETTINGS: 'settings'  // 设置
});

let currentState = GameState.MENU;
```

### 4.2 状态转换图

```
[MENU] ──点击开始──> [LEVEL_SELECT]
                           │
                    选择关卡 ──┐
                                ▼
                          [PLAYING] ──消除全部──> [WIN]
                           │  │  │
                    暂停    │  │  └──步数归零──> [LOSE]
                     ▼      │  │
                  [PAUSED]───┘  │
                     │         │
                     └──回来───┘
                                │
                          [WIN/LOSE] ──分享──> [RESULT]
                                │       │
                           再来一局   下一关
                                │       │
                         [PLAYING] <──┴──[PLAYING]
```

### 4.3 状态管理器

```javascript
class StateManager {
  constructor() {
    this.state = GameState.MENU;
    this.data = {}; // 状态间传递的数据
  }

  transition(newState, data = {}) {
    const prev = this.state;
    this.state = newState;
    this.data = data;
    this.onExit(prev);
    this.onEnter(newState);
    saveGameState(); // 自动存档
  }

  onEnter(state) {
    switch(state) {
      case GameState.MENU:
        showMenuScreen();
        playBGM('menu');
        break;
      case GameState.PLAYING:
        startLevel(this.data.level);
        playBGM('game');
        break;
      case GameState.WIN:
        showWinScreen();
        playSFX('win');
        break;
      case GameState.LOSE:
        showLoseScreen();
        playSFX('lose');
        break;
    }
  }
}
```

---

## 5. 数据存储（localStorage）

### 5.1 数据 Schema

```javascript
const STORAGE_KEYS = {
  PROGRESS:  'sm_progress',   // 游戏进度
  SETTINGS:  'sm_settings',   // 设置
  COINS:     'sm_coins',      // 金币数量
  ITEMS:     'sm_items',      // 道具数量
  STATS:     'sm_stats',      // 统计
  ACHIEVEMENTS: 'sm_achieve', // 成就
  DAILY:     'sm_daily'       // 每日数据
};

// 存档数据结构
const saveData = {
  version: '1.0.0',
  unlockedLevel: 5,        // 已解锁最高关卡
  starsPerLevel: {          // 每关星级
    1: 3, 2: 2, 3: 3, 4: 1
  },
  coins: 350,
  items: {
    undo: 2,
    shuffle: 1,
    revive: 0
  },
  stats: {
    totalPlays: 142,
    totalWins: 28,
    totalLosses: 114,
    totalTimePlayed: 7200 // 秒
  },
  achievements: ['first_win', 'level_10'],
  dailyLogin: {
    lastLoginDate: '2026-03-29',
    streak: 5
  },
  settings: {
    sound: true,
    music: true,
    vibration: true,
    language: 'en'
  }
};
```

### 5.2 存储操作封装

```javascript
const Storage = {
  save(key, data) {
    try {
      localStorage.setItem(key, JSON.stringify({
        ...data,
        _savedAt: Date.now()
      }));
    } catch(e) {
      console.warn('Storage save failed:', e);
    }
  },

  load(key, fallback = null) {
    try {
      const raw = localStorage.getItem(key);
      return raw ? JSON.parse(raw) : fallback;
    } catch(e) {
      return fallback;
    }
  },

  remove(key) {
    localStorage.removeItem(key);
  },

  saveGameState() {
    this.save(STORAGE_KEYS.PROGRESS, {
      version: '1.0.0',
      unlockedLevel,
      starsPerLevel,
      coins,
      items,
      stats,
      achievements,
      dailyLogin,
      settings
    });
  },

  loadGameState() {
    return this.load(STORAGE_KEYS.PROGRESS, DEFAULT_SAVE);
  }
};
```

---

## 6. 音效方案（Web Audio API）

### 6.1 音效引擎

```javascript
class AudioEngine {
  constructor() {
    this.ctx = new (window.AudioContext || window.webkitAudioContext)();
    this.sounds = {};
    this.bgm = null;
    this.volume = { sfx: 0.7, bgm: 0.4 };
  }

  // 生成音效（不需要外部文件）
  generateTone(freq, duration, type = 'sine', volume = 0.5) {
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();

    osc.type = type;
    osc.frequency.setValueAtTime(freq, this.ctx.currentTime);

    gain.gain.setValueAtTime(volume, this.ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);

    osc.connect(gain);
    gain.connect(this.ctx.destination);

    osc.start();
    osc.stop(this.ctx.currentTime + duration);
  }

  playMatch() {
    this.generateTone(523, 0.1, 'sine', 0.4);  // C5
    setTimeout(() => this.generateTone(659, 0.1, 'sine', 0.4), 80);  // E5
    setTimeout(() => this.generateTone(784, 0.15, 'sine', 0.4), 160);  // G5
  }

  playFail() {
    this.generateTone(400, 0.2, 'sawtooth', 0.3);
    setTimeout(() => this.generateTone(300, 0.3, 'sawtooth', 0.3), 200);
  }

  playWin() {
    const notes = [523, 659, 784, 1047];  // C5 E5 G5 C6
    notes.forEach((f, i) => {
      setTimeout(() => this.generateTone(f, 0.2, 'sine', 0.5), i * 150);
    });
  }

  playClick() {
    this.generateTone(800, 0.05, 'square', 0.2);
  }

  playBGM(name) {
    // 简单循环BGM（使用低频方波+白噪声混音）
    // 实际项目中建议加载外部MP3
    if (this.bgm) this.bgm.stop();
    // ... BGM播放逻辑
  }

  resume() {
    if (this.ctx.state === 'suspended') this.ctx.resume();
  }
}

const audio = new AudioEngine();
```

### 6.2 音效配置表

| 音效 | 触发时机 | 频率/时长 | 类型 |
|------|----------|-----------|------|
| match | 成功消除一对方块 | 523→659→784 Hz | Sine |
| fail | 步数耗尽 | 400→300 Hz | Sawtooth |
| win | 通关 | 523→659→784→1047 Hz | Sine |
| click | 点击方块 | 800 Hz, 50ms | Square |
| item_use | 使用道具 | 440→550→660 Hz | Sine |
| button | 按钮点击 | 600 Hz, 30ms | Sine |

---

## 7. Emoji 渲染（无需外部图片CDN）

### 7.1 Emoji 池

```javascript
const BLOCK_TYPES = [
  { id: 'B01', emoji: '🐪', name: 'Camel',    color: '#C19A6B' },
  { id: 'B02', emoji: '🦅', name: 'Falcon',   color: '#4A7DB8' },
  { id: 'B03', emoji: '💎', name: 'Diamond',  color: '#00D4FF' },
  { id: 'B04', emoji: '🌴', name: 'Palm',     color: '#2ECC71' },
  { id: 'B05', emoji: '⛽', name: 'OilRig',  color: '#2C3E50' },
  { id: 'B06', emoji: '🏺', name: 'Jar',      color: '#8E44AD' },
  { id: 'B07', emoji: '👑', name: 'Crown',    color: '#F1C40F' }
];
```

### 7.2 跨平台 Emoji 兼容

```javascript
// 使用 Canvas 绘制 Emoji 时的字号参考
function getEmojiFontSize(blockHeight) {
  const ua = navigator.userAgent;
  // iOS emoji 偏大，Android 偏小
  if (/iPhone|iPad/i.test(ua)) return blockHeight * 0.60;
  if (/Android/i.test(ua)) return blockHeight * 0.52;
  return blockHeight * 0.55;
}

// 备选：使用 Canvas fillText 绘制 emoji（兼容性最佳）
ctx.font = `${Math.floor(emojiSize)}px "Apple Color Emoji","Segoe UI Emoji","Noto Color Emoji",sans-serif`;
ctx.fillText(block.emoji, x + w/2, y + h/2);
```

---

## 8. 遮挡检测算法

### 8.1 核心算法

```javascript
// 判断某个方块是否被遮挡
function isBlocked(block, allBlocks) {
  // 检查同一层是否有其他方块遮挡
  const sameLayer = allBlocks.filter(b => b.layer === block.layer);

  for (const other of sameLayer) {
    if (other.id === block.id) continue;

    // 检查 other 是否"压住" block
    // 条件：other 在 block 的上下左右方向上有重叠
    const overlapH = Math.abs(block.col - other.col) < 2 &&
                     Math.abs(block.row - other.row) < 2;

    if (overlapH && isAdjacent(block, other)) {
      return true;
    }
  }

  // 检查上一层是否有方块压在 block 上
  const upperLayer = allBlocks.filter(b => b.layer === block.layer + 1);
  for (const upper of upperLayer) {
    if (isAbove(upper, block)) return true;
  }

  return false;
}

function isAbove(upper, lower) {
  // upper 的中心在 lower 的投影范围内
  const upperRowCenter = upper.row;
  const upperColCenter = upper.col;
  return Math.abs(upperRowCenter - lower.row) < 1.5 &&
         Math.abs(upperColCenter - lower.col) < 1.5;
}

function isAdjacent(a, b) {
  const rowDiff = Math.abs(a.row - b.row);
  const colDiff = Math.abs(a.col - b.col);
  return (rowDiff <= 1 && colDiff <= 1) && (rowDiff + colDiff > 0);
}

// 更新所有方块的可点击状态
function updateRemovability() {
  for (const block of allBlocks) {
    block.removable = !isBlocked(block, allBlocks);
  }
}
```

### 8.2 简化版（实用）

```javascript
// 更简洁的遮挡判断
function canRemove(block, blocks) {
  // 边界方块（row=0或col=0或row=max或col=max）总是可点击
  if (block.row === 0 || block.col === 0 ||
      block.row === MAX_ROW || block.col === MAX_COL) {
    return true;
  }

  // 同一层检查：上下左右任一方向无邻居
  const neighbors = blocks.filter(b =>
    b.layer === block.layer &&
    Math.abs(b.row - block.row) + Math.abs(b.col - block.col) === 1
  );

  if (neighbors.length < 4) return true; // 至少有一边是空的

  // 上一层检查
  const upperBlocks = blocks.filter(b => b.layer === block.layer + 1);
  const blockingAbove = upperBlocks.some(b =>
    Math.abs(b.row - block.row) <= 0.5 &&
    Math.abs(b.col - block.col) <= 0.5
  );

  return !blockingAbove;
}
```

---

## 9. 部署方案（GitHub Pages）

### 9.1 部署步骤

```bash
# 1. 创建仓库
git init
git add index.html README.md manifest.json sw.js
git commit -m "Sheep Match v1.0"

# 2. 推送到 GitHub
git remote add origin https://github.com/YOUR_USERNAME/sheep-match.git
git push -u origin main

# 3. 在 GitHub 仓库 Settings → Pages → Source: main branch
# 4. 等待部署，获得 URL: https://YOUR_USERNAME.github.io/sheep-match/
```

### 9.2 自定义域名（可选）

```
# 在仓库根目录添加 CNAME 文件
echo "sheepmatch.ae" > CNAME
```

### 9.3 PWA 支持（manifest.json）

```json
{
  "name": "Sheep Match",
  "short_name": "SheepMatch",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#1a1a2e",
  "theme_color": "#D4AF37",
  "icons": [
    { "src": "data:image/svg+xml,...", "sizes": "512x512", "type": "image/svg+xml" }
  ]
}
```

### 9.4 Service Worker（sw.js）

```javascript
const CACHE_NAME = 'sheep-match-v1';
const ASSETS = ['/', '/index.html', '/manifest.json'];

self.addEventListener('install', e => {
  e.waitUntil(
    caches.open(CACHE_NAME).then(cache => cache.addAll(ASSETS))
  );
  self.skipWaiting();
});

self.addEventListener('fetch', e => {
  e.respondWith(
    caches.match(e.request).then(r => r || fetch(e.request))
  );
});
```

---

## 10. 土豪模拟器联动方案

### 10.1 联动架构

```
Sheep Match <──共享金币池──> 土豪模拟器
     │                              │
     │  URL Scheme跳转              │
     │  sheepmatch://?coins=100     │
     │  richsim://share?game=sm&amt=500
```

### 10.2 跳转协议

```javascript
// 从 Sheep Match 跳转土豪模拟器（领取联动金币）
function linkToRichSimulator(coinAmount) {
  const url = `richsim://share?game=sheepmatch&coins=${coinAmount}&ref=${getUserId()}`;
  window.location.href = url;
}

// 从土豪模拟器跳转回 Sheep Match
function handleRichSimCallback() {
  const params = new URLSearchParams(window.location.search);
  const bonusCoins = parseInt(params.get('coins') || '0');
  const refGame = params.get('ref');

  if (refGame === 'richsim' && bonusCoins > 0) {
    addCoins(bonusCoins);
    showToast(`🎁 Got ${bonusCoins} coins from Rich Simulator!`);
  }
}

// 检测是否从土豪模拟器跳转回来
function checkIncomingLink() {
  if (document.referrer.includes('richsim')) {
    handleRichSimCallback();
  }
}
```

### 10.3 共享金币存储

```javascript
// 土豪模拟器设置共享金币（通过 Universal Link）
// Sheep Match 读取并同步
async function syncSharedCoins() {
  try {
    const resp = await fetch('https://api.richsim.ae/balance?odid=' + getUserId());
    const data = await resp.json();
    if (data.sharedCoins) {
      storage.save(STORAGE_KEYS.COINS, data.sharedCoins);
    }
  } catch(e) {
    // 网络错误，使用本地金币
  }
}
```

---

## 11. 广告集成架构

### 11.1 AdMob 激励广告流程

```javascript
class AdManager {
  constructor() {
    this.rewardedAd = null;
    this.interstitialAd = null;
    this.adReady = false;
    this.pendingReward = null;
  }

  // 初始化
  async init() {
    // 延迟加载 AdMob SDK
    if (typeof adsbygoogle === 'undefined') {
      await this.loadAdMobSDK();
    }
    await this.loadRewardedAd();
    await this.loadInterstitialAd();
  }

  loadAdMobSDK() {
    return new Promise(resolve => {
      const script = document.createElement('script');
      script.src = 'https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-app-pub-xxxxx';
      script.async = true;
      script.onload = resolve;
      document.head.appendChild(script);
    });
  }

  async loadRewardedAd() {
    try {
      this.rewardedAd = adsbygoogle.rewarded({
        adUnitId: 'ca-app-pub-xxxxx/rewarded-unit',
        beforeReward: (showPromise) => {
          // 显示广告
        },
        onRewarded: (reward) => {
          this.grantReward(this.pendingReward);
        }
      });
    } catch(e) {
      console.warn('Ad load failed:', e);
    }
  }

  async showRewardedAd(rewardType) {
    if (!this.rewardedAd) {
      showToast('Ad not ready. Try again later.');
      return;
    }
    this.pendingReward = rewardType;
    this.rewardedAd.show();
  }

  grantReward(rewardType) {
    switch(rewardType) {
      case 'revive':   addItem('revive', 1); break;
      case 'undo':     addItem('undo', 1);   break;
      case 'shuffle':  addItem('shuffle', 1); break;
      case 'coins':    addCoins(20);         break;
      case 'steps':    addSteps(5);          break;
    }
    playSFX('reward');
  }

  // 插屏广告（自动触发）
  maybeShowInterstitial() {
    const levelsPassed = getCurrentLevel();
    if (AD_REWARDS.interstitialLevels.includes(levelsPassed)) {
      this.interstitialAd?.show();
    }
  }
}

const adManager = new AdManager();
```

---

*技术架构版本：v1.0 | 最后更新：2026-03-29*
