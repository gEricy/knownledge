SQL注入时一种将恶意的SQL代码<u>插入或添加到应用的输入参数</u>的攻击，攻击者探测出开发编程过程中的漏洞，利用这些漏洞，巧妙的<u>构造SQL语句</u>，对数据库系统的内容直接进行<u>检索或修改</u>。

用户输入可控，代码对用户输入进行了拼接，带入SQL语句，产生SQL注入漏洞。

---

### 判断`是否存在`SQL注入

**报错注入**：在URL或表单中输入一个`单引号`或者`其他特殊符号`，页面出现错误说明此页面存在SQL注入。如果页面正常显示，说明有字符被过滤或不存在SQL注入。

---



### SQL注入类型

### 1. 数字型

```sql
SELECT * FROM user WHERE id=1 该语句存在SQL注入
说明：
	攻击方式桶字符型的“登录注入攻击”，只是不需要加单引号
```



### 2. 字符型

#### 2.1. 登录注入攻击

‘or 1=1 \-\-

‘or 1=1 #

```sql
String sql = " select * from user_table where username=' "userName+" ' and password=' "password" ' ";

--当输入了上面的用户名和密码，上面的SQL语句变成：
SELECT * FROM user_table WHERE username=' 'or 1 = 1 -- and password=' '

"""
分析SQL语句：
	username='' or 1=1 用户名等于'' or 1=1 ,那么, 这个条件一定会成功
	后面加两个--，这意味着注释，它将后面的语句注释，让它们不起作用，用户轻易骗过系统，获取合法身份。
--这还是比较温柔的，如果是执行
    SELECT * FROM user_table WHERE username=''; DROP DATABASE (DB Name) --' and password=''
    其后果可想而知…
"""
```

#### 2.2. 联合union注入攻击

'union select 1,2#

```sql
SELECT first_name, last_name FROM users WHERE user_id = '$id';
用户输入的字符串存在$id变量中，可以看到，上面没有任何处理用户输入的字符串的函数。因此，可以肯定这里存在SQL注入。
我们仍然可以输入'or 1#，使得SQL语句变为：SELECT first_name, last_name FROM users WHERE user_id = '' or 1#' ==> 从而查询到所有的first_name和last_name
```

```sql
' union select 1,2#；
' union select user(),database()#；
```

### 3. like语句中的注入

程序中sql语句拼装:

```sql
$sql = 'student_name like '"%'.$name.'%"';
```

貌似正常的sql语句

```sql
SELECT * FROM tblStudent WHERE unit_name like "%aaa%" order by create_time desc limit 0, 30 ;
```

倘若想要借此进行sql注入,input输入框中输入aaa %" or "1%" = "1    ,则sql语句被拼接为

```sql
SELECT * FROM tblStudent WHERE  unit_name like "%aaa %" or "1%" = "1%" order by create_time desc limit 0, 30  显示所有的列.　　
```

这似乎无关痛痒，倘若input输入框换成aaa%";drop table tbl_test;# ，sql语句成为

```sql
SELECT * FROM tblStudent WHERE unit_name like "%aaa%";drop table tbl_test;#%" order by create_time desc limit 0, 30;
```

解决方法很简单:

```sql
$binName = bin2hex("%$name%");
$arrConds[]  = " course_name like unhex('$binName')";<br><br>sql:
SELECT * FROM tblStudent WHERE unit_name like hex('2520636f7572736525223b64726f70207461626c652074626c5f746573743b2325') order by create_time desc limit 0, 30;
```



---



### 防止SQL注入，我们需要注意以下几个要点：

- 1.**永远不要信任用户的输入**。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和 双"-"进行转换等。
- 2.永远不要使用**动态拼装**sql，可以使用参数化的sql或者直接使用存储过程进行数据查询存取。
- 3.**永远不要使用管理员权限的数据库连接**，为每个应用使用单独的权限有限的数据库连接。
- 4.不要把机密信息直接存放，加密或者hash掉密码和敏感的信息。
- 5.应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装
- 6.sql注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用sql注入检测工具jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN等。采用MDCSOFT-IPS可以有效的防御SQL注入，XSS攻击等。



---

### SQL注入特殊字符处理

##### 单引号 ‘

##### 注释符号 -- # /**/

##### 分号 ;

##### 通配符 下划线_  百分号%

> 下划线_ : 表示任意一个字符
>
> 百分号%：表示任意多个字符

##### 方括弧 []

如果是没有配成对的单个左方括号，查询时这个左方括号会被忽略

> WHERE T2.name like (%+ [ + %) 
>
> 等价于下面这个语句 WHERE T2.name like (%+ + %) 
>
> ==> 这将导致查询结果中包含表中的全部记录，就像没有任何过滤条件一样。 

