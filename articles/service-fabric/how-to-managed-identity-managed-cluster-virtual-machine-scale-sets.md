---
title: 관리 되는 id를 Service Fabric 관리 되는 클러스터 노드 형식에 추가 (미리 보기)
description: 이 문서에서는 관리 되는 id를 Service Fabric 관리 되는 클러스터 노드 형식에 추가 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3ff5d66160ddbb037469378634826fd9eeae0c54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651649"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>관리 되는 id를 Service Fabric 관리 되는 클러스터 노드 형식에 추가 (미리 보기)

Service Fabric 관리 클러스터의 각 노드 유형은 가상 머신 확장 집합에 의해 지원 됩니다. 관리 되는 id를 관리 되는 클러스터 노드 유형과 함께 사용할 수 있도록 하기 위해 `vmManagedIdentity` 사용할 수 있는 id 목록이 포함 된 노드 유형 정의에 속성이 추가 되었습니다 `userAssignedIdentities` . 기능은 관리 되는 id를 [Azure Key Vault 가상 컴퓨터 확장 집합 확장](../virtual-machines/extensions/key-vault-windows.md)에서 사용 하는 경우와 같이 관리 되지 않는 클러스터에서 관리 id를 사용 하는 방법을 미러링합니다.


노드 유형에 서 관리 id를 사용 하는 Service Fabric 관리 클러스터 배포의 예는 [이 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)을 참조 하세요. 지원 되는 지역 목록은 [관리 되는 클러스터 FAQ](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview)를 참조 하세요.

> [!NOTE]
> 사용자 할당 id만 현재이 기능에 대해 지원 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
* PowerShell을 사용 하려는 경우 Azure CLI를 [설치](/cli/azure/install-azure-cli) 하 여 CLI 참조 명령을 실행 합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 id는 배포 시 만들 수 있도록 ARM (Azure Resource Manager) 템플릿의 리소스 섹션에서 정의할 수 있습니다.

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

또는 PowerShell을 통해 생성 됩니다.

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Service Fabric 리소스 공급자를 사용 하 여 역할 할당 추가

Service Fabric 리소스 공급자 응용 프로그램을 사용 하 여 관리 id에 역할 할당을 추가 합니다. 이 할당을 통해 리소스 공급자 Service Fabric 관리 클러스터의 가상 머신 확장 집합에 id를 할당할 수 있습니다. 

해당 하는 경우 다음 값을 사용 해야 합니다.

|Name|해당 Service Fabric 리소스 공급자 값|
|----|-------------------------------------|
|애플리케이션 ID|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|개체 ID|fbc587f2-66f5-4459-a027-bcd908b9d278|


|역할 정의 이름|역할 정의 ID|
|----|-------------------------------------|
|관리 ID 운영자|f1a07417-d97a-45cb-824c-7a7467783830
|



이 역할 할당은 개체 ID 및 역할 정의 ID를 사용 하 여 리소스 섹션에서 정의할 수 있습니다.

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
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

또는 응용 프로그램 ID 및 역할 정의 ID를 사용 하 여 PowerShell을 통해 만들 수도 있습니다.

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

또는 개체 ID 및 역할 정의 ID:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>노드 유형 정의에 관리 되는 id 속성 추가

마지막으로 `vmManagedIdentity` `userAssignedIdentities` 관리 되는 클러스터의 노드 형식 정의에 및 속성을 추가 합니다. 에 대해 **2021-01-01-preview** 이상을 사용 해야 `apiVersion` 합니다.

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-01-01-preview",
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

배포 후에는 생성 된 관리 id가 지정 된 노드 형식의 가상 머신 확장 집합에 추가 되 고 관리 되지 않는 클러스터에서와 마찬가지로 예상 대로 사용할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

배포 시 다음 오류가 발생 하 여 역할 할당을 올바르게 추가 하지 못했습니다.

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Id 관리 작업을 수행할 권한이 없는 SFRP의 개체/응용 프로그램 ID를 사용 하 여 클라이언트를 표시 하는 Azure Portal 배포 오류":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터에 앱 배포](./tutorial-managed-cluster-deploy-app.md)