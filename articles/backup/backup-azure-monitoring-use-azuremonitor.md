---
title: 'Azure Backup: Azure Monitor를 사용 하 여 Azure Backup 모니터링'
description: Azure Backup 워크 로드를 모니터링 하 고 Azure Monitor를 사용 하 여 사용자 지정 경고를 만듭니다.
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; 경고; 진단 설정 작업 그룹
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786322"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure Monitor를 사용 하 여 대규모로 모니터링

Azure Backup은 제공 [기본 제공 모니터링 및 경고 기능](backup-azure-monitoring-built-in-monitor.md) Recovery Services 자격 증명 모음에 있습니다. 이러한 기능은 추가 관리 인프라 없이 사용할 수 있습니다. 그러나이 기본 제공 서비스는 다음과 같은 시나리오에서 제한 됩니다.

- 구독에서 여러 Recovery Services 자격 증명 모음에서 데이터를 모니터링 하는 경우
- 이면 기본 알림 채널 *되지* 전자 메일
- 사용자가 더 많은 시나리오에 대 한 경고를 발생 시킬 경우
- 포털에 표시 되지 않습니다는 Azure에서 온-프레미스 구성 요소와 같은 System Center Data Protection Manager에서에서 정보를 볼 것인지 [ **Backup 작업** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) 하거나 [  **백업 경고**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Log Analytics 작업 영역 사용

> [!NOTE]
> Azure VM 백업, Azure Backup 에이전트를 System Center Data Protection Manager, Azure Vm에서 SQL 백업 및 Azure 파일 공유 백업에서 데이터 진단 설정을 통해 Log Analytics 작업 영역에 주입 됩니다. 

확장을 모니터링 하려면 두 가지 Azure 서비스의 기능 해야 합니다. *진단 설정을* 다른 리소스에 여러 Azure Resource Manager 리소스에서 데이터를 전송 합니다. *Log Analytics* 다른 알림 채널로 정의 작업 그룹을 사용할 수 있는 사용자 지정 경고를 생성 합니다. 

다음 섹션에는 대규모로 Azure Backup을 모니터링 하려면 Log Analytics를 사용 하는 방법을 자세히 설명 합니다.

### <a name="configure-diagnostic-settings"></a>진단 설정 구성

Recovery Services 자격 증명 같은 azure Resource Manager 리소스에서 진단 데이터와 예약 된 작업 및 작업을 트리거한 사용자에 대 한 정보를 기록합니다. 

모니터링 섹션에서 선택 **진단 설정** 및 Recovery Services 자격 증명 모음의 진단 데이터에 대 한 대상을 지정 합니다.

![Log Analytics를 대상으로 하는 Recovery Services 자격 증명의 진단 설정](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

다른 구독에서 Log Analytics 작업 영역을 대상으로 지정할 수 있습니다. 한 곳에서 구독의 자격 증명 모음을 모니터링 하려면 여러 Recovery Services 자격 증명 모음에 대 한 동일한 Log Analytics 작업 영역을 선택 합니다. Log Analytics 작업 영역에 Azure Backup에 관련 된 모든 정보를 채널 선택 **AzureBackupReport** 로그 합니다.

> [!IMPORTANT]
> 구성이 완료 되 면 완료 초기 데이터 푸시에 대해 24 시간 동안 기다려야 합니다. 이 문서의 뒷부분에 설명 된 대로 모든 이벤트는 초기 데이터 푸시, 후 푸시됩니다 합니다 [빈도 섹션](#diagnostic-data-update-frequency)합니다.

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Log Analytics 작업 영역에 솔루션 배포

Log Analytics 작업 영역 내에서 데이터를 한 후 [GitHub 템플릿 배포](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) Log analytics로 데이터를 시각화 합니다. 작업 영역을 올바르게 식별 하려면 동일한 리소스 그룹, 작업 영역 이름 및 작업 영역 위치 지정 해야 합니다. 작업 영역에서이 서식 파일을 설치 합니다.

> [!NOTE]
> Log Analytics 작업 영역에서 경고, 백업 작업 또는 복원 작업이 없다면 "BadArgumentError" 오류 코드를 포털에 표시 될 수 있습니다. 이 오류를 무시 하 고 솔루션을 사용 하 여 계속 합니다. 관련 데이터 형식이 시작 후 작업 영역에 들어가는 시각화 동일 반영 하 고 오류를 더 이상 표시 되지 않습니다.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Log Analytics를 사용 하 여 Azure Backup 데이터 보기

템플릿을 배포한 후 Azure Backup 모니터링 솔루션 작업 영역 요약 영역에 표시 됩니다. 요약에 이동 하려면 이러한 경로 중 하나를 수행 합니다.

- **Azure Monitor**: 에 **Insights** 섹션에서 **자세한** 를 관련 작업 영역을 선택 합니다.
- **Log Analytics 작업 영역**: 관련 작업 영역을 선택 합니다. 선택한 후 **일반**를 선택 **작업 영역 요약**합니다.

![Log Analytics 모니터링 타일](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

모니터링 타일을 선택 하면 디자이너 템플릿이 일련의 기본 모니터링 데이터에 대 한 그래프를 Azure Backup에서 열립니다. 다음은 일부 그래프 표시 됩니다.

* 모든 백업 작업

   ![백업 작업에 대 한 로그 분석 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* 복원 작업

   ![복원 작업에 대 한 로그 분석 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Azure 리소스에 대 한 기본 제공 Azure 백업 경고

   ![Azure 리소스에 대 한 기본 제공 Azure Backup 경고에 대 한 로그 분석 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* 온-프레미스 리소스에 대 한 기본 제공 Azure 백업 경고

   ![온-프레미스 리소스에 대 한 기본 제공 Azure Backup 경고에 대 한 로그 분석 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* 활성 데이터 원본

   ![활성 백업 엔터티에 대 한 로그 분석 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Recovery Services 자격 증명 모음 클라우드 저장소

   ![Recovery Services 자격 증명 모음 클라우드 저장소에 대 한 로그 분석 그래프](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

이러한 그래프는 템플릿으로 제공 됩니다. 그래프를 편집 하거나 하는 경우 더 많은 그래프를 추가할 수 있습니다.

> [!IMPORTANT]
> 템플릿을 배포 하는 경우 읽기 전용 잠금을 만드는 기본적으로 합니다. 를 편집 하 고 템플릿을 저장 한 잠금을 제거 해야 합니다. 잠금을 제거할 수 있습니다는 **설정** Log Analytics 작업 영역의 섹션에는 **잠금** 창.

### <a name="create-alerts-by-using-log-analytics"></a>Log Analytics를 사용 하 여 경고 만들기

Azure Monitor에서 Log Analytics 작업 영역에서 고유한 경고를 만들 수 있습니다. 작업 영역을 사용 하 여 *Azure 작업 그룹* 기본 알림 메커니즘을 선택 합니다. 

> [!IMPORTANT]
> 이 쿼리를 만드는 비용에 정보를 참조 하세요 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)합니다.

열려는 그래프 중 하나를 선택 합니다 **로그** Log Analytics 작업 영역의 섹션입니다. 에 **로그** 섹션, 쿼리를 편집 하 고,에 경고를 만듭니다.

![Log Analytics 작업 영역에서 경고 만들기](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

선택 하면 **새 경고 규칙**, 다음 이미지에 표시 된 대로 Azure Monitor 경고 만들기 페이지가 열립니다. 리소스는 Log Analytics 작업 영역으로 이미 표시 되는 여기 하 고 작업 그룹 통합이 제공 됩니다.

![Log Analytics 경고 만들기 페이지](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>경고 조건

경고의 대표적인 특징에 해당 트리거 조건이 있습니다. 선택 **조건을** 에서 Kusto 쿼리를 자동으로 로드 하는 **로그** 다음 그림에 나와 있는 것 처럼 페이지. 다음 조건이 필요에 맞게 편집할 수 있습니다. 자세한 내용은 [샘플 Kusto 쿼리](#sample-kusto-queries)합니다.

![경고 조건이 설정](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

필요한 경우 Kusto 쿼리를 편집할 수 있습니다. 임계값, 기간 및 빈도 선택 합니다. 임계값 경고를 발생 시킬 시점을 결정 합니다. 기간은 쿼리가 실행 되는 기간입니다. 예를 들어, 임계값은 0 보다 큰, 기간은 5 분이 고 빈도가 5 분, 하는 경우 다음 규칙 쿼리를 실행 5 분 마다 5 분을 검토 합니다. 결과 수가 0 보다 큰 경우 선택한 작업 그룹을 통해 알림을 받을 있습니다.

#### <a name="alert-action-groups"></a>경고 작업 그룹

알림 채널을 지정 하는 작업 그룹을 사용 합니다. 사용 가능한 알림 메커니즘을 아래에서 보려는 **작업 그룹**를 선택 **새로 만들기**합니다.

!["작업 그룹 추가" 창에서 사용할 수 있는 알림 메커니즘](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

모든 경고 및 모니터링 단독으로 Log Analytics에서 요구 사항을 충족할 수 있습니다 또는 기본 제공 알림을 보완 하기 위해 Log Analytics를 사용할 수 있습니다.

자세한 내용은 참조 하세요. [만들기, 보기 및 Azure Monitor를 사용 하 여 로그 경고를 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 하 고 [만들기 및 Azure portal에서 작업 그룹 관리](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)합니다.

### <a name="sample-kusto-queries"></a>Kusto 쿼리 예제

기본 그래프 경고를 작성할 수 있는 기본 시나리오에 대 한 Kusto 쿼리를 제공 합니다. 또한 경고를 표시 하려는 데이터를 가져오는 쿼리를 수정할 수 있습니다. 다음 샘플 Kusto 쿼리를 붙여 합니다 **로그** 페이지을 만든 다음 쿼리 경고:

* 모든 성공한 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* 모든 실패 한 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* 성공한 모든 Azure VM 백업 작업

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

* 모든 성공적인 SQL 로그 백업 작업

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

* 성공한 모든 Azure Backup 에이전트 작업

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

### <a name="diagnostic-data-update-frequency"></a>진단 데이터 새로 고침 빈도

자격 증명 모음에서 진단 데이터를 일부 lag 사용 하 여 Log Analytics 작업 영역에 주입 됩니다. Log Analytics 작업 영역에 모든 이벤트 도착 *20-30 분* 후 Recovery Services 자격 증명 모음에서 푸시됩니다. 다음과 같습니다. 추가 지연 하는 방법에 대 한 세부 정보

- 모든 솔루션에서 백업 서비스의 기본 제공 경고 만들어지는 즉시 푸시됩니다. 이므로 일반적으로 20 ~ 30 분 후 Log Analytics 작업 영역에 표시 합니다.
- 모든 솔루션에서 애드혹 백업 작업 및 복원 작업 푸시되는 되 자 마자 *완료*합니다.
- 예약 된 백업 작업으로 빨리 푸시되 SQL 백업 제외한 모든 솔루션용 *완료*합니다.
- SQL 백업에 대 한 로그 백업에는 15 분 마다 수행 될 수 있으므로 모든 완료 된 예약된 된 백업 작업, 로그를 포함 하 여에 대 한 정보 일괄 처리 이며 6 시간 마다 푸시됩니다.
- 모든 솔루션에서 백업 항목, 정책, 복구 지점, 저장소 및 등과 같은 다른 정보 푸시됩니다 이상 *하루에 한 번입니다.*
- 백업 구성 (예: 정책 변경 또는 정책 편집) 변경의 모든 관련된 백업 정보를 푸시를 트리거합니다.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Recovery Services 자격 증명 모음의 활동 로그를 사용 하 여

> [!CAUTION]
> 다음 단계에만 적용 *Azure VM 백업 합니다.* Azure Backup 에이전트, Azure 또는 Azure 파일 내에서 SQL 백업 등의 솔루션에 대 한이 단계를 사용할 수 없습니다.

또한 백업 성공 여부와 같은 이벤트에 대 한 알림을 가져오기 위한 활동 로그를 사용할 수 있습니다. 시작 하려면 다음이 단계를 수행 합니다.

1. Azure Portal에 로그인합니다.
1. 관련 Recovery Services 자격 증명 모음을 엽니다. 
1. 자격 증명 모음의 속성을 엽니다는 **활동 로그** 섹션입니다.

경고를 만들고 적절 한 로그 확인:

1. 다음 그림과에서 같이 필터를 적용 하 여 성공적인 백업에 대 한 활동 로그를 수신 하는 것을 확인 합니다. 변경 된 **Timespan** 레코드를 보려면 필요에 따라 값입니다.

   ![Azure VM 백업에 대 한 활동 로그를 찾으려면 필터링](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 관련 세부 정보를 보려면 작업 이름을 선택 합니다.
1. 선택 **새 경고 규칙** 열려는 합니다 **규칙 만들기** 페이지입니다. 
1. 단계를 수행 하 여 경고 만들기 [만들기, 보기 및 활동 로그 경고 Azure Monitor를 사용 하 여 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)합니다.

   ![새 경고 규칙](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

다음 리소스 자체는 Recovery Services 자격 증명은입니다. 모든 활동 로그를 통해 알림을 받게 하려는 자격 증명 모음에 대 한 동일한 단계를 반복 해야 합니다. 조건으로 하기 때문에이 경고 이벤트 임계값, 마침표 또는 빈도 없습니다. 관련 활동 로그가 생성 되는 즉시 경고가 발생 합니다.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics를 사용 하 여 대규모로 모니터링 하려면

활동 로그 및 Azure Monitor에서 Log Analytics 작업 영역에서 만든 모든 경고를 볼 수 있습니다. 열고 합니다 **경고** 왼쪽 창입니다.

활동 로그를 통해 알림을 가져올 수 있습니다, 있지만 규모의 모니터링에 대 한 활동 로그 보다는 Log Analytics를 사용 하는 것이 좋습니다. 그 이유는 다음과 같습니다.

- **시나리오 제한**: 알림 활동 로그를 통해 Azure VM 백업에만 적용 됩니다. 모든 Recovery Services 자격 증명 모음에 대 한 알림은 설정 해야 합니다.
- **정의 맞게**: 예약된 된 백업 작업은 활동 로그의 최신 정의 사용 하 여 맞지 않습니다. 대신에 맞추는 [진단 로그](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs)합니다. 이 맞춤 예기치 않은 결과가 발생 작업을 통해 흐르는 데이터를 채널 변경 내용을 기록 합니다.
- **활동 로그 채널을 사용 하 여 문제**: Recovery Services 자격 증명 모음에 Azure Backup에서 펌프 되는 활동 로그는 새 모델을 따릅니다. 그러나이 변경에는 Azure Government, Azure 독일 및 Azure 중국 21Vianet에서 활동 로그 생성을 영향을 줍니다. 사용자가 이러한 클라우드 서비스 만들기 또는 Azure Monitor의 활동 로그에서 모든 경고를 구성 하는 경우 경고 트리거 되지 않습니다. 또한 모든 Azure 공용 지역에서 사용자가 [Log Analytics 작업 영역으로 Recovery Services 활동 로그를 수집](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), 이러한 로그에 표시 되지 않습니다.

모니터링 및 Azure Backup으로 보호 되는 모든 워크 로드에 대해 대규모로 경고에 대 한 Log Analytics 작업 영역을 사용 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 쿼리를 만들려면 참조 [Log Analytics 데이터 모델](backup-azure-log-analytics-data-model.md)합니다.
