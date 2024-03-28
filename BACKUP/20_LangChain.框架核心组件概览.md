# [LangChain 框架核心组件概览](https://github.com/humyna/gitblog/issues/20)

[LangChain](https://github.com/langchain-ai/langchain)是一个用于开发由语言模型支持的应用程序的框架。它使应用程序能够：

- 感知上下文：将语言模型连接到上下文源（提示说明、小样本示例、响应的内容等）
- 推理：依靠语言模型进行推理（关于如何根据提供的上下文进行回答、采取什么操作等）


LangChain框架有以下几个核心组成部分：

- LangChain库：Python和JavaScript库。包含无数组件的接口和集成、将这些组件组合成链和Agent的基本运行时，以及链和Agent的现成实现。
- LangChain模板：一系列易于部署的参考架构，适用于各种任务。
- LangServe：用于将LangChain链部署为RESTAPI的库。
- LangSmith：一个开发者平台，可让您调试、测试、评估和监控基于任何LLM框架构建的链，并与LangChain无缝集成。

![image](https://github.com/humyna/gitblog/assets/2505439/cadefa0f-76d5-4edd-a706-113a8f92a407)
