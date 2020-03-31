---
title: Azure Functions HTTP 트리거 및 바인딩
description: Azure Functions에서 HTTP 트리거 및 바인딩을 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462108"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP 트리거 및 바인딩 개요

Azure 함수는 서버없는 API를 구축하고 [웹 후크에](https://en.wikipedia.org/wiki/Webhook)응답하기 위해 HTTP 요청을 통해 호출 될 수 있습니다.

| 작업 | Type |
|---------|---------|
| HTTP 요청에서 함수 실행 | [트리거](./functions-bindings-http-webhook-trigger.md) |
| 함수에서 HTTP 응답 반환 |[출력 바인딩](./functions-bindings-http-webhook-output.md) |

이 문서의 코드는 함수 버전 2.x 이상에서 사용되는 .NET Core 구문을 기본값으로 설정합니다. 1.x 구문에 대한 자세한 내용은 [1.x 기능 템플릿](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)을 참조하세요.

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>2.x 이상 기능

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되며 확장 번들은 다른 모든 응용 프로그램 유형에 사용됩니다.

| 언어                                        | 추가...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치 , 버전 3.x | |
| C # 스크립트, 자바, 자바 스크립트, 파이썬, 파워 쉘 | [확장 번들] 등록          | [Azure 도구 확장은](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Visual Studio 코드와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure 포털에서 온라인 전용)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고도 기존 바인딩 확장을 업데이트하려면 [확장 업데이트를]참조하십시오. |

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[확장 프로그램 업데이트]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 자동으로 참조를 가지고 [있습니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)

## <a name="next-steps"></a>다음 단계

- [HTTP 요청에서 함수 실행](./functions-bindings-http-webhook-trigger.md)
- [함수에서 HTTP 응답 반환](./functions-bindings-http-webhook-output.md)
