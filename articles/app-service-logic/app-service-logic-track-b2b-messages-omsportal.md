---
title: "OMS 포털에서 B2B 메시지 추적 | Microsoft Docs"
description: "OMS 포털에서 B2B 메시지를 추적하는 방법"
author: padmavc
manager: erikre
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
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: b3f564e32105708ddbd9027240c897fdd8ae2ac6
ms.openlocfilehash: 673b128c628a13c4af6c73c1a2d82953aadfd45a


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>OMS 포털에서 B2B 메시지 추적
B2B 통신에서는 실행되는 두 비즈니스 프로세스 또는 응용 프로그램 간에 메시지 교환이 진행됩니다. OMS 포털의 B2B 메시지 추적은 메시지가 올바르게 처리되는지 확인할 수 있는 풍부한, 웹 기반 추적 기능을 제공합니다.  다음을 추적할 수 있습니다.

* 메시지의 수 및 상태
* 승인 상태
* 메시지와 승인 상호 연결
* 실패에 대한 자세한 오류 설명
* 검색 기능

## <a name="prerequisites"></a>필수 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* 통합 계정. [통합 계정](app-service-logic-enterprise-integration-create-integration-account.md)을 만들고 로깅을 사용하도록 설정할 수 있습니다. [여기](app-service-logic-monitor-b2b-message.md)에서 단계를 찾을 수 있습니다.
* 논리 앱. [논리 앱](app-service-logic-create-a-logic-app.md)을 만들고 로깅을 사용하도록 설정할 수 있습니다. [여기](app-service-logic-monitor-your-logic-apps.md)에서 단계를 찾을 수 있습니다.

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>OMS 포털에 논리 앱 B2B 솔루션 추가
1. Portal에서 **추가 서비스**를 선택하고 **Log Analytics**를 검색한 후 **Log Analytics**을 선택합니다. 
![Log Analytics 검색](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. **Log Analytics**를 선택합니다. 
![Log Analytics 선택](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. **OMS 포털**을 선택하고 OMS 포털 홈페이지를 엽니다. ![OMS 포털 찾아보기](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. **솔루션 갤러리**를 선택합니다.    
![솔루션 갤러리 선택](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. **논리 앱 B2B**
를 선택합니다. ![논리 앱 B2B 선택](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. **추가**를 클릭하여 **논리 앱 B2B 메시지**를 홈페이지에 추가합니다. ![추가 선택](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. 홈페이지로 이동하여 **논리 앱 B2B 메시지**를 확인합니다. 
![홈페이지 선택](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

8. 메시지 프로세스를 게시합니다. 홈페이지는 메시지 수로 업데이트됩니다. ![홈페이지 선택](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

9. 홈페이지에서 **논리 앱 B2B 메시지**를 선택하면 AS2 및 X12 메시지 상태로 이동됩니다.  데이터는 지난 1일을 기반으로 합니다.
![논리 앱 B2B 메시지 선택](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

10. 상태별로 AS2 또는 X12 메시지를 선택하면 메시지 목록으로 이동됩니다. ![AS2 메시지 상태 선택](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![X12 메시지 상태 선택](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

11. AS2 또는 X12 메시지 목록에서 행을 선택하면 로그 검색으로 이동됩니다.  로그 검색을 수행하면 동일한 **실행 ID**를 갖는 모든 작업이 나열됩니다. 
![메시지 상태 선택](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)


## <a name="next-steps"></a>다음 단계
[사용자 지정 추적 스키마](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 추적 스키마](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 추적 스키마](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


