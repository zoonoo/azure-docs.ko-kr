---
title: Windows IaaS VM용 Azure Disk Encryption 사용
description: 이 문서에서는 Windows IaaS VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 4c053ec5fdf895c04abafc103778c86d02a8735c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312683"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Windows IaaS VM용 Azure Disk Encryption 사용

수많은 디스크 암호화 시나리오를 사용할 수 있으며 단계는 시나리오에 따라 다를 수 있습니다. 다음 섹션에서는 Windows IaaS VM에 대한 시나리오를 자세히 설명합니다. 디스크 암호화를 사용하려면 [Azure Disk Encryption 필수 구성 요소](../security/azure-security-disk-encryption-prerequisites.md)를 먼저 완료해야 합니다. 

디스크가 암호화되기 전에 먼저 [스냅숏](../virtual-machines/windows/snapshot-copy-managed-disk.md) 및/또는 백업을 수행하세요. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 완료되면 Set-AzureRmVMDiskEncryptionExtension cmdlet을 사용하여 -skipVmBackup 매개 변수를 지정함으로써 관리 디스크를 암호화할 수 있습니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [Azure Backup](../backup/backup-azure-vms-encryption.md) 문서를 참조하세요. 

>[!WARNING]
> Azure Disk Encryption은 Key Vault 및 VM이 동일한 지역에 공동 배치되게 해야 합니다. 암호화할 VM과 동일한 지역에 있는 Key Vault를 만들고 사용합니다. 


## <a name="bkmk_RunningWinVM"></a> 기존 또는 실행 중인 IaaS Windows VM에서 암호화 사용
이 시나리오에서는 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 다음 섹션에서는 Azure Disk Encryption을 사용하도록 설정하는 방법에 대해 자세히 설명합니다. 가상 머신 확장에 대한 스키마 정보가 필요한 경우 [Windows용 Azure Disk Encryption 확장](../virtual-machines/extensions/azure-disk-enc-windows.md) 문서를 참조하세요.

>[!IMPORTANT]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅숏을 만들고 백업해야 합니다. 포털에서 관리 디스크에 대한 스냅숏을 수행하거나 [Azure Backup](../backup/backup-azure-vms-encryption.md)을 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업을 만들면 Set-AzureRmVMDiskEncryptionExtension cmdlet에 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화하는 데 사용할 수 있습니다. 백업이 만들어지고 이 매개 변수가 지정되지 않으면 관리 디스크 기반 VM에 대한 Set-AzureRmVMDiskEncryptionExtension 명령은 실패하게 됩니다. 
>
>암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Azure PowerShell을 통해 기존 또는 실행 중인 VM에서 암호화 사용 
Azure에서 [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) cmdlet을 사용하여 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다. 

-  **실행 중인 VM 암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzureRmVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MySecureRg, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **KEK를 사용하여 실행 중인 VM 암호화:** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

- **디스크가 암호화되어 있는지 확인:** IaaS VM의 암호화 상태를 확인하려면 [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **디스크 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet을 사용합니다. OS와 데이터 디스크가 모두 암호화된 경우 Windows VM에서 데이터 디스크 암호화를 사용하지 않도록 설정하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a> Azure CLI를 통해 기존 또는 실행 중인 VM에서 암호화 사용
Azure에서 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 명령을 사용하여 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다.

-  **실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다. </br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

- **디스크가 암호화되어 있는지 확인:** IaaS VM의 암호화 상태를 확인하려면 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용합니다. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. OS와 데이터 디스크가 모두 암호화된 경우 Windows VM에서 데이터 디스크 암호화를 사용하지 않도록 설정하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅숏을 만들고 백업해야 합니다. 포털에서 관리 디스크에 대한 스냅숏을 수행하거나 [Azure Backup](../backup/backup-azure-vms-encryption.md)을 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업을 만들면 Set-AzureRmVMDiskEncryptionExtension cmdlet에 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화하는 데 사용할 수 있습니다. 이 명령은 백업을 만들고 이 매개 변수가 지정될 때까지 관리 디스크 기반 VM에 대해 실패합니다. 
>
>암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 

### <a name="bkmk_RunningWinVMwRM"> </a>Resource Manager 템플릿 사용
[실행 중인 Windows VM을 암호화하기 위해 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Windows VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.


1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.

2. 구독, 리소스 그룹, 위치, 설정, 약관 및 규약을 선택합니다. **구매**를 클릭하여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

다음 표에서는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수를 나열합니다.

| 매개 변수 | 설명 |
| --- | --- |
| vmName | 암호화 작업을 실행할 VM의 이름. |
| keyVaultName | BitLocker 키가 업로드될 Key Vault의 이름. cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` 또는 Azure CLI 명령 `az keyvault list --resource-group "MySecureGroup"을 사용하여 가져올 수 있습니다. |ConvertFrom-JSON`|
| keyVaultResourceGroup | 키 자격 증명 모음을 포함하는 리소스 그룹의 이름|
|  keyEncryptionKeyURL | 생성된 BitLocker 키를 암호화하는 데 사용되는 주요 암호화 키의 URL. UseExistingKek 드롭다운 목록에서 **nokek**를 선택하면 이 매개 변수가 선택 사항입니다. UseExistingKek 드롭다운 목록에서 **kek**를 선택하면 _keyEncryptionKeyURL_ 값을 반드시 입력해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_입니다. 
| forceUpdateTag | 작업을 강제로 실행해야 할 때마다 GUID 같은 고유한 값으로 전달합니다. |
| resizeOSDisk | 시스템 볼륨을 분할하기 전에 전체 OS VHD를 채우려면 OS 파티션 크기를 조정해야 합니다. |
| location | 모든 리소스에 대한 위치. |

## <a name="encrypt-virtual-machine-scale-sets"></a>가상 머신 확장 집합 암호화

[Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)을 사용하면 부하 분산된 동일한 VM의 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 가상 머신 확장 집합을 암호화하려면 CLI 또는 Azure PowerShell을 사용합니다.


### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Azure PowerShell을 사용하여 디스크 암호화 미리 보기에 등록

가상 머신 확장 집합용 Azure Disk Encryption 미리 보기를 사용하려면 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)를 사용하여 사용자가 구독을 직접 등록해야 합니다. 디스크 암호화 미리 보기 기능을 처음 사용할 때는 다음 단계를 수행하기만 하면 됩니다.

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

등록 요청을 전파하는 데 최대 10분까지 걸릴 수 있습니다. [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 명령을 사용하여 등록 상태를 확인할 수 있습니다. `RegistrationState`가 *Registered*로 보고되면 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider)를 사용하여 *Microsoft.Compute* 공급자를 다시 등록합니다.

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Azure PowerShell을 사용한 가상 머신 확장 집합 암호화

[Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) cmdlet을 사용하여 Windows 가상 머신 확장 집합에서 암호화를 사용하도록 설정합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다.

-  **실행 중인 가상 머신 확장 집합 암호화**:
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **가상 머신 확장 집합에 대한 암호화 상태 가져오기:** [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption) cmdlet을 사용합니다.
    
    ```azurepowershell-interactive
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **가상 머신 확장 집합에서 암호화 사용 안 함**: [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption) cmdlet을 사용합니다. 

    ```azurepowershell-interactive
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Azure CLI를 사용하여 디스크 암호화 미리 보기에 등록

가상 머신 확장 집합용 Azure Disk Encryption 미리 보기를 사용하려면 [az feature register](/cli/azure/feature#az-feature-register)를 사용하여 사용자가 구독을 직접 등록해야 합니다. 디스크 암호화 미리 보기 기능을 처음 사용할 때는 다음 단계를 수행하기만 하면 됩니다.

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

등록 요청을 전파하는 데 최대 10분까지 걸릴 수 있습니다. [az feature show](/cli/azure/feature#az-feature-show)를 사용하여 등록 상태를 확인할 수 있습니다. `State`가 *Registered*로 보고되면 [az provider register](/cli/azure/provider#az-provider-register)를 사용하여 *Microsoft.Compute* 공급자를 다시 등록합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```



###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Azure CLI를 사용한 가상 머신 확장 집합 암호화

[az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable)을 사용하여 Windows 가상 머신 확장 집합에서 암호화를 사용하도록 설정합니다. 확장 집합에서 업그레이드 정책을 수동으로 설정하는 경우 [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances)를 사용하여 암호화를 시작합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다.

-  **실행 중인 가상 머신 확장 집합 암호화**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **키를 래핑하는 KEK를 사용하여 실행 중인 가상 머신 확장 집합 암호화**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **가상 머신 확장 집합에 대한 암호화 상태 가져오기:** [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show) 사용

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **가상 머신 확장 집합에서 암호화 사용 안 함**: [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable) 사용
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Windows 가상 머신 확장 집합을 위한 Azure Resource Manager 템플릿

Windows 가상 머신 확장 집합을 암호화하거나 암호 해독하려면 아래의 Azure Resource Manager 템플릿 및 지침을 사용합니다.

- [Windows 가상 머신 확장 집합에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [점프 상자를 통해 Windows VM의 VM 확장 집합 배포 및 Windows VM 확장 집합에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)
- [Windows VM 확장 집합에서 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. **Deploy to Azure**를 클릭합니다.
     2. 필수 필드를 채운 후 사용 약관에 동의합니다.
     3. **구매**를 클릭하여 템플릿을 배포합니다.

## <a name="bkmk_VHDpre"> </a> 고객 암호화 VHD 및 암호화 키로 만든 새 IaaS VM

이 시나리오에서는 PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 

부록의 지침을 사용하여 Azure에서 사용할 수 있는 미리 암호화된 이미지를 준비합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.

* [사전에 암호화된 Windows VHD 준비](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [사전에 암호화된 Linux VHD 준비](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅숏을 만들고 백업해야 합니다. 포털에서 관리 디스크에 대한 스냅숏을 수행하거나 [Azure Backup](../backup/backup-azure-vms-encryption.md)을 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업을 만들면 Set-AzureRmVMDiskEncryptionExtension cmdlet에 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화하는 데 사용할 수 있습니다. 백업이 만들어지고 이 매개 변수가 지정되지 않으면 관리 디스크 기반 VM에 대한 Set-AzureRmVMDiskEncryptionExtension 명령은 실패하게 됩니다. 
>
>암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 


### <a name="bkmk_VHDprePSH"> </a> Azure PowerShell을 통해 미리 암호화된 VHD가 있는 VM 암호화
[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) PowerShell cmdlet을 사용하여 암호화된 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다. 아래 예제에서는 몇 가지 공통 매개 변수를 제공합니다. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용
[PowerShell을 사용하여 새 디스크를 Windows에 추가](../virtual-machines/windows/attach-disk-ps.md)하거나 [Azure Portal을 통해](../virtual-machines/windows/attach-managed-disk-portal.md) 추가할 수 있습니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 Powershell을 사용하여 Windows VM용 새 디스크를 암호화하는 경우 새 시퀀스 버전을 지정해야 합니다. 시퀀스 버전은 고유해야 합니다. 아래 스크립트는 시퀀스 버전에 대한 GUID를 생성합니다. 경우에 따라 새로 추가된 데이터 디스크가 Azure Disk Encryption 확장에 의해 자동으로 암호화될 수 있습니다. 자동 암호화는 일반적으로 새 디스크가 온라인 상태가 된 후에 VM이 다시 부팅되는 경우에 발생합니다. 일반적으로 이전에 VM에서 디스크 암호화를 실행했을 때 볼륨 유형으로 “모두”를 지정했기 때문에 발생합니다. 새로 추가된 데이터 디스크에서 자동 암호화가 발생하는 경우 새 시퀀스 버전으로 Set-AzureRmVmDiskEncryptionExtension cmdlet을 다시 실행하는 것이 좋습니다. 새 데이터 디스크가 자동으로 암호화되는 경우 암호화하지 않으려면, 먼저 모든 드라이브를 암호 해독한 다음, 볼륨 유형으로 OS를 지정하여 새 시퀀스 버전으로 다시 암호화합니다. 
  
 

-  **실행 중인 VM 암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzureRmVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MySecureRg, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다. 이 예제에서는 -VolumeType 매개 변수에 OS 및 데이터 볼륨을 모두 포함하는 "All"을 사용합니다. OS 볼륨만 암호화하려면 -VolumeType 매개 변수에 "OS"를 사용합니다. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **KEK를 사용하여 실행 중인 VM 암호화:** 이 예제에서는 -VolumeType 매개 변수에 OS 및 데이터 볼륨을 모두 포함하는 "All"을 사용합니다. OS 볼륨만 암호화하려면 -VolumeType 매개 변수에 "OS"를 사용합니다.

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI를 사용하여 새로 추가된 디스크에서 암호화 사용
 암호화를 사용하도록 설정하는 명령을 실행하는 경우 Azure CLI 명령은 자동으로 새 순서 버전을 제공합니다. 이 예제에서는 volume-type 매개 변수에 "All"을 사용합니다. OS 디스크만 암호화하는 경우에는 volume-type 매개 변수를 OS로 변경해야 합니다. Powershell 구문과 달리 CLI에서는 사용자가 암호화를 사용하도록 설정할 때 고유 시퀀스 버전을 제공하지 않아도 됩니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.   

-  **실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>암호화 사용 안 함
Azure PowerShell, Azure CLI 또는 Resource Manager 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. OS와 데이터 디스크가 모두 암호화된 경우 Windows VM에서 데이터 디스크 암호화를 사용하지 않도록 설정하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

- **Azure PowerShell을 사용하여 디스크 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Azure CLI를 사용하여 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type "all"
     ```
- **Resource Manager 템플릿으로 암호화를 사용하지 않도록 설정:** 

    1. [실행중인 Windows VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 템플릿에서 **Azure에 배포**를 클릭합니다.
    2. 구독, 리소스 그룹, 위치, VM, 볼륨 유형, 약관 및 규약을 선택합니다.
    3.  **구매**를 클릭하여 실행 중인 Windows VM에서 디스크 암호화를 사용하지 않도록 설정합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Linux용 Azure Disk Encryption 사용](azure-security-disk-encryption-linux.md)
