---
title: Linux VM에 대한 Azure Disk Encryption 시나리오
description: 이 문서에서는 다양한 시나리오에 대해 Linux VM에 대한 Microsoft Azure 디스크 암호화를 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6a1ea5e09dc70b2c523bf23b28f726cbe2c0818d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062684"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM에 대한 Azure Disk Encryption 시나리오

Azure 디스크 암호화는 Linux의 DM-Crypt 기능을 사용하여 Azure 가상 시스템(VM)의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공하며 Azure Key Vault와 통합되어 디스크 암호화 키 및 암호를 제어하고 관리할 수 있습니다. 서비스에 대한 개요는 Linux [VM에 대한 Azure 디스크 암호화를](disk-encryption-overview.md)참조하십시오.

많은 디스크 암호화 시나리오가 있으며 단계는 시나리오에 따라 다를 수 있습니다. 다음 섹션에서는 Linux VM에 대한 시나리오를 보다 자세히 다룹니다.

[지원되는 VM 크기 및 운영 체제의](disk-encryption-overview.md#supported-vms-and-operating-systems)가상 컴퓨터에만 디스크 암호화를 적용할 수 있습니다. 또한 다음 필수 구성 조건을 충족해야 합니다.

- [VM에 대한 추가 요구 사항](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

모든 경우에 디스크가 암호화되기 전에 [스냅샷을 만들거나](snapshot-copy-managed-disk.md) 백업을 만들어야 합니다. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 완료되면 [Set-AzVMDisk암호화확장 cmdlet을](/powershell/module/az.compute/set-azvmdiskencryptionextension) 사용하여 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화할 수 있습니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [Azure Backup](../../backup/backup-azure-vms-encryption.md) 문서를 참조하세요. 

>[!WARNING]
> - 이전에 Azure AD와 함께 Azure 디스크 암호화를 사용하여 VM을 암호화한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)가 있는 Azure 디스크 암호화를](disk-encryption-overview-aad.md) 참조하십시오. 
>
> - Linux OS 볼륨을 암호화할 때 VM을 사용할 수 없는 것으로 간주해야 합니다. 암호화 프로세스 중에 액세스해야 하는 열려 있는 파일을 차단하는 문제를 피하기 위해 암호화가 진행되는 동안 SSH 로그인을 방지하는 것이 좋습니다. 진행률을 확인하려면 [Get-AzVMDisk암호화 상태](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 또는 [VM 암호화 표시](/cli/azure/vm/encryption#az-vm-encryption-show) CLI 명령을 사용합니다. 이 프로세스는 30GB OS 볼륨을 처리하는 데 몇 시간이 걸리고 데이터 볼륨을 암호화하는 데 추가 시간이 걸릴 수 있습니다. 데이터 볼륨 암호화 시간은 encrypt format all 옵션이 사용되지 않는 한 데이터 볼륨의 크기 및 수량에 비례합니다. 
> - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다.  

## <a name="install-tools-and-connect-to-azure"></a>도구를 설치하고 Azure에 연결

Azure 디스크 암호화를 Azure [CLI](/cli/azure) 및 [Azure PowerShell](/powershell/azure/new-azureps-module-az)을 통해 활성화및 관리할 수 있습니다. 이렇게 하려면 도구를 로컬로 설치하고 Azure 구독에 연결해야 합니다.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure)은 Azure 리소스를 관리하기 위한 명령줄 도구입니다. CLI는 데이터를 유연하게 쿼리하고, 장기 실행 작업을 비차단 프로세스로 지원하고, 쉽게 스크립팅할 수 있도록 설계되었습니다. [Azure CLI 설치의](/cli/azure/install-azure-cli?view=azure-cli-latest)단계에 따라 로컬로 설치할 수 있습니다.

 

[Azure CLI를 사용하여 Azure 계정에 로그인하려면](/cli/azure/authenticate-azure-cli) [az 로그인](/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용합니다.

```azurecli
az login
```

로그인할 테넌트를 선택하려면 다음을 사용합니다.
    
```azurecli
az login --tenant <tenant>
```

구독이 여러 개이고 특정 구독을 지정하려는 경우 [az account list](/cli/azure/account#az-account-list)를 사용하여 구독 목록을 가져오고 [az account set](/cli/azure/account#az-account-set)으로 지정합니다.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요. 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az 모듈은](/powershell/azure/new-azureps-module-az) Azure 리소스 관리자 모델을 사용하여 [Azure 리소스를](../../azure-resource-manager/management/overview.md) 관리하는 cmdlet 집합을 제공합니다. [Azure Cloud Shell을](../../cloud-shell/overview.md)사용하여 브라우저에서 사용하거나 [Azure PowerShell 설치 모듈의](/powershell/azure/install-az-ps)지침을 사용하여 로컬 컴퓨터에 설치할 수 있습니다. 

이미 로컬에 설치되어있는 경우 최신 버전의 Azure PowerShell SDK 버전을 사용하여 Azure Disk Encryption을 구성해야 합니다. 최신 버전의 [Azure PowerShell 릴리스](https://github.com/Azure/azure-powershell/releases)를 다운로드합니다.

[Azure PowerShell을 사용하여 Azure 계정에 로그인하려면](/powershell/azure/authenticate-azureps?view=azps-2.5.0) [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet을 사용합니다.

```powershell
Connect-AzAccount
```

구독이 여러 개인 경우 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) cmdlet을 사용하여 나열한 다음 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet을 나열합니다.

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) cmdlet을 실행하면 올바른 구독이 선택되었는지 확인합니다.

Azure 디스크 암호화 cmdlet이 설치되어 있는지 확인하려면 [명령 받기](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) cmdlet을 사용합니다.
     
```powershell
Get-command *diskencryption*
```
자세한 내용은 [Azure PowerShell 을 시작하십시오.](/powershell/azure/get-started-azureps) 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>기존 또는 실행 중인 Linux VM에서 암호화 사용
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 가상 머신 확장에 대한 스키마 정보가 필요한 경우 [Linux용 Azure Disk Encryption 확장](../extensions/azure-disk-enc-linux.md) 문서를 참조하세요.

>[!IMPORTANT]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅샷을 만들고 백업해야 합니다. 관리 디스크의 스냅숏은 포털또는 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 통해 생성할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업이 완료되면 Set-AzVMDisk암호화확장 cmdlet을 사용하여 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화할 수 있습니다. Set-AzVMDisk암호화확장 명령은 백업이 이루어지고 이 매개 변수가 지정될 때까지 관리되는 디스크 기반 VM에 대해 실패합니다. 
>
>암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a> Azure CLI를 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용 

[Azure CLI](/cli/azure/?view=azure-cli-latest) 명령줄 도구를 설치하고 사용하여 암호화된 VHD에서 디스크 암호화를 활성화할 수 있습니다. [Azure Cloud Shell](../../cloud-shell/overview.md)과 함께 브라우저에서 사용하거나 로컬 컴퓨터에 설치하여 PowerShell 세션에서 사용할 수 있습니다. Azure에서 기존 또는 실행 중인 Linux VM에서 암호화를 사용하려면 다음 CLI 명령을 사용합니다.

az [vm 암호화 사용 명령을](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) 사용하여 Azure에서 실행 중인 가상 컴퓨터에서 암호화를 사용하도록 설정합니다.

- **실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br>
key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 

- **디스크가 암호화되었는지 확인합니다.** VM의 암호화 상태를 확인하려면 az [vm 암호화 표시](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용합니다. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a> PowerShell을 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용
Azure에서 실행 중인 가상 컴퓨터에서 암호화를 사용하도록 [설정-AzVMDisk암호화확장](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용합니다. 디스크가 암호화되기 전에 [스냅숏을](snapshot-copy-managed-disk.md) 만들고 [Azure Backup을](../../backup/backup-azure-vms-encryption.md) 통해 VM을 백업합니다. -skipVmBackup 매개 변수는 이미 PowerShell 스크립트에 지정되어 실행 중인 Linux VM을 암호화합니다.

-  **실행 중인 VM암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDisk암호화확장 cmdlet을 실행합니다. 리소스 그룹 VM 및 키 자격 증명 모음이 필수 구성 조건으로 만들어졌습니다. 마이버추얼머신리소스그룹, 마이시큐어VM, 마이시큐어볼트를 가치로 바꿉습니다. -VolumeType 매개 변수를 수정하여 암호화할 디스크를 지정합니다.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **KEK를 사용하여 실행 중인 VM 암호화:** OS 디스크가 아니라 데이터 디스크를 암호화하는 경우 -VolumeType 매개 변수를 추가해야 할 수도 있습니다. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 
    
- **디스크가 암호화되었는지 확인합니다.** VM의 암호화 상태를 확인하려면 [Get-AzVmDisk암호화 상태](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet을 사용합니다. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **디스크 암호화 를 사용하지 않도록 설정:** 암호화를 사용하지 않도록 설정하려면 [사용 안 함-AzVMDisk암호화](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>템플릿을 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용

[리소스 관리자 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)사용하여 Azure에서 기존 또는 실행 중인 Linux VM에서 디스크 암호화를 활성화할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포**를 클릭합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 매개 변수, 약관 및 규약을 선택합니다. 기존 또는 실행 중인 VM에서 암호화를 활성화하려면 **만들기를** 클릭합니다.

다음 표에서는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수를 나열합니다.

| 매개 변수 | 설명 |
| --- | --- |
| vmName | 암호화 작업을 실행할 VM의 이름. |
| keyVaultName | 암호화 키를 업로드해야 하는 키 자격 증명 모음의 이름입니다. cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 또는 Azure CLI 명령을 `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`사용하여 얻을 수 있습니다.|
| keyVaultResourceGroup | 키 자격 증명 모음을 포함하는 리소스 그룹의 이름입니다. |
|  keyEncryptionKeyURL | 암호화 키를 암호화하는 데 사용되는 키 암호화 키의 URL입니다. UseExistingKek 드롭다운 목록에서 **nokek**를 선택하면 이 매개 변수가 선택 사항입니다. UseExistingKek 드롭다운 목록에서 **kek**를 선택하면 _keyEncryptionKeyURL_ 값을 반드시 입력해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_입니다. 
| forceUpdateTag | 작업을 강제로 실행해야 할 때마다 GUID 같은 고유한 값으로 전달합니다. |
| 위치 | 모든 리소스에 대한 위치. |

Linux VM 디스크 암호화 템플릿 구성에 대한 자세한 내용은 [Linux용 Azure 디스크 암호화](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux)를 참조하십시오.

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>사용 암호화Format모든 리눅스 VM의 데이터 디스크에 대한 기능

**EncryptFormatAll** 매개 변수는 Linux 데이터 디스크가 암호화되는 시간을 줄여줍니다. 특정 기준을 충족하는 파티션은 현재 파일 시스템과 함께 서식이 지정된 다음 명령 실행 전의 상태로 다시 마운트됩니다. 조건을 충족하는 데이터 디스크를 제외하려면 명령을 실행하기 전에 분리할 수 있습니다.

 이 명령을 실행하면 이전에 탑재된 모든 드라이브의 포맷이 지정되고 이제 빈 드라이브 위에 암호화 계층이 시작됩니다. 이 옵션을 선택하면 VM에 연결된 사용 후 삭제 리소스 디스크도 암호화됩니다. 사용 후 삭제 드라이브가 다시 설정되면 다음 기회에 Azure Disk Encryption 솔루션에서 VM용으로 다시 포맷되고 다시 암호화됩니다. 리소스 디스크가 암호화되면 [Microsoft Azure Linux 에이전트는](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 리소스 디스크를 관리하고 스왑 파일을 사용할 수 없지만 스왑 파일을 수동으로 구성할 수 있습니다.

>[!WARNING]
> VM의 데이터 볼륨에 필요한 데이터가 있는 경우 EncryptFormatAll을 사용하면 안됩니다. 디스크는 분리하여 암호화에서 제외할 수 있습니다. 프로덕션 VM에서 EncryptFormatAll을 시도하기 전에 먼저 테스트 VM에서 시도하여 이 기능 매개 변수와 그 의미를 이해해야 합니다. EncryptFormatAll 옵션은 데이터 디스크를 포맷하여 해당 데이터 디스크의 모든 데이터가 손실됩니다. 계속하기 전에 제외하려는 디스크가 제대로 분리되었는지 확인합니다. </br></br>
 >암호화 설정을 업데이트하는 동안 이 매개 변수를 설정하는 경우 실제 암호화 전에 다시 부팅될 수 있습니다. 이 경우 fstab 파일에서 서식이 지정되지 않은 디스크를 제거할 수도 있습니다. 마찬가지로 암호화 작업을 시작하기 전에 암호화 형식으로 포맷하려는 파티션을 fstab 파일에 추가해야 합니다. 

### <a name="encryptformatall-criteria"></a> EncryptFormatAll 조건
매개 변수에서 아래 조건을 **모두** 충족하는 경우 모든 파티션으로 이동하여 암호화합니다. 
- 루트/OS/부팅 파티션이 아닙니다.
- 아직 암호화되지 않았습니다.
- BEK 볼륨이 아닙니다.
- RAID 볼륨이 아닙니다.
- LVM 볼륨이 아닙니다.
- 탑재되어 있습니다.

RAID 볼륨 또는 LVM 볼륨이 아닌, RAID 볼륨 또는 LVM 볼륨을 구성하는 디스크를 암호화합니다.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a> Azure CLI에서 EncryptFormatAll 매개변수 사용
az [vm 암호화 사용 명령을](/cli/azure/vm/encryption#az-vm-encryption-enable) 사용하여 Azure에서 실행 중인 가상 컴퓨터에서 암호화를 사용하도록 설정합니다.

-  **EncryptFormatAll을 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a> PowerShell cmdlet에서 EncryptFormatAll 매개변수 사용
암호화FormatAll 매개 변수와 [함께 Set-AzVMDisk암호화확장](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용합니다. 

**암호화FormatAll을 사용하여 실행 중인 VM을 암호화합니다.** 예를 들어 아래 스크립트는 변수를 초기화하고 암호화FormatAll 매개 변수를 사용 하 고 Set-AzVMDisk암호화 확장 cmdlet을 실행 합니다. 리소스 그룹, VM 및 키 자격 증명 모음이 필수 구성 조건으로 만들어졌습니다. 마이버추얼머신리소스그룹, 마이시큐어VM, 마이시큐어볼트를 가치로 바꿉습니다.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a> LVM(논리 볼륨 관리자)에서 EncryptFormatAll 매개 변수 사용 
LVM-on-crypt 설정을 사용하는 것이 좋습니다. 다음의 모든 예제에서 device-path와 mountpoint를 사용 사례에 맞는 것으로 바꿉니다. 이 설정은 다음과 같이 수행할 수 있습니다.

- VM을 구성할 데이터 디스크를 추가합니다.
- 이러한 디스크를 포맷하고, 탑재하고, fstab 파일에 추가합니다.

    1. 파티션 표준을 선택하고 전체 드라이브에 걸쳐 있는 파티션을 만든 다음 파티션을 포맷합니다. 여기서는 Azure에서 생성한 바로 가기 링크를 사용합니다. 바로 가기 링크를 사용하면 디바이스 이름 변경과 관련된 문제를 방지할 수 있습니다. 자세한 내용은 [디바이스 이름 문제 해결](troubleshoot-device-names-problems.md) 문서를 참조하세요.
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. 디스크를 탑재합니다.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. fstab에 추가 합니다.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. -암호화FormatAll을 사용하여 Set-AzVMDisk암호화확장 powerShell cmdlet을 실행하여 이러한 디스크를 암호화합니다.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. 이러한 새 디스크를 기반으로 하여 LVM을 설정합니다. VM 부팅이 완료되면 암호화된 드라이브는 잠금 해제됩니다. 따라서 LVM 탑재도 나중으로 지연되어야 합니다.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>고객 암호화 VHD 및 암호화 키로 생성된 새로운 VM
이 시나리오에서는 PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 

Azure에서 사용할 수 있는 미리 암호화된 이미지를 준비하기 위해 Azure Disk 암호화 동일한 스크립트의 지침을 사용합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.

* [사전에 암호화된 Linux VHD 준비](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Azure Disk Encryption을 사용하기 전에 외부에 관리 디스크 기반 VM 인스턴스에 대해 스냅샷을 만들고 백업해야 합니다. 관리 디스크의 스냅숏을 포털에서 만들거나 [Azure Backup을](../../backup/backup-azure-vms-encryption.md) 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업이 완료되면 Set-AzVMDisk암호화확장 cmdlet을 사용하여 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화할 수 있습니다. Set-AzVMDisk암호화확장 명령은 백업이 이루어지고 이 매개 변수가 지정될 때까지 관리되는 디스크 기반 VM에 대해 실패합니다. 
>
> 암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Azure PowerShell을 사용하여 사전 암호화된 VHD로 VM을 암호화합니다. 
PowerShell cmdlet [Set-AzVMOSDisk를](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)사용하여 암호화된 VHD에서 디스크 암호화를 활성화할 수 있습니다. 아래 예제에서는 몇 가지 공통 매개 변수를 제공합니다. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용

[az vm disk attach](add-disk.md)를 사용하거나 [Azure Portal을 통해](attach-disk-portal.md) 새 데이터 디스크를 추가할 수 있습니다. 암호화하려면 먼저 새로 연결된 데이터 디스크를 탑재해야 합니다. 암호화가 진행되는 동안 드라이브를 사용할 수 없으므로 데이터 드라이브의 암호화를 요청해야 합니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI를 사용하여 새로 추가된 디스크에서 암호화 사용

 VM이 이전에 "All"으로 암호화된 경우 --volume 형식 매개 변수는 "모두"로 유지되어야 합니다. All은 OS 디스크 및 데이터 디스크 모두를 포함합니다. VM이 이전에 볼륨 유형의 "OS"로 암호화된 경우 OS와 새 데이터 디스크가 모두 포함되도록 --volume-type 매개 변수를 "All"으로 변경해야 합니다. VM이 "Data" 볼륨 유형으로만 암호화된 경우에는 아래 설명처럼 "Data"로 유지하면 됩니다. VM에 새 데이터 디스크를 추가하고 연결하는 것으로는 암호화 준비에 충분하지 않습니다. 암호화를 사용하도록 설정하기 전에 새로 연결된 디스크를 포맷하고 VM 내에서 올바르게 마운트해야 합니다. Linux에서는 디스크를 [영구 블록 디바이스 이름](troubleshoot-device-names-problems.md)으로 /etc/fstab에 마운트해야 합니다.  

Powershell 구문과 달리 CLI에서는 사용자가 암호화를 사용하도록 설정할 때 고유 시퀀스 버전을 제공하지 않아도 됩니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.

-  **실행 중인 VM의 데이터 볼륨 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **KEK를 사용하여 실행 중인 VM의 데이터 볼륨 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 Powershell을 사용하여 Linux용 새 디스크를 암호화하는 경우 새 순서 버전을 지정해야 합니다. 시퀀스 버전은 고유해야 합니다. 아래 스크립트는 시퀀스 버전에 대한 GUID를 생성합니다. 디스크가 암호화되기 전에 [스냅숏을](snapshot-copy-managed-disk.md) 만들고 [Azure Backup을](../../backup/backup-azure-vms-encryption.md) 통해 VM을 백업합니다. -skipVmBackup 매개 변수는 새로 추가된 데이터 디스크를 암호화하기 위해 PowerShell 스크립트에 이미 지정되어 있습니다.
 

-  **실행 중인 VM의 데이터 볼륨을 암호화합니다.** 아래 스크립트는 변수를 초기화하고 Set-AzVMDisk암호화확장 cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. 마이버추얼머신리소스그룹, 마이시큐어VM, 마이시큐어볼트를 가치로 바꿉습니다. -VolumeType 매개 변수에 허용되는 값은 All, OS 및 Data입니다. VM이 이전에 "OS" 또는 "All"의 볼륨 유형으로 암호화된 경우 -VolumeType 매개 변수를 "모두"로 변경하여 OS와 새 데이터 디스크가 모두 포함되도록 해야 합니다.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **KEK를 사용하여 실행 중인 VM의 데이터 볼륨 암호화:** -VolumeType 매개 변수에 허용되는 값은 All, OS 및 Data입니다. VM이 이전에 "OS" 또는 "All" 볼륨 유형으로 암호화된 경우에는 OS와 새 데이터 디스크가 모두 포함되도록 -VolumeType 매개 변수를 All로 변경해야 합니다.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > 디스크 암호화 키볼트 매개 변수값에 대한 구문은 전체 식별자 문자열입니다.</br> key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다. 


## <a name="disable-encryption-for-linux-vms"></a>Linux VM에 대한 암호화 사용 안 함
Azure PowerShell, Azure CLI 또는 Resource Manager 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. 

>[!IMPORTANT]
>Linux VM에서 Azure Disk Encryption을 통한 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다.  

- **Azure PowerShell을 사용하면 디스크 암호화를 사용하지 않도록 설정합니다.** 암호화를 사용하지 않도록 설정하려면 [사용 안 함-AzVMDisk암호화](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Azure CLI를 통한 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **리소스 관리자 템플릿으로 암호화를 사용하지 않도록 설정합니다.** 실행 [중인 Linux VM 템플릿에서 암호화 비활성화를](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 사용하여 암호화를 사용하지 않도록 설정합니다.
     1. **Deploy to Azure**를 클릭합니다.
     2. 구독, 리소스 그룹, 위치, VM, 약관 및 규약을 선택합니다.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

Azure 디스크 암호화는 다음 Linux 시나리오, 기능 및 기술에 대해 작동하지 않습니다.

- 클래식 VM 생성 방법을 통해 생성된 기본 계층 VM 또는 VM을 암호화합니다.
- OS 드라이브가 암호화될 때 Linux VM의 OS 드라이브 또는 데이터 드라이브에서 암호화를 사용하지 않도록 설정합니다.
- Linux 가상 시스템 스케일 집합용 OS 드라이브 암호화.
- Linux VM에서 사용자 지정 이미지 암호화.
- 온-프레미스 키 관리 시스템과의 통합.
- Azure 파일(공유 파일 시스템)
- NFS(네트워크 파일 시스템)
- 동적 볼륨
- 임시 OS 디스크.
- DFS, GFS, DRDB 및 CephFS와 같은 공유/분산 파일 시스템의 암호화(이에 국한되지 않음).
- 암호화된 VM을 다른 구독으로 이동합니다.
- 커널 크래시 덤프(kdump).

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
