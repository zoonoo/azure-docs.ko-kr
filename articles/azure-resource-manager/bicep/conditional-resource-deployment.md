---
title: Bicep을 사용하여 조건부 배포
description: Bicep에서 리소스를 조건부로 배포하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 9636444af81b000443dc72cf6e3fc1d8d6da5093
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537079"
---
# <a name="conditional-deployment-in-bicep"></a>Bicep의 조건부 배포

Bicep에서 리소스를 선택적으로 배포해야 하는 경우도 있습니다. 리소스 배포 여부를 지정하려면 `if` 키워드를 사용합니다. 조건 값은 true 또는 false로 확인됩니다. 값이 true이면 리소스가 만들어집니다. 값이 false이면 리소스가 만들어지지 않습니다. 값은 전체 리소스에만 적용할 수 있습니다.

> [!NOTE]
> 조건부 배포는 [하위 리소스](child-resource-name-type.md)로 연결되지 않습니다. 리소스 및 해당 자식 리소스를 조건부로 배포하려면 각 리소스 종류에 동일한 조건을 적용해야 합니다.

## <a name="deploy-condition"></a>배포 조건

리소스 배포 여부를 나타내는 매개 변수 값을 전달할 수 있습니다. 다음 예제에서는 조건부로 DNS 영역을 배포합니다.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

조건은 종속성 선언에 사용할 수 있습니다. 조건부 리소스의 식별자가 다른 리소스의 `dependsOn`에 지정된 경우(명시적 종속성) 템플릿 배포 시 조건이 false로 평가되면 종속성이 무시됩니다. 조건이 true로 평가되면 종속성이 반영됩니다. 조건부 리소스(암시적 종속성)의 속성을 참조하는 것은 허용되지만 런타임 오류가 발생하는 경우도 있습니다.

## <a name="new-or-existing-resource"></a>신규 또는 기존 리소스

조건부 배포를 사용하여 새 리소스를 만들거나 기존 리소스를 사용할 수 있습니다. 다음 예에서는 새 스토리지 계정을 배포하거나 기존 스토리지 계정을 사용하는 방법을 보여 줍니다.

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

`newOrExisting` 매개 변수가 **new** 로 설정되면 조건이 true로 평가됩니다. 스토리지 계정이 배포됩니다. 그러나 `newOrExisting`이 **existing** 으로 설정되면 조건이 false로 평가되고 스토리지 계정이 배포되지 않습니다.

`condition` 요소를 사용하는 전체 예제 템플릿은 [신규 또는 기존 가상 네트워크, 스토리지 및 공용 IP를 사용하는 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-new-or-existing-conditions)을 참조하세요.

## <a name="runtime-functions"></a>런타임 기능

조건부로 배포된 리소스와 함께 [reference](./bicep-functions-resource.md#reference) 또는 [list](./bicep-functions-resource.md#list) 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다. 함수가 존재하지 않는 리소스를 참조하면 오류가 발생합니다.

[조건식?:](./operators-logical.md#conditional-expression--) 연산자를 사용하여 리소스가 배포될 때 함수가 조건에 대해서만 평가되도록 합니다. 다음 예제 템플릿에서는 조건부로 유효한 식에서만 이 함수를 사용하는 방법을 보여 줍니다.

```bicep
param vmName string
param location string
param logAnalytics string = ''

resource vmName_omsOnboarding 'Microsoft.Compute/virtualMachines/extensions@2017-03-30' = if (!empty(logAnalytics)) {
  name: '${vmName}/omsOnboarding'
  location: location
  properties: {
    publisher: 'Microsoft.EnterpriseCloud.Monitoring'
    type: 'MicrosoftMonitoringAgent'
    typeHandlerVersion: '1.0'
    autoUpgradeMinorVersion: true
    settings: {
      workspaceId: ((!empty(logAnalytics)) ? reference(logAnalytics, '2015-11-01-preview').customerId : json('null'))
    }
    protectedSettings: {
      workspaceKey: ((!empty(logAnalytics)) ? listKeys(logAnalytics, '2015-11-01-preview').primarySharedKey : json('null'))
    }
  }
}

output mgmtStatus string = ((!empty(logAnalytics)) ? 'Enabled monitoring for VM!' : 'Nothing to enable')
```

다른 리소스와 마찬가지로 조건부 리소스에 대해 [리소스를 종속 리소스로 설정](./resource-declaration.md#set-resource-dependencies)합니다. 조건부 리소스가 배포되지 않으면 Azure Resource Manager가 필요한 종속성에서 해당 리소스를 자동으로 제거합니다.

## <a name="next-steps"></a>다음 단계

* 조건 및 루프에 대한 Microsoft Learn 모듈은 [조건 및 루프를 사용하여 유연한 Bicep 템플릿 빌드](/learn/modules/build-flexible-bicep-templates-conditions-loops/)를 참조하세요.
* Bicep 파일 만들기에 대한 권장 사항은 [Bicep 모범 사례](best-practices.md)를 참조하세요.
* 리소스의 여러 인스턴스를 만들려면 [Bicep의 리소스 반복](loop-resources.md)을 참조하세요.
