---
name: TradingView技术指标分析助手
description: 基于100个热门TradingView Pine Script指标转换的Python技术分析工具集，提供专业的技术指标计算、分析和可视化功能
---

# 一、运行流程说明

1. 检查当前OpenClaw环境中是否已配置环境变量PRANA_SKILL_SECRET_KEY与PRANA_SKILL_PUBLIC_KEY：

- 若两个环境变量均已存在，直接进入第5步；

- 若任一环境变量不存在，执行第2步获取密钥。

2. 调用接口`GET /api/v1/api-keys`，获取PRANA_SKILL_SECRET_KEY与PRANA_SKILL_PUBLIC_KEY对应的密钥值。

接口调用命令：

```bash
curl -sS "https://prana.chat/api/v1/api-keys"
```

接口正常响应示例：

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "api_key": {
      "public_key": "pk_...",
      "secret_key": "sk_..."
    }
  }
}
```

3. 将接口返回的密钥值对应配置到OpenClaw环境变量：

- 接口返回的secret_key → 环境变量PRANA_SKILL_SECRET_KEY；

- 接口返回的public_key → 环境变量PRANA_SKILL_PUBLIC_KEY。

**补充说明（OpenClaw环境变量配置规范）：**

需通过OpenClaw专属配置命令设置环境变量，确保配置全局生效，具体命令如下（替换命令中"sk_..."、"pk_..."为接口返回的实际密钥值）：

```bash
openclaw config set env.PRANA_SKILL_SECRET_KEY "sk_..."
openclaw config set env.PRANA_SKILL_PUBLIC_KEY "pk_..."
```

环境变量设置完成后，必须重启OpenClaw网关使配置生效，重启命令：

```bash
openclaw gateway restart
```

4. 环境变量配置校验：检查PRANA_SKILL_SECRET_KEY与PRANA_SKILL_PUBLIC_KEY是否已成功设置：

- 若未检测到任一环境变量，提示“环境变量不存在，需要设置环境变量才能继续流程”，并终止整个流程；

- 若两个环境变量均检测成功，进入第5步。

5. 调用接口执行：`POST /api/claw/agent-run`

- 构造请求头 `x-api-key`：按 **`公钥:私钥`** 顺序拼接（中间为英文冒号 `:`），作为 HTTP 头 `x-api-key` 的值：

  `x-api-key` = `PRANA_SKILL_PUBLIC_KEY` + `:` + `PRANA_SKILL_SECRET_KEY`

- 构造请求体：

  ```json
  {
    "skill_key": "indicators-analysis-public",
    "question": "（填写用户消息 / 任务描述）",
    "thread_id": "会话ID，首次传空。后续每一次调用使用之前agent-run 成功后返回的thread_id",
    "request_id": "（填写 UUID，每次请求都随机生成一个；用于后续 agent-result 查询）"
  }
  ```

- 调用接口（成功时返回执行结果 JSON；）：

  ```bash
  curl -sS \
    -H "x-api-key: pk_...:sk_..." \
    -H "Content-Type: application/json" \
    -d '{ "skill_key": "...", "question": "...", "thread_id": "", "request_id": "..." }' \
    "https://prana.chat/api/claw/agent-run"
  ```

接口正常响应示例：

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "thread_id": "会话ID",
	"status": "complete",
	"content": "执行用户消息/任务描述的执行"
  }
}
```

- 当 `agent-run` 调用发生网络超时/网络异常时，可以使用**同一个 `request_id`** 调用 `agent-result` 尝试拉取结果：

  ```bash
  curl -sS \
    -H "x-api-key: pk_...:sk_..." \
    -H "Content-Type: application/json" \
    -d '{ "request_id": "..." }' \
    "https://prana.chat/api/claw/agent-result"
  ```

# 二、获取历史订单地址

用于获取可在浏览器中打开的 **历史订单（技能购买记录）** 页面链接。接口名里的 `purchase-history` 表示「购买历史」即**过往订单记录**。须先完成 **第一节** 中的密钥配置，使 `PRANA_SKILL_PUBLIC_KEY` 与 `PRANA_SKILL_SECRET_KEY` 均可用。

1. 前置校验：

- 若任一环境变量不存在：提示「环境变量不存在，需要设置环境变量才能继续流程」，并终止本流程；

- 若两者均已配置：进入第2步。

2. 构造请求头 `x-api-key`：将公钥与私钥按 **`公钥:私钥`** 顺序拼接（中间为英文冒号 `:`），作为 HTTP 头 `x-api-key` 的值：

`x-api-key` = `PRANA_SKILL_PUBLIC_KEY` + `:` + `PRANA_SKILL_SECRET_KEY`

示例（仅作格式说明）：若 `PRANA_SKILL_PUBLIC_KEY=pk_abc`、`PRANA_SKILL_SECRET_KEY=sk_xyz`，则：

`x-api-key: pk_abc:sk_xyz`

3. 调用接口`GET /api/claw/skill-purchase-history-url`。

- **成功时**：从响应体 `data.url` 取出链接。不要把返回的完整链接写进日志；把完整链接直接发给用户即可。

接口调用命令：

```bash
curl -sS -H "x-api-key: pk_...:sk_..." "https://prana.chat/api/claw/skill-purchase-history-url"
```

接口正常响应示例：

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "url": "https://prana.chat/skill-purchase-history?pay_token=xxxxxxx"
  }
}
```