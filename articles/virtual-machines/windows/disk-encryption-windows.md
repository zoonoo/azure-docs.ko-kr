---
title: Windows VM에 대한 Azure Disk Encryption 시나리오
description: 이 문서에서는 다양한 시나리오에 대해 Windows VM용 Microsoft Azure 디스크 암호화를 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f7b6e667df95d9279ad5c44caa4ba33a17909935
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113152"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Windows VM에 대한 Azure Disk Encryption 시나리오

Windows 가상 시스템(VM)에 대한 Azure 디스크 암호화는 Windows의 Bitlocker 기능을 사용하여 OS 디스크 및 데이터 디스크의 전체 디스크 암호화를 제공합니다. 또한 VolumeType 매개 변수가 모두일 때 임시 리소스 디스크의 암호화를 제공합니다.

Azure 디스크 암호화는 [Azure Key Vault와 통합되어](disk-encryption-key-vault.md) 디스크 암호화 키 및 비밀을 제어하고 관리할 수 있습니다. 서비스에 대한 개요는 Windows [VM에 대한 Azure 디스크 암호화](disk-encryption-overview.md)를 참조하십시오.

[지원되는 VM 크기 및 운영 체제의](disk-encryption-overview.md#supported-vms-and-operating-systems)가상 컴퓨터에만 디스크 암호화를 적용할 수 있습니다. 또한 다음 필수 구성 조건을 충족해야 합니다.

- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [그룹 정책 요구 사항](disk-encryption-overview.md#group-policy-requirements)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - 이전에 Azure AD와 함께 Azure 디스크 암호화를 사용하여 VM을 암호화한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)가 있는 Azure 디스크 암호화를](disk-encryption-overview-aad.md) 참조하십시오. 
>
> - 디스크를 암호화하기 전에 [스냅샷을 만들거나](snapshot-copy-managed-disk.md) 백업을 만들어야 합니다. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 완료되면 [Set-AzVMDisk암호화확장 cmdlet을](/powershell/module/az.compute/set-azvmdiskencryptionextension) 사용하여 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화할 수 있습니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [암호화된 Azure VM 을 백업하고 복원하는](../../backup/backup-azure-vms-encryption.md)방법을 참조하세요. 
>
> - 암호화를 암호화하거나 비활성화하면 VM이 재부팅될 수 있습니다.

## <a name="install-tools-and-connect-to-azure"></a>도구를 설치하고 Azure에 연결

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>기존 또는 실행 중인 Windows VM에서 암호화 사용
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 가상 머신 확장에 대한 스키마 정보가 필요한 경우 [Windows용 Azure Disk Encryption 확장](../extensions/azure-disk-enc-windows.md) 문서를 참조하세요.

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a> Azure PowerShell을 통해 기존 또는 실행 중인 VM에서 암호화 사용 
Azure에서 실행 중인 IaaS 가상 컴퓨터에서 암호화를 사용하도록 [설정-AzVMDisk암호화확장](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용합니다. 

-  **실행 중인 VM암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDisk암호화확장 cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MyKeyVaultResourceGroup, 마이버추얼머신리소스그룹, 마이시큐어VM 및 마이시큐어볼트를 가치로 바꿉습니다.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **KEK를 사용하여 실행 중인 VM 암호화:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

- **디스크가 암호화되었는지 확인합니다.** IaaS VM의 암호화 상태를 확인하려면 [Get-AzVmDisk암호화 상태](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **디스크 암호화 를 사용하지 않도록 설정:** 암호화를 사용하지 않도록 설정하려면 [사용 안 함-AzVMDisk암호화](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. OS와 데이터 디스크가 모두 암호화되었을 때 Windows VM에서 데이터 디스크 암호화를 비활성화하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Azure CLI를 사용하여 기존 또는 실행 중인 VM에서 암호화 를 사용하도록 설정합니다.
Azure에서 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 명령을 사용하여 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다.

- **실행 중인 VM 암호화:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다. </br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

- **디스크가 암호화되었는지 확인합니다.** IaaS VM의 암호화 상태를 확인하려면 [az vm 암호화 표시](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용합니다. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. OS와 데이터 디스크가 모두 암호화되었을 때 Windows VM에서 데이터 디스크 암호화를 비활성화하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Resource Manager 템플릿 사용

[실행 중인 Windows VM을 암호화하기 위해 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Windows VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.


1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.

2. 구독, 리소스 그룹, 위치, 설정, 약관 및 규약을 선택합니다. **구매**를 클릭하여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

다음 표에서는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수를 나열합니다.

| 매개 변수 | 설명 |
| --- | --- |
| vmName | 암호화 작업을 실행할 VM의 이름. |
| keyVaultName | BitLocker 키가 업로드될 Key Vault의 이름. cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 또는 Azure CLI 명령 `az keyvault list --resource-group "MyKeyVaultResourceGroup"`을 사용하여 가져올 수 있습니다.|
| keyVaultResourceGroup | 키 자격 증명 모음을 포함하는 리소스 그룹의 이름|
|  keyEncryptionKeyURL | &lt;키 볼트 이름&gt;.vault.azure.net/key/&lt;키 이름 https:// 형식의 키 암호화&gt;키의 URL입니다. KEK를 사용하지 않으려면 이 필드를 비워 둡니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_입니다. 
| forceUpdateTag | 작업을 강제로 실행해야 할 때마다 GUID 같은 고유한 값으로 전달합니다. |
| resizeOSDisk | 시스템 볼륨을 분할하기 전에 전체 OS VHD를 채우려면 OS 파티션 크기를 조정해야 합니다. |
| 위치 | 모든 리소스에 대한 위치. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a> 고객 암호화 VHD 및 암호화 키로 만든 새 IaaS VM

이 시나리오에서는 PowerShell cmdlet 또는 CLI 명령을 사용하여 미리 암호화된 VHD 및 관련 암호화 키에서 새 VM을 만들 수 있습니다. 

[사전 암호화된 Windows VHD 준비의](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)지침을 사용합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> Azure PowerShell을 통해 미리 암호화된 VHD가 있는 VM 암호화
PowerShell cmdlet [Set-AzVMOSDisk를](/powershell/module/az.compute/set-azvmosdisk#examples)사용하여 암호화된 VHD에서 디스크 암호화를 활성화할 수 있습니다. 아래 예제에서는 몇 가지 공통 매개 변수를 제공합니다. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용
[PowerShell을 사용하여 새 디스크를 Windows에 추가](attach-disk-ps.md)하거나 [Azure Portal을 통해](attach-managed-disk-portal.md) 추가할 수 있습니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 Powershell을 사용하여 Windows VM용 새 디스크를 암호화하는 경우 새 시퀀스 버전을 지정해야 합니다. 시퀀스 버전은 고유해야 합니다. 아래 스크립트는 시퀀스 버전에 대한 GUID를 생성합니다. 경우에 따라 새로 추가된 데이터 디스크가 Azure Disk Encryption 확장에 의해 자동으로 암호화될 수 있습니다. 자동 암호화는 일반적으로 새 디스크가 온라인 상태가 된 후에 VM이 다시 부팅되는 경우에 발생합니다. 일반적으로 이전에 VM에서 디스크 암호화를 실행했을 때 볼륨 유형으로 “모두”를 지정했기 때문에 발생합니다. 새로 추가된 데이터 디스크에서 자동 암호화가 발생하는 경우 새 시퀀스 버전으로 Set-AzVmDisk암호화확장 cmdlet을 다시 실행하는 것이 좋습니다. 새 데이터 디스크가 자동으로 암호화되는 경우 암호화하지 않으려면, 먼저 모든 드라이브를 암호 해독한 다음, 볼륨 유형으로 OS를 지정하여 새 시퀀스 버전으로 다시 암호화합니다. 
  
 

-  **실행 중인 VM암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDisk암호화확장 cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MyKeyVaultResourceGroup, 마이버추얼머신리소스그룹, 마이시큐어VM 및 마이시큐어볼트를 가치로 바꿉습니다. 이 예제에서는 -VolumeType 매개 변수에 OS 및 데이터 볼륨을 모두 포함하는 "All"을 사용합니다. OS 볼륨만 암호화하려면 -VolumeType 매개 변수에 "OS"를 사용합니다. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **KEK를 사용하여 실행 중인 VM 암호화:** 이 예제에서는 -VolumeType 매개 변수에 "All"을 사용하며 여기에는 OS 및 데이터 볼륨이 모두 포함됩니다. OS 볼륨만 암호화하려면 -VolumeType 매개 변수에 "OS"를 사용합니다.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI를 사용하여 새로 추가된 디스크에서 암호화 사용
 암호화를 사용하도록 설정하는 명령을 실행하는 경우 Azure CLI 명령은 자동으로 새 순서 버전을 제공합니다. 이 예제에서는 volume-type 매개 변수에 "All"을 사용합니다. OS 디스크만 암호화하는 경우에는 volume-type 매개 변수를 OS로 변경해야 합니다. Powershell 구문과 달리 CLI에서는 사용자가 암호화를 사용하도록 설정할 때 고유 시퀀스 버전을 제공하지 않아도 됩니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.   

-  **실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>암호화 사용 안 함
Azure PowerShell, Azure CLI 또는 Resource Manager 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. OS와 데이터 디스크가 모두 암호화되었을 때 Windows VM에서 데이터 디스크 암호화를 비활성화하면 예상대로 작동하지 않습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.

- **Azure PowerShell을 사용하면 디스크 암호화를 사용하지 않도록 설정합니다.** 암호화를 사용하지 않도록 설정하려면 [사용 안 함-AzVMDisk암호화](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Azure CLI를 통한 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Resource Manager 템플릿으로 암호화를 사용하지 않도록 설정:** 

    1. [실행중인 Windows VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 템플릿에서 **Azure에 배포**를 클릭합니다.
    2. 구독, 리소스 그룹, 위치, VM, 볼륨 유형, 약관 및 규약을 선택합니다.
    3.  **구매**를 클릭하여 실행 중인 Windows VM에서 디스크 암호화를 사용하지 않도록 설정합니다. 

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

Azure 디스크 암호화는 다음 시나리오, 기능 및 기술에 대해 작동하지 않습니다.

- 클래식 VM 생성 방법을 통해 생성된 기본 계층 VM 또는 VM을 암호화합니다.
- 소프트웨어 기반 RAID 시스템으로 구성된 VM 암호화.
- 2016년 이전에 S2D(저장소 공간 직접) 또는 Windows 서버 버전으로 구성된 VM 암호화
- 온-프레미스 키 관리 시스템과의 통합.
- Azure 파일(공유 파일 시스템)
- NFS(네트워크 파일 시스템)
- 동적 볼륨
- 각 컨테이너에 대한 동적 볼륨을 만드는 Windows Server 컨테이너입니다.
- 임시 OS 디스크.
- DFS, GFS, DRDB 및 CephFS와 같은 공유/분산 파일 시스템의 암호화(이에 국한되지 않음).
- 암호화된 VM을 다른 구독으로 이동합니다.
- Gen2 VM(참조: [Azure에서 2세대 VM에 대한 지원)](generation-2.md#generation-1-vs-generation-2-capabilities)
- Lsv2 시리즈 VM (참조: [Lsv2 시리즈)](../lsv2-series.md)

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
