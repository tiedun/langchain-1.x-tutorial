Langchain 1.0 模块

# Model IO
	1. 对接模型
		Deepseek
		OpenAI Compatible接入方式
	2. 调用方式
		基本调用
		流式调用
		批次调用
	3. 结构化输出
		with_structured_output

	图形化？ gradio 聊天机器人

# Agent
	create_agent 
		Agent 自带运行图
			<center><img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20251028154837987.png" alt="image-20251028154837987" style="zoom:50%;" />
		model
		tool
			&emsp;&emsp;一次完整的`Function calling`执行流程如下：
			<center><img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/202412191720637.png" alt="202412191720637" style="zoom:50%;" />
			RACT
		middleware


# OutputParser
# Memory Manager
	InMemorySaver
# Tools
# MCP
# RAG
# Server ASGI接入
# Debug LangSmith 接入
	| 功能类别                           | 描述                                   | 场景                   |
	| ------------------------------ | ------------------------------------ | -------------------- |
	| 🧪 调试追踪（Trace Debugging）       | 可视化展示每个 LLM 调用、工具调用、Prompt、输入输出      | Agent 调试、Graph 调用链分析 |
	| 📊 评估（Evaluation）              | 支持自动评估多个输入样本的回答质量，可自定义评分维度           | 批量测试 LLM 表现、A/B 对比   |
	| 🧵 会话记录（Sessions / Runs）       | 每次 chain 或 agent 的运行都会被记录为一个 Run，可溯源 | Agent 问题诊断、用户问题分析    |
	| 🔧 Prompt 管理器（Prompt Registry） | 保存、版本控制、调用历史 prompt                  | 多版本 prompt 迭代测试      |
	| 📈 流量监控（Telemetry）             | 实时查看运行次数、错误率、响应时间等                   | 在生产环境中监控 Agent 质量    |
	| 📁 Dataset 管理                  | 管理自定义测试集样本，支持自动化评估                   | 微调前评估、数据对比实验         |
	| 📜 LangGraph 可视化               | 对 LangGraph 中每个节点运行情况进行实时可视化展示       | Graph 执行追踪           |

# Middleware	 人在环，敏感操作提示
	1. before 
		<center><img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/core_agent_loop.png" alt="核心代理循环图" style="zoom:100%;" />
	2. after
		<center><img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/middleware_final.png" alt="中间件流程图" style="zoom:100%;" />
	3. 功能
		| 功能类别                           | 功能说明                                               | 常用钩子                                           |
		| ------------------------------ | -------------------------------------------------- | ---------------------------------------------- |
		| **监控 / 日志 /指标**                | 跟踪 Agent 的行为、模型调用次数、token 使用量、工具调用频率等。             | `before_model`、`after_model`、`wrap_model_call` |
		| **修改输入 / 上下文工程**               | 在模型调用前修改消息历史、添补系统提示、裁剪上下文、替换模型参数等。                 | `before_model`、`modify_model_request`          |
		| **动态模型 /工具路由**                 | 根据当前状态决定使用哪个模型、启用哪些工具，动态调整调用策略。                    | `modify_model_request`、`wrap_model_call`       |
		| **控制流程 / 限流 /重试 /降级**          | 对模型或工具调用加上重试机制、超时、降级方案、跳转结束、提前中断。                  | `wrap_model_call`、`before_model`（跳转）           |
		| **合规 / 审核（Guardrails / HITL）** | 在调用工具前或后插入人工审核、敏感内容检查、PII检测、安全过滤。                  | `after_model`（或 `wrap_tool_call`）              |
		| **摘要 / 上下文裁剪**                 | 当对话变长或token靠近限制时，自动摘要历史、压缩上下文以维持性能。                | `before_model`                                 |
		| **工具选择与调用管理**                  | 在模型调度工具前决定哪些工具可用、们是否被选中、限制调用频率。                    | `modify_model_request`、`wrap_tool_call`        |
		| **结构化输出 / 响应格式控制**             | 在请求模型之前或之后规范输出格式、校验结构化数据。                          | `modify_model_request`、`after_model`           |
		| **状态扩展**                       | 自定义 Agent 的状态 schema（如调用次数、用户偏好、认证状态），中间件可以读写这些状态。 | `before_model`、`after_model`                   |

		> 钩子是框架或系统在某些关键执行点暴露的扩展接口。开发者可以“挂上”自己的逻辑，在那些点上插入、修改或替换行为，而无需改变主流程代码。就像在流水线上某个环节设置了一个“检查点”或“插入器”。在 LangChain 的 Agent 执行循环中，比如 “模型调用前”“模型调用后”“工具调用前后” 都是可能挂钩子的点。
	4. 案例
		动态切换模型
			@wrap_model_call 中间件
				自定义实现逻辑

		上下文压缩
			裁剪
				@before_model
					自定义实现逻辑
					返回的Messages字段下用RemoveMessage标识要删除的消息，再新增保留的消息
			删除
				@after_model
					前后应该都可以
					用RemoveMessage
			摘要
				SummarizationMiddleware
		人在环
			HumanInTheLoopMiddleware
				需要部署看效果

部署？？
	Part5 结合Langgraph
	Langchain的 agent-chat-ui
	https://github.com/langchain-ai/agent-chat-ui

# DeepAgent？
