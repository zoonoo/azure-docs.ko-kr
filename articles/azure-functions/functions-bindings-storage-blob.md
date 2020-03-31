---
title: Azure Blob 저장소 트리거 및 Azure 함수에 대한 바인딩
description: Azure 함수에서 Azure Blob 저장소 트리거 및 바인딩을 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277233"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure 함수 개요에 대 한 Azure Blob 저장소 바인딩

Azure Functions는 트리거 및 바인딩을 통해 [Azure 저장소와](https://docs.microsoft.com/azure/storage/) [통합됩니다.](./functions-triggers-bindings.md) Blob 저장소와 통합하면 Blob 데이터의 변화에 반응하는 함수를 빌드하고 값을 읽고 쓸 수 있습니다.

| 작업 | Type |
|---------|---------|
| Blob 저장소 데이터가 변경될 때 함수 실행 | [트리거](./functions-bindings-storage-blob-trigger.md) |
| 함수에서 Blob 저장소 데이터 읽기 | [입력 바인딩](./functions-bindings-storage-blob-input.md) |
| 함수가 Blob 저장소 데이터를 작성할 수 있도록 허용 |[출력 바인딩](./functions-bindings-storage-blob-output.md) |

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
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[확장 프로그램 업데이트]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 자동으로 참조를 가지고 [있습니다.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs)

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>다음 단계

- [Blob 저장소 데이터가 변경될 때 함수 실행](./functions-bindings-storage-blob-trigger.md)
- [함수가 실행될 때 Blob 저장소 데이터 읽기](./functions-bindings-storage-blob-input.md)
- [함수에서 Blob 저장소 데이터 쓰기](./functions-bindings-storage-blob-output.md)
