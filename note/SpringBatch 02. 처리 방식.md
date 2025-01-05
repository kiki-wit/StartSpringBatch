#### **스프링 배치 (Spring Batch) 처리 방식**

1) Chunk-Oriented Processing
2) Tasklet-Oriented Processing

### 1. Chunk-Oriented

>스프링배치는 기본적으로 청크 지향 처리(Chunk-Oriented Processing)를 사용한다. 데이터를 하나씩 읽어들이고 그 데이터를 청크(Chunk)로 묶어서 트랜잭션 처리한다. 즉, 청크가 하나의 트랜잭션이다.

1. 데이터 읽기(ItemReader) : 데이터를 한 항목씩 읽어온다.
2. (선택) 데이터 처리(ItemProcessor) : 변환, 검증, 필터링 등 읽은 데이터를 처리한다.
3. 청크 처리 : Commit Interval에 해당하는 만큼 아이템을 읽어들이면, 그 아이템들을 청크로 묶어 처리
4. 청크 쓰기 : 하나의 청크를 ItemWriter 를 통해 저장한다.
5. 트랜잭션 커밋 : 청크 쓰기가 성공하면 해당 트랜잭션이 커밋된다.

```java
@Bean
public Step step(StepBuilderFactory stepBuilderFactory, ItemReader<MyItem> reader, ItemProcessor<MyItem, MyProcessedItem> processor, ItemWriter<MyProcessedItem> writer) {
    return stepBuilderFactory.get("myStep")
            .<MyItem, MyProcessedItem>chunk(500)  // 청크 사이즈 500으로 설정
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .build(); // step 객체 생성
}
```

#### 1-1. ItemProcessor

>Chunk-Oriented 단계에서 선택적으로 구성되며, ItemWrite로 전달되기 전에 처리한다.

**pseudo code(의사코드)** 

```java
List items = new Arraylist();
for(int i = 0; i < commitInterval; i++){
    Object item = itemReader.read();
    if (item != null) {
        items.add(item);
    }
}

List processedItems = new Arraylist();
for(Object item: items){
    Object processedItem = itemProcessor.process(item);
    if (processedItem != null) {
        processedItems.add(processedItem);
    }
}

itemWriter.write(processedItems);
```


### 2. Tasklet-Oriented

>단일 작업 처리 방식 사용. 데이터베이스의 특정 작업을 한 번에 처리할 때 사용한다.

```java
@Bean
public Step step(StepBuilderFactory stepBuilderFactory) {
    return stepBuilderFactory.get("myStep")
            .tasklet(new MyTasklet())
            .build();
}

public class MyTasklet implements Tasklet {
    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        // 여기서 단일 작업을 처리합니다. 예: 파일 처리, DB 작업 등
        System.out.println("Executing a tasklet-based step");
        return RepeatStatus.FINISHED;
    }
}
```





