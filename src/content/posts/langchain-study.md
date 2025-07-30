---
title: Langchain入门教程
published: 2025-07-30
description: 'LangChain学习记录，不要用LangChain写agent，LangGraph更好用'
image: ''
tags: ['LangChain','大模型','Agent','AI']
category: '教程'
draft: false 
lang: ''
---
# LangChain入门教程

## 简介

LangChain 是一个专为开发者设计的开源框架，用于构建基于大语言模型的复杂应用。它通过模块化设计简化了 LLM 应用的开发流程，支持灵活定制复杂逻辑。

| **维度**     | **LangChain**            | **Dify**                         | **Coze**                 |
| :----------------- | :----------------------------- | :------------------------------------- | :----------------------------- |
| **定位**     | 开发者框架                     | 低代码企业级平台                       | 零代码轻量应用工具             |
| **开发方式** | Python/JS 编码，模块化组合     | 可视化拖拽 + API 集成                  | 纯界面配置，无需代码           |
| **灵活性**   | 支持深度定制复杂逻辑           | 平衡效率与扩展性                       | 仅限预设模板和插件             |
| **模型支持** | 广泛（开源/闭源/本地模型）     | 依赖平台接入（如 OpenAI、DeepSeek）    | 绑定字节生态模型，扩展性弱     |
| **部署控制** | 完全自主，支持私有化           | 支持私有化部署和企业级功能（SSO/审计） | 仅云端托管，无本地部署选项     |
| **典型场景** | 复杂 Agent、多模型工作流、研究 | 企业知识库、智能客服、内容生成         | 微信公众号机器人、简单问答助手 |
| **学习成本** | 高（需编程基础）               | 中（理解工作流概念）                   | 低（5分钟上手）                |

## 核心功能模块

### 模型 IO

统一各类大语言模型的输入/输出接口，可以使用官方大模型api，也可以使用本地自搭建的大模型。支持多种类型的模型，包括LLMs（大语言模型）和ChatModels（聊天模型）。LLMs是文本输入文本输出的模型，而ChatModels则是将聊天消息列表作为输入并返回聊天消息的模型。

- **LLMs**：文本输入 → 文本输出（e.g. `OpenAI()`, `HuggingFaceHub()`）
- **Chat Models**：消息列表输入 → 消息输出（e.g. `ChatOpenAI()`, `ChatAnthropic()`）
- **Prompts**：动态提示模板
  - `PromptTemplate`：基础文本模板
  - `ChatPromptTemplate`：角色消息模板（System/Human/AI）
  - `FewShotPromptTemplate`：少样本学习模板
- **Output Parsers**：结构化输出解析（e.g. `CommaSeparatedListOutputParser()`, `PydanticOutputParser()`）

### 检索

用于处理外部数据，包括文档加载器、文本分割器、向量存储和检索器。

- **Document Loaders**：文档加载器（`WebBaseLoader`, `PDFMinerLoader`, 100+ 数据源支持）
- **Text Splitters**：文本分割（`RecursiveCharacterTextSplitter`, `TokenTextSplitter`）
- **Vectorstores**：向量数据库（`FAISS`, `Chroma`, `Pinecone`, `Milvus` 等）
- **Retrievers**：检索接口（支持相似度搜索/混合搜索/自查询等）

### 链

链是将多个组件组合在一起以完成特定任务的方式。链可以是简单的链（如 `LLMChain`），也可以是复杂的链（如 `SequentialChain`，它按顺序运行多个链）。

- **Utility Chains**：预设任务链

  - `LLMChain`：基础链（提示+模型）
  - `RetrievalQA`：文档问答链
  - `ConversationChain`：带记忆的对话链
- **LCEL**：LangChain表达式语言（通过 `|` 运算符组合）

  ```python
  chain = prompt | model | output_parser  # 声明式链构建
  ```

### 代理

代理使用LLM来确定要采取的动作序列。代理可以访问工具，并根据用户输入决定调用哪些工具。

- **Agent Types**：推理策略
  - `zero-shot-react-description`：零样本ReAct推理
  - `conversational-react-description`：带记忆的对话代理
- **Tools**：可调用函数集
  - 内置工具（`WikipediaQueryRun`, `PythonREPLTool` 等）
  - 自定义工具（`@tool` 装饰器）
- **Toolkits**：领域专用工具包（e.g. 数据库、GitHub工具包）

### 记忆

用于在链或代理的多次调用之间保持状态（即记忆），即让大模型记住你说过什么东西。

- **短期记忆**：
  - `ConversationBufferMemory`：原始对话历史
  - `ConversationBufferWindowMemory`：滑动窗口记忆
- **长期记忆**：
  - `VectorStoreRetrieverMemory`：向量库长期记忆
  - `RedisEntityStore`：实体记忆存储

### 回调

用于记录和监控链或代理的执行过程。可以用于日志记录、流式传输等。

- **Handlers**：事件处理器
  - `StdOutCallbackHandler`：标准输出日志
  - `LangChainTracer`：LangSmith平台追踪
- **自定义回调**：实现 `BaseCallbackHandler`

## 环境搭建

### Python 环境准备

LangChain 作为基于 Python 的开发框架，需要一个稳定可靠的 Python 运行环境。我们推荐使用 Anaconda 进行环境管理，这不仅能避免不同项目之间的依赖冲突，还能确保开发环境的一致性和可重现性。

### 安装 Anaconda

Anaconda 是一个集成了 Python 解释器、包管理器和科学计算库的发行版，它为数据科学和机器学习项目提供了完整的生态系统。

**为什么选择 Anaconda？**

- **环境隔离**：每个项目可以拥有独立的依赖环境，避免版本冲突
- **包管理**：内置 conda 和 pip 双重包管理器，解决复杂的依赖关系
- **预装库**：包含 NumPy、Pandas、Matplotlib 等常用科学计算库
- **跨平台**：支持 Windows、macOS、Linux 三大操作系统

**安装步骤：**

1. **下载安装包**

   - 访问 [Anaconda 官网](https://www.anaconda.com/download)
   - 选择对应操作系统的 Python 3.11+ 版本（推荐最新稳定版）
   - 下载完成后运行安装程序
2. **安装配置**

   ```bash
   # Windows: 直接运行 .exe 安装包
   # 勾选 "Add Anaconda to my PATH environment variable"（可选，但建议勾选）
   # 勾选 "Register Anaconda as my default Python"
   ```
3. **验证安装**

   ```bash
   # 打开命令行工具，输入以下命令验证
   conda --version
   python --version

   # 期望输出类似：
   # conda 23.x.x
   # Python 3.11.x
   ```

### 创建 LangChain 专用环境

使用 conda 创建独立的虚拟环境可以为不同项目使用不同 Python 版本和依赖库同时能精确控制每个库的版本，确保代码运行稳定

**创建环境：**

```bash
# 创建名为 langchain-env 的虚拟环境，指定 Python 3.11
conda create -n langchain-env python=3.11 -y

# 激活环境
conda activate langchain-env

# 验证环境激活成功（命令行前缀会显示环境名）
# (langchain-env) C:\Users\YourName>
```

**环境管理常用命令：**

```bash
# 查看所有环境
conda env list

# 激活指定环境
conda activate langchain-env

# 退出当前环境
conda deactivate

# 删除环境（如需要）
conda env remove -n langchain-env
```

### 安装 LangChain 核心库

LangChain 生态系统采用模块化设计，核心库提供了框架的基础功能，而特定功能则通过独立的包来实现。这种方式可以只安装项目需要的功能模块，减少依赖体积。

**安装核心库：**

```bash
# 确保在 langchain-env 环境中
conda activate langchain-env

# 安装 LangChain 核心库
pip install langchain

# 验证安装
python -c "import langchain; print(f'LangChain version: {langchain.__version__}')"
```

**核心库包含的主要模块：**

- **langchain.chains**：预构建的链组件
- **langchain.agents**：智能代理框架
- **langchain.memory**：对话记忆管理
- **langchain.prompts**：提示模板系统
- **langchain.schema**：核心数据结构
- **langchain.callbacks**：回调处理机制

### 安装 LangChain-OpenAI 集成

OpenAI 作为当前最主流的大语言模型服务提供商，大部分大模型的接口都是使用的它的接口定义规范

**安装步骤：**

```bash
# 安装 LangChain OpenAI 集成包
pip install langchain-openai

# 验证安装
python -c "from langchain_openai import ChatOpenAI; print('OpenAI integration installed successfully')"
```

### 配置 API 密钥

为了使用 OpenAI 服务，需要配置 API 密钥。推荐使用环境变量的方式，这样既安全又便于管理。

**获取 API 密钥：**

1. 访问 [OpenAI 官网](https://platform.openai.com/api-keys)
2. 注册账号并创建 API 密钥
3. 复制密钥（请妥善保管，密钥只显示一次）

**配置 API 密钥：**

创建 `.env` 文件在项目根目录：

```bash
# .env 文件内容
OPENAI_API_KEY=your-api-key-here
OPENAI_API_BASE=https://api.openai.com/v1  # 可选，使用默认即可
```

然后安装 python-dotenv 来自动加载环境变量：

```bash
pip install python-dotenv
```

## 项目实战：构建天气查询智能体

在掌握了 LangChain 的基础概念和环境配置后，我们将通过一个完整的项目实战来深入理解各个功能模块的使用。本节将构建一个具备天气查询功能的智能体，它不仅能查询天气信息，还能基于对话历史进行上下文理解和智能推荐。

### 项目概述

**项目目标：**

- 构建一个能够查询指定城市天气信息的智能助手
- 实现对话记忆功能，维持多轮对话的上下文
- 集成自定义工具，扩展智能体的功能边界
- 演示 LangChain 核心模块的协同工作

**涉及的核心模块：**

- **模型 IO**：使用 ChatOpenAI 作为推理引擎
- **代理**：初始化 Agent 并配置推理策略
- **工具**：自定义天气查询工具
- **记忆**：ConversationBufferMemory 维持对话历史
- **提示**：设计系统提示优化交互体验

### 前置准备

在开始编码之前，我们需要准备一些额外的资源和配置：

#### 1. 获取天气 API 密钥

本项目使用和风天气（QWeather）API 获取天气数据，它提供免费的天气查询服务。

**申请步骤：**

1. 访问 [和风天气开发平台](https://dev.qweather.com/)
2. 注册账号并创建应用
3. 获取 API Key 和项目 ID
4. 查看 API 文档了解接口规范

#### 2. 更新环境变量配置

在项目的 `.env` 文件中添加天气 API 配置：

```bash
# OpenAI 配置
OPENAI_API_KEY=your-openai-api-key
OPENAI_BASE_URL=https://api.openai.com/v1
MODEL_NAME=gpt-3.5-turbo

# 和风天气 API 配置
QWEATHER_API_KEY=your-qweather-api-key
```

#### 3. 安装项目依赖

```bash
# 激活虚拟环境
conda activate langchain-env

# 安装必要的依赖包
pip install requests  # HTTP 请求库，用于调用天气 API
pip install langchain-community  # 社区扩展组件
```

### 核心代码实现

#### 1. 项目初始化和配置

```python
from langchain_openai import ChatOpenAI  # 调用 OpenAI 的聊天模型接口
from langchain.memory import ConversationBufferMemory  # 简单记忆模块
from langchain.agents import initialize_agent, AgentType  # 初始化代理
from langchain.tools import BaseTool  # 工具类
import requests  # HTTP 请求库
from typing import Optional  # 类型提示
import os 
from dotenv import load_dotenv  # 环境变量加载库

# 加载环境变量
load_dotenv()

# 从环境变量获取配置信息
api_key = os.getenv("OPENAI_API_KEY")
base_url = os.getenv("OPENAI_BASE_URL")
model = os.getenv("MODEL_NAME")
qweather_api_key = os.getenv("QWEATHER_API_KEY")

# 初始化大语言模型
llm = ChatOpenAI(
    base_url=base_url,
    api_key=api_key,
    model=model,
    temperature=0,  # 设置温度为0，确保输出一致性
)
```

**配置说明：**

- **temperature=0**：确保模型输出的确定性，避免随机性影响工具调用的准确性
- **环境变量管理**：统一管理 API 密钥，避免硬编码敏感信息

#### 2. 自定义天气查询工具

LangChain 的工具系统允许我们扩展智能体的功能边界。通过继承 `BaseTool` 类，我们可以创建专门的天气查询工具：

```python
# 自定义天气查询工具
class WeatherTool(BaseTool):
    name: str = "weather_query"
    description: str = "查询指定城市的天气情况，通过输入城市名称获取当日的天气信息。"

    def _run(self, city: str, run_manager: Optional[object] = None) -> str:
        """
        工具的核心执行逻辑
        分为两步：1) 根据城市名获取城市ID，2) 使用城市ID查询天气
        """
        # 第一步：获取城市的 location ID
        city_id_res_url = f"https://geoapi.qweather.com/v2/city/lookup?location={city}&key={qweather_api_key}"
        city_id_res = requests.get(city_id_res_url)
  
        if city_id_res.status_code == 200:
            city_id_data = city_id_res.json()
            if city_id_data['code'] == '200' and city_id_data['location']:
                city_id = city_id_data['location'][0]['id']
            else:
                return f"无法获取 {city} 的地理信息，请检查城市名称是否正确。"
        else:
            return f"地理信息查询失败，状态码: {city_id_res.status_code}"
  
        # 第二步：使用城市ID查询实时天气
        weather_url = f"https://devapi.qweather.com/v7/weather/now?location={city_id}&key={qweather_api_key}"
        weather_res = requests.get(weather_url)
  
        if weather_res.status_code == 200:
            weather_data = weather_res.json()
            if weather_data['code'] == '200':
                weather = weather_data['now']
                weather_info = (
                    f"{city} 的当前天气情况：{weather['text']}，"
                    f"温度：{weather['temp']}°C，"
                    f"湿度：{weather['humidity']}%，"
                    f"风向：{weather['windDir']}，"
                    f"风力：{weather['windScale']}级"
                )
                return weather_info
            else:
                return f"无法获取 {city} 的天气信息，错误代码：{weather_data['code']}"
        else:
            return f"天气查询失败，状态码: {weather_res.status_code}"
  
    def _arun(self, city: str, run_manager: Optional[object] = None) -> str:
        """异步执行方法（本例中未实现）"""
        raise NotImplementedError("异步运行方法未实现")
```

#### 3. 记忆模块配置

记忆模块是智能体维持上下文理解的关键组件：

```python
# 初始化记忆模块
memory = ConversationBufferMemory(
    memory_key="chat_history",  # 内存中存储对话历史的键名
    return_messages=True,       # 返回消息格式而非字符串
    k=5                        # 保留最近5轮对话
)
```

**参数说明：**

- **memory_key**：指定在提示模板中引用对话历史的变量名
- **return_messages**：以结构化消息格式存储，便于后续处理
- **k=5**：限制记忆长度，避免上下文过长影响性能

#### 4. 智能体初始化

```python
# 工具列表配置
tools = [WeatherTool()]

# 系统提示设计
system_message = """你是一个专业的天气助手，擅长跟踪和维护对话的上下文。

核心能力：
1. 查询任何城市的实时天气信息
2. 基于天气条件提供穿衣、出行建议
3. 维持对话上下文，理解用户的隐式引用

对话原则：
- 当用户提到某个城市后，在后续对话中保持对该城市的引用
- 如果用户询问"这个城市"、"那里"等代词，请参考之前提到的城市
- 基于天气信息主动提供实用的生活建议
- 保持友好、专业的对话风格"""

# 初始化智能代理
agent = initialize_agent(
    tools=tools,
    llm=llm,
    agent_type=AgentType.CHAT_CONVERSATIONAL_REACT_DESCRIPTION,
    memory=memory,
    verbose=True,  # 启用详细输出，便于调试
    agent_kwargs={"system_message": system_message}
)
```

**Agent 类型说明：**

- **CHAT_CONVERSATIONAL_REACT_DESCRIPTION**：支持多轮对话的 ReAct 推理模式
- **ReAct 模式**：Reasoning + Acting，模型会进行推理并决定是否使用工具
- **对话式**：天然支持多轮对话和上下文维护

### 完整示例演示

#### 1. 基础天气查询

```python
# 第一轮对话：查询天气
print("=== 第一轮对话：天气查询 ===")
weather_response = agent.invoke("今天武汉的天气怎么样？")
print("Agent 响应:", weather_response['output'])

# 查看记忆内容
print("\n对话历史记录:")
print(memory.load_memory_variables({}))
```

#### 2. 上下文相关的生活建议

```python
# 第二轮对话：基于天气的建议
print("\n=== 第二轮对话：生活建议 ===")
advice_response = agent.invoke("根据刚才的天气情况，今天适合穿什么衣服？需要带伞吗？")
print("Agent 响应:", advice_response['output'])
```

#### 3. 隐式城市引用

```python
# 第三轮对话：测试上下文理解
print("\n=== 第三轮对话：上下文理解 ===")
context_response = agent.invoke("这个地方有什么好玩的景点推荐？")
print("Agent 响应:", context_response['output'])

# 查看完整对话历史
print("\n=== 完整对话历史 ===")
chat_history = memory.load_memory_variables({})["chat_history"]
for i, message in enumerate(chat_history):
    print(f"{i+1}. {message.type}: {message.content}")
```

### 高级功能扩展

#### 1. 多轮对话的记忆管理

LangChain 提供了多种记忆类型来满足不同的应用场景：

```python
from langchain.memory import ConversationBufferWindowMemory

# 滑动窗口记忆：只保留最近N轮对话
window_memory = ConversationBufferWindowMemory(
    memory_key="chat_history",
    return_messages=True,
    k=3  # 只保留最近3轮对话
)

# 可以替换原有的 memory 参数
agent_with_window = initialize_agent(
    tools=tools,
    llm=llm,
    agent_type=AgentType.CHAT_CONVERSATIONAL_REACT_DESCRIPTION,
    memory=window_memory,
    verbose=True
)
```

#### 2. 现代化的链式调用方式

除了传统的 Agent 模式，LangChain 还提供了更现代的 LCEL（LangChain Expression Language）方式：

```python
from langchain_community.chat_message_histories import ChatMessageHistory
from langchain_core.runnables import RunnableWithMessageHistory
from langchain.prompts import ChatPromptTemplate, MessagesPlaceholder

# 定义现代化的提示模板
modern_prompt = ChatPromptTemplate.from_messages([
    ("system", """你是一个专业的天气助手，可以查询天气信息并提供生活建议。
当需要查询天气时，请使用 weather_query 工具。"""),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}"),
])

# 创建链式调用
chain = modern_prompt | llm

# 消息历史管理
message_histories = {}

def get_message_history(session_id):
    if session_id not in message_histories:
        message_histories[session_id] = ChatMessageHistory()
    return message_histories[session_id]

# 带历史记录的链
chain_with_history = RunnableWithMessageHistory(
    chain,
    get_message_history,
    input_messages_key="input",
    history_messages_key="history",
)
```