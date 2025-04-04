# Ealogger api文档

## 1. logging_logger

```py
from Ealogger import logging_logger  
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
from Ealogger import logging_logger 

# 创建一个Logger，日志级别为DEBUG，并将日志输出到文件 'app.log'
logger = logging_logger(logger_name='my_app_logger', level=logging.DEBUG, log_file='app.log')

# 记录不同级别的日志
logger.debug("这是一个 DEBUG 级别的日志消息")
logger.info("这是一个 INFO 级别的日志消息")
logger.warning("这是一个 WARNING 级别的日志消息")
logger.error("这是一个 ERROR 级别的日志消息")
```



## 2. Log类

### 功能概述

* 用于定义日志记录表的结构，存储模块日志信息,继承自Base。

* 由模块开发者查看和管理。

### 接口：

```
from Ealogger.sql_log import Log
```

### 字段

| **record_id**: 自增主键，`Integer` 类型。                    |
| ------------------------------------------------------------ |
| **timestamp**: 日志时间，`String(19)` 类型，格式为 `'YYYY-MM-DD HH:MM:SS'`。 |
| **module_name**: 模块或函数信息，`String(255)` 类型。        |
| **log_level**: 日志等级，`String(10)` 类型。                 |
| **log_content**: 日志内容，`Text` 类型，可存储大量文本。     |

### 示例：创建Log对象记录日志

#### 1.获取当前时间作为日志时间

```
now = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
```

#### 2.生成对象

```
log_entry = Log(
    record_id=1,
    timestamp=now,
    module_name='my_module.some_func',
    log_level='INFO',
    log_content='This is a dairy.'
)
```

#### 3.输出Log对象

```
print(log_entry)
```

`print(log_entry)` 会自动调用 `log_entry` 对象的 `__repr__` 方法，并将返回的字符串打印到控制台。

## 3. log_DB 


### 接口：

```
import Ealogger.sql_log
```

### 构造函数: 

```python
db = Ealogger.sql_log.log_DB(db_path='my_app_log.db', forced_formatting=False, echo=False)
```

### 参数说明：

产生一个名为db的log_DB对象

- **db_path** (str): SQLite 数据库文件路径，默认值为 `'logs/app_log.db'`。
- **forced_formatting** (bool): 是否强制删除已有数据库并重建，默认值为 `False`。
- **echo** (bool): 是否打印 SQL 语句到控制台（调试用），默认值为 `False`。

### 方法：

####  add_log

```python
db.add_log(module_name='my_module', log_level='info', log_content='This is a log message.')

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
db=Ealogger.sql_log.log_DB(db_path='logs/app_log.db', forced_formatting=True, echo=True)

# 添加日志记录
db.add_log(module_name='my_module', log_level='info', log_content='系统正常运行')
db.add_log(module_name='my_module', log_level='error', log_content='发生错误！')
```



## 4. 辅助函数

### 接口：

```
import Ealogger.sql_log
```

### 功能

- 在程序启动时，首先检查 `ini_statement` 标志位。
- 如果标志位为 `0`，则执行数据库初始化操作，并调用 `finish_ini_logger_sql_db()` 函数更新标志位。
- 如果标志位为 `1`，则跳过数据库初始化操作，直接使用已存在的数据库。

通过这种方式，程序能够确保数据库只被初始化一次，从而避免数据丢失或损坏。

### 调用方式：

```
Ealogger.sql_log.finish_ini_logger_sql_db()
```



## 5.sql_logger

### 功能

将日志记录到SQLite数据库  

### 模块组成

#### **数据模型层**：`Log`类继承自`Base`，定义了日志表结构

- **数据库操作层**：`log_DB`类封装了所有数据库操作
- **配置管理层**：通过`logger.json`管理初始化状态
- **工具函数**：`delete_db()`和`finish_ini_logger_sql_db()`

####  设计模式

- **单例模式**：通过配置文件控制数据库初始化，确保单实例行为
- **ORM模式**：使用SQLAlchemy的声明式基类`Base`
- **工厂模式**：`log_DB`类作为日志记录器的工厂

###        **接口**

```
from Ealogger import sql_logger
```



### 参数说明

* connection_string:指定日志要写入的数据库类型和连接方式。
* table_name:指定存储日志的数据库表名称
* flush_interval:控制日志批量写入数据库的时间间隔（单位：秒）

### 完整初始化示例

```
from Ealogger import sql_logger

# 创建 sql_logger 对象
my_logger = sql_logger(module_name="my_module")

# 记录 info 级别的日志
my_logger.info(log_content="This is an info message.")

# 记录 debug 级别的日志
my_logger.debug(log_content="This is a debug message.")

# 记录 warning 级别的日志
my_logger.warning(log_content="This is a warning message.")

# 记录 error 级别的日志
my_logger.error(log_content="This is an error message.")


```

##  6.日志数据库的管理方法

### 接口：

```
import Ealogger.sql_log
```

### 功能概述

- 删除指定路径的数据库文件。如果文件不存在，则记录日志说明无需删除。

### 参数说明

- **db_path** (str): 数据库文件路径，默认值为 `'data/app.db'`。

### 示例:

```python
Ealogger.sql_log.delete_db('my_database')
```







