---
title: 커넥터 및 BizTalk API 앱 정의
description: API 앱, 커넥터 및 BizTalk API 앱에 대해 알아봅니다.
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia

---
# <a name="what-are-connectors-and-biztalk-api-apps"></a>커넥터 및 BizTalk API 앱 정의
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

*커넥터* 는 연결에 중점을 두는 API 앱 유형입니다. 다른 API 앱과 마찬가지로 커넥터는 웹앱, 모바일 앱 및 논리 앱에서 사용됩니다. 커넥터는 기존 서비스에 쉽게 연결할 수 있게 해주며 인증 관리에 도움이 되고 모니터링, 분석 등을 제공합니다.

모든 개발자가 고유한 API 앱을 만들고 비공개로 배포할 수 있습니다. 나중에 개발자는 사용자 지정해서 만든 API 앱을 마켓플레이스를 통해 공유하고 수익화할 수 있습니다. 

![API 앱 마켓플레이스](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

개발자가 솔루션을 신속하게 빌드할 수 있도록 Azure 팀은 여러 일반적인 시나리오를 충족하는 많은 커넥터를 마켓플레이스에 추가했습니다. 뿐만 아니라 도달 범위를 복잡한 고급 통합 시나리오까지 확장하기 위해 많은 프리미엄 및 BizTalk 기능도 제공됩니다.

다양한 서비스 "계층"을 사용할 수 있습니다. 모든 계층에는 전체 기능을 포함하여 모든 커넥터 및 API 앱이 포함되어 있습니다.  

[앱 서비스 가격](https://azure.microsoft.com/pricing/details/app-service/) 에서는 서비스 계층을 설명하고 이러한 계층에 포함된 항목도 나열합니다. 다음 섹션에서는 BizTalk API 앱 및 커넥터의 다양한 범주를 설명합니다.

## <a name="hybrid-connectors"></a>하이브리드 커넥터
하이브리드 커넥터는 도달률을 [DB2](app-service-logic-connector-db2.md), [Informix](app-service-logic-connector-informix.md) 및 WebSphere MQ에 대한 연결이 있는 엔터프라이즈까지 확장합니다. 

추가 리소스: [B2B 커넥터 및 API 앱](app-service-logic-b2b-connectors.md)  
[B2B 프로세스 만들기](app-service-logic-create-a-b2b-process.md)  
[거래 업체 규약 만들기](app-service-logic-create-a-trading-partner-agreement.md)  
[B2B 메시지 추적](app-service-logic-track-b2b-messages.md)  

## <a name="rules"></a>규칙
비즈니스 규칙은 비즈니스 프로세스를 제어하는 정책 및 의사 결정을 캡슐화합니다. 일반적으로 규칙은 동적이며 비즈니스 계획, 규정 및 기타 여러 이유를 비롯한 다양한 이유로 시간이 지남에 따라 변경됩니다. [BizTalk 규칙](app-service-logic-use-biztalk-rules.md) 을 사용하면 응용 프로그램 코드에서 이러한 정책을 분리하여 변경 프로세스를 간단하고 빠르게 만들 수 있습니다.

## <a name="connector-and-api-app-list"></a>커넥터 및 API 앱 목록
표준 커넥터, BizTalk EAI, 프리미엄 커넥터 등을 포함하여 각 범주에 포함된 커넥터 및 API 앱의 전체 목록은 [커넥터 및 API 앱 목록](app-service-logic-connectors-list.md) 을 참조하세요.

<!--HONumber=Oct16_HO2-->


