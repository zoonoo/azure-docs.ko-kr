---
title: Azure 리소스 잠금으로 변경 방지 | Microsoft Docs
description: 모든 사용자 및 역할에 대해 잠금을 적용하여 사용자가 중요한 Azure 리소스를 업데이트하거나 삭제하지 못하도록 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: a6c7983d22eed4a4232fbb2db490c1743684a04c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65813394"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>예기치 않은 변경을 방지하기 위해 리소스 잠그기 

관리자는 구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 할 수 있습니다. 잠금 수준을 **CanNotDelete** 또는 **ReadOnly**로 설정할 수 있습니다. 포털에서 잠금은 각각 **삭제** 및 **읽기 전용**으로 지칭됩니다.

* **CanNotDelete**는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다. 
* **ReadOnly**는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 **판독기** 역할에서 부여한 권한을 제한하는 것과 유사합니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>잠금을 적용하는 방법

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자와 역할에 걸쳐 제한을 적용합니다. 사용자 및 역할에 대한 권한 설정에 대해 알아보려면 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

리소스 관리자 잠금은 `https://management.azure.com`에 전송된 작업으로 구성되는 관리 수준에서 발생된 작업에만 적용됩니다. 잠금은 리소스 자체 기능을 수행하는 방법을 제한하지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어, SQL Database에 대해 ReadOnly 잠금이 지정되면 데이터베이스를 삭제하거나 수정하지 못합니다. 데이터베이스에서 데이터를 생성, 업데이트 또는 삭제하지 못하게 됩니다. 이러한 작업에 전송 되지 때문에 데이터 전송이 허용 됩니다 `https://management.azure.com`합니다.

적용 **ReadOnly** 것 같지 않습니다. 리소스를 수정 하는 일부 작업 잠금에 의해 차단 되는 작업을 실제로 필요 하기 때문에 예기치 않은 결과가 발생할 수 있습니다. 합니다 **ReadOnly** 잠금 리소스를 포함 하는 리소스 그룹 또는 리소스에 적용할 수 있습니다. 의해 차단 되는 작업의 몇 가지 일반적인 예는 **ReadOnly** 잠금 됩니다.

* A **ReadOnly** 저장소 계정에 대 한 잠금을 키 목록에서 모든 사용자의 것을 금지 합니다. 반환되는 키를 쓰기 작업에 사용할 수 있기 때문에 목록 키 작업은 POST 요청을 통해 처리됩니다.

* App Service 리소스에 **ReadOnly** 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

* A **ReadOnly** 가상 컴퓨터를 포함 하는 리소스 그룹에 대 한 잠금을 모든 사용자가 시작 또는 가상 컴퓨터를 다시 시작 하지 못하도록 합니다. 이러한 작업에 POST 요청을 해야합니다.

## <a name="who-can-create-or-delete-locks"></a>만들거나 수 있는 사람 잠금 삭제
관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/*` 또는 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다.

## <a name="managed-applications-and-locks"></a>관리 되는 응용 프로그램 및 잠금

Azure Databricks와 같은 일부 Azure 서비스를 사용 하 여 [응용 프로그램을 관리 되는](../managed-applications/overview.md) 서비스를 구현 합니다. 이 경우 서비스는 두 리소스 그룹을 만듭니다. 하나의 리소스 그룹 서비스의 개요를 포함 하 고 잠겨 있지 않습니다. 다른 리소스 그룹 서비스에 대 한 인프라 있고 잠겨 있습니다.

인프라 리소스 그룹을 삭제 하려는 경우 리소스 그룹이 잠겨 있음을 나타내는 오류가 표시 됩니다. 인프라 리소스 그룹에 대 한 잠금을 삭제 하려고 하면 오류가 발생 알리는 시스템 응용 프로그램에 의해 소유 하기 때문에 잠금의 삭제할 수 없습니다.

대신, 인프라 리소스 그룹도 삭제 되는 서비스를 삭제 합니다.

관리 되는 응용 프로그램의 경우 배포한 서비스를 선택 합니다.

![서비스를 선택 합니다.](./media/resource-group-lock-resources/select-service.png)

알림 서비스에 대 한 링크가 포함 된 **관리 되는 리소스 그룹**합니다. 해당 리소스 그룹 인프라를 보유 하 고 잠깁니다. 직접 삭제할 수 있습니다.

![관리 되는 그룹 표시](./media/resource-group-lock-resources/show-managed-group.png)

잠긴된 인프라 리소스 그룹을 포함 하 여 서비스에 대 한 모든 내용을 삭제 하도록 선택 **삭제** 서비스에 대 한 합니다.

![서비스 삭제](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>포털
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Template

Resource Manager 템플릿을 사용 하 여 잠금을 배포를 이름 및 범위에 따라 잠금 유형에 대해 서로 다른 값을 사용 합니다.

잠금을 적용 하는 경우를 **리소스**, 다음 형식을 사용 합니다.

* name - `{resourceName}/Microsoft.Authorization/{lockName}`
* type - `{resourceProviderNamespace}/{resourceType}/providers/locks`

잠금을 적용 하는 경우를 **리소스 그룹** 또는 **구독**, 다음 형식을 사용 합니다.

* name - `{lockName}`
* type - `Microsoft.Authorization/locks`

다음 예제에서는 웹 사이트에 App Service 계획, 웹 사이트 및 잠금을 만드는 템플릿을 보여 줍니다. 잠금의 리소스 종류는 잠그려는 리소스의 리소스 종류로, **입니다**. 잠금의 이름은 리소스 이름을 **/Microsoft.Authorization/** 과 연결하여 만들어집니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

리소스 그룹에 대 한 잠금을 설정 하는 예제를 보려면 [리소스 그룹을 만들고 잠근](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)합니다.

## <a name="powershell"></a>PowerShell
[New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 명령을 사용하여 Azure PowerShell을 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock)를 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

잠금을 삭제하려면 다음을 사용합니다.

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

[az lock create](/cli/azure/lock#az-lock-create) 명령을 사용하여 Azure CLI를 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [az lock list](/cli/azure/lock#az-lock-list)를 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup
```

잠금을 삭제하려면 다음을 사용합니다.

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
[관리 잠금을 위한 REST API](https://docs.microsoft.com/rest/api/resources/managementlocks)를 사용하여 배포된 리소스를 잠글 수 있습니다. REST API를 사용하여 잠금을 만들고, 삭제하고, 기존 잠금에 대한 정보를 검색할 수 있습니다.

잠금을 만들려면 다음을 실행합니다.

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. lock-name은 원하는 잠금 이름입니다. Api 버전을 사용 하 여 **2016-09-01**합니다.

요청에서 잠금에 대한 속성을 지정하는 JSON 개체를 포함합니다.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>다음 단계
* 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)
* 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 자세한 내용은 [Azure Policy란?](../governance/policy/overview.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)를 참조하세요.

