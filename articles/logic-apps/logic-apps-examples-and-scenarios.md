---
title: "예제 및 시나리오 - Azure Logic Apps | Microsoft Docs"
description: "일반적인 시나리오에 대한 논리 앱 예제 검토"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: .net,nodejs,java
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/14/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: dcf089d680249d0a2f9d748b315076d91c8c78e8
ms.lasthandoff: 03/30/2017


---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Azure Logic Apps 예제 및 일반적인 시나리오

Azure Logic Apps에서 다양한 패턴 및 기능에 대해 자세히 알아볼 수 있는 일반적인 예제 및 시나리오는 다음과 같습니다.

## <a name="key-scenarios-for-logic-apps"></a>논리 앱에 대한 주요 시나리요

Azure Logic Apps는 다양한 서비스에 대한 복원력 있는 오케스트레이션과 통합을 제공합니다. Logic Apps 서비스는 "서버를 사용하지 않으므로" 규모 또는 인스턴스에 대해 고민할 필요가 없으며 워크플로(트리거 및 작업)만 정의하면 됩니다. 기본 플랫폼에서 규모, 가용성 및 성능을 처리합니다. 여러 작업(특히 여러 시스템 간)을 조정해야 하는 모든 시나리오는 Azure Logic Apps에 대한 좋은 사용 사례입니다. 다음은 몇 가지 패턴과 예제입니다.

## <a name="respond-to-triggers-and-extend-actions"></a>트리거에 응답 및 작업 확장

모든 논리 앱은 트리거로 시작됩니다. 예를 들어 워크플로는 일정 이벤트, 수동 호출 또는 "파일이 FTP 서버에 추가된 경우" 트리거와 같은 외부 시스템의 이벤트로 시작할 수 있습니다. 현재 Azure Logic Apps는 온-프레미스 SAP에서 Azure Cognitive Services에 이르기까지 100개 이상의 즉시 사용 가능한 커넥터를 지원합니다. 게시된 커넥터를 포함하지 않을 수 있는 시스템 및 서비스에 대해 논리 앱을 확장할 수도 있습니다.

* [자습서: Logic Apps 및 Power BI로 몇 분 안에 AI 기반 소셜 대시보드 빌드](http://aka.ms/logicappsdemo)
* [사용자 지정 트리거 또는 작업 만들기](../logic-apps/logic-apps-create-api-app.md)
* [워크플로 실행에 대해 장기 실행 작업 설정](../logic-apps/logic-apps-create-api-app.md)
* [웹후크로 외부 이벤트 및 작업에 응답](../logic-apps/logic-apps-create-api-app.md)
* [HTTP 요청에 대한 동기 응답을 포함하는 호출, 트리거 또는 중첩 워크플로](logic-apps-http-endpoint.md)
* [자습서: Twilio SMS 웹후크에 응답 및 텍스트 응답 보내기](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>오류 처리, 로깅 및 제어 흐름 기능

논리 앱에는 조건, 스위치, 루프 및 범위와 같은 고급 제어 흐름에 대한 다양한 기능을 포함합니다. 복원력 있는 솔루션을 보장하기 위해 워크플로에서 오류 및 예외 처리를 구현할 수도 있습니다. 워크플로 실행 상태에 대한 알림 및 진단 로그를 위해 Azure Logic Apps에서는 모니터링 및 경고도 제공합니다.

* [Switch 문으로 다양한 작업 수행](logic-apps-switch-case.md)
* [논리 앱에서 루프 및 일괄 처리로 배열 및 컬렉션의 항목 처리](logic-apps-loops-and-scopes.md)
* [워크플로에서 작성자 오류 및 예외 처리](logic-apps-exception-handling.md)
* [Azure 경고 및 진단 구성](logic-apps-monitor-your-logic-apps.md)
* [사용 사례: 의료 회사에서 HL7 FHIR 워크플로에 대해 논리 앱 예외 처리를 사용하는 방법](logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>논리 앱 배포 및 관리

Visual Studio, Visual Studio Team Services 또는 기타 소스 제어 및 자동화된 빌드 도구를 사용하여 논리 앱을 완전하게 개발 및 배포할 수 있습니다. 리소스 템플릿에서 워크플로 및 종속 연결에 대한 배포를 지원하기 위해 논리 앱은 Azure 리소스 배포 템플릿을 사용합니다. Visual Studio 도구는 이러한 템플릿을 자동으로 생성하므로 버전 관리를 위해 소스 제어에 체크인할 수 있습니다.

* [자동화된 배포 템플릿 만들기](../logic-apps/logic-apps-create-deploy-template.md)
* [Visual Studio의 논리 앱 빌드 및 배포](logic-apps-deploy-from-vs.md)
* [논리 앱 상태 모니터링](logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>콘텐츠 형식, 변환 및 실행 내에서 변형

Azure Logic Apps [워크플로 정의 언어](http://aka.ms/logicappsdocs)의 다양한 함수를 사용하여 여러 콘텐츠 유형을 액세스, 변환 및 변형시킬 수 있습니다. 예를 들어, `@json()` 및 `@xml()` 워크플로 식을 사용하여 문자열, JSON 및 XML 간에 변환할 수 있습니다. Logic Apps 엔진은 서비스 간에 무손실 방식으로 콘텐츠 전송을 지원하는 콘텐츠 형식을 유지합니다.

* [비JSON 콘텐츠 형식 처리](../logic-apps/logic-apps-content-type.md)(`application/xml`, `application/octet-stream` 및 `multipart/formdata`)
* [논리 앱에서 워크플로 식이 작동하는 방식](../logic-apps/logic-apps-author-definitions.md)
* [참조: Azure Logic Apps 워크플로 정의 언어](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>기타 통합 및 기능

논리 앱에서는 Azure Functions, Azure API Management, Azure App Services와 같은 다양한 서비스 및 사용자 지정 HTTP 끝점(REST 및 SOAP)과 통합도 제공합니다.

* [Azure 서버를 사용하지 않고 실시간 소셜 대시보드 만들기](logic-apps-scenario-social-serverless.md)
* [논리 앱에서 Azure Functions 호출](../logic-apps/logic-apps-azure-functions.md)
* [시나리오: Azure Functions로 논리 앱 트리거](logic-apps-scenario-function-sb-trigger.md)
* [블로그: 논리 앱에서 SOAP 끝점 호출](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="next-steps"></a>다음 단계

- [논리 앱에서 오류 및 예외 처리](logic-apps-exception-handling.md)
- [워크플로 정의 언어로 워크플로 정의 작성](logic-apps-author-definitions.md)
- [Azure Logic Apps를 개선할 수 있는 방법에 대한 의견, 질문, 피드백 또는 제안 사항 제출](https://feedback.azure.com/forums/287593-logic-apps)
