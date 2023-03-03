# 事务定义
事务（Transaction）：将多条 DML 语句作为一个整体执行称为事务。对于集合中的 DML 语句，数据库要么全部执行成功，要么全部失败，不允许部分成功的情况。

- DML(Data Manipulation Language, 数据操作语言): 对数据的修改操作，update/insert/delete

# 事务的 ACID 特性
Atomicity(原子性)：原子性代表事务中的操作，要么全部执行成功，要么全部都不执行。
Consistency(一致性): 指数据库必须从一个正确的状态迁移到另一个正确的状态。
Isolation(隔离性): 并发执行多个事务时，各个事务之间互不干扰。
Durability(隔离性): 事务执行完后，数据不会丢失。

# 多事务并发问题

创建表如下：
```
CREATE TABLE t_test_balance (
 id INT,
 name VARCHAR(100),
 balance bigint,
 PRIMARY KEY (id)
) Engine=InnoDB CHARSET=utf8;
```
插入一行初始数据如下：
|  id | name | balance | 
|  ----  | ----  | ---- |
| 1  | iker | 100 |
| 2  | alice | 50 |

## 脏读
所谓脏读，即读到了其他事务中尚未提交的数据。
场景如下:
- iker 需要查询当前的余额，开启一个事务A 执行;
- 与此同时，iker 又充值了 40 元到账户中，现需要从 iker 的余额增加 40，开启另外一个事务 B 执行。

|  执行顺序 | 事务A | 事务B| 
|  ----  | ----  | ---- |
| 1  | BEGIN | |
| 2  | | BEGIN |
| 3  |  | update t_test_balance set Fbalance = 140 where Fid=1 |
| 4  | select Fbalance from t_test_balance where Fid=1 | |




## 脏写
场景如下，假设 iker 花费 30 购买了商品，现在需要从 iker 的余额扣除 30，开启一个事务A 执行; 同时 iker 又充值了 40 元到账户中，现需要从 iker 的余额增加 40，开启另外一个事务 B 执行。

|  执行顺序 | 事务A | 事务B| 
|  ----  | ----  | ---- |
| 1  | Begin | |
| 2  | update t_test_balance set Fbalance | 50 |
