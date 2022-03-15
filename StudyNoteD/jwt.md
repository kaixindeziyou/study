# jwt

## jwt能做什么

1.**授权**

![image-20210422150009682](../img/image-20210422150009682.png)

**2.信息交换**

![image-20210422150045370](../img/image-20210422150045370.png)



## 使用jwt

![image-20210422190816741](../img/image-20210422190816741.png)

**1.引入依赖**

**2.生成token**

```java
@Test
public void test1(){

    Map<String,Object> map = new HashMap<>();

    Calendar instance = Calendar.getInstance();
    instance.add(Calendar.DAY_OF_WEEK,4);

    String token = JWT.create()
            .withHeader(map)
            .withClaim("username", "tom")
            .withClaim("userId", 1)
            .withExpiresAt(instance.getTime())    //指定令牌过期时间
            .sign(Algorithm.HMAC256("@$%@GBJSFAGRG#@$"));//签名
    System.out.println(token);

}
```



**验证令牌：**

```java
@Test
public void test2(){
    //创建验证对象
    JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("@$%@GBJSFAGRG#@$")).build();
    DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MTk0MzY2OTksInVzZXJJZCI6MSwidXNlcm5hbWUiOiJ0b20ifQ.Zd7raJ9-P41ofv4wQJDJKoLQigyOnEjUfcuVdaaT-AE");
    System.out.println(verify.getClaim("username").asString());
    System.out.println(verify.getClaim("userId").asInt());

}
```





**jwtUtils写法：**

```java
package com.zrulin.findcomrade.utils;

import com.auth0.jwt.JWT;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.interfaces.DecodedJWT;

import java.util.Calendar;

/**
 * @author zrulin
 * @create 2021-04-22 19:58
 */
public class JWTUtils {

    //签名
    private static final String SING = "@#TGL4654H@O$7978KTO^K$&^OLKMG";


    /**
     * 生成token      header.payload.sing
     */
    public static String getToken(String name,Integer id){
    //普通的写法
        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.DATE,7); //默认七天过期

        String token = JWT.create()
                .withClaim("username", name)
                .withClaim("username", id)
                .withExpiresAt(instance.getTime())
                .sign(Algorithm.HMAC256(SING));

//        //我第一次看的map写法
//        Calendar instance = Calendar.getInstance();
//        instance.add(Calendar.DATE,7); //默认七天过期
//        //创建 jwt builder
//        JWTCreator.Builder builder = JWT.create();
//        //payload
//        map.forEach((k,v)->{
//            builder.withClaim(k,v);
//        });
//        String token = builder.withExpiresAt(instance.getTime())
//                .sign(Algorithm.HMAC256(SING));
        return token;
    }

    /**
     * 验证token合法性
     * 要是错了就会直接报异常
     * 对了就可以返回DecoodedJwt对象来获取token中的值
     * @param token
     */
    public static DecodedJWT verify(String token){
        return JWT.require(Algorithm.HMAC256(SING)).build().verify(token);
    }


}
```

**异常信息：**

![image-20210422212632154](../img/image-20210422212632154.png)