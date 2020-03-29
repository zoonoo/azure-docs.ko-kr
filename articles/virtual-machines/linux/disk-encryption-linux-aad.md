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
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970618"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Linux VM에서 Azure AD를 사용하여 Azure 디스크 암호화 사용(이전 릴리스)

Azure 디스크 암호화의 새로운 릴리스는 VM 디스크 암호화를 사용하도록 설정하는 Azure Active Directory(Azure AD) 응용 프로그램 매개 변수를 제공하는 데 대한 요구 사항을 제거합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 모든 새 VM은 새 릴리스를 사용하여 Azure AD 응용 프로그램 매개 변수 없이 암호화되어야 합니다. 새 릴리스를 사용하여 VM 디스크 암호화를 사용하도록 설정하는 방법에 대한 지침은 [Linux VMS용 Azure 디스크 암호화를](disk-encryption-linux.md)참조하십시오. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 AAD 구문을 사용하여 계속 유지 관리되어야 합니다.

많은 디스크 암호화 시나리오를 활성화할 수 있으며 시나리오에 따라 단계가 달라질 수 있습니다. 다음 섹션에서는 서비스(IaaS) VM으로서 Linux 인프라에 대한 시나리오를 보다 자세히 다룹니다. [지원되는 VM 크기 및 운영 체제의](disk-encryption-overview.md#supported-vms-and-operating-systems)가상 컴퓨터에만 디스크 암호화를 적용할 수 있습니다. 또한 다음 필수 구성 조건을 충족해야 합니다.

- [VM에 대한 추가 요구 사항](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 및 그룹 정책](disk-encryption-overview-aad.md#networking-and-group-policy)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

디스크를 암호화하기 전에 [스냅숏을](snapshot-copy-managed-disk.md)만들고 백업을 수행하거나 둘 다 수행합니다. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 끝나면 Set-AzVMDisk암호화확장 cmdlet을 사용하여 -skipVmBackup 매개 변수를 지정하여 관리 디스크를 암호화할 수 있습니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 참조하십시오. 

>[!WARNING]
 > - 이전에 이 VM을 암호화하기 위해 [Azure AD 앱과 함께 Azure 디스크 암호화를](disk-encryption-overview-aad.md) 사용한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다. 이 암호화된 VM에 대한 Azure AD 응용 프로그램에서 전환하는 것은 아직 지원되지 않는 지원되는 시나리오가 아니므로 이 암호화된 VM에서 Azure [디스크 암호화를](disk-encryption-overview.md) 사용할 수 없습니다.
 > - 암호화 암호가 지역 경계를 넘지 않도록 하려면 Azure Disk 암호화에는 키 자격 증명 모음과 VM이 동일한 지역에 공동 으로 배치되어야 합니다. 암호화할 VM과 동일한 지역에 있는 키 자격 증명 모음을 만들고 사용합니다.
 > - Linux OS 볼륨을 암호화하면 몇 시간이 걸릴 수 있습니다. Linux OS 볼륨을 암호화하는 데 데이터 볼륨보다 시간이 오래 걸리는 것은 정상입니다.
> - Linux OS 볼륨을 암호화할 때 VM을 사용할 수 없는 것으로 간주해야 합니다. 암호화 프로세스 중에 액세스해야 하는 열려 있는 파일을 차단하지 않으려면 암호화가 진행되는 동안 SSH 로그인을 방지하는 것이 좋습니다. 진행률을 확인하려면 [Get-AzVMDisk암호화 상태](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 또는 [vm 암호화 표시](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용합니다. 이 프로세스는 30GB OS 볼륨에 몇 시간, 데이터 볼륨을 암호화하는 데 몇 시간이 걸릴 것으로 예상할 수 있습니다. 암호화 **형식이 모든** 옵션을 사용하지 않는 한 데이터 볼륨 암호화 시간은 데이터 볼륨의 크기 및 수량에 비례합니다. 
 > - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 데이터 또는 OS 볼륨에서 지원되지 않습니다. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> 기존 또는 실행 중인 IaaS Linux VM에서 암호화 사용

이 시나리오에서는 Azure 리소스 관리자 템플릿, PowerShell cmdlet 또는 Azure CLI 명령을 사용 하 여 암호화를 활성화할 수 있습니다. 

>[!IMPORTANT]
 >Azure 디스크 암호화를 사용하도록 설정하기 전에 스냅숏을 찍거나 관리되는 디스크 기반 VM 인스턴스를 백업해야 합니다. Azure 포털에서 관리 되는 디스크의 스냅숏을 만들거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업이 이루어진 후 Set-AzVMDisk암호화확장 cmdlet을 사용하여 -skipVmBackup 매개 변수를 지정하여 관리되는 디스크를 암호화합니다. Set-AzVMDisk암호화확장 명령은 백업이 이루어지고 이 매개 변수가 지정될 때까지 관리되는 디스크 기반 VM에 대해 실패합니다. 
>
>암호화를 암호화하거나 비활성화하면 VM이 재부팅될 수 있습니다. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Azure CLI를 사용하여 기존 또는 실행 중인 Linux VM에서 암호화를 사용하도록 설정합니다. 
[Azure CLI 2.0](/cli/azure) 명령줄 도구를 설치하고 사용하여 암호화된 VHD에서 디스크 암호화를 활성화할 수 있습니다. [Azure Cloud Shell](../../cloud-shell/overview.md)과 함께 브라우저에서 사용하거나 로컬 컴퓨터에 설치하여 PowerShell 세션에서 사용할 수 있습니다. Azure에서 기존 또는 실행 중인 IaaS Linux VM에 암호화를 사용하도록 설정하려면 다음 CLI 명령을 사용합니다.

Azure에서 [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) 명령을 사용하여 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다.

-  **클라이언트 암호를 사용하여 실행 중인 VM을 암호화합니다.**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK를 사용하여 클라이언트 암호를 래핑하여 실행 중인 VM을 암호화합니다.**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > 디스크 암호화 키볼트 매개 변수값에 대한 구문은 전체 식별자 문자열입니다.</br> </br> 키 암호화 키 매개 변수의 값에 대한 구문은 다음과 같이 KEK에 대한 전체 URI입니다.

- **디스크가 암호화되었는지 확인합니다.** IaaS VM의 암호화 상태를 확인하려면 [az vm 암호화 표시](/cli/azure/vm/encryption#az-vm-encryption-show) 명령을 사용합니다. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> PowerShell을 사용하여 기존 또는 실행 중인 Linux VM에서 암호화 사용
Azure에서 실행 중인 IaaS 가상 컴퓨터에서 암호화를 사용하도록 [설정-AzVMDisk암호화확장](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용합니다. 디스크가 암호화되기 전에 [스냅숏을](snapshot-copy-managed-disk.md) 만들거나 [Azure Backup을](../../backup/backup-azure-vms-encryption.md) 통해 VM을 백업합니다. -skipVmBackup 매개 변수는 이미 PowerShell 스크립트에 지정되어 실행 중인 Linux VM을 암호화합니다.

- **클라이언트 암호를 사용하여 실행 중인 VM을 암호화합니다.** 다음 스크립트는 변수를 초기화하고 Set-AzVMDisk암호화확장 cmdlet을 실행합니다. 리소스 그룹, VM, 키 자격 증명 모음, Azure AD 앱 및 클라이언트 보안 이 이미 필수 구성 으로 만들어졌습니다. 마이버추얼머신리소스그룹, 마이키볼트리소스그룹, 마이시큐어VM, 마이시큐어볼트, 마이-AAD-클라이언트-ID, 마이-AAD-클라이언트-시크릿을 값으로 바꿉습니다. -VolumeType 매개 변수를 수정하여 암호화할 디스크를 지정합니다.

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
- **KEK를 사용하여 클라이언트 암호를 래핑하여 실행 중인 VM을 암호화합니다.** Azure Disk 암호화를 사용하면 키 자격 증명 모음에 있는 기존 키를 지정하여 암호화를 사용하도록 설정하는 동안 생성된 디스크 암호화 비밀을 래핑할 수 있습니다. 키 암호화 키가 지정되면 Azure Disk Encryption은 해당 키를 사용하여 키 자격 증명 모음에 쓰기 전에 암호화 비밀을 래핑합니다. -VolumeType 매개 변수를 수정하여 암호화할 디스크를 지정합니다. 

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
  > 디스크 암호화 키볼트 매개 변수값에 대한 구문은 전체 식별자 문자열입니다.</br> </br>
  키 암호화 키 매개 변수의 값에 대한 구문은 다음과 같이 KEK에 대한 전체 URI입니다. 
    
- **디스크가 암호화되었는지 확인합니다.** IaaS VM의 암호화 상태를 확인하려면 [Get-AzVmDisk암호화 상태](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet을 사용합니다. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **디스크 암호화 사용 안 함: ** 암호화를 사용하지 않도록 설정하려면 [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> 템플릿을 사용하여 기존 또는 실행 중인 IaaS Linux VM에서 암호화 사용

[Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Linux VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.

1. Azure 빠른 시작 템플릿에서 **Azure에 배포를** 선택합니다.

2. 구독, 리소스 그룹, 리소스 그룹 위치, 매개 변수, 약관 및 규약을 선택합니다. 기존 또는 실행 중인 IaaS VM에서 암호화를 활성화하려면 **만들기를** 선택합니다.

다음 표에 Azure AD 클라이언트 ID를 사용하는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수 목록이 나와 있습니다.

| 매개 변수 | 설명 |
| --- | --- |
| AADClientID | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 애플리케이션의 클라이언트 ID |
| AADClientSecret | Key Vault에 비밀을 쓸 수 있는 권한이 있는 Azure AD 애플리케이션의 클라이언트 ID |
| keyVaultName | 키가 업로드되어야 하는 Key Vault의 이름. `az keyvault show --name "MySecureVault" --query KVresourceGroup` Azure CLI cmdlet을 사용하여 가져올 수 있습니다. |
|  keyEncryptionKeyURL | 생성된 키를 암호화하는 데 사용되는 키 암호화 키의 URL. **UseExistingKek** 드롭다운 목록에서 **nokek를** 선택하는 경우 이 매개 변수는 선택 사항입니다. **UseExistingKek** 드롭다운 목록에서 **kek를** 선택하는 경우 _키암호화키URL_ 값을 입력해야 합니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 지원되는 값은 _OS_ 또는 _모두_입니다. (지원되는 Linux 배포판 및 OS 및 데이터 디스크에 대한 해당 버전은 이전 필수 구성 조건 섹션의 참조). |
| sequenceVersion | BitLocker 작업의 시퀀스 버전. 동일한 VM에서 디스크 암호화 작업이 수행될 때마다 이 버전 번호가 증가합니다. |
| vmName | 암호화 작업을 수행할 VM의 이름. |
| 암호 | 데이터 암호화 키로 강력한 암호를 입력합니다. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>리눅스 IaaS VM의 데이터 디스크에 대한 암호화FormatAll 기능 사용
EncryptFormatAll 매개 변수는 Linux 데이터 디스크가 암호화되는 시간을 줄여줍니다. 특정 기준을 충족하는 파티션의 형식이 지정됩니다(현재 파일 시스템). 그런 다음 명령 실행 전의 위치에 다시 다시 탑재됩니다. 조건을 충족하는 데이터 디스크를 제외하려면 명령을 실행하기 전에 마운트를 취소할 수 있습니다.

 이 명령을 실행한 후에는 이전에 탑재된 모든 드라이브의 서식이 지정됩니다. 그런 다음 암호화 계층이 이제 빈 드라이브 위에서 시작됩니다. 이 옵션을 선택하면 VM에 연결된 임시 리소스 디스크도 암호화됩니다. 임시 드라이브가 재설정되면 다음 기회에 Azure 디스크 암호화 솔루션에 의해 VM에 대해 다시 포업되고 다시 암호화됩니다.

>[!WARNING]
> 암호화FormatAll VM의 데이터 볼륨에 필요한 데이터가 있을 때 사용 하지 않아야 합니다. 디스크를 마운트 해제하여 암호화에서 제외할 수 있습니다. 먼저 테스트 VM에서 암호화FormatAll 매개 변수를 사용해 보고 프로덕션 VM에서 시도하기 전에 기능 매개 변수와 그 의미를 이해합니다. 암호화FormatAll 옵션은 데이터 디스크를 포맷하므로 데이터 디스크에 있는 모든 데이터가 손실됩니다. 계속하기 전에 제외하려는 디스크가 제대로 마운트 해제되었는지 확인합니다. </br></br>
 >암호화 설정을 업데이트하는 동안 이 매개 변수를 설정하면 실제 암호화 전에 다시 부팅될 수 있습니다. 이 경우 fstab 파일에서 서식이 지정되지 않는 디스크를 제거하려고 합니다. 마찬가지로 암호화 작업을 시작하기 전에 암호화 형식이 지정된 파티션을 fstab 파일에 추가해야 합니다. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll 조건
매개 변수는 모든 파티션을 통과하고 다음 기준을 *모두* 충족하는 한 암호화합니다. 
- 루트/OS/부팅 파티션이 아닙니다.
- 아직 암호화되지 않았습니다.
- BEK 볼륨이 아닙니다.
- RAID 볼륨이 아닙니다.
- LVM 볼륨이 아닙니다.
- 탑재되어 있습니다.

RAID 볼륨 또는 LVM 볼륨이 아닌, RAID 볼륨 또는 LVM 볼륨을 구성하는 디스크를 암호화합니다.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> 템플릿에서 EncryptFormatAll 매개변수 사용
암호화FormatAll 옵션을 사용하려면 Linux VM을 암호화하고 AzureDisk암호화 리소스에 대한 **암호화 작업** 필드를 변경하는 기존 Azure 리소스 관리자 템플릿을 사용합니다.

1. 예를 들어 [Resource Manager 템플릿을 사용하여 실행 중인 Linux IaaS VM을 암호화](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)합니다. 
2. Azure 빠른 시작 템플릿에서 **Azure에 배포를** 선택합니다.
3. 암호화 **작업** 필드를 **사용 에서 암호화** 형식을 **사용하도록 설정하려면 암호화 를**변경합니다.
4. 구독, 리소스 그룹, 리소스 그룹 위치, 기타 매개 변수, 약관 및 규약을 선택합니다. 기존 또는 실행 중인 IaaS VM에서 암호화를 활성화하려면 **만들기를** 선택합니다.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> PowerShell cmdlet에서 EncryptFormatAll 매개변수 사용
암호화FormatAll 매개 변수와 [함께 Set-AzVMDisk암호화확장](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용합니다.

**클라이언트 암호 및 암호화FormatAll을 사용하여 실행 중인 VM을 암호화합니다.** 예를 들어 다음 스크립트는 변수를 초기화하고 암호화FormatAll 매개 변수를 사용 하 고 Set-AzVMDisk암호화 확장 cmdlet을 실행 합니다. 리소스 그룹, VM, 키 자격 증명 모음, Azure AD 앱 및 클라이언트 보안 이 이미 필수 구성 으로 만들어졌습니다. MyKeyVaultResourceGroup, 마이버추얼머신리소스그룹, 마이시큐어VM, 마이시큐어볼트, 마이-AAD-클라이언트-ID, 마이-AAD-클라이언트-비밀을 가치로 바꿉꿉을 바꿉습니다.
  
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
LVM-on-crypt 설정을 사용하는 것이 좋습니다. 다음의 모든 예제에서는 장치 경로 및 마운트 지점을 사용 사례에 적합한 대로 교체합니다. 이 설정은 다음과 같이 수행할 수 있습니다.

- VM을 구성할 데이터 디스크를 추가합니다.
- 이러한 디스크를 포맷하고, 탑재하고, fstab 파일에 추가합니다.

    1. 새로 추가된 디스크를 포맷합니다. 여기서는 Azure에서 생성한 바로 가기 링크를 사용합니다. 바로 가기 링크를 사용하면 디바이스 이름 변경과 관련된 문제를 방지할 수 있습니다. 자세한 내용은 [장치 이름 문제 해결을](troubleshoot-device-names-problems.md)참조하십시오.
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. 디스크를 탑재합니다.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. fstab에 추가 합니다.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. -암호화FormatAll을 사용하여 Set-AzVMDisk암호화확장 powerShell cmdlet을 실행하여 이러한 디스크를 암호화합니다.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. 이러한 새 디스크를 기반으로 하여 LVM을 설정합니다. VM 부팅이 완료되면 암호화된 드라이브는 잠금 해제됩니다. 따라서 LVM 탑재도 나중으로 지연되어야 합니다.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> 고객 암호화 VHD 및 암호화 키로 만든 새 IaaS VM
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 다음 섹션에서는 Resource Manager 템플릿과 CLI 명령에 대해 자세히 설명합니다. 

부록의 지침을 사용하여 Azure에서 사용할 수 있는 미리 암호화된 이미지를 준비합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.

* [사전에 암호화된 Linux VHD 준비](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Azure 디스크 암호화를 사용하도록 설정하기 전에 스냅숏을 찍거나 관리되는 디스크 기반 VM 인스턴스를 백업해야 합니다. 포털에서 관리 되는 디스크의 스냅숏을 만들거나 [Azure Backup](../../backup/backup-azure-vms-encryption.md)을 사용할 수 있습니다. Backup은 암호화 중에 예기치 않은 오류가 발생할 경우 복구 옵션으로 사용할 수 있습니다. 백업이 이루어진 후 Set-AzVMDisk암호화확장 cmdlet을 사용하여 -skipVmBackup 매개 변수를 지정하여 관리되는 디스크를 암호화합니다. Set-AzVMDisk암호화확장 명령은 백업이 이루어지고 이 매개 변수가 지정될 때까지 관리되는 디스크 기반 VM에 대해 실패합니다. 
>
>암호화를 암호화하거나 비활성화하면 VM이 재부팅될 수 있습니다.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Azure PowerShell을 사용하여 미리 암호화된 VHD가 있는 IaaS VM 암호화 
PowerShell cmdlet [Set-AzVMOSDisk를](/powershell/module/az.compute/set-azvmosdisk#examples)사용하여 암호화된 VHD에서 디스크 암호화를 활성화할 수 있습니다. 다음 예제에서는 몇 가지 일반적인 매개 변수를 제공합니다. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용
[az vm 디스크 연결](add-disk.md) 또는 Azure 포털을 통해 새 데이터 [디스크를](attach-disk-portal.md)추가할 수 있습니다. 암호화하려면 먼저 새로 연결된 데이터 디스크를 탑재해야 합니다. 암호화가 진행되는 동안 드라이브를 사용할 수 없으므로 데이터 드라이브의 암호화를 요청해야 합니다. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Azure CLI를 사용하여 새로 추가된 디스크에서 암호화 를 사용하도록 설정합니다.
 VM이 이전에 "All"으로 암호화된 경우 --volume 형식 매개 변수는 모두로 유지되어야 합니다. All은 OS 디스크 및 데이터 디스크 모두를 포함합니다. VM이 이전에 볼륨 유형의 "OS"로 암호화된 경우 OS와 새 데이터 디스크가 모두 포함되도록 --volume-type 매개 변수를 모두로 변경해야 합니다. VM이 볼륨 유형의 "데이터"로만 암호화된 경우 여기에 설명된 대로 Data를 유지할 수 있습니다. 새 데이터 디스크를 VM에 추가하고 첨부하는 것은 암호화를 위한 충분한 준비로 는 아닙니다. 암호화를 활성화하기 전에 새로 연결된 디스크도 VM 내에 포맷하고 적절하게 마운트해야 합니다. Linux에서 디스크는 [영구 블록 장치 이름으로](troubleshoot-device-names-problems.md)/etc/fstab에 탑재되어야 합니다. 

Powershell 구문과 달리 CLI는 암호화를 사용하도록 설정할 때 고유한 시퀀스 버전을 제공할 필요가 없습니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.

-  **클라이언트 암호를 사용하여 실행 중인 VM을 암호화합니다.** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **KEK를 사용하여 클라이언트 암호를 래핑하여 실행 중인 VM을 암호화합니다.**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 Powershell을 사용하여 Linux용 새 디스크를 암호화하는 경우 새 시퀀스 버전을 지정해야 합니다. 시퀀스 버전은 고유해야 합니다. 다음 스크립트는 시퀀스 버전에 대한 GUID를 생성합니다. 
 

-  **클라이언트 암호를 사용하여 실행 중인 VM을 암호화합니다.** 다음 스크립트는 변수를 초기화하고 Set-AzVMDisk암호화확장 cmdlet을 실행합니다. 리소스 그룹, VM, 키 자격 증명 모음, Azure AD 앱 및 클라이언트 보안 이 이미 필수 구성 으로 만들어졌습니다. 마이버추얼머신리소스그룹, 마이키볼트리소스그룹, 마이시큐어VM, 마이시큐어볼트, 마이-AAD-클라이언트-ID, 마이-AAD-클라이언트-시크릿을 값으로 바꿉습니다. -VolumeType 매개 변수는 OS 디스크가 아닌 데이터 디스크로 설정됩니다. VM이 이전에 "OS" 또는 "All"의 볼륨 유형으로 암호화된 경우 -VolumeType 매개 변수를 모두로 변경하여 OS와 새 데이터 디스크가 모두 포함되도록 해야 합니다.

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
- **KEK를 사용하여 클라이언트 암호를 래핑하여 실행 중인 VM을 암호화합니다.** Azure Disk 암호화를 사용하면 키 자격 증명 모음에 있는 기존 키를 지정하여 암호화를 사용하도록 설정하는 동안 생성된 디스크 암호화 비밀을 래핑할 수 있습니다. 키 암호화 키가 지정되면 Azure Disk Encryption은 해당 키를 사용하여 키 자격 증명 모음에 쓰기 전에 암호화 비밀을 래핑합니다. -VolumeType 매개 변수는 OS 디스크가 아닌 데이터 디스크로 설정됩니다. VM이 이전에 "OS" 또는 "All"의 볼륨 유형으로 암호화된 경우 -VolumeType 매개 변수를 모두로 변경하여 OS와 새 데이터 디스크가 모두 포함되도록 해야 합니다.

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
> 디스크 암호화 키볼트 매개 변수값에 대한 구문은 전체 식별자 문자열입니다. </br> </br>
키 암호화 키 매개 변수의 값에 대한 구문은 다음과 같이 KEK에 대한 전체 URI입니다.

## <a name="disable-encryption-for-linux-vms"></a>Linux VM에 대한 암호화 사용 안 함
Azure PowerShell, Azure CLI 또는 리소스 관리자 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. 

>[!IMPORTANT]
>Linux VM에서 Azure Disk Encryption을 통한 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 데이터 또는 OS 볼륨에서 지원되지 않습니다. 

- **Azure PowerShell을 사용하면 디스크 암호화를 사용하지 않도록 설정합니다.** 암호화를 사용하지 않도록 설정하려면 [사용 안 함-AzureRmVMDisk암호화](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다. 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Azure CLI를 통한 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 명령을 사용합니다. 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **리소스 관리자 템플릿으로 암호화를 사용하지 않도록 설정합니다.** 암호화를 사용하지 않으려면 [실행 중인 Linux VM 템플릿에서 암호화 사용 안 함(사용 안 함)을](https://aka.ms/decrypt-linuxvm) 사용합니다.
     1. **Azure에 배포를**선택합니다.
     2. 구독, 리소스 그룹, 위치, VM, 약관 및 규약을 선택합니다.
     3. **구입을** 선택하여 실행 중인 Windows VM에서 디스크 암호화를 사용하지 않도록 설정합니다. 


## <a name="next-steps"></a>다음 단계

- [리눅스 개요에 대 한 Azure 디스크 암호화](disk-encryption-overview-aad.md)
- [Azure AD를 사용한 Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성(이전 릴리스)](disk-encryption-key-vault-aad.md)
