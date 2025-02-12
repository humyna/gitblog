# [DeepSeek-R1模型是如何训练的？](https://github.com/humyna/gitblog/issues/34)

DeepSeek-R1 模型运用 GRPO 算法进行训练，采用了多阶段策略。

* 在监督微调（SFT）阶段，用高质量标注数据对基础模型进行 “打磨”，让模型在特定任务上初步具备一定性能。

* 接着进入强化学习（RL）阶段，按照 GRPO 算法流程，采样动作组、评估奖励、计算相对优势、更新策略，不断迭代优化。

* 然后通过拒绝采样（RS）阶段生成合成数据集，提升模型的通用性和连贯性。

* 最后在最终强化学习阶段，再次运用 GRPO 算法，重点优化模型的实用性和无害性。


来源：https://mp.weixin.qq.com/s/rG9cRYqHIwTc7-bR2qCIEg
