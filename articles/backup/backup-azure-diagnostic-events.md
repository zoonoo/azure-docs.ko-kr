---
title: 복구 서비스 자격 증명 모음에 진단 설정 사용
description: 이 문서에서는 Azure Backup에 대한 이전 및 새 진단 이벤트를 사용하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617300"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>복구 서비스 자격 증명 모음에 진단 설정 사용

Azure Backup은 분석, 경고 및 보고를 위해 수집및 사용할 수 있는 진단 이벤트를 보냅니다.

자격 증명 모음으로 이동 하여 진단 설정을 선택 하 여 Azure 포털을 통해 복구 서비스 자격 증명에 대 한 진단 **설정을 구성할**수 있습니다. **+ 진단 추가 설정을** 선택하면 하나 이상의 진단 이벤트를 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역으로 보낼 수 있습니다.

![진단 설정 창](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 백업 사용자가 사용할 수 있는 진단 이벤트

Azure Backup은 다음 진단 이벤트를 제공합니다. 각 이벤트는 특정 백업 관련 아티팩트 집합에 대한 자세한 데이터를 제공합니다.

* 코어Azure백업
* 애드온Azure백업경고
* 애드온Azure백업보호인스턴스
* 애드온Azure백업작업
* 애드온Azure백업정책
* 애드온Azure백업스토리지

자세한 내용은 [Azure Backup 진단 이벤트에 대한 데이터 모델을](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)참조하십시오.

이러한 이벤트에 대한 데이터는 저장소 계정, Log Analytics 작업 영역 또는 이벤트 허브로 전송할 수 있습니다. 이 데이터를 Log Analytics 작업 영역으로 보내는 경우 진단 설정 화면에서 **리소스별** 토글을 **선택합니다.** 자세한 내용은 다음 섹션을 참조하세요.

## <a name="use-diagnostics-settings-with-log-analytics"></a>로그 분석과 함께 진단 설정 사용

이제 Azure Backup을 사용하여 백업을 위한 전용 로그 분석 테이블로 볼트 진단 데이터를 보낼 수 있습니다. 이러한 테이블을 [리소스별 테이블이라고](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)합니다.

볼트 진단 데이터를 로그 애널리틱스로 보내려면 다음 을 수행하십시오.

1. 볼트로 이동하여 **진단 설정을**선택합니다. **+진단 설정 추가를**선택합니다.
1. 진단 설정에 이름을 지정합니다.
1. **로그 분석으로 보내기** 확인란을 선택하고 로그 분석 작업 영역을 선택합니다.
1. 토글에서 **특정 리소스를** 선택하고 다음 여섯 가지 이벤트를 선택합니다: **CoreAzureBackup,** **AddonAzureBackupJobs,** **AddonAzureBackupAlerts,** **AddonAzureBackupPolicy,** **AddonAzureBackupStorage,** 및 **AddonAzureBackupProtectedInstance**.
1. **저장**을 선택합니다.

   ![리소스별 모드](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

데이터가 Log Analytics 작업 영역으로 유입되면 이러한 각 이벤트에 대한 전용 테이블이 작업 영역에 만들어집니다. 이러한 테이블을 직접 쿼리할 수 있습니다. 필요한 경우 이러한 테이블 간에 조인 또는 공용 구조체를 수행할 수도 있습니다.

> [!IMPORTANT]
> 여섯 이벤트, 즉, 코어 AzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupStorage, 및 AddonAzureBackupProtectedInstance, [백업 보고서의](https://docs.microsoft.com/azure/backup/configure-reports)리소스 별 *모드에서만* 지원됩니다. *Azure 진단 모드에서 이러한 6개 이벤트에 대한 데이터를 보내려고 하면 백업 보고서에 데이터가 표시되지 않습니다.*

## <a name="legacy-event"></a>레거시 이벤트

일반적으로 볼트에 대한 모든 백업 관련 진단 데이터는 AzureBackupReport라는 단일 이벤트에 포함되었습니다. 여기에 설명된 6개의 이벤트는 본질적으로 AzureBackupReport에 포함된 모든 데이터의 분해입니다. 

현재 사용자가 이 이벤트에 대한 기존 사용자 지정 쿼리가 있는 경우 이전 버전과의 호환성을 위해 AzureBackupReport 이벤트를 계속 지원합니다. 예를 들어 사용자 지정 로그 경고 및 사용자 지정 시각화입니다. *가능한 한 빨리 [새 이벤트로](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) 이동하는 것이 좋습니다.* 새 이벤트:

- 로그 쿼리에서 데이터를 훨씬 쉽게 작업할 수 있습니다.
- 스키마와 그 구조를 더 잘 검색할 수 있습니다.
- 인스레이션 대기 시간 및 쿼리 시간 모두에서 성능을 향상시킵니다. 

*Azure 진단 모드의 레거시 이벤트는 결국 더 이상 사용되지 않습니다. 새 이벤트를 선택하면 나중에 복잡한 마이그레이션을 방지하는 데 도움이 될 수 있습니다.* Log Analytics를 사용하는 [보고 솔루션도](https://docs.microsoft.com/azure/backup/configure-reports) 레거시 이벤트의 데이터 지원을 중지합니다.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>로그 분석 작업 영역에 대한 새 진단 설정으로 이동하는 단계

1. 레거시 이벤트와 레거시 이벤트가 속한 구독을 사용하여 Log Analytics 작업 영역으로 데이터를 보내는 볼트를 식별합니다. 다음 작업 영역을 실행하여 이러한 자격 증명 모음 및 구독을 식별합니다.

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
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
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

1. Azure Backup에서 [기본 제공 Azure 정책을](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) 사용하여 지정된 범위의 모든 볼트에 대한 새 진단 설정을 추가합니다. 이 정책은 진단 설정이 없거나 레거시 진단 설정만 있는 볼트에 새 진단 설정을 추가합니다. 이 정책은 한 번에 전체 구독 또는 리소스 그룹에 할당할 수 있습니다. 정책이 할당된 각 구독에 대한 소유자 액세스 권한이 있어야 합니다.

새 테이블의 데이터를 사용하기 위해 모든 사용자 지정 쿼리를 마이그레이션할 때까지 AzureBackupReport 및 6개의 새 이벤트에 대한 별도의 진단 설정을 선택할 수 있습니다. 다음 이미지는 두 개의 진단 설정이 있는 볼트의 예를 보여 주십니다. **세팅1이라는**이름의 첫 번째 설정은 AzureBackupReport 이벤트의 데이터를 Azure 진단 모드에서 로그 분석 작업 영역으로 보냅니다. **세팅2라는**두 번째 설정은 6개의 새 Azure Backup 이벤트의 데이터를 리소스별 모드의 Log Analytics 작업 영역으로 보냅니다.

![두 가지 설정](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Azure BackupReport 이벤트는 Azure 진단 *모드에서만* 지원됩니다. *리소스별 모드에서 이 이벤트에 대한 데이터를 보내려고 하면 데이터가 Log Analytics 작업 영역으로 흐르지 않습니다.*

> [!NOTE]
> **Azure 진단** 또는 **리소스 별에** 대한 토글은 사용자가 **로그 분석으로 보내기**를 선택한 경우에만 나타납니다. 저장소 계정 또는 이벤트 허브로 데이터를 전송하기 위해 사용자는 필요한 대상을 선택하고 추가 입력 없이 원하는 이벤트에 대한 확인란을 선택합니다. 다시 말하지만 앞으로 의 레거시 이벤트 AzureBackupReport를 선택하지 않는 것이 좋습니다.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure 사이트 복구 이벤트를 로그 분석으로 보내기

Azure 백업 및 Azure 사이트 복구 이벤트는 동일한 복구 서비스 자격 증명 모음에서 전송됩니다. 현재 리소스별 테이블에는 Azure 사이트 복구를 사용할 수 없습니다. Azure 사이트 복구 이벤트를 Log Analytics로 보내려는 사용자는 이미지에 표시된 대로 Azure 진단 *모드만*사용하도록 이동합니다. *Azure 사이트 복구 이벤트에 대한 리소스별 모드를 선택하면 필요한 데이터가 Log Analytics 작업 영역으로 전송되지 않습니다.*

![사이트 복구 이벤트](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

요약하면

* Azure 진단으로 이미 Log Analytics 진단을 설정하고 그 위에 사용자 지정 쿼리를 작성한 경우 새 이벤트의 데이터를 사용하도록 쿼리를 마이그레이션할 때까지 해당 설정을 *그대로* 유지합니다.
* 권장대로 새 테이블에 온보온하려는 경우 **새** 진단 설정을 만들고 **리소스별**을 선택하고 6개의 새 이벤트를 선택합니다.
* 현재 Azure 사이트 복구 이벤트를 Log Analytics로 보내는 경우 이러한 이벤트에 대한 리소스별 모드를 *선택하지 마십시오.* 그렇지 않으면 이러한 이벤트에 대한 데이터가 Log Analytics 작업 영역으로 흐르지 않습니다. 대신 추가 진단 설정을 만들고 Azure 진단을 선택하고 관련 Azure 사이트 복구 이벤트를 **선택합니다.**

다음 이미지는 볼트에 대한 세 가지 진단 설정이 있는 사용자의 예를 보여 주며 있습니다. **세팅1이라는**이름의 첫 번째 설정은 AzureBackupReport 이벤트에서 Azure 진단 모드의 로그 분석 작업 영역으로 데이터를 보냅니다. **세팅2라는**두 번째 설정은 6개의 새 Azure Backup 이벤트의 데이터를 리소스별 모드의 Log Analytics 작업 영역으로 보냅니다. 세 번째 설정인 **Setting3은**Azure 사이트 복구 이벤트의 데이터를 Azure 진단 모드의 로그 분석 작업 영역으로 보냅니다.

![세 가지 설정](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>다음 단계

[진단 이벤트에 대한 로그 분석 데이터 모델 알아보기](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
