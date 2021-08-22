---
title: Bicep에서 여러 변수 인스턴스 정의
description: Bicep 변수 루프를 사용하여 변수를 만들 때 반복합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 429a15c222e47bab29b314b0d11f7e077281b635
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635005"
---
# <a name="variable-iteration-in-bicep"></a>Bicep의 변수 반복

이 문서에서는 Bicep 파일에서 변수에 대한 값을 두 개 이상 만드는 방법을 보여 줍니다. `variables` 섹션에 루프를 추가하고 배포 중에 변수에 대한 항목 수를 동적으로 설정할 수 있습니다. 또한 Bicep 파일에서 구문이 반복하지 않도록 합니다.

[리소스](loop-resources.md), [리소스의 속성](loop-properties.md) 및 [출력](loop-outputs.md)과 함께 복사를 사용할 수도 있습니다.

## <a name="syntax"></a>Syntax

루프는 다음과 같이 여러 변수를 선언하는 데 사용할 수 있습니다.

- 배열 반복

  ```bicep
  var <variable-name> = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 배열의 요소 반복.

  ```bicep
  var <variable-name> = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- 루프 인덱스 사용.

  ```bicep
  var <variable-name> = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다. Bicep 파일을 배포하려면 최신 버전의 [Bicep 도구](install.md)를 설치합니다.

## <a name="variable-iteration"></a>변수 반복

다음 예제에서는 문자열 값의 배열을 만드는 방법을 보여줍니다.

```bicep
param itemCount int = 5

var stringArray = [for i in range(0, itemCount): 'item${(i + 1)}']

output arrayResult array = stringArray
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

다음 예제에서는 세 가지 속성(`name`, `diskSizeGB` 및 `diskIndex`)이 있는 개체 배열을 만드는 방법을 보여줍니다.

```bicep
param itemCount int = 5

var objectArray = [for i in range(0, itemCount): {
  name: 'myDataDisk${(i + 1)}'
  diskSizeGB: '1'
  diskIndex: i
}]

output arrayResult array = objectArray
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 변수에 대해 둘 이상의 값을 만드는 일반적인 시나리오를 보여줍니다.

|템플릿  |Description  |
|---------|---------|
|[루프 변수](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopvariables.bicep) | 변수를 반복하는 방법을 보여 줍니다. |
|[다중 보안 규칙](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/multiplesecurityrules.bicep) |네트워크 보안 그룹에 여러 보안 규칙을 배포합니다. 매개 변수에서 보안 규칙을 구성합니다. 매개 변수는 [여러 NSG 매개 변수 파일](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/multiplesecurityrules.parameters.json)을 참조합니다. |

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep 파일의 리소스 반복](loop-resources.md)
  - [Bicep 파일의 속성 반복](loop-properties.md)
  - [Bicep 파일의 출력 반복](loop-outputs.md)
- Bicep 파일의 섹션에 대해 알아보려면 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
- 여러 리소스를 배포하는 방법에 대한 자세한 내용은 [Bicep 모듈 사용](modules.md)을 참조하세요.
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.
- PowerShell을 사용하여 배포하는 방법을 알아보려면 [Bicep 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
- Azure CLI를 사용하여 배포하는 방법을 알아보려면 [Bicep 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)를 참조하세요.
