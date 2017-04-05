---
title: "시나리오 - Azure 서버를 사용하지 않고 Customer Insights 대시보드 만들기 | Microsoft Docs"
description: "Azure Logic Apps 및 Azure Functions를 사용하여 고객 피드백, 소셜 데이터 등을 관리하는 대시보드를 구축할 수 있는 방법의 예입니다."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 4676e0da4309b1460e471f94946161fa22d46226
ms.lasthandoff: 03/30/2017

---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Azure Logic Apps 및 Azure Functions를 사용하여 실시간 Customer Insights 대시보드 만들기

Azure 서버를 사용하지 않는 도구는 인프라에 대한 염려 없이 클라우드에서 응용 프로그램을 빠르게 구축하고 호스팅하는 강력한 기능을 제공합니다.  이 시나리오에서는 고객 피드백을 트리거하고 기계 학습으로 피드백을 분석하고 Power BI 또는 Azure Data Lake와 같은 원본 정보를 게시하는 대시보드를 만듭니다.

## <a name="overview-of-the-scenario-and-tools-used"></a>시나리오 및 사용하는 도구 개요

이 솔루션을 구현하기 위해 Azure에서 서버를 사용하지 않는 앱의 두 개의 주요 구성 요소인 [Azure Functions](https://azure.microsoft.com/services/functions/) 및 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)를 활용합니다.

Logic Apps는 클라우드에서 서버를 사용하지 않는 워크플로 엔진입니다.  서버가 없는 구성 요소에 오케스트레이션을 제공하고 100개 이상의 서비스 및 API에도 연결합니다.  이 시나리오에서는 고객의 피드백을 트리거하기 위한 논리 앱을 만듭니다.  고객 피드백에 응답하는 데 도움이 되는 일부 커넥터는 [웹 형식에서](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/) Outlook.com, Office 365, Survey Monkey, Twitter 및 HTTP 요청을 포함합니다.  아래 워크플로의 경우 Twitter에 대한 해시 태그를 모니터링합니다.

함수는 클라우드에서 서버를 사용하지 않는 계산을 제공합니다.  이 시나리오에서는 일련의 미리 정의된 키워드에 따라 고객의 트윗을 플래그 지정하기 위해 Azure Functions를 사용합니다.

전체 솔루션은 [Visual Studio에서 작성](logic-apps-deploy-from-vs.md) 및 [리소스 템플릿의 일부분으로 배포](logic-apps-create-deploy-template.md)될 수 있습니다.  [Channel 9에](http://aka.ms/logicappsdemo) 시나리오의 연습 동영상이 있습니다.

## <a name="building-the-logic-app-to-trigger-on-customer-data"></a>고객 데이터를 트리거하는 논리 앱 빌드

Visual Studio 또는 Azure Portal에서 [논리 앱을 만든](logic-apps-create-a-logic-app.md) 후:

1. Twitter에서 **새 트윗**에 대한 트리거 추가
1. 키워드 또는 해시 태그에 대한 트윗을 수신하도록 트리거를 구성합니다.

> [!NOTE]
> 트리거에 대한 되풀이 속성이 논리 앱이 폴링 기반 트리거에 대한 새 항목을 확인하는 주기를 결정합니다.

![Twitter 트리거의 예][1]

이 앱은 이제 모든 새 트윗에서 실행됩니다.  그런 다음 해당 트윗 데이터를 가져오고 더 자세한 데이터 표현을 이해할 수 있습니다.  이를 위해 [Azure 인식 서비스](https://azure.microsoft.com/services/cognitive-services/)를 사용하여 텍스트의 데이터를 검색할 수 있습니다.

1. **새 단계** 클릭
1. **텍스트 분석** 커넥터 선택 또는 검색
1. **데이터 감지** 작업 선택
1. 메시지가 나타나는 경우 텍스트 분석 서비스에 대한 올바른 Cognitive Services 키 제공
1. 분석할 텍스트로 **트윗 텍스트**를 추가합니다.

이제 트윗에 대한 트윗 데이터 및 정보가 있으므로 다양한 다른 커넥터가 연결될 수 있습니다.
* Power BI - 스트리밍 데이터 집합에 행 추가: 보Power BI 대시보드에서 실시간으로 트윗을 봅니다.
* Azure Data Lake - 파일 추가: 분석 작업에 포함할 Azure Data Lake 데이터 집합에 고객 데이터를 추가합니다.
* SQL - 행 추가: 나중의 검색을 위해 데이터베이스에 데이터를 저장합니다.
* Slack - 메시지 보내기: 작업을 필요로 하는 부정적인 피드백에 대한 Slack 채널을 경고합니다.

Azure 함수는 데이터 외에 더 많은 사용자 지정 계산을 수행하는 데도 사용할 수 있습니다.

## <a name="enriching-the-data-with-an-azure-function"></a>Azure 함수를 사용하여 데이터 보강

함수를 만들려면 먼저 Azure 구독에 함수 앱이 있어야 합니다.  포털에서 Azure 함수를 만드는 자세한 내용은 [여기에서 찾을 수](../azure-functions/functions-create-first-azure-function-azure-portal.md) 있습니다.

논리 앱에서 직접 호출될 함수의 경우 HTTP 트리거 바인딩이 있어야 합니다.  **HttpTrigger** 템플릿을 사용하는 것이 좋습니다.

이 시나리오에서 Azure 함수의 요청 본문은 트윗 텍스트입니다.  함수 코드에서 단순히 트윗 텍스트가 키워드 또는 구를 포함하는 경우에 대한 논리를 정의합니다.  함수 자체는 시나리오의 필요에 따라 간단하거나 복잡하게 유지될 수 있습니다.

함수 끝에서 일부 데이터로 논리 앱에 대한 응답을 반환합니다.  간단한 부울 값(예: `containsKeyword`) 또는 복잡한 개체일 수 있습니다.

![구성된 Azure 함수 단계][2]

> [!TIP]
> 논리 앱의 함수에서 복잡한 응답에 액세스할 때 JSON 구문 분석 작업을 사용합니다.

함수가 저장된 후 위에서 만든 논리 앱에 추가할 수 있습니다.  논리 앱에서:

1. **새 단계**를 클릭하여 추가
1. **Azure Functions** 커넥터 선택
1. 기존 함수를 선택하고 만든 함수로 이동하려면 선택
1. **요청 본문**에 대한 **트윗 텍스트**에 전송

## <a name="running-and-monitoring-the-solution"></a>솔루션 실행 및 모니터링

Logic Apps에서 서버를 사용하지 않는 오케스트레이션 제작의 이점 중 하나는 풍부한 디버그와 모니터링 기능입니다.  Visual Studio, Azure Portal 내에서 또는 REST API 및 SDK를 통해 모든 실행(현재 또는 기록)을 볼 수 있습니다.

논리 앱을 테스트하는 가장 쉬운 방법 중 하나는 디자이너에서 **실행** 단추를 사용하는 것입니다.  **실행**을 클릭하면 이벤트가 감지될 때까지 5초마다 트리거 폴링이 계속되고 실행이 진행됨에 따라 라이브 보기를 제공합니다.

Azure Portal의 개요 블레이드 또는 Visual Studio 클라우드 탐색기를 사용하여 이전 실행 기록을 볼 수 있습니다.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>자동화된 배포를 위한 배포 템플릿 만들기

솔루션이 개발되었으므로 Azure 배포 템플릿을 통해 전 세계 모든 Azure 지역에 캡처 및 배포될 수 있습니다.  이 워크플로의 다양한 버전에 대한 매개 변수 수정뿐만 아니라 빌드 및 릴리스 파이프라인에서 이 솔루션을 통합하는 데 유용합니다.  배포 템플릿 만들기에 대한 자세한 내용은 [이 문서에서](logic-apps-create-deploy-template.md) 찾을 수 있습니다.

Azure Functions는 모든 종속성과 함께 전체 솔루션을 단일 템플릿으로 관리할 수 있도록 배포 템플릿에 통합될 수도 있습니다.  함수 배포 템플릿의 예는 [Azure 빠른 시작 템플릿 리포지토리](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)에서 찾을 수 있습니다.

## <a name="whats-next"></a>다음 단계

* [Azure Logic Apps 다른 예제 및 시나리오 참조](logic-apps-examples-and-scenarios.md)
* [종단 간 이 솔루션을 만드는 연습 동영상 시청](http://aka.ms/logicappsdemo)
* [논리 앱 내에서 예외를 처리 및 catch하는 방법 알아보기](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
