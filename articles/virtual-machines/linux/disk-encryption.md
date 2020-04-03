---
title: Azure 관리 디스크의 서버 측 암호화 - Azure CLI
description: Azure Storage는 저장 클러스터에 보관하기 전에 미사용 데이터를 암호화하여 데이터를 보호합니다. 관리 디스크의 암호화를 위해 Microsoft에서 관리하는 키를 사용하거나 고객 관리 키를 사용하여 자체 키로 암호화를 관리할 수 있습니다.
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 912677a10d7098b891a4f6972b61761cd72cf292
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585936"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure 관리 디스크의 서버 측 암호화

Azure 관리 디스크는 클라우드에 데이터를 유지하면 기본적으로 데이터를 자동으로 암호화합니다. 서버 측 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. Azure 관리 디스크의 데이터는 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 [AES 암호화를](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)사용하여 투명하게 암호화되며 FIPS 140-2를 준수합니다.   

암호화는 관리되는 디스크의 성능에 영향을 미치지 않습니다. 암호화에 대한 추가 비용은 없습니다.

Azure 관리 디스크의 기본 암호화 모듈에 대한 자세한 내용은 [암호화 API: 차세대 를](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) 참조하십시오.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

관리 디스크의 암호화를 위해 플랫폼 관리 키에 의존하거나 자체 키를 사용하여 암호화를 관리할 수 있습니다. 자체 키로 암호화를 관리하도록 선택한 경우 관리 디스크의 모든 데이터를 암호화하고 해독하는 데 사용할 *고객 관리 키를* 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대한 각 옵션을 자세히 설명합니다.

## <a name="platform-managed-keys"></a>플랫폼 관리 키

기본적으로 관리되는 디스크는 플랫폼 관리 암호화 키를 사용합니다. 2017년 6월 10일부터 기존 관리 디스크에 기록된 모든 새 관리 디스크, 스냅샷, 이미지 및 새 데이터는 플랫폼 관리 키를 사용하여 미사용 으로 자동으로 암호화됩니다.

## <a name="customer-managed-keys"></a>고객 관리형 키

고유한 키를 사용하여 각 관리 디스크 수준에서 암호화를 관리하도록 선택할 수 있습니다. 고객 관리 키가 있는 관리 디스크에 대한 서버 측 암호화는 Azure Key Vault와 통합된 환경을 제공합니다. [RSA 키를](../../key-vault/key-vault-hsm-protected-keys.md) 키 볼트로 가져오거나 Azure 키 자격 증명 모음에서 새 RSA 키를 생성할 수 있습니다. 

Azure 관리 디스크는 봉투 암호화를 사용하여 완전히 투명한 방식으로 암호화 및 암호 [해독을](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)처리합니다. [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 기반 데이터 암호화 키(DEK)를 사용하여 데이터를 암호화하며, 이 키는 키를 사용하여 보호됩니다. 저장소 서비스는 RSA 암호화를 사용하여 데이터 암호화 키를 생성하고 고객 관리 키로 암호화합니다. 봉투 암호화를 사용하면 VM에 영향을 주지 않고 규정 준수 정책에 따라 주기적으로 키를 회전(변경)할 수 있습니다. 키를 회전하면 저장소 서비스는 새 고객 관리 키로 데이터 암호화 키를 다시 암호화합니다. 

DEK를 암호화하고 해독하기 위해 키를 사용하려면 키 볼트의 관리 디스크에 대한 액세스 권한을 부여해야 합니다. 이렇게 하면 데이터와 키를 완전히 제어할 수 있습니다. 언제든지 키를 비활성화하거나 관리 디스크에 대한 액세스를 취소할 수 있습니다. 또한 Azure Key Vault 모니터링을 사용하여 암호화 키 사용량을 감사하여 관리되는 디스크 또는 기타 신뢰할 수 있는 Azure 서비스만 키에 액세스하고 있는지 확인할 수 있습니다.

프리미엄 SSD, 표준 SSD 및 표준 HDD의 경우: 키를 비활성화하거나 삭제하면 해당 키를 사용하는 디스크가 있는 모든 VM이 자동으로 종료됩니다. 그런 다음 키를 다시 사용하거나 새 키를 할당하지 않으면 VM을 사용할 수 없습니다.

울트라 디스크의 경우 키를 비활성화하거나 삭제하면 키를 사용하는 울트라 디스크가 있는 VM은 자동으로 종료되지 않습니다. VM을 할당 하고 다시 시작하면 디스크가 키 사용을 중지한 다음 VM이 다시 온라인 상태가 되지 않습니다. VM을 다시 온라인 상태가 하려면 새 키를 할당하거나 기존 키를 사용하도록 설정해야 합니다.

다음 다이어그램에서는 관리되는 디스크가 Azure Active Directory 및 Azure Key Vault를 사용하여 고객 관리 키를 사용하여 요청을 하는 방법을 보여 주십습니다.

![관리되는 디스크 및 고객 관리 키 워크플로우. 관리자는 Azure Key Vault를 만들고 디스크 암호화 집합을 만들고 디스크 암호화 집합을 설정합니다. 집합은 VM에 연결되어 있으므로 디스크에서 Azure AD를 사용하여 인증할 수 있습니다.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


다음 목록에서는 다이어그램을 자세히 설명합니다.

1. Azure 키 볼트 관리자가 키 자격 증명 모음 리소스를 만듭니다.
1. 키 볼트 관리자는 RSA 키를 키 볼트로 가져오거나 키 볼트에서 새 RSA 키를 생성합니다.
1. 해당 관리자는 Azure Key Vault ID 및 키 URL을 지정하는 디스크 암호화 집합 리소스의 인스턴스를 만듭니다. 디스크 암호화 집합은 관리디스크의 키 관리를 단순화하기 위해 도입된 새로운 리소스입니다. 
1. 디스크 암호화 집합이 만들어지면 Azure Active Directory(AD)에서 [시스템 할당된 관리 ID가](../../active-directory/managed-identities-azure-resources/overview.md) 만들어지고 디스크 암호화 집합과 연결됩니다. 
1. 그런 다음 Azure 키 자격 증명 모음 관리자는 키 자격 증명 모음에서 작업을 수행할 수 있는 관리되는 ID 권한을 부여합니다.
1. VM 사용자는 디스크 암호화 세트와 연결하여 디스크를 만듭니다. VM 사용자는 또한 디스크 암호화 세트와 연결하여 기존 리소스에 대한 고객 관리 키로 서버 측 암호화를 활성화할 수도 있습니다. 
1. 관리되는 디스크는 관리되는 ID를 사용하여 Azure Key Vault에 요청을 보냅니다.
1. 데이터를 읽거나 쓰기 위해 관리되는 디스크는 데이터의 암호화 및 암호 해독을 수행하기 위해 Azure Key Vault에 요청을 보내 데이터 암호화 키를 암호화(래핑)하고 암호화(래핑 해제)합니다. 

고객 관리 키에 대한 액세스를 취소하려면 [Azure 키 볼트 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure 키 볼트 CLI를](https://docs.microsoft.com/cli/azure/keyvault)참조하십시오. Azure Storage에서 암호화 키에 액세스할 수 없기 때문에 액세스를 취소하면 저장소 계정의 모든 데이터에 대한 액세스가 효과적으로 차단됩니다.

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>제한

현재 고객 관리 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 이 기능을 사용하도록 설정하면 비활성화할 수 없습니다.
    이 방법을 해결해야 하는 경우 모든 데이터를 고객 관리 키를 사용하지 않는 완전히 다른 관리 디스크에 [복사해야](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 합니다.
- 크기 2048의 ["소프트" 및 "하드" RSA 키만](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) 지원되며 다른 키나 크기는 지원되지 않습니다.
- 서버 측 암호화 및 고객 관리 키를 사용하여 암호화된 사용자 지정 이미지에서 생성된 디스크는 동일한 고객 관리 키를 사용하여 암호화되어야 하며 동일한 구독에 있어야 합니다.
- 서버 측 암호화 및 고객 관리 키로 암호화된 디스크에서 생성된 스냅숏은 동일한 고객 관리 키로 암호화해야 합니다.
- 서버 측 암호화 및 고객 관리 키를 사용하여 암호화된 사용자 지정 이미지는 공유 이미지 갤러리에서 사용할 수 없습니다.
- 고객 관리 키(Azure Key Vault, 디스크 암호화 세트, VM, 디스크 및 스냅숏)와 관련된 모든 리소스는 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리 키로 암호화된 디스크, 스냅숏 및 이미지는 다른 구독으로 이동할 수 없습니다.
- Azure 포털을 사용하여 디스크 암호화 집합을 만드는 경우 지금은 스냅숏을 사용할 수 없습니다.
- 고객 관리 키를 사용하여 암호화된 관리 디스크도 Azure 디스크 암호화로 암호화할 수 없습니다.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Azure 키 자격 증명 모음 및 디스크 암호화 집합 설정

1. 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치했고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

1. Azure 키 자격 증명 모음 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만들 때는 소프트 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 소프트 삭제는 Key Vault가 지정된 보존 기간(90일 기본값)에 대해 삭제된 키를 보유하도록 합니다. 제거 보호는 보존 기간이 경과할 때까지 삭제된 키를 영구적으로 삭제할 수 없도록 합니다. 이러한 설정은 실수로 삭제되어 데이터가 손실되지 않도록 보호합니다. 관리되는 디스크를 암호화하기 위해 Key Vault를 사용할 때 이러한 설정은 필수입니다.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    디스크 암호화 집합의 인스턴스를 만듭니다. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    키 자격 증명 모음에 DiskEncryptionSet 리소스 액세스를 부여합니다. 

        > [!NOTE]
        > Azure에서 Azure Active 디렉터리에서 DiskEncryptionSet의 ID를 만드는 데 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다"와 같은 오류가 발생하면 몇 분 간 기다렸다가 다시 시도하십시오.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>마켓플레이스 이미지를 사용하여 VM을 생성하여 고객 관리 키로 OS 및 데이터 디스크를 암호화합니다.

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>첨부되지 않은 기존 관리 디스크 암호화 

다음 스크립트를 사용하여 디스크를 암호화하려면 기존 디스크를 실행 중인 VM에 연결해서는 안 됩니다.

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>마켓플레이스 이미지를 사용하여 가상 머신 스케일 세트를 생성하여 고객 관리 키로 OS 및 데이터 디스크를 암호화합니다.

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>고객 관리 키가 있는 서버 측 암호화를 사용하여 암호화된 빈 디스크를 만들고 VM에 연결합니다.

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>DiskEncryptionSet을 참조하는 모든 리소스에 대한 키를 회전하도록 DiskEncryptionSet의 키를 변경합니다.

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>디스크의 서버 측 암호화 상태 찾기

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> 고객 관리 키는 Azure Active Directory(Azure AD)의 기능인 Azure 리소스에 대해 관리되는 ID를 사용합니다. 고객 관리 키를 구성하면 관리되는 ID가 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 한 Azure AD 디렉터리에서 다른 Azure AD 디렉터리로 이동하면 관리 디스크와 연결된 관리되는 ID가 새 테넌트로 전송되지 않으므로 고객 관리 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 [Azure AD 디렉터리 간의 구독 전송을](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)참조하십시오.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 고객 관리 키는 Azure Active Directory(Azure AD)의 기능인 Azure 리소스에 대해 관리되는 ID를 사용합니다. 고객 관리 키를 구성하면 관리되는 ID가 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 한 Azure AD 디렉터리에서 다른 Azure AD 디렉터리로 이동하면 관리 디스크와 연결된 관리되는 ID가 새 테넌트로 전송되지 않으므로 고객 관리 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 [Azure AD 디렉터리 간의 구독 전송을](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)참조하십시오.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 측 암호화 와 Azure 디스크 암호화

[가상 시스템 및 가상 시스템 규모 집합에 대한 Azure 디스크 암호화는](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) Windows의 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 활용하여 게스트 VM 내에서 고객 관리 키로 관리되는 디스크를 암호화합니다.  고객 관리 키가 있는 서버 측 암호화는 저장소 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 하여 ADE에서 개선됩니다.

## <a name="next-steps"></a>다음 단계

- [고객 관리 키로 암호화된 디스크를 만들기 위한 Azure 리소스 관리자 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../../key-vault/key-vault-overview.md)
- [고객 관리 키가 활성화된 디스크로 컴퓨터 복제](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
