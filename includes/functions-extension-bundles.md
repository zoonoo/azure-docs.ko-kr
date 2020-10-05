---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "68639165"
---
바인딩 확장을 설치하는 가장 쉬운 방법은 [확장 번들](../articles/azure-functions/functions-bindings-register.md#extension-bundles)을 사용하도록 설정하는 것입니다. 번들을 활성화하면 미리 정의된 확장 패키지 세트가 자동으로 설치됩니다.

확장 번들을 사용하도록 설정하려면 host.json 파일을 열고 다음 코드와 일치하도록 콘텐츠를 업데이트합니다.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```