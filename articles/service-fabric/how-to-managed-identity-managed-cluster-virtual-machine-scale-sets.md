---
title: Service Fabric 관리형 클러스터 노드 유형에 관리 ID 추가
description: 이 문서에서는 관리 ID를 Service Fabric 관리형 클러스터 노드 형식에 추가하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 221f97f6354c105c106343fe45c9e747d46e35fb
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671137"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type"></a>Service Fabric 관리형 클러스터 노드 유형에 관리 ID 추가

Service Fabric 관리형 클러스터의 각 노드 형식은 가상 머신 확장 집합을 통해 백업됩니다. 관리 ID를 관리형 클러스터 노드 형식에 사용할 수 있도록 `vmManagedIdentity` 속성이 사용할 수 있는 ID의 목록이 포함된 노드 형식 정의 `userAssignedIdentities`에 추가되었습니다. 이 기능은 관리 ID를 [Azure Key Vault 가상 머신 확장 집합 확장](../virtual-machines/extensions/key-vault-windows.md)에서 사용하는 경우와 같이 비 관리형 클러스터에서 관리 ID를 사용하는 방법을 미러링합니다.

특정 노드 형식에서 관리 ID를 사용하는 Service Fabric 관리형 클러스터 배포의 예는 [이 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)을 참조하세요.

> [!NOTE]
> 현재, 이 기능에는 사용자 할당 ID만 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* PowerShell을 사용하려는 경우 Azure CLI를 [설치](/cli/azure/install-azure-cli)하여 CLI 참조 명령을 실행합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID는 ARM(Azure Resource Manager) 템플릿의 리소스에서 배포 시에 만들도록 정의할 수 있습니다.

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

또는 PowerShell을 통해 만들 수도 있습니다.

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Service Fabric 리소스 공급자를 사용하여 역할 할당 추가

Service Fabric 리소스 공급자 애플리케이션을 사용하여 관리 ID에 역할 할당을 추가합니다. 이 할당을 통해 Service Fabric 리소스 공급자가 관리형 클러스터의 가상 머신 확장 집합에 ID를 할당할 수 있습니다. 

Service Fabric 리소스 공급자 애플리케이션에 대한 서비스 주체를 가져옵니다.

```powershell
Login-AzAccount
Select-AzSubscription -SubscriptionId <SubId>
Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
```

> [!NOTE]
> 올바른 구독에 있는지 확인합니다. 구독이 다른 테넌트에 있는 경우 보안 주체 ID가 변경됩니다.

```powershell
ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
ObjectType            : ServicePrincipal
DisplayName           : Azure Service Fabric Resource Provider
Id                    : 00000000-0000-0000-0000-000000000000
Type                  :
```

이전 출력의 ID를 **principalId** 로 사용하고 아래의 역할 정의 ID를 **roleDefinitionId** 로 사용합니다(템플릿 또는 PowerShell 명령에 적용 가능한 경우).

|역할 정의 이름|역할 정의 ID|
|----|-------------------------------------|
|관리 ID 운영자|f1a07417-d97a-45cb-824c-7a7467783830|


이 역할 할당은 주체 ID 및 역할 정의 ID를 사용하여 리소스 섹션 템플릿에서 정의할 수 있습니다.

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "00000000-0000-0000-0000-000000000000" 
    } 
}, 
```
> [!NOTE]
> vmIdentityRoleNameGuid는 유효한 GUID여야 합니다. 이 역할 할당을 포함하여 동일한 템플릿을 다시 배포하는 경우 GUID가 원래 사용된 것과 동일한지 확인합니다. 또는 한 번만 만들면 되므로 이 리소스를 제거합니다.

또는 보안 주체 ID 및 역할 정의 이름을 사용하여 PowerShell을 통해 만들 수 있습니다.

```powershell
New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>노드 형식 정의에 관리 ID 속성 추가

마지막으로 관리형 클러스터의 노드 형식 정의에 `vmManagedIdentity` 및 `userAssignedIdentities` 속성을 추가합니다. `apiVersion`에 대해 **2021-05-01** 이상을 사용해야 합니다.

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-05-01",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
            ]
        }
    }
}
```

배포 후에는 생성된 관리 ID가 지정된 노드 형식의 가상 머신 확장 집합에 추가되고 비 관리형 클러스터에서와 마찬가지로 사용할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

역할 할당을 올바르게 추가하지 못하면 배포 시 다음 오류가 발생합니다.

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="ID 관리 작업을 수행할 권한이 없는 클라이언트 및 SFRP의 개체/애플리케이션 ID를 보여 주는 Azure Portal 배포 오류":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터에 앱 배포](./tutorial-managed-cluster-deploy-app.md)
