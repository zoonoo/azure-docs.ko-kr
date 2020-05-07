---
title: 하이브리드 연결에 대 한 진단 로그
description: 이 문서에서는 Azure Relay에 사용할 수 있는 모든 작업 및 진단 로그에 대 한 개요를 제공 합니다.
services: service-bus-messaging
author: spelluru
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: b7ac5f1da70352115bf05df1a61120f46a85ec5d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783462"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Azure Relay 하이브리드 연결에 대해 진단 로그를 사용 하도록 설정
Azure Relay 하이브리드 연결 사용을 시작 하는 경우 수신기 및 발신자가 열리고 닫히는 방법과 시기 및 하이브리드 연결 생성 되 고 메시지가 전송 되는 방법 및 시기를 모니터링 하는 것이 좋습니다. 이 문서에서는 Azure Relay 서비스에서 제공 하는 작업 및 진단 로그에 대 한 개요를 제공 합니다. 

Azure Relay에 대해 다음과 같은 두 가지 유형의 로그를 볼 수 있습니다.

- [활동 로그](../azure-monitor/platform/platform-logs-overview.md): 이러한 로그는 Azure Portal 또는 Azure Resource Manager 템플릿을 통해 네임 스페이스에 대해 수행 된 작업에 대 한 정보를 포함 합니다. 이러한 로그는 항상 사용 하도록 설정 됩니다. 예: "네임 스페이스 만들기 또는 업데이트", "하이브리드 연결 만들기 또는 업데이트" 
- [진단 로그](../azure-monitor/platform/platform-logs-overview.md): API를 사용 하거나 언어 SDK를 사용 하 여 네임 스페이스에 대해 수행 되는 작업 및 작업에서 발생 하는 모든 상황을 보다 잘 이해할 수 있도록 진단 로그를 구성할 수 있습니다.

## <a name="view-activity-logs"></a>활동 로그 보기
Azure Relay 네임 스페이스에 대 한 활동 로그를 보려면 Azure Portal의 **활동 로그** 페이지로 전환 합니다.

![Azure Relay 활동 로그](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>진단 로그 활성화

> [!NOTE]
> 진단 로그는 WCF (Windows Communication Foundation) 릴레이가 아닌 하이브리드 연결에만 사용할 수 있습니다.

진단 로그를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Relay 네임 스페이스로 이동한 후 **모니터링**아래에서 **진단 설정**을 선택 합니다.
1. **진단 설정** 페이지에서 **진단 설정 추가**를 선택 합니다.  

   !["진단 설정 추가" 링크](./media/diagnostic-logs/add-diagnostic-setting.png)

1. 다음 단계를 수행 하 여 진단 설정을 구성 합니다.
    1. **이름** 상자에 진단 설정의 이름을 입력 합니다.  
    2. 로그 유형에 대해 **HybridConnectionsEvent** 를 선택 합니다. 
    3. 진단 로그에 대해 다음 세 **대상** 중 하나를 선택 합니다.  
        1. **저장소 계정에 보관**을 선택 하는 경우 진단 로그가 저장 되는 저장소 계정을 구성 합니다.  
        2. **이벤트 허브로 스트림**을 선택 하는 경우 진단 로그를 스트리밍할 이벤트 허브를 구성 합니다.
        3. **Log Analytics 보내기를**선택 하는 경우 진단이 전송 될 Log Analytics 인스턴스를 지정 합니다.  

        ![샘플 진단 설정](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다.

새 설정은 약 10 분 후에 적용 됩니다. 로그는 구성 된 보관 대상의 **진단 로그** 창에 표시 됩니다. 진단 설정을 구성 하는 방법에 대 한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md)를 참조 하세요.


## <a name="schema-for-hybrid-connections-events"></a>하이브리드 연결에 대 한 스키마 이벤트
하이브리드 연결 이벤트 로그 JSON 문자열에는 다음 표에 나열 된 요소가 포함 됩니다.

| Name | 설명 |
| ------- | ------- |
| ResourceId | Azure Resource Manager 리소스 ID |
| ActivityId | 지정 된 작업을 식별 하는 데 사용 되는 내부 ID입니다. "TrackingId" 라고도 합니다. |
| 엔드포인트 | 릴레이 리소스의 주소 |
| OperationName | 기록 되는 하이브리드 연결 작업의 형식입니다. |
| EventTimeString | 로그 레코드의 UTC 타임 스탬프입니다. |
| 메시지 | 이벤트의 자세한 메시지입니다. |
| 범주 | 이벤트의 범주. 현재는만 `HybridConnectionsEvents`있습니다. 


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
| InvalidSasToken | 잘못 된 SAS 토큰입니다. | 
| ListenerAcceptingConnection | 수신기에서 연결을 수락 하 고 있습니다. |
| ListenerAcceptingConnectionTimeout | 연결을 수락 하는 수신기의 시간이 초과 되었습니다. |
| ListenerAcceptingHttpRequestFailed | 예외로 인해 HTTP 요청을 수락 하는 수신기가 실패 했습니다. |
| ListenerAcceptingRequestTimeout | 요청을 수락 하는 수신기의 시간이 초과 되었습니다. |  
| ListenerClosingFromExpiredToken | 보안 토큰이 만료 되어 수신기를 닫는 중입니다. | 
| ListenerRejectedConnection | 수신기가 연결을 거부 했습니다. |
| ListenerReturningHttpResponse | 수신기가 HTTP 응답을 반환 합니다. |  
| ListenerReturningHttpResponseFailed | 수신기가 오류 코드와 함께 HTTP 응답을 반환 합니다. | 
 ListenerSentHttpResponse | 릴레이 서비스가 수신기에서 HTTP 응답을 받았습니다. | 
| ListenerUnregistered | 수신기의 등록이 취소 되었습니다. | 
| ListenerUnresponsive | 응답을 반환 하는 경우 수신기가 응답 하지 않습니다. | 
| MessageSendingToListener | 메시지를 수신기로 보내고 있습니다. |
| MessageSentToListener | 메시지가 수신기에 전송 됩니다. | 
| NewListenerRegistered | 새 수신기를 등록 했습니다. |
| NewSenderRegistering | 새 발신자를 등록 하는 중입니다. | 
| ProcessingRequestFailed | 하이브리드 연결 작업을 처리 하지 못했습니다. | 
| SenderConnectionClosed | 보낸 사람 연결이 닫혔습니다. |
| SenderListenerConnectionEstablished | 발신자와 수신기가 연결을 설정 했습니다. |
| SenderSentHttpRequest | 발신자가 HTTP 요청을 보냈습니다. | 


## <a name="next-steps"></a>다음 단계

Azure Relay에 대 한 자세한 내용은 다음을 참조 하세요.

* [Azure Relay 소개](relay-what-is-it.md)
* [릴레이 하이브리드 연결 시작](relay-hybrid-connections-dotnet-get-started.md)
