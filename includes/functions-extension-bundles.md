---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442262"
---
바인딩 확장을 설치하는 가장 쉬운 방법은 [확장 번들](../articles/azure-functions/functions-bindings-register.md#extension-bundles)을 사용하도록 설정하는 것입니다. 사용하도록 설정된 번들을 통해 미리 정의된 확장 패키지 집합이 자동으로 설치됩니다.

확장 번들을 사용하도록 설정하려면 *host.json* 파일을 열고 다음 코드와 일치하도록 콘텐츠를 업데이트합니다.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```