---
title: "Azure Functions 개요 | Microsoft Docs"
description: "Azure Functions를 사용하여 비동기 워크로드를 몇 분 이내에 최적화하는 방법에 대해 이해합니다."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7fcf35b99cc410655f2a3b9b6961f5e3bef3aeb5
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 소개  
Azure Functions는 클라우드에서 작은 코드 또는 "함수"를 실행하기 위한 솔루션입니다. 실행할 전체 응용 프로그램 또는 인프라를 염려하지 않고 당면한 문제에 필요한 코드를 작성할 수 있습니다. 함수는 개발 생산성을 높일 수 있으며 C#, F#, Node.js, Python, PHP 등의 원하는 개발 언어를 사용할 수 있습니다. 코드를 실행한 시간에 따라 지불하고 Azure를 신뢰하여 필요에 따라 크기를 조정합니다. Azure Functions를 사용하면 Microsoft Azure에서 서버를 사용하지 않는 응용 프로그램을 개발할 수 있습니다.

이 항목에서는 Azure Functions에 대한 간략한 개요를 제공합니다. Azure Functions를 바로 시작하려면 [첫 번째 Azure 함수 만들기](functions-create-first-azure-function.md)를 시작합니다. Functions에 대한 자세한 기술 정보는 [개발자 참조](functions-reference.md)를 참조하세요.

## <a name="features"></a>기능
Azure Functions의 몇 가지 주요 기능은 다음과 같습니다.

* **언어 선택** - C#, F#, Node.js, Python, PHP, Batch, Bash 또는 기타 실행 파일을 사용하여 함수를 작성합니다.
* **사용량에 따른 가격 책정 모델** - 코드를 실행하는 데 사용한 시간에 대해서만 지불합니다. [가격 책정 섹션](#pricing)에서 소비 호스팅 요금제 옵션을 참조하세요.  
* **고유한 종속성 가져오기** - Functions는 NuGet 및 NPM을 지원하므로 즐겨찾는 라이브러리를 사용할 수 있습니다.  
* **통합 보안** - Azure Active Directory, Facebook, Google, Twitter, Microsoft 계정 등의 OAuth 공급자를 사용하여 HTTP 트리거 함수를 보호합니다.  
* **통합 간소화** - Azure 서비스 및 SaaS(software-as-a-service) 제품을 손쉽게 활용합니다. 몇 가지 예제는 [통합 섹션](#integrations)을 참조하세요.  
* **유연한 개발** - GitHub, Visual Studio Team Services, 기타 [지원되는 개발 도구](../app-service-web/web-sites-deploy.md#deploy-using-an-ide)를 통해 포털에 바로 함수를 코딩하거나 연속 통합을 설정하고 코드를 배포합니다.  
* **오픈 소스** - Functions 런타임은 오픈 소스이며 [GitHub에서 사용](https://github.com/azure/azure-webjobs-sdk-script)할 수 있습니다.  

## <a name="what-can-i-do-with-functions"></a>함수로 할 수 있는 작업은 무엇인가요?
Azure Functions는 데이터를 처리하고 시스템을 통합하며 IoT(사물 인터넷)로 작업하고 간단한 API 및 마이크로 서비스를 구축하는 데 유용한 솔루션입니다. Functions는 이미지 또는 주문 처리, 파일 유지 관리와 같은 작업 또는 일정에 따라 실행하려는 작업에 사용하는 것이 좋습니다. 

Functions는 다음을 포함한 주요 시나리오로 시작할 수 있는 템플릿을 제공합니다.

* **BlobTrigger** - 컨테이너에 Azure 저장소 Blob을 추가할 때 해당 Blob을 처리합니다. 이 함수를 이미지 크기 조정에 사용할 수 있습니다.
* **EventHubTrigger** - Azure Event Hub에 전달된 이벤트에 응답합니다. 응용 프로그램 계측, 사용자 경험 또는 워크플로 처리 및 사물 인터넷(IoT) 시나리오에서 특히 유용합니다.
* **일반 웹후크** - 웹후크를 지원하는 모든 서비스의 웹후크 HTTP 요청을 처리합니다.
* **GitHub 웹후크** - GitHub 리포지토리에서 발생하는 이벤트에 대응합니다. 예제를 보려면 [웹후크 또는 API 함수 만들기](functions-create-a-web-hook-or-api-function.md)를 참조하세요.
* **HTTPTrigger** - HTTP 요청을 사용하여 코드 실행을 트리거합니다.
* **QueueTrigger** - Azure 저장소 큐에 도착하는 메시지에 대응합니다. 예제를 보려면 [Azure 서비스에 바인딩되는 Azure Function 만들기](functions-create-an-azure-connected-function.md)를 참조하세요.
* **ServiceBusQueueTrigger** - 메시지 큐를 수신하여 다른 Azure 서비스 또는 온-프레미스 서비스에 코드를 연결합니다. 
* **ServiceBusTopicTrigger** - 항목을 구독하여 다른 Azure 서비스 또는 온-프레미스 서비스에 코드를 연결합니다. 
* **TimerTrigger** - 사전 정의된 일정에 따라 정리 또는 다른 배치 작업을 실행합니다. 예제를 보려면 [이벤트 처리 함수 만들기](functions-create-an-event-processing-function.md)를 참조하세요.

Azure Functions는 코드 실행을 시작하는 방법인 *트리거*와 입력 및 출력 데이터의 코딩을 간소화하는 방법인 *바인딩*을 지원합니다. Azure Functions가 제공하는 트리거 및 바인딩에 대한 자세한 설명은 [Azure Functions 트리거 및 바인딩 개발자 참조](functions-triggers-bindings.md)를 참조하세요.

## <a name="integrations"></a>통합
Azure Functions는 다양한 Azure 및 타사 서비스와 통합됩니다. 이러한 서비스는 함수 및 시작 실행을 트리거하거나 코드에 대한 입력 및 출력으로 사용할 수 있습니다. Azure Functions에서 지원하는 서비스 통합은 다음과 같습니다. 

* Azure Cosmos DB
* Azure 이벤트 허브 
* Azure 모바일 앱(테이블)
* Azure 알림 허브
* Azure Service Bus(큐 및 항목)
* Azure 저장소(Blob, 쿠, 테이블) 
* GitHub(웹후크)
* 온-프레미스(서비스 버스 사용)
* Twilio(SMS 메시지)

## <a name="pricing"></a>Functions 사용 비용
Azure Functions에는 두 가지 가격 책정 계획이 있으므로 요구 사항에 가장 적합한 계획을 선택할 수 있습니다. 

* **소비 요금제** - 함수가 실행되면 Azure에서 필요한 모든 계산적 리소스를 제공합니다. 리소스 관리에 대해 염려하지 않아도 되며 코드가 실행된 시간에 대해서만 비용을 지불합니다. 
* **앱 서비스 계획** - 웹, 모바일, API 앱 등의 함수를 실행합니다. 다른 응용 프로그램에 이미 앱 서비스를 사용하고 있는 경우 추가 비용 없이 동일한 계획으로 함수를 실행할 수 있습니다. 

전체 가격 책정 정보는 [Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)페이지에 나와 있습니다. 함수 조정에 대한 자세한 내용은 [Azure Functions 조정 방법](functions-scale.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [첫 번째 Azure 함수 만들기](functions-create-first-azure-function.md)  
  Azure Functions 빠른 시작을 사용하여 바로 첫 번째 함수를 만듭니다. 
* [Azure Functions 개발자 참조](functions-reference.md)  
  Azure Functions 런타임, 함수 코딩, 트리거 및 바인딩 정의에 대한 참조에 대해 더욱 기술적인 정보를 제공합니다.
* [Azure Functions 테스트](functions-test-a-function.md)  
  함수를 테스트하는 다양한 도구와 기법을 설명합니다.
* [Azure Functions 크기 조정 방법](functions-scale.md)  
  소비 호스팅 요금제, 올바른 요금제 선택 방법을 포함하여 Azure Functions에서 사용 가능한 서비스 요금제에 대해 설명합니다. 
* [Azure 앱 서비스에 대해 자세히 알아보기](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions는 개발, 환경 변수, 진단 등의 주요 함수에 대한 Azure 앱 서비스 플랫폼을 활용합니다. 


