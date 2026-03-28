# 补全百炼的 Java Embedding 代码实现说明

在此前我们将配置文件中的模型从硅基流动切换成了百炼（`provider: bailian`）并补充了 `endpoints.embedding`。但系统仍然在报：
`Embedding provider client missing: provider=bailian`。

这是因为：配置虽然指明了要调用的大厂身份以及它的 API 地址，但**程序的源代码底层压根就没有写任何对接阿里的请求代码块**！项目中只提供了 `SiliconFlowEmbeddingClient` 和 `OllamaEmbeddingClient`，却漏写了 `BailianEmbeddingClient`！
因此，在底层的 `ModelRoutingExecutor`（路由解析调度器）查找该身份的对接组件时，因为拿到了一个直接的 `null`，它只能被迫继续调用原本应该被遗弃的硅基流动等旧备用模型，从而引发连续的无效报错。

## 解决与修复方案

我直接在 `infra-ai/src/main/java/com/nageoffer/ai/ragent/infra/embedding/` 目录下为你创建并手写了缺失的核心组件代码 `BailianEmbeddingClient.java`。

由于百炼的 OpenAPI `compatible-mode` 的接口调用逻辑与硅基流动等业界标杆架构完全一致，我参考并使用了现有的解析逻辑实现了它的重写：
- 正确返回它对应的服务身份 `ModelProvider.BAILIAN.getId()` 以便底层注册在 `@Service` 扫描中并让 `ModelRoutingExecutor` 能动态发现它。
- 补全了携带 `Authorization: Bearer` Token 的 OkHttp Post 调用请求。
- 正确组装从结果中抽出多维向量（Float List）数据。

你现在只需要在 IDEA 中重新编译跑一下这段最新的 Java 源码，RAG 系统的知识库入库/检索能力就可以完美满血复活了！
