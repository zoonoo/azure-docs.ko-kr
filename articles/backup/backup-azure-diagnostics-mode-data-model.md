---
title: Azure 모니터 로그 데이터 모델
description: 이 문서에서는 Azure 백업 데이터에 대한 Azure 모니터 로그 분석 데이터 모델 세부 정보에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586379"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Azure Backup 데이터용 Log Analytics 데이터 모델

로그 분석 데이터 모델을 사용하여 Log Analytics에서 사용자 지정 경고를 생성합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> 이 데이터 모델은 진단 이벤트를 LA(로그 분석)로 보내는 Azure 진단 모드를 참조합니다. 새 리소스별 모드의 데이터 모델을 알아보려면 [다음](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model) 문서를 참조할 수 있습니다.

## <a name="using-azure-backup-data-model"></a>Azure Backup 데이터 모델 사용

데이터 모델의 일부로 제공되는 다음 필드를 사용하여 요구 사항에 따라 시각적 개체, 사용자 지정 쿼리 및 대시보드를 만들 수 있습니다.

### <a name="alert"></a>경고

이 표에서는 경고 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| AlertUniqueId_s |텍스트 |생성된 경고의 고유 식별자 |
| AlertType_s |텍스트 |경고의 형식(예: Backup) |
| AlertStatus_s |텍스트 |경고의 상태(예: Active) |
| AlertOccurrenceDateTime_s |날짜/시간 |경고를 만든 날짜 및 시간 |
| AlertSeverity_s |텍스트 |경고의 심각도(예: Critical) |
|AlertTimeToResolveInMinutes_s    | Number        |경고를 해결하는 데 걸린 시간입니다. 활성 경고의 경우 비어 있습니다.         |
|AlertConsolidationStatus_s   |텍스트         |경고가 통합 경고인지 아닌지 식별         |
|CountOfAlertsConsolidated_s     |Number         |통합 경고인 경우 통합된 경고 수          |
|AlertRaisedOn_s     |텍스트         |경고가 발생되는 엔터티 유형         |
|AlertCode_s     |텍스트         |경고 유형을 고유하게 식별하는 코드         |
|RecommendedAction_s   |텍스트         |경고를 해결하는 데 권장되는 작업         |
| EventName_s |텍스트 |이벤트의 이름, 항상 AzureBackupCentralReport임 |
| BackupItemUniqueId_s |텍스트 |경고와 관련된 백업 항목의 고유 식별자 |
| SchemaVersion_s |텍스트 |현재 버전의 스키마(예: **V2)** |
| State_s |텍스트 |경고 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 경고가 속한 FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(예: Alert) |
| Category |텍스트 |Azure 모니터 로그에 푸시된 진단 데이터의 범주입니다. 항상 AzureBackupReport임 |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedContainerUniqueId_s |텍스트 |경고와 연결된 보호된 서버의 고유 식별자(V1에서 ProtectedServerUniqueId_s 않음)|
| VaultUniqueId_s |텍스트 |경고와 관련된 보호된 자격 증명 모음의 고유 식별자 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |데이터가 수집되는 리소스의 고유 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="backupitem"></a>BackupItem

이 표에서는 백업 항목 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이벤트의 이름, 항상 AzureBackupCentralReport임 |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 식별자 |
| BackupItemId_s |텍스트 |백업 항목의 식별자(이 필드는 v1 스키마에만 해당) |
| BackupItemName_s |텍스트 |백업 항목의 이름 |
| BackupItemFriendlyName_s |텍스트 |백업 항목의 친숙한 이름 |
| BackupItemType_s |텍스트 |백업 항목의 형식(예: VM, FileFolder) |
| BackupItemProtectionState_s |텍스트 |백업 항목의 보호 상태 |
| BackupItemAppVersion_s |텍스트 |백업 항목의 응용 프로그램 버전 |
| ProtectionState_s |텍스트 |백업 항목의 현재 보호 상태(예: Protected, ProtectionStopped) |
| ProtectionGroupName_s |텍스트 | 보호 그룹의 이름 백업 항목은 SC DPM 및 MABS(해당하는 경우)에 대해 보호됩니다.|
| SecondaryBackupProtectionState_s |텍스트 |백업 항목에 대해 보조 보호가 활성화되어 있는지 여부|
| SchemaVersion_s |텍스트 |스키마 버전(예: **V2)** |
| State_s |텍스트 |백업 항목 개체의 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 수행에 대한 공급자 유형(예: IaaSVM, 이 백업 항목이 속한 FileFolder) |
| OperationName |텍스트 |작업의 이름(예: BackupItem) |
| Category |텍스트 |Azure 모니터 로그에 푸시된 진단 데이터의 범주입니다. 항상 AzureBackupReport임 |
| 리소스 |텍스트 |데이터가 수집되는 리소스(예: Recovery Services 자격 증명 모음 이름) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 ID(예: 복구 서비스 볼트 리소스 ID) |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="backupitemassociation"></a>BackupItemAssociation

이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |  
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 ID |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타내며 **V2입니다.** |
| State_s |텍스트 |백업 항목 연결 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| BackupItemSourceSize_s |텍스트 | 백업 항목의 프런트 엔드 크기 |
| BackupManagementServerUniqueId_s |텍스트 | 백업 관리 서버를 고유하게 식별하는 필드(해당하는 경우 백업 항목이 보호되는 경우) |
| Category |텍스트 |Log Analytics에 푸시된 진단 데이터의 범주를 나타내며, AzureBackupReport입니다. |
| OperationName |텍스트 |현재 작업의 이름(BackupItemAssociation)을 나타냅니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedContainerUniqueId_s |텍스트 |백업 항목과 연결된 보호된 서버의 고유 식별자(V1에서 ProtectedServerUniqueId_s 않음) |
| VaultUniqueId_s |텍스트 |백업 항목이 포함된 자격 증명 모음의 고유 식별자 |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="backupmanagementserver"></a>백업 관리 서버

이 표에서는 다양한 엔터티와의 백업 항목 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
|BackupManagementServerName_s     |텍스트         |백업 관리 서버 이름        |
|AzureBackupAgentVersion_s     |텍스트         |백업 관리 서버의 Azure 백업 에이전트 버전          |
|BackupManagementServerVersion_s     |텍스트         |백업 관리 서버 버전|
|BackupManagementServerOSVersion_s    |텍스트            |백업 관리 서버의 OS 버전|
|BackupManagementServerType_s     |텍스트         |백업 관리 서버 유형, MABS, SC DPM|
|BackupManagementServerUniqueId_s     |텍스트         |백업 관리 서버를 고유하게 식별하는 필드       |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |수집되는 데이터에 대한 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |수집되는 데이터에 대한 리소스 종류(예: Vaults) |

### <a name="job"></a>작업

이 표에서는 작업 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이벤트의 이름, 항상 AzureBackupCentralReport임 |
| BackupItemUniqueId_s |텍스트 |백업 항목의 고유 식별자 |
| SchemaVersion_s |텍스트 |스키마 버전(예: **V2)** |
| State_s |텍스트 |작업 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Job)을 나타냅니다. |
| Category |텍스트 |이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며 AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |작업과 관련된 보호된 서버의 고유 식별자 |
| ProtectedContainerUniqueId_s |텍스트 | 고유 ID를 사용하여 작업이 실행되는 보호된 컨테이너를 식별합니다. |
| VaultUniqueId_s |텍스트 |보호된 자격 증명 모음의 고유 식별자 |
| JobOperation_s |텍스트 |실행되는 작업에 대한 동작(예: Backup, 복원, Backup 구성) |
| JobStatus_s |텍스트 |완료된 작업의 상태(예: Completed, Failed) |
| JobFailureCode_s |텍스트 |발생한 작업 실패로 인한 오류 코드 문자열 |
| JobStartDateTime_s |날짜/시간 |작업 실행이 시작된 날짜 및 시간 |
| BackupStorageDestination_s |텍스트 |백업 스토리지의 대상(예: Cloud, Disk)  |
| AdHocOrScheduledJob_s |텍스트 | 작업이 임시 또는 예약된 작업인지 지정하는 필드 |
| JobDurationInSecs_s | Number |총 작업 기간(초) |
| DataTransferredInMB_s | Number |이 작업에 대해 전송되는 데이터 크기(MB 단위)|
| JobUniqueId_g |텍스트 |작업을 식별하는 고유 ID |
| RecoveryJobDestination_s |텍스트 | 데이터가 복구되는 복구 작업의 대상 |
| RecoveryJobRPDateTime_s |DateTime | 복구 중인 복구 지점이 생성된 날짜, 시간 |
| RecoveryJobRPLocation_s |텍스트 | 복구 중인 복구 지점이 저장된 위치|
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID|
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="policy"></a>정책

이 표에서는 정책 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 적용 가능한 버전 | 설명 |
| --- | --- | --- | --- |
| EventName_s |텍스트 ||이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 ||이 필드는 스키마의 현재 버전을 나타내며 **V2입니다.** |
| State_s |텍스트 ||정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 ||백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 ||현재 작업의 이름(Policy)을 나타냅니다. |
| Category |텍스트 ||이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며 AzureBackupReport입니다. |
| 리소스 |텍스트 ||데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 ||정책을 식별하는 고유 ID |
| PolicyName_s |텍스트 ||지정된 정책의 이름 |
| BackupFrequency_s |텍스트 ||백업이 실행되는 빈도(예: 매일, 매주) |
| BackupTimes_s |텍스트 ||백업이 예약된 날짜 및 시간 |
| BackupDaysOfTheWeek_s |텍스트 ||백업이 예약된 주의 요일 |
| RetentionDuration_s |정수 ||구성된 백업에 대한 보존 기간 |
| DailyRetentionDuration_s |정수 ||구성된 백업에 대한 총 보존 기간(일) |
| DailyRetentionTimes_s |텍스트 ||매일 보존이 구성된 날짜 및 시간 |
| WeeklyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 매주 보존 기간 |
| WeeklyRetentionTimes_s |텍스트 ||매주 보존이 구성된 날짜 및 시간 |
| WeeklyRetentionDaysOfTheWeek_s |텍스트 ||매주 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 보존 기간(월) |
| MonthlyRetentionTimes_s |텍스트 ||매월 보존이 구성된 날짜 및 시간 |
| MonthlyRetentionFormat_s |텍스트 ||월별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) |
| MonthlyRetentionDaysOfTheWeek_s |텍스트 ||매월 보존에 대해 선택한 주의 요일 |
| MonthlyRetentionWeeksOfTheMonth_s |텍스트 ||월별 보존을 구성한 경우 월의 주(예: First, Last 등) |
| YearlyRetentionDuration_s |10진수 ||구성된 백업에 대한 총 보존 기간(연) |
| YearlyRetentionTimes_s |텍스트 ||매년 보존이 구성된 날짜 및 시간 |
| YearlyRetentionMonthsOfTheYear_s |텍스트 ||매년 보존에 대해 선택한 연도의 달 |
| YearlyRetentionFormat_s |텍스트 ||연별 보존에 대한 구성 형식(예: 일 기준 매일, 주 기준 매주) | |
| YearlyRetentionDaysOfTheMonth_s |텍스트 ||매년 보존에 대해 선택한 월의 날짜 |
| SynchronisationFrequencyPerDay_s |정수 |v2|SC DPM 및 MABS에 대해 파일 백업이 동기화되는 일의 횟수 |
| DiffBackupFormat_s |텍스트 |v2|Azure VM 백업에서 SQL에 대한 차등 백업용 형식 |
| DiffBackupTime_s |Time |v2|Azure VM 백업에서 SQL에 대한 차등 백업 시간|
| DiffBackupRetentionDuration_s |10진수 |v2|Azure VM 백업에서 SQL에 대한 차등 백업에 대한 보존 기간|
| LogBackupFrequency_s |10진수 |v2|SQL용 로그 백업 빈도|
| LogBackupRetentionDuration_s |10진수 |v2|Azure VM 백업에서 SQL에 대한 로그 백업에 대한 보존 기간|
| DiffBackupDaysofTheWeek_s |텍스트 |v2|Azure VM 백업에서 SQL에 대한 차등 백업에 대한 요일|
| SourceSystem |텍스트 ||현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 ||수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 ||데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 ||데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 ||데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 ||데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="policyassociation"></a>PolicyAssociation

이 표에서는 다양한 엔터티와의 정책 연결에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 적용 가능한 버전 | 설명 |
| --- | --- | --- | --- |
| EventName_s |텍스트 ||이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 ||이 필드는 스키마의 현재 버전을 나타내며 **V2입니다.** |
| State_s |텍스트 ||정책 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 ||백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 ||현재 작업의 이름(PolicyAssociation)을 나타냅니다. |
| Category |텍스트 ||이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며 AzureBackupReport입니다. |
| 리소스 |텍스트 ||데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| PolicyUniqueId_g |텍스트 ||정책을 식별하는 고유 ID |
| VaultUniqueId_s |텍스트 ||이 정책이 속한 볼트의 고유 ID |
| BackupManagementServerUniqueId_s |텍스트 |v2 |백업 관리 서버를 고유하게 식별하는 필드(해당하는 경우 백업 항목이 보호되는 경우)        |
| SourceSystem |텍스트 ||현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 ||수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 ||데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 ||데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 ||데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 ||데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="protected-container"></a>보호된 컨테이너

이 표에서는 보호된 컨테이너에 대한 기본 필드를 제공합니다. (v1에서 서버가 보호되었습니다. )

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |텍스트 | 보호된 컨테이너를 고유하게 식별하는 필드 |
| ProtectedContainerOSType_s |텍스트 |보호된 컨테이너의 OS 유형 |
| ProtectedContainerOSVersion_s |텍스트 |보호된 컨테이너의 OS 버전 |
| AgentVersion_s |텍스트 |에이전트 백업 또는 보호 에이전트의 버전 번호(SC DPM 및 MABS의 경우) |
| BackupManagementType_s |텍스트 |백업을 수행하기 위한 공급자 유형입니다. 예를 들어, IaaSVM, 파일 폴더 |
| EntityState_s |텍스트 |보호된 서버 개체의 현재 상태입니다. 예를 들어 활성, 삭제됨 |
| ProtectedContainerFriendlyName_s |텍스트 |보호된 서버의 친숙한 이름 |
| ProtectedContainerName_s |텍스트 |보호된 컨테이너의 이름 |
| ProtectedContainerWorkloadType_s |텍스트 |백업된 보호된 컨테이너의 유형입니다. 예를 들어, IaaSVMContainer |
| ProtectedContainerLocation_s |텍스트 |보호된 컨테이너가 온-프레미스 또는 Azure에 있는지 여부 |
| ProtectedContainerType_s |텍스트 |보호된 컨테이너가 서버인지 컨테이너인지 여부 |
| ProtectedContainerProtectionState_s'  |텍스트 |보호된 컨테이너의 보호 상태 |

### <a name="storage"></a>스토리지

이 표에서는 스토리지 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| CloudStorageInBytes_s |10진수 |최신 값을 기반으로 계산된 백업에서 사용되는 클라우드 백업 저장소(이 필드는 v1 스키마에만 해당)|
| ProtectedInstances_s |10진수 |청구에서 프런트 엔드 스토리지 계산에 사용된 보호된 인스턴스 수이며, 최신 값을 기준으로 하여 계산됩니다. |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타내며 **V2입니다.** |
| State_s |텍스트 |스토리지 개체의 현재 상태(예: Active, Deleted) |
| BackupManagementType_s |텍스트 |백업 작업을 수행하는 서버에 대한 공급자 유형(예: IaaSVM, FileFolder) |
| OperationName |텍스트 |현재 작업의 이름(Storage)을 나타냅니다. |
| Category |텍스트 |이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며 AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| ProtectedServerUniqueId_s |텍스트 |저장소가 계산되는 보호된 서버의 고유 ID |
| VaultUniqueId_s |텍스트 |저장소용 볼트의 고유 ID가 계산됩니다. |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |
| StorageUniqueId_s |텍스트 |저장소 엔터티를 식별하는 데 사용되는 고유 ID |
| StorageType_s |텍스트 |클라우드, 볼륨, 디스크 와 같은 스토리지 유형 |
| StorageName_s |텍스트 |저장소 엔터티의 이름(예: E:\ |
| StorageTotalSizeInGBs_s |텍스트 |스토리지 엔터티에서 소비하는 총 스토리지 크기(GB)|

### <a name="storageassociation"></a>스토리지 협회

이 표에서는 저장소를 다른 엔터티에 연결하는 기본 저장소 관련 필드를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- |  --- |
| StorageUniqueId_s |텍스트 |저장소 엔터티를 식별하는 데 사용되는 고유 ID |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타내며 **V2입니다.** |
| BackupItemUniqueId_s |텍스트 |저장소 엔터티와 관련된 백업 항목을 식별하는 데 사용되는 고유 ID |
| BackupManagementServerUniqueId_s |텍스트 |저장소 엔터티와 관련된 백업 관리 서버를 식별하는 데 사용되는 고유 ID|
| VaultUniqueId_s |텍스트 |저장소 엔터티와 관련된 자격 증명 모음을 식별하는 데 사용되는 고유 ID|
| StorageConsumedInMBs_s |Number|해당 저장소의 해당 백업 항목에서 사용하는 저장소 크기 |
| StorageAllocatedInMBs_s |Number |디스크 형식의 해당 저장소에 해당 백업 항목에 의해 할당된 저장소 크기|

### <a name="vault"></a>Vault

이 표에서는 자격 증명 모음 관련 필드에 대한 세부 정보를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| EventName_s |텍스트 |이 이벤트의 이름을 나타내며, 항상 AzureBackupCentralReport입니다. |
| SchemaVersion_s |텍스트 |이 필드는 스키마의 현재 버전을 나타내며 **V2입니다.** |
| State_s |텍스트 |자격 증명 모음 개체의 현재 상태(예: Active, Deleted) |
| OperationName |텍스트 |현재 작업의 이름(Vault)을 나타냅니다. |
| Category |텍스트 |이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타내며 AzureBackupReport입니다. |
| 리소스 |텍스트 |데이터가 수집되는 리소스이며, Recovery Services 자격 증명 모음 이름이 표시됩니다. |
| VaultUniqueId_s |텍스트 |볼트의 고유 ID |
| VaultName_s |텍스트 |자격 증명 모음의 이름 |
| AzureDataCenter_s |텍스트 |자격 증명 모음이 위치한 데이터 센터 |
| StorageReplicationType_s |텍스트 |자격 증명 모음에 대한 스토리지 복제 유형(예: GeoRedundant) |
| SourceSystem |텍스트 |현재 데이터의 원본 시스템(Azure) |
| ResourceId |텍스트 |수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 복구 서비스 볼트 리소스 ID |
| SubscriptionId |텍스트 |데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| ResourceGroup |텍스트 |데이터가 수집되는 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| ResourceProvider |텍스트 |데이터가 수집되는 리소스 공급자(예: Microsoft.RecoveryServices) |
| ResourceType |텍스트 |데이터가 수집되는 리소스 종류(예: Vaults) |

### <a name="backup-management-server"></a>백업 관리 서버

이 표에서는 백업 관리 서버에 대한 기본 필드를 제공합니다.

|필드  |데이터 형식  | 설명  |
|---------|---------|----------|
|BackupManagementServerName_s     |텍스트         |백업 관리 서버 이름        |
|AzureBackupAgentVersion_s     |텍스트         |백업 관리 서버의 Azure 백업 에이전트 버전          |
|BackupManagementServerVersion_s     |텍스트         |백업 관리 서버 버전|
|BackupManagementServerOSVersion_s     |텍스트            |백업 관리 서버의 OS 버전|
|BackupManagementServerType_s     |텍스트         |백업 관리 서버 유형, MABS, SC DPM|
|BackupManagementServerUniqueId_s     |텍스트         |백업 관리 서버를 고유하게 식별하는 필드       |

### <a name="preferredworkloadonvolume"></a>기본 워크로드온볼륨

이 표에서는 볼륨이 연결된 워크로드를 지정합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| StorageUniqueId_s |텍스트 |저장소 엔터티를 식별하는 데 사용되는 고유 ID |
| BackupItemType_s |텍스트 |이 볼륨이 기본 저장소인 워크로드|

### <a name="protectedinstance"></a>보호인스턴스

이 표에서는 보호된 기본 인스턴스 관련 필드를 제공합니다.

| 필드 | 데이터 형식 |적용 가능한 버전 | 설명 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |텍스트 |v2|DPM, MABS를 사용하여 백업된 VM의 백업 항목을 식별하는 데 사용되는 고유 ID|
| ProtectedContainerUniqueId_s |텍스트 |v2|DPM, MABS를 사용하여 백업된 VM을 제외한 모든 컨테이너에 대해 보호된 컨테이너를 식별하는 데 사용되는 고유 ID|
| ProtectedInstanceCount_s |텍스트 |v2|해당 날짜 시간에 연결된 백업 항목 또는 보호된 컨테이너에 대한 보호된 인스턴스 수|

### <a name="recoverypoint"></a>RecoveryPoint

이 표에서는 기본 복구 지점 관련 필드를 제공합니다.

| 필드 | 데이터 형식 | 설명 |
| --- | --- | --- |
| BackupItemUniqueId_s |텍스트 |DPM, MABS를 사용하여 백업된 VM의 백업 항목을 식별하는 데 사용되는 고유 ID|
| OldestRecoveryPointTime_s |텍스트 |백업 항목에 대한 가장 오래된 복구 지점의 날짜 시간|
| OldestRecoveryPointLocation_s |텍스트 |백업 항목의 가장 오래된 복구 지점 위치|
| LatestRecoveryPointTime_s |텍스트 |백업 항목의 최신 복구 지점의 날짜 시간|
| LatestRecoveryPointLocation_s |텍스트 |백업 항목의 최신 복구 지점 위치|

## <a name="sample-kusto-queries"></a>샘플 쿠스토 쿼리

다음은 Azure 진단 테이블에 있는 Azure Backup 데이터에 대한 쿼리를 작성하는 데 도움이 되는 몇 가지 샘플입니다.

- 모든 성공적인 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- 실패한 모든 백업 작업

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- 모든 성공적인 Azure VM 백업 작업

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

- 모든 성공적인 SQL 로그 백업 작업

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

- 모든 성공적인 Azure 백업 에이전트 작업

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
    
## <a name="next-steps"></a>다음 단계

데이터 모델을 검토한 후 Azure Monitor 로그에서 [사용자 지정 쿼리를 만들어](../azure-monitor/learn/tutorial-logs-dashboards.md) 자체 대시보드를 작성할 수 있습니다.
