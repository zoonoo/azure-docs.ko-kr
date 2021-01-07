---
title: Azure Functions에 대 한 Azure Event Grid 바인딩
description: Azure Functions에서 Event Grid 이벤트를 처리하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104549"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Functions에 대 한 Azure Event Grid 바인딩

이 참조는 Azure Functions에서 [Event Grid](../event-grid/overview.md) 이벤트를 처리 하는 방법을 설명 합니다. HTTP 끝점에서 Event Grid 메시지를 처리 하는 방법에 대 한 자세한 내용은 [http 끝점으로 이벤트 수신](../event-grid/receive-events.md)을 참조 하세요.

Event Grid는 *게시자*에서 발생하는 이벤트를 알리기 위해 HTTP 요청을 전송하는 Azure 서비스입니다. 게시자는 이벤트를 시작하는 서비스 또는 리소스입니다. 예를 들어, Azure Blob Storage 계정은 게시자이고 [Blob 업로드 또는 삭제는 이벤트](../storage/blobs/storage-blob-event-overview.md)입니다. 일부 [Azure 서비스에는 Event Grid에 이벤트를 게시하는 방식을 기본적으로 지원합니다](../event-grid/overview.md#event-sources).

이벤트 *처리기*는 이벤트를 수신하고 처리합니다. Azure Functions는 [Event Grid 이벤트를 처리를 기본적으로 지원하는 Azure 서비스](../event-grid/overview.md#event-handlers) 중 하나입니다. 이 참조에서는 Event Grid에서 이벤트를 받을 때 Event Grid 트리거를 사용 하 여 함수를 호출 하 고 출력 바인딩을 사용 하 여 [Event Grid 사용자 지정 토픽](../event-grid/post-to-custom-topic.md)에 이벤트를 보내는 방법에 대해 알아봅니다.

원하는 경우 HTTP 트리거를 사용 하 여 Event Grid 이벤트를 처리할 수 있습니다. [HTTP 끝점으로 이벤트 수신을](../event-grid/receive-events.md)참조 하세요. 현재 [CloudEvents 스키마](../event-grid/cloudevents-schema.md#azure-functions)에서 이벤트가 전달 될 때 Azure Functions 앱에 대 한 Event Grid 트리거를 사용할 수 없습니다. 대신, HTTP 트리거를 사용합니다.

| 작업 | 형식 |
|---------|---------|
| Event Grid 이벤트가 디스패치되 면 함수를 실행 합니다. | [트리거](./functions-bindings-event-grid-trigger.md) |
| Event Grid 이벤트를 보냅니다. |[출력 바인딩](./functions-bindings-event-grid-output.md) |

이 참조의 코드는 버전 2.x 이상에서 사용 되는 .NET Core 구문을 기본값으로 사용 합니다. 1.x 구문에 대한 자세한 내용은 [1.x 기능 템플릿](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)을 참조하세요.

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 2.x 버전 | |
| C # 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C # 스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x를 자동으로 참조 합니다.

## <a name="next-steps"></a>다음 단계
* [Event Grid 이벤트가 디스패치되 면 함수를 실행 합니다.](./functions-bindings-event-grid-trigger.md)
* [Event Grid 이벤트 디스패치](./functions-bindings-event-grid-trigger.md)