# Prana 技能封装库

基于 Prana 封装的常用技能集合，适用于 AI 智能体、自动化工具、OpenClaw / ClawHub / MCP 等平台调用。

## 项目简介

本项目对 Prana 相关接口、能力与常用逻辑进行统一封装，提供标准化、可直接调用的技能模块。
便于快速集成到智能体、工作流或第三方技能市场中使用。

## 功能特性

- 统一封装 Prana 核心接口调用
- 提供标准化技能格式，支持 ClawHub / MCPMarket 规范
- 简化鉴权、参数校验、异常处理
- 模块化设计，可按需引入单个技能
- 支持配置化与扩展

## 技能列表

- TradingView技术指标分析助手

## 安装与使用

1. 克隆项目
```bash
git clone https://github.com/你的用户名/项目名.git
```

2. 引入对应技能模块
```javascript
const { pranaChat, pranaCompletion } = require('./skills');
```

3. 调用示例
```javascript
const result = await pranaChat({
  prompt: "你的问题",
  config: {
    apiKey: "你的API密钥"
  }
});
```

## 目录结构

```
.
├── skills/           # 所有 Prana 相关技能
└── README.md
```

## 适用平台

- ClawHub
- OpenClaw
- MCPMarket
- 自定义 AI 智能体
- Node.js 后端项目

## 许可证

MIT
