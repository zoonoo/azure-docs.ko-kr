---
title: "Azure Backup을 사용하여 암호화된 VM의 Key Vault 키 및 암호 복원 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure Backup에서 Key Vault 키 및 암호를 복원하는 방법을 알아봅니다."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: pajosh
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: ddb9e7909eb4ab97204059d21690795ceb6ff9e8
ms.lasthandoff: 03/02/2017


---
# <a name="restore-an-encrypted-virtual-machine-from-an-azure-backup-recovery-point"></a>Azure Backup 복구 지점에서 암호화된 가상 컴퓨터 복원
이 문서에서는 Key Vault에 키와 암호가 존재하지 않는 경우 Azure VM Backup을 사용하여 암호화된 Azure VM의 복원을 수행하는 방법을 설명합니다. 복원된 VM에 대한 키(키 암호화 키)와 암호(BitLocker 암호화 키)의 별도의 복사본을 유지하려는 경우 이러한 단계도 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소
1. **암호화된 VM 백업** - 암호화된 Azure VM은 Azure Backup을 사용하여 백업됩니다. 암호화된 Azure VM을 백업하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Azure VM의 백업 및 복원 관리](backup-azure-vms-automation.md) 문서를 참조하세요.
2. **Azure Key Vault 구성** – 키 및 암호를 복원해야 하는 Key Vault가 이미 있는지 확인합니다. Key Vault 관리에 대한 자세한 내용은 [Azure Key Vault 시작](../key-vault/key-vault-get-started.md) 문서를 참조하세요.

## <a name="setup-recovery-services-vault"></a>복구 서비스 자격 증명 모음 설정
다음 단계를 사용하여 PowerShell에 로그인하고 복구 서비스 자격 증명 모음 컨텍스트를 설정합니다.

### <a name="log-in-to-azure-powershell"></a>Azure PowerShell에 로그인합니다.
다음 cmdlet을 사용하여 Azure 계정에 로그인

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>복구 서비스 자격 증명 모음 컨텍스트 설정
로그인한 후에는 다음 cmdlet을 사용하여 사용 가능한 구독 목록을 가져옵니다.

```
PS C:\> Get-AzureRmSubscription
```

리소스를 사용할 수 있는 구독 선택

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

암호화된 VM에 대해 백업이 활성화된 Recovery Services 자격 증명 모음을 사용하여 자격 증명 모음 컨텍스트 설정

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>복구 지점 가져오기
암호화된 Azure 가상 컴퓨터를 나타내는 자격 증명 모음에서 컨테이너 선택

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

이 컨테이너를 사용하여 해당 가상 컴퓨터에 대한 백업 항목 가져오기

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

변수 rp에서 선택한 백업 항목에 대한 복구 지점 배열 가져오기

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>암호화된 가상 컴퓨터 복원
다음 단계를 사용하여 암호화된 VM과 해당 키 및 암호를 복원합니다.

### <a name="restore-key"></a>키 복원
위의 $rp 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 예를 들어 $rp[0]은 최신 복구 지점을 선택합니다.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> 이 cmdlet이 성공적으로 실행되면 Blob 파일이 실행되는 컴퓨터의 지정된 폴더에 생성됩니다. 이 Blob 파일은 암호화된 형태의 키 암호화 키를 나타냅니다.
>
>

다음 cmdlet을 사용하여 키를 Key Vault로 다시 복원합니다.

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>암호 복원
위에서 얻은 복구 지점에서 암호 데이터 복원

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> vault.azure.net 앞의 텍스트는 원래 Key Vault 이름을 나타냅니다. secrets/ 뒤에 있는 텍스트는 암호 이름을 나타냅니다.
>
>

동일한 암호 이름을 사용하려는 경우 위에서 실행한 cmdlet의 출력에서 암호 이름과 값을 가져옵니다. 다른 경우, 아래 $secretname은 새로운 암호 이름을 사용하도록 업데이트됩니다.

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

VM도 복원해야 하는 경우 암호에 대한 태그를 설정합니다. DiskEncryptionKeyFileName 태그에 대한 값에는 사용할 암호 이름이 포함됩니다.

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> DiskEncryptionKeyFileName에 대한 값은 위에서 가져온 암호 이름과 같습니다. 키를 다시 복원한 후 [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet을 사용하여 DiskEncryptionKeyEncryptionKeyURL에 대한 값을 Key Vault에서 얻을 수 있습니다.    
>
>

Key Vault에 암호 다시 설정

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>가상 컴퓨터 복원
위의 PowerShell cmdlet을 사용하면 Azure VM Backup을 사용하여 암호화된 VM을 복원한 경우 키 및 암호를 Key Vault로 복원할 수 있습니다. 복원한 후에는 [PowerShell을 사용하여 Azure VM의 백업 및 복원 관리](backup-azure-vms-automation.md) 문서를 참조하여 암호화된 VM을 복원합니다.

