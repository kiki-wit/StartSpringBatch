#### **스프링 배치 (Spring Batch) - 페이징

> 데이터베이스에서 페이징 처리를 통해 대량을 데이터를 읽을 때 사용한다.

### 1. JdbcPagingItemReader

**Gradle**
```java
    dependencies {
	    implementation 'org.springframework.batch:spring-batch-core:4.3.0'
	    // Database Driver (예: MySQL)  
		implementation 'mysql:mysql-connector-java:8.0.34'  
		// Spring JDBC  
		implementation 'org.springframework:spring-jdbc:6.0.12'
	}
	```

- mysql:mysql-connector-java : MYSQL의 공식 JAVA Connector 이며, JDBC를 통해 데이터베이스에 접속할 수 있게 해준다.
- spring-jdbc : JdbcPaingItemReader 는 JdbcTemplate 을 내부적으로 사용한다.

