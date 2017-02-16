---
title: "논리 앱 예제 및 시나리오 | Microsoft Docs"
description: "일반적인 논리 앱 예제를 참조하고 일반적인 시나리오를 구현하는 방법에 대해 알아봅니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ebdac3845e3b635ea6be7de41df9b389915e5d39


---
# <a name="logic-apps-examples-and-common-scenarios"></a>논리 앱 예제 및 일반적인 시나리오
이 문서는 논리 앱을 사용하여 비즈니스 프로세스를 자동화할 수 있는 방법을 이해할 수 있도록 일반적인 시나리오 및 예제를 자세히 설명합니다. 

## <a name="custom-triggers-and-actions"></a>사용자 지정 트리거 및 동작
몇 가지 방법으로 다른 앱에서 논리 앱을 트리거할 수 있습니다. 몇 가지 일반적인 예는 다음과 같습니다.

* [사용자 지정 트리거 또는 작업 만들기](../logic-apps/logic-apps-create-api-app.md)
* [장기 실행 작업](../logic-apps/logic-apps-create-api-app.md)
* [HTTP 요청 트리거(POST)](logic-apps-http-endpoint.md)
* [Webhook 트리거 및 작업](../logic-apps/logic-apps-create-api-app.md)
* [폴링 트리거](../logic-apps/logic-apps-create-api-app.md)

### <a name="scenarios"></a>시나리오
* [요청 동기 응답](logic-apps-http-endpoint.md)
* [SMS로 요청 응답](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>오류 처리 및 로깅
* [예외 및 오류 처리](logic-apps-exception-handling.md)
* [Azure 경고 및 진단 구성](logic-apps-monitor-your-logic-apps.md)

### <a name="scenarios"></a>시나리오
* [사용 사례: 오류 및 예외 처리](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>배포 및 관리
* [자동화된 배포 만들기](../logic-apps/logic-apps-create-deploy-template.md)
* [Visual Studio의 논리 앱 빌드 및 배포](logic-apps-deploy-from-vs.md)
* [논리 앱 모니터링](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>콘텐츠 형식, 변환 및 변형
논리 앱 [워크플로 정의 언어](http://aka.ms/logicappsdocs) 에는 여러 다른 콘텐츠 형식을 변환하고 사용할 수 있도록 하는 많은 함수가 포함되어 있습니다. 또한, 엔진은 워크플로를 통해 데이터가 흐를 때 콘텐츠 형식을 보존하기 위해 할 수 있는 모든 작업을 수행합니다.

* [콘텐츠 형식 처리](../logic-apps/logic-apps-content-type.md) (예: application/json, application/xml 및 텍스트/일반)
* [워크플로 정의 작성](../logic-apps/logic-apps-author-definitions.md)
* [워크플로 정의 언어 참조](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>배치 및 반복
* [SplitOn](logic-apps-loops-and-scopes.md)
* [ForEach](logic-apps-loops-and-scopes.md)
* [Until](logic-apps-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Azure Functions와 통합
* [Azure Functions 통합](../logic-apps/logic-apps-azure-functions.md)

### <a name="scenarios"></a>시나리오
* [서비스 버스 트리거인 Azure Function](logic-apps-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, REST 및 SOAP
* [SOAP 호출](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

이 문서에 예제 및 시나리오를 추가할 예정입니다. 아래의 의견 섹션을 사용하여 여기에서 보고 싶은 예제 또는 시나리오를 알려주세요.




<!--HONumber=Jan17_HO3-->


