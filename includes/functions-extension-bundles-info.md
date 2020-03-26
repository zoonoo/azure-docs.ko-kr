---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201024"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> 시작하는 동안 호스트는 [Storage 바인딩 확장](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) 및 기타 Microsoft 바인딩 확장을 다운로드하여 설치합니다. 이 설치는 *host.json* 파일에서 기본적으로 다음 속성을 사용하여 바인딩 확장을 사용하도록 설정되어 있기 때문에 발생합니다.
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> 바인딩 확장과 관련된 오류가 발생하면 위의 속성이 *host.json*에 있는지 확인하세요.
::: zone-end  