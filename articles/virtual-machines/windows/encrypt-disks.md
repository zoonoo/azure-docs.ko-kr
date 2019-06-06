---
title: Azure에서 Windows VM의 디스크 암호화 | Microsoft Docs
description: Azure PowerShell을 사용하여 보안 강화를 위해 Windows VM에서 가상 디스크 암호화
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 4ef485bb91fe52e138b805f347e729fc4097fc7c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431102"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Windows VM에서 가상 디스크 암호화
VM(가상 머신)의 보안과 규정 준수 상태를 향상시키기 위해 Azure에서 가상 디스크를 암호화할 수 있습니다. 디스크는 Azure Key Vault에 안전하게 보관되는 암호화 키를 사용하여 암호화됩니다. 이러한 암호화 키를 제어하고 용도를 감사할 수 있습니다. 이 문서에서는 Azure PowerShell을 사용하여 Windows VM에서 가상 디스크를 암호화하는 방법을 설명합니다. [Azure CLI를 사용하여 Linux VM을 암호화](../linux/encrypt-disks.md)할 수도 있습니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>디스크 암호화 개요
Windows VM의 가상 디스크는 미사용 시 BitLocker를 사용하여 암호화됩니다. Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. 

VM을 암호화하는 프로세스는 다음과 같습니다.

1. Azure Key Vault에서 암호화 키를 만듭니다.
1. 암호화하는 디스크에 사용될 수 있도록 암호화 키를 구성합니다.
1. 가상 디스크에 대한 디스크 암호화를 사용합니다.
1. Azure Key Vault에서 필요한 암호화 키를 요청합니다.
1. 가상 디스크는 제공된 암호화 키를 사용하여 암호화됩니다.


## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항

디스크 암호화에 대해 지원되는 시나리오 및 요구 사항은 다음과 같습니다.

* Azure Marketplace 이미지 또는 사용자 지정 VHD 이미지에서 새 Windows VM에 대해 암호화 사용
* Azure에서 기존 Windows VM에 대해 암호화 사용
* 스토리지 공간을 사용하여 구성된 Windows VM에서 암호화 사용
* Windows VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* 표준 계층 VM(예: A, D, DS, G 및 GS 시리즈 VM)

    > [!NOTE]
    > 모든 리소스(Key Vault, Storage 계정 및 VM 포함)는 동일한 Azure 지역 및 구독 내에 있어야 합니다.

다음 시나리오의 경우 디스크 암호화가 현재 지원되지 않습니다.

* 기본 계층 VM.
* 클래식 배포 모델을 사용하여 만든 VM
* 이미 암호화된 VM에서 암호화 키 업데이트
* 온-프레미스 키 관리 서비스와의 통합


## <a name="create-an-azure-key-vault-and-keys"></a>Azure Key Vault 및 키 만들기
시작하기 전에 최신 버전의 Azure PowerShell 모듈을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 다음 명령 예제에서 모든 예제 매개 변수를 사용자 고유의 이름, 위치 및 키 값으로 바꿉니다(예: *myResourceGroup*, *myKeyVault*, *myVM* 등).

첫 번째 단계는 암호화 키를 저장할 Azure Key Vault를 만드는 것입니다. Azure Key Vault는 애플리케이션 및 서비스에 안전하게 구현할 수 있는 키, 비밀 또는 암호를 저장할 수 있습니다. 가상 디스크 암호화의 경우 Key Vault를 만들어 가상 디스크 암호화 또는 암호 해독에 사용되는 암호화 키를 저장합니다. 

[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)를 사용하여 Azure 구독 내에서 Azure Key Vault 공급자를 사용하도록 설정하고 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 동부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

암호화 키를 보유하는 Azure Key Vault와 스토리지 및 VM과 같은 연결된 계산 리소스는 동일한 지역에 있어야 합니다. [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)를 사용하여 Azure Key Vault을 만들고 디스크 암호화에 사용할 Key Vault를 사용하도록 설정합니다. *keyVaultName*에 대한 고유한 Key Vault 이름을 다음과 같이 지정합니다.

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

소프트웨어 또는 HSM(하드웨어 보안 모델) 보호를 사용하여 암호화 키를 저장할 수 있습니다.  표준 Key Vault는 소프트웨어 보호 키만 저장합니다. HSM을 사용하려면 추가 비용으로 프리미엄 Key Vault가 필요합니다. 프리미엄 Key Vault를 만들려면 앞의 단계에서 *-Sku "Premium"* 매개 변수를 추가합니다. 표준 Key Vault를 만들었기 때문에 다음 예제는 소프트웨어 보호 키를 사용합니다. 

두 가지 보호 모델 모두, 가상 디스크의 암호를 해독하기 위해 VM이 부팅될 때 암호화 키를 요청하려면 Azure 플랫폼에 액세스 권한이 허용되어야 합니다. [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey)를 사용하여 Key Vault에 암호화 키를 만듭니다. 다음 예제는 *myKey*라는 키를 만듭니다.

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기
암호화 프로세스를 테스트하기 위해 [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만듭니다. 다음 예제에서는 *Windows Server 2016 Datacenter* 이미지를 사용하여 *myVM*이라는 VM을 만듭니다. 자격 증명을 묻는 메시지가 표시되면 VM에 사용할 사용자 이름과 암호를 입력합니다.

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>가상 머신 암호화
Azure Key Vault 키를 사용하여 [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension)으로 VM을 암호화합니다. 다음 예제에서는 모든 주요 정보를 검색한 후 *myVM*라는 VM을 암호화합니다.

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

확인 메시지를 수락하여 VM 암호화를 계속합니다. 이 프로세스 동안 VM이 다시 시작됩니다. 암호화 프로세스가 완료되고 VM이 재부팅되면 [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)로 암호화 상태를 검토합니다.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

다음 예제와 유사하게 출력됩니다.

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>다음 단계
* Azure Key Vault 관리 방법에 대한 자세한 내용은 [가상 머신에 대한 Key Vault 설정](key-vault-setup.md)을 참조하세요.
* Azure에 업로드할 암호화된 사용자 지정 VM 준비와 같은 디스크 암호화에 대한 자세한 내용은 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.
