---
title: Azure Virtual Machine Scale Sets 암호화 디스크 | Microsoft Docs
description: 가상 머신 확장 집합에서 연결된 디스크를 암호화하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 OS 및 연결된 데이터 디스크 암호화
Azure [가상 머신 확장 집합](/azure/virtual-machine-scale-sets/)은 Azure Disk Encryption(ADE)을 지원합니다.  Azure Disk Encryption은 Windows 및 Linux 가상 머신 확장 집합에 대해 활성화할 수 있으며, 업계 표준 암호화 기술을 사용하여 확장 집합 미사용 데이터를 안전하게 보호합니다. 자세한 내용은 Windows 및 Linux Virtual Machines용 Azure Disk Encryption을 참조하세요.

> [!NOTE]
>  가상 머신 확장 집합용 Azure Disk Encryption은 현재 공개 미리 보기 단계에 있으며, 모든 Azure 공개 지역에서 이용할 수 있습니다.

Azure Disk Encryption이 지원되는 경우는 다음과 같습니다.
- 관리 디스크를 이용하여 생성된 확장 집합 지원(원시(또는 비관리) 디스크 확장 집합은 지원하지 않음).
- Windows 확장 집합의 OS 및 데이터 볼륨. Windows 확장 집합의 OS 및 데이터 볼륨에서는 암호화 비활성화도 지원됩니다.
- Linux 확장 집합의 데이터 볼륨. 현재 미리 보기에서는 Linux 확장 집합의 OS 디스크 암호화가 지원되지 않습니다.

현재 미리 보기에서는 확장 집합 VM의 이미지로 다시 설치 작업과 업그레이드 작업이 지원되지 않습니다. 가상 머신 확장 집합용 Azure Disk Encryption은 테스트 환경에서만 사용하는 것이 권장됩니다. 미리 보기에서는 암호화된 확장 집합에서 OS 이미지를 업그레이드해야 할 필요가 있는 프로덕션 환경에서 디스크 암호화를 사용하도록 설정하지 마세요.

Linux 확장 집합 데이터 디스크 암호화에 대한 종단 간 배치 파일 예제는 [여기](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)서 찾을 수 있습니다.  이 예제에서는 리소스 그룹, Linux 확장 집합을 만들고, 5GB 데이터 디스크를 탑재하고, 가상 머신 확장 집합을 암호화합니다.

## <a name="prerequisites"></a>필수 조건
암호화 명령을 포함하는 최신 버전의 [Azure Powershell](https://github.com/Azure/azure-powershell/releases) 또는 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)을 설치합니다.

가상 머신 확장 집합용 Azure Disk Encryption 미리 보기를 사용하려면 다음과 같은 PowerShell 명령을 사용하여 사용자가 구독을 직접 등록해야 합니다. 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

아래의 명령을 입력하고 ‘Registered’ 상태가 반환될 때까지 10분 정도 기다립니다. 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>디스크 암호화가 활성화된 Azure 키 자격 증명 모음 만들기
확장 집합과 동일한 구독 및 지역에 새 키 자격 증명 모음을 만들고 ‘EnabledForDiskEncryption’ 액세스 정책을 설정합니다.

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

또는 확장 집합과 동일한 구독 및 지역에 있는 기존의 키 자격 증명 모음에서 디스크 암호화를 사용하도록 설정합니다.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>암호화 사용

다음 명령은 동일한 리소스 그룹에 있는 키 자격 증명 모음을 사용하여 실행 중인 확장 집합 내의 데이터 디스크를 암호화합니다. 실행 중인 [Windows 확장 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) 또는 [Linux 확장 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)에서 템플릿을 사용하여 디스크를 암호화할 수도 있습니다.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>암호화 진행 확인

다음 명령을 사용하여 확장 집합의 암호화 상태를 확인합니다.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>암호화 사용 안 함
다음 명령을 사용하여 실행 중인 가상 머신 확장 집합에서 암호화를 사용하지 않도록 설정합니다. 또한 실행 중인 [Windows VM 확장 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) 또는 [Linux VM 확장 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)에서 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수도 있습니다.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

