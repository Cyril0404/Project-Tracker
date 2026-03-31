# Sheep Match — 推广方案文档（MARKETING.md）
**版本**: v1.0 | **日期**: 2026-03-29

---

## 1. 病毒传播设计

### 1.1 K因子分解

```
K = i × c

K: 病毒系数（目标 > 1.2）
i: 每个用户平均邀请/分享的新用户数
c: 分享内容到实际转化的比率

目标：
- K = 1.2 = 每个用户带来1.2个新用户
- 1000用户 → 1200用户 → 1440用户 → ...
- 5轮传播: 1000 → 1200 → 1440 → 1728 → 2073 → 2488 (2.5倍增长)
```

### 1.2 病毒传播机制

| 传播机制 | 触发点 | 分享形式 | 转化率 |
|----------|--------|----------|--------|
| 截图炫耀 | Level通关 | 图片+文案 | 8-15% |
| 失败共情 | 连续失败3次 | 图片+文案 | 5-10% |
| 邀请挑战 | 任意时刻 | 链接 | 3-8% |
| 成就解锁 | 达成里程碑 | 图片+文案 | 6-12% |
| 排行榜PK | 查看排行榜 | 截图 | 4-8% |

### 1.3 截图分享核心设计

```javascript
// 通关炫耀截图生成
function generateWinScreenshot(level, stars) {
  const canvas = document.createElement('canvas');
  canvas.width = 600;
  canvas.height = 800;
  const ctx = canvas.getContext('2d');

  // 背景
  ctx.fillStyle = '#0D0D1A';
  ctx.fillRect(0, 0, 600, 800);

  // 顶部金色渐变条
  const grad = ctx.createLinearGradient(0, 0, 600, 0);
  grad.addColorStop(0, '#D4AF37');
  grad.addColorStop(0.5, '#F5D76E');
  grad.addColorStop(1, '#D4AF37');
  ctx.fillStyle = grad;
  ctx.fillRect(0, 0, 600, 80);

  // 标题
  ctx.fillStyle = '#0D0D1A';
  ctx.font = 'bold 36px Poppins, sans-serif';
  ctx.textAlign = 'center';
  ctx.fillText('🎉 LEVEL COMPLETE! 🎉', 300, 52);

  // 关卡信息
  ctx.fillStyle = '#FFFFFF';
  ctx.font = 'bold 48px Poppins';
  ctx.fillText(`Level ${level}`, 300, 200);

  // 星级
  ctx.font = '64px sans-serif';
  const starsStr = '⭐'.repeat(stars) + '☆'.repeat(3 - stars);
  ctx.fillText(starsStr, 300, 300);

  // 装饰图案
  ctx.font = '80px sans-serif';
  ctx.fillText('🐪', 150, 450);
  ctx.fillText('👑', 300, 450);
  ctx.fillText('💎', 450, 450);

  // 品牌水印
  ctx.fillStyle = '#D4AF37';
  ctx.font = 'bold 28px Poppins';
  ctx.fillText('🐪 SHEEP MATCH', 300, 700);

  // URL
  ctx.fillStyle = 'rgba(255,255,255,0.5)';
  ctx.font = '18px Poppins';
  ctx.fillText('sheepmatch.ae', 300, 750);

  return canvas.toDataURL('image/png');
}

// 分享到WhatsApp
function shareToWhatsApp(imageDataUrl, text) {
  // 方案1：Web Share API（移动端推荐）
  if (navigator.share) {
    fetch(imageDataUrl).then(r => r.blob()).then(blob => {
      const file = new File([blob], 'sheepmatch.png', { type: 'image/png' });
      navigator.share({
        title: 'Sheep Match',
        text: text,
        files: [file]
      }).catch(() => {
        fallbackShare(imageDataUrl, text);
      });
    });
  } else {
    fallbackShare(imageDataUrl, text);
  }
}

function fallbackShare(imageDataUrl, text) {
  // 降级方案：复制文案+图片到剪贴板
  copyToClipboard(text + '

https://sheepmatch.ae');
  showToast('Link copied! Paste in WhatsApp 👆');
  // 在Canvas上显示图片供用户截图
  showSharePreview(imageDataUrl);
}
```

---

## 2. WhatsApp分享文案设计

### 2.1 文案库

#### 通关分享文案（英文）
```
🇦🇪 I just beat Level {N} in Sheep Match!
Can you do better? 😤

🐪 Only 0.1% reach Level 30...
Can you beat me? 👑

💎 Got {stars}⭐ on Level {N}!
Try to beat my score! ⭐

🏆 I'm in the TOP 10% worldwide!
Can you beat me? 👇
sheepmatch.ae
```

#### 失败共情文案（英文）
```
😂 Stuck on Level {N}... Who's brave enough to try?

💀 Level {N} is IMPOSSIBLE!
Someone please help! 😭

😤 I've tried 50 times on Level {N}!
Prove you're better than me! 💪
sheepmatch.ae
```

#### 阿拉伯语文案
```
🇦🇪 لقد اجتزت المستوى {N} في Sheep Match!
هل تستطيع أن تفعل أفضل مني؟ 😤

💎 حصلت على {stars}⭐ في المستوى {N}!
حاول التغلب على نتيجتي! ⭐

😂 عالق في المستوى {N}...
من الشجاع الذي يجرب؟ 😭
sheepmatch.ae
```

### 2.2 WhatsApp Deep Link

```javascript
// WhatsApp直接调起链接
function getWhatsAppLink(text, phoneNumber = null) {
  const encoded = encodeURIComponent(text);
  if (phoneNumber) {
    return `https://wa.me/${phoneNumber}?text=${encoded}`;
  }
  return `https://api.whatsapp.com/send?text=${encoded}`;
}

// 在网页上生成可点击的WhatsApp分享按钮
function createWhatsAppShareButton(text, imageUrl) {
  const link = getWhatsAppLink(text);
  return `
    <a href="${link}" target="_blank" class="share-btn whatsapp-btn">
      <img src="whatsapp-icon.svg" width="24" height="24">
      Share via WhatsApp
    </a>
  `;
}
```

---

## 3. TikTok/Instagram Reels内容策略

### 3.1 短视频内容矩阵

| 内容类型 | 时长 | 目的 | 示例 |
|----------|------|------|------|
| 通关瞬间 | 15-30s | 病毒传播 | "I finally beat Level 30! 🎉" |
| 失败集锦 | 30s | 共情互动 | "Level 25 is literally impossible 💀" |
| 攻略教程 | 60s | SEO/留存 | "The secret trick to beat Level 20" |
| 对比挑战 | 45s | 社交PK | "My friend vs me - who wins?" |
| 幕后花絮 | 30s | 人设建立 | "I spent 200 hours on this game" |

### 3.2 TikTok/Reels内容脚本示例

#### 脚本1: 通关庆祝（15秒）
```
[0-3s]   📱 手机屏幕录制：Level 30 最后一对消除
[3-6s]   🎉 屏幕慢动作：⭐⭐⭐ 出现
[6-10s]  😂 反应镜头：朋友/自己的惊喜表情
[10-13s] 🗣️ 字幕：文字 "Only 0.1% make it..."
[13-15s] 🔗 链接：sheepmatch.ae + "Tap the link!"
```

#### 脚本2: 失败共情（30秒）
```
[0-5s]   📱 屏幕录制：Level 25 步数归零瞬间
[5-10s]  😭 表情特写：无奈+崩溃
[10-18s] 📊 数据展示：通过率 "Only 0.1% pass Level 25"
[18-25s] 🗣️ 挑战字幕："Tag someone who can't beat this"
[25-30s] 🔗 链接 + CTA
```

### 3.3 UGC激励机制

```javascript
// 分享游戏截图赢金币活动
const UGC_CAMPAIGN = {
  name: 'Share & Earn',
  rules: [
    'Share your game screenshot on TikTok/Instagram',
    'Use hashtag #SheepMatch and tag @sheepmatchgame',
    'Get 50 bonus coins for each valid post',
    'Top 10 posts each week win 500 bonus coins!'
  ],
  maxClaim: 200, // 每次活动最多领200金币
  hashtag: '#SheepMatch #SheepMatchGame'
};
```

---

## 4. 中东本地化推广方案

### 4.1 区域市场优先级

| 市场 | 优先级 | 理由 | 本地化重点 |
|------|--------|------|-----------|
| 阿联酋 🇦🇪 | P0 | 高ARPU，品牌效应强 | 英语+阿拉伯语，奢华主题 |
| 沙特 🇸🇦 | P0 | 用户基数大，增长快 | 阿拉伯语优先，保守风格 |
| 卡塔尔 🇶🇦 | P1 | 高人均GDP，极高付费力 | 精英营销，高端礼品 |
| 科威特 🇰🇼 | P1 | 高互联网渗透率 | 本地KOL合作 |
| 巴林 🇧🇭 | P2 | 小而精，传播集中 | 口碑营销 |
| 阿曼 🇴🇲 | P2 | 新兴市场，增长潜力 | 早期占位 |

### 4.2 线下推广（阿联酋）

| 活动 | 形式 | 预算 | 预期效果 |
|------|------|------|----------|
| 迪拜Mall 游戏角 | 线下展位+试玩 | AED 50,000 | 直接获客5,000+ |
| 商场大屏广告 | LED屏幕轮播 | AED 30,000 | 品牌曝光200万+人次 |
| 地铁广告 | Dubai Metro拉手广告 | AED 20,000 | 覆盖通勤人群 |
| 大学校园快闪 | 5所大学巡展 | AED 15,000 | 年轻用户渗透 |

### 4.3 本地支付整合

```javascript
// 中东本地支付方式（必须支持）
const PAYMENT_METHODS = {
  applePay: { regions: ['AE', 'SA', 'QA'], icon: '🍎' },
  googlePay: { regions: ['AE', 'SA', 'QA', 'KW'], icon: '📱' },
  card: { regions: ['ALL'], icon: '💳' }, // Visa/Mastercard
  stcPay: { regions: ['SA'], icon: '📲' }, // 沙特本地支付
  OmanNet: { regions: ['OM'], icon: '🇴🇲' }
};
```

---

## 5. KOL合作策略

### 5.1 KOL分层

| 层级 | 粉丝量 | 合作成本 | 数量目标 | 重点平台 |
|------|--------|----------|----------|----------|
| 头部KOL | 100万+ | AED 50,000+ | 2-3人 | YouTube, TikTok |
| 腰部KOL | 10-100万 | AED 5,000-30,000 | 10-15人 | TikTok, Instagram |
| 尾部KOL | 1-10万 | AED 1,000-5,000 | 30-50人 | TikTok |
| 微KOL | 1,000-10,000 | 产品置换 | 100+ | Instagram, Twitter |

### 5.2 重点KOL类型（中东市场）

| 类型 | 特点 | 合作形式 |
|------|------|----------|
| 游戏博主 | 专注手游测评 | Let's Play视频 + 直播 |
| 搞笑博主 | 制造病毒内容 | 失败集锦 + 挑战赛 |
| 生活vlogger | 记录日常生活 | 自然植入 |
| 财经/商业 | 高净值人群 | 精英挑战版本 |
| 女性KOL | 渗透女性市场 | 特别皮肤包推广 |

### 5.3 KOL合作Brief

```markdown
# Sheep Match X [KOL Name] 合作Brief

## 游戏简介
Sheep Match是一款中东主题的三消益智游戏，仅0.1%玩家能通关。
WhatsApp直接玩，无需下载。

## 合作内容
1. **视频内容**：录制一期"Let's Play" Sheep Match
   - 尝试通关（可选：挑战Level 25）
   - 真实反应（成功或失败都可以）
   - 15-60秒均可
2. **CTA**：描述文字中添加 "Play now: sheepmatch.ae"
3. **发布平台**：TikTok / Instagram Reels / YouTube Shorts
4. **Hashtag**：#SheepMatch #SheepMatchGame

## 合作权益
- 独家游戏礼包（内含道具+金币）
- 视频链接永久挂载
- Sheep Match官方账号转发合作内容
- 首月内购分成（可选）

## 禁止事项
- 不承诺特定排名或通关结果
- 不能暗示游戏有作弊途径
- 不得修改游戏截图或数据

## 联系方式
Email: kol@sheepmatch.ae
WhatsApp Business: +971 XX XXX XXXX
```

---

## 6. 上线前预热计划

### 6.1 预热时间表（上线前4周）

```
Week -4 ─────────────────────────────────────────── Week 0
│                                                    │
│ ←────────── 蓄水期 (Week -4 to -3) ──────────→    │
│                                                    │
│ Week -4:                                           │
│ • KOL保密协议签署                                   │
│ • 首批KOL内测账号发放                               │
│ • 媒体沟通稿准备                                   │
│                                                    │
│ Week -3:                                           │
│ • KOL视频陆续发布（保密）                          │
│ • TikTok #SheepMatch 话题建立                      │
│ • 游戏官网/落地页上线                              │
│ • 预约注册开放                                     │
│                                                    │
│ ←────────── 爆发期 (Week -2 to -1) ──────────→    │
│                                                    │
│ Week -2:                                           │
│ • 官方社媒账号开通                                  │
│ • 悬念预告视频发布                                  │
│ • KOL视频集中爆发                                  │
│ • PR新闻稿发布（TechCrunch Arabian Gulf等）         │
│                                                    │
│ Week -1:                                           │
│ • 倒计时活动启动                                   │
│ • 提前注册用户发放预热礼包（道具）                  │
│ • WhatsApp群聊种子用户招募                         │
│                                                    │
│ ←────────── 上线日 (Week 0 / Day 1) ──────────→    │
│                                                    │
│ Day 1:                                             │
│ • 游戏正式上线（GitHub Pages + 应用商店）          │
│ • 全渠道推广启动                                   │
│ • KOL直播通关挑战（实时互动）                      │
│ • 数据监控启动                                     │
```

### 6.2 预热素材清单

| 素材 | 数量 | 规格 | 用途 |
|------|------|------|------|
| 游戏Logo | 3种 | SVG+PNG | 多场景使用 |
| 游戏截图 | 20张 | 1080×1920 | 应用商店+社媒 |
| 游戏视频 | 3条 | 15s/30s/60s | TikTok/Reels/YouTube |
| Icon图标 | 1套 | 多种分辨率 | 应用商店 |
| KOL素材包 | 1份 | ZIP | 发送给KOL |
| 新闻稿 | 2版 | 英文+阿拉伯语 | PR发布 |

### 6.3 上线当天作战室

```javascript
// 上线当天实时监控Dashboard
const LAUNCH_DASHBOARD = {
  metrics: [
    { name: 'UV',     label: '访客数',     target: 50000,  color: '#D4AF37' },
    { name: 'DAU',    label: '日活用户',    target: 20000,  color: '#4A7DB8' },
    { name: 'Shares', label: 'WhatsApp分享', target: 5000, color: '#27AE60' },
    { name: 'Revenue',label: '当日收入',   target: 10000,  color: '#E74C3C' }
  ],

  // 每5分钟更新一次
  updateInterval: 300000,

  // 告警阈值
  alerts: {
    uvLow: { threshold: 5000, message: 'UV低于预期，请检查推广投放' },
    errorRate: { threshold: 0.05, message: '错误率>5%，请检查服务状态' },
    revenueLow: { threshold: 500, message: '收入低于预期' }
  }
};
```

---

## 7. 社交媒体运营计划

### 7.1 内容日历（上线后前30天）

| 日期 | 平台 | 内容类型 | 内容描述 |
|------|------|----------|----------|
| D+1 | IG Reels | 庆典 | 上线庆祝 + 最高通关者公布 |
| D+3 | TikTok | 挑战 | #SheepMatchChallenge |
| D+7 | WhatsApp | 活动 | 邀请好友得金币活动启动 |
| D+10 | IG Story | 问答 | KOL AMA (Ask Me Anything) |
| D+14 | TikTok | 攻略 | Level 1-10 速通教程 |
| D+21 | All | 里程碑 | 庆祝10万用户 + 抽奖 |
| D+30 | YouTube | 纪录片 | 上线30天回顾纪录片 |

### 7.2 Hashtag策略

```
主标签：
#SheepMatch          — 品牌标签（强制所有帖子）
#SheepMatchGame      — 品牌全称（SEO优化）
#Match3Puzzle        — 游戏类型标签（品类流量）
#أفراس_الم sheepgame — 阿拉伯语标签

挑战标签：
#SheepMatchChallenge — 官方挑战赛
#BeatThe0.1Percent   — 0.1%通关挑战

地区标签：
#UAE #Dubai          — 阿联酋本地流量
#Saudi #Riyadh       — 沙特本地流量
#GulfGames           — 海湾游戏圈
```

---

*推广方案文档版本：v1.0 | 最后更新：2026-03-29*
