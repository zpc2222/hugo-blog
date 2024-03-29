---
title: "登录框架"
date: 2022-09-28T00:17:58+08:00
lastmod: 2022-09-29T00:17:58+08:00
author: ["藏锋"]
keywords: 
- 框架
- spring
categories: 
- tech
tags: 
- java
- spring
description: "spring security框架 拦截器用法"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

# 登录框架
使用的spring security框架，通过过滤器进行拦截，
登录的时候通过jwt生成token
调用接口，携带token，同时刷新token时长
除了登录接口及 swagger，其他的都进行拦截

## 过滤器
```
@Configuration  
@EnableWebSecurity  
@EnableGlobalMethodSecurity(prePostEnabled = true)  
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {  
  
    private static final String[] EXCLUDE_URL = {"/doc.html","/login"};  
    /**  
     * 认证失败处理类  
     */  
    private final AuthenticationEntryPointImpl unauthorizedHandler;  
    private final JwtAccessDeniedHandler jwtAccessDeniedHandler;  
    private final JwtAppletTokenUtils jwtUtil;  
  
    public WebSecurityConfig(AuthenticationEntryPointImpl unauthorizedHandler, JwtAccessDeniedHandler jwtAccessDeniedHandler, JwtAppletTokenUtils jwtUtil) {  
        this.unauthorizedHandler = unauthorizedHandler;  
        this.jwtAccessDeniedHandler = jwtAccessDeniedHandler;  
        this.jwtUtil = jwtUtil;  
    }  
  
    @Override  
    protected void configure(HttpSecurity httpSecurity) throws Exception {  
  
        httpSecurity  
                // CSRF禁用，因为不使用session  
                .csrf().disable()  
                // 认证失败处理类  
                .exceptionHandling().authenticationEntryPoint(unauthorizedHandler).accessDeniedHandler(jwtAccessDeniedHandler)  
                // 防止iframe 造成跨域(这一段不写会导致swagger访问不了)  
                .and().headers().frameOptions().disable().and()  
                // 基于token，所以不需要session  
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS).and()  
                // 过滤请求  
                .authorizeRequests()  
                // 放行OPTIONS请求  
                .antMatchers(HttpMethod.OPTIONS, "/**").permitAll()  
                // 允许匿名访问接口  
                .antMatchers(EXCLUDE_URL).permitAll()  
                // 除上面外的所有请求全部需要鉴权认证  
                .anyRequest().authenticated();  
  
        httpSecurity.apply(new TokenConfigurer(jwtUtil));  
    }  
  
    /***  
     * 核心过滤器配置方法  
     * @param web  
     */  
    @Override  
    public void configure(WebSecurity web) {  
        web.ignoring().antMatchers("/doc.html", "/webjars/**", "/**/api-docs/**","/login","/pay/callback","/pay/refundCallback","/pay/callback");  
    }  
  
    static class TokenConfigurer extends SecurityConfigurerAdapter<DefaultSecurityFilterChain, HttpSecurity> {  
  
        private final JwtAppletTokenUtils jwtUtil;  
  
        public TokenConfigurer(JwtAppletTokenUtils jwtUtil) {  
            this.jwtUtil = jwtUtil;  
        }  
  
        @Override  
        public void configure(HttpSecurity http) {  
            JwtAuthenticationTokenFilter beforeFilter = new JwtAuthenticationTokenFilter(jwtUtil);  
            http.addFilterBefore(beforeFilter, UsernamePasswordAuthenticationFilter.class);  
        }  
    }  
}
```

## Jwt 生成方法：
```
public String createToken(Map<String, Object> claims) {  
    return Jwts.builder().claim(AUTHORITIES_KEY, JSONObject.toJSONString(claims)).setId(UUID.randomUUID().toString()).setIssuedAt(new Date()).setExpiration(new Date((new Date()).getTime() + tokenValidityInSeconds*1000)).compressWith(CompressionCodecs.DEFLATE).signWith(key, SignatureAlgorithm.HS512).compact();  
}
```
## Jwt 校验方法：
```
public boolean validateToken(String authToken) {  
    try {  
        Date expiration = getExpirationDateFromToken(authToken);  
        if (ObjectUtil.isNotNull(expiration) && expiration.getTime() - System.currentTimeMillis() <= LAST_EXPIRE_TIME) {  
            // 刷新过期时间  
            Claims claims = getClaimsFromToken(authToken);  
            claims.setExpiration(new Date((new Date()).getTime() + tokenValidityInSeconds*1000));  
        }  
        return true;  
    } catch (io.jsonwebtoken.security.SecurityException | MalformedJwtException e) {  
        log.error("Invalid JWT signature.", e);  
    } catch (ExpiredJwtException e) {  
        log.error("Expired JWT token.", e);  
    } catch (UnsupportedJwtException e) {  
        log.error("Unsupported JWT token.", e);  
    } catch (IllegalArgumentException e) {  
        log.error("JWT token compact of handler are invalid.", e);  
    }  
    return false;  
}  
  
public Date getExpirationDateFromToken(String token) {  
    Date expiration;  
    try {  
        final Claims claims = getClaimsFromToken(token);  
        expiration = claims.getExpiration();  
    } catch (Exception e) {  
        expiration = null;  
    }  
    return expiration;  
}  
  
private Claims getClaimsFromToken(String token) {  
    Claims claims;  
    try {  
        claims = Jwts.parser().setSigningKey(key).parseClaimsJws(token).getBody();  
    } catch (Exception e) {  
        claims = null;  
    }  
    return claims;  
}
```