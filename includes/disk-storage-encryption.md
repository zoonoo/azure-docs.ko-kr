---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 762d6991eb8c45abc7de4f331f1b9335d68c0143
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "73034532"
---
Azure managed disks는 클라우드로 데이터를 유지할 때 기본적으로 데이터를 자동으로 암호화 합니다. 서버 쪽 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. Azure managed disks의 데이터는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 투명 하 게 암호화 되 고, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며, FIPS 140-2 규격입니다.   

암호화는 관리 디스크의 성능에 영향을 주지 않습니다. 암호화에 대 한 추가 비용은 없습니다.

암호화 모듈 기본 Azure managed disks에 대 한 자세한 내용은 [암호화 API: 차세대](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) 을 참조 하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리 키를 사용 하 여 관리 디스크의 암호화를 사용 하거나 사용자 고유의 키 (공개 미리 보기)를 사용 하 여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용 하 여 암호화를 관리 하도록 선택 하는 경우 관리 디스크의 모든 데이터를 암호화 하 고 암호 해독 하는 데 사용할 *고객 관리 키* 를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대 한 각 옵션을 더 자세히 설명 합니다.

## <a name="platform-managed-keys"></a>플랫폼 관리 키

기본적으로 관리 디스크는 플랫폼 관리 암호화 키를 사용 합니다. 2017 년 6 월 10 일을 기준으로 기존 관리 디스크에 기록 된 모든 새 관리 디스크, 스냅숏, 이미지 및 새 데이터는 플랫폼 관리 키를 사용 하 여 미사용으로 자동으로 암호화 됩니다. 

## <a name="customer-managed-keys-public-preview"></a>고객 관리 키 (공개 미리 보기)

사용자 고유의 키를 사용 하 여 각 관리 디스크의 수준에서 암호화를 관리 하도록 선택할 수 있습니다. 고객이 관리 하는 키를 사용 하는 관리 디스크에 대 한 서버 쪽 암호화는 Azure Key Vault 통합 된 환경을 제공 합니다. Key Vault [rsa 키](../articles/key-vault/key-vault-hsm-protected-keys.md) 를 가져오거나 Azure Key Vault에서 새 rsa 키를 생성할 수 있습니다. Azure managed disks는 [봉투 (envelope) 암호화](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique)를 사용 하 여 완전히 투명 한 방식으로 암호화 및 암호 해독을 처리 합니다. 키를 사용 하 여 보호 되는 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 기반 dek (데이터 암호화 키)를 사용 하 여 데이터를 암호화 합니다. DEK를 암호화 하 고 암호 해독 하는 데 키를 사용 하려면 Key Vault에서 관리 디스크에 대 한 액세스 권한을 부여 해야 합니다. 이를 통해 데이터 및 키를 완전히 제어할 수 있습니다. 언제 든 지 키를 사용 하지 않도록 설정 하거나 관리 디스크에 대 한 액세스를 취소할 수 있습니다. 또한 Azure Key Vault 모니터링을 사용 하 여 암호화 키 사용을 감사 하 여 관리 디스크 또는 다른 신뢰할 수 있는 Azure 서비스만 키에 액세스할 수 있도록 할 수 있습니다.

다음 다이어그램에서는 관리 디스크에서 Azure Active Directory 및 Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 사용 하 여 요청을 수행 하는 방법을 보여 줍니다.

![관리 디스크 고객 관리 키 워크플로](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


다음 목록에서는 다이어그램의 번호가 매겨진 단계에 대해 설명 합니다.

1. Azure Key Vault 관리자가 주요 자격 증명 모음 리소스를 만듭니다.
1. 키 자격 증명 모음 관리자는 RSA 키를 가져와 Key Vault 하거나 Key Vault에서 새 RSA 키를 생성 합니다.
1. 관리자는 Azure Key Vault ID와 키 URL을 지정 하 여 디스크 암호화 집합 리소스의 인스턴스를 만듭니다. 디스크 암호화 집합은 관리 디스크에 대 한 키 관리를 간소화 하기 위해 도입 된 새로운 리소스입니다. 
1. 디스크 암호화 집합이 생성 되 면 [시스템 할당 관리 id](../articles/active-directory/managed-identities-azure-resources/overview.md) 가 Azure AD (active directory)에서 생성 되 고 디스크 암호화 집합과 연결 됩니다. 
1. 그런 다음 Azure key vault 관리자는 키 자격 증명 모음에서 작업을 수행할 수 있는 관리 id 권한을 부여 합니다.
1. VM 사용자는 디스크를 디스크 암호화 집합에 연결 하 여 디스크를 만듭니다. 또한 VM 사용자는 기존 리소스에 대해 고객 관리 키를 사용 하 여 서버 쪽 암호화를 사용 하도록 설정 하 여 디스크 암호화 집합에 연결할 수 있습니다. 
1. 관리 디스크는 관리 id를 사용 하 여 Azure Key Vault에 요청을 보냅니다.
1. 데이터를 읽거나 쓰기 위해 managed disks는 데이터 암호화 및 암호 해독을 수행 하기 위해 데이터 암호화 키를 암호화 (래핑) 하 고 해독 (래핑 해제) 하는 Azure Key Vault 요청을 보냅니다. 

고객 관리 키에 대 한 액세스를 취소 하려면 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure Storage 의해 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

### <a name="supported-scenarios-and-restrictions"></a>지원 되는 시나리오 및 제한 사항

미리 보기 중에는 다음 시나리오만 지원 됩니다.

- Azure Marketplace 이미지에서 VM (가상 머신)을 만들고 고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 사용 하 여 OS 디스크를 암호화 합니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키로 암호화 된 사용자 지정 이미지를 만듭니다.
- 사용자 지정 이미지에서 VM을 만들고 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 OS 디스크를 암호화 합니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키를 사용 하 여 암호화 된 데이터 디스크를 만듭니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키를 사용 하 여 암호화 된 스냅숏을 만듭니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키를 사용 하 여 암호화 된 가상 머신 확장 집합을 만듭니다.

미리 보기에는 다음과 같은 제한 사항도 있습니다.

- 미국 서 부 에서만 사용할 수 있습니다.
- 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 암호화 된 사용자 지정 이미지에서 만든 디스크는 동일한 고객 관리 키를 사용 하 여 암호화 해야 하며 동일한 구독에 있어야 합니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키로 암호화 된 디스크에서 만든 스냅숏은 동일한 고객 관리 키를 사용 하 여 암호화 해야 합니다.
- 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 암호화 된 사용자 지정 이미지는 공유 이미지 갤러리에서 사용할 수 없습니다.
- Key Vault은 고객이 관리 하는 키와 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리 키로 암호화 된 디스크, 스냅숏 및 이미지는 다른 구독으로 이동할 수 없습니다.

### <a name="setting-up-your-azure-key-vault"></a>Azure Key Vault 설정

1.  Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만들 때 일시 삭제 및 보호 제거를 사용 하도록 설정 해야 합니다. 일시 삭제를 사용 하면 Key Vault 지정 된 보존 기간 (90 일 기본값) 동안 삭제 된 키를 보유 하 게 됩니다. 보호 제거를 사용 하면 보존 기간이 경과 때까지 삭제 된 키를 영구적으로 삭제할 수 없습니다. 이러한 설정은 실수로 인 한 삭제로 인해 데이터가 손실 되지 않도록 보호 합니다. 이러한 설정은 관리 디스크를 암호화 하는 데 Key Vault를 사용 하는 경우 필수입니다.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  DiskEncryptionSet의 인스턴스를 만듭니다. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  키 자격 증명 모음에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여 합니다.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Marketplace 이미지를 사용 하 여 VM 만들기, 리소스 관리자 템플릿을 통해 고객 관리 키로 OS 및 데이터 디스크 암호화

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 사용 하 여 암호화 된 빈 디스크 만들기 및 VM에 연결

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
```


> [!IMPORTANT]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 고객 관리 키를 구성 하는 경우 관리 id는 내부적으로 리소스에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 [AZURE AD 디렉터리 간에 구독 전송](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조 하세요.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure disk encryption

[Azure Disk Encryption](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) 는 Windows의 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 기능과 Linux의 [DM-자리](https://en.wikipedia.org/wiki/Dm-crypt) 를 활용 하 여 게스트 VM 내에서 고객 관리 키를 사용 하 여 관리 디스크를 암호화 합니다.  고객 관리 키를 사용 하는 서버 쪽 암호화는 저장소 서비스의 데이터를 암호화 하 여 Vm에 대 한 모든 OS 유형 및 이미지를 사용할 수 있도록 하 여 ADE에서 향상 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../articles/key-vault/key-vault-overview.md)
