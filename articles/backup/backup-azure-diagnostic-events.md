---
title: Recovery Services 자격 증명 모음용 진단 설정 사용
description: 이 문서에서는 Azure Backup에 이전 및 새 진단 이벤트를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575418"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services 자격 증명 모음용 진단 설정 사용

Azure Backup은 분석, 경고 및 보고 목적으로 수집하고 사용할 수 있는 진단 이벤트를 전송합니다.

Azure Portal를 통해 자격 증명 모음으로 이동하고 **진단 설정** 을 선택하여 Recovery Services 자격 증명 모음용 진단 설정을 구성할 수 있습니다. **+ 진단 설정 추가** 를 선택하면 하나 이상의 진단 이벤트를 스토리지 계정, 이벤트 허브 또는 Log Analytics 작업 영역으로 보낼 수 있습니다.

![진단 설정 창](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup 사용자에게 제공되는 진단 이벤트

Azure Backup은 다음과 같은 진단 이벤트를 제공합니다. 각 이벤트는 특정 세트의 백업 관련 아티팩트에 대한 세부 데이터를 제공합니다.

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

[레거시 이벤트](#legacy-event) AzureBackupReport을 아직 사용하고 있는 경우 위의 이벤트로 전환하는 것이 좋습니다.

자세한 내용은 [Azure Backup 진단 이벤트에 대한 데이터 모델](./backup-azure-reports-data-model.md)을 참조하세요.

이러한 이벤트의 데이터는 스토리지 계정, Log Analytics 작업 영역 또는 이벤트 허브로 보낼 수 있습니다. 이 데이터를 Log Analytics 작업 영역으로 보내는 경우 **진단 설정** 화면에서 **리소스별** 토글을 선택합니다. 자세한 내용은 다음 섹션을 참조하세요.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Log Analytics에서 진단 설정 사용

이제 Azure Backup을 사용하여 자격 증명 모음 진단 데이터를 백업 전용 Log Analytics 테이블로 보낼 수 있습니다. 이러한 테이블을 [리소스별 테이블](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)이라고 합니다.

자격 증명 모음 진단 데이터를 Log Analytics로 보내려면

1. 자격 증명 모음으로 이동하고 **진단 설정** 을 선택합니다. **+ 진단 설정 추가** 를 선택합니다.
1. 진단 설정에 이름을 지정합니다.
1. **Log Analytics로 보내기** 확인란을 선택하고 Log Analytics 작업 영역을 선택합니다.
1. 토글에서 **리소스별** 을 선택하고 다음 6개 이벤트를 선택합니다. **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**, **AddonAzureBackupProtectedInstance**.
1. **저장** 을 선택합니다.

   ![리소스별 모드](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

데이터가 Log Analytics 작업 영역으로 흐르면 각 이벤트에 대한 전용 테이블이 작업 영역에 생성됩니다. 이러한 테이블을 직접 쿼리할 수 있습니다. 필요한 경우 이러한 테이블 간에 조인 또는 합집합을 수행할 수도 있습니다.

> [!IMPORTANT]
> 6개 이벤트, 즉 CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage, AddonAzureBackupProtectedInstance는 [백업 보고서](./configure-reports.md)의 리소스별 모드에서만 지원됩니다. Azure 진단 모드에서 이들 6개 이벤트의 데이터를 보내려고 하면 백업 보고서에 데이터가 표시되지 않습니다.

## <a name="legacy-event"></a>레거시 이벤트

기존에는 자격 증명 모음에 대한 모든 백업 관련 진단 데이터가 AzureBackupReport라는 단일 이벤트에 포함되었습니다. 여기에 설명된 6개 이벤트는 기본적으로 AzureBackupReport에 포함된 모든 데이터를 분해한 것입니다.

현재, 사용자가 이 이벤트에 대한 기존 사용자 지정 쿼리를 가지고 있는 경우 이전 버전과의 호환성을 위해 AzureBackupReport 이벤트를 계속 지원합니다. 예제는 사용자 지정 로그 경고 및 사용자 지정 시각화입니다. 가능한 한 빨리 [새 이벤트](#diagnostics-events-available-for-azure-backup-users)로 전환하는 것이 좋습니다. 새 이벤트:

* 로그 쿼리에서 데이터를 훨씬 쉽게 사용할 수 있습니다.
* 스키마 및 스키마 구조를 보다 효율적으로 검색할 수 있습니다.
* 수집 대기 시간과 쿼리 시간 모두에서 성능이 향상됩니다.

Azure 진단 모드의 레거시 이벤트는 결국 사용이 중단됩니다. 새 이벤트를 선택하면 나중에 복잡한 마이그레이션을 피할 수 있습니다. Log Analytics를 사용하는 [보고 솔루션](./configure-reports.md)도 레거시 이벤트의 데이터에 대한 지원을 중단할 것입니다.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 대한 새 진단 설정으로 이동하는 단계

1. 레거시 이벤트를 사용하여 Log Analytics 작업 영역에 데이터를 보내는 자격 증명 모음 및 해당 구독을 식별합니다. 각 작업 영역에서 다음 쿼리를 실행하여 이러한 자격 증명 모음 및 구독을 식별합니다.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    다음은 작업 영역 중 하나에서 실행되는 쿼리의 스크린샷입니다.

    ![작업 영역 쿼리](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Azure Backup에서 [기본 제공 Azure Policy 정의](./azure-policy-configure-diagnostics.md)를 사용하여 지정된 범위의 모든 자격 증명 모음에 대한 새 진단 설정을 추가합니다. 이 정책은 진단 설정이 없거나 레거시 진단 설정만 있는 자격 증명 모음에 새 진단 설정을 추가합니다. 이 정책은 한 번에 전체 구독 또는 리소스 그룹에 할당될 수 있습니다. 정책이 할당되는 각 구독에 대한 소유자 액세스 권한이 있어야 합니다.

새 테이블의 데이터를 사용하도록 사용자 지정 쿼리를 모두 마이그레이션할 때까지는 AzureBackupReport와 6개 새 이벤트에 대해 별도의 진단 설정을 선택할 수 있습니다. 다음 이미지는 두 개의 진단 설정이 있는 자격 증명 모음의 예를 보여 줍니다. **Setting1** 이라는 첫 번째 설정은 Azure 진단 모드에서 AzureBackupReport 이벤트의 데이터를 Log Analytics 작업 영역으로 전송합니다. **Setting2** 라는 두 번째 설정은 리소스별 모드에서 6개 새 Azure Backup 이벤트의 데이터를 Log Analytics 작업 영역으로 전송합니다.

![두 가지 설정](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport 이벤트는 Azure 진단 모드에서만 지원됩니다. 리소스별 모드에서 이 이벤트의 데이터를 보내려고 하면 Log Analytics 작업 영역에 데이터가 전달되지 않습니다.

> [!NOTE]
> **Azure 진단** 또는 **리소스별** 에 대한 토글은 사용자가 **Log Analytics로 보내기** 를 선택하는 경우에만 표시됩니다. 스토리지 계정 또는 이벤트 허브로 데이터를 보내려면 사용자는 필요한 대상을 선택하고 추가 입력 없이 원하는 이벤트의 확인란을 선택합니다. 다시 한번, 앞으로는 레거시 이벤트 AzureBackupReport는 선택하지 않는 것이 좋습니다.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure Site Recovery 이벤트를 Log Analytics로 보내기

Azure Backup 이벤트와 Azure Site Recovery 이벤트는 동일한 Recovery Services 자격 증명 모음에서 전송됩니다. 현재 Azure Site Recovery는 리소스별 테이블에 사용할 수 없습니다. Azure Site Recovery 이벤트를 Log Analytics로 보내려는 사용자는 이미지에 표시된 것처럼 Azure 진단 모드만 사용하도록 지정됩니다. Azure Site Recovery 이벤트에 리소스별 모드를 선택하면 필요한 데이터가 Log Analytics 작업 영역으로 전송되지 않습니다.

![Site Recovery 이벤트](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

요약:

* 이미 Azure Diagnostics를 사용하여 Log Analytics 진단을 설정했고 그 위에 사용자 지정 쿼리를 작성한 경우 새 이벤트의 데이터를 사용하도록 쿼리를 마이그레이션할 때까지 해당 설정을 그대로 유지합니다.
* 새 테이블도 온보딩하려면 **새** 진단 설정을 만들고, **리소스별** 을 선택하고, 6개 새 이벤트를 선택합니다.
* 현재 Azure Site Recovery 이벤트를 Log Analytics로 보내고 있는 경우 이러한 이벤트에 리소스별 모드를 선택하지 마세요. 이러한 이벤트의 데이터가 Log Analytics 작업 영역으로 흐르지 않습니다. 대신, 추가 진단 설정을 만들고, **Azure 진단** 을 선택하고, 관련 Azure Site Recovery 이벤트를 선택합니다.

다음 이미지는 자격 증명 모음에 대해 3개의 진단 설정이 있는 사용자의 예를 보여 줍니다. **Setting1** 이라는 첫 번째 설정은 Azure 진단 모드에서 AzureBackupReport 이벤트의 데이터를 Log Analytics 작업 영역으로 전송합니다. **Setting2** 라는 두 번째 설정은 리소스별 모드에서 6개 새 Azure Backup 이벤트의 데이터를 Log Analytics 작업 영역으로 전송합니다. **Setting3** 이라는 세 번째 설정은 Azure 진단 모드에서 Azure Site Recovery 이벤트의 데이터를 Log Analytics 작업 영역으로 전송합니다.

![세 가지 설정](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>다음 단계

[진단 이벤트에 대한 Log Analytics 데이터 모델에 대해 알아봅니다.](./backup-azure-reports-data-model.md)
