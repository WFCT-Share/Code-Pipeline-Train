# Api文档

## 1. logging_logger

```py
from Ealogger import logging_logger  # TODO: yzh:这里要写出的是如何调用的用法，不是定义哦
logger = logging_logger(
    logger_name='mylogger',
    level=logging.INFO,
    log_file=None
) 
```

### 功能概述

1. **创建Logger对象**：
   - 根据给定的`logger_name`创建或获取一个Logger对象。
   - 如果已经存在同名的Logger对象，则直接返回已有的对象，避免重复创建。
2. **设置日志级别**：
   - 通过`level`参数设置Logger的日志级别，决定了哪些级别的日志会被记录（如DEBUG、INFO、WARNING、ERROR等）。
3. **定义日志格式**：
   - 设置日志的输出格式，包括时间、Logger名称、日志级别和日志消息。
4. **添加日志输出目标**：
   - **控制台输出**：默认将日志输出到控制台。
   - **文件输出（可选）**：如果提供了`log_file`参数，则将日志同时输出到指定的文件中。

### 参数说明

- **logger_name** (str):
  - 用于指定Logger的名称，便于识别和管理不同的日志记录器。
  - 默认值为 `'mylogger'`。
- **level** (int):
  - 指定日志级别，决定了哪些日志消息会被处理。
  - 常见的级别包括 `logging.DEBUG`, `logging.INFO`, `logging.WARNING`, `logging.ERROR` 等。
  - 默认值为 `logging.INFO`。
- **log_file** (str, 可选):
  - 指定日志文件的路径，如果提供此参数，日志将同时写入该文件。
  - 默认值为 `None`，表示仅输出到控制台。

### 返回值

- 返回一个配置好的`Logger`对象，可以用于记录日志消息。

### 示例

```python
from Ealogger import logging_logger # TODO: yzh: 正确的外部调用方法是这样的

# 创建一个Logger，日志级别为DEBUG，并将日志输出到文件 'app.log'
logger = logging_logger(logger_name='my_app_logger', level=logging.DEBUG, log_file='app.log')

# 记录不同级别的日志
logger.debug("这是一个 DEBUG 级别的日志消息")
logger.info("这是一个 INFO 级别的日志消息")
logger.warning("这是一个 WARNING 级别的日志消息")
logger.error("这是一个 ERROR 级别的日志消息")
```



## 2. delete_db # TODO: 此处的标题应为日志数据库的管理方法

```python
def delete_db(db_path: str = 'data/app.db') -> None
```

### 功能概述

- 删除指定路径的数据库文件。如果文件不存在，则记录日志说明无需删除。

### 参数说明

- **db_path** (str): 数据库文件路径，默认值为 `'data/app.db'`。

### 示例:

```python
delete_db('data/app.db')
```

## 

## 3. Log类

```python
class Log(Base)
```

### 功能概述

- 用于定义日志记录表的结构，存储模块日志信息。

### 字段

- **record_id**: 自增主键，`Integer` 类型。
- **timestamp**: 日志时间，`String(19)` 类型，格式为 `'YYYY-MM-DD HH:MM:SS'`。
- **module_name**: 模块或函数信息，`String(255)` 类型。
- **log_level**: 日志等级，`String(10)` 类型。
- **log_content**: 日志内容，`Text` 类型，可存储大量文本。





## 4. log_DB # TODO: yzh: 注意一下，log_DB并没有给出外部调用的方法，需要和其他两个对象区分哦

```python
class log_DB

```

### 外部调用方式：

`from Ealogger import log_DB  `

### 构造函数: # TODO: yzh: 用相同的方式修改如下的表述哦，不用给出定义的方式，而是给出调用的方式

```python
def __init__(self, db_path: str = 'logs/app_log.db', forced_formatting: bool = False, echo: bool = False)
db_logger.add_log(
    module_name="init",
    log_level="INFO",
    log_content="数据库日志器初始化成功（默认配置）"
)
```

#### 参数说明

- **db_path** (str): SQLite 数据库文件路径，默认值为 `'logs/app_log.db'`。
- **forced_formatting** (bool): 是否强制删除已有数据库并重建，默认值为 `False`。
- **echo** (bool): 是否打印 SQL 语句到控制台（调试用），默认值为 `False`。

### 方法

#### 	add_log

```python
def add_log(self, module_name: str, log_level: str, log_content: str) -> None
```

##### 功能概述

- 添加一条日志记录到数据库。

##### 参数说明

- **module_name** (str): 模块或函数名称。
- **log_level** (str): 日志等级，如 `'info'`, `'debug'` 等。
- **log_content** (str): 日志内容。

### 示例:

```python
# 初始化 log_DB
log_db = log_DB(db_path='logs/app_log.db', forced_formatting=True, echo=True)

# 添加日志记录
log_db.add_log(module_name='my_module', log_level='info', log_content='系统正常运行')
log_db.add_log(module_name='my_module', log_level='error', log_content='发生错误！')
```



## 5. finish_ini_logger_sql_db

```python
def finish_ini_logger_sql_db() -> None
```

### 功能

- 初始化完成数据库后在配置文件中进行注册，防止覆盖创建。


# TODO: yzh: 还剩下一个sql_logger的对象没有写





## 6.sql_logger

### 功能

将日志记录到SQLite数据库  

  

#### 1.模块组成

#### **数据模型层**：`Log`类继承自`Base`，定义了日志表结构

- **数据库操作层**：`log_DB`类封装了所有数据库操作
- **配置管理层**：通过`logger.json`管理初始化状态
- **工具函数**：`delete_db()`和`finish_ini_logger_sql_db()`

####  设计模式

- **单例模式**：通过配置文件控制数据库初始化，确保单实例行为
- **ORM模式**：使用SQLAlchemy的声明式基类`Base`
- **工厂模式**：`log_DB`类作为日志记录器的工厂

####        **2.外部调取方式**

```
from Ealogger import sql_logger
```

#### 参数说明

* connection_string:指定日志要写入的数据库类型和连接方式。
* table_name:指定存储日志的数据库表名称
* flush_interval:控制日志批量写入数据库的时间间隔（单位：秒）

### 完整初始化示例

```
from sql_logger import sql_logger

# 生产环境配置
logger = sql_logger(
    connection_string="mysql://user:pass@dbserver/logdb",
    table_name="prod_app_logs",
    flush_interval=120,      # 2分钟批量写入一次
    batch_size=500,          # 每批最多500条
    max_queue_size=100000,   # 队列最多10万条
    log_level="WARNING"      # 只记录WARNING及以上级别
)

# 开发环境配置
dev_logger = sql_logger(
    connection_string="sqlite:///debug.db",
    table_name="dev_logs",
    flush_interval=10,       # 10秒写入一次方便调试
    log_level="DEBUG"        # 记录所有级别日志
)
```

