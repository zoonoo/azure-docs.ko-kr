---
title: 연결 문제 해결-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs의 연결 문제 해결에 대 한 정보를 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 15c93873a25e70b0f9a88fc5ea621b90d58e7581
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322382"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>연결 문제 해결-Azure Event Hubs
클라이언트 응용 프로그램에서 이벤트 허브에 연결할 수 없는 다양 한 이유가 있습니다. 발생 하는 연결 문제는 영구적 이거나 일시적일 수 있습니다. 문제가 모든 시간 (영구)에 발생 하는 경우 연결 문자열, 조직의 방화벽 설정, IP 방화벽 설정, 네트워크 보안 설정 (서비스 끝점, 개인 끝점 등) 등을 확인할 수 있습니다. 일시적인 문제의 경우 최신 버전의 SDK로 업그레이드 하 고, 삭제 된 패킷을 확인 하는 명령을 실행 하 고, 네트워크 추적을 가져오면 문제 해결에 도움이 될 수 있습니다. 

이 문서에서는 Azure Event Hubs의 연결 문제 해결에 대 한 팁을 제공 합니다. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>영구 연결 문제 해결
응용 프로그램에서 이벤트 허브에 연결할 수 없는 경우이 섹션의 단계에 따라 문제를 해결 합니다. 

### <a name="check-if-there-is-a-service-outage"></a>서비스 중단이 있는지 확인 합니다.
Azure [서비스 상태 사이트](https://azure.microsoft.com/status/)에서 azure Event Hubs 서비스 중단을 확인 하세요.

### <a name="verify-the-connection-string"></a>연결 문자열 확인 
사용 중인 연결 문자열이 올바른지 확인 합니다. Azure Portal, CLI 또는 PowerShell을 사용 하 여 연결 문자열을 가져오려면 [연결 문자열 가져오기](event-hubs-get-connection-string.md) 를 참조 하세요. 

Kafka 클라이언트의 경우 producer.config 또는 consumer.config 파일이 제대로 구성 되어 있는지 확인 합니다. 자세한 내용은 [Event Hubs Kafka으로 메시지 보내기 및 받기](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)를 참조 하세요.

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>Event Hubs와 통신 하는 데 필요한 포트가 조직의 방화벽에 의해 차단 되었는지 확인 합니다.
Azure Event Hubs와 통신 하는 데 사용 되는 포트가 조직의 방화벽에서 차단 되지 않는지 확인 합니다. Azure Event Hubs와 통신 하기 위해 열어야 하는 아웃 바운드 포트는 다음 표를 참조 하세요. 

| 프로토콜 | 포트 | 세부 정보 | 
| -------- | ----- | ------- | 
| AMQP | 5671 및 5672 | [AMQP 프로토콜 가이드](../service-bus-messaging/service-bus-amqp-protocol-guide.md)를 참조하세요. | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | [Kafka 애플리케이션에서 Event Hubs 사용](event-hubs-for-kafka-ecosystem-overview.md)을 참조하세요.

다음은 5671 포트가 차단 되었는지 여부를 확인 하는 샘플 명령입니다.

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

Linux에서:

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>회사 방화벽에서 IP 주소가 허용 되는지 확인 합니다.
Azure를 사용 하는 경우 회사 방화벽이 나 프록시에서 특정 IP 주소 범위 또는 Url을 사용 하 여 사용 하거나 사용 하려는 모든 Azure 서비스에 액세스 해야 하는 경우가 있습니다. Event Hubs에서 사용 하는 IP 주소에서 트래픽이 허용 되는지 확인 합니다. Azure Event Hubs에서 사용 하는 IP 주소에 대해서는 [AZURE IP 범위 및 서비스 태그-공용 클라우드](https://www.microsoft.com/download/details.aspx?id=56519) 및 [서비스 태그-EventHub](network-security.md#service-tags)를 참조 하세요.

또한 네임 스페이스에 대 한 IP 주소가 허용 되는지 확인 합니다. 연결에 사용할 올바른 IP 주소를 찾으려면 다음 단계를 수행 합니다.

1. 명령 프롬프트에서 다음 명령을 실행합니다. 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer`에서 반환된 IP 주소를 적어 둡니다. 다른 클러스터로 네임스페이스를 복원하는 경우에만 변경될 수 있습니다.

네임스페이스에 대해 영역 중복성을 사용하는 경우 몇 가지 추가 단계를 수행해야 합니다. 

1. 먼저 네임스페이스에서 nslookup을 실행합니다.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **신뢰할 수 없는 응답** 섹션에서 다음 형식 중 하나로 표시되는 이름을 적어 둡니다. 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 접미사 s1, s2 및 s 3이 포함 된 각 항목에 대해 nslookup을 실행 하 여 세 개의 가용성 영역에서 실행 되는 세 인스턴스의 IP 주소를 가져옵니다. 

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>응용 프로그램이 vnet의 특정 서브넷에서 실행 되 고 있어야 하는지 확인 합니다.
네임 스페이스에 대 한 액세스 권한이 있는 가상 네트워크 서브넷에서 응용 프로그램이 실행 되 고 있는지 확인 합니다. 그렇지 않은 경우 네임 스페이스에 대 한 액세스 권한이 있는 서브넷에서 응용 프로그램을 실행 하거나 응용 프로그램이 실행 되 고 있는 컴퓨터의 IP 주소를 [ip 방화벽](event-hubs-ip-filtering.md)에 추가 합니다. 

이벤트 허브 네임 스페이스에 대 한 가상 네트워크 서비스 끝점을 만들 때 네임 스페이스는 서비스 끝점에 바인딩된 서브넷의 트래픽만 허용 합니다. 이 동작에 대 한 예외는 없습니다. IP 방화벽의 특정 IP 주소를 추가 하 여 이벤트 허브 공용 끝점에 대 한 액세스를 사용할 수 있습니다. 자세한 내용은 [네트워크 서비스 끝점](event-hubs-service-endpoints.md)을 참조 하세요.

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>네임 스페이스에 대 한 IP 방화벽 설정을 확인 합니다.
응용 프로그램이 실행 되 고 있는 컴퓨터의 IP 주소가 IP 방화벽에 의해 차단 되지 않았는지 확인 합니다.  

기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 Event Hubs 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

IP 방화벽 규칙은 Event Hubs 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Event Hubs 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

자세한 내용은 [Azure Event Hubs 네임 스페이스에 대 한 IP 방화벽 규칙 구성](event-hubs-ip-filtering.md)을 참조 하세요. IP 필터링, 가상 네트워크 또는 인증서 체인 문제가 있는지 확인 하려면 [네트워크 관련 문제 해결](#troubleshoot-network-related-issues)을 참조 하세요.

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>IP 방화벽에 의해 차단 되는 IP 주소 찾기
[진단 로그 사용](event-hubs-diagnostic-logs.md#enable-diagnostic-logs)의 지침에 따라 [Event Hubs 가상 네트워크 연결 이벤트](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) 에 대 한 진단 로그를 사용 하도록 설정 합니다. 거부 된 연결에 대 한 IP 주소가 표시 됩니다.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>전용 엔드포인트를 사용 하 여 네임 스페이스에 액세스할 수 있는지 확인 합니다.
Event Hubs 네임 스페이스가 개인 끝점을 통해서만 액세스할 수 있도록 구성 된 경우 클라이언트 응용 프로그램이 개인 끝점을 통해 네임 스페이스에 액세스 하 고 있는지 확인 합니다. 

[Azure 개인 링크 서비스](../private-link/private-link-overview.md) 를 사용 하면 가상 네트워크의 **개인 끝점** 을 통해 azure Event Hubs에 액세스할 수 있습니다. 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [개인 끝점 구성](private-link-service.md)을 참조 하세요. 

### <a name="troubleshoot-network-related-issues"></a>네트워크 관련 문제 해결
Event Hubs와 관련 된 네트워크 관련 문제를 해결 하려면 다음 단계를 수행 합니다. 

또는 [wget](https://www.gnu.org/software/wget/) 으로 이동 `https://<yournamespacename>.servicebus.windows.net/` 합니다. IP 필터링 또는 가상 네트워크 또는 인증서 체인 문제가 있는지 여부를 확인 하는 데 도움이 됩니다 (Java SDK를 사용 하는 경우 가장 일반적으로).

**성공적인 메시지**의 예:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

오류 **메시지**의 예는 다음과 같습니다.

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>일시적인 연결 문제 해결
간헐적 연결 문제가 발생 하는 경우 문제 해결 팁을 보려면 다음 섹션을 참조 하세요. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>최신 버전의 클라이언트 SDK 사용
일부 일시적인 연결 문제는 사용 하는 것 보다 이후 버전의 SDK에서 수정 되었을 수 있습니다. 응용 프로그램에서 최신 버전의 클라이언트 Sdk를 사용 하 고 있는지 확인 합니다. Sdk는 새로운/업데이트 된 기능 및 버그 수정으로 지속적으로 개선 되므로 항상 최신 패키지를 사용 하 여 테스트 해야 합니다. 수정 된 문제 및 추가/업데이트 된 기능에 대 한 릴리스 정보를 확인 합니다. 

클라이언트 Sdk에 대 한 자세한 내용은 [Azure Event Hubs-클라이언트 sdk](sdks.md) 문서를 참조 하세요. 

### <a name="run-the-command-to-check-dropped-packets"></a>명령을 실행 하 여 삭제 된 패킷을 확인 합니다.
간헐적 연결 문제가 있는 경우 다음 명령을 실행 하 여 삭제 된 패킷이 있는지 확인 합니다. 이 명령은 서비스와 1 초 마다 25 가지 TCP 연결을 설정 하려고 시도 합니다. 그런 다음 성공/실패 횟수를 확인 하 고 TCP 연결 대기 시간도 확인할 수 있습니다. 이 `psping` 도구는 [여기](/sysinternals/downloads/psping)에서 다운로드할 수 있습니다.

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
, 등의 다른 도구를 사용 하는 경우에는 동일한 명령을 사용할 수 있습니다 `tnc` `ping` . 

이전 단계에서 [Wireshark](https://www.wireshark.org/)와 같은 도구를 사용 하 여 도움을 주지 않고 분석 하는 경우 네트워크 추적을 가져옵니다. 필요한 경우 [Microsoft 지원](https://support.microsoft.com/) 에 문의 하세요. 

### <a name="service-upgradesrestarts"></a>서비스 업그레이드/다시 시작
백 엔드 서비스 업그레이드 및 다시 시작으로 인해 일시적인 연결 문제가 발생할 수 있습니다. 오류가 발생 하면 다음과 같은 증상이 표시 될 수 있습니다. 

- 들어오는 메시지/요청이 삭제 될 수 있습니다.
- 로그 파일에는 오류 메시지가 포함 될 수 있습니다.
- 몇 초 동안 응용 프로그램의 서비스에서 연결을 끊을 수 있습니다.
- 요청이 일시적으로 제한 될 수 있습니다.

응용 프로그램 코드에서 SDK를 활용 하는 경우 다시 시도 정책은 이미 기본 제공 되 고 활성 상태입니다. 응용 프로그램/워크플로에 상당한 영향 없이 응용 프로그램이 다시 연결 됩니다. 그렇지 않으면 몇 분 후에 서비스에 대 한 연결을 다시 시도 하 여 문제가 계속 발생 하는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* [인증 및 권한 부여 문제 해결](troubleshoot-authentication-authorization.md)
