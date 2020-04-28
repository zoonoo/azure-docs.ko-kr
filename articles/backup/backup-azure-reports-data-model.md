---
title: Azure Backup 진단 이벤트에 대 한 데이터 모델
description: 이 데이터 모델은 Log Analytics (LA)로 진단 이벤트를 보내는 리소스 특정 모드를 참조 합니다.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0713db1cee9d6737ce69cb108f3cb8f81d1eb2ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183571"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure Backup 진단 이벤트에 대 한 데이터 모델

## <a name="coreazurebackup"></a>CoreAzureBackup

이 표에서는 자격 증명 모음 및 백업 항목과 같은 핵심 백업 엔터티에 대 한 정보를 제공 합니다.

| **필드**                         | **데이터 형식** | **설명**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | 수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 Recovery Services 자격 증명 모음 리소스 ID입니다. |
| OperationName                     | Text          | 이 필드는 현재 작업의 이름 (BackupItem, BackupItemAssociation 또는 ProtectedContainer)을 나타냅니다. |
| 범주                          | Text          | 이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. 예를 들면 CoreAzureBackup입니다. |
| AgentVersion                      | Text          | 에이전트 백업 또는 보호 에이전트의 버전 번호 (SC DPM 및 MABS의 경우) |
| AzureBackupAgentVersion           | Text          | 백업 관리 서버의 Azure Backup 에이전트 버전 |
| AzureDataCenter                   | Text          | 자격 증명 모음이 위치한 데이터 센터                       |
| BackupItemAppVersion              | Text          | 백업 항목의 응용 프로그램 버전                       |
| BackupItemFriendlyName            | Text          | 백업 항목의 이름입니다.                             |
| BackupItemName                    | Text          | 백업 항목의 이름입니다.                                      |
| BackupItemProtectionState         | Text          | 백업 항목의 보호 상태                          |
| BackupItemFrontEndSize            | Text          | 백업 항목의 프런트 엔드 크기                            |
| BackupItemType                    | Text          | 백업 항목의 유형입니다. 예: VM, FileFolder             |
| BackupItemUniqueId                | Text          | 백업 항목의 고유 식별자                         |
| BackupManagementServerType        | Text          | MABS, SC DPM에서와 같이 백업 관리 서버의 유형     |
| BackupManagementServerUniqueId    | Text          | 백업 관리 서버를 고유 하 게 식별 하는 필드      |
| BackupManagementType              | Text          | 백업 작업을 수행 하는 서버에 대 한 공급자 유형입니다. 예: 예 iaasvm, FileFolder |
| BackupManagementServerName        | Text          | 백업 관리 서버의 이름                         |
| BackupManagementServerOSVersion   | Text          | 백업 관리 서버의 OS 버전                   |
| BackupManagementServerVersion     | Text          | 백업 관리 서버의 버전                      |
| LatestRecoveryPointLocation       | Text          | 백업 항목에 대 한 최신 복구 지점의 위치    |
| LatestRecoveryPointTime           | DateTime      | 백업 항목에 대 한 최신 복구 지점의 날짜 시간   |
| OldestRecoveryPointLocation       | Text          | 백업 항목에 대 한 가장 오래 된 복구 지점의 위치    |
| OldestRecoveryPointTime           | DateTime      | 백업 항목에 대 한 최신 복구 지점의 날짜 시간   |
| PolicyUniqueId                    | Text          | 정책을 식별 하는 고유 ID                             |
| ProtectedContainerFriendlyName    | Text          | 보호 된 서버의 이름                        |
| ProtectedContainerLocation        | Text          | 보호 된 컨테이너가 온-프레미스 또는 Azure에 있는지 여부 |
| ProtectedContainerName            | Text          | 보호 된 컨테이너의 이름입니다.                            |
| ProtectedContainerOSType          | Text          | 보호 된 컨테이너의 OS 유형                          |
| ProtectedContainerOSVersion       | Text          | 보호 된 컨테이너의 OS 버전                        |
| ProtectedContainerProtectionState | Text          | 보호 된 컨테이너의 보호 상태                  |
| ProtectedContainerType            | Text          | 보호 된 컨테이너가 서버 인지 아니면 컨테이너 인지  |
| ProtectedContainerUniqueId        | Text          | DPM, MABS를 사용 하 여 백업한 Vm을 제외한 모든 항목에 대해 보호 된 컨테이너를 식별 하는 데 사용 되는 고유 ID |
| ProtectedContainerWorkloadType    | Text          | 백업 된 보호 된 컨테이너의 유형입니다. 예: IaaSVMContainer |
| ProtectionGroupName               | Text          | 해당 하는 경우 백업 항목이 보호 되는 보호 그룹 이름, SC DPM 및 MABS (해당 하는 경우) |
| ResourceGroupName                 | Text          | 수집 되는 데이터에 대 한 리소스의 리소스 그룹 (예: Recovery Services 자격 증명 모음) |
| schemaVersion                     | Text          | 이 필드는 스키마의 현재 버전을 나타내며, **V2** 입니다. |
| SecondaryBackupProtectionState    | Text          | 백업 항목에 대해 보조 보호를 사용 하도록 설정할지 여부  |
| 시스템 상태                             | Text          | 백업 항목 개체의 상태입니다. 예: 활성, 삭제 됨 |
| StorageReplicationType            | Text          | 자격 증명 모음에 대 한 저장소 복제 유형입니다. 예: GeoRedundant |
| SubscriptionId                    | Text          | 데이터가 수집 되는 리소스의 구독 식별자 (예: Recovery Services 자격 증명 모음) |
| VaultName                         | Text          | 자격 증명 모음의 이름                                            |
| VaultTags                         | Text          | 자격 증명 모음 리소스와 연결 된 태그                    |
| VaultUniqueId                     | Text          | 자격 증명 모음에 대 한 고유 식별자                             |
| SourceSystem                      | Text          | 현재 데이터의 원본 시스템-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

이 표에서는 경고 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 데이터가 수집되는 리소스의 고유 식별자(예: 예를 들어 Recovery Services 자격 증명 모음 리소스 ID |
| OperationName                  | Text          | 현재 작업의 이름입니다. 예: 경고            |
| 범주                       | Text          | Azure Monitor 로그에 푸시되는 진단 데이터의 범주-AddonAzureBackupAlerts |
| AlertCode                      | Text          | 경고 유형을 고유 하 게 식별 하는 코드                     |
| AlertConsolidationStatus       | Text          | 경고가 통합 경고 인지 여부를 확인 합니다.         |
| AlertOccurrenceDateTime        | DateTime      | 경고가 생성 된 날짜 및 시간                     |
| AlertRaisedOn                  | Text          | 경고가 발생 하는 엔터티의 유형입니다.                        |
| AlertSeverity                  | Text          | 경고의 심각도입니다. 예: 위험                 |
| AlertStatus                    | Text          | 경고의 상태입니다. 예: 활성                     |
| AlertTimeToResolveInMinutes    | 숫자        | 경고를 해결 하는 데 걸린 시간입니다. 활성 경고의 경우 비어 있습니다.     |
| AlertType                      | Text          | 경고의 유형입니다. 예: Backup                           |
| AlertUniqueId                  | Text          | 생성 된 경고의 고유 식별자                    |
| BackupItemUniqueId             | Text          | 경고와 연결 된 백업 항목의 고유 식별자 |
| BackupManagementServerUniqueId | Text          | 해당 하는 경우 백업 항목을 보호 하는 백업 관리 서버를 고유 하 게 식별 하는 필드입니다. |
| BackupManagementType           | Text          | 백업 작업을 수행 하는 서버에 대 한 공급자 유형 (예: 예 iaasvm, FileFolder) |
| CountOfAlertsConsolidated      | 숫자        | 통합 된 경고 인 경우 통합 된 경고 수  |
| ProtectedContainerUniqueId     | Text          | 경고와 연결 된 보호 된 서버의 고유 식별자입니다. |
| RecommendedAction              | Text          | 경고를 해결 하기 위한 권장 작업                      |
| schemaVersion                  | Text          | 스키마의 현재 버전 (예: **V2** )            |
| 시스템 상태                          | Text          | 경고 개체의 현재 상태(예: Active, Deleted) |
| StorageUniqueId                | Text          | 저장소 엔터티를 식별 하는 데 사용 되는 고유 ID                |
| VaultUniqueId                  | Text          | 경고와 관련 된 자격 증명 모음을 식별 하는 데 사용 되는 고유 ID입니다.    |
| SourceSystem                   | Text          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

이 표에서는 보호 된 기본 인스턴스 관련 필드를 제공 합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 수집 되는 데이터에 대 한 리소스의 고유 식별자입니다. 예를 들어 Recovery Services 자격 증명 모음 리소스 ID |
| OperationName                  | Text          | 작업 이름 (예: ProtectedInstance)         |
| 범주                       | Text          | Azure Monitor 로그에 푸시되는 진단 데이터의 범주-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | 백업 항목의 고유 ID                                 |
| BackupManagementServerUniqueId | Text          | 해당 하는 경우 백업 항목을 보호 하는 백업 관리 서버를 고유 하 게 식별 하는 필드입니다. |
| BackupManagementType           | Text          | 백업 작업을 수행 하는 서버에 대 한 공급자 유형 (예: 예 iaasvm, FileFolder) |
| ProtectedContainerUniqueId     | Text          | 작업이 실행 되는 보호 된 컨테이너를 식별 하기 위한 고유 ID |
| ProtectedInstanceCount         | Text          | 해당 날짜/시간에 연결 된 백업 항목 또는 보호 된 컨테이너에 대 한 보호 된 인스턴스 수 |
| schemaVersion                  | Text          | 스키마의 현재 버전 (예: **V2** )            |
| 시스템 상태                          | Text          | 백업 항목 개체의 상태 (예: Active, Deleted) |
| VaultUniqueId                  | Text          | 보호 된 인스턴스와 연결 된 보호 된 자격 증명 모음의 고유 식별자 |
| SourceSystem                   | Text          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

이 표에서는 작업 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 Recovery Services 자격 증명 모음 리소스 ID |
| OperationName                  | Text          | 현재 작업의 이름(Job)을 나타냅니다.    |
| 범주                       | Text          | 이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | 작업이 임시 작업 인지 또는 예약 된 작업 인지 지정 하는 필드           |
| BackupItemUniqueId             | Text          | 저장소 엔터티와 관련 된 백업 항목을 식별 하는 데 사용 되는 고유 ID입니다. |
| BackupManagementServerUniqueId | Text          | 저장소 엔터티와 관련 된 백업 관리 서버를 식별 하는 데 사용 되는 고유 ID입니다. |
| BackupManagementType           | Text          | 백업을 수행 하기 위한 공급자 유형 (예:이 경고가 속한 예 iaasvm, FileFolder) |
| DataTransferredInMB            | 숫자        | 이 작업에 대해 전송되는 데이터 크기(MB 단위)                          |
| JobDurationInSecs              | 숫자        | 총 작업 기간(초)                                |
| JobFailureCode                 | Text          | 발생한 작업 실패로 인한 오류 코드 문자열    |
| JobOperation                   | Text          | 작업을 실행 하는 작업 (예: 백업, 복원, 백업 구성) |
| JobOperationSubType            | Text          | 작업 작업의 하위 유형입니다. 예: 로그 백업 작업의 경우 ' Log ' |
| JobStartDateTime               | DateTime      | 작업 실행이 시작된 날짜 및 시간                       |
| JobStatus                      | Text          | 완료된 작업의 상태(예: Completed, Failed)   |
| JobUniqueId                    | Text          | 작업을 식별 하기 위한 고유 ID                                |
| ProtectedContainerUniqueId     | Text          | 경고와 연결 된 보호 된 서버의 고유 식별자입니다. |
| RecoveryJobDestination         | Text          | 데이터를 복구 하는 복구 작업의 대상입니다.   |
| RecoveryJobRPDateTime          | DateTime      | 복구 지점이 생성 된 날짜, 시간 |
| RecoveryJobLocation            | Text          | 복구 중인 복구 지점이 저장 된 위치입니다. |
| RecoveryLocationType           | Text          | 복구 위치의 유형입니다.                                |
| schemaVersion                  | Text          | 스키마의 현재 버전 (예: **V2** )            |
| 시스템 상태                          | Text          | 경고 개체의 현재 상태 (예: Active, Deleted) |
| VaultUniqueId                  | Text          | 경고와 연결 된 보호 된 자격 증명 모음의 고유 식별자 |
| SourceSystem                   | Text          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

이 표에서는 정책 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                       | **데이터 형식**  | **설명**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | 수집 되는 데이터에 대 한 리소스의 고유 식별자입니다. 예를 들어 Recovery Services 자격 증명 모음 리소스 ID |
| OperationName                   | Text           | 작업 이름 (예: Policy 또는 PolicyAssociation) |
| 범주                        | Text           | Azure Monitor 로그에 푸시되는 진단 데이터의 범주-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | 백업이 예약된 주의 요일            |
| BackupFrequency                 | Text           | 백업이 실행 되는 빈도입니다. 예: 매일, 매주 |
| BackupManagementType            | Text           | 백업 작업을 수행 하는 서버에 대 한 공급자 유형입니다. 예: 예 iaasvm, FileFolder |
| BackupManagementServerUniqueId  | Text           | 해당 하는 경우 백업 항목을 보호 하는 백업 관리 서버를 고유 하 게 식별 하는 필드입니다. |
| BackupTimes                     | Text           | 백업이 예약된 날짜 및 시간                     |
| DailyRetentionDuration          | 정수   | 구성된 백업에 대한 총 보존 기간(일)      |
| DailyRetentionTimes             | Text           | 매일 보존이 구성된 날짜 및 시간            |
| DiffBackupDaysOfTheWeek         | Text           | Azure VM 백업에서 SQL에 대 한 차등 백업의 요일 |
| DiffBackupFormat                | Text           | Azure VM 백업에서 SQL에 대 한 차등 백업의 형식   |
| Diffbackup보존 기간     | 10진수 | Azure VM 백업에서 SQL에 대 한 차등 백업의 보존 기간 |
| DiffBackupTime 시간                  | 시간           | Azure VM 백업에서 SQL에 대 한 차등 백업 시간     |
| LogBackupFrequency              | 10진수 | SQL에 대 한 로그 백업 빈도                            |
| Logbackup보존 기간      | 10진수 | Azure VM 백업에서 SQL에 대 한 로그 백업의 보존 기간 |
| MonthlyRetentionDaysOfTheMonth  | Text           | 매월 보존이 구성 된 월의 주입니다.  예를 들어, First, Last 등입니다. |
| MonthlyRetentionDaysOfTheWeek   | Text           | 매월 보존에 대해 선택한 주의 요일              |
| MonthlyRetentionDuration        | Text           | 구성된 백업에 대한 총 보존 기간(월)    |
| MonthlyRetentionFormat          | Text           | 월별 보존에 대 한 구성 유형입니다. 예를 들어 매일, 매주 기준으로 매주 |
| MonthlyRetentionTimes           | Text           | 매월 보존이 구성된 날짜 및 시간           |
| MonthlyRetentionWeeksOfTheMonth | Text           | 매월 보존이 구성 된 월의 주입니다.   예를 들어, First, Last 등입니다. |
| PolicyName                      | Text           | 지정된 정책의 이름                                   |
| PolicyUniqueId                  | Text           | 정책을 식별 하는 고유 ID                             |
| PolicyTimeZone                  | Text           | 로그에서 정책 시간 필드가 지정 되는 표준 시간대 |
| 보존 기간               | Text           | 구성된 백업에 대한 보존 기간                    |
| RetentionType                   | Text           | 보존 유형                                            |
| schemaVersion                   | Text           | 이 필드는 스키마의 현재 버전을 나타내며, **V2** 입니다. |
| 시스템 상태                           | Text           | 정책 개체의 현재 상태입니다. 예: 활성, 삭제 됨 |
| SynchronisationFrequencyPerDay  | 정수   | SC DPM 및 MABS에 대해 파일 백업이 동기화 된 하루 중 시간 수 |
| VaultUniqueId                   | Text           | 이 정책이 속한 자격 증명 모음의 고유 ID          |
| WeeklyRetentionDaysOfTheWeek    | Text           | 매주 보존에 대해 선택한 주의 요일               |
| WeeklyRetentionDuration         | 10진수 | 구성 된 백업에 대 한 총 주간 보존 기간 (주) |
| WeeklyRetentionTimes            | Text           | 매주 보존이 구성된 날짜 및 시간            |
| YearlyRetentionDaysOfTheMonth   | Text           | 매년 보존에 대해 선택한 월의 날짜             |
| YearlyRetentionDaysOfTheWeek    | Text           | 매년 보존에 대해 선택한 주의 요일               |
| YearlyRetentionDuration         | 10진수 | 구성된 백업에 대한 총 보존 기간(연)     |
| YearlyRetentionFormat           | Text           | 매년 보존에 대 한 구성 유형 (예: 일 기준 매일, 주 기준 매주) |
| YearlyRetentionMonthsOfTheYear  | Text           | 매년 보존에 대해 선택한 연도의 달             |
| YearlyRetentionTimes            | Text           | 매년 보존이 구성된 날짜 및 시간            |
| YearlyRetentionWeeksOfTheMonth  | Text           | 매년 보존에 대해 선택한 월의 주             |
| SourceSystem                    | Text           | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

이 표에서는 스토리지 관련 필드에 대한 세부 정보를 제공합니다.

| **필드**                      | **데이터 형식** | **설명**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 수집되는 데이터에 대한 리소스 식별자(예: 예를 들어 Recovery Services 자격 증명 모음 리소스 ID |
| OperationName                  | Text          | 이 필드는 현재 작업 (저장소 또는 StorageAssociation)의 이름을 나타냅니다. |
| 범주                       | Text          | 이 필드는 Azure Monitor 로그에 푸시되는 진단 데이터의 범주를 나타냅니다. AddonAzureBackupStorage |
| BackupItemUniqueId             | Text          | DPM, MABS를 사용 하 여 백업 된 Vm의 백업 항목을 식별 하는 데 사용 되는 고유 ID |
| BackupManagementServerUniqueId | Text          | 해당 하는 경우 백업 항목을 보호 하는 백업 관리 서버를 고유 하 게 식별 하는 필드입니다. |
| BackupManagementType           | Text          | 백업 작업을 수행 하는 서버에 대 한 공급자 유형입니다. 예: 예 iaasvm, FileFolder |
| PreferredWorkloadOnVolume      | Text          | 이 볼륨이 기본 설정 저장소 인 작업      |
| ProtectedContainerUniqueId     | Text          | 경고와 연결 된 보호 된 서버의 고유 식별자입니다. |
| schemaVersion                  | Text          | 스키마의 버전입니다. 예: **V2**                   |
| 시스템 상태                          | Text          | 백업 항목 개체의 상태입니다. 예: 활성, 삭제 됨 |
| StorageAllocatedInMBs          | 숫자        | 디스크 형식의 해당 저장소에 있는 해당 백업 항목에 의해 할당 된 저장소 크기 |
| StorageConsumedInMBs           | 숫자        | 해당 하는 저장소의 해당 백업 항목에서 사용 하는 저장소 크기 |
| StorageName                    | Text          | 저장소 엔터티의 이름입니다. 예: E:\                      |
| StorageTotalSizeInGBs          | Text          | 저장소 엔터티에서 사용한 총 저장소 크기 (GB)     |
| StorageType                    | Text          | 저장소 유형 (예: 클라우드, 볼륨, 디스크)             |
| StorageUniqueId                | Text          | 저장소 엔터티를 식별 하는 데 사용 되는 고유 ID                |
| VaultUniqueId                  | Text          | 저장소 엔터티와 관련 된 자격 증명 모음을 식별 하는 데 사용 되는 고유 ID입니다. |
| VolumeFriendlyName             | Text          | 저장소 볼륨의 식별 이름                          |
| SourceSystem                   | Text          | 현재 데이터의 원본 시스템(Azure)                    |

## <a name="next-steps"></a>다음 단계

- [Log Analytics에 진단 데이터를 보내는 방법에 대해 알아봅니다.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [리소스 관련 테이블에 대 한 쿼리를 작성 하는 방법 알아보기](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)
