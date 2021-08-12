---
title: Bicep에서 출력 값의 여러 인스턴스 정의
description: Bicep 출력 루프를 사용하여 배포 값을 반복하고 반환합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: cc931b7e0d65804892176a2965f87022a2becb7b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026754"
---
# <a name="output-iteration-in-bicep"></a>Bicep의 출력 반복

이 문서에서는 Bicep 파일에서 출력에 대한 값을 두 개 이상 만드는 방법을 보여 줍니다. 파일의 `output` 섹션에 루프를 추가하고, 배포하는 동안 여러 항목을 동적으로 반환할 수 있습니다.

[리소스](loop-resources.md), [리소스의 속성](loop-properties.md), [변수](loop-variables.md)와 함께 루프를 사용할 수도 있습니다.

## <a name="syntax"></a>구문

배포하는 동안 여러 항목을 반환하려면 루프를 사용할 수 있습니다.

- 배열 반복.

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- 배열의 요소 반복.

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- 루프 인덱스 사용.

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다. Bicep 파일을 배포하려면 최신 버전의 [Bicep 도구](install.md)를 설치합니다.

## <a name="output-iteration"></a>출력 반복

다음 예제에서는 다양한 수의 스토리지 계정을 만들고 각 스토리지 계정에 대한 엔드포인트를 반환합니다.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageEndpoints array = [for i in range(0, storageCount): reference('${i}${baseNameVar}').primaryEndpoints.blob]
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

이 예제에서는 새 스토리지 계정에서 세 가지 속성을 반환합니다.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

var baseNameVar = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}${baseNameVar}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference('${i}${baseNameVar}', '2021-02-01', 'Full').resourceId
  blobEndpoint: reference('${i}${baseNameVar}').primaryEndpoints.blob
  status: reference('${i}${baseNameVar}').statusOfPrimary
}]
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

리소스 모듈 또는 모듈 컬렉션에 대한 직접 참조가 출력 루프에서 지원되지 않으므로 이 예제에서는 배열 인덱스를 사용합니다.

```bicep
param rgLocation string = resourceGroup().location

var stgNames = [
  'demostg1'
  'demostg2'
  'demostg3'
]

resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in stgNames: {
  name: name
  location: rgLocation
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
}]

output stgOutput array = [for (name, i) in stgNames: {
  name: stg[i].name
  resourceId: stg[i].id
}]
```

출력은 다음 값을 가진 배열을 반환합니다.

```json
[
  {
    "name": "demostg1",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg1"
  },
  {
    "name": "demostg2",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg2"
  },
  {
    "name": "demostg3",
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/demostg3"
  }
]
```

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep 파일의 리소스 반복](loop-resources.md)
  - [Bicep 파일의 속성 반복](loop-properties.md)
  - [Bicep 파일의 변수 반복](loop-variables.md)
- Bicep 파일의 섹션에 대해 알아보려면 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
- 여러 리소스를 배포하는 방법에 대한 자세한 내용은 [Bicep 모듈 사용](modules.md)을 참조하세요.
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.
- PowerShell을 사용하여 배포하는 방법을 알아보려면 [Bicep 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
- Azure CLI를 사용하여 배포하는 방법을 알아보려면 [Bicep 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)를 참조하세요.
