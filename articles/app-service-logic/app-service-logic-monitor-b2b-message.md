---
title: "B2B 메시지 모니터링 | Microsoft Docs"
description: "통합 계정을 모니터링하는 방법"
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
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>B2B 메시지 모니터링
B2B 통신에서는 실행되는 두 비즈니스 프로세스 또는 응용 프로그램 간에 메시지 교환이 진행됩니다. 이러한 관계는 비즈니스 프로세스 간 계약을 정의합니다. 통신이 설정되면 통신이 예상대로 작동되는지 모니터링할 수 있는 방법이 있어야 합니다.  메시지 추적은 B2B 프로토콜인 AS2, X12 및 EDIFACT에 대해 구현되었습니다.  다양한 세부 정보 및 디버깅을 위해서는 진단을 사용하도록 통합 계정을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* 통합 계정. [통합 계정](app-service-logic-enterprise-integration-create-integration-account.md)을 만들 수 있습니다.
* 논리 앱. [논리 앱](app-service-logic-create-a-logic-app.md)을 만들고 로깅을 사용하도록 설정할 수 있습니다. [여기](app-service-logic-monitor-your-logic-apps.md)에서 단계를 찾을 수 있습니다.

## <a name="enable-logging-for-an-integration-account"></a>통합 계정에 대한 로깅 사용
**Azure Portal** 또는 **모니터**에서 통합 계정에 대한 로깅을 사용하도록 설정할 수 있습니다.

### <a name="enable-logging-with-azure-portal"></a>Azure Portal에서 로깅을 사용하도록 설정

1. **통합 계정**을 선택하고 **진단 로그** 
![통합 계정 선택](./media/app-service-logic-monitor-integration-account/Pic5.png)을 선택합니다.  

2. **구독** 및 **리소스 그룹**을 선택하고 리소스 유형에서 **통합 계정**을 선택한 후 리소스 드롭다운에서 **통합 계정**을 선택하여 진단을 사용하도록 설정합니다.  **진단 사용**을 클릭하여 선택한 통합 계정에 대해 진단을 사용하도록 설정합니다.               
![통합 계정 선택](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. 상태 **ON**     을 선택합니다.  
![진단 사용](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. **Log Analytics로 보내기**를 선택하고 데이터를 내보내도록 Log Analytics를 구성합니다.               
![진단 사용](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>모니터를 통해 로깅을 사용하도록 설정

1. **모니터**를 선택하고 **진단 로그** 를 클릭합니다.  
![모니터 선택](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. **구독** 및 **리소스 그룹**을 선택하고 리소스 유형에서 **통합 계정**을 선택한 후 리소스 드롭다운에서 **통합 계정**을 선택하여 진단을 사용하도록 설정합니다.  **진단 사용**을 클릭하여 선택한 통합 계정에 대해 진단을 사용하도록 설정합니다.               
![통합 계정 선택](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. 상태 **ON**     을 선택합니다.  
![진단 사용](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. **Log Analytics로 보내기**를 선택하고 데이터를 내보내도록 Log Analytics를 구성합니다.![진단 사용](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>솔루션 확장
**Log Analytics** 외에, 통합 계정과 Event Hub 및 저장소 계정에 대한 [논리 앱](./app-service-logic-monitor-your-logic-apps.md)을 구성할 수 있습니다.         
![Azure 진단 설정](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

Event Hub 또는 저장소에서 이 원격 분석을 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) 및 [Power BI](https://powerbi.com)와 같은 다른 서비스에 사용하여 통합 워크플로의 실시간 모니터링이 가능합니다.

## <a name="supported-tracking-schema"></a>지원되는 추적 스키마
다음과 같은 추적 스키마 형식이 지원됩니다.  모든 형식은 사용자 지정 형식을 제외하고 고정된 스키마를 갖습니다.

* [사용자 지정 추적 스키마](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [AS2 추적 스키마](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 추적 스키마](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>다음 단계
[OMS 포털에서 B2B 메시지 추적](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


