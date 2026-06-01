# Claude Code

## Claude 安装

安装 [Node.js](https://nodejs.org/zh-cn/download/) 18+。

Windows 用户需安装 [Git for Windows](https://git-scm.com/download/win)。

在命令行界面，执行以下命令安装 Claude Code：

```shell
# Windows
winget uninstall Anthropic.ClaudeCode
# Linux
curl -fsSL https://claude.ai/install.sh | bash
```

安装完成后，验证安装是否成功：

```shell
claude --version
# 如果终端输出了版本号，说明安装成功:
> 2.1.81 (Claude Code)
```

创建~/.claude.json，写入：

```json
{
  "hasCompletedProjectOnboarding": true
}
```

## 第三方 API 接入

### deepseek api

Windows环境变量配置

```shell
# 打开 powershell 配置文件
notepad $PROFILE
# 在记事本中写入环境变量配置
$env:ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
$env:ANTHROPIC_AUTH_TOKEN="<你的DeepSeek API Key>"
$env:ANTHROPIC_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL="deepseek-v4-flash"
$env:CLAUDE_CODE_SUBAGENT_MODEL="deepseek-v4-flash"
$env:CLAUDE_CODE_EFFORT_LEVEL="max"
# 修改 powershell 执行策略（如果加载配置失败）
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## 常用命令

更新 claude

```shell
claude install
# 或者
claude update
# Claude Code 在启动和运行时会自动检查更新，后台下载完成后，下次启动即生效。
```

启动交互模式

```shell
# 切换到工作区目录
cd /path/to/proj
# 启动交互模式
claude
# 执行一次性任务后退出交互模式
claude "任务描述"
# 执行单次查询后立即退出（适合脚本集成）
claude -p "查询内容"
# 继续当前目录的最近一次对话
claude -c
# 从历史中选择并恢复一次对话
claude -r
```

交互模式内命令

```shell
# 显示所有可用命令和功能说明
/help
# 登录账号或切换到其他账号
/login
# 从历史中恢复之前中断的对话
/resume
# 清除当前对话历史，开始全新会话
/clear
# 查看和修改 Claude Code 配置
/config
# 退出 Claude Code，返回终端
exit
```
