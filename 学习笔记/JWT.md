## 1、什么是JWT

JWT，也就是JSON Web Tokens。JSON Web Token (JWT) is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be  verified and trusted because it is digitally signed. JWTs can be signed  using a secret (with the **HMAC** algorithm) or a public/private key pair using **RSA** or **ECDSA**.

重点：

- JWT是一个开源标准，它定义了一种紧凑的，自包含的方式，用于在各方之间以**JSON对象安全地**传输信息。
- 此对象可以验证和信任，因为它是数字签名的。
- JWT可以使用秘密(使用HMAC算法)或使用RSA或ECDSA的公钥/私钥对进行签名
- 通俗来说，JWT就是以JSON形式作为Web应用中的令牌，用于在各方之间安全地将信息作为JSON对象传输。

## 2、JWT能做什么

- 授权：这也是JWT最常见的功能，一旦用户登录，每个后续请求将包括JWT，从而允许用户访问该令牌允许的路由，服务和资源。单点登录就是当前广泛使用JWT的一项功能，因为它开销很小并且可以在不同的域中轻松使用。
- 信息交换：JWT也是在各方之间安全地传输信息的号方法。因为可以对JWT进行签名，所以可以确保发件人是他们所说的人。此外，由于签名是使用标头和有效负载计算，因此您可以验证内容是否遭到篡改。

## 3、为什么是JWT

### 3.1、基于传统的Session认证

- 认证方式：我们知道，http协议本身是一种无状态的协议，而这就意味着如果用户向我们的应用提供了用户名和密码来进行用户认证，那么下一次请求时，用户还要再一次进行用户认证才行，因为根据http协议，我们并不能知道是哪个用户发出的请求，所以为了让我们的应用能识别是哪个用户发出的请求，我们只能在服务器存储一份用户登录的信息，这份登录信息会在响应时传递给浏览器，告诉其保存为cookie,以便下次请求时发送给我们的应用，这样我们的应用就能识别请求来自哪个用户了,这就是传统的基于session认证。
- ![](http://fl.ljuuu.com/img/20200817171649769.png)
- 问题：
  - 每个用户经过我们的应用认证之后，我们的应用都要在服务端做一次记录，以方便用户下次请求的鉴别，通常而言session都是保存在内存中，而随着认证用户的增多，服务端的开销会明显增大。
  - 用户认证之后，服务端做认证记录，如果认证的记录被保存在内存中的话，这意味着用户下次请求还必须要请求在这台服务器上,这样才能拿到授权的资源，这样在分布式的应用上，相应的限制了负载均衡器的能力。这也意味着限制了应用的扩展能力。
  -  因为是基于cookie来进行用户识别的, cookie如果被截获，用户就会很容易受到跨站请求伪造的攻击。

### 3.2、基于JWT认证

- 认证方式：首先用户登录后，服务系统会生成一个JWT令牌返回给用户保存在本地，当再次登录是则需要带上令牌，服务端验证JWT，通过则返回数据，错误则返回错误信息
- ![](http://fl.ljuuu.com/img/src=http---img2018.cnblogs.com-blog-1384899-201811-1384899-20181109192859025-1251269511.png&refer=http---img2018.cnblogs.jpg)

## 4、JWT的结构

- 令牌组成：Header，Payload，Signature。JWT通常格式为：xxxx.yyyy.zzzz，header.Payload.Signature

- Header：标头通常由两部分组成：令牌的类型(如JWT)和所使用的签名算法(例如HMAC,SHA256或RSA)。它会使用Base64编码组成JWT结构的第一部分。注意：Base64是一种编码，并不是一种加密算法

  ```json
  {
      "alg": "HS256",
      "typ": "JWT"
  }
  
  Base64: xxxxxx.
  ```

- Payload：令牌的第二部分是有效负载，其中包含声明。声明是有关实体(通常是用户)和其他数据的声明。同样的，它会使用Base64编码组成JWT的第二部分

  ```json
  //不要用用户敏感信息，比如密码
  {
      "sub": "sanfashi",
      "name": "ferron",
      "admin": true
  }
  
  Base64: yyyyyy.
  ```

- Signature：前面两部分都是使用Base64进行编码，即前端可以解开知道里面的信息。这部分是需要使用编码后的header和payload以及我们提供的一个密钥，然后使用header中的签名算法进行签名。签名的作用就是保住JWT没有被篡改过。

## 5、使用JWT

- 引入依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
  <dependency>
      <groupId>com.auth0</groupId>
      <artifactId>java-jwt</artifactId>
      <version>3.4.0</version>
  </dependency>
  ```

- 生成`token`

  ```java
  //header的map
  HashMap<String,Object> map = new HashMap<>();
  //过期时间
  Calendar instance = Calendar.getInstance();
  instance.add(Calendar.SECOND, 20);
  //生成token
  String token = JWT.create()
      .withHeader(map)  //header
      .withClaim("userId", 21)  //payload
      .withClaim("username", "ferron")
      .withExpiresAt(instance.getTime())
      .sign(Algorithm.HMAC256("!SANFASHI"));  //签名
  
  System.out.println(token);
  
  //token
  //eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
  //.eyJleHAiOjE2MjAwMzEwODYsInVzZXJJZCI6MjEsInVzZXJuYW1lIjoiZmVycm9uIn0
  //.oe-fCfk51AYdqW_pSKL_r0XOGljw_Bt-M5qglKNyisk
  ```

- 验证`token`

  ```java
  //创建验证对象
  JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("!SANFASHI")).build();
  
  DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MjAwMzE5NTIsInVzZXJJZCI6MjEsInVzZXJuYW1lIjoiZmVycm9uIn0.AFq_J5-9tR00jXMACLAhKeURuId4GdGzx_8lN1OrW-0");
  
  System.out.println(verify.getClaim("userId").asInt());
  System.out.println(verify.getClaim("username").asString());
  
  //21
  //ferron
  ```

- 常见验证异常

  ```
  SignatureVerificationException: 签名不一致异常
  TokenExpiredException: 令牌过期异常
  AlgorithmMismatchException: 算法不匹配异常
  InvalidClaimException: 失效的payload异常
  ```

## 6、封装工具类

```java
public class JWTUtils {
    
    private static final String SING = "SANFASHI";

    /**
     * 生成token
     */
    public static String getToken(Map<String,String> map){

        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.DATE, 7); //默认7天过期

        //创建jwt builder
        JWTCreator.Builder builder = JWT.create();

        //输入payload
        map.forEach((k,v) ->{
            builder.withClaim(k,v);
        });

        String token = builder.withExpiresAt(instance.getTime())
                .sign(Algorithm.HMAC256(SING));

        return token;
    }

    /**
     * 验证token，并且返回token信息
     */
    public static DecodedJWT verify(String token){
        return JWT.require(Algorithm.HMAC256(SING)).build().verify(token);
    }
}
```

## 7、整合springboot

- 导入依赖

  ```xml
  <dependency>
      <groupId>com.auth0</groupId>
      <artifactId>java-jwt</artifactId>
      <version>3.4.0</version>
  </dependency>
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.1.3</version>
  </dependency>
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
  </dependency>
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.19</version>
  </dependency>
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
  </dependency>
  ```

  









