# Navicat 连接本地数据库配置指南

如果你想通过电脑上的可视化数据库工具（比如 Navicat 等）来查阅验证目前项目容器内的数据，请选择 **PostgreSQL** 类型的连接建立，具体的连接参数信息如下：

### Navicat 连接配置参数

- **连接类型 (Connection Type)**：PostgreSQL
- **连接名 (Connection Name)**：ragent-local (任意起个名字都可以)
- **主机 (Host)**：`127.0.0.1` 或者 `localhost`
- **端口 (Port)**：`5432`
- **初始数据库 (Initial Database)**：`ragent`
- **用户名 (User Name)**：`root`
- **密码 (Password)**：`123456`

### 补充说明
1. 本地的 docker-compose 虽然是以容器方式运行的数据库，但在 `docker-compose.yml` 里面我们将 `5432` 端口暴露并穿透到了本地，因此用 `127.0.0.1:5432` 是可以直接连上的。
2. 虽然是 AI RAG 知识库系统，但底层依然是用经典的 Postgre + PgVector 插件完成的，所以使用标准的 PostgreSQL 客户端就可以查询维护数据。

配置好后你可以点击 Navicat 左下角的“测试连接(Test Connection)”按钮，提示成功即代表配置无误，可以直接打开。
