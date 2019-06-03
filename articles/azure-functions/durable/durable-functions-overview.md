---
title: 지속성 함수 개요 - Azure
description: Azure Functions의 지속성 함수 확장을 소개합니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 2228f3fe05e1021d0f87ce0b0d33a8287f048a8c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872802"
---
# <a name="what-are-durable-functions"></a>Durable Functions란?

*Durable Functions*는 서버리스 환경에서 상태 저장 함수를 작성할 수 있게 해주는 [Azure Functions](../functions-overview.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다.

## <a name="benefits"></a>이점

확장을 통해 다음과 같은 이점을 제공할 수 있는 [*오케스트레이터 함수*](durable-functions-types-features-overview.md#orchestrator-functions)를 사용하여 상태 저장 워크플로를 정의할 수 있습니다.

* 코드에서 워크플로를 정의할 수 있습니다. JSON 스키마 또는 디자이너가 필요하지 않습니다.
* 다른 함수를 동기적 및 비동기적으로 호출할 수 있습니다. 호출된 함수의 출력은 지역 변수에 저장할 수 있습니다.
* 함수에서 기다릴 때 자동으로 진행 상황 검사점이 설정됩니다. 프로세스가 재활용되거나 VM이 다시 부팅되더라도 로컬 상태가 손실되지 않습니다.

## <a name="application-patterns"></a>애플리케이션 패턴

Durable Functions에 대한 기본 사용 사례는 서버리스 애플리케이션에서 복잡한 상태 저장 조정 요구 사항을 단순화하는 것입니다. 다음 섹션에서는 Durable Functions를 활용할 수 있는 몇 가지 일반적인 애플리케이션 패턴입니다.

* [체이닝](durable-functions-concepts.md#chaining)
* [팬아웃/팬인](durable-functions-concepts.md#fan-in-out)
* [비동기 HTTP API](durable-functions-concepts.md#async-http)
* [모니터링](durable-functions-concepts.md#monitoring)
* [사용자 개입](durable-functions-concepts.md#human)

## <a name="language-support"></a>지원되는 언어

Durable Functions는 현재 다음 언어를 지원합니다.

* **C#** : [미리 컴파일된 클래스 라이브러리](../functions-dotnet-class-library.md) 및 [C# 스크립트](../functions-reference-csharp.md) 모두
* **F#** : 미리 컴파일된 클래스 라이브러리 및 F# 스크립트. F# 스크립트는 Azure Functions 런타임 버전 1.x에서만 지원됩니다.
* **JavaScript**: Azure Functions 런타임 버전 2.x에서만 지원됩니다. Durable Functions 확장 버전 1.7.0 이상이 필요합니다. 

Durable Functions는 모든 [Azure Functions 언어](../supported-languages.md)를 지원하는 것을 목표로 합니다. 추가 언어를 지원하기 위한 최신 작업 상태는 [Durable Functions 문제 목록](https://github.com/Azure/azure-functions-durable-extension/issues)를 참조하세요.

Azure Functions와 마찬가지로 [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) 및 [Azure Portal](durable-functions-create-portal.md)을 사용하여 Durable Functions를 개발하는 데 도움이 되는 템플릿이 있습니다.

## <a name="billing"></a>결제

Durable Functions 요금은 Azure Functions와 동일하게 청구됩니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요.

## <a name="jump-right-in"></a>지금 바로 시작

다음 언어별 빠른 시작 자습서 중 하나를 완료하여 10분 이내에 Durable Functions를 시작할 수 있습니다.

* [Visual Studio 2019를 사용하는 C#](durable-functions-create-first-csharp.md)
* [Visual Studio Code를 사용하는 JavaScript](quickstart-js-vscode.md)

두 빠른 시작에서는 “hello world” 지속성 함수를 로컬에서 만들고 테스트합니다. 그런 후 함수 코드를 Azure에 게시합니다. 생성한 함수는 다른 함수에 대한 호출을 오케스트레이션하고 함께 연결합니다.

## <a name="learn-more"></a>자세한 정보

다음 비디오는 Durable Functions의 이점을 강조합니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Durable Functions는 [Azure Functions](../functions-overview.md)의 고급 확장이므로 모든 애플리케이션에 적합하지는 않습니다. Durable Functions에 대해 자세히 알아보려면 [Durable Functions 패턴 및 기술 개념](durable-functions-concepts.md)을 참조하세요. 다른 Azure 오케스트레이션 기술과 비교해 보려면 [Azure Functions 및 Azure Logic Apps 비교](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions 패턴 및 기술 개념](durable-functions-concepts.md)
