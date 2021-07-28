---
title: 템플릿의 데이터 형식
description: Azure Resource Manager 템플릿에서 사용할 수 있는 데이터 형식을 설명합니다.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 05/07/2021
ms.openlocfilehash: 4905dbe184301ea9bcf86d63d527d07276a95c9e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111957938"
---
# <a name="data-types-in-arm-templates"></a>ARM 템플릿의 데이터 형식

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 지원되는 데이터 형식을 설명합니다.

## <a name="supported-types"></a>지원되는 형식

ARM 템플릿 내에서 다음 데이터 형식을 사용할 수 있습니다.

* array
* 부울
* int
* object
* secureObject
* secureString
* 문자열

## <a name="arrays"></a>배열

배열은 왼쪽 대괄호(`[`)로 시작하고 오른쪽 대괄호(`]`)로 끝납니다. 배열은 한 줄 또는 여러 줄로 선언할 수 있습니다. 각 요소는 쉼표로 구분합니다.

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

배열의 요소는 동일한 형식이거나 다른 형식일 수 있습니다.

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

## <a name="booleans"></a>부울

부울 값을 지정하는 경우 `true` 또는 `false`를 사용합니다. 값을 따옴표로 묶지 마세요.

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

## <a name="integers"></a>정수

정수 값을 지정하는 경우 따옴표를 사용하지 마세요.

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

인라인 매개 변수로 전달된 정수의 경우 값 범위는 배포에 사용하는 SDK 또는 명령줄 도구를 통해 제한될 수 있습니다. 예를 들어, PowerShell을 사용하여 템플릿을 배포하는 경우 정수 형식의 범위는 -2147483648~2147483647일 수 있습니다. 이 제한을 피하려면 [매개 변수 파일](parameter-files.md)에 큰 정수 값을 지정합니다. 리소스 종류는 정수 속성에 대해 고유한 한도를 적용합니다.

## <a name="objects"></a>개체

개체는 왼쪽 중괄호(`{`)로 시작하고 오른쪽 중괄호(`}`)로 끝납니다. 개체의 각 속성은 `key` 및 `value`로 구성됩니다. `key` 및 `value`는 큰따옴표로 묶고 콜론(`:`)으로 구분합니다. 각 속성은 쉼표로 구분합니다.

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

## <a name="strings"></a>문자열

문자열은 큰따옴표를 사용하여 표시합니다.

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

## <a name="secure-strings-and-objects"></a>보안 문자열 및 개체

보안 문자열은 문자열과 동일한 형식을 사용하고 보안 개체는 개체와 동일한 형식을 사용합니다. 매개 변수를 보안 문자열 또는 보안 개체로 설정하면 매개 변수 값이 배포 기록에 저장되지 않고 로그되지 않습니다. 그러나 해당 보안 값을 보안 값이 필요하지 않은 속성으로 설정하면 값이 보호되지 않습니다. 예를 들어, 보안 문자열을 태그로 설정하면 해당 값이 일반 텍스트로 저장됩니다. 암호 및 비밀에 보안 문자열을 사용합니다.

다음 예에서는 두 개의 보안 매개 변수를 보여줍니다.

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

## <a name="next-steps"></a>다음 단계

템플릿 구문에 관해 자세히 알아보려면 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.