---
제목: 파일 설명 포함: 파일 서비스 포함: 이벤트 허브 작성자: spelluru ms.service: 이벤트 허브 ms.topic: ms.date 포함: 2021/03/31 ms.author: spelluru ms.custom: "파일 포함", "fasttrack-편집", "iot", "이벤트 허브"

---

다음 표는 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)의 할당량과 제한을 제공합니다. Event Hubs 가격에 대한 자세한 내용은 [Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

### <a name="common-limits-for-all-tiers"></a>모든 계층에 대한 공통 제한
다음 제한은 모든 계층에서 공통적으로 적용됩니다. 

| 제한 |  메모 | 값 |
| --- |  --- | --- |
 이벤트 허브 이름의 크기 |- | 256자 |
| 소비자 그룹 이름의 크기 | Kafka 프로토콜에는 소비자 그룹을 만들 필요가 없습니다. | <p>Kafka: 256문자</p><p>AMQP: 50문자 |
| 소비자 그룹당 비 Epoch 수신자 수 |- |5 |
| 네임스페이스당 권한 부여 규칙의 수 | 권한 부여 규칙 만들기에 대한 후속 요청이 거부됩니다.|12 |
| GetRuntimeInformation 메서드에 대한 호출 수 |  - | 초당 50 | 
| VNet(가상 네트워크) 수 | - | 128 | 
| IP 구성 규칙 수 | - | 128 | 
| 스키마 그룹 이름의 최대 길이 | | 50 |  
| 스키마 이름의 최대 길이 | | 100 |    
| 스키마당 크기(바이트) | | 1MB |   
| 스키마 그룹당 속성 수 | | 1024 |
| 스키마 그룹 속성 키당 크기(바이트) | | 256 | 
| 스키마 그룹 속성 값당 크기(바이트) | | 1024 | 

### <a name="basic-vs-standard-vs-dedicated-tiers"></a>기본, 표준, 전용 계층 비교
다음 표에서는 기본, 표준, 전용 계층에 대해 다를 수 있는 제한을 보여줍니다. 표에서 CU는 [용량 단위](../articles/event-hubs/event-hubs-dedicated-overview.md)이고 TU는 [처리량 단위](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-)입니다. 

| 제한 | Basic | Standard | 전용 |
| ----- | ----- | -------- | -------- | 
| Event Hubs 게시의 최대 크기 | 256 KB | 1MB | 1MB |
| Event Hub당 소비자 그룹 수 | 1 | 20 | CU당 제한 없음, 이벤트 허브당 1,000개 |
| 네임스페이스당 AMQP 연결 수 | 100 | 5,000 | 100 K 포함 및 최대 |
| 이벤트 데이터의 최대 보존 기간 | 1일 | 1-7일 | 90일, CU당 10TB 포함 |
| 최대 TU 또는 CU |20TU | 20TU | 20개 CU |
| Event Hub당 파티션 수 | 32 | 32 | 이벤트 허브당 1024<br/> CU당 2,000개 |
| 구독당 네임스페이스의 수 | 100 | 100 | 100(CU당 50개) |
| 네임스페이스당 Event Hubs 개수 | 10 | 10 | 1000 |
| 수신 이벤트 | | 100만 이벤트당 요금 부과 | 포함|
| 캡처 | 해당 없음 | 시간당 요금 부과 | 포함 |
| 스키마 레지스트리(네임스페이스) 크기(메가바이트) | 해당 없음 | 25 |  1024 |
| 스키마 레지스트리 또는 네임스페이스에 있는 스키마 그룹 수 | 해당 없음 | 1 - 기본 그룹 제외 | 1000 |
| 모든 스키마 그룹의 스키마 버전 수 | 해당 없음 | 25 | 10000 |

> [!NOTE]
> 이벤트를 개별적으로 게시하거나 일괄처리할 수 있습니다. 게시 제한(SKU에 따름)은 단일 이벤트인지 일괄 처리인지에 관계없이 적용됩니다. 최대 임계값보다 큰 게시 이벤트는 거부됩니다.

