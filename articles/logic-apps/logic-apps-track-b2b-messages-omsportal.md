---
title: "Operations Management Suite 포털에서 B2B 메시지 추적 - Azure | Microsoft Docs"
description: "Operations Management Suite 포털에서 B2B 메시지를 추적하는 방법"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: d1c61ba220b4334f053428a23e620e8004fc60f7
ms.contentlocale: ko-kr
ms.lasthandoff: 06/09/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Operations Management Suite 포털에서 B2B 메시지 추적

![Logic Apps B2B 기호](./media/logic-apps-track-b2b-messages-omsportal/logic-apps-b2b-symbol.png)

B2B 통신에서는 실행되는 두 비즈니스 프로세스 또는 응용 프로그램 간에 메시지 교환이 진행됩니다. Operations Management Suite 포털에서 다음과 같은 웹 기반 추적 기능을 사용하여 메시지가 제대로 처리되는지 여부를 확인합니다.

* 메시지 수 및 상태
* 승인 상태
* 승인된 메시지 상호 연결
* 실패에 대한 자세한 오류 설명
* 검색 기능

## <a name="prerequisites"></a>필수 조건
* Azure 계정. [무료 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* 통합 계정. [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 만들고 로깅을 설정할 수 있습니다. 로깅을 설정하려면 [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)을 참조하세요.
* 논리 앱. [논리 앱](../logic-apps/logic-apps-create-a-logic-app.md)을 만들고 로깅을 설정할 수 있습니다. 로깅을 설정하려면 [Azure 진단 및 경고](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)를 참조하세요.

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Operations Management Suite 포털에 Logic Apps B2B 솔루션 추가

1. Azure Portal에서 **추가 서비스**를 선택하고, Log Analytics를 검색한 다음 **Log Analytics**를 선택합니다.   
![log analytics 검색](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. **Log Analytics**를 선택합니다.  
![Log Analytics 선택](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. **OMS 포털**을 선택합니다. Operations Management Suite 포털 홈 페이지가 표시됩니다.   
![Operations Management Suite 포털 찾아보기](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. **솔루션 갤러리**를 선택합니다.    
![솔루션 갤러리 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. **Logic Apps B2B**를 선택합니다.     
![Logic Apps B2B 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. **추가**를 선택하여 **Logic Apps B2B**를 홈 페이지에 추가합니다.  
![추가 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **Logic Apps B2B 메시지**가 홈 페이지에 나타납니다.   
![홈 페이지 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Operations Management Suite 포털에서 데이터 추적

1. 메시지를 처리한 후에 업데이트된 메시지 수가 표시됩니다.   
![업데이트된 메시지](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. 홈 페이지에서 **Logic Apps B2B 메시지**를 선택하여 AS2 및 X12 메시지 상태를 봅니다.  데이터는 1일을 기반으로 합니다.
![논리 앱 B2B 메시지 선택](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. 상태별로 AS2 또는 X12 또는 EDIFACT 메시지를 선택하여 메시지 목록으로 이동합니다. 다음 스크린 샷에서는 AS2 메시지 상태를 보여 줍니다. AS2 및 X12 메시지 상태 속성 설명이 나중에 "메시지 목록 속성 설명"에 표시됩니다.  
![AS2 메시지 상태 선택](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

4. AS2 또는 X12 또는 EDIFACT 메시지 목록에서 행을 선택하여 로그 검색으로 이동합니다.  로그 검색은 동일한 실행 ID를 갖는 모든 작업을 나열합니다.
![메시지 상태 선택](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>메시지 목록 속성 설명

#### <a name="as2-message-list-property-descriptions"></a>AS2 메시지 목록 속성 설명

| 속성 | 설명 |
| --- | --- |
| 보낸 사람 | 수신 설정에 구성된 게스트 파트너 또는 AS2 규약의 송신 설정에 구성된 호스트 파트너입니다. |
| 받는 사람 | 수신 설정에 구성된 호스트 파트너 또는 AS2 규약의 송신 설정에 구성된 게스트 파트너입니다. |
| 논리 앱 | AS2 작업이 구성된 Logic App. |
| 가동 상태 | AS2 메시지 상태 <br>성공 = 양호한 AS2 메시지를 받거나 보내고, MDN을 구성하지 않았습니다. <br>성공 = 양호한 AS2 메시지를 받거나 보내고, MDN을 구성하고, MDN을 받거나 보냈습니다. <br>실패 = 잘못된 AS2 메시지를 받고 MDN을 구성하지 않았습니다. <br>보류 중 = 양호한 AS2 메시지를 받거나 보내고, MDN을 구성하고 MDN을 기다리고 있습니다. |
| Ack | MDN 메시지 상태 <br>수락됨 = 양의 MDN을 받거나 보냈습니다. <br>보류 중 = MDN을 받거나 보낼 때까지 대기 중입니다. <br>거부됨 = 음의 MDN을 받거나 보냈습니다. <br>필요하지 않음 = 규약에 MDN이 구성되지 않습니다. |
| 방향 | AS2 메시지 방향. |
| 상관관계 ID | Logic App 내의 모든 트리거와 작업을 상호 연결하는 ID. |
| 메시지 ID |  AS2 메시지 헤더의 AS2 메시지 ID. |
| Timestamp | AS2 작업이 메시지를 처리하는 시간. |

#### <a name="x12-message-list-property-descriptions"></a>X12 메시지 목록 속성 설명

| 속성 | 설명 |
| --- | --- |
| 보낸 사람 | 수신 설정에 구성된 게스트 파트너 또는 X12 규약의 송신 설정에 구성된 호스트 파트너입니다. |
| 받는 사람 | 수신 설정에 구성된 호스트 파트너 또는 X12 규약의 송신 설정에 구성된 게스트 파트너입니다. |
| 논리 앱 | AS2 작업이 구성된 Logic App. |
| 가동 상태 | X12 메시지 상태 <br>성공 = 양호한 X12 메시지를 받거나 보내고, 기능 Ack를 구성하지 않았습니다. <br>성공 = 양호한 X12 메시지를 받거나 보내고, 기능 Ack를 구성하고 받거나, 기능 Ack를 보냈습니다. <br>실패 = 잘못된 X12 메시지를 받거나 보냈습니다. <br>보류 중 = 양호한 X12 메시지를 받거나 보내고, 기능 Ack를 구성하고 기능 Ack를 기다리고 있습니다. |
| Ack | 기능 Ack(997) 상태 <br>수락됨 = 양의 기능 Ack를 받거나 보냈습니다. <br>거부됨 = 음의 기능 Ack를 받거나 보냈습니다. <br>보류 중 = 기능 Ack를 기다렸지만 받지 못했습니다. <br>보류 중 = 기능 Ack를 생성했지만 파트너에게 보낼 수 없습니다. <br>필요하지 않음 = 기능 Ack가 구성되지 않습니다. |
| 방향 | X12 메시지 방향. |
| 상관관계 ID | Logic App 내의 모든 트리거와 작업을 상호 연결하는 ID. |
| 메시지 유형 |  EDI X12 메시지 유형. |
| ICN | X12 메시지의 교환 제어 번호. |
| TSCN | X12 메시지의 트랜잭션 집합 컨트롤 번호. |
| Timestamp | X12 작업이 메시지를 처리하는 시간. |


#### <a name="edifact-message-list-property-descriptions"></a>EDIFACT 메시지 목록 속성 설명

| 속성 | 설명 |
| --- | --- |
| 보낸 사람 | 수신 설정에 구성된 게스트 파트너 또는 EDIFACT 규약의 송신 설정에 구성된 호스트 파트너입니다. |
| 받는 사람 | 수신 설정에 구성된 호스트 파트너 또는 EDIFACT 규약의 송신 설정에 구성된 게스트 파트너입니다. |
| 논리 앱 | AS2 작업이 구성된 Logic App. |
| 가동 상태 | EDIFACT 메시지 상태 <br>성공 = 양호한 X12 메시지를 받거나 보내고, 기능 Ack를 구성하지 않았습니다. <br>성공 = 양호한 X12 메시지를 받거나 보내고, 기능 Ack를 구성하고 받거나, 기능 Ack를 보냈습니다. <br>실패 = 잘못된 X12 메시지를 받거나 보냈습니다. <br>보류 중 = 양호한 X12 메시지를 받거나 보내고, 기능 Ack를 구성하고 기능 Ack를 기다리고 있습니다. |
| Ack | 기능 Ack(997) 상태 <br>수락됨 = 양의 기능 Ack를 받거나 보냈습니다. <br>거부됨 = 음의 기능 Ack를 받거나 보냈습니다. <br>보류 중 = 기능 Ack를 기다렸지만 받지 못했습니다. <br>보류 중 = 기능 Ack를 생성했지만 파트너에게 보낼 수 없습니다. <br>필요하지 않음 = 기능 Ack가 구성되지 않습니다. |
| 방향 | EDIFACT 메시지 방향 |
| 상관관계 ID | Logic App 내의 모든 트리거와 작업을 상호 연결하는 ID. |
| 메시지 유형 |  EDIFACT 메시지 형식 |
| ICN | EDIFACT 메시지의 교환 제어 번호 |
| TSCN | EDIFACT 메시지의 트랜잭션 집합 컨트롤 번호 |
| 타임 스탬프 | EDIFACT 작업이 메시지를 처리하는 시간 |


## <a name="queries-in-the-operations-management-suite-portal"></a>Operations Management Suite 포털의 쿼리

검색 페이지에서 쿼리를 만들 수 있습니다. 검색할 때 패싯 컨트롤을 사용하여 결과를 필터링할 수 있습니다.

### <a name="create-a-query"></a>쿼리 만들기

1. 로그 검색에서 쿼리를 작성하고 **저장**을 선택합니다. **검색 저장**이 표시됩니다. 쿼리를 작성하려면 [쿼리를 사용하여 Operations Management Suite 포털에서 B2B 메시지 추적](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)을 참조하세요.
![홈 페이지 선택](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. **검색 저장**에서 **이름** 및 **범주**를 추가하한 다음 **저장**을 선택합니다.   
![홈 페이지 선택](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. 쿼리를 보려면 **즐겨찾기**를 선택합니다.    
![홈 페이지 선택](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![홈 페이지를 선택합니다.](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>저장된 쿼리 사용

* 로그 검색에서 **즐겨찾기**를 선택하여 저장된 쿼리를 봅니다.  쿼리 결과를 보려면 쿼리를 선택합니다.
![홈 페이지 선택](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>다음 단계
[사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 추적 스키마](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[AS2 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

