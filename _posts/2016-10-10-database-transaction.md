---
layout: post
title:  "数据库事务"
date:   2016-10-10 12:29:00 +0800
categories: db
tag:  ['MySql', 'Spring']
---

* content
{:toc}

---

**数据库事务**（简称：**事务**）是数据库管理系统执行过程中的一个逻辑单位，由一个有限的数据库操作序列构成。

**数据库事务**拥有以下四个特性，习惯上被称之为**ACID**特性。

原子性（Atomicity）：事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行。

一致性（Consistency）：事务应确保数据库的状态从一个一致状态转变为另一个一致状态。一致状态的含义是数据库中的数据应满足完整性约束。

隔离性（Isolation）：多个事务并发执行时，一个事务的执行不应影响其他事务的执行。

持久性（Durability）：已被提交的事务对数据库的修改应该永久保存在数据库中。

## MySql 事务

```

  //开启事务
  start transaction／begin;
  // update/delete/insert
  update table set name = 'name' where id = 1;
  // 提交／回滚
  commit/rollback;

```

## JDBC 事务

```

	//URL指向要访问的数据库名s
	String url = "jdbc:mysql://127.0.0.1:3306/test";
	//MySQL配置时的用户名
	String user = "root";
	//MySQL配置时的密码
	String password = "root";
	// 加载驱动
    Class.forName("com.mysql.jdbc.Driver");
	//获取连接
	Connection con = DriverManager.getConnection(url, user, password);
	//开启事务
	con.setAutoCommit(false);
	//执行CRUD
	//提交事务
	con.commit();
	// 异常 回滚事务
	con.rollback();
	//finally 关闭连接
	con.close();

```
## Spring 事务

### Spring 编程式事务

**编程式事务**指的是通过编码方式实现事务，即类似于**JDBC**编程实现事务管理。管理使用`TransactionTemplate`或者直接使用底层的`PlatformTransactionManager`。对于编程式事务管理，**Spring**推荐使用`TransactionTemplate`。

```

    public String modify(final Param param) {
        return transactionTemplate.execute(new TransactionCallback<String>() {
            @Override
            public String doInTransaction(TransactionStatus transactionStatus) {
                try {
                  // update
                  // update
                } catch (Exception e) {
                    logger.error("modify failed", e.getCause());
                    transactionStatus.setRollbackOnly();
                    return "修改失败,请稍后再试";
                }
                return null;
            }
        });
    }

```

 context配置

```

    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"  destroy-method="close">
	    <property name="driverClassName" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
        <property name="initialSize" value="2"/>
	    <property name="maxActive" value="20"/>
	    <property name="maxWait" value="60000"/>
	    <property name="poolPreparedStatements" value="true"/>
	    <property name="validationQuery" value="/* ping */SELECT 1"></property>
	</bean>
    <!-- 事务处理 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
        <property name="transactionManager" ref="transactionManager"/>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"/>

```

###  Spring 声明式事务

**生明式事务**是通过配置的方式实现事务。**声明式事务**建立在**AOP**之上的，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明(或通过基于**@Transactional**注解的方式)，便可以将事务规则应用到业务逻辑中。

显然**声明式事务**管理要优于编程式事务管理，这正是**Spring**倡导的非侵入式的开发方式。

声明式事务管理使业务代码不受污染，一个普通的POJO对象，只要加上注解就可以获得完全的事务支持。和编程式事务相比，声明式事务唯一不足地方是，后者的最细粒度只能作用到方法级别，无法做到像编程式事务那样可以作用到代码块级别。但是即便有这样的需求，也存在很多变通的方法，比如，可以将需要进行事务管理的代码块独立为方法等等。

 context配置

```

<tx:advice id="txadvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="add*" propagation="REQUIRED" rollback-for="Exception" />
        <tx:method name="modify*" propagation="REQUIRED" rollback-for="Exception" />
        <tx:method name="del*" propagation="REQUIRED" rollback-for="Exception"/>
        <tx:method name="*" propagation="REQUIRED" read-only="true"/>
    </tx:attributes>
</tx:advice>

<aop:config>
    <aop:pointcut id="businessService" expression="execution(* com.sevice.*.*(..))"/>
    <aop:advisor pointcut-ref="businessService" advice-ref="txadvice"/>
</aop:config>

```
其中第一个*代表返回值，第二*代表service下子包，第三个*代表方法名，“（..）”代表方法参数。
