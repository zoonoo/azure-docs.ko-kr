---
title: 보관 계층 지원 (미리 보기)
description: Azure Backup에 대 한 보관 계층 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 6c597d640f24dc4c680bfd5db16f9df09017ee54
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609855"
---
# <a name="archive-tier-support-preview"></a>보관 계층 지원 (미리 보기)

고객은 조직의 규정 준수 규칙에 의해 정의 되는 보존 요구 사항과 함께 LTR (Long-Term 보존) 백업 데이터를 포함 하 여 백업 데이터를 저장 하는 Azure Backup을 사용 합니다. 대부분의 경우 이전 백업 데이터는 거의 액세스 되지 않으며 규정 준수 요구 사항에 대해서만 저장 됩니다.

Azure Backup는 스냅숏 및 표준 계층 외에도 보관 계층에서 장기 보존 지점의 백업을 지원 합니다.

## <a name="scope-for-preview"></a>미리 보기 범위

지원 되는 워크 로드:

- Azure 가상 머신
  - 월별 및 연간 복구 지점만 매일 및 매주 복구 지점은 지원 되지 않습니다.
  - Age >= Vault-Standard 계층의 3 개월
  - 남아 있는 보존 >= 6 개월
  - 일별 및 주별 종속성 없음
- Azure 가상 컴퓨터의 SQL Server
  - 전체 복구 지점만 로그 및 차등은 지원 되지 않습니다.
  - Vault-Standard 계층의 Age >= 45 일
  - 남아 있는 보존 >= 6 개월
  - 종속성 없음

지원되는 클라이언트:

- PowerShell을 사용 하 여 기능이 제공 됩니다.

>[!NOTE]
>Azure vm의 Azure Vm 및 SQL Server에 대 한 보관 계층 지원은 제한 된 signups를 사용 하는 제한 된 공개 미리 보기 상태입니다. 보관 지원에 등록 하려면이 [링크](https://aka.ms/ArchivePreviewInterestForm)를 사용 합니다.

## <a name="get-started-with-powershell"></a>PowerShell 시작

1. PowerShell에서 다음 명령을 실행합니다.
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 Azure에 연결 합니다.
1. 구독에 로그인 합니다.

   `Set-AzContext -Subscription "SubscriptionName"`

1. 자격 증명 모음을 가져옵니다.

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. 백업 항목의 목록을 가져옵니다.

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. 백업 항목을 가져옵니다.

    - Azure virtual machines의 경우:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Azure virtual machines의 SQL Server:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>PowerShell 사용

### <a name="check-archivable-recovery-points"></a>보관할 수 복구 지점의 확인

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

그러면 보관으로 이동할 준비가 된 특정 백업 항목과 연결 된 모든 복구 지점이 나열 됩니다.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>복구 지점을 보관으로 이동할 수 없는 이유 확인

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

여기서 `$rp[0]` 은 보관할 수 되지 않는 이유를 확인 하려는 복구 지점입니다.

샘플 출력:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>권장 보관할 수 지점의 집합 (Azure Vm에만 해당)을 선택 합니다.

가상 머신과 연결 된 복구 지점은 기본적으로 증분입니다. 특정 복구 지점이 보관으로 이동 되 면 전체 백업으로 변환 된 다음 보관으로 이동 됩니다. 따라서 archive로 이동과 관련 된 비용 절감은 데이터 원본의 변동에 따라 다릅니다.

따라서 Azure Backup으로 함께 이동 하면 비용을 절감할 수 있는 권장 복구 지점의 집합이 제공 됩니다.

>[!NOTE]
>비용 절감은 다양 한 원인에 따라 다르며, 두 인스턴스에 대해 동일 하지 않을 수 있습니다.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>보관으로 이동

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

이 명령은 보관할 수 복구 지점을 archive로 이동 합니다. 포털 및 PowerShell에서 이동 작업을 추적 하는 데 사용할 수 있는 작업을 반환 합니다.

### <a name="view-archived-recovery-points"></a>보관 된 복구 지점의 보기

이 명령은 보관 된 복구 지점은 모두 반환 합니다.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>PowerShell을 사용 하 여 복원

보관의 복구 지점의 경우 Azure Backup는 통합 복원 방법론을 제공 합니다.

통합 복원은 2 단계 프로세스입니다. 첫 번째 단계는 보관에 저장 된 복구 지점의 리하이드레이션는 10 ~ 30 일 사이의 기간 (리하이드레이션 기간이 라고도 함)에 대 한 자격 증명 모음 표준 계층에 임시로 저장 하는 것입니다. 기본값은 15 일입니다. 리하이드레이션의 두 가지 우선 순위 (표준 및 높은 우선 순위)가 있습니다. [리하이드레이션 우선 순위](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration#rehydrate-an-archived-blob-to-an-online-tier)에 대해 자세히 알아보세요.

>[!NOTE]
>
>- 리하이드레이션 기간을 선택한 후에는 변경할 수 없으며, 해당 복구 지점은 리하이드레이션 기간 동안 표준 계층에 유지 됩니다.
>- 리하이드레이션의 추가 단계는 비용이 발생 합니다.

Azure virtual machines의 다양 한 복원 방법에 대 한 자세한 내용은 [PowerShell을 사용 하 여 AZURE VM 복원](backup-azure-vms-automation.md#restore-an-azure-vm)을 참조 하세요.

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

SQL Server 복원 하려면 [다음 단계](backup-azure-sql-automation.md#restore-sql-dbs)를 수행 합니다. 필요한 추가 매개 변수는 **RehydrationPriority** 및 **RehydrationDuration** 입니다.

### <a name="view-jobs-from-powershell"></a>PowerShell에서 작업 보기

이동 및 복원 작업을 보려면 다음 PowerShell cmdlet을 사용 합니다.

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>포털 사용

### <a name="check-archived-recovery-point"></a>보관 된 복구 지점 확인

이제 보관으로 이동 된 모든 복구 지점은 볼 수 있습니다.

![모든 복구 지점이 있습니다.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>포털에서 복원

보관으로 이동 된 복구 지점의 경우 restore에서 리하이드레이션 duration 및 리하이드레이션 priority에 대 한 매개 변수를 추가 해야 합니다.

![포털에서 복원.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>포털에서 작업 보기

![포털에서 작업을 봅니다.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>보호 수정

데이터 원본에 대 한 보호를 수정할 수 있는 방법에는 다음 두 가지가 있습니다.

- 기존 정책 수정
- 새 정책을 사용 하 여 데이터 원본 보호

두 경우 모두 새로운 정책은 표준 계층에 있는 모든 이전 복구 지점과 보관 계층의 모든 복구 시점에 적용 됩니다. 따라서 정책이 변경 된 경우 오래 된 복구 지점이 삭제 될 수 있습니다.

복구 지점이 보관으로 이동 되 면 초기 삭제 기간인 180 일이 발생 합니다. 요금은 비례 배분 됩니다. 180 일 동안 보관에 높게 유지 되지 않은 복구 지점을 삭제 하는 경우 180에 해당 하는 비용이 표준 계층에서 사용한 일 수를 뺀 값과 동일 하 게 부과 됩니다.

최소 6 개월 동안 보관에 높게 유지 되지 않은 복구 지점은 삭제 시 초기 삭제 비용이 발생 합니다.

## <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

보호를 중지 하 고 데이터를 삭제 하면 모든 복구 지점이 삭제 됩니다. 보관 계층에서 180 일 동안 높게 유지 하지 않은 보관의 복구 지점의 경우 복구 지점의 삭제는 초기 삭제 비용을 초래 합니다.

## <a name="error-codes-and-troubleshooting-steps"></a>오류 코드 및 문제 해결 단계

복구 지점을 보관으로 이동할 수 없는 경우 발생 하는 몇 가지 오류 코드를 제공 합니다.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**오류 메시지** -Recovery-Point 유형은 보관 이동에 적합 하지 않습니다.

**설명** -이 오류 코드는 선택한 복구 지점 유형을 보관으로 이동할 수 없는 경우에 표시 됩니다.

**권장 조치** – [여기](#scope-for-preview) 에서 복구 지점의 자격을 확인 합니다.

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**오류 메시지** -복원에 대 한 활성 종속성이 있는 Recovery-Point 보관 이동에 적합 하지 않습니다.

**설명-** 선택한 복구 지점에 활성 종속성이 있으므로 보관으로 이동할 수 없습니다.

**권장 조치** – [여기](#scope-for-preview) 에서 복구 지점의 자격을 확인 합니다.

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**오류 메시지** -Recovery-Point 자격 증명 모음에 소요 된 수명으로 보관 이동에 적합 하지 않습니다. 표준 계층은 필요한 최소 수준 보다 작습니다.

**설명** – 복구 지점은 azure virtual machines에 대해 최소 3 개월 동안 표준 계층에 유지 해야 하 고, azure virtual machines에서 SQL Server는 45 일을 유지 해야 합니다.

**권장 조치** – [여기](#scope-for-preview) 에서 복구 지점의 자격을 확인 합니다.

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**오류 메시지** -Recovery-Point 남은 수명이 필요한 최소값 보다 작습니다.

**설명** – 보관 이동 자격에 대 한 복구 지점에 필요한 최소 수명은 6 개월입니다.

**권장 조치** – [여기](#scope-for-preview) 에서 복구 지점의 자격을 확인 합니다.

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**오류 메시지** -보관 계층으로 이미 이동 되었으므로 보관 이동에 대 한 Recovery-Point 없습니다.

**설명** – 선택한 복구 지점이 이미 보관에 있습니다. 따라서 보관으로 이동할 수 없습니다.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**오류 메시지** -데이터 원본 유형은 권장 사항 API에 적합 하지 않습니다.

**설명** – 권장 사항 API는 Azure virtual machines에만 적용 됩니다. 선택한 데이터 원본 유형에는 적용 되지 않습니다.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**오류 메시지** -복구 지점에 이미가 중입니다. 이 RP에서는 리하이드레이션을 사용할 수 없습니다.

**설명** – 선택한 복구 지점이 이미로 지정 되었습니다.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**오류 메시지** -복구 지점은 보관 이동에 적합 하지 않습니다.

**설명** – 선택한 복구 지점은 보관 이동에 적합 하지 않습니다.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**오류** **메시지** -보관 복구 지점의 위치가 잘못 되었습니다. 보관 RP에서 리하이드레이션 완료 된 후 복원을 다시 시도 합니다.

**설명** – 복구 지점이 함께 되지 않습니다. 복구 지점을 리하이드레이션 후에 복원을 시도 하세요.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**오류** **메시지**-리하이드레이션은 보관 복구 지점만 지원 됩니다.-리하이드레이션는 보관 복구 지점만 지원 됩니다.

**설명** – 선택한 복구 지점에 대해 리하이드레이션이 허용 되지 않습니다.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**오류 메시지** – 리하이드레이션가 이미 보관 복구 지점에 대 한 In-Progress 되었습니다.

**설명** – 선택한 복구 지점에 대 한 리하이드레이션 이미 진행 중입니다.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**오류 메시지** -정책에 지정 된 보존 기간 부족으로 인해 복구 지점을 보관 계층으로 이동할 수 없습니다.

**권장 작업** -적절 한 보존 설정을 사용 하 여 보호 된 항목에서 정책을 업데이트 하 고 다시 시도 하세요.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**오류 메시지** -이 복구 지점을 이동할 수 있는지 여부를 계속 확인 하 고 있습니다.

**설명** – 복구 지점의 이동 준비를 아직 확인 하지 않았습니다.

**권장 작업** -일정 시간 동안 기다린 후에 다시 확인 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>보호를 중지 하 고 데이터를 보존 하는 경우 복구 지점이 보관 되는 경우는 어떻게 되나요?

복구 지점은 영구적으로 보관 된 상태로 유지 됩니다. 자세한 내용은 [복구 지점의 보호 중지의 영향](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 가격 책정](azure-backup-pricing.md)
