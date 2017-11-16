```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------| 
|maxPollingInterval|60000|큐 폴링 사이 최대 간격(밀리초)입니다.| 
|visibilityTimeout|0|메시지 처리가 실패하는 경우 재시도 사이의 간격입니다.| 
|batchSize|16|동시에 검색하고 처리할 큐 메시지의 수입니다. 최대값은 32입니다.| 
|maxDequeueCount|5|포이즌 큐로 이동하기 전에 메시지 처리를 시도할 횟수입니다.| 
|newBatchThreshold|batchSize/2|새 메시지 일괄 처리를 페치할 임계값입니다.| 
