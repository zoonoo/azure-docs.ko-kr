---
title: Azure Functions에 대 한 Azure RabbitMQ 바인딩
description: Azure Functions에서 Azure RabbitMQ 트리거 및 바인딩을 보내는 방법에 대해 알아봅니다.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746527"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Azure Functions 개요에 대 한 RabbitMQ 바인딩

> [!NOTE]
> RabbitMQ 바인딩은 **프리미엄 및 전용** 계획 에서만 완전 하 게 지원 됩니다. 소비는 지원 되지 않습니다.

Azure Functions는 [트리거와 바인딩을](./functions-triggers-bindings.md)통해 [RabbitMQ](https://www.rabbitmq.com/) 와 통합 됩니다. Azure Functions RabbitMQ 확장을 사용 하면 함수를 사용 하 여 RabbitMQ API를 사용 하 여 메시지를 보내고 받을 수 있습니다.

| 작업 | Type |
|---------|---------|
| 큐를 통해 RabbitMQ 메시지가 들어올 때 함수를 실행 합니다. | [트리거](./functions-bindings-rabbitmq-trigger.md) |
| RabbitMQ 메시지 보내기 |[출력 바인딩](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

이 확장을 사용 하 여 개발을 시작 하려면 먼저 [RabbitMQ 끝점을 설정](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)해야 합니다. RabbitMQ에 대해 자세히 알아보려면 [시작 페이지](https://www.rabbitmq.com/getstarted.html)를 확인 하세요.

### <a name="functions-3x-and-higher"></a>함수 3(sp3) 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 4.x | |
| C # 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장] 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C # 스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>함수 1.x 및 2.x

RabbitMQ 바인딩 확장은 함수 1. x 및 2.x에 대해 지원 되지 않습니다. 3. x 이상의 함수를 사용 하십시오.

## <a name="next-steps"></a>다음 단계

- [RabbitMQ 메시지가 생성 될 때 함수 실행 (트리거)](./functions-bindings-rabbitmq-trigger.md)
- [Azure Functions에서 RabbitMQ 메시지 보내기 (출력 바인딩)](./functions-bindings-rabbitmq-output.md)