---
title: Service Fabric 관리 클러스터 (미리 보기) 노드에 대해 디스크 암호화 사용
description: ARM 템플릿을 사용 하 여 Windows에서 Azure Service Fabric 관리 클러스터 노드에 대 한 디스크 암호화를 사용 하도록 설정 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100642512"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Service Fabric 관리 클러스터 (미리 보기) 노드에 대해 디스크 암호화 사용

이 가이드에서는 ARM (Azure Resource Manager) 템플릿을 통해 [가상 머신 확장 집합](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) 의 [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) 기능을 사용 하 여 Windows에서 Service Fabric 관리 클러스터 노드에 대해 디스크 암호화를 사용 하도록 설정 하는 방법을 알아봅니다.

> [!IMPORTANT]
> 가상 머신 확장 집합 디스크 암호화 미리 보기는 이미지 업그레이드 또는 이미지 업그레이드를 아직 지원 하지 않습니다. OS 이미지를 업그레이드 해야 하는 경우에는 사용 하지 마십시오.

## <a name="register-for-azure-disk-encryption"></a>Azure Disk Encryption 등록

가상 머신 확장 집합에 대 한 디스크 암호화 미리 보기에는 자체 등록이 필요 합니다. 다음 명령을 실행합니다.

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

다음을 실행 하 여 등록의 상태를 확인 합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

상태가 *등록* 됨으로 변경 되 면 진행할 준비가 된 것입니다.

## <a name="provision-a-key-vault-for-disk-encryption"></a>디스크 암호화에 대 한 Key Vault 프로 비전

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다. Key Vault 및 Service Fabric 관리 되는 클러스터는 동일한 Azure 지역 및 구독에 상주해 야 합니다. 이러한 요구 사항이 충족 되는 한 새 또는 기존 Key Vault를 디스크 암호화에 사용 하도록 설정 하 여 사용할 수 있습니다.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>디스크 암호화를 사용 하 여 Key Vault 만들기

다음 명령을 실행 하 여 디스크 암호화에 대 한 새 Key Vault을 만듭니다. Key Vault에 대 한 지역이 클러스터와 동일한 지역에 [Service Fabric 관리 되는 클러스터에 대해 지원](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) 되는지 확인 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>디스크 암호화를 사용 하도록 기존 Key Vault 업데이트

다음 명령을 실행 하 여 기존 Key Vault에 대해 디스크 암호화를 사용 하도록 설정 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>디스크 암호화에 대 한 템플릿 및 매개 변수 파일 업데이트

다음 단계에서는 [기존 관리 클러스터](tutorial-managed-cluster-deploy.md)에서 디스크 암호화를 사용 하도록 설정 하는 데 필요한 템플릿 변경 내용을 안내 합니다. 또는이 템플릿을 사용 하 여 디스크 암호화를 사용 하는 새 Service Fabric 관리 클러스터를 배포할 수 있습니다. https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. 다음 매개 변수를 템플릿에 추가 하 여 사용자 고유의 구독, 리소스 그룹 이름 및 자격 증명 모음 이름을 `keyVaultResourceId` 다음과 같이 바꿉니다.

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. 그런 다음 `AzureDiskEncryption` 템플릿의 관리 되는 클러스터 노드 형식에 VM 확장을 추가 합니다.

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. 마지막으로 *keyVaultResourceId* 에서 사용자 고유의 구독, 리소스 그룹 및 주요 자격 증명 모음 이름을 대체 하 여 매개 변수 파일을 업데이트 합니다.

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>변경 내용 배포 및 확인

준비가 되 면 변경 내용을 배포 하 여 관리 되는 클러스터에서 디스크 암호화를 사용 하도록 설정 합니다.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

명령을 사용 하 여 노드 형식의 기본 확장 집합에서 디스크 암호화 상태를 확인할 수 있습니다 `Get-AzVmssDiskEncryption` . 먼저 관리 되는 클러스터의 지원 리소스 그룹 이름 (기본 가상 네트워크, 부하 분산 장치, 공용 IP, NSG, 확장 집합 및 저장소 계정 포함)을 확인 해야 합니다. `VmssName`배포 템플릿에 지정 된 대로 확인할 클러스터 노드 형식 이름을 수정 해야 합니다.

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

출력은 다음과 유사 하 게 표시 됩니다.

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>다음 단계

[샘플: 표준 SKU Service Fabric 관리 되는 클러스터, 디스크 암호화를 사용 하는 노드 유형 1 개](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Windows Vm에 대 한 Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

[Azure Resource Manager를 사용한 가상 머신 확장 집합 암호화](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
