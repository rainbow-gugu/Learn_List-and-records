# LangChain 短期记忆学习笔记

> 日期：2026-09-23
> 来源：PyCharm - LC-Study 项目 / test1.py

## 一、核心概念

LangChain 的短期记忆通过 **Checkpointer（检查点机制）** 实现，核心是在多轮对话中保存和恢复 Agent 的状态。

### 关键组件

| 组件 | 作用 |
|------|------|
| `InMemorySaver` | 内存级记忆存储，进程内有效，重启后丢失 |
| `checkpointer` | Agent 创建时传入的记忆检查点 |
| `thread_id` | 对话线程标识，同一 thread_id 共享记忆 |
| `thread_config` | 调用 Agent 时传入的配置，包含 thread_id |

## 二、代码实现

### 1. 导入依赖

```python
from dotenv import load_dotenv
from langchain.agents import create_agent
from langchain.chat_models import init_chat_model
from langgraph.checkpoint.memory import InMemorySaver
from langchain.tools import tool

load_dotenv()
```

### 2. 定义工具（可选）

```python
@tool
def get_user_info() -> str:
    """获取用户信息"""
    return "Not Found"
```

### 3. 创建带记忆的 Agent

```python
agent = create_agent(
    model="deepseek:deepseek-v4-flash",
    tools=[get_user_info],          # 传函数，不要加()
    checkpointer=InMemorySaver(),    # 关键：启用短期记忆
)
```

### 4. 配置对话线程

```python
thread_config = {"configurable": {"thread_id": "1"}}
```

### 5. 多轮对话验证记忆

```python
# 第一轮：告诉名字 bob
response1 = agent.invoke(
    {"messages": [{"role": "user", "content": "hi! My name is bob"}]},
    thread_config,
)["messages"][-1].content
print("第一轮：", response1)

# 第二轮：同一个 thread_id，读取记忆
response2 = agent.invoke(
    {"messages": [{"role": "user", "content": "what's my name?"}]},
    thread_config,
)["messages"][-1].content
print("第二轮：", response2)
```

## 三、关键要点

1. **`checkpointer=InMemorySaver()` 是启用记忆的核心**，没有它 Agent 不会记住上下文。
2. **`thread_id` 区分不同对话**：相同 thread_id 的调用共享记忆，不同 thread_id 相互隔离。
3. **`InMemorySaver` 是短期记忆**：数据存在内存中，程序重启后丢失；如需持久化需使用其他 Checkpointer（如 SqliteSaver、PostgresSaver）。
4. **工具传入方式**：`tools=[get_user_info]` 传函数对象，不要加括号调用。
5. **消息格式**：使用 `{"messages": [{"role": "user", "content": "..."}]}` 标准格式。

## 四、记忆类型对比

| 类型 | 存储位置 | 生命周期 | 适用场景 |
|------|----------|----------|----------|
| 短期记忆 (InMemorySaver) | 内存 | 进程内 | 单会话对话、开发测试 |
| 持久记忆 (SqliteSaver) | SQLite 文件 | 跨进程 | 本地应用、小型项目 |
| 持久记忆 (PostgresSaver) | PostgreSQL | 跨进程/跨服务 | 生产环境、分布式部署 |

## 五、易错点

- 忘记传 `checkpointer` → Agent 无记忆
- 每轮用不同的 `thread_id` → 无法读取之前的记忆
- `tools` 中写 `get_user_info()` → 传入的是返回值而非函数
- 期望 `InMemorySaver` 持久化 → 重启后记忆丢失是正常行为
