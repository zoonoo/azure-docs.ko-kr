---
title: Azure 함수 개요
description: Azure Functions를 사용하여 비동기 워크로드를 몇 분 이내에 최적화하는 방법에 대해 이해합니다.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "77621013"
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 소개

Azure Functions를 사용하면 애플리케이션 인프라에 대한 걱정 없이 작은 코드 부분("함수"라고 함)을 실행할 수 있습니다. Azure Functions를 사용하면 클라우드 인프라에서 애플리케이션을 규모에 맞게 실행하는 데 필요한 모든 최신 서버를 제공합니다.

함수는 특정 이벤트 유형을 통해 "트리거"됩니다. [지원되는 트리거](./functions-triggers-bindings.md)에는 데이터 변경에 대한 응답, 메시지에 대한 응답, 일정에 따라 실행 또는 HTTP 요청의 결과가 포함됩니다.

항상 수많은 서비스에 대해 직접 코딩할 수 있지만, 다른 서비스와의 통합은 바인딩을 사용하여 간소화됩니다. 바인딩은 [매우 다양한 Azure 및 타사 서비스에 대한 선언적 액세스](./functions-triggers-bindings.md)를 제공합니다.

## <a name="features"></a>기능

Azure Functions의 몇 가지 주요 기능은 다음과 같습니다.

- **서버리스 애플리케이션**: Functions를 사용하면 Microsoft Azure에서 [서버리스](https://azure.microsoft.com/solutions/serverless/) 애플리케이션을 개발할 수 있습니다.

- **언어 선택**: 선택한 [C#, Java, JavaScript, Python 및 PowerShell](supported-languages.md)을 사용하여 함수를 작성합니다.

- **사용량 단위 가격 책정 모델**: 코드를 실행하는 데 소요된 시간에 대해서만 요금을 지불합니다. [가격 책정 섹션](#pricing)에서 소비 호스팅 요금제 옵션을 참조하세요.  

- **사용자 고유 종속성 가져오기**: Functions는 NuGet 및 NPM을 지원하므로 즐겨찾는 라이브러리에 액세스할 수 있습니다.

- **통합 보안**: Azure Active Directory, Facebook, Google, Twitter 및 Microsoft 계정과 같은 OAuth 공급자를 사용하여 HTTP 트리거 함수를 보호합니다.

- **간소화된 통합**: Azure 서비스 및 SaaS(Software-as-a-Service) 제품과 쉽게 통합됩니다.

- **유연한 개발**: 지속적인 통합을 설정하고, [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) 및 기타 [지원되는 개발 도구](../app-service/deploy-local-git.md)를 통해 코드를 배포합니다.

- **상태 저장 서버리스 아키텍처**: [Durable Functions](durable/durable-functions-overview.md)를 사용하여 서버리스 애플리케이션을 오케스트레이션합니다.

- **오픈 소스**: Functions 런타임은 오픈 소스이며 [GitHub에서 사용](https://github.com/azure/azure-webjobs-sdk-script)할 수 있습니다.

## <a name="what-can-i-do-with-functions"></a>함수로 할 수 있는 작업은 무엇인가요?

Functions는 대량 데이터 처리, 시스템 통합, IoT(사물 인터넷) 작업 및 간단한 API/마이크로서비스 빌드를 위한 훌륭한 솔루션입니다.

다음과 같은 주요 시나리오를 시작하는 데 사용할 수 있는 일련의 템플릿이 있습니다.

- **HTTP**: [HTTP 요청](functions-create-first-azure-function.md)에 따라 코드 실행

- **타이머**: [미리 정의된 시간에 실행](./functions-create-scheduled-function.md)할 코드 예약

- **Azure Cosmos DB**: [새롭거나 수정된 Azure Cosmos DB 문서](./functions-create-cosmos-db-triggered-function.md) 처리

- **Blob Storage**: [새롭거나 수정된 Azure Storage Blob](./functions-create-storage-blob-triggered-function.md) 처리

- **Queue storage**: [Azure Storage 큐 메시지](./functions-create-storage-queue-triggered-function.md)에 응답

- **Event Grid**: [구독 및 필터를 통해 Azure Event Grid 이벤트](../event-grid/resize-images-on-storage-blob-upload-event.md)에 응답

- **Event Hub**: [대량의 Azure Event Hub 이벤트](./functions-bindings-event-hubs.md)에 응답

- **Service Bus 큐**: [Service Bus 큐 메시지에 응답](./functions-bindings-service-bus.md)하여 다른 Azure 또는 온-프레미스 서비스에 연결

- **Service Bus 토픽**: [Service Bus 토픽 메시지에 응답](./functions-bindings-service-bus.md)하여 다른 Azure 서비스 또는 프레미스 서비스 연결

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Functions 사용 비용

Azure Functions에는 세 종류의 가격 책정 계획이 있습니다. 사용자의 요구에 가장 잘 맞는 하나를 선택합니다.

- **사용량 요금제**: Azure는 필요한 모든 계산 리소스를 제공합니다. 리소스 관리에 대해 걱정할 필요가 없으며, 코드가 실행된 시간에 대해서만 요금을 지불합니다.

- **프리미엄 요금제**: 항상 온라인 상태이며 즉시 응답할 준비가 된 예열된 여러 인스턴스의 수를 지정합니다. 함수가 실행될 때 Azure는 필요한 추가 계산 리소스를 제공합니다. 지속적으로 실행되는 예열된 인스턴스에 대한 요금을 지불하고 Azure로 사용하는 추가 인스턴스는 앱의 규모를 확장 및 축소합니다.

- **App Service 계획**: 웹앱과 마찬가지로 함수를 실행합니다. App Service를 다른 애플리케이션에 사용하는 경우 추가 비용 없이 동일한 계획에서 함수를 실행할 수 있습니다.

호스팅 계획에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요. 전체 가격 책정 정보는 [Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)페이지에 나와 있습니다.

## <a name="next-steps"></a>다음 단계

- [첫 번째 Azure Function 만들기](functions-create-first-function-vs-code.md)  
  [Visual Studio Code](functions-create-first-function-vs-code.md), [명령줄](functions-create-first-azure-function-azure-cli.md)을 시작하거나 [Azure Portal](functions-create-first-azure-function.md)을 사용합니다.

- [Azure Functions 개발자 참조](functions-reference.md)  
  Azure Functions 런타임, 함수 코딩, 트리거 및 바인딩 정의에 대한 참조에 대해 더욱 기술적인 정보를 제공합니다.

- [Azure Functions 크기 조정 방법](functions-scale.md)  
  소비 호스팅 요금제, 올바른 요금제 선택 방법을 포함하여 Azure Functions에서 사용 가능한 서비스 요금제에 대해 설명합니다.

- [Azure App Service에 대해 자세히 알아보기](../app-service/overview.md)  
  Azure Functions는 개발, 환경 변수, 진단 등의 주요 함수에 대한 Azure App Service를 활용합니다.
