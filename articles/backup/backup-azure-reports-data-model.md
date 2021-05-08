---
title: Azure Backup 진단 이벤트에 대한 데이터 모델
description: 이 데이터 모델은 LA(Log Analytics)로 진단 이벤트를 보내는 리소스별 모드를 참조합니다.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499597"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure Backup 진단 이벤트에 대한 데이터 모델

> [!NOTE]
>
> 사용자 지정 보고 보기를 만들려면 아래에 있는 원시 테이블로 작업하는 대신 [Azure Monitor 로그에서 시스템 함수](backup-reports-system-functions.md)를 사용하는 것이 좋습니다.

## <a name="coreazurebackup"></a>CoreAzureBackup

이 표에서는 자격 증명 모음 및 백업 항목과 같은 핵심 백업 엔터티에 대한 정보를 제공합니다.

| **필드**                         | **데이터 형식** | **설명**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | 텍스트          | 수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID). |
| OperationName                     | 텍스트          | 이 필드는 현재 작업의 이름(BackupItem, BackupItemAssociation 또는 ProtectedContainer)을 나타냅니다. |
| Category                          | 텍스트          | 이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주를 나타냅니다. 예: CoreAzureBackup. |
| AgentVersion                      | 텍스트          | 에이전트 백업 또는 보호 에이전트의 버전 번호(SC DPM 및 MABS의 경우) |
| AzureBackupAgentVersion           | 텍스트          | 백업 관리 서버의 Azure Backup 에이전트 버전 |
| AzureDataCenter                   | 텍스트          | 자격 증명 모음이 위치한 데이터 센터                       |
| BackupItemAppVersion              | 텍스트          | 백업 항목의 애플리케이션 버전                       |
| BackupItemFriendlyName            | 텍스트          | 백업 항목의 식별 이름                             |
| BackupItemName                    | 텍스트          | 백업 항목의 이름                                      |
| BackupItemProtectionState         | 텍스트          | Backup 항목의 보호 상태                          |
| BackupItemFrontEndSize            | 텍스트          | 백업 항목의 프런트 엔드 크기                            |
| BackupItemType                    | 텍스트          | 백업 항목의 유형. 예: VM, FileFolder             |
| BackupItemUniqueId                | 텍스트          | 백업 항목의 고유 식별자                         |
| BackupManagementServerType        | 텍스트          | MABS, SC DPM에서와 같은 백업 관리 서버 유형     |
| BackupManagementServerUniqueId    | 텍스트          | 백업 관리 서버를 고유하게 식별하는 필드      |
| BackupManagementType              | 텍스트          | 백업 작업을 수행하는 서버의 공급자 유형. 예: IaaSVM, FileFolder |
| BackupManagementServerName        | 텍스트          | 백업 관리 서버의 이름                         |
| BackupManagementServerOSVersion   | 텍스트          | 백업 관리 서버의 OS 버전                   |
| BackupManagementServerVersion     | 텍스트          | 백업 관리 서버의 버전                      |
| LatestRecoveryPointLocation       | 텍스트          | 백업 항목에 대한 최신 복구 지점의 위치    |
| LatestRecoveryPointTime           | DateTime      | 백업 항목에 대한 최신 복구 지점의 날짜 시간   |
| OldestRecoveryPointLocation       | 텍스트          | 백업 항목에 대한 가장 오래된 복구 지점의 위치    |
| OldestRecoveryPointTime           | DateTime      | 백업 항목에 대한 최신 복구 지점의 날짜 시간   |
| PolicyUniqueId                    | 텍스트          | 정책을 식별하는 고유 ID                             |
| ProtectedContainerFriendlyName    | 텍스트          | 보호된 서버의 식별 이름                        |
| ProtectedContainerLocation        | 텍스트          | 보호된 컨테이너가 온-프레미스 또는 Azure에 있는지 여부 |
| ProtectedContainerName            | 텍스트          | 보호된 컨테이너의 이름                            |
| ProtectedContainerOSType          | 텍스트          | 보호된 컨테이너의 OS 유형                          |
| ProtectedContainerOSVersion       | 텍스트          | 보호된 컨테이너의 OS 버전                        |
| ProtectedContainerProtectionState | 텍스트          | 보호된 컨테이너의 보호 상태                  |
| ProtectedContainerType            | 텍스트          | 보호된 컨테이너가 서버인지 또는 컨테이너인지 여부  |
| ProtectedContainerUniqueId        | 텍스트          | DPM, MABS를 사용하여 백업된 VM을 제외한 모든 항목에 대해 보호된 컨테이너를 식별하는 데 사용되는 고유 ID |
| ProtectedContainerWorkloadType    | 텍스트          | 백업된 보호된 컨테이너의 유형. 예: IaaSVMContainer |
| ProtectionGroupName               | 텍스트          | SC DPM 및 MABS의 경우 백업 항목이 보호되는 보호 그룹의 이름(해당하는 경우) |
| ResourceGroupName                 | 텍스트          | 수집되는 데이터에 대한 리소스의 리소스 그룹(예: Recovery Services 자격 증명 모음) |
| schemaVersion                     | 텍스트          | 이 필드는 스키마의 현재 버전을 나타냅니다. 이는 **V2** 입니다. |
| SecondaryBackupProtectionState    | 텍스트          | 백업 항목에 대해 보조 보호를 사용하도록 설정할지 여부  |
| 시스템 상태                             | 텍스트          | 백업 항목 개체 상태. 예: 활성, 삭제됨 |
| StorageReplicationType            | 텍스트          | 자격 증명 모음에 대한 스토리지 복제 유형. 예: GeoRedundant |
| SubscriptionId                    | 텍스트          | 데이터가 수집되는 리소스의 구독 식별자(예: Recovery Services 자격 증명 모음) |
| VaultName                         | 텍스트          | 자격 증명 모음의 이름                                            |
| VaultTags                         | 텍스트          | 자격 증명 모음 리소스에 연결된 태그                    |
| VaultUniqueId                     | 텍스트          | 자격 증명 모음의 고유 식별자                             |
| SourceSystem                      | 텍스트          | 현재 데이터의 원본 시스템 - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

이 표에서는 경고 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 텍스트          | 데이터가 수집되는 리소스의 고유 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| OperationName                  | 텍스트          | 현재 작업의 이름. 예: 경고            |
| Category                       | 텍스트          | Azure Monitor 로그에 푸시된 진단 데이터의 범주 - AddonAzureBackupAlerts |
| AlertCode                      | 텍스트          | 경고 유형을 고유하게 식별하는 코드                     |
| AlertConsolidationStatus       | 텍스트          | 경고가 통합 경고인지 여부 식별         |
| AlertOccurrenceDateTime        | DateTime      | 경고가 생성된 날짜 및 시간                     |
| AlertRaisedOn                  | 텍스트          | 경고가 발생하는 엔터티의 유형                        |
| AlertSeverity                  | 텍스트          | 경고의 심각도입니다. 예: 위험                 |
| AlertStatus                    | 텍스트          | 경고의 상태. 예: 활성                     |
| AlertTimeToResolveInMinutes    | Number        | 경고를 해결하는 데 걸린 시간입니다. 활성 경고의 경우 비어 있습니다.     |
| AlertType                      | 텍스트          | 경고의 유형. 예: Backup                           |
| AlertUniqueId                  | 텍스트          | 생성된 경고의 고유 식별자                    |
| BackupItemUniqueId             | 텍스트          | 경고와 관련된 백업 항목의 고유 식별자 |
| BackupManagementServerUniqueId | 텍스트          | 해당하는 경우 백업 항목을 보호하는 백업 관리 서버를 고유하게 식별하는 필드 |
| BackupManagementType           | 텍스트          | 백업 작업을 수행하는 서버의 공급자 유형(예: IaaSVM, FileFolder) |
| CountOfAlertsConsolidated      | 숫자        | 통합된 경고인 경우 통합된 경고 수  |
| ProtectedContainerUniqueId     | 텍스트          | 경고와 관련된 보호된 서버의 고유 식별자 |
| RecommendedAction              | 텍스트          | 경고를 해결하기 위한 권장 작업                      |
| schemaVersion                  | 텍스트          | 스키마의 현재 버전(예: **V2**)            |
| 시스템 상태                          | 텍스트          | 경고 개체의 현재 상태(예: Active, Deleted) |
| StorageUniqueId                | 텍스트          | 스토리지 엔터티를 식별하는 데 사용되는 고유 ID                |
| VaultUniqueId                  | 텍스트          | 경고와 관련하여 자격 증명 모음을 식별하는 데 사용되는 고유 ID    |
| SourceSystem                   | 텍스트          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

이 표에서는 보호된 기본 인스턴스 관련 필드를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 텍스트          | 데이터가 수집되는 리소스의 고유 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| OperationName                  | 텍스트          | 작업 이름(예: ProtectedInstance)         |
| Category                       | 텍스트          | Azure Monitor 로그에 푸시된 진단 데이터의 범주 -   AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | 텍스트          | 백업 항목의 고유 ID                                 |
| BackupManagementServerUniqueId | 텍스트          | 해당하는 경우 백업 항목을 보호하는 백업 관리 서버를 고유하게 식별하는 필드 |
| BackupManagementType           | 텍스트          | 백업 작업을 수행하는 서버의 공급자 유형(예: IaaSVM, FileFolder) |
| ProtectedContainerUniqueId     | 텍스트          | 작업이 실행되는 보호 컨테이너를 식별하기 위한 고유 ID |
| ProtectedInstanceCount         | 텍스트          | 해당 날짜/시간에 연결된 백업 항목 또는 보호된 컨테이너에 대한 보호된 인스턴스 수 |
| schemaVersion                  | 텍스트          | 스키마의 현재 버전(예: **V2**)            |
| 시스템 상태                          | 텍스트          | 백업 항목 개체의 상태(예: 활성, 삭제됨) |
| VaultUniqueId                  | 텍스트          | 보호된 인스턴스와 관련된 보호된 자격 증명 모음의 고유 식별자 |
| SourceSystem                   | 텍스트          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

이 표에서는 작업 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 텍스트          | 수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| OperationName                  | 텍스트          | 현재 작업의 이름(Job)을 나타냅니다.    |
| Category                       | 텍스트          | 이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주 AddonAzureBackupJobs를 나타냅니다. |
| AdhocOrScheduledJob            | 텍스트          | 작업이 임시 작업인지 또는 예약된 작업인지 지정하는 필드           |
| BackupItemUniqueId             | 텍스트          | 스토리지 엔터티와 관련된 백업 항목을 식별하는 데 사용되는 고유 ID |
| BackupManagementServerUniqueId | 텍스트          | 스토리지 엔터티와 관련된 백업 관리 서버를 식별하는 데 사용되는 고유 ID |
| BackupManagementType           | 텍스트          | 백업 수행에 대한 공급자 유형(예: IaaSVM, 이 작업이 속한 FileFolder) |
| DataTransferredInMB            | Number        | 이 작업에 대해 전송되는 데이터 크기(MB 단위)                          |
| JobDurationInSecs              | Number        | 총 작업 기간(초)                                |
| JobFailureCode                 | 텍스트          | 발생한 작업 실패로 인한 오류 코드 문자열    |
| JobOperation                   | 텍스트          | 실행되는 작업에 대한 동작(예: 백업, 복원, 백업 구성) |
| JobOperationSubType            | 텍스트          | 작업의 하위 유형. 예: 로그 백업 작업의 경우 ‘Log’ |
| JobStartDateTime               | DateTime      | 작업 실행이 시작된 날짜 및 시간                       |
| JobStatus                      | 텍스트          | 완료된 작업의 상태(예: Completed, Failed)   |
| JobUniqueId                    | 텍스트          | 작업을 식별하는 고유 ID                                |
| ProtectedContainerUniqueId     | 텍스트          | 작업과 관련된 보호된 서버의 고유 식별자 |
| RecoveryJobDestination         | 텍스트          | 데이터를 복구하는 복구 작업의 대상   |
| RecoveryJobRPDateTime          | DateTime      | 복구 중인 복구 지점이 생성된 날짜, 시간 |
| RecoveryJobLocation            | 텍스트          | 복구 중인 복구 지점이 저장된 위치 |
| RecoveryLocationType           | 텍스트          | 복구 위치의 유형                                |
| schemaVersion                  | 텍스트          | 스키마의 현재 버전(예: **V2**)            |
| 시스템 상태                          | 텍스트          | 작업 개체의 현재 상태(예: 활성, 삭제됨) |
| VaultUniqueId                  | 텍스트          | 작업 관련된 보호된 자격 증명 모음의 고유 식별자 |
| SourceSystem                   | 텍스트          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

이 표에서는 정책 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                       | **데이터 형식**  | **설명**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | 텍스트           | 데이터가 수집되는 리소스의 고유 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| OperationName                   | 텍스트           | 작업 이름(예: Policy 또는 PolicyAssociation) |
| Category                        | 텍스트           | Azure Monitor 로그에 푸시된 진단 데이터의 범주 - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | 텍스트           | 백업이 예약된 주의 요일            |
| BackupFrequency                 | 텍스트           | 백업이 실행되는 빈도. 예: 매일, 매주 |
| BackupManagementType            | 텍스트           | 백업 작업을 수행하는 서버의 공급자 유형. 예: IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | 텍스트           | 해당하는 경우 백업 항목을 보호하는 백업 관리 서버를 고유하게 식별하는 필드 |
| BackupTimes                     | 텍스트           | 백업이 예약된 날짜 및 시간                     |
| DailyRetentionDuration          | 정수   | 구성된 백업에 대한 총 보존 기간(일)      |
| DailyRetentionTimes             | 텍스트           | 매일 보존이 구성된 날짜 및 시간            |
| DiffBackupDaysOfTheWeek         | 텍스트           | Azure VM 백업에서 SQL에 대한 차등 백업의 요일 |
| DiffBackupFormat                | 텍스트           | Azure VM 백업에서 SQL에 대한 차등 백업의 형식   |
| DiffBackupRetentionDuration     | 10진수 | Azure VM 백업에서 SQL에 대한 차등 백업의 보존 기간 |
| DiffBackupTime                  | 시간           | Azure VM 백업에서 SQL에 대한 차등 백업 시간     |
| LogBackupFrequency              | 10진수 | SQL에 대한 로그 백업의 빈도                            |
| LogBackupRetentionDuration      | 10진수 | Azure VM 백업에서 SQL에 대한 로그 백업의 보존 기간 |
| MonthlyRetentionDaysOfTheMonth  | 텍스트           | 월별 보존이 구성된 경우 월의 주  예: 첫째 주, 마지막 주 |
| MonthlyRetentionDaysOfTheWeek   | 텍스트           | 매월 보존에 대해 선택한 주의 요일              |
| MonthlyRetentionDuration        | 텍스트           | 구성된 백업에 대한 총 보존 기간(월)    |
| MonthlyRetentionFormat          | 텍스트           | 월별 보존에 대한 구성 유형. 예: 일별 기준 매일, 주별 기준 매주 |
| MonthlyRetentionTimes           | 텍스트           | 매월 보존이 구성된 날짜 및 시간           |
| MonthlyRetentionWeeksOfTheMonth | 텍스트           | 월별 보존이 구성된 경우 월의 주   예: 첫째 주, 마지막 주 |
| PolicyName                      | 텍스트           | 지정된 정책의 이름                                   |
| PolicyUniqueId                  | 텍스트           | 정책을 식별하는 고유 ID                             |
| PolicyTimeZone                  | 텍스트           | 로그에서 정책 시간 필드가 지정되는 표준 시간대 |
| RetentionDuration               | 텍스트           | 구성된 백업에 대한 보존 기간                    |
| RetentionType                   | 텍스트           | 보존 유형                                            |
| schemaVersion                   | 텍스트           | 이 필드는 스키마의 현재 버전을 나타내며, **V2** 입니다. |
| 시스템 상태                           | 텍스트           | 정책 개체의 현재 상태. 예: 활성, 삭제됨 |
| SynchronisationFrequencyPerDay  | 정수   | SC DPM 및 MABS에 대해 하루 중 파일 백업이 동기화된 수 |
| VaultUniqueId                   | 텍스트           | 이 정책이 속한 자격 증명 모음의 고유 ID          |
| WeeklyRetentionDaysOfTheWeek    | 텍스트           | 매주 보존에 대해 선택한 주의 요일               |
| WeeklyRetentionDuration         | 10진수 | 구성된 백업의 총 매주 보존 기간 |
| WeeklyRetentionTimes            | 텍스트           | 매주 보존이 구성된 날짜 및 시간            |
| YearlyRetentionDaysOfTheMonth   | 텍스트           | 매년 보존에 대해 선택한 월의 날짜             |
| YearlyRetentionDaysOfTheWeek    | 텍스트           | 매년 보존에 대해 선택한 주의 요일               |
| YearlyRetentionDuration         | 10진수 | 구성된 백업에 대한 총 보존 기간(연)     |
| YearlyRetentionFormat           | 텍스트           | 연별 보존의 구성 형식(예: 일별 기준 매일, 주별 기준 매주) |
| YearlyRetentionMonthsOfTheYear  | 텍스트           | 매년 보존에 대해 선택한 연도의 달             |
| YearlyRetentionTimes            | 텍스트           | 매년 보존이 구성된 날짜 및 시간            |
| YearlyRetentionWeeksOfTheMonth  | 텍스트           | 연간 보존을 위해 선택한 월의 주             |
| SourceSystem                    | 텍스트           | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

이 표에서는 스토리지 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | 텍스트          | 수집되는 데이터에 대한 리소스 식별자(예: Recovery Services 자격 증명 모음 리소스 ID) |
| OperationName                  | 텍스트          | 이 필드는 현재 작업의 이름을 나타내며, Storage 또는 StorageAssociation입니다. |
| Category                       | 텍스트          | 이 필드는 Azure Monitor 로그에 푸시된 진단 데이터의 범주 AddonAzureBackupStorage입니다. |
| BackupItemUniqueId             | 텍스트          | DPM, MABS를 사용하여 백업된 VM의 백업 항목을 식별하는 데 사용되는 고유 ID |
| BackupManagementServerUniqueId | 텍스트          | 해당하는 경우 백업 항목을 보호하는 백업 관리 서버를 고유하게 식별하는 필드 |
| BackupManagementType           | 텍스트          | 백업 작업을 수행하는 서버의 공급자 유형. 예: IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | 텍스트          | 이 볼륨이 기본 스토리지인 워크로드      |
| ProtectedContainerUniqueId     | 텍스트          | 백업 항목과 관련된 보호된 컨테이너의 고유 식별자 |
| schemaVersion                  | 텍스트          | 스키마의 버전. 예: **V2**                   |
| 시스템 상태                          | 텍스트          | 백업 항목 개체 상태. 예: 활성, 삭제됨 |
| StorageAllocatedInMBs          | 숫자        | Disk 유형의 해당 스토리지에서 해당 백업 항목이 할당한 스토리지 크기 |
| StorageConsumedInMBs           | 숫자        | 해당 스토리지의 해당 백업 항목에서 사용하는 스토리지 크기 |
| StorageName                    | 텍스트          | 스토리지 엔터티 이름. 예: E:\                      |
| StorageTotalSizeInGBs          | 텍스트          | 스토리지 엔터티에서 사용한 총 스토리지 크기(GB)     |
| StorageType                    | 텍스트          | 스토리지 유형(예: 클라우드, 볼륨, 디스크)             |
| StorageUniqueId                | 텍스트          | 스토리지 엔터티를 식별하는 데 사용되는 고유 ID                |
| VaultUniqueId                  | 텍스트          | 스토리지 엔터티와 관련된 자격 증명 모음을 식별하는 데 사용되는 고유 ID |
| VolumeFriendlyName             | 텍스트          | 스토리지 볼륨의 식별 이름                          |
| SourceSystem                   | 텍스트          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="valid-operation-names-for-each-table"></a>각 테이블의 유효한 작업 이름

위 표의 각 레코드에는 연결된 **작업 이름** 이 있습니다. 작업 이름은 레코드 유형을 설명합니다(테이블에서 해당 레코드에 대해 채워지는 필드도 나타냄). 각 테이블(범주)은 하나 이상의 고유한 작업 이름을 지원합니다. 다음은 위의 각 테이블에 대해 지원되는 작업 이름을 요약한 것입니다.

| **테이블 이름/범주**                   | **지원되는 작업 이름** | **설명**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | ID, 이름, 유형 등의 지정된 백업 항목에 대한 모든 세부 정보를 포함하는 레코드를 나타냅니다. |
| CoreAzureBackup | BackupItemAssociation | 백업 항목 및 관련 보호된 컨테이너 간의 매핑을 나타냅니다(해당하는 경우). |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | 백업 항목과 해당 프런트 엔드 크기 사이의 매핑을 나타냅니다. |
| CoreAzureBackup | ProtectedContainer | ID, 이름, 유형 등의 지정된 보호된 컨테이너 대한 모든 세부 정보를 포함하는 레코드를 나타냅니다. |
| CoreAzureBackup | ProtectedContainerAssociation | 보호된 컨테이너와 해당 백업에 사용되는 자격 증명 모음 간의 매핑을 나타냅니다. |
| CoreAzureBackup | Vault | 지정된 자격 증명 모음의 모든 세부 정보를 포함하는 레코드를 나타냅니다. 예: ID, 이름, 태그, 위치 |
| CoreAzureBackup | 복구 지점 | 지정된 백업 항목에 대한 가장 오래된 복구 지점과 최신 복구 지점을 포함하는 레코드를 나타냅니다. |
| AddonAzureBackupJobs | 작업 |  지정된 작업의 모든 세부 정보를 포함하는 레코드를 나타냅니다. 예: 작업, 시작 시간, 상태 |
| AddonAzureBackupAlerts | 경고 | 지정된 경고의 모든 세부 정보를 포함하는 레코드를 나타냅니다. 예: 경고 생성 시간, 심각도, 상태  |
| AddonAzureBackupStorage | 스토리지 | 지정된 스토리지 엔터티에 대한 모든 세부 정보를 포함하는 레코드를 나타냅니다. 예: 스토리지 이름, 유형 |
| AddonAzureBackupStorage | StorageAssociation | 백업 항목과 백업 항목에서 사용한 총 클라우드 스토리지 간의 매핑을 나타냅니다. |
| AddonAzureBackupProtectedInstance | ProtectedInstance | 각 컨테이너 또는 백업 항목에 대한 보호된 인스턴스 수를 포함하는 레코드를 나타냅니다. Azure VM 백업의 경우 보호된 인스턴스 수는 백업 항목 수준에서 사용할 수 있으며, 다른 워크로드의 경우 보호된 컨테이너 수준에서 사용할 수 있습니다. |
| AddonAzureBackupPolicy | 정책 |  백업 및 보존 정책의 모든 세부 정보를 포함하는 레코드를 나타냅니다. 예: ID, 이름, 보존 설정 |
| AddonAzureBackupPolicy | PolicyAssociation | 백업 항목과 해당 백업 항목에 적용되는 백업 정책 간의 매핑을 나타냅니다. |   

분석에 필요한 모든 필드를 가져오려면 동일한 테이블(작업 이름으로 구분)의 일부인 다른 레코드 세트뿐만 아니라 서로 다른 테이블 간의 조인을 수행해야 하는 경우가 많습니다. 시작하려면 [샘플 쿼리](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Log Analytics로 진단 데이터를 보내는 방법](./backup-azure-diagnostic-events.md) 알아보기
- [리소스별 테이블에 쿼리를 작성하는 방법 알아보기](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)