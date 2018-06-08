---
layout: post
title:  "MyBatis typeHandlers"
date:   2016-06-06 12:29:00 +0800
categories: Java
tag: ['MyBatis', 'Java']
---
	
* content
{:toc}	

---

当 `MyBatis` 将一个 `Java` 对象作为输入参数执行 `INSERT` 语句操作时，它会创建一个 `PreparedStatement` 对象，并且 使用 `setXXX()`方式对占位符设置相应的参数值,当从 SQL 结果集构 建 `JavaBean` 时，也有类似的过程。

`MyBatis` 对于以下的类型使用内建的类型处理器:所有的基本数据类型、基本类型的包装类型、`byte[]`、 `java.util.Date`、`java.sql.Date`、`java,sql.Time`、`java.sql.Timestamp`、`Enum`类型等。对于自定义类型，持久化和查询则可创建自定义`typeHandlers`类型处理器。


## `typeHandlers`原理
对于如下mapper

```
<insert id="insertStudent" parameterType="Student">
    INSERT INTO STUDENTS(STUD_ID,NAME,EMAIL,BIRTH)
    VALUES(#{studId},#{name},#{email},#{birth})
</insert>
```
执行预处理

```
PreparedStatement pstmt = connection.prepareStatement
                    ("INSERT INTO STUDENTS(STUD_ID,NAME,EMAIL,birth) VALUES(?,?,?,?)")
```

通过使用类型处理器`typeHandlers`设置参数，对于`java.util.Date` 类型转会换为 `java.sql.Timestamp` 设值（见：`DateTypeHandler`）。

```
pstmt.setInt(1,student.getStudId());
pstmt.setString(2, student.getName());
pstmt.setString(3, student.getEmail());
pstmt.setTimestamp(4, new Timestamp(student.getBirth().getTime()));
```
## 自定义`typeHandlers`
对于如下自定义枚举类型则需要新建`typeHandlers`

```
public enum ThirdPartyLogType {
	ITEM(0, "商品"), TRADE(1, "订单"), SUPPLIER(2, "供应商");

	private int type;
	private String name;

	ThirdPartyLogType(int type, String name) {
		this.type = type;
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getType() {
		return type;
	}

	public void setType(int type) {
		this.type = type;
	}

	public static ThirdPartyLogType valuesOf(int type) {
		for (ThirdPartyLogType thirdPartyLogType : ThirdPartyLogType.values()) {
			if (thirdPartyLogType.getType() == type) {
				return thirdPartyLogType;
			}
		}
		return null;
	}
}
```

`ThirdPartyLogTypeTypeHandler`如下：

```
@MappedTypes(ThirdPartyLogType.class)
public class ThirdPartyLogTypeTypeHandler extends EnumTypeHandler<ThirdPartyLogType> {
    public ThirdPartyLogTypeTypeHandler(Class<ThirdPartyLogType> type) {
        super(type);
    }

    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, ThirdPartyLogType parameter, JdbcType jdbcType) throws SQLException {
        if (jdbcType == null) {
            ps.setInt(i, parameter.getType());
        } else {
            ps.setObject(i, parameter.name(), jdbcType.TYPE_CODE);
        }
    }

    @Override
    public ThirdPartyLogType getNullableResult(ResultSet rs, String columnName) throws SQLException {
        int s = rs.getInt(columnName);
        return ThirdPartyLogType.valuesOf(s);
    }

    @Override
    public ThirdPartyLogType getNullableResult(ResultSet rs, int columnIndex) throws SQLException{
        int s = rs.getInt(columnIndex);
        return ThirdPartyLogType.valuesOf(s);
    }

    @Override
    public ThirdPartyLogType getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        int s = cs.getInt(columnIndex);
        return ThirdPartyLogType.valuesOf(s);
    }
}   
```
## 注册`typeHandlers`

对于自定义`typeHandlers`需要注册，可以使用`SqlSessionFactory`中的`typeHandlersPackage`或`typeHandlers`进行注册

    <bean id="sqlSessionFactory" class="org.mybatis.HgjSqlSessionFactoryBean">
        <property name="mapperLocations" value="classpath*:org/dinozhang/**/dao/xml/**/*.xml"></property>
        <property name="dataSource" ref="dataSource" />
        <property name="typeHandlersPackage" value="org.dinozhang.typehandler"/>
    </bean>
