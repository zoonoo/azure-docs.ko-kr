---
title: 이벤트 복제 태스크 패턴-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 특정 이벤트 복제 태스크 패턴을 구현 하기 위한 자세한 지침을 제공 합니다.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 7702b1987faabfce8d97e7b5c9b18766df72caad
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803989"
---
# <a name="event-replication-tasks-patterns"></a>이벤트 복제 태스크 패턴

[페더레이션 개요](event-hubs-federation-overview.md) 및 [복제기 함수 개요](event-hubs-federation-replicator-functions.md) 에서는 복제 작업의 및 기본 요소에 대해 설명 하 고이 문서를 계속 하기 전에이에 대해 잘 알고 있는 것이 좋습니다.

이 문서에서는 개요 섹션에 강조 표시 된 몇 가지 패턴에 대 한 구현 지침을 자세히 설명 합니다.

## <a name="replication"></a>복제

복제 패턴은 이벤트 허브 간에 이벤트를 복사 하거나 이벤트 허브에서 Service Bus 큐와 같은 다른 대상으로 이벤트를 복사 합니다. 이벤트 페이로드를 수정 하지 않고 이벤트가 전달 됩니다.

이 패턴의 구현은 [Event Hubs와 Service Bus 샘플 간의](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) 이벤트 복제와 [Event Hubs 간의 이벤트 복제](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) 에 적용 됩니다.

### <a name="streams-and-order-preservation"></a>스트림 및 순서 유지

Azure Functions 또는 Azure Stream Analytics를 통해 복제는 대상 이벤트 허브에 대 한 정확한 1:1 클론 생성을 보장 하는 것이 아니라 응용 프로그램에서 필요로 하는 이벤트의 상대적 순서를 유지 하는 데 중점을 두고 있습니다. 응용 프로그램은 동일한 파티션 키를 사용 하 여 관련 이벤트를 그룹화 하 고 동일한 파티션에 동일한 [파티션 키를 사용 하 여 메시지를 정렬 하 Event Hubs](event-hubs-features.md#partitions)를 전달 합니다.

> [!IMPORTANT] 
> "오프셋" 정보는 각 이벤트 허브에 대해 고유 하며 동일한 이벤트에 대 한 오프셋은 이벤트 허브 인스턴스 간에 차이가 있습니다. 복사 된 이벤트 스트림에서 위치를 찾으려면 시간 기반 오프셋을 사용 하 고 [전파 된 서비스 할당 메타 데이터](#service-assigned-metadata)를 참조 합니다.
>
> 시간 기반 오프셋은 특정 시점에서 받는 사람을 시작 합니다.
> - *Eventposition. FromStart ()* -보존 된 모든 데이터를 다시 읽습니다.
> - *FromEnd ()* -연결 된 시간부터 모든 새 데이터를 읽습니다.
> - *FromEnqueuedTime (dateTime)* -지정 된 날짜 및 시간에서 시작 하는 모든 데이터입니다.
>
> EventProcessor에서 EventProcessorOptions의 InitialOffsetProvider를 통해 위치를 설정 합니다. 다른 수신기 Api를 사용 하 여 위치는 생성자를 통해 전달 됩니다. 


Azure Functions 기반 지침에 사용 되는 [샘플로 제공](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 되는 미리 작성 된 복제 함수 도우미는 원본 파티션에서 검색 된 동일한 파티션 키를 사용 하는 이벤트 스트림이 원본 스트림의 일괄 처리와 동일한 파티션 키로 대상 이벤트 허브에 제출 되도록 합니다.

원본 및 대상 이벤트 허브의 파티션 수가 동일한 경우 대상의 모든 스트림은 원본에서와 동일한 파티션에 매핑됩니다.
다음에 설명 된 몇 가지 추가 패턴에서 중요 한 파티션 수가 다른 경우 매핑은 다르지만 스트림은 항상 함께 순서 대로 유지 됩니다.

대상 파티션에서 파티션 키가 없는 다른 스트림 또는 독립 이벤트에 속하는 이벤트의 상대적인 순서는 항상 원본 파티션과 다를 수 있습니다.

### <a name="service-assigned-metadata"></a>서비스 할당 메타 데이터

원본 이벤트 허브에서 가져온 이벤트의 서비스 할당 메타 데이터 (원래 큐에 대기 시간, 시퀀스 번호 및 오프셋)는 대상 이벤트 허브에서 새 서비스 할당 값으로 대체 되지만 [도우미 함수](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)및 샘플에서 제공 되는 복제 작업을 사용 하 여 원래 값은 사용자 속성 `repl-enqueue-time` (ISO8601 string),,에서 유지 됩니다. `repl-sequence` `repl-offset`

이러한 속성은 문자열 형식 이며 해당 원래 속성의 문자열 형식 값을 포함 합니다. 이벤트가 여러 번 전달 되는 경우에는 직접 소스의 서비스 할당 메타 데이터가 이미 존재 하는 속성에 추가 되 고 값은 세미콜론으로 구분 됩니다.

### <a name="failover"></a>장애 조치

재해 복구를 위해 복제를 사용 하는 경우 Event Hubs 서비스의 지역 가용성 이벤트 또는 네트워크 중단에 대 한 보호를 위해 이러한 모든 오류 시나리오에서는 하나의 이벤트 허브에서 다음 이벤트 허브로 장애 조치 (failover)를 수행 하 여 생산자 및/또는 소비자에 게 보조 끝점을 사용 하도록 지시 해야 합니다.

모든 장애 조치 (failover) 시나리오에서는 네임 스페이스의 필수 요소가 구조적으로 동일 하다 고 가정 합니다. 즉, Event Hubs 및 소비자 그룹의 이름이 동일 하 고 공유 액세스 서명 규칙 및/또는 역할 기반 액세스 제어 규칙이 동일한 방식으로 설정 됩니다. [네임 스페이스를 이동](move-across-regions.md) 하 고 정리 단계를 생략 하는 지침에 따라 보조 네임 스페이스를 만들고 업데이트할 수 있습니다.

생산자와 소비자가 강제로 전환 되도록 하려면 쉽게 연결 하 고 업데이트할 수 있는 위치에서 조회에 사용할 수 있는 네임 스페이스에 대 한 정보를 확인 해야 합니다. 생산자 나 소비자가 자주 발생 하거나 지속적으로 오류가 발생 하는 경우 해당 위치를 참조 하 고 구성을 조정 해야 합니다. 이 구성을 공유 하는 방법에는 여러 가지가 있지만 DNS 및 파일 공유에는 두 가지가 있습니다.

#### <a name="dns-based-failover-configuration"></a>DNS 기반 장애 조치 (failover) 구성

하나는 사용자가 제어 하는 dns에서 DNS SRV 레코드의 정보를 유지 하 고 해당 이벤트 허브 끝점을 가리키는 것입니다. 

> [!IMPORTANT] 
> Event Hubs는 해당 끝점이 CNAME 레코드를 사용 하 여 직접 별칭을 지정 하는 것을 허용 하지 않습니다. 즉, DNS를 끝점 주소에 대 한 복원 가능한 조회 메커니즘으로 사용 하 고 IP 주소 정보를 직접 확인 하지는 않습니다.

도메인 `example.com` 및 응용 프로그램에 대 한 영역을 소유 한다고 가정 `test.example.com` 합니다. 두 개의 대체 Event Hubs에 대해 이제 중첩 된 영역 두 개와 각각에 SRV 레코드를 만듭니다.

SRV 레코드는 일반적인 규칙에 따라 접두사를 사용 하 `_azure_eventhubs._amqp` 고 두 개의 끝점 레코드를 포함 합니다. 두 개의 끝점 레코드는 포트 5671에서 amqp over 443 websocket에 대 한 하나이 고, 둘 다 영역에 해당 하는 네임 스페이스의 Event Hubs 끝점을 가리킵니다.

| 영역                   | SRV 레코드                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

그런 다음 응용 프로그램의 영역에서 기본 이벤트 허브에 해당 하는 하위 영역을 가리키는 CNAME 항목을 만듭니다.

| CNAME 레코드                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

CNAME 및 SRV 레코드를 명시적으로 쿼리할 수 있도록 허용 하는 DNS 클라이언트를 사용 하는 경우 (Java 및 .NET의 기본 제공 클라이언트는 IP 주소에 대 한 단순한 이름 확인만 허용) 원하는 끝점을 확인할 수 있습니다. 예를 들어 [DnsClient.NET](https://dnsclient.michaco.net/)를 사용 하는 경우 lookup 함수는 다음과 같습니다.

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

함수는 위에 표시 된 대로 현재 CNAME로 별칭이 지정 된 영역의 포트 5671에 대해 등록 된 대상 호스트 이름을 반환 합니다.

장애 조치 (failover)를 수행 하려면 CNAME 레코드를 편집 하 고 대체 영역을 가리켜야 합니다.

DNS를 사용 하는 경우와 특히 [Azure DNS](../dns/dns-overview.md)하는 장점은 Azure DNS 정보가 전역적으로 복제 되므로 단일 지역 가동 중단에 대해 복원 됩니다.

이 절차는 [Event Hubs 지리적 DR](event-hubs-geo-dr.md) 이 작동 하는 방식과 유사 하지만 사용자 고유의 컨트롤에서 완벽 하 게 작동 하 고 활성/활성 시나리오 에서도 작동 합니다.

#### <a name="file-share-based-failover-configuration"></a>파일 공유 기반 장애 조치 (failover) 구성

DNS를 사용 하 여 끝점 정보를 공유 하는 가장 간단한 방법은 기본 끝점의 이름을 일반 텍스트 파일에 넣고 중단에 대해 강력 하 고 여전히 업데이트를 허용 하는 인프라에서 파일을 제공 하는 것입니다.

전역 가용성 및 콘텐츠 복제를 사용 하 여 항상 사용 가능한 웹 사이트 인프라를 이미 실행 한 경우 이러한 파일을 추가 하 고 스위치가 필요한 경우 파일을 다시 게시 합니다.

> [!CAUTION]
> 암호를 포함 하는 전체 연결 문자열이 아닌 이러한 방식으로 끝점 이름만 게시 해야 합니다.

#### <a name="extra-considerations-for-failing-over-consumers"></a>소비자 장애 조치 (failover)에 대 한 추가 고려 사항

이벤트 허브 소비자의 경우 장애 조치 (failover) 전략에 대 한 추가 고려 사항은 이벤트 프로세서의 필요에 따라 달라 집니다.

백업 데이터에서 데이터베이스를 포함 하 여 시스템을 다시 작성 해야 하는 재해가 발생 하 고 해당 데이터베이스가 이벤트 허브에 있는 이벤트에서 직접 또는 중간 처리를 통해 생성 되는 경우에는 백업을 복원한 다음 원래 시스템이 삭제 된 순간이 아니라 데이터베이스 백업이 생성 된 순간부터 시스템으로 이벤트를 재생 하기 시작 합니다.

오류가 발생 하는 경우에만 시스템의 조각화 나 단일 이벤트 허브에만 영향을 줄 수 있습니다 .이 경우에는 처리가 중단 된 위치와 동일한 위치에서 이벤트를 계속 처리할 수 있습니다.

이러한 시나리오를 실현 하 고 각 Azure SDK의 이벤트 프로세서를 사용 하 여 처리를 다시 시작 하려는 _타임 스탬프_ 를 기반으로 [새 검사점 저장소를 만들고](event-processor-balance-partition-load.md#checkpointing) 초기 파티션 위치를 제공 합니다.

외부로 전환 하는 이벤트 허브의 검사점 저장소에 대 한 액세스 권한이 있는 경우에는 위에서 설명한 [전파 된 메타 데이터](#service-assigned-metadata) 를 사용 하 여 이미 처리 된 이벤트를 건너뛰고 마지막으로 중단 한 위치에서 정확 하 게 다시 시작 하는 데 도움이 됩니다.

## <a name="merge"></a>병합

병합 패턴에는 하나의 대상을 가리키는 하나 이상의 복제 태스크가 있습니다. 일반 생산자와 동시에 동일한 대상에 이벤트를 보낼 수도 있습니다.

이러한 패턴의 변형은 다음과 같습니다.

- 두 개 이상의 복제 함수는 서로 다른 원본에서 이벤트를 동시에 가져오고 동일한 대상으로 전송 합니다.
- 원본에서 이벤트를 가져오는 하나 이상의 복제 함수는 생산자에서 직접 사용 됩니다.
- 이전 패턴 이지만 두 개 이상의 Event Hubs 간에 미러링된 경우에는 이벤트가 생성 되는 위치에 관계 없이 동일한 스트림을 포함 하는 Event Hubs을 생성 합니다.

처음 두 가지 패턴 변형은 trivial 이며 일반 복제 작업과는 다릅니다.

마지막 시나리오에서는 이미 복제 된 이벤트를 다시 복제 하는 것을 제외 해야 합니다. 이 기법은 [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) 샘플에 설명 되어 있습니다.

## <a name="editor"></a>편집기

편집기 패턴은 [복제](#replication) 패턴을 기반으로 하지만 메시지를 전달 하기 전에 수정 합니다. 

이러한 수정의 예는 다음과 같습니다.

- **_트랜스 코딩_** -이벤트 콘텐츠 ("body" 또는 "페이로드" 라고도 함)가 _Apache Avro_ 형식 또는 일부 독점 serialization 형식을 사용 하 여 인코딩된 원본에서 도착 하는 경우 그러나 대상을 소유 하는 시스템의 경우 콘텐츠를 _JSON_ 으로 인코딩할 수 있습니다. 복제 작업에서는 먼저 _Apache Avro_ 의 페이로드를 메모리 내 개체 그래프로 deserialize 한 다음 전달 되는 이벤트에 대 한 _JSON_ 형식으로 해당 그래프를 직렬화 합니다. 코드 변환에는 **콘텐츠 압축** 및 압축 풀기 작업도 포함 됩니다.
- **_변환_** -구조화 된 데이터를 포함 하는 이벤트는 다운스트림 소비자가 보다 쉽게 사용할 수 있도록 해당 데이터를 변경 해야 할 수 있습니다. 이렇게 하려면 중첩 된 구조를 평면화 하거나, 불필요 한 데이터 요소를 정리 하거나, 지정 된 스키마에 정확히 맞도록 페이로드를 변형 하는 등의 작업이 필요할 수 있습니다.
- **_일괄 처리_** -이벤트를 원본에서 일괄 처리 (단일 전송의 여러 이벤트)로 수신할 수 있지만 대상으로 한 번만 전달 하거나 그 반대의 경우도 가능 합니다. 따라서 태스크는 단일 입력 이벤트 전송에 따라 여러 이벤트를 전달 하거나 함께 전송 되는 이벤트 집합을 집계할 수 있습니다.
- **_유효성 검사_** -외부 원본의 이벤트 데이터를 전달 하기 전에 규칙 집합을 준수 하는지 여부를 확인 해야 하는 경우가 종종 있습니다. 규칙은 스키마 또는 코드를 사용 하 여 나타낼 수 있습니다. 규정을 준수 하지 않는 것으로 확인 된 이벤트는 로그에 표시 된 문제를 사용 하 여 삭제 될 수도 있고,이를 추가로 처리 하기 위해 특수 대상 대상으로 전달할 수도 있습니다.
- **_보강_** -일부 원본에서 들어오는 이벤트 데이터는 대상 시스템에서 사용할 수 있도록 추가 컨텍스트가 있는 보강 필요할 수 있습니다. 이러한 작업에는 참조 데이터를 조회 하 고 해당 데이터를 이벤트에 포함 하거나 복제 작업에 알려진 원본에 대 한 정보를 추가 하거나 이벤트에는 포함 하지 않을 수 있습니다.
- **_필터링_** -원본에서 도착 하는 일부 이벤트는 일부 규칙에 따라 대상에서 보안상 이유로 수 있습니다. 필터는 규칙에 대해 이벤트를 테스트 하 고 이벤트가 규칙과 일치 하지 않는 경우 이벤트를 삭제 합니다. 특정 기준을 관찰 하 고 동일한 값을 가진 후속 이벤트를 삭제 하 여 중복 이벤트를 필터링 하는 것은 필터링의 일종입니다.
- **_암호화_** -복제 작업은 원본에서 도착 한 콘텐츠 및/또는 암호화 된 콘텐츠를 대상으로 해독 하 고, 이벤트에 전달 된 서명을 기준으로 콘텐츠 및 메타 데이터의 무결성을 확인 하거나, 이러한 서명을 연결 해야 할 수 있습니다.
- **_증명_** -복제 작업은 특정 채널이 나 특정 시간에 이벤트를 수신 입증 하는 이벤트에 디지털 서명으로 보호할 수 있는 메타 데이터를 연결할 수 있습니다.
- 연결 **_-복제_** 태스크가 스트림의 무결성을 보호 하 고 누락 이벤트를 검색할 수 있도록 이벤트 스트림에 서명을 적용할 수 있습니다.

변환, 일괄 처리 및 보강 패턴은 일반적으로 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 작업으로 구현 하는 것이 가장 좋습니다. 

이러한 모든 패턴은 Azure Functions를 사용 하 여 구현할 수 있습니다. 이러한 패턴은 이벤트를 얻기 위해 [Event Hubs 트리거와](../azure-functions/functions-bindings-event-hubs-trigger.md) 이벤트를 전달 하기 위한 [이벤트 허브 출력 바인딩을](../azure-functions/functions-bindings-event-hubs-output.md) 사용 합니다.

## <a name="routing"></a>라우팅

라우팅 패턴은 [복제](#replication) 패턴을 기반으로 하지만 원본 및 대상 하나를 포함 하는 대신 복제 작업에 여러 대상이 있습니다. 여기에는 c #에서 다음과 같이 표시 됩니다.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

라우팅 함수는 메시지 메타 데이터 및/또는 메시지 페이로드를 고려 하 고 보낼 사용 가능한 대상 중 하나를 선택 합니다.

Azure Stream Analytics에서는 여러 출력을 정의한 다음 출력 마다 쿼리를 실행 하는 것과 동일한 결과를 달성할 수 있습니다.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>로그 프로젝션

로그 프로젝션 패턴은 이벤트 스트림을 데이터베이스의 레코드가 되는 인덱싱된 데이터베이스로 평면화 합니다. 일반적으로 이벤트는 동일한 컬렉션 또는 테이블에 추가 되 고, 이벤트 허브 파티션 키는 레코드를 고유 하 게 만드는 기본 키의 파티가 됩니다.

로그 프로젝션은 이벤트 데이터의 시계열 historian 또는 압축 된 보기를 생성 하 여 각 파티션 키에 대 한 최신 이벤트만 보존 합니다. 대상 데이터베이스의 모양은 궁극적으로 사용자와 응용 프로그램의 요구 사항에 달려 있습니다. 이 패턴을 "이벤트 소싱"이 라고도 합니다.

> [!TIP]
> Azure Stream Analytics에서 [Azure SQL Database](../stream-analytics/sql-database-output.md) 및 [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) 에 대 한 로그 프로젝션을 쉽게 만들 수 있으며,이 옵션을 선호 해야 합니다.

다음 Azure 함수는 Azure CosmosDB collection에 압축 된 이벤트 허브의 콘텐츠를 프로젝션 합니다.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Functions의 이벤트 복제기 응용 프로그램][1]
- [Event Hubs 간 이벤트 복제][2]
- [Azure Service Bus에 이벤트 복제][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
