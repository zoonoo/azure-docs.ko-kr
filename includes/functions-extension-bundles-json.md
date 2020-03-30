---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381987"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

다음 속성은 다음 `extensionBundle`에서 사용할 수 있습니다.

| 속성 | 설명 |
| -------- | ----------- |
| id | Microsoft Azure 함수 확장 번들의 네임스페이스입니다. |
| 버전 | 설치할 번들의 버전입니다. 함수 런타임은 항상 버전 범위 또는 간격으로 정의된 최대 허용 버전을 선택합니다. 위의 버전 값을 사용하면 1.0.0부터 2.0.0까지의 모든 번들 버전이 허용되지만 2.0.0은 포함되지 않습니다. 자세한 내용은 버전 [범위 지정에 대한 간격 표기와를](/nuget/reference/package-versioning#version-ranges)참조하십시오. |

번들 버전이 번들 변경의 패키지로 증분됩니다. 주 버전 변경은 번들의 패키지가 주 버전에 의해 증분될 때 발생합니다. 번들의 주 버전 변경은 일반적으로 함수 런타임의 주 버전 변경과 일치합니다.  

기본 번들에 의해 설치된 현재 확장 집합은 이 [extensions.json 파일에](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)함께 표시됩니다.
