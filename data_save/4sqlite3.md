# SQLite3

```objc
SQLite3是一款开源的嵌入式关系型数据库,可移植性好,易使用,内存开销小.

SQLite3是无类型的,意味着你可以保存任何类型的数据到任意表的任意字段中.

SQLite3常用的4种数据类型:text(文本字符串), integer(整型值), real(浮点值),  blob(二进制数据(比如文件)).

在iOS中使用SQLite3,首先要添加库文件'libsqlite3.dylib'和导入主头文件#import<sqlite3.h>
```

SQL语句的特点:

    1> 不区分大小写;

    2> 每条语句都必须以分号;结尾
    
SQL中常用的关键字:

    pselect、insert、update、delete、from、create、where、desc、order、by、group、table、alter、view、index等等

    数据库中不可以使用关键字来命名表,字段.
SQL语句种类:

    1> 数据定义语句（DDL：Data Definition Language） 

    包括create和drop等操作 ;

    在数据库中创建新表或删除表（create table或 drop table）.

    2> 数据操作语句（DML：Data Manipulation Language） 

    包括insert、update、delete等操作 ;

    上面的3种操作分别用于添加、修改、删除表中的数据 .

    3> 数据查询语句（DQL：Data Query Language）

    可以用于查询获得表中的数据 ;

    关键字select是DQL（也是所有SQL）用得最多的操作 ;

    其他DQL常用的关键字有where，order by，group by和having创建.

创表: 

    create table if not exists t_student (id integer, name text, age inetger, score real) ;

删表:

    drop table if exists t_student;

插入数据(insert):

    insert into t_student (name, age) values ('JN', 22) ;

    注意:数据库中的字符串内容应该用单引号''括住.

更新数据(updata):

    pupdate t_student set name = 'jack', age = 20 ;     

    注意:上面的更新会将t_student表中所有记录的name都改为jack,age都改为20;

删除数据(delete):

    delete from t_student;

    会将t_student表中所有记录都删掉.

    如果只想更新或者删除某些固定的记录,那就必须在DML语句后加上一些条件.示例如下:
```objc
 // 将t_student表中年龄大于10 并且 姓名不等于jack的记录，年龄都改为 5 
 update t_student set age = 5 where age > 10 and name != 'jack' ; 
 // 删除t_student表中年龄小于等于10 或者 年龄大于30的记录 
 delete from t_student where age <= 10 or age > 30 ;
 ```
 查询语句(DQL):

    select * from t_student where age > 10 ;  //  条件查询条件语句:

主键约束:

    每张表都必须有一个主键,用来标识记录的唯一性.

什么是主键:

    主键(Primary Key,简称PK),用来唯一的标识某一条记录.

    例如t_student可以增加一个id字段作为主键,相当于人的身份证.

    主键可以是一个字段或多个字段.

外键约束:

    利用外键约束可以来建立表与表之间的联系.

    外键的一般情况是:一张表的某个字段引用着另一张表的主键字段.

打开,关闭数据库

```objc
创建或打开数据库:

// path为：~/Documents/person.db
sqlite3 *db;
int result = sqlite3_open([path UTF8String], &db);

代码解析:

    sqlite3_open()将根据文件路径打开数据库,如果不存在,则会创建一个新的数据库.如果result等于常量SQLITE_OK,则表示成功打开数据库.

    sqlite *db:一个打开的数据库实例.

    数据库文件的路径必须以C字符串(而非NSString)传入.
```
```objc
关闭数据库:sqlite3_close(db)
执行不返回语句的SQL语句

char *errorMsg;  // 用来存储错误信息
char *sql = "create table if not exists t_person(id integer primary key autoincrement, name text, age integer);";
int result = sqlite3_exec(db, sql, NULL, NULL, &errorMsg);

代码解析:

    sqlite3_exec()可以执行任何SQL语句,比如创表, 更新, 插入和删除操作.但是一般不用它执行查询语句,因为它不会返回查询到得数据.

    sqlite3_exec()还可以执行的语句:

    1> 开启事务:begain transaction;

    2> 回滚事务:rollback

    3> 提交事务:commit
```
SQLite函数总结:
```objc
1.打开数据库
int sqlite3_open(
    const char *filename,   // 数据库的文件路径
    sqlite3 **ppDb          // 数据库实例
);
 
2.执行任何SQL语句
int sqlite3_exec(
    sqlite3*,                                  // 一个打开的数据库实例
    const char *sql,                           // 需要执行的SQL语句
    int (*callback)(void*,int,char**,char**),  // SQL语句执行完毕后的回调
    void *,                                    // 回调函数的第1个参数
    char **errmsg                              // 错误信息
);
 
3.检查SQL语句的合法性（查询前的准备）
int sqlite3_prepare_v2(
    sqlite3 *db,            // 数据库实例
    const char *zSql,       // 需要检查的SQL语句
    int nByte,              // SQL语句的最大字节长度
    sqlite3_stmt **ppStmt,  // sqlite3_stmt实例，用来获得数据库数据
    const char **pzTail
);
 
4.查询一行数据
int sqlite3_step(sqlite3_stmt*); // 如果查询到一行数据，就会返回SQLITE_ROW
 
5.利用stmt获得某一字段的值（字段的下标从0开始）
double sqlite3_column_double(sqlite3_stmt*, int iCol);  // 浮点数据
int sqlite3_column_int(sqlite3_stmt*, int iCol); // 整型数据
sqlite3_int64 sqlite3_column_int64(sqlite3_stmt*, int iCol); // 长整型数据
const void *sqlite3_column_blob(sqlite3_stmt*, int iCol); // 二进制文本数据
const unsigned char *sqlite3_column_text(sqlite3_stmt*, int iCol);  // 字符串数据
```
