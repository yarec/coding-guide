# TOC
<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [TOC](#toc)
- [spring-boot](#spring-boot)
    - [initializr](#initializr)
        - [relevant info](#relevant-info)
        - [Ajust](#ajust)
        - [TestCase](#testcase)
    - [web](#web)
        - [BaseController](#basecontroller)
            - [ApiResponse](#apiresponse)
    - [test](#test)
        - [TestCase](#testcase-1)
            - [assertJson](#assertjson)
        - [junit5](#junit5)
        - [AssertJ](#assertj)
        - [JSONAssert](#jsonassert)
    - [utils](#utils)
        - [lombok](#lombok)
    - [devtools](#devtools)
- [Gradle SubProjects](#gradle-subprojects)
- [Rest Repositories](#rest-repositories)
- [mybatis-plus](#mybatis-plus)
    - [h2](#h2)
- [spring-security](#spring-security)
- [utils](#utils-1)
    - [lombok](#lombok-1)
- [Docs](#docs)

<!-- markdown-toc end -->


# spring-boot
## initializr
- https://start.spring.io/
- https://start.aliyun.com/bootstrap.html

dependencies=web,security,lombok,h2,devtools,configuration-processor
```
https://start.aliyun.com/bootstrap.html/#!type=gradle-project&language=java&architecture=none&platformVersion=2.4.1&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=demo&name=demo&description=DemoProject&packageName=com.example.demo&dependencies=web,security,lombok,h2,devtools,configuration-processor
```

dependencies=web,security,lombok,h2,devtools,configuration-processor,fastjson,data-jpa,data-redis,data-rest
```
https://start.aliyun.com/bootstrap.html/#!type=gradle-project&language=java&architecture=none&platformVersion=2.4.1&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=demo&name=demo&description=DemoProject&packageName=com.example.demo&dependencies=web,security,lombok,h2,devtools,configuration-processor,fastjson,data-jpa,data-redis,data-rest
```
gitee: https://gitee.com/mirrors/spring-initializr

### relevant info

like version, options and dependencies.
```
curl -H 'Accept: application/json' https://start.spring.io
curl -H 'Accept: application/json' https://start.spring.io | jq '.dependencies.values[].values[].id'
curl https://start.spring.io/starter.zip -d dependencies=web,devtools -d bootVersion=2.1.9.RELEASE -o my-project.zip
```

### Ajust

build.gradle
```diff
repositories {
-    mavenCentral()
+    maven { url "https://maven.aliyun.com/nexus/content/groups/public" }
}

dependencies {
+   implementation 'io.springfox:springfox-boot-starter:3.0.0'
+   implementation 'org.springframework.boot:spring-boot-starter-validation'
+   implementation 'com.google.guava:guava:29.0-jre'

+   implementation 'io.jsonwebtoken:jjwt-api:0.11.2'
+   implementation 'io.jsonwebtoken:jjwt-impl:0.11.2'
+   implementation 'io.jsonwebtoken:jjwt-jackson:0.11.2'

}
```

application.yaml
```yaml
server:
  port: 9333
  servlet:
    context-path: /api/
spring:
  datasource:
    driverClassName: org.h2.Driver
    username: root
    password: 123456
    platform: h2
    url: jdbc:h2:mem:jwt-demo
  h2:
    console:
      enabled: true
      settings:
        web-allow-others: true
  jpa:
    hibernate:
      ddl-auto: update
    open-in-view: false
    properties:
      hibernate:
        enable_lazy_load_no_trans: true
    show-sql: true
  cache:
    # 使用了Spring Cache后，能指定spring.cache.type就手动指定一下，虽然它会自动去适配已有Cache的依赖，但先后顺序会对Redis使用有影响（JCache -> EhCache -> Redis -> Guava）
    type: REDIS
  redis:
    host: 127.0.0.1
    port: 6379
    # password:  默认没有密码，生产环境一定要设置密码
    # 连接超时时间（ms）
    timeout: 10000
    # Redis默认情况下有16个分片，这里配置具体使用的分片，默认是0
    database: 0
    jedis:
      pool:
        # 连接池最大连接数（使用负值表示没有限制） 默认 8
        max-active: 10
        # 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
        max-wait: -1
        # 连接池中的最大空闲连接 默认 8
        max-idle: 8
        # 连接池中的最小空闲连接 默认 0
        min-idle: 0
```

Copy code from spring-security-jwt-guide
```sh
cp -r src/main/java/github/javaguide/springsecurityjwtguide/security src/main/java/com/example/demo/
cp -r src/main/java/github/javaguide/springsecurityjwtguide/system src/main/java/com/example/demo/
```

App.java
```java
import com.example.demo.system.entity.Role;
import com.example.demo.system.entity.User;
import com.example.demo.system.entity.UserRole;
import com.example.demo.system.enums.RoleType;
import com.example.demo.system.repository.RoleRepository;
import com.example.demo.system.repository.UserRepository;
import com.example.demo.system.repository.UserRoleRepository;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.boot.CommandLineRunner;

@SpringBootApplication
public class DemoApplication implements CommandLineRunner{

    @Autowired
    private RoleRepository roleRepository;
    @Autowired
    private UserRepository userRepository;
    @Autowired
    private UserRoleRepository userRoleRepository;

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    // @Override
    public void run(java.lang.String... args) {
        //初始化角色信息
        for (RoleType roleType : RoleType.values()) {
            roleRepository.save(new Role(roleType.getName(), roleType.getDescription()));
        }
        //初始化一个 admin 用户
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        User user = User.builder().enabled(true).fullName("admin").userName("root").password(bCryptPasswordEncoder.encode("root")).build();
        userRepository.save(user);
        Role role = roleRepository.findByName(RoleType.ADMIN.getName()).get();
        userRoleRepository.save(new UserRole(user, role));
    }
}
```

### TestCase
TestCase.java
```java
package com.softidy.test;

import java.util.Objects;

import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockHttpServletResponse;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.request.MockHttpServletRequestBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
// import org.springframework.transaction.annotation.Transactional;
import org.springframework.util.MultiValueMap;
import org.springframework.util.StringUtils;

import static org.mockito.Mockito.when;
import static org.hamcrest.Matchers.containsString;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;


import static org.assertj.core.api.Assertions.assertThat;

import org.springframework.beans.factory.annotation.Autowired;
import com.alibaba.fastjson.JSONObject;

@SpringBootTest
@AutoConfigureMockMvc
public class TestCase {

    @Autowired
    private MockMvc mockMvc;

    public MvcResult result;

    public MvcResult get(String url) throws Exception {
        result = mockMvc.perform(MockMvcRequestBuilders.get(url)).andReturn();
        return result;
    }

    public String asString () throws Exception {
        String content = result.getResponse().getContentAsString();
        return content;
    }

    public JSONObject asJson() throws Exception {
        JSONObject json = JSONObject.parseObject(asString());
        return json;
    }

    public JSONObject data() throws Exception {
        JSONObject json = JSONObject.parseObject(asString());
        JSONObject data = json.getJSONObject("data");
        return data;
    }

    public void assertCode(String code) throws Exception {
        JSONObject json = asJson();
        assertThat(json.getString("code")).isEqualTo(code);
    }

}
```

TestControllerTest.java
```java
package com.example.demo.controller;

import com.softidy.test.TestCase;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import com.alibaba.fastjson.JSONObject;
import static org.assertj.core.api.Assertions.assertThat;

public class TestControllerTest extends TestCase {

    @Test
    public void test_get() throws Exception {
        get("/succ");
        JSONObject res = asJson();

        System.out.println(res);
        System.out.println(res.getString("code"));
        System.out.println(res.getString("msg"));

        assertThat(res.getString("code")).isEqualTo("200");

    }

    @Test
    public void test_get_user() throws Exception {
        get("/testUser");
        JSONObject res = asJson();

        System.out.println(res);
    }
}
```

## web

### BaseController
    success(status, data, message)
#### ApiResponse

## test
### TestCase
#### assertJson

### junit5
https://junit.org/junit5/docs/current/user-guide


import org.junit.jupiter.api.*;
Test
BeforeEach;
BeforeAll;

### AssertJ

 http://joel-costigliola.github.io/assertj/index.html
 https://assertj.github.io/doc/
 
### JSONAssert

 http://jsonassert.skyscreamer.org/

## utils

### lombok

- @Setter @Getter: 生成 get、set 方法
- @NoArgsConstructor @AllArgsConstructor: 生成无参、全参构造器
- @ToString:  生成toString方法，
- @Data: @ToString、@EqualsAndHashCode、@Getter、@Setter和@RequiredArgsConstrutor的组合
- @Value: 无setter
- @NonNull: 用在属性或构造器上
- @RequiredArgsConstructor: 对指定的参数生成构造方法。
- @Builder: 创建者模式又叫生成器模式（Builder Pattern）,链式调用。 
- @Log: 生成log对象

## devtools

# Gradle SubProjects

https://my.oschina.net/u/580483/blog/4277257

# Rest Repositories
使用 Spring Data REST 以 REST 形式暴露 Spring Data 存储库。
https://www.javainterviewpoint.com/spring-data-rest-example/

https://springhow.com/spring-boot-hateoas/
https://spring.io/guides/gs/accessing-data-rest/

# mybatis-plus
## h2

# spring-security

# utils
## lombok


# Docs

- https://github.com/CodingDocs/springboot-guide
