# 4.SQLite3

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