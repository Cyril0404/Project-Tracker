# Sheep Match — 游戏数据文档（GAME_DATA.md）
**版本**: v1.0 | **日期**: 2026-03-29

---

## 1. 方块（Block）完整属性

### 1.1 七种基础方块

| ID | 名称（英） | 名称（中） | Emoji | 颜色（HEX） | 稀有度 | 权重 |
|----|-----------|-----------|-------|-------------|--------|------|
| B01 | Camel | 骆驼 | 🐪 | #C19A6B | 普通 | 15% |
| B02 | Falcon | 猎鹰 | 🦅 | #4A7DB8 | 普通 | 15% |
| B03 | Diamond | 钻石 | 💎 | #00D4FF | 稀有 | 13% |
| B04 | Palm Tree | 棕榈树 | 🌴 | #2ECC71 | 普通 | 15% |
| B05 | Oil Rig | 油井 | ⛽ | #2C3E50 | 普通 | 14% |
| B06 | Ancient Jar | 古代罐子 | 🏺 | #8E44AD | 稀有 | 13% |
| B07 | Crown | 皇冠 | 👑 | #F1C40F | 传说 | 15% |

### 1.2 方块尺寸规格

```javascript
const BLOCK_SIZE = {
  width: 50,      // px，方块宽度
  height: 50,     // px，方块高度
  gap: 2,         // px，方块间距
  borderRadius: 8 // px，圆角半径
};
```

### 1.3 方块数据结构

```javascript
// 单个方块实例
{
  id: "B01_001",      // 方块ID = 类型ID + 序号
  type: "B01",        // 方块类型（对应7种之一）
  emoji: "🐪",        // 显示emoji
  color: "#C19A6B",   // 背景色
  layer: 0,           // 所在层级（0=底层，1=顶层）
  row: 3,             // 所在行
  col: 5,             // 所在列
  removable: false,   // 是否可消除（无遮挡时为true）
}
```

---

## 2. 关卡数据（30个关卡详细配置）

### 2.1 关卡配置格式

```javascript
{
  level: 1,              // 关卡编号
  stars: 1,              // 难度星级（1-3）
  name: "Desert Dawn",   // 关卡名称（英文）
  totalBlocks: 42,       // 方块总数（必须是7的倍数）
  pairs: 21,             // 消除对数
  steps: 32,             // 允许步数
  topLayerCount: 12,     // 顶层方块数量
  bottomLayerCount: 30,  // 底层方块数量
  targetPairs: 21,       // 需要消除的对数（=pairs）
  dailyReward: 20,       // 首次通关奖励金币
  layoutType: "standard", // 布局类型
  description: "Easy warm-up level with simple pyramid layout"
}
```

### 2.2 Level 1-10（超简单，星级1）

---

**Level 1 — Desert Dawn**
```javascript
{
  level: 1, stars: 1, name: "Desert Dawn",
  totalBlocks: 28, pairs: 14, steps: 32,
  topLayerCount: 8, bottomLayerCount: 20,
  layoutType: "standard",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽",
    "💎 🌴 🦅 👑 ⛽ 🐪 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅"
  ],
  topLayout: [
    "🐪 🦅 💎 🐪",
    "🌴 ⛽ 🏺 👑"
  ],
  tip: "Match edge blocks first to clear the board faster.",
  passRate: 0.28
}
```

---

**Level 2 — Oasis Calm**
```javascript
{
  level: 2, stars: 1, name: "Oasis Calm",
  totalBlocks: 35, pairs: 14, steps: 32,
  topLayerCount: 10, bottomLayerCount: 25,
  layoutType: "standard",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽",
    "💎 🌴 🦅 👑 ⛽ 🐪 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽"
  ],
  topLayout: [
    "🐪 🦅 🏺 👑",
    "🌴 ⛽ 🐪 🦅",
    "💎 👑"
  ],
  tip: "Always clear from the edges inward.",
  passRate: 0.25
}
```

---

**Level 3 — Falcon's Flight**
```javascript
{
  level: 3, stars: 1, name: "Falcon's Flight",
  totalBlocks: 35, pairs: 14, steps: 31,
  topLayerCount: 10, bottomLayerCount: 25,
  layoutType: "cross",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽",
    "💎 🌴 🦅 👑 ⛽ 🐪 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽"
  ],
  topLayout: [
    "    🐪 🦅    ",
    "  🌴 ⛽ 🏺 👑",
    "  💎 🐪 💎  "
  ],
  tip: "Look for pairs in the top layer before clicking.",
  passRate: 0.23
}
```

---

**Level 4 — Palm Shade**
```javascript
{
  level: 4, stars: 1, name: "Palm Shade",
  totalBlocks: 42, pairs: 14, steps: 31,
  topLayerCount: 12, bottomLayerCount: 30,
  layoutType: "stepped",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽",
    "💎 🌴 🦅 👑 ⛽ 🐪 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽",
    "🐪 💎 🦅 🌴 ⛽ 🏺 👑"
  ],
  topLayout: [
    "🐪 🦅 💎 🌴",
    "🌴 ⛽ 🏺 👑",
    "    💎 🦅  "
  ],
  tip: "Count each emoji type before starting.",
  passRate: 0.22
}
```

---

**Level 5 — Crown Jewel**
```javascript
{
  level: 5, stars: 1, name: "Crown Jewel",
  totalBlocks: 42, pairs: 14, steps: 30,
  topLayerCount: 12, bottomLayerCount: 30,
  layoutType: "diamond",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽",
    "💎 🌴 🦅 👑 ⛽ 🐪 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽",
    "🐪 💎 🦅 🌴 ⛽ 🏺 👑"
  ],
  topLayout: [
    "    🐪    ",
    "  🦅 💎 🌴",
    "⛽ 🏺 👑 🐪",
    "  💎 🌴 🦅",
    "    👑    "
  ],
  tip: "Crown appears less often — plan ahead!",
  passRate: 0.21
}
```

---

**Level 6 — Desert Storm**
```javascript
{
  level: 6, stars: 1, name: "Desert Storm",
  totalBlocks: 42, pairs: 14, steps: 30,
  topLayerCount: 14, bottomLayerCount: 28,
  layoutType: "irregular",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑",
    "  💎 🐪 🌴 🏺 👑  ",
    "  🦅 🌴 ⛽ 🐪 🦅  ",
    "  💎 🏺 👑 💎 🌴  ",
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑"
  ],
  topLayout: [
    "🐪 🦅 🏺 👑",
    "🌴 ⛽ 🐪 🦅",
    "💎 👑 🏺 🌴",
    "    ⛽    "
  ],
  tip: "Mixed layout — scan carefully before clicking.",
  passRate: 0.20
}
```

---

**Level 7 — Ancient Road**
```javascript
{
  level: 7, stars: 1, name: "Ancient Road",
  totalBlocks: 49, pairs: 14, steps: 29,
  topLayerCount: 14, bottomLayerCount: 35,
  layoutType: "bridge",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑 🐪",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽ 🦅",
    "💎 🌴 🦅 👑 ⛽ 🐪 💎 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅 🌴",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽ 🏺"
  ],
  topLayout: [
    "  🐪 🦅 💎 🌴  ",
    "  🌴 ⛽ 🏺 👑  ",
    "    💎 🐪 🦅  ",
    "      👑      "
  ],
  tip: "Bridge layout — prioritize center blocks.",
  passRate: 0.19
}
```

---

**Level 8 — Oil Fortune**
```javascript
{
  level: 8, stars: 1, name: "Oil Fortune",
  totalBlocks: 49, pairs: 14, steps: 29,
  topLayerCount: 16, bottomLayerCount: 33,
  layoutType: "tower",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑 🐪",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽ 🦅",
    "💎 🌴 🦅 👑 ⛽ 🐪 💎 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅 🌴",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽ 🏺"
  ],
  topLayout: [
    "    🐪 🦅    ",
    "  🌴 ⛽ 🏺 👑",
    "  💎 🐪 🦅 🌴",
    "⛽ 🏺 👑 🐪 🦅"
  ],
  tip: "Oil Rig pairs are tricky — watch for them.",
  passRate: 0.18
}
```

---

**Level 9 — Golden Palace**
```javascript
{
  level: 9, stars: 1, name: "Golden Palace",
  totalBlocks: 49, pairs: 14, steps: 28,
  topLayerCount: 16, bottomLayerCount: 33,
  layoutType: "pyramid",
  bottomLayout: [
    "🐪 🦅 💎 🌴 ⛽ 🏺 👑 🐪",
    "🦅 💎 🐪 🌴 🏺 👑 ⛽ 🦅",
    "💎 🌴 🦅 👑 ⛽ 🐪 💎 🏺",
    "🌴 ⛽ 👑 🏺 🐪 💎 🦅 🌴",
    "🏺 👑 🐪 💎 🦅 🌴 ⛽ 🏺"
  ],
  topLayout: [
    "    🐪 🦅    ",
    "  💎 🌴 ⛽ 🏺",
    "  👑 🐪 🦅 💎",
    "🌴 ⛽ 🏺 🐪 🦅"
  ],
  tip: "Pyramid layout — clear the top rows first.",
  passRate: 0.17
}
```

---

**Level 10 — Mirage**
```javascript
{
  level: 10, stars: 1, name: "Mirage",
  totalBlocks: 49, pairs: 14, steps: 28,
  topLayerCount: 18, bottomLayerCount: 31,
  layoutType: "chaos",
  bottomLayout: [
    "🐪  💎 🌴 🏺 🐪 🦅 💎 🌴",
    "👑 🏺 🐪 ⛽ 🦅 💎 🐪 🌴",
    "💎 🦅 🌴 🐪 🏺 👑 🦅 💎",
    "🌴 🏺 🐪 🦅 ⛽ 💎 🌴 👑",
    "🏺 🐪 🦅 💎 🐪 🌴 🏺 ⛽",
    "👑 💎 🌴 🐪 🦅 🏺 👑 🐪"
  ],
  topLayout: [
    "🐪 🦅 💎 🌴",
    "🏺 👑 🐪 🦅",
    "💎 🌴 🏺 👑",
    "    ⛽ 💎    "
  ],
  tip: "Chaos layout — patience is key.",
  passRate: 0.15
}
```

### 2.3 Level 11-20（中等难度，星级2）

---

**Level 11 — Sand Dunes** ⭐⭐
```javascript
{
  level: 11, stars: 2, name: "Sand Dunes",
  totalBlocks: 56, pairs: 21, steps: 27,
  topLayerCount: 20, bottomLayerCount: 36,
  layoutType: "wave",
  description: "Wave pattern adds visual complexity",
  tip: "Watch the wave pattern — it hides similar emojis together.",
  passRate: 0.08
}
```

---

**Level 12 — Emirate Night** ⭐⭐
```javascript
{
  level: 12, stars: 2, name: "Emirate Night",
  totalBlocks: 56, pairs: 21, steps: 26,
  topLayerCount: 22, bottomLayerCount: 34,
  layoutType: "grid",
  description: "Standard grid becomes tighter",
  tip: "Grid layout — scan columns top to bottom.",
  passRate: 0.07
}
```

---

**Level 13 — Arabian Knights** ⭐⭐
```javascript
{
  level: 13, stars: 2, name: "Arabian Knights",
  totalBlocks: 56, pairs: 21, steps: 26,
  topLayerCount: 22, bottomLayerCount: 34,
  layoutType: "castle",
  description: "Castle wall pattern — tight clusters",
  tip: "Castle walls trap Crowns — save Undo for emergencies.",
  passRate: 0.065
}
```

---

**Level 14 — Spice Route** ⭐⭐
```javascript
{
  level: 14, stars: 2, name: "Spice Route",
  totalBlocks: 63, pairs: 21, steps: 25,
  topLayerCount: 24, bottomLayerCount: 39,
  layoutType: "zigzag",
  description: "Zigzag rows challenge spatial memory",
  tip: "Zigzag layout — use Shuffle if you lose track.",
  passRate: 0.06
}
```

---

**Level 15 — Dubai Marina** ⭐⭐
```javascript
{
  level: 15, stars: 2, name: "Dubai Marina",
  totalBlocks: 63, pairs: 21, steps: 25,
  topLayerCount: 26, bottomLayerCount: 37,
  layoutType: "tower_block",
  description: "Tower blocks block vision of bottom layer",
  tip: "Count Diamond blocks carefully before committing.",
  passRate: 0.055
}
```

---

**Level 16 — Bedouin Camp** ⭐⭐
```javascript
{
  level: 16, stars: 2, name: "Bedouin Camp",
  totalBlocks: 63, pairs: 21, steps: 25,
  topLayerCount: 26, bottomLayerCount: 37,
  layoutType: "circle_outer",
  description: "Outer circle surrounds inner core",
  tip: "Clear outer ring first to expose the center.",
  passRate: 0.05
}
```

---

**Level 17 — Oil Empire** ⭐⭐
```javascript
{
  level: 17, stars: 2, name: "Oil Empire",
  totalBlocks: 70, pairs: 21, steps: 24,
  topLayerCount: 28, bottomLayerCount: 42,
  layoutType: "oil_field",
  description: "Oil Rig blocks cluster in the center",
  tip: "Oil Rig pairs are surrounded — use Shuffle.",
  passRate: 0.045
}
```

---

**Level 18 — Falcon Hunt** ⭐⭐
```javascript
{
  level: 18, stars: 2, name: "Falcon Hunt",
  totalBlocks: 70, pairs: 21, steps: 24,
  topLayerCount: 28, bottomLayerCount: 42,
  layoutType: "hunt",
  description: "Falcon blocks scattered across all layers",
  tip: "Falcon pairs are always at opposite corners — plan your route.",
  passRate: 0.04
}
```

---

**Level 19 — Desert Oasis** ⭐⭐
```javascript
{
  level: 19, stars: 2, name: "Desert Oasis",
  totalBlocks: 70, pairs: 21, steps: 23,
  topLayerCount: 30, bottomLayerCount: 40,
  layoutType: "oasis",
  description: "Palm Tree clusters surround a hidden core",
  tip: "Oasis core blocks are hard to reach — clear paths early.",
  passRate: 0.035
}
```

---

**Level 20 — Royal Decree** ⭐⭐
```javascript
{
  level: 20, stars: 2, name: "Royal Decree",
  totalBlocks: 70, pairs: 21, steps: 23,
  topLayerCount: 32, bottomLayerCount: 38,
  layoutType: "throne",
  description: "Crown blocks form the throne — hardest level 2⭐",
  tip: "Save Crowns for last — they block critical paths.",
  passRate: 0.03
}
```

### 2.4 Level 21-30（极难，星级3）

---

**Level 21 — The Gauntlet** ⭐⭐⭐
```javascript
{
  level: 21, stars: 3, name: "The Gauntlet",
  totalBlocks: 77, pairs: 21, steps: 22,
  topLayerCount: 35, bottomLayerCount: 42,
  layoutType: "gauntlet",
  description: "First 3-star level. Extremely tight layout.",
  tip: "You WILL need at least one Undo. Save it.",
  passRate: 0.005
}
```

---

**Level 22 — Sandstorm** ⭐⭐⭐
```javascript
{
  level: 22, stars: 3, name: "Sandstorm",
  totalBlocks: 77, pairs: 21, steps: 21,
  topLayerCount: 36, bottomLayerCount: 41,
  layoutType: "storm",
  description: "Storm pattern — blocks shift visually",
  tip: "Focus on Crown and Diamond pairs — they're the bottleneck.",
  passRate: 0.004
}
```

---

**Level 23 — Pharaoh's Tomb** ⭐⭐⭐
```javascript
{
  level: 23, stars: 3, name: "Pharaoh's Tomb",
  totalBlocks: 84, pairs: 21, steps: 21,
  topLayerCount: 38, bottomLayerCount: 46,
  layoutType: "tomb",
  description: "Ancient Jar blocks are the key — and the trap",
  tip: "Ancient Jar is scarce — never eliminate it casually.",
  passRate: 0.003
}
```

---

**Level 24 — Emir's Challenge** ⭐⭐⭐
```javascript
{
  level: 24, stars: 3, name: "Emir's Challenge",
  totalBlocks: 84, pairs: 21, steps: 20,
  topLayerCount: 40, bottomLayerCount: 44,
  layoutType: "palace",
  description: "Palace layout — multiple layers overlap",
  tip: "This level is designed to be near-impossible without Shuffle.",
  passRate: 0.0025
}
```

---

**Level 25 — Zero Percent** ⭐⭐⭐
```javascript
{
  level: 25, stars: 3, name: "Zero Percent",
  totalBlocks: 84, pairs: 21, steps: 20,
  topLayerCount: 42, bottomLayerCount: 42,
  layoutType: "zero",
  description: "The ultimate challenge. Less than 0.1% will pass.",
  tip: "There's no shame in watching an ad for Revive.",
  passRate: 0.001
}
```

---

**Level 26 — Wealth Paradox** ⭐⭐⭐
```javascript
{
  level: 26, stars: 3, name: "Wealth Paradox",
  totalBlocks: 91, pairs: 21, steps: 20,
  topLayerCount: 42, bottomLayerCount: 49,
  layoutType: "paradox",
  description: "Wealth blocks paradox — Crown and Diamond at center",
  tip: "Core blocks are trapped — clear surrounding blocks first.",
  passRate: 0.0008
}
```

---

**Level 27 — Camel Caravan** ⭐⭐⭐
```javascript
{
  level: 27, stars: 3, name: "Camel Caravan",
  totalBlocks: 91, pairs: 21, steps: 19,
  topLayerCount: 44, bottomLayerCount: 47,
  layoutType: "caravan",
  description: "Camel trail stretches across the entire board",
  tip: "Camel pairs are at both ends — connect the trail.",
  passRate: 0.0006
}
```

---

**Level 28 — Black Gold** ⭐⭐⭐
```javascript
{
  level: 28, stars: 3, name: "Black Gold",
  totalBlocks: 91, pairs: 21, steps: 19,
  topLayerCount: 44, bottomLayerCount: 47,
  layoutType: "oil_pool",
  description: "Oil Rig pool at center — surrounded by gold",
  tip: "Oil cluster is impenetrable — use Shuffle repeatedly.",
  passRate: 0.0005
}
```

---

**Level 29 — Last Oasis** ⭐⭐⭐
```javascript
{
  level: 29, stars: 3, name: "Last Oasis",
  totalBlocks: 98, pairs: 21, steps: 18,
  topLayerCount: 46, bottomLayerCount: 52,
  layoutType: "final_oasis",
  description: "Second-to-last level. Almost nobody reaches here.",
  tip: "You need all 3 tools: Undo + Shuffle + Revive.",
  passRate: 0.0003
}
```

---

**Level 30 — Sheep King** ⭐⭐⭐
```javascript
{
  level: 30, stars: 3, name: "The Sheep King",
  totalBlocks: 98, pairs: 21, steps: 17,
  topLayerCount: 48, bottomLayerCount: 50,
  layoutType: "final_throne",
  description: "The final boss. Only 0.1% reach here. Fewer survive.",
  tip: "Congratulations even getting here. Now prove you're the 0.1%.",
  passRate: 0.0001
}
```

---

## 3. 道具体数据

### 3.1 道具基础配置

```javascript
const ITEMS = {
  UNDO: {
    id: "undo",
    name: "Undo",
    nameAr: "التراجع",
    emoji: "↩️",
    color: "#3498DB",
    effect: "撤销上一次操作，恢复2个方块，返还1步",
    coinCost: 50,
    adReward: true,
    dailyFree: 0,
    maxStack: 3,
    description: "Oops! Take back your last move."
  },
  SHUFFLE: {
    id: "shuffle",
    name: "Shuffle",
    nameAr: "خلط",
    emoji: "🔀",
    color: "#9B59B6",
    effect: "随机打乱所有剩余方块的位置",
    coinCost: 80,
    adReward: true,
    dailyFree: 0,
    maxStack: 3,
    description: "When stuck, shuffle everything!"
  },
  REVIVE: {
    id: "revive",
    name: "Revive",
    nameAr: "إعادة الحياة",
    emoji: "❤️‍🔥",
    color: "#E74C3C",
    effect: "失败后使用，返还3步，获得第二次机会",
    coinCost: 200,
    adReward: true,
    dailyFree: 0,
    maxStack: 5,
    description: "Don't give up! Try again with +3 steps."
  }
};
```

---

## 4. 金币经济系统

### 4.1 金币产出配置

```javascript
const COIN_REWARDS = {
  dailyLogin: [10, 15, 20, 25, 30, 35, 40], // 第1-7天递增
  watchAd: { min: 5, max: 20 },
  levelComplete: level => Math.floor(20 + level * 3), // 20-110金币
  inviteFriend: 100,
  firstTimeShare: 10
};

const COIN_DAILY_CAP = {
  watchAd: 100,    // 看广告每日最多获得100金币
  login: 40,       // 每日登录最多40金币
  invite: 300,     // 邀请好友每日最多300金币
  total: 440        // 每日总上限
};
```

### 4.2 金币消耗配置

```javascript
const COIN_COSTS = {
  undo: 50,
  shuffle: 80,
  revive: 200,
  skipLevel: 1000,
  theme_desert_night: 200,
  theme_ocean: 300,
  theme_royal: 500
};
```

### 4.3 内购定价

```javascript
const IAP_PACKAGES = [
  { id: "coin_pack_small",  name: "Small Pouch",     aed: 3.00,  coins: 200,  bonus: 0   },
  { id: "coin_pack_medium", name: "Medium Pouch",    aed: 8.00,  coins: 600,  bonus: 0   },
  { id: "coin_pack_large",   name: "Large Pouch",     aed: 15.00, coins: 1200, bonus: 100 },
  { id: "coin_pack_sultan",  name: "Sultan's Chest", aed: 30.00, coins: 2800, bonus: 400 }
];

const AED_TO_COIN_RATE = {
  "coin_pack_small":  Math.round(200 / 3.00),   // 66.7
  "coin_pack_medium": Math.round(600 / 8.00),   // 75
  "coin_pack_large":  Math.round(1300 / 15.00), // 86.7
  "coin_pack_sultan": Math.round(3200 / 30.00) // 106.7
};
```

---

## 5. 广告奖励配置

### 5.1 激励广告奖励表

```javascript
const AD_REWARDS = {
  // 场景：激励视频
  onFirstFail:      { item: "revive",   amount: 1,  label: "Free Revive!" },
  onDailyFirst:     { item: "random",  amount: 1,  label: "Free Random Item!" },
  onOutOfItems:     { item: "undo",     amount: 1,  label: "Free Undo!" },
  onDoubleCoins:    { item: "coins",   amount: 2,  label: "2x Coins!" },
  onExtraSteps:     { item: "steps",   amount: 5,  label: "+5 Steps!" },
  onLevelComplete:  { item: "coins",   amount: 50, label: "+50 Bonus Coins!" },
  // 场景：插屏广告（每3关自动展示）
  interstitialCooldown: 180, // 秒
  interstitialLevels: [3, 6, 9, 12, 15, 18, 21, 24, 27, 30] // 每3关插屏
};
```

---

## 6. 排行榜数据

### 6.1 排行榜数据结构

```javascript
const LEADERBOARD = {
  types: ["friends", "global", "country"],
  timeFrames: ["today", "week", "allTime"],
  fields: {
    level: "Highest Level",
    wins: "Total Wins",
    winRate: "Win Rate %"
  }
};
```

### 6.2 排行榜存储结构（localStorage）

```javascript
// 排行榜本地缓存
{
  lastSynced: 1711651200000, // 时间戳
  friends: [
    { odid: "fr_abc123", name: "Ahmed K.", level: 25, wins: 142, avatar: "👨" },
    { odid: "fr_def456", name: "Mohammed A.", level: 22, wins: 98, avatar: "🧔" }
  ],
  global: [], // 空数组=未联网，使用模拟数据
  personalBest: {
    highestLevel: 18,
    totalWins: 56,
    totalPlays: 312,
    winRate: 17.9
  }
}
```

---

*数据版本：v1.0 | 最后更新：2026-03-29*
