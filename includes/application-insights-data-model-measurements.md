사용자 지정 측정값 컬렉션입니다. 이 컬렉션을 사용하여 원격 분석 항목에 연결된 이름이 지정된 측정값을 보고합니다. 일반적인 사용 사례는 다음과 같습니다.
- 종속성 원격 분석 페이로드의 크기
- 요청 원격 분석에서 처리한 큐 항목의 수
- 고객이 마법사 단계 완료 이벤트 원격 분석을 완료하는 데 걸린 시간.

응용 프로그램 분석에서 [사용자 지정 측정값](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)을 쿼리할 수 있습니다.

```
customEvents 
| where customMeasurements != "" 
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > 현재 소속된 원격 분석 항목과 연결된 사용자 지정 측정값입니다. 이러한 측정값이 포함된 원격 분석 항목을 사용하여 샘플링됩니다. 다른 원격 분석 유형과 독립적 관계에 있는 측정값을 추적하려면 [메트릭 원격 분석](#metric-telemetry)을 사용하세요. 

최대 키 길이: 150
