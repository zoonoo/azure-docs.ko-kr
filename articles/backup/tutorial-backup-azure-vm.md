---
title: PowerShell을 사용하여 여러 Azure VM 백업
description: 이 자습서에서는 Azure PowerShell을 사용하여 여러 Azure VM을 Recovery Services 자격 증명 모음에 백업하는 방법을 자세히 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2a35435f56a4bb09a8a1958fbc175ef7c889c380
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863018"
---
# <a name="back-up-azure-vms-with-powershell"></a>PowerShell을 사용하여 Azure VM 백업

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서에서는 PowerShell을 통해 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 배포하여 여러 Azure VM을 백업하는 방법에 대해 설명합니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Recovery Services 자격 증명 모음을 만들고 자격 증명 모음 컨텍스트를 설정합니다.
> * 백업 정책 정의
> * 여러 가상 머신을 보호하기 위해 백업 정책 적용
> * 보호된 가상 머신에 대한 주문형 백업 작업 트리거. 가상 머신을 백업하거나 보호하려면 먼저 [필수 조건](backup-azure-arm-vms-prepare.md)을 완료하여 VM을 보호하기 위한 환경을 준비해야 합니다. 

> [!IMPORTANT]
> 이 자습서에서는 리소스 그룹 및 Azure 가상 머신을 이미 만들었다고 가정합니다.


## <a name="log-in-and-register"></a>로그인 및 등록


1. `Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

    ```powershell
    Connect-AzAccount
    ```
2. Azure Backup을 처음 사용하는 경우 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)를 사용하여 구독에서 Azure Recovery Service 공급자를 등록해야 합니다. 이미 등록한 경우 이 단계를 건너뛰세요.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

[Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)은 Azure VM과 같은 보호된 리소스에 대한 백업 데이터를 저장하는 논리 컨테이너입니다. 백업 작업이 실행되면 Recovery Services 자격 증명 모음 내에 복구 지점이 만들어집니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.


- 이 자습서에서는 백업하려는 VM과 동일한 리소스 그룹 및 위치에 자격 증명 모음을 만듭니다.
- Azure Backup은 백업된 데이터에 대한 스토리지를 자동으로 처리합니다. 기본적으로 자격 증명 모음은 [GRS(지역 중복 스토리지)](../storage/common/storage-redundancy-grs.md)를 사용합니다. 지역 중복은 주 지역에서 수백 마일 떨어져 있는 보조 Azure 지역에 백업된 데이터가 복제되도록 합니다.

자격 증명 모음을 만들려면 다음을 수행합니다.

1. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)를 사용하여 자격 증명 모음을 만듭니다. 백업하려는 VM의 리소스 그룹 이름과 위치를 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. 많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이런 이유 때문에, 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)를 사용하여 자격 증명 모음 컨텍스트를 설정합니다.

   - 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다.
   - 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다.

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Azure VM 백업

백업은 백업 정책에 지정된 일정에 따라 실행됩니다. Recovery Services 자격 증명 모음을 만들면 기본 보호 및 보존 정책이 함께 제공됩니다.

- 기본 보호 정책은 백업 작업을 매일 지정된 시간에 한 번 트리거합니다.
- 기본 보존 정책은 매일 복구 지점을 30일 동안 유지합니다. 

이 자습서에서 Azure VM을 사용하도록 설정하고 백업하려면 다음을 수행합니다.

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)를 사용하여 백업 데이터를 보관하는 컨테이너를 자격 증명 모음에 지정합니다.
2. 백업을 위한 각 VM은 하나의 항목입니다. 백업 작업을 시작하려면 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)을 사용하여 VM에 대한 정보를 가져옵니다.
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)을 사용하여 임시 백업을 실행합니다. 
    - 첫 번째 초기 백업 작업에서는 전체 복구 지점이 만들어집니다.
    - 초기 백업 후에는 각 백업 작업에서 증분 복구 지점이 만들어집니다.
    - 증분 복구 지점은 마지막 백업 이후 변경된 내용만을 전송하기 때문에 저장소 및 시간 효율적입니다.

백업을 사용하도록 설정하고 실행하려면 다음을 수행합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>문제 해결 

가상 머신을 백업하는 동안 문제가 발생하면 이 [문제 해결 문서](backup-azure-vms-troubleshoot.md)를 검토하세요.

### <a name="deleting-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

자격 증명 모음을 삭제해야 하는 경우 다음과 같이 먼저 자격 증명 모음의 복구 지점을 삭제한 다음, 자격 증명 모음의 등록을 취소합니다.


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>다음 단계

- PowerShell을 사용하여 Azure VM을 백업 및 복원하는 방법에 대한 자세한 연습을 [검토합니다](backup-azure-vms-automation.md). 
- [Azure VM 관리 및 모니터링](backup-azure-manage-vms.md)
- [Azure VM 복원](backup-azure-arm-restore-vms.md)
