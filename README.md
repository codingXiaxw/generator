## 用IDEA开发MyBatis逆向工程

以往我们在开发时都需要通过数据库中的表然后自己在po包下建立相对应的pojo类，并要创建相应的mapper.xml写出对表的所有操作，而使用mybatis逆向工程就不用我们自己再编写pojo类与相应的mapper.java和mapper.xml文件，它可以自动对单表生成sql，包括:mapper.xml、mapper.java、表名.java(po类)。是不是很方便？接下来我将为你们介绍如何使用mybatis的逆向工程，只需三步便可以简单做到。  


<!--more-->   

[更多资料请点击这里进入我的博客](http://codingxiaxw.cn/2016/11/13/41-mybatis9%E9%80%86%E5%90%91%E5%B7%A5%E7%A8%8B/)

首先我们需要在官网下载:[逆向工程开发文档](http://www.mybatis.org/generator/)以及jar包:mybatis-generator-core-bundle。用IDEA的好处就是可以使用Maven依赖，但是此篇文章中我们就新建一个普通工程。  

## 1.逆向工程使用配置
### 1.1jar包的导入
这里我们需要导入四个包，1.mybatis3.xjar包。2.逆向工程核心包。3.数据库连接包。4.log4j.jar，用于输出日志。目录如下:  

<img src="http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-15%20%E4%B8%8B%E5%8D%885.46.07.png" width="50%" height="50%"/>  


### 1.2配置逆向工程的配置文件
在src包下创建逆向工程配置文件generatorConfig.xml,内容如下，直接拷贝官网介绍的内容即可:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<context id="testTables" targetRuntime="MyBatis3">
		<commentGenerator>
			<!-- 是否去除自动生成的注释 true：是 ： false:否 -->
			<property name="suppressAllComments" value="true" />
		</commentGenerator>
		<!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
		<jdbcConnection driverClass="com.mysql.jdbc.Driver"
			connectionURL="jdbc:mysql://localhost:3306/mybatis" userId="root"
			password="xiaxunwu1996.">
		</jdbcConnection>
		<!-- <jdbcConnection driverClass="oracle.jdbc.OracleDriver"
			connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:yycg"
			userId="yycg"
			password="yycg">
		</jdbcConnection> -->

		<!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
			NUMERIC 类型解析为java.math.BigDecimal -->
		<javaTypeResolver>
			<property name="forceBigDecimals" value="false" />
		</javaTypeResolver>

		<!-- targetProject:生成PO类的位置 -->
		<javaModelGenerator targetPackage="po"
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
			<!-- 从数据库返回的值被清理前后的空格 -->
			<property name="trimStrings" value="true" />
		</javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
		<sqlMapGenerator targetPackage="mapper"
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</sqlMapGenerator>
		<!-- targetPackage：mapper接口生成的位置 -->
		<javaClientGenerator type="XMLMAPPER"
			targetPackage="mapper"
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</javaClientGenerator>
		<!-- 指定数据库表 -->
		<table tableName="items"></table>
		<table tableName="orders"></table>
		<table tableName="orderdetail"></table>
		<!-- <table schema="" tableName="sys_user"></table>
		<table schema="" tableName="sys_role"></table>
		<table schema="" tableName="sys_permission"></table>
		<table schema="" tableName="sys_user_role"></table>
		<table schema="" tableName="sys_role_permission"></table> -->

		<!-- 有些表的字段需要指定java类型
		 <table schema="" tableName="">
			<columnOverride column="" javaType="" />
		</table> -->
	</context>
</generatorConfiguration>
```

需要修改的地方:  

- javaModelGenerator,生成PO类的位置
- sqlMapGenerator,mapper映射文件生成的位置
- javaClientGenerator,mapper接口生成的位置
- table,其tableName属性对应数据库中相应表

### 1.3执行生成代码
在src包下新建一个Generator.java文件，内容如下，也是拷贝的官网中介绍的代码:
```java
import java.io.File;
import java.util.ArrayList;
import java.util.List;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

public class GeneratorSqlmap {

    public void generator() throws Exception{

        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        //指定 逆向工程配置文件
        File configFile = new File("src/generatorConfig.xml");
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                callback, warnings);
        myBatisGenerator.generate(null);

    }
    public static void main(String[] args) throws Exception {
        try {
            GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
            generatorSqlmap.generator();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

}
```
注意，这里new File中传入的参数只能是`src/generatorConfig.xml`而不能为`generatorConfig.xml`，否则会出现`java.io.FileNotFoundException: generatorConfig.xml (No such file or directory)`的报错信息，运行程序，在打印台看到输出日志信息为:![](http://od2xrf8gr.bkt.clouddn.com/111.png)  

然后再点击文件目录上的刷新图标刷新文件目录，文件目录下出现我们通过单表映射出来的po类包以及mapper包下的mapper.xml和mapper.java，刚开始的工程目录如下:  


<img src="http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-15%20%E4%B8%8B%E5%8D%885.55.50.png" width="50%" height="20%" />   
运行程序后最后的工程目录结构如下:  

<img src="http://od2xrf8gr.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-15%20%E4%B8%8B%E5%8D%885.53.21.png" width="50%" height="20%" />  

这样我们便通过mybatis的逆向工程完成了通过单表直接创建出对应的mapper.java和mapper.xml的工作。

## 2.逆向工程的应用
逆向工程往往是单独的建立一个普通工程如A，通过运行逆向工程生成相应的mapper和po后然后再将这两个包拷贝到我们使用到ssm框架创建的web项目，而不是直接在web项目中使用逆向工程。  

通过运行上述的程序，我们便通过数据库中的表快速的生成了相应的po类和mapper，而不用我们程序员自己再编写相应的po类和mapper，为我们带来了很大的方便，所以这个一定要学会，在后续开发中只要使用到mybatis的地方我们都会通过mybatis的逆向工程自动为我们生成mapper和po类。


## 3.联系

  If you have some questions after you see this article,you can tell your doubts in the comments area or you can find some info by  clicking these links.


- [Blog@codingXiaxw's blog](http://codingxiaxw.cn)

- [Weibo@codingXiaxw](http://weibo.com/u/5023661572?from=hissimilar_home&refer_flag=1005050003_)

- [Zhihu@codingXiaxw](http://www.zhihu.com/people/e9f78fa34b8002652811ac348da3f671)  
- [Github@codingXiaxw](https://github.com/codingXiaxw)


## 如何使用
1. git clone https://github.com/codingXiaxw/generator.git
2. 用IDEA open 即可。