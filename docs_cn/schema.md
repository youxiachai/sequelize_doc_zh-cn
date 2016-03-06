## Syncing

`sequelize.sync()` will, based on your model definitions, create any missing tables.
If `force: true` it will first drop tables before recreating them.

## 同步

`sequelize.sync()` 将会根据你定义的模型创建缺少的数据库表。如果使用`force: true`将会首先在创建数据表之前把现有的数据库表全部删除。

## Migrations / Manual schema changes

Sequelize has a [sister library](https://github.com/sequelize/umzug) for handling execution and logging of migration tasks.
Sequelize provides a list of ways to programmatically create or change a table schema.

## 迁移 / 手动 元数据改变

Sequelize 有一个 [sister library](https://github.com/sequelize/umzug) 用于处理执行和记录迁移任务

Sequelize 提供一系列的方式用于可编程创建或者改变架构表

### createTable

### addColumn

### changeColumn

### removeColumn

### addIndex

### removeIndex

### addConstraint

### removeConstraint


