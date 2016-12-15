title: 代码生成器
---
在代码生成之前，首先进行配置，MP提供了大量的自定义设置，生成的代码完全能够满足各类型的需求，如果你发现配置不能满足你的需求，欢迎提交issue和pull-request，有兴趣的也可以查看[源码](https://github.com/baomidou/mybatis-plus/tree/master/mybatis-plus/src/main/java/com/baomidou/mybatisplus/generator)进行了解。

# 参数说明

参数相关的配置，详见[config包](https://github.com/baomidou/mybatis-plus/tree/master/mybatis-plus/src/main/java/com/baomidou/mybatisplus/generator/config)类

```java saveDir
类型：String
描述：生成代码存放目录
```

```java entityPackage
类型：String
描述：Entity类路径
```

```java mapperPackage
类型：String
描述：Mapper类路径
```

```java xmlPackage
类型：String
描述：Mapper对应的XML文件存放路径（默认为Mapper目录下的xml文件夹）
```

```java servicePackage
类型：String
描述：Service类路径
```

```java serviceImplPackage
类型：String
描述：ServiceImpl类路径（默认为Service目录下的impl文件夹）
```

```java superService
类型：String
描述：Service 父类包路径（通过该项，你可以自定义Service层的父类，实现自定义通用方法。若无需求，不用配置）
```

```java superServiceImpl
类型：String
描述：ServiceImpl 父类包路径（同上）
```

```java mapperName
类型：String
描述：自定义Mapper名称（采用"%s"做占位符，默认为"%sMapper"，生成后为FooMapper.java）
```

```java mapperXMLName
类型：String
描述：自定义XML名称（采用"%s"做占位符，默认为"%sMapper"，生成后为FooMapper.xml）
```

```java serviceName
类型：String
描述：自定义 Service 名称（采用"%s"做占位符，默认为"I%sService"，生成后为IFooService.java）
```

```java serviceImplName
类型：String
描述：自定义 ServiceImpl 名称（采用"%s"做占位符，默认为"%sServiceImpl"，生成后为FooServiceImpl.java）
```

```java tableNames
类型：String[]
描述：要生成代码的表名称（若为空就直接指定所有表，格式为逗号分割）
```

```java fileOverride
类型：boolean
描述：是否覆盖当前已有文件（true为覆盖，false为不覆盖）
```

```java dbPrefix
类型：boolean
描述：设置是否取消数据库前缀（例如`mp_user`生成实体类，false 为 MpUser.java , true 为 User.java）
```

```java dbColumnUnderline
类型：boolean
描述：设置数据库是否为下划线
```

```java dbDriverName
类型：String
描述：数据库驱动名（如com.mysql.jdbc.Driver）
```

```java dbUser
类型：String
描述：数据库username
```

```java dbPassword
类型：String
描述：数据库密码
```

```java dbUrl
类型：String
描述：数据库URL
```

```java idType
类型：boolean
描述：ID主键策略（表主键 ID 生成类型, 自增该设置无效。详见主键策略选择）
```

# 主键策略选择

MP支持以下4中主键策略，可根据需求自行选用：

值                | 描述
---------------- | ---------------------
IdType.AUTO      | 数据库ID自增
IdType.INPUT     | 用户输入ID
IdType.ID_WORKER | 全局唯一ID，内容为空自动填充（默认配置）
IdType.UUID      | 全局唯一ID，内容为空自动填充

AUTO、INPUT和UUID大家都应该能够明白，这里主要讲一下ID_WORKER。首先得感谢开源项目`Sequence`，感谢作者`李景枫`。

什么是Sequence？简单来说就是一个分布式高效有序ID生产黑科技工具，思路主要是来源于`Twitter-Snowflake算法`。这里不详细讲解Sequence，有兴趣的朋友请[点此去了解Sequence](http://git.oschina.net/yu120/sequence)。

MP在Sequence的基础上进行部分优化，用于产生全局唯一ID，好的东西希望推广给大家，所以我们将ID_WORDER设置为默认配置。

# 表及字段命名策略选择

在MP中，我们建议`数据库表名`采用`下划线命名方式`，而`表字段名`采用`驼峰命名方式`。

这么做的原因是为了避免在对应实体类时产生的性能损耗，这样字段不用做映射就能直接和实体类对应。当然如果项目里不用考虑这点性能损耗，那么你采用下滑线也是没问题的，只需要在生成代码时配置`dbColumnUnderline`属性就可以。

# 如何生成代码

## 方式一、代码生成
```依赖jars
<!-- 模板引擎 -->
<dependency>
	<groupId>org.apache.velocity</groupId>
	<artifactId>velocity</artifactId>
	<version>1.7</version>
</dependency>

<!-- MP 核心库 -->
<dependency>
	<groupId>com.baomidou</groupId>
	<artifactId>mybatis-plus</artifactId>
	<version>2.0</version>
</dependency>
```

```java 生成示例
import java.util.HashMap;
import java.util.Map;

import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.DbType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

/**
 * <p>
 * 代码生成器演示
 * </p>
 */
public class MpGenerator {

	/**
	 * <p>
	 * MySQL 生成演示
	 * </p>
	 */
	public static void main(String[] args) {
		AutoGenerator mpg = new AutoGenerator();

		// 全局配置
		GlobalConfig gc = new GlobalConfig();
		gc.setOutputDir("D://");
		gc.setFileOverride(true);
		gc.setActiveRecord(true);
		gc.setEnableCache(false);// XML 二级缓存
		gc.setBaseResultMap(true);// XML ResultMap
		gc.setBaseColumnList(false);// XML columList
		gc.setAuthor("Yanghu");

		// 自定义文件命名，注意 %s 会自动填充表实体属性！
		// gc.setMapperName("%sDao");
		// gc.setXmlName("%sDao");
		// gc.setServiceName("MP%sService");
		// gc.setServiceImplName("%sServiceDiy");
		// gc.setControllerName("%sAction");
		mpg.setGlobalConfig(gc);

		// 数据源配置
		DataSourceConfig dsc = new DataSourceConfig();
		dsc.setDbType(DbType.MYSQL);
		dsc.setDriverName("com.mysql.jdbc.Driver");
		dsc.setUsername("root");
		dsc.setPassword("521");
		dsc.setUrl("jdbc:mysql://127.0.0.1:3306/mybatis-plus?characterEncoding=utf8");
		mpg.setDataSource(dsc);

		// 策略配置
		StrategyConfig strategy = new StrategyConfig();
		strategy.setTablePrefix("bmd_");// 此处可以修改为您的表前缀
		strategy.setNaming(NamingStrategy.underline_to_camel);// 表名生成策略
		// strategy.setInclude(new String[] { "user" }); // 需要生成的表
		// strategy.setExclude(new String[]{"test"}); // 排除生成的表
		// 字段名生成策略
		strategy.setFieldNaming(NamingStrategy.underline_to_camel);
		// 自定义实体父类
		// strategy.setSuperEntityClass("com.baomidou.demo.TestEntity");
		// 自定义实体，公共字段
		// strategy.setSuperEntityColumns(new String[] { "test_id", "age" });
		// 自定义 mapper 父类
		// strategy.setSuperMapperClass("com.baomidou.demo.TestMapper");
		// 自定义 service 父类
		// strategy.setSuperServiceClass("com.baomidou.demo.TestService");
		// 自定义 service 实现类父类
		// strategy.setSuperServiceImplClass("com.baomidou.demo.TestServiceImpl");
		// 自定义 controller 父类
		// strategy.setSuperControllerClass("com.baomidou.demo.TestController");
		// 【实体】是否生成字段常量（默认 false）
		// public static final String ID = "test_id";
		// strategy.setEntityColumnConstant(true);
		// 【实体】是否为构建者模型（默认 false）
		// public User setName(String name) {this.name = name; return this;}
		// strategy.setEntityBuliderModel(true);
		mpg.setStrategy(strategy);

		// 包配置
		PackageConfig pc = new PackageConfig();
		pc.setParent("com.baomidou");
		pc.setModuleName("test");
		mpg.setPackageInfo(pc);

		// 注入自定义配置，可以在 VM 中使用 cfg.abc 设置的值
		InjectionConfig cfg = new InjectionConfig() {
			@Override
			public void initMap() {
				Map<String, Object> map = new HashMap<String, Object>();
				map.put("abc", this.getConfig().getGlobalConfig().getAuthor() + "-mp");
				this.setMap(map);
			}
		};
		mpg.setCfg(cfg);

		// 自定义模板配置
		// TemplateConfig tc = new TemplateConfig();
		// tc.setController("...");
		// tc.setEntity("...");
		// tc.setMapper("...");
		// tc.setXml("...");
		// tc.setService("...");
		// tc.setServiceImpl("...");
		// mpg.setTemplate(tc);

		// 执行生成
		mpg.execute();

		// 打印注入设置
		System.err.println(mpg.getCfg().getMap().get("abc"));
	}

}
```

## 方式二、Maven插件生成

待补充（Maven代码生成插件 待完善）
http://git.oschina.net/baomidou/mybatisplus-maven-plugin

