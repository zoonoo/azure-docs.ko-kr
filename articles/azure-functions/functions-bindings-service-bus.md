---
title: Azure Functions의 Azure Service Bus 바인딩
description: Azure 함수에서 Azure Service 버스 트리거 및 바인딩을 보내는 방법을 알아봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277415"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions의 Azure Service Bus 바인딩

Azure Functions는 트리거 및 바인딩을 통해 [Azure Service Bus와](https://azure.microsoft.com/services/service-bus) [통합됩니다.](./functions-triggers-bindings.md) Service Bus와 통합하면 큐 또는 토픽 메시지에 반응하고 보내는 함수를 빌드할 수 있습니다.

| 작업 | Type |
|---------|---------|
| 서비스 버스 큐 또는 토픽 메시지가 생성될 때 함수 실행 | [트리거](./functions-bindings-service-bus-trigger.md) |
| Azure 서비스 버스 메시지 보내기 |[출력 바인딩](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>2.x 이상 기능

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되며 확장 번들은 다른 모든 응용 프로그램 유형에 사용됩니다.

| 언어                                        | 추가...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치 , 버전 4.x | |
| C # 스크립트, 자바, 자바 스크립트, 파이썬, 파워 쉘 | [확장 번들] 등록          | [Azure 도구 확장은] Visual Studio 코드와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure 포털에서 온라인 전용)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고도 기존 바인딩 확장을 업데이트하려면 [확장 업데이트를]참조하십시오. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 프로그램 업데이트]: ./install-update-binding-extensions-manual.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 자동으로 참조를 가지고 [있습니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)

## <a name="next-steps"></a>다음 단계

- [서비스 버스 큐 또는 토픽 메시지가 생성될 때 함수 실행(트리거)](./functions-bindings-service-bus-trigger.md)
- [Azure 함수에서 Azure Service 버스 메시지 보내기(출력 바인딩)](./functions-bindings-service-bus-output.md)
