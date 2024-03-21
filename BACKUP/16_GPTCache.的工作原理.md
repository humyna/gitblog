# [GPTCache 的工作原理](https://github.com/humyna/gitblog/issues/16)

GPTCache 利用在线服务的数据局部性特点，存储常用数据，降低检索时间，减轻后端服务器负载。与传统缓存系统不同，GPTCache 进行语义缓存，识别并存储相似或相关的查询以提高缓存命中率。

GPTCache 通过 embedding 算法将查询问题转换为向量并使用向量数据库进行相似性搜索，从缓存中检索相关查询。 GPTCache 采用了模块化的设计，允许用户灵活自定义每个模块。

虽然语义缓存可能会返回假正类（false positive）和负类（negative）结果，但 GPTCache 提供 3 种性能指标来帮助开发人员优化其缓存系统。

通过上述流程，GPTCache 能够从缓存中寻找并召回相似或相关查询。
![image](https://github.com/humyna/gitblog/assets/2505439/3e6bcfed-9f14-4435-a0d6-1b6ef7e6c61f)

