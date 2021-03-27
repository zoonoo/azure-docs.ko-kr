---
title: Bicep 모듈
description: 모듈을 정의 하 고 사용 하는 방법과 모듈 범위를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 7a680e8aa0fa4d5ef9cac7f9e7ba07a3aa4ee1e2
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611738"
---
# <a name="use-bicep-modules-preview"></a>Bicep 모듈 사용 (미리 보기)

Bicep를 사용 하면 복잡 한 솔루션을 모듈로 분할할 수 있습니다. Bicep 모듈은 함께 배포할 하나 이상의 리소스 집합입니다. 모듈은 가독성을 높일 수 있는 원시 리소스 선언의 복잡 한 세부 정보를 추상화 합니다. 이러한 모듈을 다시 사용 하 고 다른 사용자와 공유할 수 있습니다. [템플릿 사양과](./template-specs.md)결합 하 여 모듈화 및 코드 재사용을 위한 방법을 만듭니다. 자습서는 [자습서: Bicep 모듈 추가](./bicep-tutorial-add-modules.md)를 참조 하세요.

## <a name="define-modules"></a>모듈 정의

모든 Bicep 파일을 모듈로 사용할 수 있습니다. 모듈은 매개 변수 및 출력을 다른 Bicep 파일에 대 한 계약 으로만 노출 합니다. 매개 변수와 출력은 모두 선택 사항입니다.

다음 Bicep 파일을 직접 배포 하 여 저장소 계정을 만들거나 모듈로 사용할 수 있습니다.  다음 섹션에서는 모듈을 사용 하는 방법을 보여 줍니다.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

출력은 부모 Bicep 파일에 값을 전달 하는 데 사용 됩니다.

## <a name="consume-modules"></a>모듈 사용

_Module 키워드를_ 사용 하 여 모듈을 사용 합니다. 다음 Bicep 파일은 참조 되는 모듈 파일에 정의 된 리소스를 배포 합니다.

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **모듈**: 키워드.
- **기호화 된 이름** (stgModule): 모듈에 대 한 식별자입니다.
- **모듈 파일**:이 예의 모듈에 대 한 경로는 상대 경로 (./storageAccount.bicep)를 사용 하 여 지정 합니다. 일관된 컴파일 플랫폼 간 사용을 위해 슬래시(/) 디렉터리 구분 기호를 사용하여 Bicep의 모든 경로를 지정해야 합니다. Windows 백슬래시 ( \\ ) 문자는 지원 되지 않습니다.
- 모듈을 사용 하는 경우 **_name_** 속성 (storagedeploy)이 필요 합니다. Bicep에서 템플릿 IL을 생성 하는 경우이 필드는 모듈에 대해 생성 되는 중첩 된 배포 리소스의 이름으로 사용 됩니다.

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

모듈에서 출력 값을 가져오려면 다음과 같은 구문을 사용 하 여 속성 값을 검색 `stgModule.outputs.storageEndpoint` 합니다. 여기서 `stgModule` 은 모듈의 식별자입니다.

## <a name="configure-module-scopes"></a>모듈 범위 구성

모듈을 선언할 때 _scope_ 속성을 제공 하 여 모듈을 배포할 범위를 설정할 수 있습니다.

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

모듈의 대상 범위와 부모의 대상 범위가 같으면 _scope_ 속성을 생략할 수 있습니다. Scope 속성을 제공 하지 않으면 모듈이 부모의 대상 범위에 배포 됩니다.

다음 Bicep 파일은 리소스 그룹을 만들고 리소스 그룹에 모듈을 배포 하는 방법을 보여 줍니다.

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>다음 단계

- 자습서를 진행 하려면 [자습서: Bicep 모듈 추가](./bicep-tutorial-add-modules.md)를 참조 하세요.
