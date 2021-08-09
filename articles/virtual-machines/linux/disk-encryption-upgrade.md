---
title: 디스크에서 Azure Disk Encryption를 업그레이드하는 방법
description: 이 문서에서는 디스크의 Azure Disk Encryption을 업그레이드하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 05/27/2021
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0cdc32e303de7d9a26a99d1f08188dc54539930b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758242"
---
# <a name="upgrading-the-azure-disk-encryption-version"></a>Azure Disk Encryption 버전 업그레이드

ADE(Azure Disk Encryption)의 첫 번째 버전은 인증을 위해 AAD(Azure Active Directory)에 의존했습니다. 현재 버전은 그렇지 않습니다.  최신 버전을 사용하는 것이 좋습니다.

## <a name="determine-ade-version"></a>ADE 버전 확인

마이그레이션 범위의 ADE 버전은 다음과 같습니다.
- **Windows**: 1.1.*(VM의 ADE를 2.2로 업그레이드해야 함)
- **Linux**: 0.1.*(VM의 ADE를 1.2로 업그레이드해야 함)

Azure CLI, Azure PowerShell 또는 Azure Portal을 통해 VM이 암호화된 ADE 버전을 확인할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/CLI)

ADE 버전을 확인하려면 Azure CLI [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) 명령을 실행합니다.

```azurecli-interactive
az vm get-instance-view --resource-group  <ResourceGroupName> --name <VMName> 
```

출력에서 AzureDiskEncryption 확장을 찾아 출력의 "TypeHandlerVersion" 필드에서 버전 번호를 확인합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

ADE 버전을 확인하려면 Azure PowerShell [Get-AzVM](/powershell/module/az.compute/get-azvm) 명령을 실행합니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName <ResourceGroupName> -Name <VMName> -Status
```

출력에서 AzureDiskEncryption 확장을 찾아 출력의 "TypeHandlerVersion" 필드에서 버전 번호를 확인합니다.

# <a name="portal"></a>[포털](#tab/Portal)

Azure Portal VM의 "확장" 블레이드로 이동합니다.

:::image type="content" source="../media/disk-encryption/ade-version-1.png" alt-text="ADE 버전 포털 찾기 스크린샷 1":::

Windows용 "AzureDiskEncryption" 확장 또는 Linux용 "AzureDiskEncryptionForLinux" 확장을 선택하고 "버전"" 필드에서 버전 번호를 찾습니다.

:::image type="content" source="../media/disk-encryption/ade-version-2.png" alt-text="ADE 버전 포털 찾기 스크린샷 2":::

---

## <a name="how-to-migrate"></a>마이그레이션하는 방법

AAD(Azure Disk Encryption)에서 Azure Disk Encryption(AAD 제외)으로의 마이그레이션은 Azure PowerShell을 통해서만 사용할 수 있습니다. 최신 버전의 Azure PowerShell 및 [Azure PowerShell Az module version 5.9.0](/powershell/azure/new-azureps-module-az)가 설치되어 있는지 확인합니다.

Azure Disk Encryption(AAD 포함)에서 Azure Disk Encryption(AAD 제외)로 업그레이드하려면 [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) PowerShell cmdlet을 사용합니다. 

> [!WARNING]
> Set-AzVMDiskEncryptionExtension cmdlet은 Azure Disk Encryption(AAD 포함)으로 암호화된 VM에서만 사용해야 합니다. 암호화되지 않은 VM 또는 Azure Disk Encryption(AAD 제외)으로 암호화된 VM을 마이그레이션하면 터미널 오류가 발생합니다.

```azurepowershell-interactive
Set-AzVMDiskEncryptionExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Migrate
```

cmdlet에서 확인을 묻는 메시지가 표시되면 "Y"를 입력합니다.  ADE 버전이 업데이트되고 VM이 다시 부팅됩니다. 출력은 다음과 비슷합니다.

```bash
> Set-AzVMDiskEncryptionExtension -ResourceGroupName myResourceGroup -VMName myVM -Migrate

Update AzureDiskEncryption version?
This cmdlet updates Azure Disk Encryption version to single pass (Azure Disk Encryption without AAD). This may reboot
the machine and takes 10-15 minutes to finish. Are you sure you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
Azure Disk Encryption Extension Public Settings
"KeyVaultResourceId": /subscriptions/ea500758-3163-4849-bd2c-3e50f06efa7a/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault
"SequenceVersion":
"MigrateFlag": Migrate
"KeyVaultURL": https://myKeyVault.vault.azure.net/
"AADClientID": d29edf8c-3fcb-42e7-8410-9e39fdf0dd70
"KeyEncryptionKeyURL":
"KekVaultResourceId":
"EncryptionOperation": EnableEncryption
"AADClientCertThumbprint":
"VolumeType":
"KeyEncryptionAlgorithm":

Running ADE extension (with AAD) for -Migrate..
ADE extension (with AAD) is now complete. Updating VM model..
Running ADE extension (without AAD) for -Migrate..
ADE extension (without AAD) is now complete. Clearing VM model..

RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


> [!IMPORTANT]
> 업그레이드를 완료하는 데 10-15분 이상 소요됩니다. 업그레이드가 진행되는 동안에는 cmdlet을 취소하지 마세요. 이렇게 하면 VM의 상태가 위험에 노출됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
