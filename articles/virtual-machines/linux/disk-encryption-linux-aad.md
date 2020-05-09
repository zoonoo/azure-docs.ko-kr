---
title: Linux IaaS VM용 Azure AD 앱을 사용한 Azure Disk Encryption(이전 릴리스)
description: 이 문서에서는 Linux IaaS VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2ce3afb533aa33b88b15510eacc88c0884811cc6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792601"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Linux Vm에서 Azure AD를 사용 하 여 Azure Disk Encryption 사용 (이전 릴리스)

Azure Disk Encryption의 새 릴리스는 VM 디스크 암호화를 사용 하도록 설정 하기 위해 Azure Active Directory (Azure AD) 응용 프로그램 매개 변수를 제공 하는 요구 사항을 제거 합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 새 릴리스를 사용 하 여 Azure AD 응용 프로그램 매개 변수 없이 모든 새 Vm을 암호화 해야 합니다. 새 릴리스를 사용 하 여 VM 디스크 암호화를 사용 하도록 설정 하는 방법에 대 한 지침은 [LINUX vm에 대 한 Azure Disk Encryption](disk-encryption-linux.md)를 참조 하세요. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 AAD 구문을 사용하여 계속 유지 관리되어야 합니다.

많은 디스크 암호화 시나리오를 사용 하도록 설정할 수 있으며, 이러한 단계는 시나리오에 따라 달라질 수 있습니다. 다음 섹션에서는 Linux IaaS (infrastructure as a service) Vm에 대 한 보다 자세한 시나리오를 다룹니다. [지원 되는 VM 크기 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)의 가상 머신에만 디스크 암호화를 적용할 수 있습니다. 또한 다음 필수 구성 요소를 충족 해야 합니다.

- [Vm에 대 한 추가 요구 사항](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 및 그룹 정책](disk-encryption-overview-aad.md#networking-and-group-policy)
- [암호화 키 저장소 요구 사항](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

디스크를 암호화 하기 전에 [스냅숏](snapshot-copy-managed-disk.md)만들기, 백업 또는 둘 다를 수행 합니다. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 수행 된 후에는 AzVMDiskEncryptionExtension cmdlet을 사용 하 여-skipVmBackup 매개 변수를 지정 하 여 관리 디스크를 암호화할 수 있습니다. 암호화 된 Vm을 백업 및 복원 하는 방법에 대 한 자세한 내용은 [Azure Backup](../../backup/backup-azure-vms-encryption.md)를 참조 하세요. 

>[!WARNING]
 > - 이전에 [AZURE AD 앱과 Azure Disk Encryption](disk-encryption-overview-aad.md) 를 사용 하 여이 vm을 암호화 한 경우에는이 옵션을 계속 사용 하 여 vm을 암호화 해야 합니다. 이 암호화 된 vm에는 [Azure Disk Encryption](disk-encryption-overview.md) 를 사용할 수 없습니다 .이 시나리오는 지원 되지 않습니다. 즉,이 암호화 된 vm에 대 한 Azure AD 응용 프로그램에서 외부로의 전환이 아직 지원 되지 않습니다.
 > - 암호화 암호가 지역 경계를 넘지 않도록 하려면 키 자격 증명 모음과 Vm이 동일한 지역에 함께 배치 되어야 Azure Disk Encryption 합니다. 암호화할 VM과 동일한 지역에 있는 key vault를 만들고 사용 합니다.
 > - Linux OS 볼륨을 암호화 하는 경우 프로세스에 몇 시간이 걸릴 수 있습니다. Linux OS 볼륨은 암호화 하는 데 필요한 데이터 볼륨 보다 오래 걸리는 것이 일반적입니다.
> - Linux OS 볼륨을 암호화 하는 경우 VM을 사용할 수 없는 것으로 간주 해야 합니다. 암호화를 진행 하는 동안에는 암호화 프로세스 중에 액세스 해야 하는 열려 있는 파일을 차단 하지 않도록 하는 것이 좋습니다. 진행률을 확인 하려면 [AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 또는 [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용 합니다. 이 프로세스는 30GB OS 볼륨에는 몇 시간이 걸리고 데이터 볼륨을 암호화 하는 데는 시간이 더 걸릴 수 있습니다. 데이터 볼륨 암호화 시간은 **암호화 형식 모두 암호화** 옵션을 사용 하지 않는 한 데이터 볼륨의 크기와 양에 비례 합니다. 
 > - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화 된 경우에는 데이터 또는 OS 볼륨에서 지원 되지 않습니다. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> 기존 또는 실행 중인 IaaS Linux VM에서 암호화 사용

이 시나리오에서는 Azure Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI 명령을 사용 하 여 암호화를 사용 하도록 설정할 수 있습니다. 

>[!IMPORTANT]
 >Azure Disk Encryption를 사용 하도록 설정 하기 전에 스냅숏을 만들거나 관리 디스크 기반 VM 인스턴스를의 외부에서 백업 해야 합니다. Azure Portal에서 관리 디스크의 스냅숏을 만들거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md)를 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업이 수행 된 후-skipVmBackup 매개 변수를 지정 하 여 AzVMDiskEncryptionExtension cmdlet을 사용 하 여 관리 디스크를 암호화 합니다. AzVMDiskEncryptionExtension 명령은 백업이 수행 되 고이 매개 변수가 지정 될 때까지 관리 디스크 기반 Vm에 대해 실패 합니다. 
>
>암호화를 암호화 하거나 사용 하지 않도록 설정 하면 VM이 다시 부팅 될 수 있습니다. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Azure CLI를 사용 하 여 기존 또는 실행 중인 Linux VM에서 암호화 사용 
[Azure CLI 2.0](/cli/azure) 명령줄 도구를 설치 하 고 사용 하 여 암호화 된 VHD에서 디스크 암호화를 사용 하도록 설정할 수 있습니다. [Azure Cloud Shell](../../cloud-shell/overview.md)과 함께 브라우저에서 사용하거나 로컬 컴퓨터에 설치하여 PowerShell 세션에서 사용할 수 있습니다. Azure에서 기존 또는 실행 중인 IaaS Linux VM에 암호화를 사용하도록 설정하려면 다음 CLI 명령을 사용합니다.

Azure에서 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 명령을 사용하여 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다.

-  **클라이언트 암호를 사용 하 여 실행 중인 VM 암호화:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK를 사용 하 여 클라이언트 암호를 래핑하는 실행 중인 VM을 암호화 합니다.**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 디스크 암호화-keyvault 매개 변수 값에 대 한 구문은 전체 식별자 문자열입니다./subscriptions/[subscription-id]/Ssourceg/[/providers/Microsoft.KeyVault/vaults/] [keyvault-name].</br> </br> 키-암호화 키 매개 변수의 값 구문은 KEK의 전체 URI입니다. https://[keyvault-name]. KEK/keys/[kekname]/[].

- **디스크가 암호화 되었는지 확인 합니다.** IaaS VM의 암호화 상태를 확인 하려면 [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용 합니다. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> PowerShell을 사용 하 여 기존 또는 실행 중인 Linux VM에서 암호화 사용
[AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용 하 여 Azure에서 실행 중인 IaaS 가상 컴퓨터에서 암호화를 사용 하도록 설정 합니다. 디스크를 암호화 하기 전에 [스냅숏을](snapshot-copy-managed-disk.md) 만들거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md) 를 사용 하 여 VM의 백업을 수행 합니다. 실행 중인 Linux VM을 암호화 하기 위해 PowerShell 스크립트에-skipVmBackup 매개 변수가 이미 지정 되어 있습니다.

- **클라이언트 암호를 사용 하 여 실행 중인 VM 암호화:** 다음 스크립트는 변수를 초기화 하 고 AzVMDiskEncryptionExtension cmdlet을 실행 합니다. 리소스 그룹, VM, 키 자격 증명 모음, Azure AD 앱 및 클라이언트 암호는 필수 조건으로 이미 만들어져 있어야 합니다. MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, Mysecurevm, 및 사용자의 값으로 대체 합니다. -Vetype 매개 변수를 수정 하 여 암호화 하는 디스크를 지정 합니다.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **KEK를 사용 하 여 클라이언트 암호를 래핑하는 실행 중인 VM을 암호화 합니다.** Azure Disk Encryption를 통해 키 자격 증명 모음에 기존 키를 지정 하 여 암호화를 사용 하도록 설정 하는 동안 생성 된 디스크 암호화 암호를 래핑할 수 있습니다. 키 암호화 키를 지정 하면 Azure Disk Encryption 키 자격 증명 모음에 쓰기 전에 해당 키를 사용 하 여 암호화 비밀을 래핑합니다. -Vetype 매개 변수를 수정 하 여 암호화 하는 디스크를 지정 합니다. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > 디스크 암호화-keyvault 매개 변수 값에 대 한 구문은 전체 식별자 문자열입니다./subscriptions/[subscription-id]/Ssourceg/[KVresource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  키-암호화 키 매개 변수의 값 구문은 KEK의 전체 URI입니다. https://[keyvault-name]. KEK/keys/[kekname]/[]. 
    
- **디스크가 암호화 되었는지 확인 합니다.** IaaS VM의 암호화 상태를 확인 하려면 [AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet을 사용 합니다. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **디스크 암호화 사용 안 함: ** 암호화를 사용하지 않도록 설정하려면 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> 템플릿을 사용하여 기존 또는 실행 중인 IaaS Linux VM에서 암호화 사용

[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Linux VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **azure에 배포를** 선택 합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 매개 변수, 약관 및 규약을 선택합니다. **만들기** 를 선택 하 여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용 하도록 설정 합니다.

다음 표에 Azure AD 클라이언트 ID를 사용하는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수 목록이 나와 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| AADClientID | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 애플리케이션의 클라이언트 ID |
| AADClientSecret | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 애플리케이션의 클라이언트 ID |
| keyVaultName | 키가 업로드되어야 하는 Key Vault의 이름. `az keyvault show --name "MySecureVault" --query KVresourceGroup` Azure CLI cmdlet을 사용하여 가져올 수 있습니다. |
|  keyEncryptionKeyURL | 생성된 키를 암호화하는 데 사용되는 키 암호화 키의 URL. **UseExistingKek** 드롭다운 목록에서 **nokek** 를 선택 하는 경우이 매개 변수는 선택 사항입니다. **UseExistingKek** 드롭다운 목록에서 **kek** 를 선택 하는 경우 _하면 keyencryptionkeyurl_ 값을 입력 해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 지원 되는 유효한 값은 _OS_ 또는 _모두_입니다. 앞의 전제 조건 섹션에서 지원 되는 Linux 배포판 및 OS 및 데이터 디스크에 대 한 해당 버전을 참조 하세요. |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호화 작업이 수행될 때마다 이 버전 번호가 증가합니다. |
| vmName | 암호화 작업을 수행할 VM의 이름. |
| 암호 | 데이터 암호화 키로 강력한 암호를 입력합니다. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Linux IaaS vm의 데이터 디스크에 EncryptFormatAll 기능 사용
EncryptFormatAll 매개 변수는 Linux 데이터 디스크가 암호화되는 시간을 줄여줍니다. 특정 기준을 충족 하는 파티션은 현재 파일 시스템으로 포맷 됩니다. 그런 다음 명령 실행 전의 위치에 다시 탑재 합니다. 조건을 충족 하는 데이터 디스크를 제외 하려는 경우 명령을 실행 하기 전에 분리할 수 있습니다.

 이 명령을 실행 하면 이전에 탑재 된 모든 드라이브가 포맷 됩니다. 그런 다음 암호화 계층은 빈 드라이브의 맨 위에서 시작 됩니다. 이 옵션을 선택 하면 VM에 연결 된 임시 디스크도 암호화 됩니다. 임시 드라이브가 다시 설정 되 면 다음 기회에 Azure Disk Encryption 솔루션에 의해 VM에 대해 다시 포맷 되 고 다시 암호화 됩니다.

>[!WARNING]
> VM의 데이터 볼륨에 필요한 데이터가 있는 경우 EncryptFormatAll를 사용할 수 없습니다. 디스크를 탑재 해제 하 여 암호화에서 디스크를 제외할 수 있습니다. 먼저 테스트 VM에서 EncryptFormatAll 매개 변수를 사용해 보고 프로덕션 VM에서 사용해 보기 전에 기능 매개 변수 및 그에 해당 하는 의미를 이해 합니다. EncryptFormatAll 옵션은 데이터 디스크의 형식을 지정 하므로 데이터 디스크의 모든 데이터가 손실 됩니다. 계속 하기 전에 제외 하려는 디스크가 제대로 분리 되었는지 확인 합니다. </br></br>
 >암호화 설정을 업데이트 하는 동안이 매개 변수를 설정 하면 실제 암호화 전에 다시 부팅이 발생할 수 있습니다. 이 경우 fstab 파일에서 포맷 하지 않으려는 디스크를 제거 하는 것도 좋습니다. 마찬가지로 암호화 작업을 시작 하기 전에 암호화할 파티션을 fstab 파일에 추가 해야 합니다. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll 조건
매개 변수는 모든 파티션을 통과 하 고 다음 조건을 *모두* 충족 하는 동안 암호화 합니다. 
- 루트/OS/부팅 파티션이 아닙니다.
- 아직 암호화되지 않았습니다.
- BEK 볼륨이 아닙니다.
- RAID 볼륨이 아닙니다.
- LVM 볼륨이 아닙니다.
- 탑재되어 있습니다.

RAID 볼륨 또는 LVM 볼륨이 아닌, RAID 볼륨 또는 LVM 볼륨을 구성하는 디스크를 암호화합니다.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> 템플릿에서 EncryptFormatAll 매개변수 사용
EncryptFormatAll 옵션을 사용 하려면 Linux VM을 암호화 하 고 AzureDiskEncryption 리소스의 **작업** 필드를 변경 하는 기존 Azure Resource Manager 템플릿을 사용 합니다.

1. 예를 들어 [Resource Manager 템플릿을 사용하여 실행 중인 Linux IaaS VM을 암호화](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)합니다. 
2. Azure 빠른 시작 템플릿에서 **azure에 배포를** 선택 합니다.
3. \Operation **EncryptionOperation** 필드를 **Enableencryption** 에서 **enable\formatal**으로 변경 합니다.
4. 구독, 리소스 그룹, 리소스 그룹 위치, 기타 매개 변수, 약관 및 규약을 선택합니다. **만들기** 를 선택 하 여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용 하도록 설정 합니다.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> PowerShell cmdlet에서 EncryptFormatAll 매개변수 사용
EncryptFormatAll 매개 변수와 함께 [AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용 합니다.

**클라이언트 암호 및 EncryptFormatAll를 사용 하 여 실행 중인 VM을 암호화 합니다.** 예를 들어 다음 스크립트는 변수를 초기화 하 고 EncryptFormatAll 매개 변수를 사용 하 여 AzVMDiskEncryptionExtension cmdlet을 실행 합니다. 리소스 그룹, VM, 키 자격 증명 모음, Azure AD 앱 및 클라이언트 암호는 필수 조건으로 이미 만들어져 있어야 합니다. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, Mysecurevm, 및 사용자의 값으로 대체 합니다.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> LVM(논리 볼륨 관리자)에서 EncryptFormatAll 매개 변수 사용 
LVM-on-crypt 설정을 사용하는 것이 좋습니다. 다음 모든 예제에서 장치 경로 및 탑재 지점이를 사용 사례에 적합 한 것으로 바꿉니다. 이 설정은 다음과 같이 수행할 수 있습니다.

- VM을 구성할 데이터 디스크를 추가합니다.
- 이러한 디스크를 포맷하고, 탑재하고, fstab 파일에 추가합니다.

    1. 새로 추가된 디스크를 포맷합니다. 여기서는 Azure에서 생성한 바로 가기 링크를 사용합니다. 바로 가기 링크를 사용하면 디바이스 이름 변경과 관련된 문제를 방지할 수 있습니다. 자세한 내용은 [장치 이름 문제 해결](troubleshoot-device-names-problems.md)을 참조 하세요.
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. 디스크를 탑재합니다.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. fstab에 추가 합니다.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. -EncryptFormatAll을 사용 하 여 AzVMDiskEncryptionExtension PowerShell cmdlet을 실행 하 여 이러한 디스크를 암호화 합니다.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. 이러한 새 디스크를 기반으로 하여 LVM을 설정합니다. VM 부팅이 완료되면 암호화된 드라이브는 잠금 해제됩니다. 따라서 LVM 탑재도 나중으로 지연되어야 합니다.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> 고객 암호화 VHD 및 암호화 키로 만든 새 IaaS VM
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 다음 섹션에서는 Resource Manager 템플릿과 CLI 명령에 대해 자세히 설명합니다. 

부록의 지침을 사용하여 Azure에서 사용할 수 있는 미리 암호화된 이미지를 준비합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.

* [사전에 암호화된 Linux VHD 준비](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Azure Disk Encryption를 사용 하도록 설정 하기 전에 스냅숏을 만들거나 관리 디스크 기반 VM 인스턴스를의 외부에서 백업 해야 합니다. 포털에서 관리 디스크의 스냅숏을 만들거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md)를 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업이 수행 된 후-skipVmBackup 매개 변수를 지정 하 여 AzVMDiskEncryptionExtension cmdlet을 사용 하 여 관리 디스크를 암호화 합니다. AzVMDiskEncryptionExtension 명령은 백업이 수행 되 고이 매개 변수가 지정 될 때까지 관리 디스크 기반 Vm에 대해 실패 합니다. 
>
>암호화를 암호화 하거나 사용 하지 않도록 설정 하면 VM이 다시 부팅 될 수 있습니다.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Azure PowerShell을 사용하여 미리 암호화된 VHD가 있는 IaaS VM 암호화 
PowerShell cmdlet [AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)를 사용 하 여 암호화 된 VHD에서 디스크 암호화를 사용 하도록 설정할 수 있습니다. 다음 예제에서는 몇 가지 일반적인 매개 변수를 제공 합니다. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용
[Az vm disk attach](add-disk.md) 를 사용 하거나 [Azure Portal을 통해](attach-disk-portal.md)새 데이터 디스크를 추가할 수 있습니다. 암호화하려면 먼저 새로 연결된 데이터 디스크를 탑재해야 합니다. 암호화가 진행 중인 동안에는 드라이브를 사용할 수 없기 때문에 데이터 드라이브의 암호화를 요청 해야 합니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Azure CLI를 사용 하 여 새로 추가 된 디스크에서 암호화 사용
 VM이 "모두"로 이전에 암호화 된 경우--volume type 매개 변수는 모두 그대로 유지 되어야 합니다. All은 OS 디스크 및 데이터 디스크 모두를 포함합니다. VM이 이전에 "OS" 볼륨 유형으로 암호화 된 경우에는 OS와 새 데이터 디스크가 모두 포함 되도록--volume-type 매개 변수를 모두로 변경 해야 합니다. VM이 "Data" 볼륨 유형 으로만 암호화 된 경우에는 여기에 설명 된 대로 데이터를 유지할 수 있습니다. 새 데이터 디스크를 추가 하 고 VM에 연결 하는 것은 암호화를 준비 하는 데 충분 하지 않습니다. 암호화를 사용 하도록 설정 하기 전에 새로 연결 된 디스크도 포맷 되 고 VM 내에서 적절히 탑재 되어야 합니다. Linux에서 디스크는 [영구 블록 장치 이름을](troubleshoot-device-names-problems.md)사용 하 여/etc/fstab에 탑재 되어야 합니다. 

PowerShell 구문과 달리 CLI에서는 암호화를 사용 하도록 설정할 때 고유한 시퀀스 버전을 제공 하지 않아도 됩니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.

-  **클라이언트 암호를 사용 하 여 실행 중인 VM 암호화:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **KEK를 사용 하 여 클라이언트 암호를 래핑하는 실행 중인 VM을 암호화 합니다.**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 PowerShell을 사용 하 여 Linux 용 새 디스크를 암호화 하는 경우 새 시퀀스 버전을 지정 해야 합니다. 시퀀스 버전은 고유해야 합니다. 다음 스크립트는 시퀀스 버전에 대 한 GUID를 생성 합니다. 
 

-  **클라이언트 암호를 사용 하 여 실행 중인 VM 암호화:** 다음 스크립트는 변수를 초기화 하 고 AzVMDiskEncryptionExtension cmdlet을 실행 합니다. 리소스 그룹, VM, 키 자격 증명 모음, Azure AD 앱 및 클라이언트 암호는 필수 조건으로 이미 만들어져 있어야 합니다. MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, Mysecurevm, 및 사용자의 값으로 대체 합니다. -VolumeType 매개 변수는 OS 디스크가 아닌 데이터 디스크로 설정됩니다. VM이 이전에 "OS" 또는 "All" 볼륨 유형으로 암호화 된 경우에는 OS와 새 데이터 디스크가 모두 포함 되도록-Vetype 매개 변수를 All로 변경 해야 합니다.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **KEK를 사용 하 여 클라이언트 암호를 래핑하는 실행 중인 VM을 암호화 합니다.** Azure Disk Encryption를 통해 키 자격 증명 모음에 기존 키를 지정 하 여 암호화를 사용 하도록 설정 하는 동안 생성 된 디스크 암호화 암호를 래핑할 수 있습니다. 키 암호화 키를 지정 하면 Azure Disk Encryption 키 자격 증명 모음에 쓰기 전에 해당 키를 사용 하 여 암호화 비밀을 래핑합니다. -VolumeType 매개 변수는 OS 디스크가 아닌 데이터 디스크로 설정됩니다. VM이 이전에 "OS" 또는 "All" 볼륨 유형으로 암호화 된 경우에는 OS와 새 데이터 디스크가 모두 포함 되도록-Vetype 매개 변수를 All로 변경 해야 합니다.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> 디스크 암호화-keyvault 매개 변수 값에 대 한 구문은 전체 식별자 문자열입니다./subscriptions/[subscription-id]/Ssourceg/[/providers/Microsoft.KeyVault/vaults/] [keyvault-name]. </br> </br>
키-암호화 키 매개 변수의 값 구문은 KEK의 전체 URI입니다. https://[keyvault-name]. KEK/keys/[kekname]/[].

## <a name="disable-encryption-for-linux-vms"></a>Linux VM에 대한 암호화 사용 안 함
Azure PowerShell, Azure CLI 또는 리소스 관리자 템플릿을 사용 하 여 암호화를 사용 하지 않도록 설정할 수 있습니다. 

>[!IMPORTANT]
>Linux VM에서 Azure Disk Encryption을 통한 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화 된 경우에는 데이터 또는 OS 볼륨에서 지원 되지 않습니다. 

- **Azure PowerShell에서 디스크 암호화 사용 안 함:** 암호화를 사용 하지 않도록 설정 하려면 [AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용 합니다. 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Azure CLI를 통한 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **리소스 관리자 템플릿으로 암호화 사용 안 함:** 암호화를 사용 하지 않도록 설정 하려면 [실행 중인 LINUX VM에서 암호화 사용 안 함](https://aka.ms/decrypt-linuxvm) 템플릿을 사용 합니다.
     1. **Azure에 배포를**선택 합니다.
     2. 구독, 리소스 그룹, 위치, VM, 약관 및 규약을 선택합니다.
     3. 실행 중인 Windows VM에서 디스크 암호화를 사용 하지 않도록 설정 하려면 **구매** 를 선택 합니다. 


## <a name="next-steps"></a>다음 단계

- [Linux에 대 한 Azure Disk Encryption 개요](disk-encryption-overview-aad.md)
- [Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault-aad.md)
