---
title: Azure Monitor를 사용 하 여 Azure Backup 모니터링
description: Azure Backup 작업을 모니터링 하 고 Azure Monitor를 사용 하 여 사용자 지정 경고를 만듭니다.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 65bab1a6d6d424c90b38a3bdf99b6bf5bd8ded09
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172902"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Monitor를 사용 하 여 규모에 맞게 모니터링

Azure Backup은 Recovery Services 자격 증명 모음에 [기본 제공 모니터링 및 경고 기능](backup-azure-monitoring-built-in-monitor.md) 을 제공 합니다. 이러한 기능은 추가 관리 인프라 없이 사용할 수 있습니다. 그러나이 기본 제공 서비스는 다음과 같은 시나리오에서 제한 됩니다.

- 여러 구독에서 여러 Recovery Services 자격 증명 모음의 데이터를 모니터링 하는 경우
- 기본 알림 채널이 전자 메일이 *아닌* 경우
- 사용자가 더 많은 시나리오에 대 한 경고를 원하는 경우
- Azure의 System Center Data Protection Manager와 같은 온-프레미스 구성 요소의 정보를 보려는 경우 포털이 [**백업 작업**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) 또는 [**백업 경고**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 에 표시 되지 않는 경우

## <a name="using-log-analytics-workspace"></a>Log Analytics 작업 영역 사용

> [!NOTE]
> Azure VM 백업, Azure Backup 에이전트, System Center Data Protection Manager, Azure Vm의 SQL 백업 및 Azure Files 공유 백업 데이터는 진단 설정을 통해 Log Analytics 작업 영역으로 펌프 됩니다. MABS (Microsoft Azure Backup 서버) 지원이 곧 추가 될 예정입니다.

규모에 맞게 모니터링/보고 하려면 두 Azure 서비스의 기능이 필요 합니다. *진단 설정은* 여러 Azure Resource Manager 리소스의 데이터를 다른 리소스에 보냅니다. *Log Analytics* 는 작업 그룹을 사용 하 여 다른 알림 채널을 정의할 수 있는 사용자 지정 경고를 생성 합니다.

다음 섹션에서는 Log Analytics를 사용 하 여 대규모로 Azure Backup를 모니터링 하는 방법에 대해 자세히 설명 합니다.

### <a name="configure-diagnostic-settings"></a>진단 설정 구성

Recovery Services 자격 증명 모음과 같은 Azure Resource Manager 리소스는 예약 된 작업 및 사용자가 트리거한 작업에 대 한 정보를 진단 데이터로 기록 합니다.

모니터링 섹션에서 **진단 설정** 을 선택 하 고 Recovery Services 자격 증명 모음 진단 데이터의 대상을 지정 합니다.

![Recovery Services 자격 증명 모음의 진단 설정, 대상 지정 Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

다른 구독에서 Log Analytics 작업 영역을 대상으로 지정할 수 있습니다. 한 위치의 구독에서 자격 증명 모음을 모니터링 하려면 여러 Recovery Services 자격 증명 모음에 대해 동일한 Log Analytics 작업 영역을 선택 합니다. Log Analytics 작업 영역 Azure Backup와 관련 된 모든 정보를 채널 하려면 **Azurebackupreport** 를 로그로 선택 합니다.

> [!IMPORTANT]
> 구성을 완료 한 후에는 초기 데이터 푸시가 완료 될 때까지 24 시간 동안 대기 해야 합니다. 초기 데이터 푸시 후에는이 문서의 뒷부분에 나오는 [frequency 섹션](#diagnostic-data-update-frequency)에서 모든 이벤트가 푸시됩니다.

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Log Analytics 작업 영역에 솔루션 배포

> [!IMPORTANT]
> Azure Backup에서 LA 기반 모니터링 및 보고를 위해 업데이트 된 다중 뷰 [템플릿이](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) 출시 되었습니다. [이전 솔루션](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) 을 사용 하 던 사용자는 새 솔루션을 배포한 후에도 해당 작업 영역에서 계속 표시 됩니다. 그러나 일부 사소한 스키마 변경으로 인해 이전 솔루션은 부정확 한 결과를 제공할 수 있습니다. 따라서 사용자는 새 템플릿을 배포 해야 합니다.

데이터가 Log Analytics 작업 영역에 포함 된 후에는 Log Analytics에 [GitHub 템플릿을 배포](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) 하 여 데이터를 시각화할 수 있습니다. 작업 영역을 올바르게 식별 하려면 동일한 리소스 그룹, 작업 영역 이름 및 작업 영역 위치를 지정 해야 합니다. 그런 다음이 템플릿을 작업 영역에 설치 합니다.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Log Analytics를 사용 하 여 Azure Backup 데이터 보기

템플릿을 배포한 후 Azure Backup의 모니터링 및 보고에 대 한 솔루션이 작업 영역 요약 영역에 표시 됩니다. 요약으로 이동 하려면 다음 경로 중 하나를 수행 합니다.

- **Azure Monitor**: **Insights** 섹션에서 **자세히** 를 선택 하 고 관련 작업 영역을 선택 합니다.
- **Log Analytics 작업 영역**: 관련 작업 영역을 선택한 다음 **일반**에서 **작업 영역 요약**을 선택 합니다.

![Log Analytics 모니터링 및 보고 타일](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

개요 타일 중 하나를 선택 하면 추가 정보를 볼 수 있습니다. 표시 되는 보고서는 다음과 같습니다.

- 로그가 아닌 백업 작업

   ![백업 작업에 대 한 Log Analytics 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Azure 리소스 백업에서 발생 하는 경고

   ![복원 작업에 대 한 Log Analytics 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

마찬가지로, 다른 타일을 클릭 하면 복원 작업, 클라우드 저장소, 백업 항목, 온-프레미스 리소스 백업에서 발생 하는 경고 및 로그 백업 작업에 대 한 보고서를 볼 수 있습니다.

이러한 그래프가 템플릿과 함께 제공 됩니다. 필요한 경우 그래프를 편집 하거나 더 많은 그래프를 추가할 수 있습니다.

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics를 사용 하 여 경고 만들기

Azure Monitor에서 Log Analytics 작업 영역에 경고를 직접 만들 수 있습니다. 작업 영역에서 *Azure 작업 그룹* 을 사용 하 여 기본 설정 된 알림 메커니즘을 선택 합니다.

> [!IMPORTANT]
> 이 쿼리를 만드는 데 드는 비용에 대 한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

그래프 중 하나를 선택 하 여 Log Analytics 작업 영역의 **로그** 섹션을 엽니다. **로그** 섹션에서 쿼리를 편집 하 고이에 대 한 경고를 만듭니다.

![Log Analytics 작업 영역에서 경고 만들기](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

**새 경고 규칙**을 선택 하면 다음 그림에 표시 된 것 처럼 Azure Monitor 경고 생성 페이지가 열립니다. 여기서 리소스는 이미 Log Analytics 작업 영역으로 표시 되었으며 작업 그룹 통합이 제공 됩니다.

![Log Analytics 경고 만들기 페이지](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>경고 조건

경고의 정의 특성은 트리거 조건입니다. 다음 이미지와 같이 **로그** 페이지에서 kusto 쿼리를 자동으로 로드 하는 **조건을** 선택 합니다. 여기서 요구 사항에 맞게 조건을 편집할 수 있습니다. 자세한 내용은 [샘플 Kusto 쿼리](#sample-kusto-queries)를 참조 하세요.

![경고 조건 설정](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

필요한 경우 Kusto 쿼리를 편집할 수 있습니다. 임계값, 기간 및 빈도를 선택 합니다. 임계값은 경고가 발생 하는 시간을 결정 합니다. 기간은 쿼리가 실행 되는 기간입니다. 예를 들어 임계값이 0 보다 크면 기간이 5 분이 고 빈도가 5 분인 경우 규칙은 5 분 마다 쿼리를 실행 하 여 이전 5 분을 검토 합니다. 결과 수가 0 보다 큰 경우 선택한 작업 그룹을 통해 알림이 표시 됩니다.

#### <a name="alert-action-groups"></a>경고 작업 그룹

알림 채널을 지정 하려면 작업 그룹을 사용 합니다. 사용 가능한 알림 메커니즘을 보려면 **작업 그룹**에서 **새로 만들기**를 선택 합니다.

!["작업 그룹 추가" 창에서 사용할 수 있는 알림 메커니즘](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Log Analytics의 모든 경고 및 모니터링 요구 사항을 충족 하거나 Log Analytics를 사용 하 여 기본 제공 알림을 보완할 수 있습니다.

자세한 내용은 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 및 [Azure Portal에서 작업 그룹 만들기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)를 참조 하세요.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

기본 그래프는 경고를 작성할 수 있는 기본 시나리오에 대 한 Kusto 쿼리를 제공 합니다. 또한 경고를 발생 시킬 데이터를 가져오도록 쿼리를 수정할 수 있습니다. **로그** 페이지에서 다음 샘플 Kusto 쿼리를 붙여넣은 다음 쿼리에 대 한 경고를 만듭니다.

- 성공한 모든 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- 실패 한 모든 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- 모든 성공한 Azure VM 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- 모든 성공한 SQL 로그 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- 성공한 모든 Azure Backup 에이전트 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>진단 데이터 업데이트 빈도

자격 증명 모음의 진단 데이터는 일정 시간 동안 Log Analytics 작업 영역에 펌프 됩니다. 모든 이벤트는 Recovery Services 자격 증명 모음에서 푸시된 후 *20 분에서 30 분* Log Analytics 작업 영역에 도착 합니다. 지연에 대 한 자세한 내용은 다음과 같습니다.

- 모든 솔루션에서 백업 서비스의 기본 제공 경고는 생성 되는 즉시 푸시됩니다. 따라서 일반적으로 20 분에서 30 분 후에 Log Analytics 작업 영역에 나타납니다.
- 모든 솔루션에서 주문형 백업 작업 및 복원 작업은 *완료*되는 즉시 푸시됩니다.
- SQL 백업을 제외한 모든 솔루션에 대해 예약 된 백업 작업은 *완료*되는 즉시 푸시됩니다.
- SQL 백업의 경우 로그 백업이 15 분 마다 발생할 수 있으므로 로그를 포함 하 여 완료 된 모든 예약 된 백업 작업에 대 한 정보는 6 시간 마다 일괄 처리 되 고 푸시됩니다.
- 모든 솔루션에서 백업 항목, 정책, 복구 지점, 저장소 등의 기타 정보는 *하루에 한 번* 이상 푸시됩니다.
- 백업 구성 변경 (예: 정책 변경 또는 정책 편집)은 관련 된 모든 백업 정보의 푸시를 트리거합니다.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Recovery Services 자격 증명 모음의 활동 로그 사용

> [!CAUTION]
> 다음 단계는 *AZURE VM 백업* 에만 적용 됩니다. Azure Backup 에이전트, Azure 내의 SQL 백업 또는 Azure Files와 같은 솔루션에는 이러한 단계를 사용할 수 없습니다.

활동 로그를 사용 하 여 백업 성공 같은 이벤트에 대 한 알림을 받을 수도 있습니다. 시작 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인합니다.
1. 관련 Recovery Services 자격 증명 모음을 엽니다.
1. 자격 증명 모음의 속성에서 **활동 로그** 섹션을 엽니다.

적절 한 로그를 확인 하 고 경고를 만들려면 다음을 수행 합니다.

1. 다음 이미지에 표시 된 필터를 적용 하 여 성공적인 백업에 대 한 활동 로그를 수신 하 고 있는지 확인 합니다. 레코드를 보려면 필요에 따라 **Timespan** 값을 변경 합니다.

   ![Azure VM 백업에 대 한 활동 로그를 검색 하는 필터링](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 작업 이름을 선택 하 여 관련 세부 정보를 확인 합니다.
1. **새 경고 규칙** 을 선택 하 여 **규칙 만들기** 페이지를 엽니다.
1. [Azure Monitor를 사용 하 여 활동 로그 경고 만들기, 보기 및 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)의 단계를 수행 하 여 경고를 만듭니다.

   ![새 경고 규칙](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

여기서 리소스는 Recovery Services 자격 증명 모음 자체입니다. 활동 로그를 통해 알림이 받으려는 모든 자격 증명 모음에 대해 동일한 단계를 반복 합니다. 이 경고는 이벤트를 기반으로 하기 때문에 조건에는 임계값, 기간 또는 빈도가 없습니다. 관련 활동 로그가 생성 되는 즉시 경고가 발생 합니다.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics를 사용 하 여 대규모로 모니터링

활동 로그 및 Log Analytics 작업 영역에서 생성 한 모든 경고를 Azure Monitor에서 볼 수 있습니다. 왼쪽에서 **경고** 창을 열어야 합니다.

활동 로그를 통해 알림을 받을 수는 있지만, 대규모로 모니터링 하기 위해 활동 로그가 아닌 Log Analytics를 사용 하는 것이 좋습니다. 이유는 다음과 같습니다.

- **제한 된 시나리오**: 활동 로그를 통한 알림은 Azure VM 백업에만 적용 됩니다. 모든 Recovery Services 자격 증명 모음에 대해 알림을 설정 해야 합니다.
- **정의 맞춤**: 예약 된 백업 작업이 활동 로그의 최신 정의와 일치 하지 않습니다. 대신 [리소스 로그](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace)에 맞춰집니다. 이 맞춤은 활동 로그 채널을 통해 흐르는 데이터가 변경 될 때 예기치 않은 효과를 발생 시킵니다.
- **활동 로그 채널 문제**: Recovery Services 자격 증명 모음에서 Azure Backup 펌프 활동 로그는 새 모델을 따릅니다. 아쉽게도이 변경 내용은 Azure Government, Azure 독일 및 Azure 중국 21Vianet에서 활동 로그를 생성 하는 것에 영향을 줍니다. 이러한 클라우드 서비스 사용자가 Azure Monitor의 활동 로그에서 경고를 만들거나 구성 하는 경우 경고가 트리거되지 않습니다. 또한 모든 Azure 공용 지역에서 사용자가 [Recovery Services 활동 로그를 Log Analytics 작업 영역으로 수집](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)하면 이러한 로그가 표시 되지 않습니다.

Log Analytics 작업 영역을 사용 하 여 Azure Backup으로 보호 되는 모든 작업에 대해 대규모로 모니터링 하 고 경고 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 쿼리를 만들려면 [Log Analytics 데이터 모델](backup-azure-log-analytics-data-model.md)을 참조 하세요.
