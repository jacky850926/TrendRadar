# TrendRadar 新手运行指南

欢迎使用 **TrendRadar**！这是一个全网热点监控工具，可以帮助你聚合来自知乎、微博、抖音等多个平台的热搜新闻，并通过企业微信、飞书、Telegram、邮件等方式推送给你。

这份指南将带你从零开始，一步步运行这个项目。

---

## 📂 1. 项目结构概览

首先，了解一下几个核心文件和目录的作用：

*   **`config/config.yaml`** (核心): **配置文件**。在这里填写你的推送地址 (Webhook)、邮箱配置、推送模式等。
*   **`config/frequency_words.txt`** (核心): **关键词文件**。在这里写下你关心的关键词（如"人工智能"、"华为"），只有匹配这些词的新闻才会被推送。
*   **`docker/`**: 包含 Docker 运行所需的文件 (`docker-compose.yml`)。
*   **`output/`**: 程序运行后生成的报告、日志和历史记录都会保存在这里。
*   **`main.py`**: 项目的主程序代码。

---

## ⚙️ 2. 关键配置 (必做)

在运行之前，你**必须**修改配置文件，否则程序不知道要把新闻推送到哪里。

### A. 修改 `config/config.yaml`

打开 `config/config.yaml` 文件，关注以下几点：

1.  **推送渠道 (Notification)**:
    找到 `notification` -> `webhooks` 部分。你**至少需要配置一个**渠道才能收到通知。
    *   **企业微信/飞书/钉钉**: 填入机器人的 Webhook URL。
    *   **邮件 (Email)**:
        *   `email_from`: 发件人邮箱 (必填)。
        *   `email_password`: 邮箱授权码/密码 (必填)。
        *   `email_to`: 收件人邮箱 (必填)。
    *   **Telegram**: 填入 `bot_token` 和 `chat_id`。

2.  **推送模式 (Report Mode)**:
    找到 `report` -> `mode`。
    *   `daily`: **每日汇总**。适合每天看一次日报。
    *   `current`: **实时榜单**。适合时刻关注最新热搜。
    *   `incremental`: **增量监控**。只有出现新的热点时才推送，避免重复打扰。

### B. 修改 `config/frequency_words.txt`

打开 `config/frequency_words.txt`，配置你感兴趣的内容。

*   **普通关键词**: 直接写词，如 `DeepSeek`。
*   **必须包含**: 使用 `+`，如 `+发布` (表示新闻标题必须包含"发布")。
*   **过滤词**: 使用 `!`，如 `!广告` (表示包含"广告"的新闻不要)。
*   **限制数量**: 使用 `@`，如 `@5` (表示该组关键词最多显示5条)。

**示例**:
```text
# 关注 AI 动态
OpenAI
ChatGPT
+模型

# 关注手机发布，但不看传闻
华为
小米
+发布
!传闻
```

---

## 🚀 3. 运行项目

推荐使用 **Docker** 方式运行，最简单且环境稳定。如果你熟悉 Python，也可以选择 **本地运行**。

### 方式一：使用 Docker (推荐)

**前提**: 你的电脑上需要安装 [Docker Desktop](https://www.docker.com/products/docker-desktop/)。

1.  **打开终端 (Terminal)**。
2.  **进入 docker 目录**:
    ```bash
    cd /Users/jackyma/Developer/TrendRadar/docker
    ```
    *(请根据你的实际项目路径调整)*
3.  **启动容器**:
    ```bash
    docker-compose up -d
    ```
    *   `up`: 启动服务。
    *   `-d`: 后台运行 (Detached mode)。

**查看运行状态**:
*   查看容器是否在运行: `docker ps`
*   查看运行日志: `docker logs -f trend-radar` (用于检查是否有报错)
*   停止运行: `docker-compose down`

### 方式二：本地 Python 运行 (适合调试)

**前提**: 你的电脑上安装了 Python 3.10+。

1.  **打开终端**，进入项目根目录:
    ```bash
    cd /Users/jackyma/Developer/TrendRadar
    ```
2.  **安装依赖**:
    ```bash
    pip install -r requirements.txt
    ```
3.  **运行程序**:
    ```bash
    python main.py
    ```

---

## ❓ 常见问题 (FAQ)

**Q: 我配置了邮件，但是没收到？**
A: 请检查：
1.  `email_from`, `email_password`, `email_to` 是否都填了？
2.  `email_password` 通常是**授权码**，而不是你的登录密码（特别是 QQ/163 邮箱）。
3.  检查 `output/` 目录下的日志，看是否有报错信息。

**Q: 运行后什么反应都没有？**
A:
1.  检查 `config.yaml` 中的 `enable_crawler` 是否为 `true`。
2.  检查 `frequency_words.txt` 是否有内容。如果关键词太偏门，可能确实没有匹配的新闻。
3.  如果是 Docker 运行，请使用 `docker logs trend-radar` 查看日志。

**Q: 如何修改运行频率？**
A:
*   **Docker 模式**: 修改 `docker/docker-compose.yml` 中的 `CRON_SCHEDULE` 环境变量（默认是 `*/5 * * * *`，即每5分钟一次）。
*   **本地模式**: `main.py` 默认运行一次就结束。如果你想定时运行，需要自己配置系统的 Crontab 或使用循环脚本。

---

祝你使用愉快！如有其他问题，欢迎随时提问。
