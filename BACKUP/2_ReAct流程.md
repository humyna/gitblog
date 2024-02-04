# [ReAct流程](https://github.com/humyna/gitblog/issues/2)

ReAct是Reasoning and Acting缩写，意思是大模型可以根据逻辑推理（Reason），构建完整系列行动（Act），从而达成期望目标。ReAct方式的关键就是协调大语言模型和外部的信息获取，与其他功能交互：大模型是大脑，通过ReAct框架可以让大脑来控制手和脚。

![image](https://github.com/humyna/gitblog/assets/2505439/e52a3fca-fda3-47fe-8cab-e55936f5c802)

在ReAct流程中，我们可以抓住三个关键的元素：

- **思考(Thought)**： 思考是由大模型创建的，为其行为和决定提供理论支撑。我们可以通过分析大模型的思考过程，来评估其即将采取的行动是否符合逻辑。它作为一个关键指标，能够帮助我们判断其决策的合理性。相比于人类的决策，Thought的存在赋予了大模型更出色的可解释性和可信度。
- **行动(Act)**： 行动代表大模型认为需要采取的具体行为。行动一般由两个部分构成：动作和目标，这在编程中对应着API名称和其输入参数。大模型的一大优点在于，它可以根据思考的结果，选择合适的API并生成所需的参数。这确保了ReAct框架在执行方面的实用性。
- **观察(Obs)**： 观察代表大模型如何获取外部输入。它就像大模型的感知系统，将环境的反馈信息同步给大模型，帮助它进一步进行分析或者决策。

[ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629)

来源：https://juejin.cn/post/7259018705786339385
