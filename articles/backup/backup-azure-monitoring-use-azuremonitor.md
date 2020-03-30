---
title: Azure 모니터를 사용 하 고 Azure 백업 모니터링
description: Azure 백업 워크로드를 모니터링하고 Azure 모니터를 사용하여 사용자 지정 경고를 만듭니다.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459517"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Azure 모니터를 사용하여 대규모 모니터링

Azure Backup은 [기본 제공 모니터링 및 경고 기능](backup-azure-monitoring-built-in-monitor.md)을 Recovery Services 자격 증명 모음에 제공합니다. 이러한 기능은 추가 관리 인프라 없이 사용할 수 있습니다. 그러나 이 기본 제공 서비스는 다음 시나리오에서 제한됩니다.

- 구독 전체에서 여러 복구 서비스 자격 증명 모음의 데이터를 모니터링하는 경우
- 기본 알림 채널이 전자 메일이 *아닌* 경우
- 사용자가 더 많은 시나리오에 대한 경고를 원하는 경우
- 포털이 [**백업 작업**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) 또는 [**백업 경고에**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 표시되지 않는 Azure의 시스템 센터 데이터 보호 관리자와 같은 온-프레미스 구성 요소에서 정보를 보려면

## <a name="using-log-analytics-workspace"></a>로그 분석 작업 영역 사용

### <a name="create-alerts-by-using-log-analytics"></a>로그 분석을 사용하여 경고 만들기

Azure 모니터에서 로그 분석 작업 영역에서 사용자 고유의 경고를 만들 수 있습니다. 작업 영역에서 *Azure 작업 그룹을* 사용하여 기본 설정 알림 메커니즘을 선택합니다.

> [!IMPORTANT]
> 이 쿼리를 만드는 데 드는 비용에 대한 자세한 내용은 [Azure Monitor 가격 책정을](https://azure.microsoft.com/pricing/details/monitor/)참조하십시오.

로그 분석 작업 영역의 **로그** 섹션을 열고 사용자 고유의 로그에 대한 쿼리를 만듭니다. **새 경고 규칙을**선택하면 다음 이미지와 같이 Azure Monitor 경고 만들기 페이지가 열립니다.

![로그 분석 작업 영역에서 경고 만들기](media/backup-azure-monitoring-laworkspace/custom-alert.png)

여기서 리소스는 이미 Log Analytics 작업 영역으로 표시되어 있으며 작업 그룹 통합이 제공됩니다.

![로그 분석 경고 생성 페이지](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>경고 조건

경고의 정의 특성은 경고의 트리거링 조건입니다. 다음 이미지와 같이 **Logs** 페이지에 Kusto 쿼리를 자동으로 로드하려면 **조건을** 선택합니다. 여기에서 필요에 맞게 조건을 편집할 수 있습니다. 자세한 내용은 [샘플 Kusto 쿼리를](#sample-kusto-queries)참조하십시오.

![경고 조건 설정](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

필요한 경우 Kusto 쿼리를 편집할 수 있습니다. 임계값, 기간 및 빈도를 선택합니다. 임계값은 경고를 발생시 결정하는 기준입니다. 기간은 쿼리가 실행되는 기간입니다. 예를 들어 임계값이 0보다 크고 기간이 5분이고 빈도가 5분인 경우 규칙은 5분마다 쿼리를 실행하여 이전 5분을 검토합니다. 결과 수가 0보다 크면 선택한 작업 그룹을 통해 알림을 받게 됩니다.

#### <a name="alert-action-groups"></a>경고 작업 그룹

작업 그룹을 사용하여 알림 채널을 지정합니다. 사용 가능한 알림 메커니즘을 보려면 **작업 그룹**에서 **새 만들기를**선택합니다.

!["작업 그룹 추가" 창에서 사용 가능한 알림 메커니즘](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Log Analytics에서만 모든 경고 및 모니터링 요구 사항을 충족하거나 Log Analytics를 사용하여 기본 제공 알림을 보완할 수 있습니다.

자세한 내용은 [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리를 참조하고 Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) [Portal에서 작업 그룹을 만들고 관리합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

### <a name="sample-kusto-queries"></a>샘플 쿠스토 쿼리

기본 그래프는 경고를 빌드할 수 있는 기본 시나리오에 대한 Kusto 쿼리를 제공합니다. 경고를 받을 데이터를 얻기 위해 쿼리를 수정할 수도 있습니다. **로그** 페이지에 다음 샘플 Kusto 쿼리를 붙여 넣은 다음 쿼리에 대한 경고를 만듭니다.

- 모든 성공적인 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- 실패한 모든 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- 모든 성공적인 Azure VM 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 모든 성공적인 SQL 로그 백업 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 모든 성공적인 Azure 백업 에이전트 작업

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 백업 항목당 사용되는 백업 스토리지

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>진단 데이터 업데이트 빈도

볼트의 진단 데이터는 약간의 지연이 있는 로그 분석 작업 공간으로 펌핑됩니다. 모든 이벤트는 복구 서비스 자격 증명 모음에서 푸시된 후 *20~30분* 후에 Log Analytics 작업 영역에 도착합니다. 지연에 대한 자세한 내용은 다음과 같습니다.

- 모든 솔루션에서 백업 서비스의 기본 제공 경고는 생성되는 즉시 푸시됩니다. 따라서 일반적으로 20~30분 후에 Log Analytics 작업 영역에 나타납니다.
- 모든 솔루션에서 온디맨드 백업 작업 및 복원 작업이 *완료되는*즉시 푸시됩니다.
- SQL 백업을 제외한 모든 솔루션의 경우 예약된 백업 작업이 *완료되는*즉시 푸시됩니다.
- SQL 백업의 경우 로그 백업은 15분마다 발생할 수 있으므로 로그를 포함하여 완료된 모든 예약된 백업 작업에 대한 정보가 6시간마다 일괄 처리되고 푸시됩니다.
- 모든 솔루션에서 백업 항목, 정책, 복구 지점, 저장소 등과 같은 기타 정보는 *하루에 한 번* 이상 푸시됩니다.
- 백업 구성(예: 정책 변경 또는 정책 편집)이 변경하면 모든 관련 백업 정보가 푸시됩니다.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>복구 서비스 볼트의 활동 로그 사용

> [!CAUTION]
> 다음 단계는 *Azure VM 백업에만 적용됩니다.* Azure 백업 에이전트, Azure 내의 SQL 백업 또는 Azure 파일과 같은 솔루션에는 이러한 단계를 사용할 수 없습니다.

활동 로그를 사용하여 백업 성공과 같은 이벤트에 대한 알림을 받을 수도 있습니다. 시작하려면 다음 단계를 따르십시오.

1. Azure Portal에 로그인합니다.
1. 관련 복구 서비스 자격 증명 모음을 엽니다.
1. 볼트의 속성에서 활동 로그 섹션을 **엽니다.**

적절한 로그를 식별하고 경고를 만들려면 다음을 수행하십시오.

1. 다음 이미지에 표시된 필터를 적용하여 성공적인 백업을 위한 활동 로그를 받고 있는지 확인합니다. 레코드를 보려면 필요에 따라 **Timespan** 값을 변경합니다.

   ![Azure VM 백업에 대한 활동 로그를 찾기 위한 필터링](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 작업 이름을 선택하여 관련 세부 정보를 확인합니다.
1. **새 경고 규칙을** 선택하여 규칙 **만들기** 페이지를 엽니다.
1. [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리의](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)단계에 따라 경고를 만듭니다.

   ![새 경고 규칙](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

여기서 리소스는 복구 서비스 볼트 자체입니다. 활동 로그를 통해 알림을 받을 모든 자격 증명 모음에 대해 동일한 단계를 반복합니다. 이 경고는 이벤트를 기반으로 하기 때문에 조건에는 임계값, 기간 또는 빈도가 없습니다. 관련 활동 로그가 생성되는 즉시 경고가 발생합니다.

## <a name="using-log-analytics-to-monitor-at-scale"></a>로그 분석을 사용하여 대규모 모니터링

Azure Monitor에서 활동 로그 및 로그 분석 작업 공간에서 만든 모든 경고를 볼 수 있습니다. 왼쪽의 **경고** 창을 열기만 하면 됩니다.

활동 로그를 통해 알림을 받을 수 있지만 대규모 모니터링을 위한 활동 로그 보다는 Log Analytics를 사용하는 것이 좋습니다. 이유는 다음과 같습니다.

- **제한된 시나리오:** 활동 로그를 통한 알림은 Azure VM 백업에만 적용됩니다. 알림은 모든 복구 서비스 자격 증명 모음에 대해 설정되어야 합니다.
- **정의 맞춤**: 예약된 백업 활동이 활동 로그의 최신 정의에 맞지 않습니다. 대신 [리소스 로그와 정렬됩니다.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace) 이 정렬은 활동 로그 채널을 통해 흐르는 데이터가 변경될 때 예기치 않은 영향을 미칩니다.
- **활동 로그 채널 문제**: 복구 서비스 자격 증명 모음에서 Azure Backup에서 펌핑되는 활동 로그는 새 모델을 따릅니다. 안타깝게도 이 변경 사항은 Azure 정부, Azure 독일 및 Azure China 21Vianet의 활동 로그 생성에 영향을 줍니다. 이러한 클라우드 서비스의 사용자가 Azure Monitor의 활동 로그에서 경고를 만들거나 구성하면 경고가 트리거되지 않습니다. 또한 모든 Azure 공용 리전에서 사용자가 [복구 서비스 활동 로그를 Log Analytics 작업 영역에 수집하는](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)경우 이러한 로그가 나타나지 않습니다.

Azure Backup에서 보호되는 모든 워크로드에 대해 대규모로 모니터링 및 경고하려면 Log Analytics 작업 영역을 사용합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 쿼리를 만들려면 [로그 분석 데이터 모델을](backup-azure-reports-data-model.md)참조하십시오.
