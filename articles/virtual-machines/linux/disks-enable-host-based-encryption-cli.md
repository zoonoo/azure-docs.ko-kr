---
title: 호스트 - Azure CLI - 관리 디스크에서 암호화를 사용하여 엔드투엔드 암호화 사용
description: 호스트에서 암호화를 사용하여 Azure 관리 디스크에서 엔드투엔드 암호화를 사용하도록 설정합니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3eecb584f468bc170f0325da8d734a1890691483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601774"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure CLI를 사용하여 호스트에서 암호화를 사용하는 엔드투엔드 암호화를 사용하도록 설정

호스트에서 암호화를 사용하도록 설정하면 VM 호스트에 저장된 데이터는 미사용 및 스토리지 서비스로 암호화된 흐름으로 암호화됩니다. 호스트에서 암호화 및 기타 관리 디스크 암호화 유형에 대한 개념 정보는 [호스트에서 암호화 - VM 데이터에 대한 엔드투엔드 암호화](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 참조하세요.

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

VM 크기를 프로그래밍 방식으로 찾을 수도 있습니다. VM 크기를 프로그래밍 방식으로 검색하는 방법에 대한 자세한 내용은 [지원되는 VM 크기 찾기](#finding-supported-vm-sizes) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

VM/VMSS에 대한 EncryptionAtHost 속성을 사용하기 전에 구독에 대해 이 기능을 사용하도록 설정해야 합니다. 다음 단계를 수행하여 구독에 대한 기능을 사용하도록 설정하세요.

1.  다음 명령을 실행하여 구독에 대한 기능 등록

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  기능을 테스트하기 전에 아래 명령을 사용하여 등록 상태가 등록됨으로 나타나는지(몇 분 소요됨) 확인하세요.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault 및 DiskEncryptionSet 만들기

기능을 사용하도록 설정한 후에는 Azure Key Vault 및 DiskEncryptionSet를 아직 설정하지 않은 경우 설정해야 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>예제

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>호스트에서 고객 관리형 키로 암호화를 사용하도록 설정된 VM을 생성합니다. 

고객 관리형 키로 OS 및 데이터 디스크의 캐시를 암호화하기 위해 이전에 만든 DiskEncryptionSet의 리소스 URI를 사용하여 관리 디스크가 있는 VM을 만듭니다. 임시 디스크는 플랫폼 관리형 키를 사용하여 암호화됩니다. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>호스트에서 플랫폼 관리형 키로 암호화를 사용하도록 설정된 VM을 생성합니다. 

플랫폼 관리형 키를 사용하여 OS/데이터 디스크 및 임시 디스크의 캐시를 암호화하려면 호스트에서 암호화가 설정된 VM을 만듭니다. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>호스트에서 암호화를 사용하도록 VM을 업데이트합니다. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>호스트에서 VM에 대한 암호화 상태를 확인합니다.

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>호스트에서 고객 관리형 키로 암호화를 사용하도록 설정된 가상 머신 확장 집합을 만듭니다. 

고객 관리형 키로 OS 및 데이터 디스크의 캐시를 암호화하기 위해 앞서 만든 DiskEncryptionSet의 리소스 URI를 사용해 관리 디스크로 가상 머신 확장 집합을 만듭니다. 임시 디스크는 플랫폼 관리형 키를 사용하여 암호화됩니다. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>호스트에서 플랫폼 관리형 키를 사용하여 암호화를 사용하도록 설정된 가상 머신 확장 집합을 만듭니다. 

호스트에서 플랫폼 관리형 키를 사용하여 OS/데이터 디스크 및 임시 디스크의 캐시를 암호화하는 데 사용할 수 있는 암호화를 사용하여 가상 머신 확장 집합을 만듭니다. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>호스트에서 암호화를 사용하도록 가상 머신 확장 집합을 업데이트합니다. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>호스트에서 가상 머신 확장 집합에 대한 암호화 상태 확인

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>지원되는 VM 크기 찾기

레거시 VM 크기는 지원되지 않습니다. 다음 중 하나를 수행하여 지원되는 VM 크기 목록을 찾을 수 있습니다.

[리소스 SKU API](/rest/api/compute/resourceskus/list)를 호출하고 `EncryptionAtHostSupported` 기능이 **True** 로 설정되었는지 확인합니다.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

또는 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell cmdlet을 호출합니다.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>다음 단계

이제 리소스를 만들고 구성했으므로 해당 리소스를 사용하여 관리 디스크를 보호할 수 있습니다. 다음 링크에는 관리 디스크를 보호하는 데 사용할 수 있는 개별 시나리오를 포함하는 예제 스크립트가 포함되어 있습니다.

[Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
