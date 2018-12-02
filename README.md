# Spring Cloud Config Server
Service to externalize application configurations

## ToDo
*

----

### Main Dependencies and Components
* Actuator
* Web
* Lombok
* DevTools
* <b>Config Server</b>
```yml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
    <spring-cloud.version>Greenwich.M3</spring-cloud.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>

<repositories>
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/milestone</url>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

### bootstrap.yml
Main properties needed for config server
> Documentation includes allowing Config Server to encrypt/decrypt secrets
```
keytool -genkeypair -alias alias -keyalg RSA -dname "CN=cn,OU=ou,O=org,L=locale,S=state,C=country" -keypass changeme -keystore server.jks -storepass changeme

curl https://config-server/encrypt -d mysecret
curl https://config-server/decrypt -d {cipher}
```
```yaml
info:
  component: Spring Cloud Config Server

server:
  port: 8888

spring:
  application:
    name: zeus-config-service
  cloud:
    config:
      server:
        git:
          uri: githubRepoUrl
          username: user
          password: {cipher}...
          
encrypt:
  key-store:
    location: server.jks
    password: changeme
    alias: alias
    secret: changeme
    
# encrypt:
#   key-store:
#     location: file:/etc/config/server.jks

---

spring:
  profiles: local
  cloud:
    config:
      server:
        git:
          uri: ${HOME}/DevApps/github/zeus-config-repo
```
> In Prod, the keystore credentials should be passed in via startup parameters, with only the location set inside the cloud config properties. The keystore should also be stored on an external system (ie...file system, vault, etc.)

-Djavax.net.ssl.keyStorePassword=

-Djavax.net.ssl.trustStorePassword=

### Main Application class
<pre>
<b>@EnableConfigServer</b>
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}        
</pre>