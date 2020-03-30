---
title: Azure SignalR 서비스에 대한 진단 로그
description: Azure SignalR 서비스에 대한 진단 로그를 설정하는 방법과 이를 자체 문제 해결에 활용하는 방법에 대해 알아봅니다.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536737"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Azure SignalR 서비스에 대한 진단 로그

이 자습서에서는 Azure SignalR 서비스에 대한 진단 로그및 진단 로그를 설정하는 방법 및 진단 로그로 문제를 해결하는 방법을 다룹니다.

## <a name="prerequisites"></a>사전 요구 사항
진단 로그를 사용하려면 로그 데이터를 저장할 수 있는 곳이 필요합니다. 이 자습서에서는 Azure 저장소 및 로그 분석을 사용합니다.

* [Azure 저장소](../azure-monitor/platform/resource-logs-collect-storage.md) - 정책 감사, 정적 분석 또는 백업을 위한 진단 로그를 유지합니다.
* [로그 분석](../azure-monitor/platform/resource-logs-collect-workspace.md) - Azure 리소스에서 생성된 원시 로그를 분석할 수 있는 유연한 로그 검색 및 분석 도구입니다.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Azure SignalR 서비스에 대한 진단 로그 설정

Azure SignalR 서비스에 대한 진단 로그를 볼 수 있습니다. 이러한 로그는 Azure SignalR 서비스 인스턴스에 대한 연결에 대한 보다 풍부한 보기를 제공합니다. 진단 로그는 모든 연결에 대한 자세한 정보를 제공합니다. 예를 들어, 기본 정보(사용자 ID, 연결 ID 및 전송 유형 등) 및 연결의 이벤트 정보(연결, 연결 해제 및 중단 이벤트 등)입니다. 진단 로그는 문제 식별, 연결 추적 및 분석에 사용할 수 있습니다.

### <a name="enable-diagnostic-logs"></a>진단 로그 활성화

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1. Azure [포털에서](https://portal.azure.com) **모니터링**에서 **진단 설정을**클릭합니다.

    ![진단 설정으로 의한 창 탐색](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. 그런 다음 **진단 추가 설정을**클릭합니다.

    ![진단 로그 추가](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. 원하는 아카이브 대상을 설정합니다. 현재 저장소 **계정에 대한 아카이브** 및 **로그 분석으로 보내기를**지원합니다.

1. 보관할 로그를 선택합니다.

    ![진단 설정 창](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. 새 진단 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 창에 로그가 나타납니다.

진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/platform-logs-overview.md)를 참조하세요.

### <a name="diagnostic-logs-categories"></a>진단 로그 범주

Azure SignalR 서비스는 진단 로그를 한 범주에서 캡처합니다.

* **모든 로그**: Azure SignalR 서비스에 연결하는 연결을 추적합니다. 로그는 연결/연결 해제, 인증 및 제한에 대한 정보를 제공합니다. 자세한 내용은 다음 섹션을 참조하세요.

### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관

로그는 진단 로그 창에서 구성된 저장소 계정에 **저장됩니다.** 진단 로그를 저장하기 위해 명명된 `insights-logs-alllogs` 컨테이너가 자동으로 만들어집니다. 컨테이너 내부에 로그가 파일에 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`저장됩니다. 기본적으로 경로는 및 `resource ID` . `Date Time` 로그 파일은 `hour`로그 파일별로 분할됩니다. 따라서 분은 항상 `m=00`.

모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

아카이브 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함됩니다.

**형식**

이름 | 설명
------- | -------
time | 로그 이벤트 시간
level | 로그 이벤트 수준
resourceId | Azure SignalR 서비스의 리소스 ID
위치 | Azure SignalR 서비스의 위치
category | 로그 이벤트의 범주
operationName | 이벤트의 작업 이름
callerIpAddress | 서버/클라이언트의 IP 주소
properties | 이 로그 이벤트와 관련된 자세한 속성입니다. 자세한 내용은 아래 의 속성 표를 참조하십시오.

**속성 테이블**

이름 | 설명
------- | -------
type | 로그 이벤트의 유형입니다. 현재 Azure SignalR 서비스에 대한 연결에 대한 정보를 제공합니다. 유형만 `ConnectivityLogs` 사용할 수 있습니다.
collection | 로그 이벤트의 컬렉션입니다. 허용된 값은 다음과 `Connection`같습니다. `Authorization``Throttling`
connectionId | 연결의 ID
전송 유형 | 연결의 전송 유형입니다. 허용된 값은 다음과 `Websockets` \| `ServerSentEvents` \| 같습니다.`LongPolling`
connectionType | 연결 유형입니다. 허용되는 값은 `Server` \| `Client`입니다. `Server`: 서버 측에서 연결; `Client`: 클라이언트 측의 연결
userId | 사용자의 ID
message | 로그 이벤트의 자세한 메시지

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

### <a name="archive-logs-schema-for-log-analytics"></a>로그 분석을 위한 로그 스키마 보관

진단 로그를 보려면 다음 단계를 따르십시오.

1. 대상 `Logs` 로그 분석을 클릭합니다.

    ![로그 분석 메뉴 항목](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. `SignalRServiceDiagnosticLogs` 입력하고 진단 로그를 쿼리하는 시간 범위를 선택합니다. 고급 쿼리의 경우 [Azure 모니터에서 로그 분석 시작 을](../azure-monitor/log-query/get-started-portal.md) 참조하십시오.

    ![로그 분석의 쿼리 로그인](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

아카이브 로그 열에는 다음 표에 나열된 요소가 포함됩니다.

이름 | 설명
------- | ------- 
TimeGenerated | 로그 이벤트 시간
컬렉션 | 로그 이벤트의 컬렉션입니다. 허용된 값은 다음과 `Connection`같습니다. `Authorization``Throttling`
OperationName | 이벤트의 작업 이름
위치 | Azure SignalR 서비스의 위치
Level | 로그 이벤트 수준
callerIpAddress | 서버/클라이언트의 IP 주소
메시지 | 로그 이벤트의 자세한 메시지
UserId | 사용자의 ID
ConnectionId | 연결의 ID
ConnectionType | 연결 유형입니다. 허용되는 값은 `Server` \| `Client`입니다. `Server`: 서버 측에서 연결; `Client`: 클라이언트 측의 연결
TransportType | 연결의 전송 유형입니다. 허용된 값은 다음과 `Websockets` \| `ServerSentEvents` \| 같습니다.`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>진단 로그문제 해결

Azure SignalR 서비스의 문제를 해결하려면 서버/클라이언트 측 로그를 사용하여 오류를 캡처할 수 있습니다. 현재 Azure SignalR 서비스는 진단 로그를 노출하며 서비스 측에 로그를 사용하도록 설정할 수도 있습니다.

연결이 예기치 않게 증가하거나 중단될 경우 진단 로그를 활용하여 문제를 해결할 수 있습니다.

일반적인 문제는 종종 연결의 예기치 않은 수량 변경, 연결 연결 제한 및 권한 부여 실패에 관한 것입니다. 문제 해결 방법에 대한 다음 섹션을 참조하세요.

#### <a name="unexpected-connection-number-changes"></a>예기치 않은 연결 번호 변경

##### <a name="unexpected-connection-dropping"></a>예기치 않은 연결 끊김

예기치 않은 연결이 끊어지면 먼저 서비스, 서버 및 클라이언트 측면에서 로그를 사용하도록 설정합니다.

연결이 끊어지면 진단 로그에 이 연결 끊김 이벤트가 `ConnectionAborted` 기록됩니다. `ConnectionEnded` `operationName`

차이점은 `ConnectionAborted` `ConnectionEnded` 클라이언트 또는 `ConnectionEnded` 서버 측에서 트리거되는 예상 연결 해제입니다. 는 `ConnectionAborted` 일반적으로 예기치 않은 연결 삭제 이벤트이며 중단 이유가 `message`에 제공됩니다.

중단 사유는 다음 표에 나와 있습니다.

이유 | 설명
------- | ------- 
연결 수가 한계에 도달했습니다. | 연결 수는 현재 가격 계층의 한도에 도달합니다. 서비스 단위 확장 고려
응용 프로그램 서버가 연결을 닫았습니다. | 앱 서버는 낙태를 트리거합니다. 그것은 예상된 낙태로 간주될 수 있습니다
연결 핑 시간 | 일반적으로 네트워크 문제로 인해 발생합니다. 인터넷에서 앱 서버의 가용성 확인을 고려하십시오.
서비스 재장전, 다시 연결 | Azure SignalR 서비스가 다시 로드됩니다. Azure SignalR는 자동 재연결을 지원하며, 다시 연결될 때까지 기다리거나 Azure SignalR 서비스에 수동으로 다시 연결할 수 있습니다.
내부 서버 과도 오류 | Azure SignalR 서비스에서 일시적인 오류가 발생하며 자동 복구되어야 합니다.
서버 연결이 끊김됨 | 서버 연결은 알 수 없는 오류와 함께 삭제되며, 먼저 서비스/서버/클라이언트 측 로그를 통해 자체 문제 해결을 고려하십시오. 기본 문제(예: 네트워크 문제, 앱 서버 측면 문제 등)를 제외해 보십시오. 문제가 해결되지 않으면 문의하여 추가 도움을 요청하십시오. 자세한 내용은 [도움말 받기](#get-help) 섹션을 참조하세요. 

##### <a name="unexpected-connection-growing"></a>예기치 않은 연결 증가

예기치 않은 연결 증가에 대한 문제를 해결하려면 가장 먼저 해야 할 일은 추가 연결을 필터링하는 것입니다. 테스트 클라이언트 연결에 고유한 테스트 사용자 ID를 추가할 수 있습니다. 그런 다음 두 개 이상의 클라이언트 연결이 동일한 테스트 사용자 ID 또는 IP를 가지고 있는 경우 진단 로그를 사용하여 확인한 다음 클라이언트 측이 예상보다 많은 연결을 만들고 설정할 수 있습니다. 클라이언트 측을 확인합니다.

#### <a name="authorization-failure"></a>권한 부여 실패

클라이언트 요청에 대해 401 무단으로 반환된 경우 진단 로그를 확인합니다. 이 경우 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`액세스 토큰의 모든 잠재고객이 유효하지 않음을 의미합니다. 로그에 제안된 유효한 잠재고객을 사용해 보십시오.


#### <a name="throttling"></a>스로틀

Azure SignalR 서비스에 대한 SignalR 클라이언트 연결을 설정할 수 없는 경우 진단 로그를 확인합니다. 진단 로그에 발생하는 `Connection count reaches limit` 경우 연결 수 제한에 도달하는 SignalR 서비스에 대한 연결이 너무 많습니다. SignalR 서비스를 확장하는 것이 좋습니다. 진단 로그에 발생하는 `Message count reaches limit` 경우 프리 티어를 사용하고 메시지 할당량을 사용하는 것을 의미합니다. 더 많은 메시지를 보내려면 SignalR 서비스를 표준 계층으로 변경하여 추가 메시지를 보내는 것이 좋습니다. 자세한 내용은 [Azure SignalR 서비스 가격 책정](https://azure.microsoft.com/pricing/details/signalr-service/)을 참조하십시오.

### <a name="get-help"></a>도움말 보기

먼저 혼자서 문제를 해결하는 것이 좋습니다. 대부분의 문제는 앱 서버 또는 네트워크 문제로 인해 발생합니다. [진단 로그](#troubleshooting-with-diagnostic-logs) 및 기본 문제 해결 가이드를 사용하여 문제 해결 [가이드를](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) 따라 근본 원인을 찾으십시오.
그래도 문제를 해결할 수 없는 경우 GitHub에서 문제를 열거나 Azure Portal에서 티켓을 만드는 것이 좋습니다.
다음을 제공합니다.
1. 문제가 발생한 경우 약 30분 의 시간 범위
2. Azure 신호R 서비스의 리소스 ID
3. 가능한 한 구체적으로 세부 정보 발행: 예를 들어, appserver는 메시지를 보내지 않고 클라이언트 연결이 끊어지는 등의 등
4. 서버/클라이언트 측에서 수집한 로그 및 유용할 수 있는 기타 자료
5. [선택 사항] 재현 코드

> [!NOTE]
> GitHub에서 문제를 여는 경우 중요한 정보(예: 리소스 ID, 서버/클라이언트 로그)를 비공개로 유지하고 Microsoft 조직의 구성원에게만 비공개로 보냅니다.