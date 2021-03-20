---
title: 메시지 복제 태스크 패턴-Azure Service Bus | Microsoft Docs
description: 이 문서에서는 특정 메시지 복제 태스크 패턴을 구현 하기 위한 자세한 지침을 제공 합니다.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657503"
---
# <a name="message-replication-tasks-patterns"></a>메시지 복제 태스크 패턴

[페더레이션 개요](service-bus-federation-overview.md) 및 [복제기 함수 개요](service-bus-federation-replicator-functions.md) 에서는 복제 작업의 및 기본 요소에 대해 설명 하 고이 문서를 계속 하기 전에이에 대해 잘 알고 있는 것이 좋습니다.

이 문서에서는 개요 섹션에 강조 표시 된 몇 가지 패턴에 대 한 구현 지침을 자세히 설명 합니다. 

## <a name="replication"></a>복제 

복제 패턴은 한 큐 또는 토픽에서 다음 또는 큐 또는 토픽으로 이벤트 허브와 같은 다른 대상으로 메시지를 복사 합니다. 메시지 페이로드를 수정 하지 않고 메시지를 전달 합니다. 

이 패턴의 구현은 [Azure Service Bus 샘플에 대 한 메시지 복제](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) 에 포함 됩니다.

### <a name="sequences-and-order-preservation"></a>시퀀스 및 순서 유지

복제 모델은 원본 큐 또는 토픽 메시지의 절대 순서를 대상 큐 또는 토픽으로 유지 하는 것을 목표로 하지 않지만, 필요할 때마다 응용 프로그램에서 필요로 하는 메시지의 상대적 순서를 유지 하는 데 중점을 두고 있습니다. 응용 프로그램에서는 원본 엔터티에 대 한 세션 지원을 사용 하도록 설정 하 고 동일한 [세션 키](message-sessions.md)를 사용 하 여 관련 메시지를 그룹화 합니다.

세션 인식 미리 작성 된 복제 함수는 원본 엔터티에서 동일한 세션 id를 가진 메시지 시퀀스가 원본 시퀀스의 일괄 처리와 동일한 세션 id로 대상 큐 또는 토픽에 전송 되도록 합니다. 

### <a name="service-assigned-metadata"></a>서비스 할당 메타 데이터 

원본 큐 또는 토픽에서 얻은 메시지의 서비스 할당 메타 데이터 (원래 큐에 넣기 시간 및 시퀀스 번호)는 대상 큐 또는 토픽의 새 서비스 할당 값으로 대체 되지만, 샘플에 제공 된 기본 복제 작업을 사용 하 여 원래 값은 사용자 속성 `repl-enqueue-time` (ISO8601 string) 및에 유지 `repl-sequence` 됩니다.

이러한 속성은 문자열 형식 이며 해당 원래 속성의 문자열 형식 값을 포함 합니다.  메시지가 여러 번 전달 되는 경우에는 직접 소스의 서비스 할당 메타 데이터가 이미 존재 하는 속성에 추가 되 고 값은 세미콜론으로 구분 됩니다. 

### <a name="failover"></a>장애 조치 

재해 복구를 위해 복제를 사용 하는 경우 Service Bus 서비스의 지역 가용성 메시지 또는 네트워크 중단에 대 한 보호를 위해 이러한 모든 오류 시나리오에서는 하나의 큐 또는 토픽에서 다음 큐로 장애 조치 (failover)를 수행 하 여 생산자 및/또는 소비자에 게 보조 끝점을 사용 하도록 지시 해야 합니다.

모든 장애 조치 (failover) 시나리오에서는 네임 스페이스의 필수 요소가 구조적으로 동일 하다 고 가정 합니다. 즉, 큐와 항목의 이름이 동일 하 고 공유 액세스 서명 규칙 및/또는 역할 기반 액세스 제어 규칙이 동일한 방식으로 설정 됩니다. [네임 스페이스를 이동](move-across-regions.md) 하 고 정리 단계를 생략 하는 지침에 따라 보조 네임 스페이스를 만들고 업데이트할 수 있습니다.

생산자와 소비자가 강제로 전환 되도록 하려면 쉽게 연결 하 고 업데이트할 수 있는 위치에서 조회에 사용할 수 있는 네임 스페이스에 대 한 정보를 확인 해야 합니다. 생산자 나 소비자가 자주 발생 하거나 지속적으로 오류가 발생 하는 경우 해당 위치를 참조 하 고 구성을 조정 해야 합니다. 이 구성을 공유 하는 방법에는 여러 가지가 있지만 DNS 및 파일 공유에는 두 가지가 있습니다.

#### <a name="dns-based-failover-configuration"></a>DNS 기반 장애 조치 (failover) 구성

하나는 사용자가 제어 하는 DNS에서 DNS SRV 레코드의 정보를 유지 하 고 해당 큐 또는 토픽 끝점을 가리키는 것입니다. 메시지 허브는 끝점을 CNAME 레코드를 사용 하 여 직접 별칭 지정 하는 것을 허용 하지 않습니다. 즉, DNS를 끝점 주소에 대 한 복원 가능한 조회 메커니즘으로 사용 하 고 IP 주소 정보를 직접 확인 하지는 않습니다. 

도메인 `example.com` 및 응용 프로그램에 대 한 영역을 소유 한다고 가정 `test.example.com` 합니다. 두 개의 대체 Service Bus에 대해 이제 중첩 된 영역 두 개와 각각에 SRV 레코드를 만듭니다. 

SRV 레코드는 일반적인 규칙에 따라 접두사를 사용 하 `_azure_servicebus._amqp` 고 두 개의 끝점 레코드를 포함 합니다. 두 개의 끝점 레코드는 포트 5671에서 amqp over 443 websocket에 대 한 하나이 고, 둘 다 영역에 해당 하는 네임 스페이스의 Service Bus 끝점을 가리킵니다.

| 영역                 | SRV 레코드
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

그런 다음 응용 프로그램의 영역에서 기본 큐 또는 토픽에 해당 하는 하위 영역을 가리키는 CNAME 항목을 만듭니다.

| 레코드                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

CNAME 및 SRV 레코드를 명시적으로 쿼리할 수 있도록 허용 하는 DNS 클라이언트를 사용 하는 경우 (Java 및 .NET의 기본 제공 클라이언트는 IP 주소에 대 한 단순한 이름 확인만 허용) 원하는 끝점을 확인할 수 있습니다. 예를 들어 [DnsClient.NET](https://dnsclient.michaco.net/)를 사용 하는 경우 lookup 함수는 다음과 같습니다.

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

이 절차는 [Service Bus 지리적 DR](service-bus-geo-dr.md) 이 작동 하는 방식과 유사 하지만 사용자 고유의 컨트롤에서 완벽 하 게 작동 하 고 활성/활성 시나리오 에서도 작동 합니다.

#### <a name="file-share-based-failover-configuration"></a>파일 공유 기반 장애 조치 (failover) 구성

DNS를 사용 하 여 끝점 정보를 공유 하는 가장 간단한 방법은 기본 끝점의 이름을 일반 텍스트 파일에 넣고 중단에 대해 강력 하 고 여전히 업데이트를 허용 하는 인프라에서 파일을 제공 하는 것입니다. 

전역 가용성 및 콘텐츠 복제를 사용 하 여 항상 사용 가능한 웹 사이트 인프라를 이미 실행 한 경우 이러한 파일을 추가 하 고 스위치가 필요한 경우 파일을 다시 게시 합니다.

## <a name="merge"></a>병합

병합 패턴에는 한 대상을 가리키는 하나 이상의 복제 태스크가 있습니다. 일반 생산자와 동시에 동일한 대상으로 메시지를 보낼 수도 있습니다. 

이 패턴의 변형은 다음과 같습니다.
- 두 개 이상의 복제 함수는 서로 다른 원본에서 메시지를 동시에 가져오고 동일한 대상으로 전송 합니다.
- 원본에서 메시지를 가져오는 하나 이상의 복제 함수는 생산자에서 직접 사용 됩니다. 
- 이전 패턴 이지만 메시지가 두 개 이상의 항목 간에 미러링된 경우 메시지가 생성 되는 위치에 관계 없이 동일한 메시지가 포함 된 항목이 생성 됩니다.

처음 두 가지 패턴 변형은 trivial 이며 일반 복제 작업과는 다릅니다.

마지막 시나리오에서는 이미 복제 된 메시지를 다시 복제 하지 않도록 제외 해야 합니다. 활성/활성 샘플에서 기술에 대해 설명 하 고 설명 합니다.

## <a name="editor"></a>편집기

편집기 패턴은 [복제](#replication) 패턴을 기반으로 하지만 메시지를 전달 하기 전에 수정 합니다. 이러한 수정의 예는 다음과 같습니다.

- ***트랜스 코딩*** -메시지 콘텐츠 ("body" 또는 "페이로드" 라고도 함)가 *Apache Avro* 형식 또는 일부 독점 serialization 형식을 사용 하 여 인코딩된 원본에서 도착 하는 경우 그러나 대상을 소유 하는 시스템의 경우에는 콘텐츠를 *JSON* 으로 인코딩해야 합니다. 복제 작업에서는 먼저 *Apache Avro* 에서 메모리 내 개체 그래프로 페이로드를 Deserialize 한 다음 전달 되는 메시지의 *JSON* 형식으로 해당 그래프를 직렬화 합니다. 코드 변환에는 **콘텐츠 압축** 및 압축 풀기 작업도 포함 됩니다.
- ***변환*** -구조화 된 데이터를 포함 하는 메시지는 다운스트림 소비자가 쉽게 사용할 수 있도록 해당 데이터를 변경 해야 할 수 있습니다. 이렇게 하려면 중첩 된 구조를 평면화 하거나, 불필요 한 데이터 요소를 정리 하거나, 지정 된 스키마에 정확히 맞도록 페이로드를 변형 하는 등의 작업이 필요할 수 있습니다.
- ***일괄 처리*** -메시지를 원본에서 일괄 처리 (단일 전송의 여러 메시지)로 받을 수 있지만 대상으로 한 번만 전달 하거나 그 반대의 경우도 가능 합니다. 따라서 태스크는 단일 입력 메시지 전송을 기반으로 여러 메시지를 전달 하거나 메시지 집합을 집계 한 다음 함께 전송할 수 있습니다. 
- ***유효성 검사*** -외부 원본의 메시지 데이터를 전달 하기 전에 규칙 집합을 준수 하는지 여부를 확인 해야 하는 경우가 종종 있습니다. 규칙은 스키마 또는 코드를 사용 하 여 나타낼 수 있습니다. 규정을 준수 하지 않는 것으로 확인 된 메시지는 로그에 표시 된 문제를 사용 하 여 삭제 될 수도 있고, 추가 처리를 위해 특수 대상 대상으로 전달 될 수도 있습니다.   
- ***보강*** -일부 원본에서 가져온 메시지 데이터는 대상 시스템에서 사용할 수 있도록 추가 컨텍스트가 있는 보강 필요할 수 있습니다. 여기에는 참조 데이터를 조회 하 고 해당 데이터를 메시지에 포함 하거나 복제 작업에 알려진 원본에 대 한 정보를 추가 하거나 메시지에는 포함 하지 않을 수 있습니다. 
- ***필터링*** -원본에서 도착 하는 일부 메시지는 일부 규칙에 따라 대상에서 보안상 이유로 수 있습니다. 필터는 규칙에 대해 메시지를 테스트 하 고 메시지가 규칙과 일치 하지 않는 경우 메시지를 삭제 합니다. 특정 기준을 관찰 하 고 동일한 값을 가진 후속 메시지를 삭제 하 여 중복 메시지를 필터링 하는 것은 필터링의 일종입니다.
- ***라우팅 및 분할*** -일부 복제 작업은 두 개 이상의 대체 대상에 대해 허용할 수 있으며, 메시지의 메타 데이터 또는 내용에 따라 특정 메시지에 대해 복제 대상을 선택 하는 규칙을 정의 합니다. 특수 한 형태의 라우팅은 분할 이며,이 경우 태스크는 규칙에 따라 하나의 복제 대상에 파티션을 명시적으로 할당 합니다.
- ***암호화*** -복제 작업은 원본에서 도착 한 콘텐츠를 해독 하 고 대상에 전달 되는 콘텐츠를 암호화 하거나, 메시지에 전달 된 서명을 기준으로 콘텐츠 및 메타 데이터의 무결성을 확인 하거나 이러한 서명을 연결 해야 할 수 있습니다. 
- ***증명*** -복제 작업은 특정 채널이 나 특정 시간에 메시지가 수신 되었음을 입증 하는 메시지에 디지털 서명에 의해 보호 되는 메타 데이터를 연결할 수 있습니다.     
- 연결 ***-복제*** 태스크는 시퀀스의 무결성이 보호 되 고 누락 된 메시지를 검색할 수 있도록 메시지 시퀀스에 서명을 적용할 수 있습니다.

메시지 [허브 트리거와](../azure-functions/functions-bindings-service-bus-trigger.md) 메시지를 전달 하기 위한 [큐 또는 토픽 출력 바인딩을](../azure-functions/functions-bindings-service-bus-output.md) 사용 하 여 Azure Functions를 사용 하 여 모든 패턴을 구현할 수 있습니다. 

## <a name="routing"></a>라우팅

라우팅 패턴은 [복제](#replication) 패턴을 기반으로 하지만 원본 및 대상 하나를 포함 하는 대신 복제 작업에 여러 대상이 있습니다. 여기에는 c #에서 다음과 같이 표시 됩니다.

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

라우팅 함수는 메시지 메타 데이터 및/또는 메시지 페이로드를 고려 하 고 보낼 사용 가능한 대상 중 하나를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Functions의 메시지 복제기 응용 프로그램][1]
- [Service Bus 간에 메시지 복제][2]
- [Azure Event Hubs에 메시지 복제][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub