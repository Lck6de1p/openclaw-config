# OpenClaw 搭建流程总结

## 概述

OpenClaw 是一个强大的 AI 助手平台，本指南总结b并成功安装和配置 OpenClaw 的详细步骤。

## 准备工作

### 1. 系统要求
- 操作系统：macOS (Apple Silicon) 或 Linux
- Node.js 版本：> 22 或更高版本（本机使用22版本）
- npm 或 yarn 包管理器
- Git 版本控制工具

### 2. 环境检查
在终端中运行以下命令检查环境：
```bash
node --version
npm --version
git --version
```

## 安装流程

### 1. 安装 OpenClaw
使用 npm 全局安装 OpenClaw：
```bash
npm install -g openclaw
```

### 2. 初始化 OpenClaw
运行初始化命令：
```bash
 openclaw onboard
```

按照指令提示，按照要求填写信息。
[选择对应的模型](./image.png)


如果你的模型不在选择范围内就先随便选一个，安装完后去配置文件中修改。

OpenClaw 的配置文件位于` ~/.openclaw/openclaw.json `
provider 配置：

```json

// 我选的火山云的lite模型，首月9.9元。
"models": {
    "providers": {
      "doubao": {
        "baseUrl": "https://ark.cn-beijing.volces.com/api/coding/v3",
        "apiKey": "YOUR_API_KEY",
        "api": "openai-completions",
        "models": [
          {
            "id": "ark-code-latest",
            "name": "ark-code-latest"
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "doubao/ark-code-latest"
      },
      "models": {
        "doubao/ark-code-latest": {
          "alias": "doubao"
        }
      }
    }
  },

```

对话app我选择的是飞书


### 3. 启动

运行以下命令启动 OpenClaw：
```bash
openclaw gateway restart
openclaw tui
```
在 TUI 中发送消息，如："hello"，看到模型正常回复就算成功。
[回复](./image2.png)



### 4. 配置 Feishu 集成
按照以下步骤配置 Feishu（飞书）集成：

#### 4.1 创建飞书应用
1. 访问 [飞书开放平台](https://open.feishu.cn/)
2. 登录并创建企业自建应用
3. 添加机器人能力
4. 配置应用权限
进入「权限管理」，添加以下权限：
- im:message - 获取与发送消息
- im:message:send_as_bot - 以机器人身份发送消息
- im:chat:readonly - 获取群列表
- contact:user.id:readonly - 获取用户 ID

进入「事件与回调」：
「回调配置」 → 订阅方式 → 选择 「使用长连接接收回调」
「添加事件」 → 选择「消息与群组」 → 「接收消息 (im.message.receive_v1)」
注意添加事件后记得点击开通权限
[开通权限](./img3.png)

5. 点击顶部「创建版本」→ 填写版本信息 → 提交发布。


#### 4.2 获取应用凭证
在飞书开放平台获取以下信息：
- App ID
- App Secret
- Verification Token
- Encryption Key

#### 4.3 配置 OpenClaw
创建或编辑 `config.yaml` 文件，添加飞书配置：
```bash
openclaw config set channels.feishu.appId 'yourfeishuAppId'
openclaw config set channels.feishu.appSecret 'yourfeishuappSecret'
openclaw config set channels.feishu.enabled true
openclaw config set channels.feishu.groupPolicy 'open' 
```


#### 5 重启 gateway

```bash
openclaw gateway restart

```

#### 6.在feishu机器人中发消息测试

备注：若TUI中已经可以回复消息，本地的问题可以直接让小龙虾自己修复