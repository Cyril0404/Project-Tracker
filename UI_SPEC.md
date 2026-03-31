# Sheep Match — UI/UX设计规范（UI_SPEC.md）
**版本**: v1.0 | **日期**: 2026-03-29

---

## 1. 配色系统

### 1.1 主色板（中东金色主题）

```css
:root {
  /* 主色 */
  --color-primary:       #D4AF37;   /* 沙漠金（品牌主色） */
  --color-primary-dark:  #B8960C;   /* 深金 */
  --color-primary-light: #F5D76E;   /* 浅金 */

  /* 背景 */
  --color-bg-dark:       #0D0D1A;   /* 深夜蓝黑（主背景） */
  --color-bg-card:       #1A1A2E;   /* 卡片背景 */
  --color-bg-elevated:   #252542;  /* 提升层背景 */

  /* 方块背景 */
  --color-block-camel:   #C19A6B;
  --color-block-falcon:  #4A7DB8;
  --color-block-diamond: #00D4FF;
  --color-block-palm:    #2ECC71;
  --color-block-oil:     #2C3E50;
  --color-block-jar:     #8E44AD;
  --color-block-crown:   #F1C40F;

  /* 文字 */
  --color-text-primary:  #FFFFFF;
  --color-text-secondary:#B0B0C8;
  --color-text-muted:   #6B6B8A;
  --color-text-gold:    #D4AF37;

  /* 状态色 */
  --color-success:       #27AE60;
  --color-danger:        #E74C3C;
  --color-warning:       #F39C12;

  /* 道具色 */
  --color-undo:          #3498DB;
  --color-shuffle:       #9B59B6;
  --color-revive:        #E74C3C;

  /* UI线条 */
  --color-border:        rgba(212, 175, 55, 0.2);
  --color-border-active: rgba(212, 175, 55, 0.6);

  /* 遮罩 */
  --color-overlay:       rgba(0, 0, 0, 0.7);
  --color-blocked:       rgba(0, 0, 0, 0.4);
}
```

### 1.2 渐变规范

```css
/* 主按钮渐变 */
.btn-primary {
  background: linear-gradient(135deg, #D4AF37 0%, #B8960C 100%);
  box-shadow: 0 4px 15px rgba(212, 175, 55, 0.4);
}

/* 卡片渐变 */
.card-gold {
  background: linear-gradient(145deg, #1A1A2E 0%, #252542 100%);
  border: 1px solid rgba(212, 175, 55, 0.3);
}

/* 通关庆祝渐变 */
.win-gradient {
  background: linear-gradient(180deg, #D4AF37 0%, #F5D76E 50%, #D4AF37 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

/* 背景星空效果 */
.bg-starry {
  background:
    radial-gradient(ellipse at 20% 80%, rgba(212,175,55,0.08) 0%, transparent 50%),
    radial-gradient(ellipse at 80% 20%, rgba(74,125,184,0.08) 0%, transparent 50%),
    linear-gradient(180deg, #0D0D1A 0%, #1A1A2E 100%);
}
```

---

## 2. 字体规范

### 2.1 字体栈

```css
/* 英文主字体 */
font-family:
  'Poppins',        /* Google Fonts 首选 */
  -apple-system,     /* iOS 系统 */
  BlinkMacSystemFont,
  'Segoe UI',       /* Windows */
  sans-serif;

/* 数字/分数专用 */
font-family:
  'Poppins',
  'SF Pro Display',
  system-ui;

/* 阿拉伯语（RTL） */
font-family:
  'Noto Sans Arabic',
  'Arabic Typesetting',
  sans-serif;
```

### 2.2 字体加载

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700;900&family=Noto+Sans+Arabic:wght@400;600;700&display=swap" rel="stylesheet">
```

### 2.3 字号系统

```css
:root {
  --font-xs:    0.75rem;   /* 12px */
  --font-sm:    0.875rem;  /* 14px */
  --font-base:  1rem;      /* 16px */
  --font-lg:    1.125rem;  /* 18px */
  --font-xl:    1.25rem;   /* 20px */
  --font-2xl:   1.5rem;   /* 24px */
  --font-3xl:   1.875rem;  /* 30px */
  --font-4xl:   2.25rem;   /* 36px */
  --font-5xl:   3rem;      /* 48px */
  --font-6xl:   3.75rem;   /* 60px — 通关大字 */
}
```

### 2.4 字号使用规范

| 场景 | 字号 | 字重 | 示例 |
|------|------|------|------|
| 关卡数字 | 5xl-6xl | 900 | "Level 25" |
| 按钮文字 | xl | 600 | "PLAY NOW" |
| 正文 | base | 400 | 说明文字 |
| 步数计数 | 4xl | 700 | "18" |
| 道具名称 | sm | 500 | "Undo" |
| 金币数 | lg | 600 | "350 🪙" |

---

## 3. 屏幕流程与布局

### 3.1 屏幕流转图

```
┌─────────────┐
│  启动页     │───(2s)──>  ┌─────────────────┐
│  (Splash)   │            │  主菜单 (Menu)  │
└─────────────┘            │  - Play button  │
                          │  - Settings    │
                          │  - Leaderboard │
                          └────────┬────────┘
                                   │
                    点击PLAY ──────┴───── 点击其他
                                   ▼
                         ┌─────────────────┐
                         │ 关卡选择         │
                         │ (Level Select)  │
                         │ 30个关卡网格     │
                         └────────┬────────┘
                                   │
                          选择关卡 ─┴──
                                   ▼
                         ┌─────────────────┐
                         │ 游戏页 (Playing) │
                         │ - 方块区         │
                         │ - 步数          │
                         │ - 道具栏        │
                         │ - 暂停按钮       │
                         └────────┬────────┘
                                  │
                     消除全部 ─────┴───── 步数归零
                          ▼              ▼
                   ┌──────────┐    ┌──────────┐
                   │ 胜利页   │    │ 失败页   │
                   │ (Win)    │    │ (Lose)   │
                   └────┬─────┘    └────┬─────┘
                        │               │
                   分享/下一关      复活/重试
                        │               │
                        ▼               ▼
                   ┌──────────────────────────┐
                   │   分享页 (Share)         │
                   │  截图 + WhatsApp按钮    │
                   └──────────────────────────┘
```

### 3.2 各屏幕布局规格

#### 启动页（Splash）

```
┌──────────────────────────────────────┐
│                                      │
│                                      │
│           🐪 SHEEP MATCH            │  ← Logo (金色渐变)
│                                      │
│          ══════════════════          │
│                                      │
│            ⭐ ⭐ ⭐                    │  ← 装饰星星
│                                      │
│          "The Ultimate               │
│           Puzzle Challenge"          │
│                                      │
│              [  PLAY  ]              │  ← 主按钮（脉冲动画）
│                                      │
│         © 2026 Sheep Match           │
└──────────────────────────────────────┘
```

#### 主菜单（Menu）

```
┌──────────────────────────────────────┐
│  🪙 350        ⚙️ Settings    🏆     │  ← 顶栏（金币+设置+排行榜）
│                                      │
│                                      │
│           🐪 SHEEP MATCH              │
│                                      │
│         "Match to Survive"           │
│                                      │
│                                      │
│        ┌──────────────────┐          │
│        │    ▶  PLAY       │          │  ← 主按钮（金色渐变）
│        └──────────────────┘          │
│                                      │
│        ┌────┐  ┌────┐  ┌────┐        │
│        │🎨 │  │🏆 │  │❓ │        │  ← 图标按钮行
│        │Themes│ │Rank │ │Help │        │
│        └────┘  └────┘  └────┘        │
└──────────────────────────────────────┘
```

#### 游戏页（Playing）

```
┌──────────────────────────────────────┐
│ ⬅️ Back   Level 5: Crown Jewel  ⏸️  │  ← 导航栏
├──────────────────────────────────────┤
│                                      │
│    ┌─────────────────────────┐      │
│    │ 🐪  🦅  💎  🐪           │      │  ← 游戏区（Canvas）
│    │ 🏺  👑  ⛽  🌴           │      │
│    │  🦅  🐪  👑  💎          │      │  ← 底层（被遮挡半透明）
│    │ 🏺  ⛽  🌴  🦅           │      │
│    │ 💎  🐪  🦅  👑          │      │
│    └─────────────────────────┘      │
│                                      │
│    ┌──────────────────────────┐      │
│    │   STEPS: 18              │      │  ← 步数指示器
│    │   ████████░░░░░  18/30  │      │  ← 进度条
│    └──────────────────────────┘      │
│                                      │
│    ┌────┐   ┌────┐   ┌────┐          │
│    │↩️ 2│   │🔀 1│   │❤ 0│          │  ← 道具栏
│    │Undo│  │Shuf│  │Rev│          │
│    │ 50 │  │ 80 │  │200│          │  ← 金币价格
│    └────┘   └────┘   └────┘          │
│                                      │
│         [ 📺 Watch Ad for +5 Steps ] │  ← 广告提示条
└──────────────────────────────────────┘
```

#### 胜利页（Win）

```
┌──────────────────────────────────────┐
│                                      │
│         ✨  LEVEL COMPLETE!  ✨       │
│                                      │
│           ⭐  ⭐  ⭐                   │  ← 获得星级
│                                      │
│         Level 5 — Crown Jewel        │
│                                      │
│         +80 🪙  earned!              │  ← 金币奖励
│                                      │
│    ┌──────┐  ┌──────┐  ┌────────┐   │
│    │ SHARE│  │ NEXT │  │MENU    │   │
│    └──────┘  └──────┘  └────────┘   │
│                                      │
│    📊 Your stats: 56% Win Rate       │
└──────────────────────────────────────┘
```

#### 失败页（Lose）

```
┌──────────────────────────────────────┐
│                                      │
│         😤  SO CLOSE...  😤          │
│                                      │
│         Level 5 failed               │
│         Blocks left: 4               │
│                                      │
│    ┌─────────────────────────┐      │
│    │   ❤️  Watch Ad to Revive  │      │  ← 复活按钮（红色）
│    └─────────────────────────┘      │
│                                      │
│    ┌──────┐  ┌──────┐               │
│    │RETRY │  │ MENU │               │
│    └──────┘  └──────┘               │
│                                      │
│    "Only 0.1% make it. Try again?"   │
└──────────────────────────────────────┘
```

---

## 4. 组件设计

### 4.1 方块组件

```css
.block {
  width: 50px;
  height: 50px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  cursor: pointer;
  transition: transform 0.1s ease, box-shadow 0.1s ease;
  user-select: none;
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}

.block:active {
  transform: scale(0.92);
}

.block.blocked {
  filter: brightness(0.6);
  cursor: not-allowed;
}

.block.selected {
  box-shadow: 0 0 0 3px var(--color-primary), 0 4px 15px rgba(212,175,55,0.5);
  transform: scale(1.08);
  z-index: 10;
}

.block.matched {
  animation: blockMatch 0.3s ease-out forwards;
}

@keyframes blockMatch {
  0%   { transform: scale(1); opacity: 1; }
  50%  { transform: scale(1.3); opacity: 0.8; }
  100% { transform: scale(0); opacity: 0; }
}
```

### 4.2 按钮样式

```css
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 14px 32px;
  border-radius: 12px;
  font-family: 'Poppins', sans-serif;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  border: none;
  outline: none;
  transition: all 0.2s ease;
  min-height: 48px; /* 触摸友好 */
  -webkit-tap-highlight-color: transparent;
}

.btn-primary {
  background: linear-gradient(135deg, #D4AF37 0%, #B8960C 100%);
  color: #0D0D1A;
  box-shadow: 0 4px 15px rgba(212,175,55,0.4);
  font-size: 1.25rem;
  letter-spacing: 0.05em;
}

.btn-primary:hover {
  box-shadow: 0 6px 20px rgba(212,175,55,0.6);
  transform: translateY(-2px);
}

.btn-primary:active {
  transform: translateY(0);
  box-shadow: 0 2px 10px rgba(212,175,55,0.3);
}

.btn-secondary {
  background: var(--color-bg-elevated);
  color: var(--color-text-primary);
  border: 1px solid var(--color-border);
}

.btn-danger {
  background: var(--color-danger);
  color: white;
}

.btn-icon {
  width: 44px;
  height: 44px;
  padding: 0;
  border-radius: 50%;
}
```

### 4.3 道具栏组件

```css
.item-slot {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  padding: 10px 14px;
  background: var(--color-bg-elevated);
  border: 1px solid var(--color-border);
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.2s ease;
  min-width: 70px;
}

.item-slot:hover {
  border-color: var(--color-border-active);
  transform: translateY(-2px);
}

.item-slot:active {
  transform: scale(0.95);
}

.item-slot.disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.item-count {
  font-size: 0.7rem;
  font-weight: 700;
  color: var(--color-text-secondary);
}

.item-price {
  font-size: 0.65rem;
  color: var(--color-gold);
}
```

### 4.4 步数计数器

```css
.step-counter {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
  padding: 12px 24px;
  background: var(--color-bg-elevated);
  border: 1px solid var(--color-border);
  border-radius: 16px;
}

.step-number {
  font-size: 2.5rem;
  font-weight: 900;
  color: var(--color-primary);
  line-height: 1;
  font-variant-numeric: tabular-nums;
}

.step-number.warning {
  color: var(--color-warning);
  animation: stepPulse 0.5s ease infinite;
}

.step-number.danger {
  color: var(--color-danger);
  animation: stepPulse 0.3s ease infinite;
}

@keyframes stepPulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.step-bar {
  width: 100%;
  height: 6px;
  background: rgba(255,255,255,0.1);
  border-radius: 3px;
  overflow: hidden;
}

.step-bar-fill {
  height: 100%;
  background: linear-gradient(90deg, var(--color-primary) 0%, var(--color-primary-light) 100%);
  border-radius: 3px;
  transition: width 0.3s ease;
}
```

### 4.5 关卡卡片

```css
.level-card {
  width: 64px;
  height: 64px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: var(--color-bg-elevated);
  border: 1px solid var(--color-border);
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.2s ease;
  position: relative;
}

.level-card:hover {
  border-color: var(--color-primary);
  transform: scale(1.05);
}

.level-card.locked {
  opacity: 0.5;
  cursor: not-allowed;
}

.level-card .level-num {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--color-text-primary);
}

.level-card .level-stars {
  font-size: 0.6rem;
  margin-top: 2px;
}

.level-card.completed {
  background: linear-gradient(135deg, rgba(212,175,55,0.2) 0%, rgba(212,175,55,0.05) 100%);
  border-color: rgba(212,175,55,0.4);
}
```

---

## 5. 动效规范

### 5.1 方块消除动画序列

```javascript
// 方块消除动画
const matchAnimation = [
  { t: 0,   scale: 1,    opacity: 1,   rotate: 0    },
  { t: 150, scale: 1.3,  opacity: 0.8, rotate: 0    },
  { t: 300, scale: 0,    opacity: 0,   rotate: 15   }
];

function animateMatch(blockId) {
  const block = document.getElementById(blockId);
  block.animate([
    { transform: 'scale(1)', opacity: 1 },
    { transform: 'scale(1.3)', opacity: 0.8 },
    { transform: 'scale(0) rotate(15deg)', opacity: 0 }
  ], {
    duration: 300,
    easing: 'cubic-bezier(0.4, 0, 0.2, 1)',
    fill: 'forwards'
  });
}
```

### 5.2 道具特效

```javascript
// 洗牌动画（所有方块翻转）
function animateShuffle() {
  blocks.forEach((block, i) => {
    block.element.animate([
      { transform: 'rotateY(0deg)', opacity: 1 },
      { transform: 'rotateY(90deg)', opacity: 0 },
      { transform: 'rotateY(0deg)', opacity: 1 }
    ], {
      duration: 600,
      delay: i * 20,
      easing: 'ease-in-out',
      fill: 'forwards'
    });
  });
}

// 撤回动画（方块从底部飞回）
function animateUndo() {
  const [b1, b2] = lastMatchedPair;
  [b1, b2].forEach((block, i) => {
    block.element.animate([
      { transform: 'translateY(0)', opacity: 0 },
      { transform: 'translateY(-50px)', opacity: 0.5 },
      { transform: 'translateY(0)', opacity: 1 }
    ], {
      duration: 400,
      delay: i * 100,
      easing: 'bounce'
    });
  });
}
```

### 5.3 通关庆祝动画

```javascript
// 胜利粒子特效
function playWinCelebration() {
  const canvas = document.getElementById('fx-canvas');

  // 星星粒子
  for (let i = 0; i < 50; i++) {
    const star = document.createElement('div');
    star.innerHTML = ['⭐','✨','🌟','💫'][Math.floor(Math.random()*4)];
    star.style.cssText = `
      position: fixed;
      font-size: ${Math.random() * 20 + 10}px;
      left: ${Math.random() * 100}vw;
      top: -50px;
      animation: starFall ${Math.random() * 2 + 2}s linear forwards;
      pointer-events: none;
      z-index: 9999;
    `;
    document.body.appendChild(star);
    setTimeout(() => star.remove(), 4000);
  }
}

// CSS 动画
const celebrationCSS = `
  @keyframes starFall {
    0% { transform: translateY(0) rotate(0deg); opacity: 1; }
    100% { transform: translateY(110vh) rotate(360deg); opacity: 0; }
  }
  @keyframes levelWin {
    0% { transform: scale(0); opacity: 0; }
    50% { transform: scale(1.2); opacity: 1; }
    70% { transform: scale(0.9); }
    100% { transform: scale(1); opacity: 1; }
  }
  @keyframes goldPulse {
    0%, 100% { text-shadow: 0 0 10px rgba(212,175,55,0.5); }
    50% { text-shadow: 0 0 30px rgba(212,175,55,0.9), 0 0 60px rgba(212,175,55,0.5); }
  }
`;
```

### 5.4 屏幕切换动画

```css
.screen {
  position: absolute;
  inset: 0;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;
}

.screen.active {
  opacity: 1;
  pointer-events: auto;
}

/* 滑入效果 */
.slide-up-enter {
  animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1) forwards;
}

@keyframes slideUp {
  from { transform: translateY(100%); opacity: 0; }
  to   { transform: translateY(0); opacity: 1; }
}

.slide-down-exit {
  animation: slideDown 0.3s ease-in forwards;
}

@keyframes slideDown {
  from { transform: translateY(0); opacity: 1; }
  to   { transform: translateY(100%); opacity: 0; }
}
```

---

## 6. 音效规范

### 6.1 音效规格表

| 音效名 | 文件格式 | 时长 | 文件大小 | 用途 |
|--------|----------|------|----------|------|
| match.mp3 | MP3 | <1s | <20KB | 消除成功 |
| win.mp3 | MP3 | 3-5s | <100KB | 通关 |
| lose.mp3 | MP3 | 1-2s | <30KB | 失败 |
| click.mp3 | MP3 | <0.5s | <10KB | 按钮点击 |
| item_use.mp3 | MP3 | <1s | <20KB | 道具使用 |
| bgm_menu.mp3 | MP3 | ~60s循环 | <500KB | 菜单背景音乐 |
| bgm_game.mp3 | MP3 | ~60s循环 | <500KB | 游戏背景音乐 |

### 6.2 音量控制

```javascript
const audioSettings = {
  music: { key: 'music', default: true },
  sfx:   { key: 'sound', default: true }
};

function setMusicVolume(level) {
  if (bgmSource) {
    bgmGain.gain.setTargetAtTime(level, audioCtx.currentTime, 0.1);
  }
}

function setSFXVolume(level) {
  sfxGain.gain.setTargetAtTime(level, audioCtx.currentTime, 0.1);
}
```

---

## 7. RTL（阿拉伯语）适配

### 7.1 方向感知样式

```css
/* 默认 LTR（英语） */
.game-layout {
  direction: ltr;
}

/* 阿拉伯语 RTL */
html[lang="ar"] .game-layout {
  direction: rtl;
}

html[lang="ar"] .btn-icon-back {
  transform: scaleX(-1); /* 箭头翻转 */
}

html[lang="ar"] .item-slot {
  flex-direction: column-reverse; /* 图标在上，文字在下 */
}
```

### 7.2 RTL 检测与切换

```javascript
function setLanguage(lang) {
  document.documentElement.lang = lang;

  if (lang === 'ar') {
    document.dir = 'rtl';
    document.body.classList.add('rtl');
    // 阿拉伯语文案
  } else {
    document.dir = 'ltr';
    document.body.classList.remove('rtl');
    // 英语文案
  }

  Storage.save(STORAGE_KEYS.SETTINGS, { ...settings, language: lang });
}
```

---

*UI/UX设计规范版本：v1.0 | 最后更新：2026-03-29*
