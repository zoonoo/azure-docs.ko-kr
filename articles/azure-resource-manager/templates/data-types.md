---
title: 템플릿의 데이터 형식
description: Azure Resource Manager 템플릿에서 사용할 수 있는 데이터 형식에 대해 설명 합니다.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125143"
---
# <a name="data-types-in-arm-templates"></a>ARM 템플릿의 데이터 형식

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿)에서 지원 되는 데이터 형식에 대해 설명 합니다. JSON 및 Bicep 데이터 형식을 모두 포함 합니다.

## <a name="supported-types"></a>지원되는 형식

ARM 템플릿 내에서 다음 데이터 형식을 사용할 수 있습니다.

* array
* bool
* int
* 개체
* secureObject-Bicep에서 한정자로 표시 됨
* secureString-Bicep에서 한정자로 표시 됨
* 문자열

## <a name="arrays"></a>배열

배열은 왼쪽 대괄호 ()로 시작 `[` 하 고 오른쪽 대괄호 ()로 끝납니다 `]` .

JSON에서 배열은 한 줄 또는 여러 줄로 선언할 수 있습니다. 각 요소는 쉼표로 구분 됩니다.

Bicep에서는 배열을 여러 줄로 선언 해야 합니다. 값 사이에 쉼표를 사용 하지 마세요.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

배열의 요소는 형식이 나 형식이 다를 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>부울

부울 값을 지정 하는 경우 또는를 사용 `true` `false` 합니다. 값을 따옴표로 묶지 마세요.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>정수

정수 값을 지정할 때는 따옴표를 사용 하지 마십시오.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

인라인 매개 변수로 전달 된 정수의 경우 배포에 사용 하는 SDK 또는 명령줄 도구를 사용 하 여 값 범위를 제한할 수 있습니다. 예를 들어 PowerShell을 사용 하 여 템플릿을 배포 하는 경우 정수 형식의 범위는-2147483648 ~ 2147483647 일 수 있습니다. 이러한 제한을 방지 하려면 [매개 변수 파일](parameter-files.md)에 큰 정수 값을 지정 합니다. 리소스 형식은 정수 속성에 대해 고유한 제한을 적용 합니다.

## <a name="objects"></a>개체

개체는 왼쪽 중괄호 ()로 시작 `{` 하 고 오른쪽 중괄호 ()로 끝납니다 `}` . 개체의 각 속성은 키와 값으로 구성 됩니다. 키와 값은 콜론 ()으로 구분 됩니다 `:` .

JSON에서 키는 큰따옴표로 묶여 있습니다. 각 속성은 쉼표로 구분 됩니다.

Bicep에서는 키가 따옴표로 묶여 있지 않습니다. 속성 사이에 쉼표를 사용 하지 마세요.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>문자열

JSON에서 문자열은 큰따옴표로 표시 됩니다. Bicep에서 문자열은 서명 된과 따옴표를 사용 하 여 표시 됩니다.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>보안 문자열 및 개체

보안 문자열은 문자열과 동일한 형식을 사용 하 고 보안 개체는 개체와 동일한 형식을 사용 합니다. 매개 변수를 보안 문자열 또는 보안 개체에 설정 하면 매개 변수 값이 배포 기록에 저장 되지 않고 기록 되지 않습니다. 그러나 보안 값을 예상 하지 않는 속성에 해당 보안 값을 설정 하면 값이 보호 되지 않습니다. 예를 들어 보안 문자열을 태그로 설정 하면 해당 값이 일반 텍스트로 저장 됩니다. 암호 및 암호에 보안 문자열을 사용 합니다.

Bicep를 사용 하 여 `@secure()` 문자열 또는 개체에 한정자를 추가 합니다.

다음 예제에서는 두 개의 보안 매개 변수를 보여 줍니다.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>다음 단계

템플릿 구문에 대 한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
