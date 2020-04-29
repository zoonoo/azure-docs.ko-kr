---
title: Azure Logic Apps 및 전원 자동화를 사용 하 여 Azure Monitor 로그 사용
description: Azure Monitor 커넥터를 사용 하 여 반복 가능한 프로세스를 신속 하 게 자동화 하기 위해 Azure Logic Apps 및 파워 자동화를 사용 하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480014"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Logic Apps 및 흐름에 대 한 Azure Monitor Logs 커넥터
[Azure Logic Apps](/azure/logic-apps/) 및 [파워 자동화](https://ms.flow.microsoft.com) 를 사용 하면 다양 한 서비스에 대해 수백 개의 작업을 사용 하 여 자동화 된 워크플로를 만들 수 있습니다. Azure Monitor Logs 커넥터를 사용 하 여 Azure Monitor의 Application Insights 응용 프로그램 또는 Log Analytics 작업 영역에서 데이터를 검색 하는 워크플로를 만들 수 있습니다. 이 문서에서는 커넥터에 포함 된 작업에 대해 설명 하 고이 데이터를 사용 하 여 워크플로를 작성 하는 연습을 제공 합니다.

예를 들어, Office 365의 전자 메일 알림에서 Azure Monitor 로그 데이터를 사용 하 여 Azure DevOps에서 버그를 만들거나 여유 시간 메시지를 게시 하는 논리 앱을 만들 수 있습니다.  메일이나 트윗이 수신될 때와 같이 연결된 서비스의 일부 작업 또는 단순 일정에서 워크플로를 트리거할 수 있습니다. 

## <a name="actions"></a>작업
다음 표에서는 Azure Monitor Logs 커넥터에 포함 된 작업에 대해 설명 합니다. 둘 다 Log Analytics 작업 영역 또는 Application Insights 응용 프로그램에 대해 로그 쿼리를 실행할 수 있습니다. 차이점은 데이터가 반환 되는 방식에 있습니다.

> [!NOTE]
> Azure Monitor Logs 커넥터는 [Azure Log Analytics 커넥터](https://docs.microsoft.com/connectors/azureloganalytics/) 및 [Azure 애플리케이션 Insights 커넥터](https://docs.microsoft.com/connectors/applicationinsights/)를 대체 합니다. 이 커넥터는 다른 사용자와 동일한 기능을 제공 하며, Log Analytics 작업 영역 또는 Application Insights 응용 프로그램에 대해 쿼리를 실행 하는 기본 방법입니다.


| 작업 | Description |
|:---|:---|
| [쿼리를 실행 하 고 결과를 나열 합니다.](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | 각 행을 자체 개체로 반환 합니다. 워크플로의 나머지 부분에서 각 행에 대해 개별적으로 작업 하려는 경우이 작업을 사용 합니다. 작업은 일반적으로 [각 작업에 대 한](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)입니다. |
| [쿼리 실행 및 결과 시각화](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 결과 집합의 모든 행을 형식이 지정 된 단일 개체로 반환 합니다. 워크플로의 나머지 부분에서 결과 집합을 함께 사용 하려는 경우이 작업을 사용 합니다 (예: 결과를 메일로 보내기).  |

## <a name="walkthroughs"></a>연습
다음 자습서에서는 Azure Logic Apps에서 Azure Monitor 커넥터를 사용 하는 방법을 보여 줍니다. 전원 자동화를 사용 하 여 이와 동일한 예제를 수행할 수 있습니다. 초기 워크플로를 만들고 완료 될 때 실행 하는 방법만 다릅니다. 워크플로 및 작업 구성은 둘 다에서 동일 합니다. 시작 하려면 [Power 자동화에서 템플릿에서 흐름 만들기](https://docs.microsoft.com/power-automate/get-started-logic-template) 를 참조 하세요.


### <a name="create-a-logic-app"></a>논리 앱 만들기

Azure Portal **Logic Apps** 로 이동 하 고 **추가**를 클릭 합니다. 새 논리 앱을 저장할 **구독**, **리소스 그룹**및 **지역을** 선택 하 고 고유한 이름을 지정 합니다. [Azure Monitor 로그 설정 및 Azure Logic Apps에 대 한 진단 데이터 수집](../../logic-apps/monitor-logic-apps-log-analytics.md)에 설명 된 대로 런타임 데이터 및 이벤트에 대 한 정보를 수집 하는 **Log Analytics** 설정을 켤 수 있습니다. Azure Monitor Logs 커넥터를 사용 하는 경우이 설정이 필요 하지 않습니다.

![논리 앱 만들기](media/logicapp-flow-connector/create-logic-app.png)


**검토 + 만들기** 및 **만들기**를 차례로 클릭 합니다. 배포가 완료 되 면 **리소스로 이동** 을 클릭 하 여 **Logic Apps 디자이너**를 엽니다.

### <a name="create-a-trigger-for-the-logic-app"></a>논리 앱에 대 한 트리거 만들기
**일반적인 트리거로 시작**에서 **되풀이**를 선택 합니다. 이렇게 하면 일정 한 간격으로 자동으로 실행 되는 논리 앱이 만들어집니다. 작업의 **빈도** 상자에서 **일** 을 선택 하 고 **간격** 상자에 **1** 을 입력 하 여 하루에 한 번 워크플로를 실행 합니다.

![되풀이 작업](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>연습: 메일 시각화 결과
다음 자습서에서는 Azure Monitor 로그 쿼리 결과를 전자 메일로 전송 하는 논리 앱을 만드는 방법을 보여 줍니다. 

### <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 추가 작업
**+ 새 단계** 를 클릭 하 여 되풀이 작업 후 실행 되는 작업을 추가 합니다. **작업 선택**아래에서 **azure monitor** 를 입력 하 고 **Azure Monitor 로그**를 선택 합니다.

![Azure Monitor 로그 작업](media/logicapp-flow-connector/select-azure-monitor-connector.png)

**Azure Log Analytics - 쿼리 실행 및 결과 시각화**를 클릭합니다.

![쿼리 실행 및 결과 시각화 동작](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 추가 작업

Log Analytics 작업 영역에 대 한 **구독** 및 **리소스 그룹** 을 선택 합니다. **리소스 종류** 에 대 한 *Log Analytics 작업 영역* 을 선택 하 고 **리소스 이름**아래에서 작업 영역 이름을 선택 합니다.

다음 로그 쿼리를 **쿼리** 창에 추가합니다.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

**차트 종류**에 대 한 **시간 범위** 및 **HTML 테이블** 에 대해 *쿼리에서 설정* 을 선택 합니다.
   
![쿼리 실행 및 결과 시각화 동작](media/logicapp-flow-connector/run-query-visualize-action.png)

현재 연결과 관련 된 계정이 메일을 보냅니다. **연결 변경**을 클릭 하 여 다른 계정을 지정할 수 있습니다.

### <a name="add-email-action"></a>전자 메일 작업 추가

**+ 새 단계**를 클릭 한 다음 **+ 작업 추가**를 클릭 합니다. **작업 선택**에서 **outlook** 을 입력 한 다음 **Office 365 outlook**을 선택 합니다.

![Outlook 커넥터 선택](media/logicapp-flow-connector/select-outlook-connector.png)

**전자 메일 보내기 (V2)** 를 선택 합니다.

![Office 365 Outlook 선택 창](media/logicapp-flow-connector/select-mail-action.png)

[ **본문** ] 상자에서 아무 곳 이나 클릭 하 여 논리 앱에서 이전 작업의 값이 포함 된 **동적 콘텐츠** 창을 엽니다. **자세히 보기** 를 선택 하 고 Log Analytics 작업에서 쿼리의 결과를 나타내는 **본문** 을 선택 합니다.

![본문 선택](media/logicapp-flow-connector/select-body.png)

**받는 사람** 창에서 수신자의 이메일 주소 및 **제목**에서 전자 메일에 대한 제목을 지정합니다. 

![메일 작업](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>논리 앱 저장 및 테스트
**저장** 을 클릭 하 고 **실행** 을 클릭 하 여 논리 앱의 테스트 실행을 수행 합니다.

![저장 및 실행](media/logicapp-flow-connector/save-run.png)


논리 앱이 완료 되 면 지정 된 받는 사람의 메일을 확인 합니다.  다음과 유사한 본문이 있는 메일을 받아야 합니다.

![샘플 메일](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)에 대해 자세히 알아봅니다.
- [Logic Apps](/azure/logic-apps/) 에 대 한 자세한 정보
- [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.

