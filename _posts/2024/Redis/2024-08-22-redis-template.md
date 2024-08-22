---
layout: post
title: "Redis template"
date: 2024-08-22
categories: redis 
---



## Redis template 이란?
Redis 와 상호작용 할때 사용하는 주요 클래스
RedisTemplate이 한 번 설정되면, 이 템플릿은 스레드 안전하며 여러 인스턴스에서 재사용 가능
RedisTemplate은 대부분의 작업에서 Java 기반 직렬화 사용
- 템플릿이 읽거나 쓰는 모든 객체가 Java 직렬화 및 역직렬화를 통해 처리됨


### 문자열 중심
Redis에 저장되는 키와 값이 java.lang.String인 경우가 매우 흔함
Redis 모듈은 RedisConnection과 RedisTemplate의 두 가지 확장을 제공


## 예제 코드
### bean 등록
```
@Configuration
public class MyConfig {

    @Bean
    LettuceConnectionFactory connectionFactory() {
        return new LettuceConnectionFactory();
    }


    @Bean
    RedisTemplate<String, String> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, String> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        return template;
    }

    // string 특화
    @Bean
    StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {

        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }


}
```

### String 중심 사용 예시
```
@Autowired
    private StringRedisTemplate redisTemplate;


    public RedisTemplateService(@Qualifier("stringRedisTemplate") StringRedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public void addValue(String userId, String value) {
        redisTemplate.opsForList().leftPush(userId, value);
    }

    public String getValue(String userId){
        return redisTemplate.opsForList().leftPop(userId);
    }

```
@Qualifier로 bean 사용을 명시 


local 환경에서 테스트하기 위해서 redis 서버를 먼저 띄어 두어야한다
redis 서버 시작 명령어 : `redis-server`