---
title: Azure Stack 백업 | Microsoft Docs
description: 백업을 준비를 사용 하 여 Azure Stack에는 주문형 백업을 수행 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139374"
---
# <a name="back-up-azure-stack"></a>Azure Stack 백업

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

백업을 준비를 사용 하 여 Azure Stack에는 주문형 백업을 수행 합니다. PowerShell 환경 구성에 대 한 지침을 참조 하세요 [Azure Stack 용 PowerShell 설치 ](azure-stack-powershell-install.md)합니다. Azure Stack에 로그인 하려면 참조 [관리자 포털을 사용 하 여 Azure Stack에서](azure-stack-manage-portals.md)합니다.

## <a name="start-azure-stack-backup"></a>Azure Stack 백업 시작

### <a name="start-a-new-backup-without-job-progress-tracking"></a>새 백업 작업 진행 상황을 추적 하지 않고 시작
작업 진행 상황 관리를 사용 하 여 새 백업을 즉시 시작 하려면 시작 AzSBackup를 사용 합니다.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>작업 진행 상태 추적을 사용 하 여 Azure Stack 백업 시작
백업 작업 진행 상황을 추적할-AsJob 변수를 사용 하 여 새 백업을 시작 하려면 시작 AzSBackup를 사용 합니다.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>백업 완료 되었는지 확인

### <a name="confirm-backup-has-completed-using-powershell"></a>PowerShell을 사용 하 여 백업 완료 되었는지 확인
다음 PowerShell 명령을 사용 하 여 해당 백업을 성공적으로 완료 되었는지 확인:

```powershell
   Get-AzsBackup
```

다음 출력과 유사한 결과가 표시 됩니다.

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>관리 포털에서 백업 완료 되었는지 확인
Azure Stack 관리 포털을 사용 하 여 이러한 단계를 수행 하 여 해당 백업을 성공적으로 완료 되었는지 확인 하려면:

1. 엽니다는 [Azure Stack 관리 포털](azure-stack-manage-portals.md)합니다.
2. 선택 **더 많은 서비스** > **인프라 백업**합니다. 선택 **Configuration** 에 **인프라 백업** 블레이드입니다.
3. 찾을 합니다 **이름** 하 고 **완료 날짜** 백업 **사용 가능한 백업** 목록.
4. 확인 합니다 **상태** 됩니다 **Succeeded**합니다.

## <a name="next-steps"></a>다음 단계

데이터 손실 이벤트에서 복구에 대 한 워크플로에 대 한 자세한 정보에 대해 알아봅니다. 참조 [치명적인 데이터 손실 로부터 복구](azure-stack-backup-recover-data.md)합니다.
