---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586134"
---
## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>2.x 이상 기능

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되며 확장 번들은 다른 모든 응용 프로그램 유형에 사용됩니다.

| 언어                                        | 추가...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치 , 버전 3.x | |
| C # 스크립트, 자바, 자바 스크립트, 파이썬, 파워 쉘 | [확장 번들] 등록          | [Azure 도구 확장은] Visual Studio 코드와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure 포털에서 온라인 전용)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고도 기존 바인딩 확장을 업데이트하려면 [확장 업데이트를]참조하십시오. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[확장 번들]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[확장 프로그램 업데이트]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 자동으로 참조를 가지고 [있습니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)