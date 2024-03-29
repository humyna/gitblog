# [初识AI Agent](https://github.com/humyna/gitblog/issues/4)

AI Agent也是被认为是向AGI（通用人工智能）又迈进了一步！

# 什么是AI Agent?
目前与AI的交互形式基本上都是你输入指令，AI模型会根据你的指令内容做出响应，这样就是导致你每次在进行提供有效的提示词才能达到你想要的效果。
而AI Agent则不同，它被设计为具有独立思考和行动能力的AI程序。你只需要提供一个目标，比如写一个游戏、开发一个网页，他就会根据环境的反应和独白的形式生成一个任务序列开始工作。就好像是人工智能可以自我提示反馈，不断发展和适应，以尽可能最好的方式来实现你给出的目标。

# AI Agent的作用
AI Agent可以利用外部工具来克服LLM的一些缺点。工具就是代理用它来完成特定任务的一个插件、一个集成API、一个代码库等等，例如：1）Google搜索：获取最新信息2）Python REPL：执行代码3）Wolfram：进行复杂的计算4）外部API：获取特定信息
```
LLM的一些缺点
1）会产生幻觉。
2）结果并不总是真实的。
3）对时事的了解有限或一无所知。
4）很难应对复杂的计算。
```

AI Agent的诞生就是为了处理各种复杂任务的，就复杂任务的处理流程而言AI Agent主要分为两大类：行动类、规划执行类。
**行动类行动类Agent**负责执行简单直接的任务，例如他们可以通过调用API来检索最新的天气信息。
**规划执行类Agent**首先会制定一个包含多个操作的计划任务，然后按照顺序去执行这些操作。如AutoGPT、BabyAGI、GPTEngineer等

# AI Agent的特点
Agent在执行计划时会有以下特别重要的两点：
1）反思与完善：Agent中设置了一些反思完善的Agent机制，可以让其进行自我批评和反思，与其它一些信息源形成对比，从错误中不断地去吸取教训，同时针对未来的步骤进行完善，提供最终的效果和质量！
2）长期记忆：我们常见的上下文学习的提升工程项目都是利用模型的短期记忆来学习的，但是AI Agent则提供了长期保留和调用无限信息的能力，通常是利用外部的向量储存和快速检索来实现！

# AI Agent的关键组件
AI Agent充当大语言模型的大脑，主要有以下几个关键组件进行补充：
**规划组件（Planning）**

- 子目标和分解：代理将大型任务分解为更小的、可管理的子目标，从而能够有效处理复杂的任务。
- 反思和完善：智能体可以对过去的行为进行自我批评和自我反思，从错误中吸取教训，并针对未来的步骤进行完善，从而提高最终结果的质量。

**记忆组件（Memory）**

- 短期记忆：我认为所有的上下文学习（参见提示工程）都是利用模型的短期记忆来学习。
- 长期记忆：这为代理提供了长时间保留和回忆（无限）信息的能力，通常是通过利用外部向量存储和快速检索。

**工具组件（Tools）**

- 代理学习调用外部 API 来获取模型权重中缺失的额外信息（通常在预训练后很难更改），包括当前信息、代码执行能力、对专有信息源的访问等。

![image](https://github.com/humyna/gitblog/assets/2505439/252eb1f0-e9a4-4d81-8937-350f74a78568)

# AI Agent开源和闭源项目
详见https://www.wehelpwin.com/article/4357

[来源](https://www.wehelpwin.com/article/4357)