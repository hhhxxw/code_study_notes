mybatis操作数据库的步骤：
1. 准备工作（创建SpringBoot工程、数据库表、实体类）
2. 引入Mybatis的相关依赖，配置Mybatis（数据库的连接信息）
3. 编写SQL语句（注解/XML）



创建`SpringBoot`工程的时候，勾选`Mybatis Framework`就可以自动在pom.xml中引入`Mybatis`的相关依赖。


application.properties:
```sql
spring.application.name=springboot-mybatis-qucikstart  
# 驱动类名称  
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
  
# 数据库连接的url  
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis  
  
#连接数据库的用户名  
spring.datasource.username=root  
  
# 连接数据库的密码  
spring.datasource.password=1234
```

这里会出现`com.mysql.cj.jdbc.Driver`的爆红问题，如何解决呢
![[Pasted image 20250117122025.png]]
配置文件中指定版本号`<version>8.0.33</version>`
```
```XML
<!--        解决mysql.cj.jdbc.Driver爆红-->  
        <dependency>  
            <groupId>com.mysql</groupId>  
            <artifactId>mysql-connector-j</artifactId>  
            <version>8.0.33</version>  
        </dependency>
```

使用lombok遇到的问题
```
java: 找不到符号
  符号:   方法 setUsername(java.lang.String)
  位置: 类型为org.itheima.springbootmybatiscrud.pojo.Emp的变量 emp
```

不需要在创建SpringBoot工程的时候勾选lombok，只要在pom文件中添加如下依赖即可
```XML
<dependency>  
    <groupId>org.projectlombok</groupId>  
    <artifactId>lombok</artifactId>  
</dependency>
```

## JDBC

JDBC程序操作数据库的步骤：
1. 注册驱动
2. 获取连接对象
3. 执行SQL语句，返回执行结果
4. 处理执行结果
5. 释放资源

JDBC具体代码实现

```java
import com.itheima.pojo.User;
import org.junit.jupiter.api.Test;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

public class JdbcTest {
    @Test
    public void testJdbc() throws Exception {
        //1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");

        //2. 获取数据库连接
        String url="jdbc:mysql://127.0.0.1:3306/mybatis";
        String username = "root";
        String password = "1234";
        Connection connection = DriverManager.getConnection(url, username, password);

        //3. 执行SQL
        Statement statement = connection.createStatement(); //操作SQL的对象
        String sql="select id,name,age,gender,phone from user";
        ResultSet rs = statement.executeQuery(sql);//SQL查询结果会封装在ResultSet对象中

        List<User> userList = new ArrayList<>();//集合对象（用于存储User对象）
        //4. 处理SQL执行结果
        while (rs.next()){
            //取出一行记录中id、name、age、gender、phone下的数据
            int id = rs.getInt("id");
            String name = rs.getString("name");
            short age = rs.getShort("age");
            short gender = rs.getShort("gender");
            String phone = rs.getString("phone");
            //把一行记录中的数据，封装到User对象中
            User user = new User(id,name,age,gender,phone);
            userList.add(user);//User对象添加到集合
        }
        //5. 释放资源
        statement.close();
        connection.close();
        rs.close();

        //遍历集合
        for (User user : userList) {
            System.out.println(user);
        }
    }
}
```


## 入门案例

准备数据库表，在指定数据中，编写数据库表，这里要对应，因为后面配置需要明白到底是在哪一个数据库中。
```sql
-- 部门管理
create table dept
(
    id          int unsigned primary key auto_increment comment '主键ID',
    name        varchar(10) not null unique comment '部门名称',
    create_time datetime    not null comment '创建时间',
    update_time datetime    not null comment '修改时间'
) comment '部门表';
-- 部门表测试数据
insert into dept (id, name, create_time, update_time)
values (1, '学工部', now(), now()),
       (2, '教研部', now(), now()),
       (3, '咨询部', now(), now()),
       (4, '就业部', now(), now()),
       (5, '人事部', now(), now());


-- 员工管理
create table emp
(
    id          int unsigned primary key auto_increment comment 'ID',
    username    varchar(20)      not null unique comment '用户名',
    password    varchar(32) default '123456' comment '密码',
    name        varchar(10)      not null comment '姓名',
    gender      tinyint unsigned not null comment '性别, 说明: 1 男, 2 女',
    image       varchar(300) comment '图像',
    job         tinyint unsigned comment '职位, 说明: 1 班主任,2 讲师, 3 学工主管, 4 教研主管, 5 咨询师',
    entrydate   date comment '入职时间',
    dept_id     int unsigned comment '部门ID',
    create_time datetime         not null comment '创建时间',
    update_time datetime         not null comment '修改时间'
) comment '员工表';
-- 员工表测试数据
INSERT INTO emp (id, username, password, name, gender, image, job, entrydate, dept_id, create_time, update_time)
VALUES 
(1, 'jinyong', '123456', '金庸', 1, '1.jpg', 4, '2000-01-01', 2, now(), now()),
(2, 'zhangwuji', '123456', '张无忌', 1, '2.jpg', 2, '2015-01-01', 2, now(), now()),
(3, 'yangxiao', '123456', '杨逍', 1, '3.jpg', 2, '2008-05-01', 2, now(), now()),
(4, 'weiyixiao', '123456', '韦一笑', 1, '4.jpg', 2, '2007-01-01', 2, now(), now()),
(5, 'changyuchun', '123456', '常遇春', 1, '5.jpg', 2, '2012-12-05', 2, now(), now()),
(6, 'xiaozhao', '123456', '小昭', 2, '6.jpg', 3, '2013-09-05', 1, now(), now()),
(7, 'jixiaofu', '123456', '纪晓芙', 2, '7.jpg', 1, '2005-08-01', 1, now(), now()),
(8, 'zhouzhiruo', '123456', '周芷若', 2, '8.jpg', 1, '2014-11-09', 1, now(), now()),
(9, 'dingminjun', '123456', '丁敏君', 2, '9.jpg', 1, '2011-03-11', 1, now(), now()),
(10, 'zhaomin', '123456', '赵敏', 2, '10.jpg', 1, '2013-09-05', 1, now(), now()),
(11, 'luzhangke', '123456', '鹿杖客', 1, '11.jpg', 5, '2007-02-01', 3, now(), now()),
(12, 'hebiweng', '123456', '鹤笔翁', 1, '12.jpg', 5, '2008-08-18', 3, now(), now()),
(13, 'fangdongbai', '123456', '方东白', 1, '13.jpg', 5, '2012-11-01', 3, now(), now()),
(14, 'zhangsanfeng', '123456', '张三丰', 1, '14.jpg', 2, '2002-08-01', 2, now(), now()),
(15, 'yulianzhou', '123456', '俞莲舟', 1, '15.jpg', 2, '2011-05-01', 2, now(), now()),
(16, 'songyuanqiao', '123456', '宋远桥', 1, '16.jpg', 2, '2010-01-01', 2, now(), now()),
(17, 'chenyouliang', '123456', '陈友谅', 1, '17.jpg', NULL, '2015-03-21', NULL, now(), now());
```

**application.properties中引入数据库连接信息**
其中值得注意的是驱动类（.mysql.cj.jdbc.Driver爆红问题）和数据库连接（数据库的路径）

```XML
spring.application.name=springboot-mybatis-crud  
# 驱动类名称  
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
  
# 数据库连接的url  
spring.datasource.url=jdbc:mysql://localhost:3306/db02?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC  
  
#连接数据库的用户名  
spring.datasource.username=root  
  
# 连接数据库的密码  
spring.datasource.password=1234
```

**创建对应的实体类Emp（实体类属性采用驼峰命名）**
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Emp {
    private Integer id;
    private String username;
    private String password;
    private String name;
    private Short gender;
    private String image;
    private Short job;
    private LocalDate entrydate;     //LocalDate类型对应数据表中的date类型
    private Integer deptId;
    private LocalDateTime createTime;//LocalDateTime类型对应数据表中的datetime类型
    private LocalDateTime updateTime;
}
```

日志输出：

```XML
#指定mybatis输出日志的位置, 输出控制台
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```
这样就可以在控制台看到执行的过程了，但是这知识预编译-例如id的值用？去占位

**准备Mapper接口：EmpMapper**

实现
- 删除
- 增加
- 修改

## Mybatis的XML配置文件

Mybatis开发有两种方式：注解和XML，其中，注解主要用来解决一些简单的增删改查，如果要实现复杂的SQL功能，建议使用XML来配置映射语句。

### XML配置文件规范
将SQL写在配置文件中，这个过程要遵守一些规范





