---
title: Maven Profile
date: 2017-11-15
tags: maven
---

Maven的Profile用于在不同的环境下应用不同的配置，这里的“环境”可以是操作系统，jdk，配置文件等等

如果说你有两个配置文件夹在src目录下：

> env_dev   开发环境

> env_prod  线上环境

分别对应不同的环境，在不同的环境下使用不同的配置文件，你会怎么做？

我们可以在pom文件中加入以下代码：

```
<build>
    <resources>
        <resource>
            <directory>src/${env}</directory>
        </resource>
    </resources>
</build>
```

```
<profiles>
    <profile>
        <id>env_dev</id>
        <properties>
            <env>env_dev</env>
        </properties>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
    </profile>
    <profile>
        <id>env_prod</id>
        <properties>
            <env>env_prod</env>
        </properties>
    </profile>
</profiles>
```

或者

```
  <profiles>
    <profile>
      <id>env_dev</id>
      <build>
        <resources>
          <resource>
            <directory>src/env_dev</directory>
          </resource>
        </resources>
      </build>
    </profile>
    <profile>
      <id>env_prod</id>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
      <build>
        <resources>
          <resource>
            <directory>src/env_prod</directory>
          </resource>
        </resources>
      </build>
    </profile>
  </profiles>
```
maven依据配置打包:
```
mvn clean package -Dmaven.test.skip=true -P env_prod
```
-Dmaven.test.skip=true : 跳过测试
-P env_prod是配置为线上环境，这里的env_prod为profile中设置的id，通过改变
-P后的id值，来实现对不同配置文件夹的使用

打包完成后，可打开生成的jar或war包，查看其中的配置文件内容，即可验证maven profile的功能

补充
---
如何使用maven打包生成jar包或者war包：

```
 <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>1.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                  <mainClass>com.javen</mainClass>
                </transformer>
              </transformers>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

生成的jar包含所有依赖，所以可以直接运行，其中mainClass为程序的入口

这里要补充一句：要想jar通过java -jar xxx.jar 运行，需要满足：
1.在jar包中的META-INF、MANIFEST.MF中指定Main-Class,这样才能确定程序的入口在哪里
2.要能加载到依赖包

注意：如果项目中用到了Spring FrameWork,将依赖打到一个jar包中，运行时会出现读取XML schema文件出错。

原因是SF的多个jar包中包含相同的文件spring.handlers和spring.schemas,如果生成一个jar包会相互覆盖。
为了避免互相影响，可以使用AppendingTransformer来对文件内容追加合并：
```
<configuration>  
    <transformers>  
        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">  
            <mainClass>com.xxg.Main</mainClass>  
        </transformer>  
        <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">  
            <resource>META-INF/spring.handlers</resource>  
        </transformer>  
        <transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">  
            <resource>META-INF/spring.schemas</resource>  
        </transformer>  
    </transformers>  
</configuration>  
```