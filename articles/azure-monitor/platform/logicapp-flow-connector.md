---
title: Azure 논리 앱및 전원 자동화를 사용하여 Azure 모니터 로그 사용
description: Azure 논리 앱 및 전원 자동 자동화를 사용하여 Azure 모니터 커넥터를 사용하여 반복 가능한 프로세스를 신속하게 자동화하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480014"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>논리 앱 및 흐름에 대한 Azure 모니터 로그 커넥터
[Azure 논리 앱](/azure/logic-apps/) 및 [전원 자동화를](https://ms.flow.microsoft.com) 사용하면 다양한 서비스에 대해 수백 개의 작업을 사용하여 자동화된 워크플로를 만들 수 있습니다. Azure 모니터 로그 커넥터를 사용하면 로그 분석 작업 영역 또는 Azure 모니터의 응용 프로그램 인사이트 응용 프로그램에서 데이터를 검색하는 워크플로를 빌드할 수 있습니다. 이 문서에서는 커넥터에 포함된 작업에 대해 설명하고 이 데이터를 사용하여 워크플로를 빌드하는 연습을 제공합니다.

예를 들어 Office 365의 전자 메일 알림에서 Azure Monitor 로그 데이터를 사용하거나 Azure DevOps에서 버그를 만들거나 Slack 메시지를 게시하는 논리 앱을 만들 수 있습니다.  메일이나 트윗이 수신될 때와 같이 연결된 서비스의 일부 작업 또는 단순 일정에서 워크플로를 트리거할 수 있습니다. 

## <a name="actions"></a>동작
다음 표는 Azure 모니터 로그 커넥터에 포함된 작업에 대해 설명합니다. 둘 다 로그 분석 작업 영역 또는 응용 프로그램 인사이트 응용 프로그램에 대해 로그 쿼리를 실행할 수 있습니다. 차이점은 데이터가 반환되는 방식에 있습니다.

> [!NOTE]
> Azure 모니터 로그 커넥터는 [Azure Log Analytics 커넥터와](https://docs.microsoft.com/connectors/azureloganalytics/) [Azure 응용 프로그램 인사이트 커넥터를](https://docs.microsoft.com/connectors/applicationinsights/)대체합니다. 이 커넥터는 다른 커넥터와 동일한 기능을 제공하며 Log Analytics 작업 영역 또는 응용 프로그램 인사이트 응용 프로그램에 대해 쿼리를 실행하는 데 선호되는 방법입니다.


| 작업 | 설명 |
|:---|:---|
| [쿼리 및 목록 결과 실행](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | 각 행을 고유한 개체로 반환합니다. 워크플로의 나머지 부분에서 각 행을 개별적으로 작업하려는 경우 이 작업을 사용합니다. 작업은 일반적으로 [각 활동 For](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)다음에 수행됩니다. |
| [쿼리 실행 및 결과 시각화](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | 결과 집합의 모든 행을 형식이 지정된 단일 개체로 반환합니다. 메일로 결과를 보내는 것과 같이 워크플로의 나머지 부분에서 설정된 결과를 함께 사용하려는 경우 이 작업을 사용합니다.  |

## <a name="walkthroughs"></a>연습
다음 자습서에서는 Azure 논리 앱에서 Azure 모니터 커넥터의 사용을 보여 줍니다. Power Automate를 사용하여 이러한 동일한 예제를 수행할 수 있으며, 유일한 차이점은 초기 워크플로를 만들고 완료되면 실행하는 방법입니다. 워크플로와 작업의 구성은 둘 다 동일합니다. 시작하려면 [Power 자동화의 템플릿에서 흐름 만들기를](https://docs.microsoft.com/power-automate/get-started-logic-template) 참조하십시오.


### <a name="create-a-logic-app"></a>논리 앱 만들기

Azure 포털에서 **논리 앱으로** 이동하여 **추가 를**클릭합니다. 새 논리 앱을 저장한 다음 고유한 이름을 지정하려면 **구독,** **리소스 그룹**및 **지역을** 선택합니다. **로그 분석** 설정을 설정하여 Azure Monitor 로그 설정에 설명된 대로 런타임 데이터 및 이벤트에 대한 정보를 수집하고 [Azure Logic Apps에 대한 진단 데이터를 수집할](../../logic-apps/monitor-logic-apps-log-analytics.md)수 있습니다. 이 설정은 Azure 모니터 로그 커넥터를 사용하기 위해 필요하지 않습니다.

![논리 앱 만들기](media/logicapp-flow-connector/create-logic-app.png)


**검토 + 만들기를** 클릭한 다음 **을 만듭니다.** 배포가 완료되면 **리소스로 이동을** 클릭하여 **논리 앱 디자이너를 엽니다.**

### <a name="create-a-trigger-for-the-logic-app"></a>논리 앱에 대한 트리거 만들기
**공통 트리거로 시작에서** **되풀이를 선택합니다.** 이렇게 하면 정기적으로 자동으로 실행되는 논리 앱이 만들어집니다. 작업의 **빈도** 상자에서 **일** 및 **간격** 상자에서 **1을** 입력하여 하루에 한 번 워크플로를 실행합니다.

![되풀이 작업](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>연습: 메일 시각화된 결과
다음 자습서에서는 Azure Monitor 로그 쿼리의 결과를 전자 메일로 보내는 논리 앱을 만드는 방법을 보여 주며 있습니다. 

### <a name="add-azure-monitor-logs-action"></a>Azure 모니터 로그 작업 추가
**+ 새 단계를** 클릭하여 되풀이 작업 후에 실행되는 작업을 추가합니다. **작업 선택에서** **Azure 모니터를** 입력한 다음 **Azure 모니터 로그를 선택합니다.**

![Azure 모니터 로그 작업](media/logicapp-flow-connector/select-azure-monitor-connector.png)

**Azure Log Analytics - 쿼리 실행 및 결과 시각화**를 클릭합니다.

![쿼리 실행 및 결과 작업 시각화](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure 모니터 로그 작업 추가

로그 분석 작업 영역에 대한 **구독** 및 **리소스 그룹을** 선택합니다. **리소스 유형에** 대한 *로그 분석 작업 영역을* 선택한 다음 **리소스 이름**에서 작업 영역의 이름을 선택합니다.

다음 로그 쿼리를 **쿼리** 창에 추가합니다.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

차트 유형에 대한 **시간 범위** 및 **HTML 테이블에** 대한 *쿼리에서 설정을* **선택합니다.**
   
![쿼리 실행 및 결과 작업 시각화](media/logicapp-flow-connector/run-query-visualize-action.png)

메일은 현재 연결과 연결된 계정으로 전송됩니다. **연결 변경**을 클릭하여 다른 계정을 지정할 수 있습니다.

### <a name="add-email-action"></a>이메일 작업 추가

**+ 새 단계를**클릭한 다음 + 작업 **추가를 클릭합니다.** **작업 선택에서** **Outlook을** 입력한 다음 **Office 365 Outlook을**선택합니다.

![Outlook 커넥터 선택](media/logicapp-flow-connector/select-outlook-connector.png)

**전자 메일 보내기(V2)를**선택합니다.

![Office 365 Outlook 선택 창](media/logicapp-flow-connector/select-mail-action.png)

**Body** 상자의 아무 곳이나 클릭하여 논리 앱의 이전 작업의 값으로 열리는 **동적 콘텐츠** 창을 엽니다. **로그** 분석 작업에서 쿼리 의 결과인 자세히 보기를 선택한 다음 **본문을** 선택합니다.

![본문 선택](media/logicapp-flow-connector/select-body.png)

**받는 사람** 창에서 수신자의 이메일 주소 및 **제목**에서 전자 메일에 대한 제목을 지정합니다. 

![메일 작업](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>논리 앱 저장 및 테스트
**저장을** 클릭한 다음 **실행하여** 논리 앱의 테스트 실행을 수행합니다.

![저장 및 실행](media/logicapp-flow-connector/save-run.png)


논리 앱이 완료되면 지정한 받는 사람의 메일을 확인합니다.  다음과 유사한 본문이 있는 메일을 받아야 합니다.

![샘플 메일](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)에 대해 자세히 알아봅니다.
- [로직 앱에](/azure/logic-apps/) 대해 자세히 알아보기
- [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.

