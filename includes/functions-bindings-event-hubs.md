---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623460"
---
## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 3(sp3) | |
| C # 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장] 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C # 스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[확장 번들]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[확장 업데이트]: ../articles/azure-functions/functions-bindings-register.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs 확장 5.x 이상

새 버전의 Event Hubs 바인딩 확장은 [Preview NuGet 패키지로](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1)사용할 수 있습니다. 이 미리 보기에서는 [비밀 대신 id를 사용 하 여 연결](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입 되었습니다. .NET 응용 프로그램의 경우 바인딩할 수 있는 형식도 변경 하 여 `Microsoft.Azure.EventHubs` [EventHubs](/dotnet/api/azure.messaging.eventhubs)의 새 형식으로 형식을 바꿉니다.

> [!NOTE]
> 미리 보기 패키지는 확장 번들에 포함 되지 않으며 수동으로 설치 해야 합니다. .NET 앱의 경우 패키지에 대 한 참조를 추가 합니다. 다른 모든 앱 유형에 대해서는 [확장 업데이트]를 참조 하세요.

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[확장 업데이트]: ./functions-bindings-register.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x를 자동으로 참조 합니다.

## <a name="hostjson-settings"></a>host.json 설정
<a name="host-json"></a>

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 파일에는 Event Hubs 트리거 동작을 제어하는 설정이 포함됩니다. 구성은 Azure Functions 버전에 따라 달라집니다.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]