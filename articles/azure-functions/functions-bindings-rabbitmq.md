---
title: Azure Functions에 대한 RabbitMQ 바인딩
description: Azure Functions에서 Azure RabbitMQ 트리거 및 바인딩을 보내는 방법을 배워봅니다.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746527"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions 개요에 대한 RabbitMQ 바인딩

> [!NOTE]
> RabbitMQ 바인딩은 **프리미엄 및 전용** 플랜에서만 완전히 지원됩니다. 사용은 지원되지 않습니다.

Azure Functions는 [트리거 및 바인딩](./functions-triggers-bindings.md)을 통해 [RabbitMQ](https://www.rabbitmq.com/)와 통합됩니다. Azure Functions RabbitMQ 확장을 사용하면 함수를 사용하여 RabbitMQ API를 사용하여 메시지를 보내고 받을 수 있습니다.

| 작업 | 유형 |
|---------|---------|
| 큐를 통해 RabbitMQ 메시지가 들어올 때 함수를 실행합니다. | [트리거](./functions-bindings-rabbitmq-trigger.md) |
| RabbitMQ 메시지 보내기 |[출력 바인딩](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Functions 앱에 추가

이 확장을 사용하여 개발을 시작하려면 먼저 [RabbitMQ 엔드포인트를 설정](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)해야 합니다. RabbitMQ에 대해 자세히 알아보려면 [시작 페이지](https://www.rabbitmq.com/getstarted.html)를 확인하세요.

### <a name="functions-3x-and-higher"></a>Functions 3.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지], 버전 4.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Functions 1.x 과 2.x.

RabbitMQ 바인딩 확장은 Functions 1. x 및 2.x에 대해 지원되지 않습니다. Functions 3.x 이상을 사용하세요.

## <a name="next-steps"></a>다음 단계

- [RabbitMQ 메시지가 생성될 때(트리거) 함수 실행](./functions-bindings-rabbitmq-trigger.md)
- [Azure Functions에서 RabbitMQ 메시지 보내기(출력 바인딩)](./functions-bindings-rabbitmq-output.md)