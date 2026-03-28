# 部署指南：接入微信 / 企业微信

本指南帮助您将高考志愿Agent部署为微信可用的AI助手，面向普通家长。

---

## 方案选择

| 方案 | 适用场景 | 难度 | 费用 |
|------|---------|------|------|
| **方案A：腾讯云一键部署（推荐）** | 个人/小团队，不想折腾服务器 | ⭐ | ~199元/年 |
| **方案B：OpenClaw + openclaw-china插件** | 有服务器基础，想完全掌控 | ⭐⭐⭐ | 服务器费用 |
| **方案C：Dify/FastGPT云端（无需服务器）** | 只需要对话功能，不需要微信接入 | ⭐⭐ | 按用量 |

---

## 方案A：腾讯云一键部署（推荐新手）

### 第一步：购买服务器

1. 进入腾讯云轻量应用服务器控制台
2. 选择**应用镜像 → OpenClaw**
3. 配置选2GiB内存以上（约199元/年的基础套餐足够）
4. 完成购买

### 第二步：配置大模型

OpenClaw支持国内主流模型，推荐以下选项：

- **免费试用**：通义千问（阿里百炼平台，有免费额度）
- **性价比**：DeepSeek API（成本极低，中文能力强）
- **效果最好**：Claude API（需要海外支付方式）

在OpenClaw控制台 → 执行命令 → 配置API Key。

### 第三步：安装高考志愿Agent Skill

```bash
# 在OpenClaw控制台的SSH终端中执行
openclaw skills install https://github.com/YOUR_USERNAME/gaokao-mentor/raw/main/skills/gaokao-mentor.skill

# 验证安装
openclaw skills list
# 应看到 gaokao-mentor v1.0.0
```

### 第四步：接入微信

**企业微信机器人（推荐·支持群聊）**：

1. 打开企业微信 → 工作台 → 智能机器人 → 创建机器人
2. 获取 Bot ID 和 Secret
3. 在OpenClaw控制台 → 渠道配置 → 企业微信 → 填入信息
4. 将机器人加入群聊，@机器人即可对话

**微信公众号（服务号·面向更广泛用户）**：

1. 注册微信服务号（需企业资质）
2. 在公众号后台 → 开发 → 基本配置 → 获取AppID和AppSecret
3. 在OpenClaw控制台 → 渠道配置 → 微信公众号 → 填入信息
4. 粉丝发消息即可触发对话

---

## 方案B：OpenClaw + openclaw-china（完全掌控）

### 前提条件

- 一台有公网IP的服务器（国内云服务器即可）
- 已安装Node.js 18+
- 基本的命令行操作能力

### 安装步骤

```bash
# 安装 OpenClaw
npm install -g openclaw

# 安装 openclaw-china（微信支持插件）
openclaw plugins install @tencent-weixin/openclaw-weixin

# Windows用户如果上面命令报错，使用：
# openclaw plugins install "@tencent-weixin/openclaw-weixin"

# 克隆本知识库
git clone https://github.com/YOUR_USERNAME/gaokao-mentor.git
cd gaokao-mentor

# 安装 Skill
openclaw skills install ./skills/gaokao-mentor.skill

# 启动服务
openclaw start --port 3000
```

### 微信接入配置

参考 [openclaw-china 文档](https://github.com/BytePioneer-AI/openclaw-china) 完成具体渠道配置。

---

## 方案C：Dify 云端部署（最简单）

如果只需要网页/API访问，不需要微信接入：

1. 注册 [Dify Cloud](https://dify.ai)
2. 创建新应用 → 知识库应用
3. 上传 `knowledge/` 下所有 `.md` 文件
4. 在「提示词」中粘贴 `prompts/system_prompt.md` 的内容
5. 发布即可使用

---

## 使用效果验证

部署完成后，可以用以下测试问题验证效果：

**测试1：基础信息收集**
> "我孩子今年高考，我想咨询志愿填报"

期望行为：Agent应先询问省份、分数、选科，而不是直接给建议。

**测试2：可行集约束**
> "我孩子山东695分，想学计算机，北大行吗"

期望行为：Agent应先说明500分在山东的位次范围，在可行集内给建议，而不是直接说北大。

**测试3：目标倒推**
> "我孩子物化生，550分，不知道学什么好"

期望行为：Agent应先问"希望孩子毕业后达到什么状态"，再做专业分析。

**测试4：特长识别**
> "我孩子是体育特长生，国家一级运动员，普通高考只有480分左右"

期望行为：Agent应先提示评估高水平运动员招生通道，而不是直接用480分做可行集。

---

## 常见问题

**Q：用什么模型效果最好？**  
A：中文理解和推理能力方面，DeepSeek-V3和Claude Sonnet系列效果最好。如果有成本限制，通义千问qwen-plus也足够日常使用。

**Q：知识库怎么保持更新？**  
A：每年高考后，社区会更新录取位次数据和就业形势分析。关注本仓库的Release即可获得更新通知。执行 `git pull` 后重新安装skill即可更新。

**Q：可以同时接多个微信渠道吗？**  
A：可以，OpenClaw支持同时配置企业微信、公众号、QQ等多个渠道。

**Q：普通家长会用吗？**  
A：企业微信群聊模式下，家长只需要在已有的微信群里@机器人，学习成本几乎为零。公众号模式下，关注后直接发消息即可。
