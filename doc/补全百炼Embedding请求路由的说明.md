# 修复大模型配置中缺失百炼（Bailian）Embedding 接口路由的说明

在刚才的大模型配置替换中，我们把 `embedding` 功能指定给了阿里百炼（`provider: bailian`）。
但是在系统原有的 `application.yaml` 配置里，并没有定义阿里百炼的 `embedding` URL 请求路径（它只配置了 `chat` 和 `rerank` 路径）。由于找不到发往哪里的路由，系统发出了一个警告 `Embedding provider client missing: provider=bailian`。然后系统尝试调用备胎（硅基流动和本地 Ollama模型），他们全都没有可用的 API Key 或模型，从而导致报错。

## 解决办法
我已经自动修改了 `bootstrap/src/main/resources/application.yaml` 中的 `ai.providers.bailian.endpoints` 节点，为其补全了 OpenAI 兼容规范的 Embedding 调用地址：
```yaml
    bailian:
      url: https://dashscope.aliyuncs.com
      api-key: ${BAILIAN_API_KEY:}
      endpoints:
        chat: /compatible-mode/v1/chat/completions
        rerank: /api/v1/services/rerank/text-rerank/text-rerank
        embedding: /compatible-mode/v1/embeddings # <--- 新增加的路由
```

现在重启服务端 `ragent-service` 后，系统在使用百炼的大模型生成文本切片向量时就能成功发起请求了。
