# 一键双语功能规格

## 核心交互
- 三个语言模式：🇨🇳 中文 / 🇺🇸 English / 🔤 双语
- 点击即切换，无需刷新，localStorage 记忆偏好

## 双语显示规则
- **中文模式**：纯中文原文
- **英文模式**：纯英文翻译
- **双语模式**：段落级对照（中→英交替显示，或左右分栏）

## 布局
```
[语言切换] [章节选择 ▾] [◀ 上一章] [下一章 ▶]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
              阅读区域
         (根据语言模式渲染)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## 内容来源
- 路径：`/content/xiyouji/` 目录
- 中文原文：`ch_XXX.md`（服务器本地）
- 英文翻译：`/translations/xiyouji-en/bilingual_en/ch_XXX_en.md`（动态加载）
- 西班牙文：`/translations/xiyouji-en/bilingual_es/ch_XXX_es.md`

## 技术实现
- fetch() 动态加载章节内容
- DOM 替换渲染（无刷新）
- localStorage 存储 lang preference
- 响应式：桌面端左右分栏，移动端交替段落
