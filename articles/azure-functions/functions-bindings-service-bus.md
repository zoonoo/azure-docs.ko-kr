---
title: Azure Functions의 Azure Service Bus 바인딩
description: Azure Functions에서 Azure Service Bus 트리거와 바인딩을 보내는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e15dfec049197fa056cbd55fd839b3eb93be77c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530372"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions의 Azure Service Bus 바인딩

Azure Functions는 [트리거와 바인딩을](./functions-triggers-bindings.md)통해 [Azure Service Bus](https://azure.microsoft.com/services/service-bus) 와 통합 됩니다. Service Bus와 통합 하면 큐 또는 토픽 메시지에 응답 하는 함수를 작성할 수 있습니다.

| 작업 | 형식 |
|---------|---------|
| Service Bus 큐 또는 토픽 메시지가 만들어지면 함수를 실행 합니다. | [트리거](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus 메시지 보내기 |[출력 바인딩](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

> [!NOTE]
> Service Bus 바인딩은 현재 관리 되는 id를 사용 하 여 인증을 지원 하지 않습니다. 대신 [Service Bus 공유 액세스 서명을](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)사용 하세요.

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 4.x | |
| C # 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장] 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C # 스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./install-update-binding-extensions-manual.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대 한 참조를 자동으로 포함 합니다.

## <a name="next-steps"></a>다음 단계

- [Service Bus 큐 또는 토픽 메시지가 생성 될 때 함수 실행 (트리거)](./functions-bindings-service-bus-trigger.md)
- [Azure Functions에서 Azure Service Bus 메시지 보내기 (출력 바인딩)](./functions-bindings-service-bus-output.md)
