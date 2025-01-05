#### **스프링 배치 (Spring Batch) 기본 개념 이해하기**

스프링 배치는 대용량 배치 작업을 처리하기 위한 프레임워크로, 데이터 처리의 효율성과 안정성을 제공합니다. 주요 기능으로는 대용량 데이터의 읽기, 처리, 쓰기를 관리하며, 트랜잭션 관리, 청크 기반 처리, 에러 처리, 스케줄링 등을 지원합니다.

- **배치 처리**란?
    - 배치 작업은 반복적이고 대량의 데이터를 처리하는 작업으로, 예를 들어 일일 매출 집계, 데이터 마이그레이션, 로그 분석 등이 포함됩니다.
- **주요 특징**
    - **대용량 데이터 처리**: 대량의 데이터를 효율적으로 읽고 쓰는 기능 제공
    - **청크 처리(Chunk Processing)**: 데이터를 작은 단위로 나누어 읽고 처리한 후 한 번에 저장
    - **트랜잭션 관리**: 일괄 처리 중에 발생할 수 있는 오류를 관리하고 롤백 기능 제공
    - **스케줄링**: 정기적으로 배치 작업을 실행할 수 있도록 지원 (예: Quartz 연동)

#### 2. **스프링 배치 설치 방법**

스프링 배치를 사용하려면 **Spring Batch 라이브러리**를 프로젝트에 추가해야 합니다.

1. **Maven 사용 시:**
     
    ```java
    <dependency>
    <groupId>org.springframework.batch</groupId>
    <artifactId>spring-batch-core</artifactId>
    <version>4.3.0</version> <!-- 최신 버전 확인 필요 -->
	</dependency>
    ```
    
2. **Gradle 사용 시:**
    
    ```java
    dependencies {
	    implementation 'org.springframework.batch:spring-batch-core:4.3.0'
	}
	```
    
3. **Spring Boot 사용 시:** Spring Boot에서 스프링 배치를 사용할 때는 `spring-boot-starter-batch` 의존성을 추가합니다.
    
	```java
	<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-batch</artifactId>
	</dependency>
	```


#### 3. **스프링 배치 구조**

스프링 배치 작업은 크게 **Job**, **Step**, **Item**으로 나눠집니다. 이 구조는 배치 프로세스를 세분화하고 관리할 수 있도록 해줍니다.

- Job : 배치 프로세스의 전체 흐름을 정의
- Step : Job 내의 개별 작업
- ItemReader : 데이터 읽기
- ItemProcessor : 데이터 처리
- ItemWriter : 데이터 저장
- JobLauncher : Job을 실행하고 JobRepository로 실행 상태(성공, 실패)를 저장하고 관리한다.

- **Job**: 전체 배치 작업을 정의하는 객체입니다. Job은 하나 이상의 Step으로 구성됩니다.
- **Step**: Job 내에서 실행되는 개별 단위 작업입니다. 각 Step은 데이터를 읽고, 처리하고, 쓰는 작업을 정의합니다.
- **Item**: 처리할 데이터 단위를 의미합니다. 스프링 배치는 `ItemReader`, `ItemProcessor`, `ItemWriter`로 구성됩니다.

##### 주요 구성 요소:

1. **ItemReader**: 데이터를 읽어오는 역할 (예: 데이터베이스, 파일, 메시지 큐 등에서 데이터 읽기)
2. **ItemProcessor**: 읽은 데이터를 처리하는 역할 (필터링, 변환, 계산 등)
3. **ItemWriter**: 처리된 데이터를 저장하는 역할 (예: 데이터베이스, 파일 등에 기록)

##### 예시 구조:

- **Job**: 데이터를 읽고, 처리하고, 결과를 저장하는 배치 작업
- **Step 1**: 데이터 읽기 (ItemReader)
- **Step 2**: 데이터 처리 (ItemProcessor)
- **Step 3**: 데이터 저장 (ItemWriter)

#### 4. **배치 작업 흐름 예시**

```java
@Bean
public Job exampleJob(JobBuilderFactory jobBuilderFactory, StepBuilderFactory stepBuilderFactory) {
    return jobBuilderFactory.get("exampleJob")
            .start(exampleStep(stepBuilderFactory))
            .build();
}

@Bean
public Step exampleStep(StepBuilderFactory stepBuilderFactory) {
    return stepBuilderFactory.get("exampleStep")
            .<String, String>chunk(10) // 10개씩 묶어서 처리
            .reader(exampleItemReader())
            .processor(exampleItemProcessor())
            .writer(exampleItemWriter())
            .build();
}

@Bean
public ItemReader<String> exampleItemReader() {
    return new ListItemReader<>(Arrays.asList("item1", "item2", "item3"));
}

@Bean
public ItemProcessor<String, String> exampleItemProcessor() {
    return item -> item.toUpperCase(); // 데이터를 대문자로 변환
}

@Bean
public ItemWriter<String> exampleItemWriter() {
    return items -> items.forEach(System.out::println); // 데이터를 출력
}

```

#### 5. **스프링 배치의 주요 기능**

1. **트랜잭션 관리**: 각 Step의 실행 결과를 트랜잭션으로 묶어 처리하므로, 오류가 발생하면 롤백할 수 있습니다.
2. **Error Handling**: 예외 처리 기능을 제공하여, 실패한 작업을 처리하거나 재시도할 수 있습니다.
3. **Chunk 기반 처리**: 데이터를 한 번에 처리하는 것이 아니라, 일정 단위로 나누어 처리함으로써 메모리 효율성을 높이고 안정성을 제공합니다.
4. **잡 파라미터**: Job 실행 시 파라미터를 전달하여 유연한 실행이 가능하며, 이를 통해 동적 파라미터 설정이 가능합니다.
5. **모니터링**: 배치 작업의 실행 결과와 상태를 기록하고, 실패한 작업을 재실행하거나 알림을 보내는 기능을 제공합니다.

