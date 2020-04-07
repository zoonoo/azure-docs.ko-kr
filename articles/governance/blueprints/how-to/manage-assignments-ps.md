---
title: PowerShell으로 과제를 관리하는 방법
description: 공식 Azure BlueprintPowerShell 모듈인 Az.Blueprint를 사용하여 청사진 할당을 관리하는 방법을 알아봅니다.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677437"
---
# <a name="how-to-manage-assignments-with-powershell"></a>PowerShell으로 과제를 관리하는 방법

**Az.Blueprint** Azure PowerShell 모듈을 사용하여 Blueprint 할당을 관리할 수 있습니다. 이 모듈은 할당 가져오기, 생성, 업데이트 및 제거를 지원합니다. 모듈은 기존 Blueprint 정의에 대한 세부 정보를 가져올 수도 있습니다. 이 문서에서는 모듈을 설치하고 사용을 시작하는 방법을 설명합니다.

## <a name="add-the-azblueprint-module"></a>Az.Blueprint 모듈 추가

Azure PowerShell에서 Blueprint 할당을 관리하려면 모듈을 추가해야 합니다. [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell/)와 함께 로컬로 설치된 PowerShell로 이 모듈을 사용할 수 있습니다.

### <a name="base-requirements"></a>기본 요구 사항

Azure Blueprints 모듈에는 다음 소프트웨어가 필요합니다.

- Azure PowerShell 1.5.0 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
- PowerShellGet 2.0.1 이상 설치되거나 업데이트되지 않은 경우 [다음 지침](/powershell/scripting/gallery/installing-psget)을 따릅니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell용 Azure Blueprint 모듈은 **Az.Blueprint입니다.**

1. **관리자** PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > **Az.Accounts가** 이미 설치되어 있는 경우 `-AllowClobber` 설치를 강제로 사용해야 할 수 있습니다.

1. 모듈을 가져왔고 올바른 버전(0.2.6)이 맞는지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>청사진 정의 받기

할당 작업을 위한 첫 번째 단계는 종종 청사진 정의에 대한 참조를 얻는 것입니다.
cmdlet은 `Get-AzBlueprint` 하나 이상의 Blueprint 정의를 가져옵니다. cmdlet은 을 가진 `-ManagementGroupId {mgId}` 관리 그룹 또는 구독에서 `-SubscriptionId {subId}`Blueprint 정의를 얻을 수 있습니다. **Name** 매개 변수는 Blueprint 정의를 얻지만 **ManagementGroupId** 또는 **SubscriptionId**. **버전은** **Name과** 함께 사용하여 반환되는 Blueprint 정의에 대해 더 명시적으로 지정할 수 있습니다. **버전**대신 스위치가 `-LatestPublished` 가장 최근에 게시된 버전을 잡습니다.

다음 예제에서는 `Get-AzBlueprint` 다음과 같이 `{subId}`표시된 특정 구독에서 '101-Blueprints-정의-구독'이라는 이름의 모든 버전의 Blueprint 정의를 가져옵니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

여러 버전이 있는 Blueprint 정의의 예제 출력은 다음과 같습니다.

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

청사진 정의의 [청사진 매개변수를](../concepts/parameters.md#blueprint-parameters) 확장하여 자세한 정보를 제공할 수 있습니다.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>청사진 과제 받기

Blueprint 할당이 이미 있는 경우 cmdlet을 `Get-AzBlueprintAssignment` 사용하여 Blueprint 할당에 대한 참조를 얻을 수 있습니다. cmdlet은 **SubscriptionId** 및 **Name을** 선택적 매개 변수로 사용합니다. **SubscriptionId를** 지정하지 않으면 현재 구독 컨텍스트가 사용됩니다.

다음 예제에서는 `Get-AzBlueprintAssignment` 다음과 같이 `{subId}`표시된 특정 구독에서 '할당 잠금 리소스 그룹'이라는 단일 Blueprint 할당을 얻는 데 사용됩니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Blueprint 할당의 예제 출력은 다음과 같습니다.

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>청사진 할당 생성

Blueprint 할당이 아직 존재하지 않는 경우 cmdlet을 사용하여 Blueprint 할당을 `New-AzBlueprintAssignment` 만들 수 있습니다. 이 cmdlet은 다음 매개 변수를 사용합니다.

- **이름** [필수]
  - Blueprint 할당 의 이름을 지정합니다.
  - 고유해야 하며 **구독Id에** 아직 존재하지 않아야 합니다.
- **청사진** [필수]
  - 할당할 Blueprint 정의를 지정합니다.
  - 참조 `Get-AzBlueprint` 개체를 얻는 데 사용
- **위치** [필수]
  - 에서 만들 시스템 할당관리 ID 및 구독 배포 개체에 대한 지역 지정
- **구독(선택** 사항)
  - 할당이 배포된 구독을 지정합니다.
  - 제공되지 않으면 현재 구독 컨텍스트에 기본값이 설정됩니다.
- **잠금(선택** 사항)
  - 배포된 [리소스에](../concepts/resource-locking.md) 사용할 Blueprint 리소스 잠금 정의
  - 지원되는 옵션: _없음,_ _AllResourcesReadOnly,_ _AllResourcesDoNotDelete_
  - 제공되지 않으면 기본값은 _없음으로_ 설정됩니다.
- **시스템할당된 정체성(선택** 사항)
  - 할당에 대해 시스템 할당된 관리 ID를 만들고 리소스를 배포하려면 선택합니다.
  - "ID" 매개 변수 집합에 대한 기본값
  - **사용자 할당된 ID와** 함께 사용할 수 없습니다.
- **사용자 할당된 ID(선택** 사항)
  - 할당에 사용하고 리소스를 배포할 사용자 할당관리 ID를 지정합니다.
  - "ID" 매개 변수 집합의 일부
  - **시스템 할당된 ID에서** 사용할 수 없습니다.
- **매개 변수(선택** 사항)
  - Blueprint 할당에서 [동적 매개변수를](../concepts/parameters.md#dynamic-parameters) 설정하기 위한 키/값 쌍의 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 동적 매개 변수의 기본값은 정의의 **defaultValue입니다.**
  - 매개 변수가 제공되지 않고 **defaultValue가**없는 경우 매개 변수는 선택 사항이 아닙니다.

    > [!NOTE]
    > **매개 변수는** secureStrings를 지원하지 않습니다.

- **리소스 그룹매개 변수(선택** 사항)
  - 리소스 그룹 아티팩트의 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 각 리소스 그룹 아티팩트 자리 표시자는 해당 리소스 그룹 아티팩트에서 **이름** 및 **위치를** 동적으로 설정하기 위한 키/값 쌍을 가지고 있습니다.
  - 리소스 그룹 매개 변수가 제공되지 않고 **defaultValue가**없는 경우 리소스 그룹 매개 변수는 선택 사항이 아닙니다.
- **과제 파일(선택** 사항)
  - 청사진 할당의 JSON 파일 표현 경로
  - 이 매개 변수는 **이름,** **Blueprint**및 **SubscriptionId**및 일반 매개 변수만 포함하는 PowerShell 매개 변수 집합의 일부입니다.

### <a name="example-1-provide-parameters"></a>예제 1: 매개 변수 제공

다음 예제에서는 가져온 'my-Blueprint' Blueprint 정의의 버전 '1.1'의 `Get-AzBlueprint`새 할당을 만들고, 관리되는 ID 및 할당 개체 위치를 'westus2'로 _설정하고, AllResourcesReadOnly를_사용하여 리소스를 `{subId}`잠그고, 매개 **변수** 및 **ResourceGroupParameter에** 대한 해시 테이블을 다음과 같이 표현합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

청사진 할당을 만들기 위한 예제 출력은 다음과 같습니다.

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>예제 2: JSON 할당 정의 파일 사용

다음 예제는 [예제 1과](#example-1-provide-parameters)거의 동일한 할당을 만듭니다.
이 예제에서는 매개 변수를 cmdlet에 전달하는 대신 JSON 할당 정의 파일 및 **AssignmentFile** 매개 변수의 사용을 보여 주었습니다. 또한 **제외된Principals** 속성은 **잠금의**일부로 구성됩니다. 제외에 대한 PowerShell 매개 **변수가 없으며주며** JSON 할당 정의 파일을 통해 설정해야만 속성을 구성할 수 있습니다.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

사용자 할당된 관리 ID에 대한 JSON 할당 정의 파일의 예는 예: REST API에 대해 [사용자가 할당한 관리 ID가 있는 할당의](/rest/api/blueprints/assignments/createorupdate#examples) 요청 본문을 참조하십시오.

## <a name="update-blueprint-assignments"></a>청사진 할당 업데이트

경우에 따라 이미 생성된 Blueprint 할당을 업데이트해야 합니다. cmdlet은 `Set-AzBlueprintAssignment` 이 작업을 처리합니다. cmdlet은 `New-AzBlueprintAssignment` cmdlet이 수행하는 것과 동일한 매개 변수의 대부분을 차지하므로 할당에 설정된 모든 매개 변수를 업데이트할 수 있습니다. 이름은 , _Blueprint_및 _SubscriptionId입니다._ _Name_ 제공된 값만 업데이트됩니다.

Blueprint 할당을 업데이트할 때 발생하는 일을 이해하려면 [할당 업데이트 에 대한 규칙을 참조하세요.](./update-existing-assignments.md#rules-for-updating-assignments)

- **이름** [필수]
  - 업데이트할 Blueprint 할당의 이름을 지정합니다.
  - 할당을 변경하지 않고 업데이트할 할당을 찾는 데 사용됩니다.
- **청사진** [필수]
  - 청사진 할당의 청사진 정의를 지정합니다.
  - 참조 `Get-AzBlueprint` 개체를 얻는 데 사용
  - 할당을 변경하지 않고 업데이트할 할당을 찾는 데 사용됩니다.
- **위치(선택** 사항)
  - 에서 만들 시스템 할당관리 ID 및 구독 배포 개체에 대한 지역 지정
- **구독(선택** 사항)
  - 할당이 배포된 구독을 지정합니다.
  - 제공되지 않으면 현재 구독 컨텍스트에 기본값이 설정됩니다.
  - 할당을 변경하지 않고 업데이트할 할당을 찾는 데 사용됩니다.
- **잠금(선택** 사항)
  - 배포된 [리소스에](../concepts/resource-locking.md) 사용할 Blueprint 리소스 잠금 정의
  - 지원되는 옵션: _없음,_ _AllResourcesReadOnly,_ _AllResourcesDoNotDelete_
- **시스템할당된 정체성(선택** 사항)
  - 할당에 대해 시스템 할당된 관리 ID를 만들고 리소스를 배포하려면 선택합니다.
  - "ID" 매개 변수 집합에 대한 기본값
  - **사용자 할당된 ID와** 함께 사용할 수 없습니다.
- **사용자 할당된 ID(선택** 사항)
  - 할당에 사용하고 리소스를 배포할 사용자 할당관리 ID를 지정합니다.
  - "ID" 매개 변수 집합의 일부
  - **시스템 할당된 ID에서** 사용할 수 없습니다.
- **매개 변수(선택** 사항)
  - Blueprint 할당에서 [동적 매개변수를](../concepts/parameters.md#dynamic-parameters) 설정하기 위한 키/값 쌍의 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 동적 매개 변수의 기본값은 정의의 **defaultValue입니다.**
  - 매개 변수가 제공되지 않고 **defaultValue가**없는 경우 매개 변수는 선택 사항이 아닙니다.

    > [!NOTE]
    > **매개 변수는** secureStrings를 지원하지 않습니다.

- **리소스 그룹매개 변수(선택** 사항)
  - 리소스 그룹 아티팩트의 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 각 리소스 그룹 아티팩트 자리 표시자는 해당 리소스 그룹 아티팩트에서 **이름** 및 **위치를** 동적으로 설정하기 위한 키/값 쌍을 가지고 있습니다.
  - 리소스 그룹 매개 변수가 제공되지 않고 **defaultValue가**없는 경우 리소스 그룹 매개 변수는 선택 사항이 아닙니다.

다음 예제는 잠금 모드를 `Get-AzBlueprint` 변경하여 가져온 'my-Blueprint' 청사진 정의의 버전 '1.1'의 할당을 업데이트합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

청사진 할당을 만들기 위한 예제 출력은 다음과 같습니다.

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>청사진 할당 제거

Blueprint 할당을 제거할 때가 되면 `Remove-AzBlueprintAssignment` cmdlet에서 이 작업을 처리합니다. cmdlet은 **이름을** 지정하거나 **InputObject를** 사용하여 제거할 Blueprint 할당을 지정합니다. **SubscriptionId는** _필수이며_ 모든 경우에 제공되어야 합니다.

다음 예제는 기존 Blueprint 할당을 `Get-AzBlueprintAssignment` 가져온 다음 다음과 같이 `{subId}`표시된 특정 구독에서 제거합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>종단 간 코드 예제

다음 예제에서는 모든 단계를 함께 가져오면 Blueprint 정의가 가져온 다음 다음과 같이 `{subId}`표시된 특정 구독에서 Blueprint 할당을 생성, 업데이트 및 제거합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기에](../concepts/lifecycle.md)대해 자세히 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결