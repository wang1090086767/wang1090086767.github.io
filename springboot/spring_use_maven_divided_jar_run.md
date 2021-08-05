# SpringBoot 使用 Maven 打包 分离所依赖的 jar 包到 lib 目录下，并以外部依赖的方式启动 jar 包

## 1. 配置 pom 文件

~~~xml
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<executable>true</executable>
<!--					<mainClass>cn.it.ProducerApplication</mainClass>-->
					<layout>ZIP</layout>
					<includes>
						<include>
							<groupId>nothing</groupId>
							<artifactId>nothing</artifactId>
						</include>
					</includes>
				</configuration>
				<executions>
					<execution>
						<goals>
							<goal>repackage</goal>
						</goals>
					</execution>
				</executions>

			</plugin>

			<!-- 指定启动类，将依赖打成外部jar包 -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
				<configuration>
					<archive>
						<!-- 生成的jar中，不要包含pom.xml和pom.properties这两个文件 -->
						<addMavenDescriptor>false</addMavenDescriptor>
						<manifest>
							<!-- 是否要把第三方jar加入到类构建路径 -->
							<addClasspath>true</addClasspath>
							<!-- 外部依赖jar包的最终位置 -->
							<classpathPrefix>lib/</classpathPrefix>
							<!-- 项目启动类 -->
<!--							<mainClass>com.mozi.mq_monitor.MqMonitorApplication</mainClass>-->
						</manifest>
					</archive>
				</configuration>
			</plugin>
			<!--拷贝依赖到jar外面的lib目录-->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>copy-lib</id>
						<phase>package</phase>
						<goals>
							<goal>copy-dependencies</goal>
						</goals>
						<configuration>
							<outputDirectory>target/lib</outputDirectory>
							<excludeTransitive>false</excludeTransitive>
							<stripVersion>false</stripVersion>
							<includeScope>compile</includeScope>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
~~~

## 2. 使用命令启动时添加参数

~~~ shell
 java -Dloader.path=./lib -jar ./producer-1.0-SNAPSHOT.jar
~~~

## 3. 若以外部配置启动 springboot 可使用以下命令

~~~ shell
使用如下参数指定Springboot 配置文件
java  -Dspring.config.location=./application.yml -jar  demo-0.0.1-SNAPSHOT.jar
~~~

