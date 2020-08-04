---
title: 하이브리드 연결에 대한 진단 로그
description: 이 문서에서는 Azure Relay에 사용할 수 있는 모든 활동 및 진단 로그에 대한 개요를 제공합니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 980f2f7a737d3f2460c17a84c472cbf56f5eb90f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533005"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Azure Relay 하이브리드 연결에 진단 로그 사용
Azure Relay 하이브리드 연결을 사용하기 시작하면 수신기와 송신기가 열리고 닫히는 방법과 시기, 그리고 하이브리드 연결이 생성되고 메시지가 전송되는 방법을 모니터링하는 것이 좋습니다. 이 문서에서는 Azure Relay 서비스가 제공하는 활동 및 진단 로그에 대한 개요를 제공합니다. 

Azure Relay에 대한 다음 두 가지 유형의 로그를 볼 수 있습니다.

- [활동 로그](../azure-monitor/platform/platform-logs-overview.md): 이러한 로그는 Azure Portal의 네임스페이스에 대해 수행된 작업 또는 Azure Resource Manager 템플릿을 통해 수행된 작업에 대한 정보를 포함합니다. 이러한 로그는 항상 사용하도록 설정됩니다. 다음은 그 예입니다.  "네임스페이스 만들기 또는 업데이트", "하이브리드 연결 만들기 또는 업데이트" 
- [진단 로그](../azure-monitor/platform/platform-logs-overview.md): API 또는 언어 SDK를 사용하여 네임스페이스에 대해 수행된 작업 및 동작과 관련하여 발생한 모든 상황을 자세히 볼 수 있도록 진단 로그를 구성할 수 있습니다.

## <a name="view-activity-logs"></a>활동 로그 보기
Azure Relay 네임스페이스에 대한 활동 로그를 보려면 Azure Portal의 **활동 로그** 페이지로 전환합니다.

![Azure Relay - 활동 로그](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>진단 로그 활성화

> [!NOTE]
> 진단 로그는 WCF(Windows Communication Foundation) 릴레이가 아닌 하이브리드 연결에만 사용할 수 있습니다.

진단 로그를 사용하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Relay 네임스페이스로 이동한 다음, **모니터링**에서 **진단 설정**을 선택합니다.
1. **진단 설정** 페이지에서 **진단 설정 추가**를 선택합니다.  

   !["진단 설정 추가" 링크](./media/diagnostic-logs/add-diagnostic-setting.png)

1. 다음 단계를 수행하여 진단 설정을 구성합니다.
    1. **이름** 상자에 진단 설정의 이름을 입력합니다.  
    2. 로그 유형에 **HybridConnectionsEvent**를 선택합니다. 
    3. 진단 로그에 다음 세 가지 **대상** 중 하나를 선택합니다.  
        1. **스토리지 계정에 보관**을 선택하는 경우 진단 로그가 저장될 스토리지 계정을 구성합니다.  
        2. **이벤트 허브에 스트리밍**을 선택하는 경우 진단 로그를 스트리밍할 이벤트 허브를 구성합니다.
        3. **Log Analytics에 보내기**를 선택하는 경우 진단이 전송될 Log Analytics 인스턴스를 지정합니다.  

        ![샘플 진단 설정](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. 도구 모음에서 **저장**을 선택하여 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 구성된 보관 대상의 **진단 로그** 창에 로그가 표시됩니다. 진단 구성 설정에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/platform-logs-overview.md)를 참조하세요.


## <a name="schema-for-hybrid-connections-events"></a>하이브리드 연결 이벤트에 대한 스키마
하이브리드 연결 이벤트 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

| 속성 | Description |
| ------- | ------- |
| ResourceId | Azure Resource Manager 리소스 ID |
| ActivityId | 지정된 작업을 식별하는 데 사용되는 내부 ID입니다. "TrackingId"라고도 합니다. |
| 엔드포인트 | 릴레이 리소스의 주소 |
| OperationName | 로깅되는 하이브리드 연결 작업의 유형 |
| EventTimeString | 로그 레코드의 UTC 타임스탬프 |
| 메시지 | 이벤트의 자세한 메시지 |
| Category | 이벤트의 범주. 현재는 `HybridConnectionsEvents`만 있습니다. 


## <a name="sample-hybrid-connections-event"></a>샘플 하이브리드 연결 이벤트
JSON 형식의 샘플 하이브리드 연결 이벤트는 다음과 같습니다. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>진단 로그에 캡처되는 이벤트 및 작업

| 작업(Operation) | Description | 
| --------- | ----------- | 
| AuthorizationFailed | 권한 부여에 실패했습니다.|
| InvalidSasToken | 잘못된 SAS 토큰입니다. | 
| ListenerAcceptingConnection | 연결을 수락하는 수신기입니다. |
| ListenerAcceptingConnectionTimeout | 연결을 수락하는 수신기의 시간이 초과되었습니다. |
| ListenerAcceptingHttpRequestFailed | 예외로 인해 HTTP 요청을 수락하는 수신기가 실패했습니다. |
| ListenerAcceptingRequestTimeout | 요청을 수락하는 수신기의 시간이 초과되었습니다. |  
| ListenerClosingFromExpiredToken | 보안 토큰이 만료되어 수신기가 닫히는 중입니다. | 
| ListenerRejectedConnection | 수신기가 연결을 거부했습니다. |
| ListenerReturningHttpResponse | 수신기가 HTTP 응답을 반환하는 중입니다. |  
| ListenerReturningHttpResponseFailed | 수신기가 오류 코드와 함께 HTTP 응답을 반환하는 중입니다. | 
 ListenerSentHttpResponse | 릴레이 서비스가 수신기에서 HTTP 응답을 받았습니다. | 
| ListenerUnregistered | 수신기의 등록이 취소되었습니다. | 
| ListenerUnresponsive | 응답을 반환하는 경우 수신기가 응답하지 않습니다. | 
| MessageSendingToListener | 수신기로 메시지가 전송되는 중입니다. |
| MessageSentToListener | 수신기로 메시지가 전송되었습니다. | 
| NewListenerRegistered | 새 수신기가 등록되었습니다. |
| NewSenderRegistering | 새 수신기가 등록되는 중입니다. | 
| ProcessingRequestFailed | 하이브리드 연결 작업을 처리하지 못했습니다. | 
| SenderConnectionClosed | 송신기 연결이 닫혔습니다. |
| SenderListenerConnectionEstablished | 송신기와 수신기의 연결이 설정되었습니다. |
| SenderSentHttpRequest | 송신기가 HTTP 요청을 보냈습니다. | 


## <a name="next-steps"></a>다음 단계

Azure Relay에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Relay 소개](relay-what-is-it.md)
* [릴레이 하이브리드 연결 시작](relay-hybrid-connections-dotnet-get-started.md)
