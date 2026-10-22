# SD WebUI Agent Dev 2.5

Forge Neo / Stable Diffusion WebUI 的对话式智能体扩展。它将自然语言对话、WebUI 生成能力、模型管理和工作区工具整合到一个 Gradio 界面，并通过 REST API 向外部客户端提供工具发现与调用能力。

## 功能概览

- **对话与工具调用**：智能体根据请求选择并调用注册工具，支持多轮对话、工具执行反馈、上传图像/视频/文档以及可配置的工具迭代上限。
- **模型服务**：支持本地 OpenAI 兼容接口（例如 llama.cpp、LM Studio）及扩展中已实现的云端服务；聊天模型、图像服务等可分别配置。
- **图像工作流**：文生图、图生图、放大、图像编辑，以及可用扩展提供的图像处理/拆层/3D 工具。
- **视频工作流**：通过已安装的视频扩展调用 H3 等视频生成工具，并提供关键帧/视频帧相关辅助工具。具体工具可用性取决于相应扩展、模型和后端是否已配置。
- **WebUI 与文件工具**：查询和切换模型组件、列出模型/扩展、工作区文件读取与诊断、网页搜索/读取等；部分操作会修改文件或 WebUI 状态，请先确认工具参数。
- **Skills**：扩展内置技能发现和读取接口，可从 `skills/<name>/SKILL.md` 加载专用工作流程；Archify 技能可校验并渲染架构、工作流、序列、生命周期和数据流图表。
- **外部客户端 API**：注册 `/agent/api/tools` 和 `/agent/api/execute_tool`，用于发现 Agent 工具并提交执行请求。它是 WebUI REST API，不是独立的 MCP Server。

## 安装

将扩展目录放入 WebUI 的 `extensions/`：

```text
<webui>/extensions/sd-webui-agent-dev-2.5
```

重启 WebUI，在扩展标签中打开 Agent。扩展依赖由 WebUI 所用 Python 环境提供；可使用 `requirements.txt` 或随附安装脚本补齐扩展依赖。若安装失败，请查看启动日志中的具体包和错误，避免在其他 Python 环境中安装。

## 配置

可在 Agent 设置界面配置文本模型、图像/视频服务、模型名、API 地址和密钥。兼容 OpenAI API 的本地服务通常需要填写其 `/v1` 地址及服务要求的模型名；云端服务应按服务商文档配置端点、模型和 Token。文本、图像和视频配置分别保存。

`agent_config.json` 可能包含 API 密钥和本机模型路径。公共仓库中的配置应仅保留空密钥和通用默认值；本机真实配置不要提交或公开。使用本地推理时，请确认 WebUI 进程可以访问推理服务地址。

## REST API

WebUI 启动 API 后，扩展注册：

- `GET /agent/api/tools`：获取工具定义/可用工具列表。
- `POST /agent/api/execute_tool`：提交工具名称与参数并返回执行结果。

接口受 WebUI 的 API 启动及认证设置约束。不要将未启用认证的 WebUI API 直接暴露到公网；远程使用时应配置认证并使用可信的 HTTPS 代理。

## 用户数据与输出

- Agent 记忆、对话历史和偏好属于本机数据。
- 生成图表及媒体输出写入扩展或 WebUI 的输出目录，临时上传文件写入运行时临时目录。
- 这些数据和输出不属于源代码，发布时不应提交 `data/`、`outputs/` 或 `tmp/` 中的个人文件。

## 仓库结构

- `scripts/`：WebUI UI、对话/路由、REST API、工具注册与各领域工具实现。
- `skills/`：随插件提供的 Agent Skills。
- `runtime/archify/`：Archify 的 schema、校验器、渲染器、示例及第三方声明。
- `requirements.txt`：扩展额外依赖。

## 安全提示

工具可能读取/写入工作区文件、修改 WebUI 设置或启动外部进程。请只使用可信的模型服务和客户端，检查工具名称及参数，妥善保管 API Token，并避免将本地端口或未认证接口暴露给不可信网络。
