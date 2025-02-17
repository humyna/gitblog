# [OpenAI 的 RLHF 算法和 DeepSeek 的 GRPO 算法对比](https://github.com/humyna/gitblog/issues/35)


OpenAI 的 RLHF 算法大名鼎鼎，它基于人类反馈，通过奖励建模和强化学习来优化模型输出，让结果更符合人类偏好。和 GRPO 比起来，二者各有千秋。

* 从算法原理看，GRPO 通过组内相对奖励机制估计优势函数，还加入 KL 散度正则项；RLHF 则依赖人类反馈进行奖励建模和优化。
* 训练效率上，GRPO 简化流程，计算开销和内存需求低，训练速度快；RLHF 训练过程复杂，计算成本高。
* 策略更新稳定性方面，GRPO 通过组内相对奖励和 KL 散度正则化，更新稳定且可控；RLHF 的稳定性取决于奖励模型的准确性和标注数据质量，容易出现偏差。
* 应用场景中，GRPO 特别适合数学推理、代码生成这类需要推理能力的任务；RLHF 通用性强，在聊天机器人、内容生成等优化模型输出符合人类偏好的任务中表现出色。
* 资源需求上，GRPO 对大规模语言模型更友好，资源需求低；RLHF 则需要大量人类标注数据和计算资源。
* 模型性能上，GRPO 在特定任务（如数学推理）中解题准确率提升显著；RLHF 生成的输出更符合人类偏好，能减少有害内容生成。


RLHF=Reinforcement Learning with Human Feedback
GRPO=Group Relative Policy Optimization


来源：https://mp.weixin.qq.com/s/rG9cRYqHIwTc7-bR2qCIEg