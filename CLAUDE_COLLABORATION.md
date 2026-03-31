# CC-OC 双Agent协作系统开发手册

> 文档版本：v1.0
> 最后更新：2026-03-31
> 维护者：丞相（主Agent）、CC（终端Claude）

---

## 一、项目概述

### 1.1 目标
实现 OpenClaw 主Agent（OC）与终端 Claude Code（CC）之间的实时双向通信，使两位Agent能够：
- 同时看到用户消息
- 实时讨论问题
- 协同完成任务

### 1.2 架构概览

```
用户 ←→ OpenClaw（OC） ←→ relay-server ←→ 终端Claude（CC）
                      （腾讯云）
                      ws://150.158.119.114:3001
```

### 1.3 核心文件

| 文件 | 行数 | 作用 |
|------|------|------|
| relay-server/src/index.js | 772 | 主服务，AgentMQ消息队列核心 |
| relay-server/src/gateway-bridge.js | 552 | Gateway WebSocket桥接 |
| relay-server/src/api/pair.js | 242 | 配对码API |
| relay-server/src/relay-client/index.js | 523 | CC的消息发送客户端 |
| relay-server/src/relay-client/cli.js | 144 | CC的命令行工具 |
| cc_oc_watch.sh | - | OC轮询脚本，每3秒检查CC回复 |

---

## 二、消息队列设计（AgentMQ）

### 2.1 队列结构

```javascript
// 每条消息格式
{
  id: "msg_xxx",           // 唯一ID，CC生成
  from: "cc-terminal",    // 发送者ID
  to: "oc-agent",          // 接收者ID
  type: "task|command|reply",
  payload: "...",          // 消息内容
  timestamp: 1743432000000
}
```

### 2.2 两个队列
- `cc_to_oc`：CC发往OC的消息
- `oc_to_cc`：OC发往CC的消息

### 2.3 操作接口

| 接口 | 方法 | 说明 |
|------|------|------|
| `/api/messages/send` | POST | 发送消息 |
| `/api/messages/cc/to/oc` | GET | CC获取发给自己的消息（dequeue） |
| `/api/messages/oc/to/cc` | GET | OC获取发给自己的消息 |

---

## 三、部署信息

### 3.1 服务器
- **IP**：150.158.119.114
- **端口**：3001
- **进程管理**：PM2（openclaw-relay）
- **启动命令**：`cd ~/openclaw/OpenClawTrader/relay-server && node src/index.js`
- **PM2配置**：pm2.config.js

### 3.2 环境变量
```
RELAY_SERVER=http://localhost:3001
CC_ID=cc-terminal
OC_ID=oc-agent
```

---

## 四、通信流程

### 4.1 OC → CC
1. OC调用 `curl -X POST http://localhost:3001/api/messages/send` 发送消息
2. 消息进入 `cc_to_oc` 队列
3. CC通过 `cc_oc_watch.sh` 轮询或直接调用 `/api/messages/cc/to/oc` 获取

### 4.2 CC → OC
1. CC调用 `curl -X POST http://localhost:3001/api/messages/send -d '{"to":"oc-agent",...}'`
2. 消息进入 `oc_to_cc` 队列
3. OC通过 `cc_oc_watch.sh` 轮询获取
4. OC将CC的回复合并到自己的对话中，发给用户

---

## 五、watch脚本

### 5.1 cc_oc_watch.sh
- **位置**：`~/openclaw/OpenClawTrader/cc_oc_watch.sh`
- **轮询间隔**：3秒
- **作用**：OC端持续监控relay-server，等待CC的回复
- **运行**：`nohup bash cc_oc_watch.sh > /tmp/cc_oc_watch.log 2>&1 &`

### 5.2 监控类型
- `task`：需要执行的任务
- `command`：需要执行的命令
- `reply`：对之前消息的回复

---

## 六、开发规则

### 6.1 文档唯一性
**死命令**：所有开发记录只能写在 `CLAUDE_COLLABORATION.md`，禁止创建重复文档。

### 6.2 修改记录
每次修改必须记录：
```
时间 | 修改人 | 修改内容 | 修改原因
```

### 6.3 通信规则
- OC发送给CC后，等CC回复再继续
- CC发送给OC后，通过watch脚本自动获取OC的合并回复
- 双向通信是半双工（需要轮询）

---

## 七、已知问题

### 7.1 gateway-bridge token失效
- **问题**：ClawPilot重启后gateway-bridge token过期
- **影响**：OpenClawTrader App配对功能不可用
- **修复**：运行 `openclaw configure` 重新配置

### 7.2 半双工限制
- CC→OC目前需要OC主动轮询才能获取
- 暂无CC主动推送机制

---

## 八、腾讯云服务器信息

```
IP: 150.158.119.114
SSH: root / Nzf9744002009
端口: 3001
```

---

## 九、修改记录

| 时间 | 修改人 | 修改内容 |
|------|--------|----------|
| 2026-03-31 19:41 | 丞相 | 初始版本创建 |
