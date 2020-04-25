---
title: Azure Managed Disks의 서버 쪽 암호화-Azure CLI
description: Azure Storage는 저장소 클러스터에 유지 하기 전에 미사용 데이터를 암호화 하 여 데이터를 보호 합니다. Microsoft 관리 키를 사용 하 여 관리 디스크의 암호화를 사용 하거나, 고객 관리 키를 사용 하 여 사용자 고유의 키로 암호화를 관리할 수 있습니다.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 027efd268ee80fbaf921b42d09cc424c8e8483ba
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136926"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure managed disks의 서버 쪽 암호화

Azure managed disks는 클라우드로 데이터를 유지할 때 기본적으로 데이터를 자동으로 암호화 합니다. 서버 쪽 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. Azure managed disks의 데이터는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 투명 하 게 암호화 되 고, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며, FIPS 140-2 규격입니다.   

암호화는 관리 디스크의 성능에 영향을 주지 않습니다. 암호화에 대 한 추가 비용은 없습니다.

암호화 모듈 기본 Azure managed disks에 대 한 자세한 내용은 [암호화 API: 차세대](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) 을 참조 하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리 키를 사용 하 여 관리 디스크의 암호화를 사용 하거나 사용자 고유의 키를 사용 하 여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용 하 여 암호화를 관리 하도록 선택 하는 경우 관리 디스크의 모든 데이터를 암호화 하 고 암호 해독 하는 데 사용할 *고객 관리 키* 를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대 한 각 옵션을 더 자세히 설명 합니다.

## <a name="platform-managed-keys"></a>플랫폼 관리 키

기본적으로 관리 디스크는 플랫폼 관리 암호화 키를 사용 합니다. 2017 년 6 월 10 일부 터 모든 새 관리 디스크, 스냅숏, 이미지 및 기존 관리 디스크에 기록 된 새 데이터는 플랫폼 관리 키를 사용 하 여 미사용 상태로 자동으로 암호화 됩니다.

## <a name="customer-managed-keys"></a>고객 관리형 키

사용자 고유의 키를 사용 하 여 각 관리 디스크의 수준에서 암호화를 관리 하도록 선택할 수 있습니다. 고객이 관리 하는 키를 사용 하는 관리 디스크에 대 한 서버 쪽 암호화는 Azure Key Vault 통합 된 환경을 제공 합니다. Key Vault [rsa 키](../../key-vault/keys/hsm-protected-keys.md) 를 가져오거나 Azure Key Vault에서 새 rsa 키를 생성할 수 있습니다. 

Azure managed disks는 [봉투 (envelope) 암호화](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)를 사용 하 여 완전히 투명 한 방식으로 암호화 및 암호 해독을 처리 합니다. 키를 사용 하 여 보호 되는 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 기반 dek (데이터 암호화 키)를 사용 하 여 데이터를 암호화 합니다. 저장소 서비스는 데이터 암호화 키를 생성 하 고 RSA 암호화를 사용 하 여 고객 관리 키로 암호화 합니다. 봉투 (envelope) 암호화를 사용 하면 Vm에 영향을 주지 않고 준수 정책에 따라 정기적으로 키를 회전 (변경) 할 수 있습니다. 키를 회전 하면 저장소 서비스는 새로운 고객 관리 키를 사용 하 여 데이터 암호화 키를 다시 암호화 합니다. 

DEK를 암호화 하 고 암호 해독 하는 데 키를 사용 하려면 Key Vault에서 관리 디스크에 대 한 액세스 권한을 부여 해야 합니다. 이를 통해 데이터 및 키를 완전히 제어할 수 있습니다. 언제 든 지 키를 사용 하지 않도록 설정 하거나 관리 디스크에 대 한 액세스를 취소할 수 있습니다. 또한 Azure Key Vault 모니터링을 사용 하 여 암호화 키 사용을 감사 하 여 관리 디스크 또는 다른 신뢰할 수 있는 Azure 서비스만 키에 액세스할 수 있도록 할 수 있습니다.

Premium Ssd, standard Ssd 및 standard Hdd의 경우: 키를 사용 하지 않도록 설정 하거나 삭제 하면 해당 키를 사용 하는 디스크가 있는 모든 Vm이 자동으로 종료 됩니다. 그런 다음 키를 다시 사용 하도록 설정 하지 않거나 새 키를 할당 하지 않는 한 Vm을 사용할 수 없습니다.

Ultra disks의 경우 키를 사용 하지 않도록 설정 하거나 삭제 하면 키를 사용 하는 울트라 디스크가 있는 모든 Vm이 자동으로 종료 되지 않습니다. Vm의 할당을 취소 하 고 다시 시작한 후에는 디스크에서 키 사용이 중지 되 고 Vm이 다시 온라인 상태가 되지 않습니다. Vm을 다시 온라인 상태로 전환 하려면 새 키를 할당 하거나 기존 키를 사용 하도록 설정 해야 합니다.

다음 다이어그램에서는 관리 디스크에서 Azure Active Directory 및 Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 사용 하 여 요청 하는 방법을 보여 줍니다.

![관리 되는 디스크 및 고객이 관리 하는 키 워크플로. 관리자는 Azure Key Vault 만든 다음 디스크 암호화 집합을 만들고 디스크 암호화 집합을 설정 합니다. 집합은 디스크에서 Azure AD를 사용 하 여 인증할 수 있도록 하는 VM에 연결 되어 있습니다.](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


다음 목록에서는 다이어그램에 대해 보다 자세히 설명 합니다.

1. Azure Key Vault 관리자가 주요 자격 증명 모음 리소스를 만듭니다.
1. 키 자격 증명 모음 관리자는 RSA 키를 가져와 Key Vault 하거나 Key Vault에서 새 RSA 키를 생성 합니다.
1. 관리자는 Azure Key Vault ID와 키 URL을 지정 하 여 디스크 암호화 집합 리소스의 인스턴스를 만듭니다. 디스크 암호화 집합은 관리 디스크에 대 한 키 관리를 간소화 하기 위해 도입 된 새로운 리소스입니다. 
1. 디스크 암호화 집합이 생성 되 면 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 는 AD (Azure Active Directory)에서 생성 되 고 디스크 암호화 집합과 연결 됩니다. 
1. 그런 다음 Azure key vault 관리자는 키 자격 증명 모음에서 작업을 수행할 수 있는 관리 id 권한을 부여 합니다.
1. VM 사용자는 디스크를 디스크 암호화 집합에 연결 하 여 디스크를 만듭니다. 또한 VM 사용자는 기존 리소스에 대해 고객 관리 키를 사용 하 여 서버 쪽 암호화를 사용 하도록 설정 하 여 디스크 암호화 집합에 연결할 수 있습니다. 
1. 관리 디스크는 관리 id를 사용 하 여 Azure Key Vault에 요청을 보냅니다.
1. 데이터를 읽거나 쓰기 위해 managed disks는 데이터 암호화 및 암호 해독을 수행 하기 위해 데이터 암호화 키를 암호화 (래핑) 하 고 해독 (래핑 해제) 하는 Azure Key Vault 요청을 보냅니다. 

고객 관리 키에 대 한 액세스를 취소 하려면 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 및 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure Storage 의해 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>제한

현재, 고객 관리 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해이 기능을 사용 하는 경우이 기능을 사용 하지 않도록 설정할 수 없습니다.
    이 문제를 해결 해야 하는 경우 고객 관리 키를 사용 하지 않는 완전히 다른 관리 디스크로 [모든 데이터를 복사](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 해야 합니다.
- 크기 2048의 ["소프트" 및 "하드" RSA 키](../../key-vault/keys/about-keys.md) 만 지원 되 고 다른 키 나 크기는 지원 되지 않습니다.
- 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 암호화 된 사용자 지정 이미지에서 만든 디스크는 동일한 고객 관리 키를 사용 하 여 암호화 해야 하며 동일한 구독에 있어야 합니다.
- 서버 쪽 암호화 및 고객이 관리 하는 키로 암호화 된 디스크에서 만든 스냅숏은 동일한 고객 관리 키를 사용 하 여 암호화 해야 합니다.
- 서버 쪽 암호화 및 고객 관리 키를 사용 하 여 암호화 된 사용자 지정 이미지는 공유 이미지 갤러리에서 사용할 수 없습니다.
- 고객 관리 키 (Azure 키 자격 증명 모음, 디스크 암호화 집합, Vm, 디스크 및 스냅숏)와 관련 된 모든 리소스는 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리 키로 암호화 된 디스크, 스냅숏 및 이미지는 다른 구독으로 이동할 수 없습니다.
- Azure Portal를 사용 하 여 디스크 암호화 집합을 만드는 경우 지금은 스냅숏을 사용할 수 없습니다.
- 고객 관리 키를 사용 하 여 암호화 된 관리 디스크는 Azure Disk Encryption로 암호화 될 수도 없습니다.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault 및 Diskset 설정

1. 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치했고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

1. Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만들 때 일시 삭제 및 보호 제거를 사용 하도록 설정 해야 합니다. 일시 삭제를 사용 하면 Key Vault 지정 된 보존 기간 (90 일 기본값) 동안 삭제 된 키를 보유 하 게 됩니다. 보호 제거를 사용 하면 보존 기간이 경과 때까지 삭제 된 키를 영구적으로 삭제할 수 없습니다. 이러한 설정은 실수로 인 한 삭제로 인해 데이터가 손실 되지 않도록 보호 합니다. 이러한 설정은 관리 디스크를 암호화 하는 데 Key Vault를 사용 하는 경우 필수입니다.

    > [!IMPORTANT]
    > 지역을 카멜식 대/소문자를 구분 하지 마십시오. 이렇게 하면 Azure Portal 리소스에 추가 디스크를 할당할 때 문제가 발생할 수 있습니다.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    DiskEncryptionSet의 인스턴스를 만듭니다. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    키 자격 증명 모음에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여 합니다. 

        > [!NOTE]
        > Azure가 Azure Active Directory에서 DiskEncryptionSet의 id를 만드는 데 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다."와 같은 오류가 발생 하면 몇 분 정도 기다린 후 다시 시도 하세요.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용 하 여 VM 만들기, 고객이 관리 하는 키를 사용 하 여 OS 및 데이터 디스크 암호화

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-managed-disks"></a>기존 관리 디스크 암호화 

다음 스크립트를 사용 하 여 기존 디스크를 암호화 하려면 실행 중인 VM에 연결 해서는 안 됩니다.

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace 이미지를 사용 하 여 가상 머신 확장 집합 만들기, 고객이 관리 하는 키를 사용 하 여 OS 및 데이터 디스크 암호화

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 사용 하 여 암호화 된 빈 디스크 만들기 및 VM에 연결

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Diskencryptionset의 키를 변경 하 여 DiskEncryptionSet를 참조 하는 모든 리소스에 대 한 키를 회전 합니다.

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>디스크의 서버 쪽 암호화 상태 찾기

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 고객 관리 키를 구성 하는 경우 관리 id는 내부적으로 리소스에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 [AZURE AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조 하세요.

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 고객 관리 키를 구성 하는 경우 관리 id는 내부적으로 리소스에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 [AZURE AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조 하세요.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure disk encryption

[가상 컴퓨터 및 가상 컴퓨터 크기 집합에 대 한 Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) 는 Windows의 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 기능과 Linux의 [DM-자리](https://en.wikipedia.org/wiki/Dm-crypt) 를 활용 하 여 게스트 VM 내에서 고객 관리 키를 사용 하 여 관리 되는 디스크를 암호화 합니다.  고객 관리 키를 사용 하는 서버 쪽 암호화는 저장소 서비스의 데이터를 암호화 하 여 Vm에 대 한 모든 OS 유형 및 이미지를 사용할 수 있도록 하 여 ADE에서 향상 됩니다.

## <a name="next-steps"></a>다음 단계

- [고객 관리 키를 사용 하 여 암호화 된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../../key-vault/general/overview.md)
- [고객 관리 키를 사용 하도록 설정 된 디스크를 사용 하 여 컴퓨터 복제](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
