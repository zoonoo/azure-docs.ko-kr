---
title: "Logic Apps B2B 통합 계정 재해 복구 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps B2B 재해 복구"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Logic Apps B2B 지역 간 재해 복구
B2B 워크로드에는 주문, 송장 등 금전 거래가 포함됩니다.  기업은 재해 중에도 파트너와 계약한 비즈니스 수준 SLA를 충족할 수 있도록 신속하게 복구하는 것이 매우 중요합니다.  이 토픽에서는 B2B 워크로드에 대한 비즈니스 연속성 계획을 작성하는 방법을 보여 줍니다.  포함된 내용

* 보조 지역에 통합 계정 만들기
* 주 지역에서 보조 지역으로 연결 설정 
* 재해 중 보조 지역으로 장애 조치
* 재해 처리 후 주 지역으로 복귀

## <a name="create-an-integration-account-in-secondary-region"></a>보조 지역에 통합 계정 만들기
1. 보조 지역을 선택하고 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 만듭니다.  

2. 실행 상태를 보조 지역 통합 계정으로 복제해야 하는 필수 메시지 흐름에 대한 파트너, 스키마 및 계약을 추가합니다.

    > [!Tip]
    > 모든 지역에서 통합 계정 아티팩트 명명 규칙의 일관성 유지 
    > 
    > 

3. 보조 지역에도 모든 주 지역 리소스를 배포하는 것이 좋습니다(예: SQL Azure 또는 DocumentDB 데이터베이스, 또는 메시지에 사용되는 Service Bus/이벤트 허브의 경우 APIM, Logic Apps).  

## <a name="establish-a-connection-from-primary-to-secondary"></a>주 지역에서 보조 지역으로 연결 설정 
주 지역에서 실행 상태를 끌어오려면 보조 지역에 논리 앱을 만듭니다.  논리 앱에 **트리거** 및 **작업**이 있어야 합니다.  트리거는 주 지역 통합 계정에 연결해야 하고 작업은 보조 지역 통합 계정에 연결해야 합니다.  시간 간격에 따라 트리거는 주 지역 실행 상태 테이블을 폴링하고 새 레코드를 가져오며(있는 경우), 작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다. 따라서 주 지역에서 보조 지역으로 증분 런타임 상태를 가져올 수 있습니다.

Logic Apps 통합 계정의 비즈니스 연속성은 B2B 프로토콜 X12, AS2 및 EDIFACT에 따라 지원하도록 설계되었습니다.  자세한 단계를 알아보려면 해당 링크를 선택하세요.

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT(출시 예정)

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>재해 중 보조 지역으로 장애 조치
재해 중 주 지역을 비즈니스 연속성에 사용할 수 없는 경우 트래픽을 보조 지역으로 전송합니다. 보조 지역은 파트너와 맺은 계약에 따라 RPO/RTO(복구 시간/지점 목표)를 충족하도록 신속하게 비즈니스 기능을 복구하는 데 도움이 됩니다.  또한 한 지역에서 다른 지역으로 장애 조치(Failover)하는 데 필요한 노력을 최소화합니다. 

주 지역에서 보조 지역으로 컨트롤 번호를 복사하는 동안 예상되는 대기 시간이 있습니다.  재해 중에 중복되는 생성된 컨트롤 번호를 파트너에게 보내지 않도록 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)을 사용하여 **보조 지역 계약**의 컨트롤 번호를 높이는 것이 좋습니다.

## <a name="fall-back-to-primary-region-post-disaster-event"></a>재해 처리 후 주 지역으로 복귀
주 지역을 다시 사용할 수 있게 되면 아래 단계에 따라 주 지역으로 복귀     
* **보조 지역**의 파트너로부터 메시지 수신 중지   
* [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery)을 사용하여 모든 **주 지역 계약**에 대해 생성된 컨트롤 번호 높이기   
* 보조 지역에서 주 지역으로 트래픽 전송   
* 보조 지역에 생성된 논리 앱이 주 지역의 실행 상태를 가져오도록 설정되었는지 확인    

## <a name="x12"></a>X12 
EDI X12 문서의 비즈니스 연속성은 컨트롤 번호를 기반으로 설계   
* 파트너에게 받은 컨트롤 번호(인바운드 메시지)  
* 생성되어 파트너에게 전송되는 컨트롤 번호(아웃바운드 메시지)  
    
    > [!Tip]
    > [X12 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/)을 사용하여 Logic Apps를 만들 수도 있습니다.  템플릿을 사용하려면 주 통합 계정 및 보조 통합 계정을 만들어야 합니다.  템플릿은 2개의 Logic Apps을 만드는 데 도움이 됩니다.(하나는 받은 컨트롤 번호용이고 다른 하나는 생성된 컨트롤 번호용).  각 트리거 및 작업은 Logic Apps에 생성되며, 트리거는 주 통합 계정에, 작업은 보조 통합 계정에 연결됩니다.
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>파트너에게 받은 컨트롤 번호
1. 보조 지역에 [논리 앱](../logic-apps/logic-apps-create-a-logic-app.md)을 만듭니다.   

2. **X12**를 검색하고 **X12 - 받은 컨트롤 번호가 수정되었을 때**  를 선택합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. 트리거를 선택하면 통합 계정에 대한 연결을 설정하라는 메시지가 나타납니다. 트리거는 주 지역 통합 계정에 연결해야 합니다.  연결 이름을 입력하고, 목록에서 **주 지역 통합 계정**을 선택하고, 만들기를 클릭합니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. 컨트롤 번호 동기화를 시작하는 DateTime은 선택 사항입니다.  빈도는 **일**, **시간**, **분** 또는 **초** 간격으로 설정할 수 있습니다.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. **새 단계**를 클릭하고 **작업 추가**  를 클릭합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. **X12**를 검색하고 **X12 - 받은 컨트롤 번호 추가 또는 업데이트** 를 선택합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. 작업은 보조 통합 계정에 연결해야 합니다.  **연결 변경**을 선택하면 **새 연결 추가**에 사용 가능한 통합 계정이 나열됩니다.  연결 이름을 입력하고, 목록에서 **보조 지역 통합 계정**을 선택하고, 만들기를 클릭합니다.     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. 동적 콘텐츠를 선택하고 논리 앱을 저장합니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. 시간 간격에 따라 트리거는 주 지역 받은 컨트롤 번호 테이블을 폴링하고 새 레코드를 가져오며, 작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다.  업데이트가 없는 경우 트리거 상태가 건너뜀으로 표시됩니다.
    
    > [!Tip]
    > 규약 수신 설정에 중복 확인을 사용하면 받은 컨트롤 번호 런타임 상태가 유지되며 일정한 간격으로 트리거를 실행하는 데 도움이 됩니다.
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>생성되어 파트너에게 전송되는 컨트롤 번호
1. 보조 지역에 [논리 앱](../logic-apps/logic-apps-create-a-logic-app.md)을 만듭니다.  

2. **X12**를 검색하고 **X12 - 생성된 컨트롤 번호가 수정된 경우** 를 선택합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. 트리거를 선택하면 통합 계정에 대한 연결을 설정하라는 메시지가 나타납니다. 트리거는 주 지역 통합 계정에 연결해야 합니다.  연결 이름을 입력하고, 목록에서 **주 지역 통합 계정**을 선택하고, 만들기를 클릭합니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. 컨트롤 번호 동기화를 시작하는 DateTime은 선택 사항입니다.  빈도는 **일**, **시간**, **분** 또는 **초** 간격으로 설정할 수 있습니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. **새 단계**를 클릭하고 **작업 추가** 를 클릭합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. **X12**를 검색하고 **X12 - 생성된 컨트롤 번호 추가 또는 업데이트** 를 선택합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. 작업은 보조 통합 계정에 연결해야 합니다.  **연결 변경**을 선택하면 **새 연결 추가**에 사용 가능한 통합 계정이 나열됩니다.  연결 이름을 입력하고, 목록에서 **보조 지역 통합 계정**을 선택하고, 만들기를 클릭합니다.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. 동적 콘텐츠를 선택하고 논리 앱을 저장합니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. 시간 간격에 따라 트리거는 주 지역 받은 컨트롤 번호 테이블을 폴링하고 새 레코드를 가져오며, 작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다.  업데이트가 없는 경우 트리거 상태가 건너뜀으로 표시됩니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

시간 간격에 따라 증분 런타임 상태가 주 지역에서 보조 지역으로 복제됩니다.  재해 중 주 지역을 사용할 수 없는 경우 비즈니스 연속성을 위해 트래픽을 보조 지역으로 전송합니다. 

## <a name="as2"></a>AS2 
AS2 프로토콜을 사용하는 문서에 대한 비즈니스 연속성은 메시지 id와 MIC 값을 기반으로 설계되었습니다.

> [!Tip]
    > [AS2 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/pull/3302)을 사용하여 Logic Apps를 만들 수도 있습니다.  템플릿을 사용하려면 주 통합 계정 및 보조 통합 계정을 만들어야 합니다.  템플릿은 논리 앱을 만드는 데 도움이 되며, 트리거와 작업을 갖고 있습니다.  트리거에서 주 통합 계정으로, 그리고 작업에서 보조 통합 계정으로 연결을 만듭니다.
    > 
    >

1. 보조 지역에 [논리 앱](../logic-apps/logic-apps-create-a-logic-app.md)을 만듭니다.  

2. **AS2**를 검색하고 **AS2 - MIC 값이 생성될 때** 를 선택합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. 트리거를 선택하면 통합 계정에 대한 연결을 설정하라는 메시지가 나타납니다. 트리거는 주 지역 통합 계정에 연결해야 합니다.  연결 이름을 입력하고, 목록에서 **주 지역 통합 계정**을 선택하고, 만들기를 클릭합니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. MIC 값 동기화를 시작하는 DateTime은 선택 사항입니다.  빈도는 **일**, **시간**, **분** 또는 **초** 간격으로 설정할 수 있습니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. **새 단계**를 클릭하고 **작업 추가** 를 클릭합니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. **AS2**를 검색하고 **AS2 - MIC 추가 또는 업데이트**   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. 작업은 보조 통합 계정에 연결해야 합니다.  **연결 변경**을 선택하면 **새 연결 추가**에 사용 가능한 통합 계정이 나열됩니다.  연결 이름을 입력하고, 목록에서 **보조 지역 통합 계정**을 선택하고, 만들기를 클릭합니다.    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. 동적 콘텐츠를 선택하고 논리 앱을 저장합니다.   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. 시간 간격에 따라 트리거는 주 지역 테이블을 폴링하고 새 레코드를 가져오며, 작업은 새 레코드를 보조 지역 통합 계정에 업데이트합니다.  업데이트가 없는 경우 트리거 상태가 건너뜀으로 표시됩니다.  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

시간 간격에 따라 증분 런타임 상태가 주 지역에서 보조 지역으로 복제됩니다.  재해 중 주 지역을 사용할 수 없는 경우 비즈니스 연속성을 위해 트래픽을 보조 지역으로 전송합니다. 

## <a name="next-steps"></a>다음 단계
* [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md)에 대해 자세히 알아봅니다.   
