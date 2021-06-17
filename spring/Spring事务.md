Sping事务的传播行为

| 事务传播类型              | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| Propagation.REQUIRED      | 如果当前存在事务，则==加入==到当前事务；如果当前没有事务，则新建一个事务 |
| Propagation.SUPPORTS      | 如果当前存在事务，则加入到当前事务；如果当前没有事务，则以非事务方式运行 |
| Propagation.MANDATORY     | 如果当前存在事务，则加入到当前事务；如果当前没有事务，则==抛出异常== |
| Propagation.REQUIRES_NEW  | 新建一个事务；如果当前存在事务，则==挂起==到当前事务         |
| Propagation.NESTED        | 如果当前存在事务，则==嵌套==到当前事务；如果当前没有事务，则新建一个事务 |
| Propagation.NOT_SUPPORTED | 以非事务方式运行；如果当前存在事务，则挂起当前事务           |
| Propagation.NEVER         | 以非事务方式运行；如果当前存在事务，则抛出异常               |

Sping事务隔离级别

| 事务隔离级别               | 说明 |
| -------------------------- | ---- |
| Isolation.DEFAULT          |      |
| Isolation.READ_UNCOMMITTED |      |
| Isolation.READ_COMMITTED   |      |
| Isolation.REPEATABLE_READ  |      |
| Isolation.SERIALIZABLE     |      |

