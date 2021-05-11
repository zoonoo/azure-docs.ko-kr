---
title: Azure SignalR Service에 대한 리소스 로그
description: Azure SignalR Service에 대한 리소스 로그를 설정하는 방법과 이를 활용하여 스스로 문제를 해결하는 방법에 대해 알아봅니다.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5650ff0e039d1e9211b8d0013726e101efdfab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572260"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Azure SignalR Service에 대한 리소스 로그

이 자습서는 Azure SignalR Service에 대한 리소스 로그, 설정 방법 및 문제 해결 방법을 설명합니다. 

## <a name="prerequisites"></a>사전 요구 사항
리소스 로그를 사용하도록 설정하려면 로그 데이터를 저장하는 위치에 있어야 합니다. 이 자습서에서는 Azure Storage와 Log Analytics를 사용합니다.

* [Azure storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) - 정책 감사, 정적 분석 또는 백업에 대한 리소스 로그를 유지합니다.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) - Azure 리소스에서 생성된 원시 로그를 분석할 수 있는 유연한 로그 검색 및 분석 도구입니다.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Azure SignalR Service에 대한 리소스 로그 설정

Azure SignalR Service에 대한 리소스 로그를 볼 수 있습니다. 해당 로그는 Azure SignalR Service 인스턴스에 대한 연결을 더욱 잘 보여 주는 보기를 제공합니다. 리소스 로그는 모든 연결에 대한 자세한 정보를 제공합니다. 예를 들면 연결의 기본 정보(사용자 ID, 연결 ID 및 전송 유형 등)와 이벤트 정보(연결, 연결 끊기 및 중단 이벤트 등)가 있습니다. 문제 식별, 연결 추적 및 분석에 리소스 로그를 사용할 수 있습니다.

### <a name="enable-resource-logs"></a>리소스 로그 사용

리소스 로그는 기본적으로 사용되지 않습니다. 리소스 로그를 활성화하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 **모니터링** 에서 **진단 설정** 을 클릭합니다.

    ![진단 설정 창 탐색](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. **진단 설정 추가** 를 클릭합니다.

    ![리소스 로그 추가](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. 원하는 보관 대상을 설정합니다. 현재 **스토리지 계정에 보관** 및 **Log Analytics에 보내기** 를 지원합니다.

1. 보관하려는 로그를 선택합니다.

    ![진단 설정 창](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. 새 진단 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 창에 로그가 나타납니다.

진단 구성에 대한 자세한 내용은 [Azure 리소스 로그 개요](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

### <a name="resource-logs-categories"></a>리소스 로그 범주

Azure SignalR Service는 한 범주에서 리소스 로그를 캡처합니다.

* **모든 로그**: Azure SignalR Service에 연결하는 연결을 추적합니다. 로그는 연결/연결 끊기, 인증 및 제한에 대한 정보를 제공합니다. 자세한 내용은 다음 단원을 참조하세요.

### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관

로그는 **진단 로그** 창에 구성된 스토리지 계정에 저장됩니다. `insights-logs-alllogs`이라는 컨테이너는 리소스 로그를 저장하기 위해 자동으로 만들어집니다. 컨테이너 내에서 로그는 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` 파일에 저장됩니다. 기본적으로 경로는 `resource ID` 및 `Date Time`으로 결합됩니다. 로그 파일은 `hour`로 분할됩니다. 따라서 분은 항상 `m=00`입니다.

모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

보관 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

**Format**

이름 | 설명
------- | -------
time | 로그 이벤트 시간
수준 | 로그 이벤트 수준
resourceId | Azure SignalR Service의 리소스 ID
위치 | Azure SignalR Service의 위치
category | 로그 이벤트 범주
operationName | 이벤트의 작업 이름
callerIpAddress | 서버/클라이언트의 IP 주소
properties | 해당 로그 이벤트와 관련된 자세한 속성입니다. 더 자세히 알아보려면 아래 속성 표를 참조하세요.

**속성 표**

이름 | Description
------- | -------
type | 로그 이벤트의 유형입니다. 현재 Azure SignalR Service에 대한 연결 정보를 제공합니다. `ConnectivityLogs` 형식만 사용할 수 있습니다.
collection | 로그 이벤트의 컬렉션입니다. 허용되는 값은 `Connection`, `Authorization` 및 `Throttling`입니다.
connectionId | 연결 ID
transportType | 연결의 전송 유형입니다. 허용된 값은 `Websockets` \| `ServerSentEvents` \| `LongPolling`입니다.
connectionType | 연결 유형입니다. 허용되는 값은 `Server` \| `Client`입니다. `Server`: 서버 쪽에서 연결, `Client`: 클라이언트 쪽에서 연결
userId | 사용자 ID
message | 로그 이벤트의 세부 메시지

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Log Analytics에 대한 보관 로그 스키마

리소스 로그를 보려면 다음 단계를 수행합니다.

1. 대상 Log Analytics에서 `Logs`를 클릭합니다.

    ![Log Analytics 메뉴 항목](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. `SignalRServiceDiagnosticLogs`를 입력하고 리소스 로그를 쿼리하는 시간 범위를 선택합니다. 고급 쿼리의 경우 [Azure Monitor에서 Log Analytics 시작](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요.

    ![Log Analytics 쿼리 로그](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

보관 로그 열에는 다음 표에 나열된 요소가 포함되어 있습니다.

이름 | Description
------- | ------- 
TimeGenerated | 로그 이벤트 시간
컬렉션 | 로그 이벤트의 컬렉션입니다. 허용되는 값은 `Connection`, `Authorization` 및 `Throttling`입니다.
OperationName | 이벤트의 작업 이름
Location | Azure SignalR Service의 위치
Level | 로그 이벤트 수준
callerIpAddress | 서버/클라이언트의 IP 주소
메시지 | 로그 이벤트의 세부 메시지
UserId | 사용자 ID
ConnectionId | 연결 ID
ConnectionType | 연결 유형입니다. 허용되는 값은 `Server` \| `Client`입니다. `Server`: 서버 쪽에서 연결, `Client`: 클라이언트 쪽에서 연결
TransportType | 연결의 전송 유형입니다. 허용된 값은 `Websockets` \| `ServerSentEvents` \| `LongPolling`입니다.

### <a name="troubleshooting-with-resource-logs"></a>리소스 로그 문제 해결

Azure SignalR Service에 대한 문제를 해결하기 위해 서버/클라이언트 쪽 로그를 사용하도록 설정하여 오류를 캡처할 수 있습니다. 현재 Azure SignalR Service는 리소스 로그를 노출하며, 서비스 측에 대해 로그를 사용하도록 설정할 수도 있습니다.

예기치 않게 연결이 늘어나거나 줄어드는 상황이 발생할 경우 리소스 로그를 활용하여 문제를 해결할 수 있습니다.

일반적인 문제는 연결의 예기치 않은 수량 변경, 연결의 연결 제한 도달 및 권한 부여 오류에 대한 경우가 많습니다. 문제를 해결하는 방법에 대한 다음 섹션을 참조하세요.

#### <a name="unexpected-connection-number-changes"></a>예기치 않은 연결 수 변경

##### <a name="unexpected-connection-dropping"></a>예기치 않은 연결 끊김

예기치 않은 연결 끊김이 발생하는 경우 먼저 서비스, 서버 및 클라이언트 쪽에서 로그를 사용하도록 설정합니다.

연결이 끊어지면 리소스 로그는 이 연결 끊김 이벤트를 기록하고, `operationName`에 `ConnectionAborted` 또는 `ConnectionEnded`가 나타납니다.

`ConnectionAborted`와 `ConnectionEnded`의 차이점은 `ConnectionEnded`가 클라이언트 또는 서버 쪽에서 트리거되는 예상한 연결 끊김이라는 것입니다. `ConnectionAborted`는 일반적으로 예기치 않은 연결 끊김 이벤트이며, `message`에 중단 이유가 제공됩니다.

중단 이유는 다음 표에 나열되어 있습니다.

이유 | 설명
------- | ------- 
연결 수가 제한에 도달 | 연결 수가 현재 가격 계층의 제한에 도달합니다. 서비스 단위를 스케일 업하는 것이 좋습니다.
애플리케이션 서버가 연결을 닫음 | 앱 서버가 중단을 트리거합니다. 예상된 중단으로 간주될 수 있습니다.
연결 ping 시간 제한 | 일반적으로 네트워크 문제로 인해 발생합니다. 인터넷에서 앱 서버 가용성을 확인하는 것이 좋습니다.
서비스 다시 로드, 다시 연결 | Azure SignalR Service를 다시 로드하고 있습니다. Azure SignalR은 자동 다시 연결을 지원합니다. 다시 연결될 때까지 또는 수동으로 Azure SignalR Service에 다시 연결될 때까지 대기할 수 있습니다.
내부 서버 일시적인 오류 | Azure SignalR Service에서 일시적인 오류가 발생하며, 자동으로 복구되어야 함
서버 연결 끊김 | 서버 연결이 알 수 없는 오류와 함께 끊깁니다. 먼저 서비스/서버/클라이언트 쪽 로그를 사용하여 자체 문제 해결을 하는 것이 좋습니다. 기본 문제(예: 네트워크 문제, 앱 서버 쪽 문제 등)가 아닌지 확인해보세요. 문제가 해결되지 않으면 Microsoft에 문의하여 도움을 받으세요. 자세한 내용은 [도움 받기](#get-help) 섹션을 참조하세요. 

##### <a name="unexpected-connection-growing"></a>예기치 않은 연결 증가

예기치 않은 연결 증가 문제를 해결하려면 먼저 추가 연결을 필터링해야 합니다. 테스트 클라이언트 연결에 고유한 테스트 사용자 ID를 추가할 수 있습니다. 그런 다음 리소스 로그를 사용하여 이를 확인합니다. 두 개 이상의 클라이언트 연결이 동일한 테스트 사용자 ID 또는 IP를 사용하는 경우 클라이언트 쪽에서 예상보다 많은 연결을 만들고 설정할 가능성이 높습니다. 클라이언트 쪽을 확인합니다.

#### <a name="authorization-failure"></a>권한 부여 실패

클라이언트 요청에 대해 인증되지 않은 401이 반환되는 경우 리소스 로그를 확인합니다. `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`가 나올 경우, 액세스 토큰의 모든 대상 그룹이 잘못된 것입니다. 로그에 제안된 유효한 대상 그룹을 사용하세요.


#### <a name="throttling"></a>제한

Azure SignalR Service에 대한 SignalR 클라이언트 연결을 설정할 수 없는 경우 리소스 로그를 확인합니다. 리소스 로그에 `Connection count reaches limit`이 나올 경우 연결 수 제한에 도달할 정도로 SignalR Service에 대한 연결을 너무 많이 설정한 것입니다. SignalR Service를 스케일 업하는 것이 좋습니다. 리소스 로그에 `Message count reaches limit`이 나올 경우 무료 계층을 사용하며 메시지 할당량을 다 사용한 것입니다. 더 많은 메시지를 보내려면 SignalR Service를 표준 계층으로 변경하여 추가 메시지를 전송하는 것이 좋습니다. 자세한 내용은 [Azure SignalR Service 가격 책정](https://azure.microsoft.com/pricing/details/signalr-service/)을 참조하세요.

### <a name="get-help"></a>도움말 보기

먼저 자체적으로 문제를 해결하는 것이 좋습니다. 대부분의 문제는 앱 서버 또는 네트워크 문제로 인해 발생합니다. [리소스 로그 문제 해결 가이드](#troubleshooting-with-resource-logs) 및 [기본 문제 해결 가이드](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md)에 따라 근본 원인을 찾으세요.
그래도 문제를 해결할 수 없는 경우 GitHub에서 문제를 열거나 Azure Portal에서 티켓을 만듭니다.
다음을 제공합니다.
1. 문제가 발생하는 시간 범위는 30분입니다.
2. Azure SignalR Service의 리소스 ID
3. 최대한 특정한 문제 세부 정보: 예를 들어 앱 서버가 메시지를 보내지 않음, 클라이언트 연결 끊김 등
4. 서버/클라이언트 쪽에서 수집된 로그 및 유용하게 사용할 수 있는 기타 자료
5. [선택 사항]재현 코드

> [!NOTE]
> GitHub에서 문제를 여는 경우 중요한 정보(예: 리소스 ID, 서버/클라이언트 로그)를 비공개로 유지하며, Microsoft 조직의 구성원에게만 보냅니다.