---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
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

`extensionBundle`에서 사용할 수 있는 속성은 다음과 같습니다.

| 속성 | Description |
| -------- | ----------- |
| id | Microsoft Azure Functions 확장 번들에 대한 네임스페이스입니다. |
| 버전 | 설치할 번들의 버전입니다. Functions 런타임은 항상 버전 범위 또는 간격으로 정의된 허용 가능한 최대 버전을 선택합니다. 위의 version 값은 1.0.0에서 2.0.0 사이의 모든 번들 버전을 허용하지만 2.0.0은 이 범위에 포함되지 않습니다. 자세한 내용은 [버전 범위를 지정하기 위한 간격 표기법](/nuget/reference/package-versioning#version-ranges)을 참조하세요. |

번들의 패키지가 변경됨에 따라 번들 버전이 증분됩니다. 번들의 패키지가 주 버전에 따라 증분하는 경우 주 버전 변경이 발생합니다. 번들의 주 버전 변경은 일반적으로 Functions 런타임의 주 버전 변경과 일치합니다.  

기본 번들로 설치된 확장의 현재 세트는 이 [extensions.json 파일](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)에 열거되어 있습니다.
