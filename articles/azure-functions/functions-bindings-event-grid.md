---
title: Azure 함수에 대한 Azure 이벤트 그리드 바인딩
description: Azure Functions에서 Event Grid 이벤트를 처리하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461082"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure 함수에 대한 Azure 이벤트 그리드 바인딩

이 참조에서는 Azure Functions에서 [이벤트 그리드](../event-grid/overview.md) 이벤트를 처리하는 방법을 설명합니다. HTTP 끝점에서 이벤트 그리드 메시지를 처리하는 방법에 대한 자세한 내용은 [HTTP 끝점으로 이벤트 받기](../event-grid/receive-events.md)를 참조하십시오.

Event Grid는 *게시자*에서 발생하는 이벤트를 알리기 위해 HTTP 요청을 전송하는 Azure 서비스입니다. 게시자는 이벤트를 시작하는 서비스 또는 리소스입니다. 예를 들어, Azure Blob Storage 계정은 게시자이고 [Blob 업로드 또는 삭제는 이벤트](../storage/blobs/storage-blob-event-overview.md)입니다. 일부 [Azure 서비스에는 Event Grid에 이벤트를 게시하는 방식을 기본적으로 지원합니다](../event-grid/overview.md#event-sources).

이벤트 *처리기*는 이벤트를 수신하고 처리합니다. Azure Functions는 [Event Grid 이벤트를 처리를 기본적으로 지원하는 Azure 서비스](../event-grid/overview.md#event-handlers) 중 하나입니다. 이 참조에서는 이벤트 그리드 트리거를 사용하여 이벤트 그리드에서 이벤트를 수신할 때 함수를 호출하고 출력 바인딩을 사용하여 [이벤트를 이벤트 Grid 사용자 지정 항목으로](../event-grid/post-to-custom-topic.md)보내는 방법을 배웁니다.

원하는 경우 HTTP 트리거를 사용하여 이벤트 그리드 이벤트를 처리할 수 있습니다. [HTTP 끝점에 대한 이벤트 수신을](../event-grid/receive-events.md)참조하십시오. 현재 [이벤트가 CloudEvents 스키마에서](../event-grid/cloudevents-schema.md#azure-functions)제공될 때 Azure Functions 앱에 이벤트 그리드 트리거를 사용할 수 없습니다. 대신, HTTP 트리거를 사용합니다.

| 작업 | Type |
|---------|---------|
| 이벤트 그리드 이벤트가 디스패치될 때 함수 실행 | [트리거](./functions-bindings-event-grid-trigger.md) |
| 이벤트 그리드 이벤트 전송 |[출력 바인딩](./functions-bindings-event-grid-output.md) |

이 참조의 코드는 함수 버전 2.x 이상에서 사용되는 .NET Core 구문으로 기본설정됩니다. 1.x 구문에 대한 자세한 내용은 [1.x 기능 템플릿](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)을 참조하세요.

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
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[확장 프로그램 업데이트]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 자동으로 참조를 가지고 [있습니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)

## <a name="next-steps"></a>다음 단계
* [이벤트 그리드 이벤트가 디스패치될 때 함수 실행](./functions-bindings-event-grid-trigger.md)
* [이벤트 그리드 이벤트 디스패치](./functions-bindings-event-grid-trigger.md)
