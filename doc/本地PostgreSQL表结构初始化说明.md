# 本地 PostgreSQL 表结构初始化说明

随着之前的数据库配置调整（将 JDBC URL 指向本地的 Postgres），项目成功连上了数据库。但是出现了新的启动报错：
`org.postgresql.util.PSQLException: ERROR: relation "t_query_term_mapping" does not exist`

## 问题原因

这意味着数据库已经连接成功，但**由于 PostgreSQL 容器仅仅是刚创建好的默认空库，尚未执行本项目专属的数据表初始化操作**。
所以在启动时，MyBatis Plus 按实体映射进行数据查询时，由于找不到对应的物理表，抛出了异常结束运行。

## 解决办法与执行结果

我在项目源码中发现有 `resources/database/schema_pg.sql` 和 `init_data_pg.sql` 两个专属的初始化脚本。
为了避免你手动进入容器操作的麻烦，我已经**自动在后台通过命令行**，将这两个初始化 SQL 导入到了你的 docker 容器内部：

```
docker exec -i ragent-postgres psql -U root -d ragent < resources/database/schema_pg.sql
docker exec -i ragent-postgres psql -U root -d ragent < resources/database/init_data_pg.sql
```

**执行成功！所有表结构和基础账户/数据均导入完成。**

你可以直接再次在 IDEA 中重新启动应用了。 
