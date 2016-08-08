<properties
   pageTitle="논리 앱 예제 및 시나리오 | Microsoft Azure"
   description="일반적인 논리 앱 예제를 참조하고 일반적인 시나리오를 구현하는 방법에 대해 알아봅니다."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# 논리 앱 예제 및 일반적인 시나리오

이 문서는 논리 앱을 사용하여 비즈니스 프로세스를 자동화할 수 있는 방법을 이해할 수 있도록 일반적인 시나리오 및 예제를 자세히 설명합니다.

## 논리 앱 트리거

몇 가지 방법으로 다른 앱에서 논리 앱을 트리거할 수 있습니다. 다음은 이러한 방법 중 일부입니다.

- [HTTP 요청(POST)](app-service-logic-http-endpoint.md)
- [WebHook 수신](app-service-logic-create-api-app.md)
- [끝점 폴링](app-service-logic-create-api-app.md)

### 시나리오

- [논리 앱 요청 동기 응답](app-service-logic-http-endpoint.md)

## 사용자 지정 및 장기 실행 작업

- [사용자 지정 작업 만들기](app-service-logic-create-api-app.md)

## 논리 앱 배포 및 관리

- [논리 앱 배포 템플릿 만들기](app-service-logic-create-deploy-template.md)
- [논리 앱으로 문제 진단](app-service-logic-diagnosing-failures.md)
- [Visual Studio에서 논리 앱 배포](app-service-logic-deploy-from-vs.md)
- [논리 앱 모니터링](app-service-logic-monitor-your-logic-apps.md)

## 콘텐츠 형식, 변환 및 변형

논리 앱 [워크플로 정의 언어](http://aka.ms/logicappsdocs)에는 여러 다른 콘텐츠 형식을 변환하고 사용할 수 있도록 하는 많은 함수가 포함되어 있습니다. 또한 엔진은 워크플로를 통해 데이터가 흐를 때 콘텐츠 형식을 보존하기 위해 할 수 있는 모든 작업을 수행합니다.

- [콘텐츠 형식 처리](app-service-logic-content-type.md)(예: application/json, application/xml 및 일반/텍스트)
- [워크플로 정의 작성](app-service-logic-author-definitions.md)
- [워크플로 정의 언어 참조](http://aka.ms/logicappsdocs)

## 일괄 처리 및 반복

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Azure Functions와 통합

- [Azure Functions 통합](app-service-logic-azure-functions.md)

### 시나리오

- [서비스 버스 트리거로 Azure Function](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST 및 SOAP

 - [SOAP 호출](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


이 문서에 예제 및 시나리오를 추가할 예정입니다. 아래의 의견 섹션을 사용하여 여기에서 보고 싶은 예제 또는 시나리오를 알려주세요.

<!---HONumber=AcomDC_0727_2016-->