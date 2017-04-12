---
title: "PowerShell을 사용한 Azure 솔루션 관리 | Microsoft Docs"
description: "Azure PowerShell 및 Resource Manager를 사용하여 리소스를 관리합니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 407e9a1e4a50b875fa65e61d3e9aae245dd907e5
ms.lasthandoff: 03/04/2017


---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Azure PowerShell 및 Resource Manager를 사용하여 리소스 관리
> [!div class="op_single_selector"]
> * [포털](resource-group-portal.md)
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [REST API](resource-manager-rest-api.md)
>
>

이 항목에서는 Azure PowerShell 및 Azure Resource Manager를 사용하여 솔루션을 관리하는 방법을 알아봅니다. Resource Manager에 익숙하지 않은 경우에는 [Resource Manager 개요](resource-group-overview.md) 참조하세요. 이 항목에서는 관리 작업에 중점을 둡니다. 다음을 수행합니다.

1. 리소스 그룹 만들기
2. 리소스 그룹에 리소스 추가
3. 리소스에 태그 추가
4. 이름 또는 태그 값을 기반으로 리소스 쿼리
5. 리소스에 대하 잠금 적용 및 제거
6. 리소스 그룹에서 Resource Manager 템플릿 만들기
7. 리소스 그룹 삭제

## <a name="get-started-with-azure-powershell"></a>Azure PowerShell 시작

Azure PowerShell을 설치하지 않은 경우 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

Azure PowerShell을 전에 설치했지만 최근에 업데이트하지 않은 경우에는 최신 버전을 설치하는 것이 좋습니다. 설치하는 방법과 동일한 방법을 통해 버전을 업데이트할 수 있습니다. 예를 들어 웹 플랫폼 설치 관리자를 사용한 경우에는 관리자를 다시 시작하고 업데이트를 찾아봅니다.

Azure 리소스 모듈의 버전을 확인하려면 다음 cmdlet을 사용합니다.

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

이 항목은 버전 3.3.0에 대해 업데이트되었습니다. 이보다 오래된 버전이 설치되어 있는 경우에는 사용자의 환경이 이 항목에 표시되는 단계와 일치하지 않을 수 있습니다. 이 버전의 cmdlet에 대한 설명서는 [AzureRM.Resources Module](/en-us/powershell/resourcemanager/azurerm.resources/v3.3.0/azurerm.resources)을 참조하세요.

## <a name="log-in-to-your-azure-account"></a>Azure 계정에 로그인합니다.
솔루션에서 작업하기 전에 자신의 계정으로 로그인해야 합니다.

Azure 계정에 로그인하려면 **Login-AzureRmAccount** Cmdlet을 사용합니다.

```powershell
Login-AzureRmAccount
```

Cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

이 cmdlet은 작업에 사용할 구독과 계정에 대한 정보를 반환합니다.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

구독이 둘 이상인 경우에는 다른 구독으로 전환할 수 있습니다. 우선 계정에 대한 모든 구독을 살펴보겠습니다.

```powershell
Get-AzureRmSubscription
```

사용되는 구독 및 사용이 해제된 구독을 반환합니다.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

다른 구독으로 전환하려면 **Set-AzureRmContext** cmdlet에 구독 이름을 제공합니다.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
구독에 리소스를 배포하려면 리소스를 포함하는 리소스 그룹을 만들어야 합니다.

리소스 그룹을 만들려면 **New-AzureRmResourceGroup** Cmdlet을 사용합니다. 이 명령은 **Name** 매개 변수를 사용하여 리소스 그룹에 대한 이름을 지정하고 **Location** 매개 변수를 사용하여 위치를 지정합니다.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

다음 형식으로 출력됩니다.

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

나중에 리소스 그룹을 검색해야 하는 경우 다음 cmdlet을 사용합니다.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

구독에서 리소스 그룹을 모두 가져오려면 이름을 지정하지 않습니다.

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>리소스 그룹에 리소스 추가
리소스를 리소스 그룹에 추가하려면 **New-AzureRmResource** cmdlet을 사용하거나 만드는 리소스의 종류에 해당하는 cmdlet(예: **New-AzureRmStorageAccount**)을 사용합니다. 리소스의 종류에 해당하는 cmdlet에는 새 리소스에 필요한 속성의 매개 변수가 포함되기 때문에 이 cmdlet을 사용하는 것이 간편합니다. **New-AzureRmResource**를 사용하는 경우 속성을 설정하라는 메시지를 표시하지 않으려면 설정할 속성을 모두 알아야 합니다.

하지만 cmdlet을 통해 리소스를 추가하면 새 리소스가 Resource Manager 템플릿에 존재하지 않기 때문에 나중에 혼동을 일으킬 수 있습니다. Azure 솔루션에 대한 인프라는 Resource Manager 템플릿에서 구성하는 것이 좋습니다. 템플릿을 사용하면 솔루션을 안정적이고 반복적으로 배포할 수 있습니다. 이 항목에서는 Resource Manager 템플릿을 구독에 배포하는 방법을 보여주지 않습니다. 해당 정보는 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](resource-group-template-deploy.md)를 참조하세요. 이 항목에서는 PowerShell cmdlet을 사용하여 저장소 계정을 만들지만 나중에 리소스 그룹에서 템플릿을 생성합니다.

다음 cmdlet은 저장소 계정을 만듭니다. 예제에 표시된 이름을 사용하는 대신 저장소 계정에 대한 고유 이름을 제공합니다. 이름은 길이가 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다. 예제에 표시된 이름을 사용하면 해당 이름을 이미 사용 중이기 때문에 오류가 표시됩니다.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

나중에 이 리소스를 검색해야 하는 경우 다음 cmdlet을 사용합니다.

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>태그 추가

태그를 사용하면 다양한 속성에 따라 리소스를 구성할 수 있습니다. 예를 들어 동일한 부서에 속하는 여러 리소스 그룹에 몇 가지 리소스를 둘 수 있습니다. 리소스에 부서 태그 및 값을 적용하여 동일한 범주에 속하는 것으로 표시할 수 있습니다. 또는 리소스가 프로덕션 환경에서 사용되는지 또는 테스트 환경에서 사용되는지를 표시할 수 있습니다. 이 항목에서는 하나의 리소스에만 태그를 적용하지만 사용자 환경에서는 모든 리소스에 태그를 적용하는 것이 대부분 타당합니다.

다음 cmdlet은 저장소 계정에 두 개의 태그를 적용합니다.

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

태그는 단일 개체로 업데이트됩니다. 이미 태그가 포함된 리소스에 태그를 추가하려면 우선 기존 태그를 검색합니다. 기존 태그가 포함된 개체에 새 태그를 추가하고 리소스에 모든 태그를 다시 적용합니다.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>리소스 검색

다른 검색 조건에 대해 리소스를 검색하려면 **Find-AzureRmResource** cmdlet을 사용합니다.

* 이름으로 리소스를 가져오려면 **ResourceNameContains** 매개 변수를 제공합니다.

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* 리소스 그룹의 리소스를 모두 가져오려면 **ResourceGroupNameContains** 매개 변수를 제공합니다.

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* 태그 이름 및 값을 사용하여 리소스를 모두 가져오려면 **TagName** 및 **TagValue** 매개 변수를 제공합니다.

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* 특정 리소스 종류에 해당하는 리소스를 모두 가져오려면 **ResourceType** 매개 변수를 제공합니다.

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="lock-a-resource"></a>리소스 잠금

중요한 리소스가 실수로 삭제되거나 수정되지 않도록 해야 하는 경우에는 리소스에 잠금을 적용합니다. **CanNotDelete** 또는 **ReadOnly**를 지정할 수 있습니다.

관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/*` 또는 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 소유자 및 사용자 액세스 관리자에게만 이러한 작업의 권한이 부여됩니다.

잠금을 적용하려면 다음 cmdlet을 사용합니다.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

앞의 예제에서 잠긴 리소스는 잠금이 제거될 때까지 삭제될 수 없습니다. 잠금을 제거하려면 다음을 사용합니다.

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

잠금 설정에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

## <a name="export-resource-manager-template"></a>Resource Manager 템플릿 내보내기
기존 리소스 그룹(PowerShell 또는 포털을 비롯한 다른 방법 중 하나를 통해 배포된)의 경우 리소스 그룹에 대 한 Resource Manager 템플릿을 볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

1. 모든 인프라가 템플릿에 정의되어 있기 때문에 향후 솔루션 배포를 간단하게 자동화할 수 있습니다.
2. 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해질 수 있습니다.

> [!NOTE]
> 템플릿 내보내기 기능은 미리 보기 버전이며, 템플릿 내보내기를 지원하지 않는 리소스 유형도 있습니다. 템플릿 내보내기를 시도할 때 일부 리소스를 내보내지 못했다는 오류가 표시될 수 있습니다. 필요한 경우 템플릿을 다운로드한 후 템플릿에서 이러한 리소스를 수동으로 정의할 수 있습니다.
>
>

리소스 그룹에 대한 템플릿을 보려면 **Export-AzureRmResourceGroup** Cmdlet을 실행합니다.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
```

Resource Manager 템플릿을 내보내는 옵션과 시나리오는 많이 있습니다. 자세한 내용은 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.

## <a name="remove-resources-or-resource-group"></a>리소스 또는 리소스 그룹 제거
리소스 또는 리소스 그룹을 제거할 수 있습니다. 리소스 그룹을 제거하면 리소스 그룹에 포함된 리소스도 모두 제거됩니다.

* 리소스 그룹에서 리소스를 삭제하려면 **Remove-AzureRmResource** cmdlet를 사용합니다. 이 cmdlet은 리소스를 삭제하지만 리소스 그룹은 삭제하지 않습니다.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* 리소스 그룹과 거기에 포함된 리소스를 모두 삭제하려면 **Remove-AzureRmResourceGroup** cmdlet을 사용합니다.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

두 cmdlet 모두, 리소스 또는 리소스 그룹을 제거할지를 묻는 메시지가 표시됩니다. 작업에서 리소스 또는 리소스 그룹이 성공적으로 삭제되면 **True**가 반환됩니다.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Azure Automation을 사용하여 Resource Manager 스크립트 실행

이 항목은 Azure PowerShell을 사용하여 리소스에 기본적인 작업을 수행하는 방법을 보여줍니다. 고급 관리 시나리오의 경우 일반적으로 스크립트를 만들고 필요에 따라 또는 일정에 따라 스크립트를 다시 사용합니다. [Azure Automation](../automation/automation-intro.md)은 Azure 솔루션을 관리하는 자주 사용되는 스크립트를 자동화하는 방법을 제공합니다.

다음 항목은 Azure Automation, Resource Manager 및 PowerShell을 사용하여 관리 작업을 효과적으로 수행하는 방법을 보여줍니다.

- Runbook 만들기에 대한 내용은 [내 첫 번째 PowerShell Runbook](../automation/automation-first-runbook-textual-powershell.md)을 참조하세요.
- 스크립트 갤러리 작업에 대한 내용은 [Azure Automation용 Runbook 및 모듈 갤러리](../automation/automation-runbook-gallery.md)를 참조하세요.
- 가상 컴퓨터를 시작하고 중지하는 Runbook에 대한 내용은 [Azure Automation 시나리오: JSON 형식 태그를 사용하여 Azure VM 시작 및 종료 일정 만들기](../automation/automation-scenario-start-stop-vm-wjson-tags.md)를 참조하세요.
- 가상 컴퓨터 업무 외 시간을 시작하고 중지하는 Runbook에 대한 내용은 [Automation의 업무 시간 외 VM 시작/중지 솔루션](../automation/automation-solution-vm-management.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* Resource Manager 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 템플릿 배포에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.
* 기존 리소스를 새 리소스 그룹으로 이동할 수 있습니다. 예제를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


