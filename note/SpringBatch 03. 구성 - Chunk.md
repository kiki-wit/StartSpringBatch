#### **스프링 배치 (Spring Batch) - Chunk-Oriented Processing

### 1. Job 구조
- Job : Job 객체 생성
- JobBuilder : "sampleJob" 이라는 특정 Job 객체 설정
- JobRepository : JobExecution을 통해 Job의 실행 상태를 관리한다.
- build() : JobBuilder를 통해 설정 된 Job을 생성한다.

### 2. Step 구조
- Step : Step 객체 생성
- StepBuilder : "sampleStep" 이라는 Step 객체 설정
- JobRepository : StepExecution을 통해 Step의 실행 상태를 추적, 관리한다.
- PlatformTransactionManager : 트랜잭션 관리 인터페이스. 커밋, 롤백 작업

#### 2-1. StepExecution
- Step의 실행상태를 추적하고 관리한다.
- Step의 실행정보를 저장한다. (시작 시간, 종료 시간, 상태, 처리 된 항목 수, 실패 여부 등)

#### 2-2. ExecutionCentext
- StepExecution에 설정되어 중간 데이터를 저장하고 StepExecution은 그 데이터를 추적, 관리한다.


```java
@Bean
public Job sampleJob(JobRepository jobRepository, Step sampleStep) {
    return new JobBuilder("sampleJob", jobRepository)
                .start(sampleStep)
                .build();
}

@Bean
public Step sampleStep(JobRepository jobRepository, 
		PlatformTransactionManager transactionManager) { 
	return new StepBuilder("sampleStep", jobRepository)
				.<String, String>chunk(10, transactionManager) 
				.reader(itemReader())
				.writer(itemWriter())
				.build();
}
```


마지막 학습 페이지
https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/configuring.html