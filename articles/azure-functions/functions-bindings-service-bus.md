---
title: Azure Functions의 Azure Service Bus 바인딩
description: Azure Functions에서 Azure Service Bus 트리거 및 바인딩을 보내는 방법을 배워봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: b32f16d170df9963960862bc82aef1a4baf13896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104447"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions의 Azure Service Bus 바인딩

Azure Functions는 [트리거 및 바인딩](./functions-triggers-bindings.md)을 통해 [Azure Service Bus](https://azure.microsoft.com/services/service-bus)와 통합됩니다. Service Bus와 통합하면 큐 또는 토픽 메시지에 응답하고 이를 전송하는 함수를 작성할 수 있습니다.

| 작업 | 유형 |
|---------|---------|
| Service Bus 큐 또는 토픽 메시지가 만들어지면 함수를 실행합니다. | [트리거](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus 메시지 전송 |[출력 바인딩](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

> [!NOTE]
> Service Bus 바인딩은 관리 ID를 사용하여 인증을 현재 지원하지 않습니다. 대신 [Service Bus 공유 액세스 서명](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)을 사용하세요.

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 4.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만 사용)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

## <a name="next-steps"></a>다음 단계

- [Service Bus 큐 또는 토픽 메시지가 생성(트리거) 될 때 함수 실행](./functions-bindings-service-bus-trigger.md)
- [Azure Functions에서 Azure Service Bus 메시지 전송(출력 바인딩)](./functions-bindings-service-bus-output.md)