# JDK configuration

The project is built based on JDK11, so to run the project, you need a JDK11 compatible JDK. Considering that Oracle commercial JDK charges enterprises, we recommend that you use Open JDK 11, which can be downloaded [here](https://jdk.java.net/archive/).

![](https://oscimg.oschina.net/oscnet/up-8df92de1dbe0df325e4f41557aee4594510.JPEG)

# maven configuration
Edit the file ~/.m2/settings.xml, find the tag named servers, and add the following content to it:
```xml
        <server>
            <id>official-docker</id>
            <username>qwfys</username>
            <password>Gah6kuP7ohfio4</password>
            <configuration>
                <email>qwfys200@qq.com</email>
            </configuration>
        </server>
```
>~ Is a symbolic representation of the user directory, if we have a user with a login name of lwk, under normal circumstances, in the Linux / Uninx system, the root user directory is / root, and the non-root user directory is / home / lwk . In Windows 7 / 8.x / 10, the user directory is C: \ Users \ lwk.

# project struct
- common

Dockerfile is common to all projects and is located in the root directory of the project. The contents are as follows:
```
FROM openjdk:11-jdk
VOLUME /tmp
ARG JAR_FILE
ADD ${JAR_FILE} app.jar
RUN bash -c 'touch /app.jar'
EXPOSE 18000
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom","-jar", "/app.jar"]
```

Considering that we currently build and push the docker image for the project to the image warehouse, all through the plugin named dockerfile. We focus on listing the pom.xml files of each project to help you configure the details.

- user-business

pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.xtwj.mall</groupId>
        <artifactId>user-business</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <groupId>com.xtwj.mall</groupId>
    <artifactId>user-business-impl</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <!--<docker.image.organization>registry.cn-hangzhou.aliyuncs.com/qwfys</docker.image.organization>-->
        <docker.image.organization>lanzhou</docker.image.organization>
        <docker.image.repository>user-business</docker.image.repository>
        <!--<docker.tag.version>0.0.1.release</docker.tag.version>-->
        <docker.tag.version>latest</docker.tag.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.xtwj.mall</groupId>
            <artifactId>user-business-spec</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>com.xtwj.mall</groupId>
            <artifactId>user-repository-spec</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <finalName>${project.name}</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.4.13</version>
                <configuration>
                    <repository>${docker.image.organization}/${docker.image.repository}</repository>
                    <tag>${docker.tag.version}</tag>
                    <tag>${docker.tag.version}</tag>
                    <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                    </buildArgs>
                    <useMavenSettingsForAuth>true</useMavenSettingsForAuth>
                </configuration>
            </plugin>
        </plugins>

    </build>
    <repositories>
        <!--<repository>-->
        <!--    <id>aliyun-docker</id>-->
        <!--    <url>registry.cn-hangzhou.aliyuncs.com</url>-->
        <!--</repository>        -->
        <repository>
            <id>official-docker</id>
            <url>https://index.docker.io/v1/</url>
        </repository>
    </repositories>

</project>
```

- user-service
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.xtwj.mall</groupId>
        <artifactId>user-service</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <groupId>com.xtwj.mall</groupId>
    <artifactId>user-service-impl</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <!--<docker.image.organization>registry.cn-hangzhou.aliyuncs.com/qwfys</docker.image.organization>-->
        <docker.image.organization>lanzhou</docker.image.organization>
        <docker.image.repository>user-service</docker.image.repository>
        <!--<docker.tag.version>0.0.1.release</docker.tag.version>-->
        <docker.tag.version>latest</docker.tag.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.xtwj.mall</groupId>
            <artifactId>user-service-spec</artifactId>
        </dependency>
        <dependency>
            <groupId>com.xtwj.mall</groupId>
            <artifactId>user-business-spec</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <finalName>${project.name}</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.4.13</version>
                <configuration>
                    <repository>${docker.image.organization}/${docker.image.repository}</repository>
                    <tag>${docker.tag.version}</tag>
                    <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                    </buildArgs>
                    <useMavenSettingsForAuth>true</useMavenSettingsForAuth>
                </configuration>
            </plugin>
        </plugins>

    </build>
    <repositories>
        <!--<repository>-->
        <!--    <id>aliyun-docker</id>-->
        <!--    <url>registry.cn-hangzhou.aliyuncs.com</url>-->
        <!--</repository>        -->
        <repository>
            <id>official-docker</id>
            <url>https://index.docker.io/v1/</url>
        </repository>
    </repositories>
</project>
```

- user-api

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.xtwj.mall</groupId>
        <artifactId>dragon-api</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>

    <groupId>com.xtwj.mall</groupId>
    <artifactId>usr-api</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <description>user-oriented</description>

    <properties>
        <!--<docker.image.organization>registry.cn-hangzhou.aliyuncs.com/qwfys</docker.image.organization>-->
        <docker.image.organization>lanzhou</docker.image.organization>
        <docker.image.repository>user-api</docker.image.repository>
        <!--<docker.tag.version>0.0.1.release</docker.tag.version>-->
        <docker.tag.version>latest</docker.tag.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.xtwj.mall</groupId>
            <artifactId>user-service-spec</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>com.spring4all</groupId>
            <artifactId>swagger-spring-boot-starter</artifactId>
            <version>1.9.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <finalName>${project.name}</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.4.13</version>
                <configuration>
                    <repository>${docker.image.organization}/${docker.image.repository}</repository>
                    <tag>${docker.tag.version}</tag>
                    <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                    </buildArgs>
                    <useMavenSettingsForAuth>true</useMavenSettingsForAuth>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <repositories>
        <!--<repository>-->
        <!--    <id>aliyun-docker</id>-->
        <!--    <url>registry.cn-hangzhou.aliyuncs.com</url>-->
        <!--</repository>        -->
        <repository>
            <id>official-docker</id>
            <url>https://index.docker.io/v1/</url>
        </repository>
    </repositories>

</project>
```



# login to dokcer hub

```
lwk@qwfys:~$ docker login --username=qwfys
Password: 
WARNING! Your password will be stored unencrypted in /home/lwk/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
lwk@qwfys:~$ 
```

# create repository on docker hub

- step 1
Create an organization named lanzhou on docker hub.

- step 2
Create three repositories under the organization named lanzhou, named user-api, user-service, user-business.

![](https://oscimg.oschina.net/oscnet/up-252b10b0e823321ab21b3a9a1ad37cb85fe.png)

# build & push with dockerfile 
Use the maven plugin named dockerfile to build a docker image based on the current project and push it to the corresponding repositoriy on the docker hub.

- for project named user-business
```bash
lwk@qwfys:~/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl$ mvn clean install dockerfile:build dockerfile:push
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.google.inject.internal.cglib.core.$ReflectUtils$1 (file:/usr/share/maven/lib/guice.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
WARNING: Please consider reporting this to the maintainers of com.google.inject.internal.cglib.core.$ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< com.xtwj.mall:user-business-impl >------------------
[INFO] Building user-business-impl 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ user-business-impl ---
[INFO] Deleting /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:apply (default) @ user-business-impl ---
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:validate (default) @ user-business-impl ---
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ user-business-impl ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ user-business-impl ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ user-business-impl ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ user-business-impl ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.21.0:test (default-test) @ user-business-impl ---
[INFO] No tests to run.
[INFO] 
[INFO] --- maven-jar-plugin:3.1.0:jar (default-jar) @ user-business-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl.jar
[INFO] 
[INFO] --- maven-javadoc-plugin:3.1.0:jar (javadoc) @ user-business-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-javadoc.jar
[INFO] 
[INFO] --- maven-javadoc-plugin:3.1.0:jar (default) @ user-business-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-javadoc.jar
[INFO] 
[INFO] >>> maven-source-plugin:3.0.1:jar (attach-sources) > generate-sources @ user-business-impl >>>
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:apply (default) @ user-business-impl ---
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:validate (default) @ user-business-impl ---
[INFO] 
[INFO] <<< maven-source-plugin:3.0.1:jar (attach-sources) < generate-sources @ user-business-impl <<<
[INFO] 
[INFO] 
[INFO] --- maven-source-plugin:3.0.1:jar (attach-sources) @ user-business-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-sources.jar
[INFO] 
[INFO] --- maven-source-plugin:3.0.1:jar-no-fork (default) @ user-business-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-sources.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.2.1.RELEASE:repackage (default) @ user-business-impl ---
[INFO] Replacing main artifact with repackaged archive
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ user-business-impl ---
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-business-impl/0.0.1-SNAPSHOT/user-business-impl-0.0.1-SNAPSHOT.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/pom.xml to /home/lwk/.m2/repository/com/xtwj/mall/user-business-impl/0.0.1-SNAPSHOT/user-business-impl-0.0.1-SNAPSHOT.pom
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-javadoc.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-business-impl/0.0.1-SNAPSHOT/user-business-impl-0.0.1-SNAPSHOT-javadoc.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-javadoc.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-business-impl/0.0.1-SNAPSHOT/user-business-impl-0.0.1-SNAPSHOT-javadoc.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-sources.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-business-impl/0.0.1-SNAPSHOT/user-business-impl-0.0.1-SNAPSHOT-sources.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-sources.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-business-impl/0.0.1-SNAPSHOT/user-business-impl-0.0.1-SNAPSHOT-sources.jar
[INFO] 
[INFO] --- dockerfile-maven-plugin:1.4.13:build (default-cli) @ user-business-impl ---
[INFO] dockerfile: null
[INFO] contextDirectory: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl
[INFO] Building Docker context /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl
[INFO] Path(dockerfile): null
[INFO] Path(contextDirectory): /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl
[INFO] 
[INFO] Image will be built as lanzhou/user-business:latest
[INFO] 
[INFO] Step 1/7 : FROM openjdk:11-jdk
[INFO] 
[INFO] Pulling from library/openjdk
[INFO] Digest: sha256:c03d9833fa868027f586b86ddcc0c9d02efc81ed4825d535efc590f5bdda7327
[INFO] Status: Image is up to date for openjdk:11-jdk
[INFO]  ---> ecea1177bed0
[INFO] Step 2/7 : VOLUME /tmp
[INFO] 
[INFO]  ---> Using cache
[INFO]  ---> 6d3d1ae86d01
[INFO] Step 3/7 : ARG JAR_FILE
[INFO] 
[INFO]  ---> Using cache
[INFO]  ---> 27ff65287565
[INFO] Step 4/7 : ADD ${JAR_FILE} app.jar
[INFO] 
[INFO]  ---> 2d98683595d1
[INFO] Step 5/7 : RUN bash -c 'touch /app.jar'
[INFO] 
[INFO]  ---> Running in 6c83e249a298
[INFO] Removing intermediate container 6c83e249a298
[INFO]  ---> 10943668e1e0
[INFO] Step 6/7 : EXPOSE 18000
[INFO] 
[INFO]  ---> Running in 46e4d9404564
[INFO] Removing intermediate container 46e4d9404564
[INFO]  ---> a1f70db1bbb2
[INFO] Step 7/7 : ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom","-jar", "/app.jar"]
[INFO] 
[INFO]  ---> Running in 1922a29d5f25
[INFO] Removing intermediate container 1922a29d5f25
[INFO]  ---> 5bc0313d3efb
[INFO] Successfully built 5bc0313d3efb
[INFO] Successfully tagged lanzhou/user-business:latest
[INFO] 
[INFO] Detected build of image with id 5bc0313d3efb
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl/target/user-business-impl-docker-info.jar
[INFO] Successfully built lanzhou/user-business:latest
[INFO] 
[INFO] --- dockerfile-maven-plugin:1.4.13:push (default-cli) @ user-business-impl ---
[WARNING] Did not find maven server configuration for docker server docker.io
[INFO] The push refers to repository [docker.io/lanzhou/user-business]
[INFO] Image 5a64dbe3b54c: Preparing
[INFO] Image fbed4f2c9b32: Preparing
[INFO] Image 38c8683424bf: Preparing
[INFO] Image 2fcb5a3afb1f: Preparing
[INFO] Image 27974e3533d3: Preparing
[INFO] Image d35c5bda4793: Preparing
[INFO] Image a3c1026c6bcc: Preparing
[INFO] Image f1d420c2af1a: Preparing
[INFO] Image 461719022993: Preparing
[INFO] Image d35c5bda4793: Waiting
[INFO] Image a3c1026c6bcc: Waiting
[INFO] Image f1d420c2af1a: Waiting
[INFO] Image 461719022993: Waiting
[INFO] Image 2fcb5a3afb1f: Pushing
[INFO] Image fbed4f2c9b32: Pushing
[INFO] Image 5a64dbe3b54c: Pushing
[INFO] Image fbed4f2c9b32: Pushed
[INFO] Image 2fcb5a3afb1f: Pushed
[INFO] Image 38c8683424bf: Pushing
[INFO] Image 27974e3533d3: Pushing
[INFO] Image d35c5bda4793: Pushing
[INFO] Image 5a64dbe3b54c: Pushed
[INFO] Image a3c1026c6bcc: Pushing
[INFO] Image 27974e3533d3: Pushed
[INFO] Image f1d420c2af1a: Pushing
[INFO] Image 461719022993: Pushing
[INFO] Image d35c5bda4793: Pushed
[INFO] Image a3c1026c6bcc: Pushed
[INFO] Image f1d420c2af1a: Pushed
[INFO] Image 461719022993: Pushed
[INFO] Image 38c8683424bf: Pushed
[INFO] latest: digest: sha256:6bf40e4490ad7a73e882cf02df490022c3478ceb3bd6ae91debc431b7b2a56c8 size: 2218
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  02:16 min
[INFO] Finished at: 2020-04-20T13:26:30+08:00
[INFO] ------------------------------------------------------------------------
lwk@qwfys:~/Public/project/com/qwfys/dragon/dragon-business/user-business/user-business-impl$ 
```

- for project named user-service
```bash
lwk@qwfys:~/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl$ mvn clean install dockerfile:build dockerfile:push
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.google.inject.internal.cglib.core.$ReflectUtils$1 (file:/usr/share/maven/lib/guice.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
WARNING: Please consider reporting this to the maintainers of com.google.inject.internal.cglib.core.$ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< com.xtwj.mall:user-service-impl >-------------------
[INFO] Building user-service-impl 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ user-service-impl ---
[INFO] Deleting /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:apply (default) @ user-service-impl ---
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:validate (default) @ user-service-impl ---
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ user-service-impl ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ user-service-impl ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 4 source files to /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ user-service-impl ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ user-service-impl ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.21.0:test (default-test) @ user-service-impl ---
[INFO] No tests to run.
[INFO] 
[INFO] --- maven-jar-plugin:3.1.0:jar (default-jar) @ user-service-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl.jar
[INFO] 
[INFO] --- maven-javadoc-plugin:3.1.0:jar (javadoc) @ user-service-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-javadoc.jar
[INFO] 
[INFO] --- maven-javadoc-plugin:3.1.0:jar (default) @ user-service-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-javadoc.jar
[INFO] 
[INFO] >>> maven-source-plugin:3.0.1:jar (attach-sources) > generate-sources @ user-service-impl >>>
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:apply (default) @ user-service-impl ---
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:validate (default) @ user-service-impl ---
[INFO] 
[INFO] <<< maven-source-plugin:3.0.1:jar (attach-sources) < generate-sources @ user-service-impl <<<
[INFO] 
[INFO] 
[INFO] --- maven-source-plugin:3.0.1:jar (attach-sources) @ user-service-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-sources.jar
[INFO] 
[INFO] --- maven-source-plugin:3.0.1:jar-no-fork (default) @ user-service-impl ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-sources.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.2.1.RELEASE:repackage (default) @ user-service-impl ---
[INFO] Replacing main artifact with repackaged archive
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ user-service-impl ---
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-service-impl/0.0.1-SNAPSHOT/user-service-impl-0.0.1-SNAPSHOT.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/pom.xml to /home/lwk/.m2/repository/com/xtwj/mall/user-service-impl/0.0.1-SNAPSHOT/user-service-impl-0.0.1-SNAPSHOT.pom
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-javadoc.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-service-impl/0.0.1-SNAPSHOT/user-service-impl-0.0.1-SNAPSHOT-javadoc.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-javadoc.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-service-impl/0.0.1-SNAPSHOT/user-service-impl-0.0.1-SNAPSHOT-javadoc.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-sources.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-service-impl/0.0.1-SNAPSHOT/user-service-impl-0.0.1-SNAPSHOT-sources.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-sources.jar to /home/lwk/.m2/repository/com/xtwj/mall/user-service-impl/0.0.1-SNAPSHOT/user-service-impl-0.0.1-SNAPSHOT-sources.jar
[INFO] 
[INFO] --- dockerfile-maven-plugin:1.4.13:build (default-cli) @ user-service-impl ---
[INFO] dockerfile: null
[INFO] contextDirectory: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl
[INFO] Building Docker context /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl
[INFO] Path(dockerfile): null
[INFO] Path(contextDirectory): /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl
[INFO] 
[INFO] Image will be built as lanzhou/user-service:latest
[INFO] 
[INFO] Step 1/7 : FROM openjdk:11-jdk
[INFO] 
[INFO] Pulling from library/openjdk
[INFO] Digest: sha256:c03d9833fa868027f586b86ddcc0c9d02efc81ed4825d535efc590f5bdda7327
[INFO] Status: Image is up to date for openjdk:11-jdk
[INFO]  ---> ecea1177bed0
[INFO] Step 2/7 : VOLUME /tmp
[INFO] 
[INFO]  ---> Using cache
[INFO]  ---> 6d3d1ae86d01
[INFO] Step 3/7 : ARG JAR_FILE
[INFO] 
[INFO]  ---> Using cache
[INFO]  ---> 27ff65287565
[INFO] Step 4/7 : ADD ${JAR_FILE} app.jar
[INFO] 
[INFO]  ---> 69c36df2f825
[INFO] Step 5/7 : RUN bash -c 'touch /app.jar'
[INFO] 
[INFO]  ---> Running in d42637bde22a
[INFO] Removing intermediate container d42637bde22a
[INFO]  ---> 37ff60ddd734
[INFO] Step 6/7 : EXPOSE 18000
[INFO] 
[INFO]  ---> Running in b84e24045f41
[INFO] Removing intermediate container b84e24045f41
[INFO]  ---> ad14301ad6ad
[INFO] Step 7/7 : ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom","-jar", "/app.jar"]
[INFO] 
[INFO]  ---> Running in 08f459131831
[INFO] Removing intermediate container 08f459131831
[INFO]  ---> 60d4d9045b43
[INFO] Successfully built 60d4d9045b43
[INFO] Successfully tagged lanzhou/user-service:latest
[INFO] 
[INFO] Detected build of image with id 60d4d9045b43
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl/target/user-service-impl-docker-info.jar
[INFO] Successfully built lanzhou/user-service:latest
[INFO] 
[INFO] --- dockerfile-maven-plugin:1.4.13:push (default-cli) @ user-service-impl ---
[WARNING] Did not find maven server configuration for docker server docker.io
[INFO] The push refers to repository [docker.io/lanzhou/user-service]
[INFO] Image 50710bdf600e: Preparing
[INFO] Image a7ddd4a51cbc: Preparing
[INFO] Image 38c8683424bf: Preparing
[INFO] Image 2fcb5a3afb1f: Preparing
[INFO] Image 27974e3533d3: Preparing
[INFO] Image d35c5bda4793: Preparing
[INFO] Image a3c1026c6bcc: Preparing
[INFO] Image f1d420c2af1a: Preparing
[INFO] Image 461719022993: Preparing
[INFO] Image d35c5bda4793: Waiting
[INFO] Image a3c1026c6bcc: Waiting
[INFO] Image f1d420c2af1a: Waiting
[INFO] Image 461719022993: Waiting
[INFO] Image 50710bdf600e: Pushing
[INFO] Image a7ddd4a51cbc: Pushing
[INFO] Image 2fcb5a3afb1f: Mounted from lanzhou/user-business
[INFO] Image 27974e3533d3: Mounted from lanzhou/user-business
[INFO] Image 38c8683424bf: Mounted from lanzhou/user-business
[INFO] Image a3c1026c6bcc: Mounted from lanzhou/user-business
[INFO] Image 50710bdf600e: Pushed
[INFO] Image d35c5bda4793: Mounted from lanzhou/user-business
[INFO] Image f1d420c2af1a: Mounted from lanzhou/user-business
[INFO] Image 461719022993: Mounted from lanzhou/user-business
[INFO] Image a7ddd4a51cbc: Pushed
[INFO] latest: digest: sha256:7960c98adf8cfb87b1eb873438d1e03c25ce5abac2570946f5e74bfd1e9d011f size: 2218
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:23 min
[INFO] Finished at: 2020-04-20T13:28:00+08:00
[INFO] ------------------------------------------------------------------------
lwk@qwfys:~/Public/project/com/qwfys/dragon/dragon-service/user-service/user-service-impl$
```

- for project name usr-api
```bash
lwk@qwfys:~/Public/project/com/qwfys/dragon/dragon-api/usr-api$ mvn clean install dockerfile:build dockerfile:push
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by com.google.inject.internal.cglib.core.$ReflectUtils$1 (file:/usr/share/maven/lib/guice.jar) to method java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain)
WARNING: Please consider reporting this to the maintainers of com.google.inject.internal.cglib.core.$ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------------< com.xtwj.mall:usr-api >------------------------
[INFO] Building usr-api 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ usr-api ---
[INFO] Deleting /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:apply (default) @ usr-api ---
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:validate (default) @ usr-api ---
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ usr-api ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ usr-api ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 5 source files to /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ usr-api ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ usr-api ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.21.0:test (default-test) @ usr-api ---
[INFO] No tests to run.
[INFO] 
[INFO] --- maven-jar-plugin:3.1.0:jar (default-jar) @ usr-api ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api.jar
[INFO] 
[INFO] --- maven-javadoc-plugin:3.1.0:jar (javadoc) @ usr-api ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-javadoc.jar
[INFO] 
[INFO] --- maven-javadoc-plugin:3.1.0:jar (default) @ usr-api ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-javadoc.jar
[INFO] 
[INFO] >>> maven-source-plugin:3.0.1:jar (attach-sources) > generate-sources @ usr-api >>>
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:apply (default) @ usr-api ---
[INFO] 
[INFO] --- spring-javaformat-maven-plugin:0.0.9:validate (default) @ usr-api ---
[INFO] 
[INFO] <<< maven-source-plugin:3.0.1:jar (attach-sources) < generate-sources @ usr-api <<<
[INFO] 
[INFO] 
[INFO] --- maven-source-plugin:3.0.1:jar (attach-sources) @ usr-api ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-sources.jar
[INFO] 
[INFO] --- maven-source-plugin:3.0.1:jar-no-fork (default) @ usr-api ---
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-sources.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.2.1.RELEASE:repackage (default) @ usr-api ---
[INFO] Replacing main artifact with repackaged archive
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ usr-api ---
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api.jar to /home/lwk/.m2/repository/com/xtwj/mall/usr-api/0.0.1-SNAPSHOT/usr-api-0.0.1-SNAPSHOT.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/pom.xml to /home/lwk/.m2/repository/com/xtwj/mall/usr-api/0.0.1-SNAPSHOT/usr-api-0.0.1-SNAPSHOT.pom
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-javadoc.jar to /home/lwk/.m2/repository/com/xtwj/mall/usr-api/0.0.1-SNAPSHOT/usr-api-0.0.1-SNAPSHOT-javadoc.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-javadoc.jar to /home/lwk/.m2/repository/com/xtwj/mall/usr-api/0.0.1-SNAPSHOT/usr-api-0.0.1-SNAPSHOT-javadoc.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-sources.jar to /home/lwk/.m2/repository/com/xtwj/mall/usr-api/0.0.1-SNAPSHOT/usr-api-0.0.1-SNAPSHOT-sources.jar
[INFO] Installing /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-sources.jar to /home/lwk/.m2/repository/com/xtwj/mall/usr-api/0.0.1-SNAPSHOT/usr-api-0.0.1-SNAPSHOT-sources.jar
[INFO] 
[INFO] --- dockerfile-maven-plugin:1.4.13:build (default-cli) @ usr-api ---
[INFO] dockerfile: null
[INFO] contextDirectory: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api
[INFO] Building Docker context /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api
[INFO] Path(dockerfile): null
[INFO] Path(contextDirectory): /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api
[INFO] 
[INFO] Image will be built as lanzhou/user-api:latest
[INFO] 
[INFO] Step 1/7 : FROM openjdk:11-jdk
[INFO] 
[INFO] Pulling from library/openjdk
[INFO] Digest: sha256:c03d9833fa868027f586b86ddcc0c9d02efc81ed4825d535efc590f5bdda7327
[INFO] Status: Image is up to date for openjdk:11-jdk
[INFO]  ---> ecea1177bed0
[INFO] Step 2/7 : VOLUME /tmp
[INFO] 
[INFO]  ---> Using cache
[INFO]  ---> 6d3d1ae86d01
[INFO] Step 3/7 : ARG JAR_FILE
[INFO] 
[INFO]  ---> Using cache
[INFO]  ---> 27ff65287565
[INFO] Step 4/7 : ADD ${JAR_FILE} app.jar
[INFO] 
[INFO]  ---> 647ee379d768
[INFO] Step 5/7 : RUN bash -c 'touch /app.jar'
[INFO] 
[INFO]  ---> Running in 311c0ca3eed1
[INFO] Removing intermediate container 311c0ca3eed1
[INFO]  ---> 164a94ed6e82
[INFO] Step 6/7 : EXPOSE 18000
[INFO] 
[INFO]  ---> Running in c6fb5ff5673a
[INFO] Removing intermediate container c6fb5ff5673a
[INFO]  ---> 2e29956a39d5
[INFO] Step 7/7 : ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom","-jar", "/app.jar"]
[INFO] 
[INFO]  ---> Running in e4892ef98529
[INFO] Removing intermediate container e4892ef98529
[INFO]  ---> d80892132614
[INFO] Successfully built d80892132614
[INFO] Successfully tagged lanzhou/user-api:latest
[INFO] 
[INFO] Detected build of image with id d80892132614
[INFO] Building jar: /home/lwk/Public/project/com/qwfys/dragon/dragon-api/usr-api/target/usr-api-docker-info.jar
[INFO] Successfully built lanzhou/user-api:latest
[INFO] 
[INFO] --- dockerfile-maven-plugin:1.4.13:push (default-cli) @ usr-api ---
[WARNING] Did not find maven server configuration for docker server docker.io
[INFO] The push refers to repository [docker.io/lanzhou/user-api]
[INFO] Image b45cd27c3790: Preparing
[INFO] Image 5b35cc8afc2c: Preparing
[INFO] Image 38c8683424bf: Preparing
[INFO] Image 2fcb5a3afb1f: Preparing
[INFO] Image 27974e3533d3: Preparing
[INFO] Image d35c5bda4793: Preparing
[INFO] Image a3c1026c6bcc: Preparing
[INFO] Image f1d420c2af1a: Preparing
[INFO] Image 461719022993: Preparing
[INFO] Image d35c5bda4793: Waiting
[INFO] Image a3c1026c6bcc: Waiting
[INFO] Image f1d420c2af1a: Waiting
[INFO] Image 461719022993: Waiting
[INFO] Image b45cd27c3790: Pushing
[INFO] Image 5b35cc8afc2c: Pushing
[INFO] Image 27974e3533d3: Mounted from lanzhou/user-service
[INFO] Image 38c8683424bf: Mounted from lanzhou/user-service
[INFO] Image 2fcb5a3afb1f: Mounted from lanzhou/user-service
[INFO] Image d35c5bda4793: Mounted from lanzhou/user-service
[INFO] Image f1d420c2af1a: Mounted from lanzhou/user-service
[INFO] Image a3c1026c6bcc: Mounted from lanzhou/user-service
[INFO] Image 5b35cc8afc2c: Pushed
[INFO] Image b45cd27c3790: Pushed
[INFO] Image 461719022993: Mounted from lanzhou/user-service
[INFO] latest: digest: sha256:d1c2b9c65f0037371f4e199ad7c56c64b424484267959bdf32af5b2d88276bf6 size: 2218
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:16 min
[INFO] Finished at: 2020-04-20T13:29:23+08:00
[INFO] ------------------------------------------------------------------------
lwk@qwfys:~/Public/project/com/qwfys/dragon/dragon-api/usr-api$ 
```

# Run

- step 1 search docker image
```bash
[root@xtwj90 ~]# docker search lanzhou
NAME                    DESCRIPTION         STARS               OFFICIAL            AUTOMATED
lanzhou/user-business                       0                                       
lanzhou/user-service                        0                                       
lanzhou/user-api                            0                                       
[root@xtwj90 ~]# 
```

- step2 run 

```bash
[root@xtwj90 ~]# docker run -d --name user-business lanzhou/user-business
Unable to find image 'lanzhou/user-business:latest' locally
latest: Pulling from lanzhou/user-business
7e2b2a5af8f6: Pull complete 
09b6f03ffac4: Pull complete 
dc3f0c679f0f: Pull complete 
fd4b47407fc3: Pull complete 
bb40faab53a2: Pull complete 
4bb55ce33bc6: Pull complete 
b2c255cea82d: Pull complete 
d8523297843c: Pull complete 
d74a19fc4ded: Pull complete 
Digest: sha256:6bf40e4490ad7a73e882cf02df490022c3478ceb3bd6ae91debc431b7b2a56c8
Status: Downloaded newer image for lanzhou/user-business:latest
84d59fe471ea39ad5c8a9d9a48caca35921a79c62b9712bcb859c144ab00c460
[root@xtwj90 ~]# docker run -d --name user-service lanzhou/user-service
Unable to find image 'lanzhou/user-service:latest' locally
latest: Pulling from lanzhou/user-service
7e2b2a5af8f6: Already exists 
09b6f03ffac4: Already exists 
dc3f0c679f0f: Already exists 
fd4b47407fc3: Already exists 
bb40faab53a2: Already exists 
4bb55ce33bc6: Already exists 
b2c255cea82d: Already exists 
97241e915afc: Pull complete 
32be84694706: Pull complete 
Digest: sha256:7960c98adf8cfb87b1eb873438d1e03c25ce5abac2570946f5e74bfd1e9d011f
Status: Downloaded newer image for lanzhou/user-service:latest
add3525792b3f7572d732a26aca5f9a24f87a24679fd2ac82942cc36d5c45592
[root@xtwj90 ~]# docker run -d --name user-backend -p 18000:18000 lanzhou/user-api
Unable to find image 'lanzhou/user-api:latest' locally
latest: Pulling from lanzhou/user-api
7e2b2a5af8f6: Already exists 
09b6f03ffac4: Already exists 
dc3f0c679f0f: Already exists 
fd4b47407fc3: Already exists 
bb40faab53a2: Already exists 
4bb55ce33bc6: Already exists 
b2c255cea82d: Already exists 
05e259fd5e9c: Pull complete 
4c60065635a9: Pull complete 
Digest: sha256:d1c2b9c65f0037371f4e199ad7c56c64b424484267959bdf32af5b2d88276bf6
Status: Downloaded newer image for lanzhou/user-api:latest
bea17018d7e608b0fdc5c53f1b8be922b85d31a2cf79f2b0d28a66fcdee248e9
[root@xtwj90 ~]# docker ps -a
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                      NAMES
bea17018d7e6        lanzhou/user-api        "java -Djava.securit…"   37 seconds ago      Up 35 seconds       0.0.0.0:18000->18000/tcp   user-backend
add3525792b3        lanzhou/user-service    "java -Djava.securit…"   5 minutes ago       Up 5 minutes        18000/tcp                  user-service
84d59fe471ea        lanzhou/user-business   "java -Djava.securit…"   8 minutes ago       Up 8 minutes        18000/tcp                  user-business
[root@xtwj90 ~]# 
[root@xtwj90 ~]# ss -ntlp
State      Recv-Q Send-Q                                                                          Local Address:Port                                                                                         Peer Address:Port              
LISTEN     0      100                                                                                 127.0.0.1:25                                                                                                      *:*                   users:(("master",pid=1547,fd=13))
LISTEN     0      128                                                                                         *:22                                                                                                      *:*                   users:(("sshd",pid=1147,fd=3))
LISTEN     0      100                                                                                     [::1]:25                                                                                                   [::]:*                   users:(("master",pid=1547,fd=14))
LISTEN     0      128                                                                                      [::]:18000                                                                                                [::]:*                   users:(("docker-proxy",pid=7004,fd=4))
LISTEN     0      128                                                                                      [::]:22                                                                                                   [::]:*                   users:(("sshd",pid=1147,fd=4))
[root@xtwj90 ~]# 
```

打开浏览器http://192.168.3.90:18000/swagger-ui.html
![](https://oscimg.oschina.net/oscnet/up-22cbb9b340356bef0856ef2399ac596af8e.png)

![](https://oscimg.oschina.net/oscnet/up-7c22b86e31861b9e52487d3d3d0f7023d1a.JPEG)

至此运行成功。
