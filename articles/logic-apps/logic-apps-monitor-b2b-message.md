---
title: "B2B 트랜잭션의 메시지 모니터링 - Azure Logic Apps | Microsoft Docs"
description: "통합 계정에서 Logic Apps를 사용하여 프로세스와 앱 사이의 B2B 통신 중 메시지 모니터링 및 추적."
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
ms.date: 01/27/2017
ms.author: padmavc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>AS2, X12 및 EDIFACT 메시지의 로깅을 시작 또는 사용하여 성공, 오류 및 메시지 속성을 모니터링합니다.

B2B 통신에서는 실행되는 두 비즈니스 프로세스 또는 응용 프로그램 간에 메시지 교환이 진행됩니다. 이러한 관계는 비즈니스 프로세스 간 계약을 정의합니다. 통신이 연결된 후 메시지 모니터링을 설정하여 통신이 예상대로 작동하는지 확인할 수 있습니다. 다양한 세부 정보는 디버깅을 위해 통합 계정에 대한 진단을 설정할 수 있습니다.

메시지 추적은 AS2, X12 및 EDIFACT와 같은 B2B 프로토콜에 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 계정. [무료 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* 통합 계정. [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)을 만들 수 있습니다.
* 논리 앱. [논리 앱](logic-apps-create-a-logic-app.md)을 만들고 [로깅을 사용하도록 설정](logic-apps-monitor-your-logic-apps.md)할 수 있습니다.

## <a name="enable-logging-for-an-integration-account"></a>통합 계정에 대한 로깅 사용

**Azure Portal** 또는 **모니터**에서 통합 계정에 대한 로깅을 사용하도록 설정할 수 있습니다.

### <a name="enable-logging-with-azure-portal"></a>Azure Portal에서 로깅을 사용하도록 설정

1. 통합 계정을 선택한 다음 **진단 로그**를 선택합니다.

    ![통합 계정 선택](media/logic-apps-monitor-b2b-message/pic5.png)

2. **구독** 및 **리소스 그룹**을 선택합니다. **리소스 종류**에서 **통합 계정**을 선택합니다. **리소스**에서 통합 계정을 선택합니다. **진단 사용**을 클릭하여 선택한 통합 계정에 대해 진단을 사용하도록 설정합니다.

    ![통합 계정에 대한 진단 설정](media/logic-apps-monitor-b2b-message/pic2.png)

3. 상태 **ON**을 선택합니다.

    ![진단 상태 켜기](media/logic-apps-monitor-b2b-message/pic3.png)

4. **Send to Log Analytics**(Log Analytics로 보내기)를 선택하고 데이터를 내보내도록 Log Analytics를 구성합니다.

    ![진단 데이터를 로그로 보내기](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>모니터를 통해 로깅을 사용하도록 설정

0. **모니터**, **진단 로그**를 선택합니다.

    ![모니터, 진단 로그 선택](media/logic-apps-monitor-b2b-message/pic1.png)

0. **구독** 및 **리소스 그룹**을 선택합니다. **리소스 종류**에서 **통합 계정**을 선택합니다. **리소스**에서 통합 계정을 선택합니다. **진단 사용**을 클릭하여 선택한 통합 계정에 대해 진단을 사용하도록 설정합니다.

    ![통합 계정에 대한 진단 설정](media/logic-apps-monitor-b2b-message/pic2.png)

0. 상태 **ON**을 선택합니다.

    ![진단 상태 켜기](media/logic-apps-monitor-b2b-message/pic3.png) 

0. **Send to Log Analytics**(Log Analytics로 보내기)를 선택하고 데이터를 내보내도록 **Log Analytics**를 구성합니다.

    ![진단 데이터를 로그로 보내기](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>솔루션 확장

**Log Analytics** 외에, Event Hub 및 저장소 계정에 대해 통합 계정 및 [Logic Apps](./logic-apps-monitor-your-logic-apps.md)를 구성할 수 있습니다.

![Azure 진단 설정](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

Event Hub 또는 저장소에서 이 원격 분석을 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) 및 [Power BI](https://powerbi.com)와 같은 다른 서비스에 사용하여 통합 워크플로의 실시간 모니터링이 가능합니다.

## <a name="supported-tracking-schema"></a>지원되는 추적 스키마

이러한 추적 스키마 형식을 지원하며 사용자 지정 유형을 제외한 고정된 스키마가 포함됩니다.

* [사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)
* [AS2 추적 스키마](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

[OMS 포털에서 B2B 메시지 추적](logic-apps-track-b2b-messages-omsportal.md "B2B 메시지 추적")

[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")


