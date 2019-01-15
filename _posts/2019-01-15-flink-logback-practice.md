---
layout: post
title:  "Flink Logback实践"
date:   2019-01-15 12:29:00 +0800
categories: Flink
tag: LogBack
---

* content
{:toc}

---
Flink默认依赖于Log4j，Logback较Log4j性能有不错提升，因此在项目和集群中统一使用Logback。<br /><br />
### job 配置

在每个FLink job中去除Log4j依赖

```xml
<dependencies>
	<!-- Add the two required logback dependencies -->
	<dependency>
		<groupId>ch.qos.logback</groupId>
		<artifactId>logback-core</artifactId>
		<version>1.1.3</version>
	</dependency>
	<dependency>
		<groupId>ch.qos.logback</groupId>
		<artifactId>logback-classic</artifactId>
		<version>1.1.3</version>
	</dependency>

	<!-- Add the log4j -> sfl4j (-> logback) bridge into the classpath
	 Hadoop is logging to log4j! -->
	<dependency>
		<groupId>org.slf4j</groupId>
		<artifactId>log4j-over-slf4j</artifactId>
		<version>1.7.7</version>
	</dependency>

	<dependency>
		<groupId>org.apache.flink</groupId>
		<artifactId>flink-java</artifactId>
		<version>1.7.0</version>
		<exclusions>
			<exclusion>
				<groupId>log4j</groupId>
				<artifactId>*</artifactId>
			</exclusion>
			<exclusion>
				<groupId>org.slf4j</groupId>
				<artifactId>slf4j-log4j12</artifactId>
			</exclusion>
		</exclusions>
	</dependency>
	<dependency>
		<groupId>org.apache.flink</groupId>
		<artifactId>flink-streaming-java_2.11</artifactId>
		<version>1.7.0</version>
		<exclusions>
			<exclusion>
				<groupId>log4j</groupId>
				<artifactId>*</artifactId>
			</exclusion>
			<exclusion>
				<groupId>org.slf4j</groupId>
				<artifactId>slf4j-log4j12</artifactId>
			</exclusion>
		</exclusions>
	</dependency>
	<dependency>
		<groupId>org.apache.flink</groupId>
		<artifactId>flink-clients_2.11</artifactId>
		<version>1.7.0</version>
		<exclusions>
			<exclusion>
				<groupId>log4j</groupId>
				<artifactId>*</artifactId>
			</exclusion>
			<exclusion>
				<groupId>org.slf4j</groupId>
				<artifactId>slf4j-log4j12</artifactId>
			</exclusion>
		</exclusions>
	</dependency>
</dependencies>
```


### cluster 配置

要使用Logback而不是使用Flink的Log4j，您需要从lib目录中删除log4j-1.2.xx.jar和sfl4j-log4j12-xxx.jar。<br />同时需要添加
* logback-classic.jar
* logback-core.jar
* log4j-over-slf4j.jar

```
wget http://central.maven.org/maven2/ch/qos/logback/logback-classic/1.1.3/logback-classic-1.1.3.jar
wget http://central.maven.org/maven2/ch/qos/logback/logback-core/1.1.3/logback-core-1.1.3.jar
wget http://central.maven.org/maven2/org/slf4j/log4j-over-slf4j/1.7.7/log4j-over-slf4j-1.7.7.jar
```


logback.xml

```xml
<!--
  ~ Licensed to the Apache Software Foundation (ASF) under one
  ~ or more contributor license agreements.  See the NOTICE file
  ~ distributed with this work for additional information
  ~ regarding copyright ownership.  The ASF licenses this file
  ~ to you under the Apache License, Version 2.0 (the
  ~ "License"); you may not use this file except in compliance
  ~ with the License.  You may obtain a copy of the License at
  ~
  ~     http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing, software
  ~ distributed under the License is distributed on an "AS IS" BASIS,
  ~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  ~ See the License for the specific language governing permissions and
  ~ limitations under the License.
  -->

<configuration>
    <!-- INFO级别日志 appender -->
    <!--<property name="LOG_DIR" value="log"/>-->

    <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--&lt;!&ndash; 过滤器，只记录INFO级别的日志 &ndash;&gt;-->
        <!--<filter class="ch.qos.logback.classic.filter.LevelFilter">-->
            <!--<level>INFO</level>-->
            <!--<onMatch>ACCEPT</onMatch>-->
            <!--<onMismatch>DENY</onMismatch>-->
        <!--</filter>-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 按天回滚 daily -->
            <fileNamePattern>%d{yyyy-MM-dd}/flink-info-log.log
            </fileNamePattern>
            <!-- 日志最大的历史 60天 -->
            <maxHistory>10</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread][%C-%L] %-5level %logger - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- This affects logging for both user code and Flink -->
    <root level="INFO">
        <appender-ref ref="file"/>
    </root>

    <!-- Uncomment this if you want to only change Flink's logging -->
    <!--<logger name="org.apache.flink" level="INFO">-->
        <!--<appender-ref ref="file"/>-->
    <!--</logger>-->

    <!-- The following lines keep the log level of common libraries/connectors on
         log level INFO. The root logger does not override this. You have to manually
         change the log levels here. -->
    <logger name="akka" level="INFO">
        <appender-ref ref="file"/>
    </logger>
    <logger name="org.apache.kafka" level="INFO">
        <appender-ref ref="file"/>
    </logger>
    <logger name="org.apache.hadoop" level="INFO">
        <appender-ref ref="file"/>
    </logger>
    <logger name="org.apache.zookeeper" level="INFO">
        <appender-ref ref="file"/>
    </logger>

    <!-- Suppress the irrelevant (wrong) warnings from the Netty channel handler -->
    <logger name="org.apache.flink.shaded.akka.org.jboss.netty.channel.DefaultChannelPipeline" level="ERROR">
        <appender-ref ref="file"/>
    </logger>
</configuration>

```
