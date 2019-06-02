
# 在maven在使用Ant功能

### 背景
当前企业应用中，Java项目构建工具以Apache Maven为主，Maven的约定大于配置功能对应大多数应用场景非常方便。如果以申明式和命令时编程语言为比方，Maven类是声明式编程语言，只要申明干什么，maven就能按照预定配置完成任务。在maven之前,历史上存在Apache Ant这样的构建工具，而Ant则完全是命令式，构建过程中每个任务都需要显示调用,Ant提供的Task，或自定义task。

最近在改造一个历史遗留项目,涉及到构建过程中从依赖的jar中拷贝静态资源以及从代码目录中复制文件操作操作等，通过在Maven中利用相应```maven-antrun-plugin```插件简单完成工作

### maven-antrun-plugin插件介绍
插件坐标信息

```
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-antrun-plugin</artifactId>
<version>1.8</version>
```
按照该插件的[官方介绍信息](https://ant.apache.org/manual/index.html)
该插件提供从Maven内运行Ant任务的功能。您甚至可以将您的Ant脚本嵌入POM！

这个插件不是提供污染POM的手段意图，因此它鼓励所有Ant任务移动到build.xml文件并使用Ant的POM的<ant/> task调用它（或者说所有在build.xml文件的Ant任务移动到POM中，并使用<ant/> task调用它）。

这个插件的主要目的之一是方便从Ant基础项目迁移到Maven。某些项目可能无法迁移，因为它们依赖于默认情况下Maven不提供的自定义构建功能。

### 使用例子

在plugin里直接使用Ant target

```
 <build>
        <plugins>
            <plugin>
               <artifactId>maven-antrun-plugin</artifactId>
                <version>1.8</version>
                <executions>
                    <execution>
                        <id>copy-deal-static</id>
                        <phase>prepare-package</phase>
                        <configuration>
                            <!-- 这里定义Ant任务-->
                            <target name="copy-deal-static" description="copy trd-web-deal webapp static resources">
                                <copy todir="target/static-resource/deal">
                                    <fileset dir="../../trd-web-deal/src/main/webapp/" excludes="WEB-INF/"/>
                                </copy>
                            </target>

                        </configuration>
                        <goals>
                            <goal>run</goal>
                        </goals>
                    </execution>                        
                </executions>
            </plugin>
        </plugins>
</build>                                 
```
 上面例子中使用了Apache ant ```copy``` [Task](https://ant.apache.org/manual/Tasks/copy.html) 其它Ant任务可以参考[Ant手册](https://ant.apache.org/manual/index.html)
  
 调用build.xml定义的target
 
 ```
<build>
	<plugins>
		<plugin>
			<artifactId>maven-antrun-plugin</artifactId>
			<version>1.8</version>
			<executions>
				<execution>
					<id>call-deploy</id>
					<phase>prepare-package</phase>
					<configuration>
						<target name="call-deploy"
                                    description="call-deploy">
							<ant antfile="${basedir}/build.xml">
								<target name="deploy"/>
							</ant>
						</target>
					</configuration>
					<goals>
						<goal>run</goal>
					</goals>
				</execution>
				<executions>
				</plugin>
			</plugins>
		</build>                
 
 ```
 
 
