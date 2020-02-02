## Flask - ChainDb，PyMySQL自定义封装操作数据库
本封装库模仿ThinkPHP的链式方法操作数据库，使用之前需要先安装Python、Flask和PyMySQL，Python版本要求Python3.x版本。

### 一、PyMySQL介绍
PyMySQL是在 Python3.x 版本中用于连接 MySQL 服务器的一个库，Python2中是使用mysqldb。

### 二、PyMySQL安装
```
pip3 install pymysql
```

### 三、ChainDb方法介绍

#### 执行sql语句
##### query(sql, tuple_value)
|参数|必填|说明|
|---|---|---|
|sql|必填|需要执行的sql语句|
|tuple_value|非必填|需要替换占位符的元组|

> 该方法是执行sql语句，返回执行成功后的游标状态

```
例1：res = ChainDb('user').query('select * from user') res.fetchall()
例2：res = ChainDb('user').query('select * from user where id = %s', (7,)) res.fetchone()
```

#### 新增数据
##### insert(data)
|参数|必填|说明|
|---|---|---|
|data|必填|新增数据，字典类型|

> 参数data必填，要求是dict字典数据组<br>
> 返回值：1.单次插入数据时返回新增的数据的主键，2.批量插入数据时返回成功的行数

1.单个数据插入，直接传入dict字典数据
```
ChainDb('user').insert({'username': '张三', 'password': '123456'})
```

2.批量数据插入，传入list列表数据组
```
ChainDb('user').insert([{'username': '张三', 'password': '123456'}, {'username': '李四', 'password': '123456'}])
```

#### 更新数据
##### update(data)
|参数|必填|说明|
|---|---|---|
|data|必填|更新数据，字典类型|

> 参数data必填，要求是dict字典<br>
> 返回值：bool值

```
ChainDb('user').where('id', 1).where('username', '张三').update({'username': '王五'})
```

#### 删除数据
##### delete()
> 和where()一起调用(查询方法里会介绍)，如果没有使用where()方法链式调用时会删除数据表的所以数据<br>
> 返回值：bool值

```
ChainDb('user').where('id', 1).delete()
```

#### 查询数据
##### select()
> 查询数据列表，和多个方法一起调用(下面会依次介绍)<br>
> 返回值：数据列表list
 
```
ChainDb('user').select()
```

##### find()
> 查询单条数据，和多个方法一起调用(下面会依次介绍)<br>
> 返回值：单个数据字典dict
 
```
ChainDb('user').find()
```

##### value(field)
|参数|必填|说明|举例|
|---|---|---|---|
|field|必填|查询字段|value('username')|
> 查询单条数据，和多个方法一起调用(下面一一介绍)<br>
> 返回值：返回单个查询字段
 
```
ChainDb('user').value('username')
```

##### where(where=None, value=None, method='and')
|参数|必填|说明|
|---|---|---|
|where|非必填|查询条件，接收字典和字符串类型，为字符串时value必填且当前值为数据库字段|
|value|非必填|查询字段值|
|method|非必填|字段连接方式，and或者or|
> 参数类似TP5的where，参数where可以接受dict字典和str字符串，是操作数据库的主要方法之一，使用方式如下：

1.多个where条件同时使用
```
ChainDb('user').where('username', '小明').where('password', '123456').select()
```

2.单个where条件使用
```
ChainDb('user').where({'username': '小明', 'password': '123456'}).select()
```

3.单个where和list同时使用多条件，例如：{'字段': ['比较符号'， '查询值', 'and|or']}，多个list时最后一个list的且或不计入比较
```
ChainDb('user').where({'username': ['!=', '小明', 'or'], 'password': ['=', '123456']}).select()
```

##### field(params)
|参数|必填|说明|举例|
|---|---|---|---|
|params|必填|查询字段，可是字符串或是字典|field('username')|

> 查询字段，params为需要查询的字段，可以是字符串或是字典

```
例1：ChainDb('user').field('username').find()
例2：ChainDb('user').field('username,password').find()
例3：ChainDb('user').field(['username', 'password']).find()
```
##### page(page=1, rows=10)
|参数|必填|说明|
|---|---|---|
|page|非必填|分页页数|
|rows|非必填|分页行数|

> 数据分页，使用页面分页时会用到

```
ChainDb('user').page(1, 10).select()
```

##### group(param)
|参数|必填|说明|
|---|---|---|
|param|非必填|分页页数|

> 数据归组

```
ChainDb('user').group('username').select()
```

##### order(param)
|参数|必填|说明|
|---|---|---|
|param|非必填|分页页数|

> 数据排序，asc正序，desc倒序

```
ChainDb('user').order('id desc').select()
```

