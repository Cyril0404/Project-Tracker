# Sheep Match — 变现设计文档（MONETIZATION.md）
**版本**: v1.0 | **日期**: 2026-03-29

---

## 1. 变现战略概述

### 1.1 变现模式

```
┌────────────────────────────────────────────────────────┐
│                    变现三大支柱                          │
├────────────────┬────────────────┬─────────────────────┤
│   广告变现      │    内购变现     │     数据/品牌变现    │
│   (Ad Revenue) │  (IAP Revenue) │   (Future)         │
│                │                │                     │
│  占比: 65%     │  占比: 30%     │  占比: 5%           │
│  ARPU: $0.15   │  LTV: $0.50    │  (KOL联名等)        │
└────────────────┴────────────────┴─────────────────────┘
```

### 1.2 用户付费漏斗

```
用户进入
   ↓
[免费游戏体验] ── 激励广告（道具/续命）──→ 付费意愿萌芽
   ↓
[首次卡关] ── 看广告复活 ──→ 建立付费习惯
   ↓
[频繁卡关] ── 购买金币/跳过关卡 ──→ 首次付费
   ↓
[深度用户] ── 购买大额礼包/主题 ──→ 高价值用户
   ↓
[传播节点] ── 分享炫耀/邀请好友 ──→ K因子>1
```

---

## 2. 广告变现方案

### 2.1 Google AdMob 集成

#### 2.1.1 广告单元配置

| 广告单元 | 类型 | 优先级 | eCPM预期（中东） |
|----------|------|--------|-----------------|
| 激励视频 | Rewarded Video | P0 | $3-8 |
| 插屏广告 | Interstitial | P1 | $1-3 |
|  Banner广告 | Banner | P2（暂不使用） | $0.2-0.5 |

> **注意**：本游戏不使用Banner广告。Banner会遮挡游戏区域，影响体验，与游戏品质定位不符。

#### 2.1.2 AdMob SDK 集成代码

```html
<!-- index.html 的 <head> 中添加 -->
<meta name="google-adsense-account" content="ca-app-pub-xxxxx">
```

```javascript
// 广告管理器（完整实现）
class AdMobManager {
  constructor() {
    this.rewardedAds = {};
    this.interstitialAd = null;
    this.isInitialized = false;
  }

  async init() {
    // 等待 AdMob SDK 加载
    if (typeof adsbygoogle === 'undefined') {
      await this._loadSDK();
    }

    this._preloadRewardedAds();
    this._preloadInterstitial();
    this.isInitialized = true;
  }

  _loadSDK() {
    return new Promise((resolve, reject) => {
      const script = document.createElement('script');
      script.src = 'https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-app-pub-xxxxx';
      script.async = true;
      script.crossOrigin = 'anonymous';
      script.onload = resolve;
      script.onerror = reject;
      document.head.appendChild(script);
    });
  }

  _preloadRewardedAds() {
    // 预加载3个激励广告实例（轮流使用，避免加载冷却）
    const adUnitIds = [
      'ca-app-pub-xxxxx/rewarded_unit_1',
      'ca-app-pub-xxxxx/rewarded_unit_2',
      'ca-app-pub-xxxxx/rewarded_unit_3'
    ];

    adUnitIds.forEach(unitId => {
      this.rewardedAds[unitId] = {
        ad: null,
        loaded: false,
        lastLoad: 0
      };
    });

    // 预加载第一个
    this._loadRewardedAd(adUnitIds[0]);
  }

  _loadRewardedAd(unitId) {
    if (!this.rewardedAds[unitId]) return;

    const adConfig = {
      adUnitId: unitId,
      rethrow: true
    };

    try {
      this.rewardedAds[unitId].ad = adsbygoogle.rewarded(config => ({
        ...adConfig,
        onRewarded: (reward) => this._onRewarded(reward, unitId),
        onAdDismissed: () => this._onAdDismissed(unitId),
        onAdFailedToLoad: () => this._onAdFailed(unitId),
      }));
      this.rewardedAds[unitId].loaded = true;
    } catch(e) {
      // SDK not ready yet, will retry
    }
  }

  _getNextAdUnit() {
    // 轮询选择下一个可用的广告单元
    const unitIds = Object.keys(this.rewardedAds);
    for (const unitId of unitIds) {
      if (this.rewardedAds[unitId].loaded) {
        return unitId;
      }
    }
    return unitIds[0];
  }

  _onRewarded(reward, unitId) {
    // 发放奖励
    const pending = this.pendingReward;
    if (pending) {
      this.grantReward(pending.type, pending.amount);
      this.pendingReward = null;
    }

    // 重新加载
    setTimeout(() => this._loadRewardedAd(unitId), 1000);
  }

  _onAdDismissed(unitId) {
    this.rewardedAds[unitId].loaded = false;
    setTimeout(() => this._loadRewardedAd(unitId), 30000);
  }

  _onAdFailed(unitId) {
    this.rewardedAds[unitId].loaded = false;
    setTimeout(() => this._loadRewardedAd(unitId), 60000);
  }

  // ===== 对外接口 =====

  async showRewardedAd(rewardType, amount = 1) {
    if (!this.isInitialized) {
      await this.init();
    }

    const unitId = this._getNextAdUnit();
    const ad = this.rewardedAds[unitId]?.ad;

    if (!ad) {
      showToast('Ad not ready. Please try again in a moment.');
      return false;
    }

    this.pendingReward = { type: rewardType, amount };
    ad.show();
    return true;
  }

  grantReward(type, amount) {
    switch(type) {
      case 'revive':
        game.addItem('revive', amount);
        showToast(`❤️ +${amount} Revive!`);
        break;
      case 'undo':
        game.addItem('undo', amount);
        showToast(`↩️ +${amount} Undo!`);
        break;
      case 'shuffle':
        game.addItem('shuffle', amount);
        showToast(`🔀 +${amount} Shuffle!`);
        break;
      case 'coins':
        game.addCoins(amount);
        showToast(`🪙 +${amount} coins!`);
        break;
      case 'steps':
        game.addSteps(amount);
        showToast(`👣 +${amount} steps!`);
        break;
      case 'random':
        const items = ['revive', 'undo', 'shuffle'];
        const randomItem = items[Math.floor(Math.random() * items.length)];
        game.addItem(randomItem, 1);
        showToast(`${randomItem} +1!`);
        break;
    }
  }

  _preloadInterstitial() {
    this.interstitialAd = adsbygoogle.interstitial({
      adUnitId: 'ca-app-pub-xxxxx/interstitial_unit',
      onAdDismissed: () => {
        setTimeout(() => this._loadInterstitial(), 180000);
      }
    });
  }

  maybeShowInterstitial(currentLevel) {
    const interstitialLevels = [3, 6, 9, 12, 15, 18, 21, 24, 27, 30];
    if (interstitialLevels.includes(currentLevel)) {
      if (this._canShowInterstitial()) {
        this.interstitialAd?.show();
      }
    }
  }

  _canShowInterstitial() {
    const lastShown = localStorage.getItem('sm_last_interstitial') || 0;
    return Date.now() - lastShown > 180000; // 3分钟冷却
  }
}

const admob = new AdMobManager();
```

---

## 3. 激励广告场景设计

### 3.1 广告触发场景矩阵

| 场景 | 时机 | 奖励类型 | 用户接受度 | 设计目的 |
|------|------|----------|-----------|----------|
| 首次失败 | 步数归零时 | 复活+3步 | 高 | 降低流失 |
| 每日首次 | 每日首次失败 | 随机道具×1 | 极高 | 提升DAU |
| 道具耗尽 | 道具栏为空 | 指定道具×1 | 中 | 销售转化前奏 |
| 双倍金币 | 通关结算 | 奖励×2 | 高 | ARPU提升 |
| +5步数 | 步数紧张时 | +5步 | 中高 | 提高通关率 |
| 跳过卡关 | 连续失败3次 | 直接通关 | 低 | VIP付费转化 |

### 3.2 广告UI设计

```css
/* 广告提示Banner */
.ad-banner {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 10px 16px;
  background: linear-gradient(90deg, rgba(212,175,55,0.15) 0%, rgba(212,175,55,0.05) 100%);
  border-top: 1px solid rgba(212,175,55,0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  z-index: 100;
  animation: adBannerPulse 2s ease infinite;
}

@keyframes adBannerPulse {
  0%, 100% { opacity: 0.9; }
  50% { opacity: 1; }
}

.ad-banner-text {
  font-size: 0.85rem;
  color: var(--color-primary-light);
  font-weight: 500;
}

.ad-banner-btn {
  padding: 8px 16px;
  background: var(--color-primary);
  color: var(--color-bg-dark);
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 700;
  cursor: pointer;
}
```

### 3.3 广告冷却控制

```javascript
const AD_CONFIG = {
  maxDailyRewarded: 10,
  cooldownMs: 30000, // 30秒冷却
  interstitialCooldownMs: 180000, // 3分钟冷却
  dailyLimitResetHour: 0, // 每天0点重置

  watchedToday: 0,
  lastWatchTime: 0,

  canWatchAd() {
    if (this.watchedToday >= this.maxDailyRewarded) {
      showToast('Daily ad limit reached. Come back tomorrow! 💫');
      return false;
    }
    if (Date.now() - this.lastWatchTime < this.cooldownMs) {
      showToast('Please wait before watching another ad.');
      return false;
    }
    return true;
  },

  recordAdWatch() {
    this.watchedToday++;
    this.lastWatchTime = Date.now();
    Storage.save('sm_ad_daily', {
      count: this.watchedToday,
      date: new Date().toDateString()
    });
  }
};
```

---

## 4. 内购设计（AED金币系统）

### 4.1 定价哲学

| 市场 | 参照物 | 价格锚定 |
|------|--------|----------|
| 阿联酋 AED | 一杯咖啡 | AED 8 = 适中包 |
| 沙特 SAR | 一顿快餐 | SAR 10 ≈ AED 10 |
| 卡塔尔 QAR | 一杯奶茶 | QAR 10 ≈ AED 10 |

> **中东定价策略**：中东用户对价格敏感度显著低于欧美，但AED 30（≈$8）仍是心理分界线。大额包（$8）定价不超过当地一杯星巴克。

### 4.2 内购包配置

```javascript
const IAP_PACKAGES = [
  {
    id: 'coin_small',
    name: 'Small Pouch',
    nameAr: 'كيس صغير',
    aed: 3.00,
    coins: 200,
    bonus: 0,
    costUsd: 0.82,
    tagline: 'Perfect for beginners!',
    color: '#8B7355'
  },
  {
    id: 'coin_medium',
    name: 'Medium Pouch',
    nameAr: 'كيس متوسط',
    aed: 8.00,
    coins: 600,
    bonus: 0,
    costUsd: 2.18,
    tagline: 'Most popular! ⭐',
    color: '#C0C0C0',
    badge: 'POPULAR'
  },
  {
    id: 'coin_large',
    name: 'Large Pouch',
    nameAr: 'كيس كبير',
    aed: 15.00,
    coins: 1200,
    bonus: 100,
    costUsd: 4.08,
    tagline: 'Best value! +100 bonus',
    color: '#D4AF37',
    badge: 'BEST VALUE'
  },
  {
    id: 'coin_sultan',
    name: "Sultan's Chest",
    nameAr: 'صندوق السلطان',
    aed: 30.00,
    coins: 2800,
    bonus: 400,
    costUsd: 8.17,
    tagline: "For true Sultans! +400 bonus",
    color: '#FFD700',
    badge: 'SULTAN PACK'
  }
];

// Apple/Google内购回调处理
function handleIAPPurchase(productId, transactionId) {
  const pkg = IAP_PACKAGES.find(p => p.id === productId);
  if (!pkg) return;

  // 验证 receipt（生产环境必须服务端验证）
  verifyReceipt(transactionId).then(verified => {
    if (verified) {
      addCoins(pkg.coins + pkg.bonus);
      showIAPSuccess(pkg);
      trackEvent('iap_purchase', {
        productId,
        amount: pkg.aed,
        currency: 'AED'
      });
    }
  });
}
```

### 4.3 内购UI设计

```javascript
// 内购商店组件
function renderIAPShop() {
  const container = document.getElementById('iap-container');
  container.innerHTML = `
    <div class="iap-header">
      <h2>🪙 Coin Shop</h2>
      <p>Your balance: <strong>${coins} 🪙</strong></p>
    </div>
    <div class="iap-grid">
      ${IAP_PACKAGES.map(pkg => `
        <div class="iap-card ${pkg.badge ? 'has-badge' : ''}" onclick="purchasePackage('${pkg.id}')">
          ${pkg.badge ? `<span class="iap-badge">${pkg.badge}</span>` : ''}
          <div class="iap-icon">🪙</div>
          <div class="iap-name">${pkg.name}</div>
          <div class="iap-coins">${pkg.coins + pkg.bonus} 🪙</div>
          <div class="iap-price">${pkg.aed} AED</div>
          <div class="iap-tagline">${pkg.tagline}</div>
        </div>
      `).join('')}
    </div>
  `;
}

const iapStyles = `
  .iap-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
    padding: 16px;
  }
  .iap-card {
    background: var(--color-bg-elevated);
    border: 1px solid var(--color-border);
    border-radius: 16px;
    padding: 16px;
    text-align: center;
    cursor: pointer;
    position: relative;
    transition: all 0.2s ease;
  }
  .iap-card:hover {
    border-color: var(--color-primary);
    transform: translateY(-2px);
  }
  .iap-card.has-badge {
    border-color: var(--color-primary);
  }
  .iap-badge {
    position: absolute;
    top: -10px;
    left: 50%;
    transform: translateX(-50%);
    background: var(--color-primary);
    color: var(--color-bg-dark);
    font-size: 0.6rem;
    font-weight: 700;
    padding: 2px 8px;
    border-radius: 10px;
    white-space: nowrap;
  }
  .iap-icon { font-size: 2.5rem; margin-bottom: 8px; }
  .iap-name { font-weight: 600; font-size: 0.9rem; margin-bottom: 4px; }
  .iap-coins { color: var(--color-primary); font-weight: 700; font-size: 1.1rem; }
  .iap-price { font-size: 0.8rem; color: var(--color-text-secondary); margin: 4px 0; }
  .iap-tagline { font-size: 0.65rem; color: var(--color-text-muted); }
`;
```

---

## 5. 金币消耗场景

### 5.1 消耗场景完整列表

| 场景 | 消耗金币 | 触发频率 | 必要性 | 用户价值 |
|------|----------|----------|--------|----------|
| 撤回（Undo） | 50 | 中 | 中 | 后悔药 |
| 洗牌（Shuffle） | 80 | 中 | 中 | 破局工具 |
| 复活（Revive） | 200 | 低 | 低 | 续命 |
| 跳过卡关 | 1000 | 极低 | 极低 | 氪金玩家救星 |
| 沙漠之夜主题 | 200 | 低 | 无 | 收藏 |
| 海洋探索主题 | 300 | 低 | 无 | 收藏 |
| 皇室奢华主题 | 500 | 低 | 无 | 收藏 |

### 5.2 付费转化漏斗

```
出现卡关（第1次失败）
   ↓
看广告复活（免费）
   ↓
再次卡关（第2次失败）
   ↓
购买道具（50-200金币）
   ↓
再次卡关（连续3次）
   ↓
购买金币包（AED 8-30）
   ↓
VIP用户（长期留存，高LTV）
```

### 5.3 价格心理学应用

```javascript
// 道具价格展示策略
function renderItemSlot(item) {
  const coinPrice = COIN_COSTS[item.id];
  const hasItem = items[item.id] > 0;

  if (hasItem) {
    return `
      <div class="item-slot" onclick="useItem('${item.id}')">
        <span class="item-emoji">${item.emoji}</span>
        <span class="item-count">${items[item.id]}</span>
        <span class="item-name">${item.name}</span>
      </div>
    `;
  } else {
    // 无道具时显示价格，引导购买
    return `
      <div class="item-slot empty" onclick="buyItemWithCoins('${item.id}')">
        <span class="item-emoji">${item.emoji}</span>
        <span class="item-price">${coinPrice} 🪙</span>
        <span class="item-name">${item.name}</span>
        <span class="buy-hint">Tap to buy</span>
      </div>
    `;
  }
}

// 广告+金币混合购买
function buyItemWithCoins(itemId) {
  const cost = COIN_COSTS[itemId];
  if (coins >= cost) {
    coins -= cost;
    addItem(itemId, 1);
    updateCoinsDisplay();
    playSFX('purchase');
    showToast(`${itemId} purchased!`);
  } else {
    // 金币不足，推荐内购
    showModal('Need more coins?', [
      { text: 'Watch Ad (Free!)', action: () => admob.showRewardedAd(itemId, 1) },
      { text: 'Buy Coins', action: () => showIAPShop() }
    ]);
  }
}
```

---

## 6. 变现时序

### 6.1 用户生命周期变现策略

```
Day 1 ────────────────────────────────────────── Day 30+
│                                                   │
│ ←────── 习惯养成期（广告为主）────→               │
│                                                   │
Day 1-7:                                           Day 8-30:
• 前3关几乎免费通关（体验爽感）                    • 卡关开始出现
• 第5关首次卡关 → 看广告复活                      • 道具购买开始
• 第10关再次卡关 → 广告变现                        • 首次内购转化
• 每日首次广告 → 建立习惯                          • 大额礼包推荐
│                                                   │
│ ←──────── 深度付费期（内购+广告）────→            │
│                                                   │
Day 8-14:                                          Day 15-30:
• 付费道具包购买                                   • 主题皮肤购买
• AED 8-15内购包首次转化                          • Sultan包推荐
• ARPU快速提升                                     • LTV稳定在$0.5+
│                                                   │
│ ←───────── 传播裂变期（免费为主）────────→       │
│                                                   │
Day 5+:                                            Day 30+:
• 炫耀截图 → WhatsApp分享                         • 邀请好友（免费金币）
• 分享带来新用户（K>1.2）                         • 口碑传播（K因子持续）
• 病毒传播带来自然增长                             • 品牌价值积累
```

### 6.2 变现指标监控

```javascript
const MONETIZATION_KPIS = {
  // 广告指标
  adWatchRate: 'DAU中看过广告的用户比例（目标: >40%）',
  adARPU: '每DAU的广告收入（目标: >$0.15）',
  avgAdsPerUser: '人均广告观看次数（目标: >3次/日）',

  // 内购指标
  payingUserRate: '付费用户比例（目标: >1.5%）',
  firstPurchaseDay: '首次付费天数（目标: Day 8-14）',
  avgOrderValue: '平均订单金额（目标: >$2.50 AED 8+）',
  IAPLTV: '内购生命周期价值（目标: >$0.50）',

  // 综合指标
  totalARPU: '综合ARPU（目标: >$0.65）',
  conversionFunnel: {
    installs: 1000,
    d1Active: 350,     // D1留存 35%
    d7Active: 150,     // D7留存 15%
    everPaid: 15,      // 付费用户 1.5%
    avgRevenue: 0.65   // 美元
  }
};
```

---

*变现设计文档版本：v1.0 | 最后更新：2026-03-29*
