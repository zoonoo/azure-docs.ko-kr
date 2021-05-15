---
title: Azure Monitor 로그의 시스템 함수
description: 시스템 함수를 사용하여 Azure Monitor 로그에 사용자 지정 쿼리 작성
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564911"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Azure Monitor 로그의 시스템 함수

Azure Backup은 LA(Log Analytics) 작업 영역에서 기본적으로 사용할 수 있는 시스템 함수 또는 솔루션 함수라고 하는 일련의 함수를 제공합니다.
 
함수는 LA의 [원시 Azure Backup 테이블](./backup-azure-reports-data-model.md)에 있는 데이터에서 작동하며 간단한 쿼리를 사용하여 모든 백업 관련 엔터티의 정보를 쉽게 검색하는 데 도움이 되는 형식 지정 데이터를 반환합니다. 사용자는 함수에 매개 변수를 전달하여 이러한 함수에서 반환되는 데이터를 필터링할 수 있습니다. 

아래 섹션에 설명된 대로 시스템 함수는 여러 가지 이점을 제공하므로 사용자 지정 보고서를 만들기 위해 LA 작업 영역에서 백업 데이터를 쿼리하는 데 시스템 함수를 사용하는 것이 좋습니다.

## <a name="benefits-of-using-system-functions"></a>시스템 함수 사용의 이점

* **간단한 쿼리**: 함수를 사용하면 쿼리에 필요한 조인 수를 줄일 수 있습니다. 기본적으로 함수는 쿼리 중인 엔터티(백업 인스턴스, 작업, 자격 증명 모음 등)와 관련된 모든 정보를 통합하는 '일반' 스키마를 반환합니다. 예를 들어 백업 항목 이름 및 연결된 컨테이너를 기준으로 성공한 백업 작업 목록을 가져와야 하는 경우 **_AzureBackup_getJobs()** 함수를 간단히 호출하면 각 작업에 대한 이 모든 정보가 제공됩니다. 반면에 원시 테이블을 직접 쿼리하면 [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) 테이블과 [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) 테이블 간에 여러 조인을 수행해야 합니다.

* **레거시 진단 이벤트에서 원활한 전환**: 시스템 함수를 사용하면 [레거시 진단 이벤트](./backup-azure-diagnostic-events.md#legacy-event)(AzureDiagnostics 모드의 AzureBackupReport)에서 [리소스 관련 이벤트](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users)로 원활하게 전환할 수 있습니다. Azure Backup에서 제공하는 모든 시스템 함수를 사용하면 함수에서 리소스 관련 테이블의 데이터만 쿼리해야 하는지 아니면 레거시 테이블과 리소스 관련 테이블(레코드 중복 제거) 모두에서 데이터를 쿼리해야 하는지를 선택할 수 있는 매개 변수를 지정할 수 있습니다.
    * 리소스 관련 테이블로 마이그레이션한 경우 함수에서 쿼리하는 레거시 테이블을 제외하도록 선택할 수 있습니다.
    * 현재 마이그레이션 프로세스를 진행 중이며 분석에 필요한 일부 데이터가 레거시 테이블에 있는 경우 레거시 테이블을 포함하도록 선택할 수 있습니다. 전환이 완료되고 레거시 테이블의 데이터가 더 이상 필요하지 않은 경우 쿼리에서 함수로 전달된 매개 변수 값을 업데이트하여 레거시 테이블을 제외할 수 있습니다.
    * 레거시 테이블만 사용하고 있는 경우에도 동일한 매개 변수를 통해 레거시 테이블을 포함하도록 선택하면 함수가 계속 작동합니다. 그러나 초기에 [리소스 관련 테이블로 전환](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace)하는 것이 좋습니다.

* **사용자 지정 쿼리 중단 가능성 감소**: 향후 보고 시나리오를 수용하기 위해 Azure Backup이 기본 LA 테이블의 스키마에 대한 개선 사항을 도입하는 경우 스키마 변경을 고려하도록 함수 정의가 업데이트될 수도 있습니다. 따라서 시스템 함수를 사용하여 사용자 지정 쿼리를 만드는 경우 테이블의 기본 스키마가 변경되더라도 쿼리는 중단되지 않습니다.

> [!NOTE]
> 시스템 함수는 Microsoft에서 유지 관리하며, 함수 정의는 사용자가 편집할 수 없습니다. 편집 가능한 함수가 필요한 경우 LA에서 [저장된 함수](../azure-monitor/logs/functions.md)를 만들 수 있습니다.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Azure Backup에서 제공하는 시스템 함수 유형

* **핵심 함수**: 백업 인스턴스, 자격 증명 모음, 정책, 작업, 청구 엔터티 등 주요 Azure Backup 엔터티를 쿼리하는 데 도움이 되는 함수입니다. 예를 들어 **_AzureBackup_getBackupInstances** 함수는 최근 완료된 날짜(UTC)를 기준으로 현재 환경에 있는 모든 백업 인스턴스의 목록을 반환합니다. 각 주요 함수에 대한 매개 변수 및 반환된 스키마는 이 문서의 아래에 요약되어 있습니다.

* **추세 함수**: 백업 관련 엔터티(예: 백업 인스턴스, 청구 그룹)에 대한 기록 레코드를 반환하고 엔터티와 관련된 주요 메트릭(예: 개수, 사용된 스토리지)에 대한 일별, 주별, 월별 추세 정보를 얻을 수 있는 함수입니다. 각 추세 함수에 대한 매개 변수 및 반환된 스키마는 이 문서의 아래에 요약되어 있습니다.

> [!NOTE]
> 현재 시스템 함수는 마지막으로 완료된 날짜(UTC)까지의 데이터를 반환합니다. 현재 날짜의 일부 시간에 대한 데이터는 반환되지 않습니다. 따라서 현재 날짜에 대한 레코드를 검색하려는 경우 원시 LA 테이블을 사용해야 합니다.


## <a name="list-of-system-functions"></a>시스템 함수 목록

### <a name="core-functions"></a>핵심 함수

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults()

Azure 환경에서 LA 작업 영역과 연결된 모든 Recovery Services 자격 증명 모음의 목록을 반환하는 함수입니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 자격 증명 모음 관련 레코드를 모두 가져와야 하는 경우에만 RangeEnd 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 자격 증명 모음에 대한 최신 레코드만 검색합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 자격 증명 모음 관련 레코드를 모두 가져와야 하는 경우에만 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 자격 증명 모음에 대한 최신 레코드만 검색합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 해당 자격 증명 모음만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 해당 자격 증명 모음만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    |특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 레코드만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 레코드를 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |

**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 자격 증명 모음 ID를 나타내는 기본 키 |
| Id | 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| 이름 | 자격 증명 모음의 이름 |
| SubscriptionId | 자격 증명 모음이 있는 구독의 ID |
| Location | 자격 증명 모음이 있는 위치 |
| VaultStore_StorageReplicationType | 자격 증명 모음과 연결된 스토리지 복제 유형 |
| 태그들 | 자격 증명 모음의 태그 |
| TimeGenerated | 레코드 타임스탬프 |
| 유형 |  자격 증명 모음 유형("Microsoft.RecoveryServices/vaults")|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies()

데이터 원본 유형, 스토리지 복제 유형 등의 각 정책에 대한 세부 정보와 함께 Azure 환경에서 사용 중인 백업 정책 목록을 반환하는 함수입니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 정책 관련 레코드를 모두 가져와야 하는 경우에만 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 정책에 대한 최신 레코드만 검색합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 정책 관련 레코드를 모두 가져와야 하는 경우에만 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 정책에 대한 최신 레코드만 검색합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 해당 정책만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 해당 정책만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    |특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 정책 레코드만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 정책 레코드를 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |
| BackupSolutionList | Azure 환경에서 사용되는 특정 백업 솔루션 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들어 이 매개 변수 값으로 "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM"을 지정하면 함수가 Azure Backup에 Azure Virtual Machine 백업, Azure VM 백업의 SQL 또는 DPM을 사용하여 백업된 항목과 관련된 레코드만 반환합니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 백업 보고서에서 지원하는 모든 백업 솔루션과 관련된 레코드를 반환합니다(지원되는 값: "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage(Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" 또는 쉼표로 구분된 이러한 값의 조합). | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |

**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 정책 ID를 나타내는 기본 키 |
| Id | 정책의 ARM(Azure Resource Manager) ID |
| 이름 | 정책 이름 |
| 백업 솔루션 | 정책이 연결된 백업 솔루션. 예: Azure VM 백업, Azure VM 백업의 SQL 등 |
| TimeGenerated | 레코드 타임스탬프 |
| VaultUniqueId | 정책과 연결된 자격 증명 모음을 참조하는 외래 키 |
| VaultResourceId | 정책과 연결된 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| VaultName | 정책과 연결된 자격 증명 모음의 이름 |
| VaultTags | 정책과 연결된 자격 증명 모음의 태그 |
| VaultLocation | 정책과 연결된 자격 증명 모음의 위치 |
| VaultSubscriptionId | 정책과 연결된 자격 증명 모음의 구독 ID |
| VaultStore_StorageReplicationType | 정책과 연결된 자격 증명 모음의 스토리지 복제 유형 |
| VaultType | 자격 증명 모음 유형("Microsoft.RecoveryServices/vaults") |
| ExtendedProperties | 정책의 추가 속성 |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs()

각 작업에 대한 세부 정보(예: 작업 상태, 작업 기간, 전송된 데이터 등)와 함께 지정된 시간 범위 내에 트리거된 모든 백업 및 복원 관련 작업 목록을 반환하는 함수입니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 기간에 시작된 모든 작업 목록을 검색해야 하는 경우 RangeEnd 매개 변수와 함께 이 매개 변수를 사용합니다. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 기간에 시작된 모든 작업 목록을 검색해야 하는 경우 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 자격 증명 모음과 연결된 해당 작업만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 자격 증명 모음과 연결된 해당 작업만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    | 특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 작업만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 작업을 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |
| BackupSolutionList | Azure 환경에서 사용되는 특정 백업 솔루션 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들어 이 매개 변수 값으로 "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM"을 지정하면 함수가 Azure Backup에 Azure Virtual Machine 백업, Azure VM 백업의 SQL 또는 DPM을 사용하여 백업된 항목과 관련된 레코드만 반환합니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 백업 보고서에서 지원하는 모든 백업 솔루션과 관련된 레코드를 반환합니다(지원되는 값: "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage(Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" 또는 쉼표로 구분된 이러한 값의 조합). | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| JobOperationList | 특정 작업 유형에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들면 백업 또는 복원 작업이 있습니다. 기본적으로 이 매개 변수의 값은 “*”입니다. 이 값을 사용하면 함수가 백업 작업과 복원 작업을 모두 검색합니다. | N | "Backup" | 
| JobStatusList | 특정 작업 상태에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들면 Completed, Failed 등이 있습니다. 기본적으로 이 매개 변수의 값은 "*"입니다. 이 값을 사용하면 상태에 관계없이 함수가 모든 작업을 검색합니다. | N | "Failed,CompletedWithWarnings" |
| JobFailureCodeList | 특정 오류 코드에 대한 함수 출력을 필터링하려는 경우 사용합니다. 기본적으로 이 매개 변수의 값은 "*"입니다. 이 값을 사용하면 오류 코드에 관계없이 함수가 모든 작업을 검색합니다. | N | "Success"
| DatasourceSetName | 함수 출력을 특정 부모 리소스로 필터링하려는 경우 사용합니다. 예를 들어 가상 머신 "testvm"에 속하는 Azure VM 백업 인스턴스에 SQL을 반환하려면 _testvm_ 을 이 매개 변수 값으로 지정합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 백업 인스턴스에서 레코드를 검색합니다. | N | "testvm" |
| BackupInstanceName | 특정 백업 인스턴스에서 이름을 기준으로 작업을 검색하려는 경우 사용합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 백업 인스턴스에서 레코드를 검색합니다. | N | "testvm" |
| ExcludeLog | 함수에서 반환되는 로그 작업을 제외하려는 경우 사용합니다(쿼리 성능에 도움이 됨). 기본적으로 이 매개 변수의 값은 true입니다. 이 값을 사용하면 함수가 로그 작업을 제외합니다. | N | true


**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 작업 ID를 나타내는 기본 키 |
| OperationCategory | 수행 중인 작업의 범주. 예: 백업 또는 복원 |
| 작업 | 수행 중인 작업의 세부 정보. 예: 로그(로그 백업용)|
| 상태 | 작업의 상태. 예: Completed, Failed, CompletedWithWarnings |
| ErrorTitle | 작업의 오류 코드 |
| StartTime | 작업이 시작된 날짜 및 시간 |
| DurationInSecs | 작업 기간(초) |
| DataTransferredInMBs | 작업별로 전송된 데이터(MB) |
| RestoreJobRPDateTime | 복구 중인 복구 지점이 생성된 날짜 및 시간 |
| RestoreJobRPLocation | 복구 중인 복구 지점이 저장된 위치 |
| BackupInstanceUniqueId | 작업과 연결된 백업 인스턴스를 참조하는 외래 키 |
| BackupInstanceId | 작업과 연결된 백업 인스턴스의 ARM(Azure Resource Manager) ID |
| BackupInstanceFriendlyName | 작업과 연결된 백업 인스턴스의 이름 |
| DatasourceResourceId | 작업과 연결된 기본 데이터 원본의 ARM(Azure Resource Manager) ID. 예: VM의 ARM(Azure Resource Manager) ID |
| DatasourceFriendlyName | 작업과 연결된 기본 데이터 원본의 식별 이름 |
| DatasourceType | 작업과 연결된 데이터 원본의 유형. 예: "Microsoft.Compute/virtualMachines" |
| BackupSolution | 작업이 연결된 백업 솔루션. 예: Azure VM 백업, Azure VM 백업의 SQL 등 |
| DatasourceSetResourceId | 데이터 원본의 부모 리소스에 대한 ARM(Azure Resource Manager) ID(해당하는 경우). 예: SQL이 Azure VM 데이터 원본에 있는 경우 이 필드에는 SQL Database가 있는 VM의 ARM(Azure Resource Manager) ID가 포함됨 |
| DatasourceSetType | 데이터 원본의 부모 리소스 유형(해당하는 경우). 예: SAP HANA가 Azure VM 데이터 원본에 있는 경우 부모 리소스가 Azure VM이므로 이 필드는 Microsoft.Compute/virtualMachines가 됨 |
| VaultResourceId | 작업과 연결된 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| VaultUniqueId | 작업과 연결된 자격 증명 모음을 참조하는 외래 키 |
| VaultName | 작업과 연결된 자격 증명 모음의 이름 |
| VaultTags | 작업과 연결된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 작업과 연결된 자격 증명 모음의 구독 ID |
| VaultLocation | 작업과 연결된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 작업과 연결된 자격 증명 모음의 스토리지 복제 유형 |
| VaultType | 자격 증명 모음 유형("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | 레코드 타임스탬프 |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances()

클라우드 스토리지 사용량, 연결된 정책 등과 같은 각 백업 인스턴스에 대한 자세한 정보와 함께 Recovery Services 자격 증명 모음과 연결된 백업 인스턴스 목록을 반환하는 함수입니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 백업 인스턴스 관련 레코드를 모두 가져와야 하는 경우에만 RangeEnd 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 백업 인스턴스에 대한 최신 레코드만 검색합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 백업 인스턴스 관련 레코드를 모두 가져와야 하는 경우에만 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 백업 인스턴스에 대한 최신 레코드만 검색합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 해당 백업 인스턴스만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 해당 백업 인스턴스만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    |특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 백업 인스턴스 레코드만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 백업 인스턴스 레코드를 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |
| BackupSolutionList | Azure 환경에서 사용되는 특정 백업 솔루션 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들어 이 매개 변수 값으로 "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM"을 지정하면 함수가 Azure Backup에 Azure Virtual Machine 백업, Azure VM 백업의 SQL 또는 DPM을 사용하여 백업된 항목과 관련된 레코드만 반환합니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 백업 보고서에서 지원하는 모든 백업 솔루션과 관련된 레코드를 반환합니다(지원되는 값: "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage(Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" 또는 쉼표로 구분된 이러한 값의 조합). | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| ProtectionInfoList | 적극적으로 보호되는 백업 인스턴스만 포함할지 아니면 보호가 중지된 인스턴스와 초기 백업이 보류 중인 인스턴스를 포함할지 여부를 선택하려는 경우 사용합니다. 지원되는 값은 "Protected", "ProtectionStopped", "InitialBackupPending" 또는 쉼표로 구분된 이러한 값의 조합입니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 보호 세부 사항에 관계없이 모든 백업 인스턴스를 검색합니다. | N | "Protected" |
| DatasourceSetName | 함수 출력을 특정 부모 리소스로 필터링하려는 경우 사용합니다. 예를 들어 가상 머신 "testvm"에 속하는 Azure VM 백업 인스턴스에 SQL을 반환하려면 _testvm_ 을 이 매개 변수 값으로 지정합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 백업 인스턴스에서 레코드를 검색합니다. | N | "testvm" |
| BackupInstanceName | 이름을 기준으로 특정 백업 인스턴스를 검색하려는 경우 사용합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 백업 인스턴스를 검색합니다. | N | "testvm" |
| DisplayAllFields | 함수에서 반환된 필드의 하위 세트만 검색할지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 함수 출력에서 스토리지 및 보존 지점 관련 정보를 제거합니다. 더 큰 쿼리에서 이 함수를 중간 단계로 사용하고 분석에 필요하지 않은 열을 제거하여 쿼리 성능을 최적화해야 하는 경우에 유용합니다. 기본적으로 이 매개 변수의 값은 true입니다. 이 경우 함수가 백업 인스턴스와 관련된 모든 필드를 반환합니다. | N | true |

**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 백업 인스턴스 ID를 나타내는 기본 키 |
| Id | 백업 인스턴스의 ARM(Azure Resource Manager) ID |
| FriendlyName | 백업 인스턴스의 식별 이름 |
| ProtectionInfo | 백업 인스턴스의 보호 설정에 대한 정보. 예: 보호 구성 됨, 보호 중지 됨, 초기 백업 보류 중 |
| LatestRecoveryPoint | 백업 인스턴스와 연결된 최신 복구 지점의 날짜 및 시간 |
| OldestRecoveryPoint | 백업 인스턴스와 연결된 가장 오래된 복구 지점의 날짜 및 시간 |
| SourceSizeInMBs | 백업 인스턴스의 프런트 엔드 크기(MB) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음-표준 계층의 백업 인스턴스에 사용된 총 클라우드 스토리지 |
| DataSourceFriendlyName | 백업 인스턴스에 해당하는 데이터 원본의 식별 이름 |
| BackupSolution | 백업 인스턴스가 연결된 백업 솔루션. 예: Azure VM 백업, Azure VM 백업의 SQL 등 |
| DatasourceType | 백업 인스턴스에 해당하는 데이터 원본의 유형. 예: "Microsoft.Compute/virtualMachines" |
| DatasourceResourceId | 백업 인스턴스에 해당하는 기본 데이터 원본의 ARM(Azure Resource Manager) ID. 예: VM의 ARM(Azure Resource Manager) ID |
| DatasourceSetFriendlyName | 데이터 원본의 부모 리소스에 대한 식별 이름(해당하는 경우). 예: SQL이 Azure VM 데이터 원본에 있는 경우 이 필드에는 SQL Database가 있는 VM의 이름이 포함됨 |
| DatasourceSetResourceId | 데이터 원본의 부모 리소스에 대한 ARM(Azure Resource Manager) ID(해당하는 경우). 예: SQL이 Azure VM 데이터 원본에 있는 경우 이 필드에는 SQL Database가 있는 VM의 ARM(Azure Resource Manager) ID가 포함됨 |
| DatasourceSetType | 데이터 원본의 부모 리소스 유형(해당하는 경우). 예: SAP HANA가 Azure VM 데이터 원본에 있는 경우 부모 리소스가 Azure VM이므로 이 필드는 Microsoft.Compute/virtualMachines가 됨 |
| PolicyName | 백업 인스턴스와 연결된 정책의 이름 |
| PolicyUniqueId | 백업 인스턴스와 연결된 정책을 참조하는 외래 키  |
| PolicyId | 백업 인스턴스와 연결된 정책의 ARM(Azure Resource Manager) ID |
| VaultResourceId | 백업 인스턴스와 연결된 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| VaultUniqueId | 백업 인스턴스와 연결된 자격 증명 모음을 참조하는 외래 키 |
| VaultName | 백업 인스턴스와 연결된 자격 증명 모음의 이름 |
| VaultTags | 백업 인스턴스와 연결된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 백업 인스턴스와 연결된 자격 증명 모음의 구독 ID |
| VaultLocation | 백업 인스턴스와 연결된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 백업 인스턴스와 연결된 자격 증명 모음의 스토리지 복제 유형 |
| VaultType | 자격 증명 모음 유형("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | 레코드 타임스탬프 |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups()

프런트 엔드 크기 및 총 클라우드 스토리지와 같은 주요 청구 구성 요소에 대한 정보와 함께 모든 백업 관련 청구 엔터티(청구 그룹) 목록을 반환하는 함수입니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 청구 그룹 관련 레코드를 모두 가져와야 하는 경우에만 RangeEnd 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 청구 그룹에 대한 최신 레코드만 검색합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 청구 그룹 관련 레코드를 모두 가져와야 하는 경우에만 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. 기본적으로 RangeStart 및 RangeEnd 값은 null입니다. 이 경우 함수가 각 청구 그룹에 대한 최신 레코드만 검색합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    |특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 백업 인스턴스 레코드만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 청구 그룹 레코드를 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |
| BackupSolutionList | Azure 환경에서 사용되는 특정 백업 솔루션 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들어 이 매개 변수 값으로 "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM"을 지정하면 함수가 Azure Backup에 Azure Virtual Machine 백업, Azure VM 백업의 SQL 또는 DPM을 사용하여 백업된 항목과 관련된 레코드만 반환합니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 백업 보고서에서 지원하는 모든 백업 솔루션과 관련된 레코드를 반환합니다(지원되는 값: "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage(Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" 또는 쉼표로 구분된 이러한 값의 조합). | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| BillingGroupName | 이름별로 특정 청구 그룹을 검색하려는 경우 사용합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 청구 그룹을 검색합니다. | N | "testvm" |

**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 청구 그룹 ID를 나타내는 기본 키 |
| FriendlyName | 청구 그룹의 식별 이름 |
| 이름 | 청구 그룹의 이름 |
| 유형 | 청구 그룹의 유형. 예: ProtectedContainer 또는 BackupItem |
| SourceSizeInMBs | 청구 그룹의 프런트 엔드 크기(MB) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음-표준 계층의 청구 그룹에 사용된 총 클라우드 스토리지 |
| BackupSolution | 청구 그룹이 연결된 백업 솔루션. 예: Azure VM 백업, Azure VM 백업의 SQL 등 |
| VaultResourceId | 청구 그룹과 연결된 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| VaultUniqueId | 청구 그룹과 연결된 자격 증명 모음을 참조하는 외래 키 |
| VaultName | 청구 그룹과 연결된 자격 증명 모음의 이름 |
| VaultTags | 청구 그룹과 연결된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 청구 그룹과 연결된 자격 증명 모음의 구독 ID |
| VaultLocation | 청구 그룹과 연결된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 청구 그룹과 연결된 자격 증명 모음의 스토리지 복제 유형 |
| VaultType | 자격 증명 모음 유형("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | 레코드 타임스탬프 |
| ExtendedProperties | 청구 그룹의 추가 속성 |

### <a name="trend-functions"></a>추세 함수

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends()

각 백업 인스턴스에 대한 기록 레코드를 반환하므로 여러 세분성 수준에서 백업 인스턴스 수 및 스토리지 사용량과 관련된 주요 일별, 주별, 월별 추세를 볼 수 있습니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 백업 인스턴스 관련 레코드를 모두 검색하려는 경우 RangeEnd 매개 변수와 함께 이 매개 변수를 사용합니다. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 백업 인스턴스 관련 레코드를 모두 검색하려는 경우 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 해당 백업 인스턴스만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 해당 백업 인스턴스만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    |특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 백업 인스턴스 레코드만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 백업 인스턴스 레코드를 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |
| BackupSolutionList | Azure 환경에서 사용되는 특정 백업 솔루션 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들어 이 매개 변수 값으로 "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM"을 지정하면 함수가 Azure Backup에 Azure Virtual Machine 백업, Azure VM 백업의 SQL 또는 DPM을 사용하여 백업된 항목과 관련된 레코드만 반환합니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 백업 보고서에서 지원하는 모든 백업 솔루션과 관련된 레코드를 반환합니다(지원되는 값: "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage(Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" 또는 쉼표로 구분된 이러한 값의 조합). | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| ProtectionInfoList | 적극적으로 보호되는 백업 인스턴스만 포함할지 아니면 보호가 중지된 인스턴스와 초기 백업이 보류 중인 인스턴스를 포함할지 여부를 선택하려는 경우 사용합니다. 지원되는 값은 "Protected", "ProtectionStopped", "InitialBackupPending" 또는 쉼표로 구분된 이러한 값의 조합입니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 보호 세부 사항에 관계없이 모든 백업 인스턴스를 검색합니다. | N | "Protected" |
| DatasourceSetName | 함수 출력을 특정 부모 리소스로 필터링하려는 경우 사용합니다. 예를 들어 가상 머신 "testvm"에 속하는 Azure VM 백업 인스턴스에 SQL을 반환하려면 _testvm_ 을 이 매개 변수 값으로 지정합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 백업 인스턴스에서 레코드를 검색합니다. | N | "testvm" |
| BackupInstanceName | 이름을 기준으로 특정 백업 인스턴스를 검색하려는 경우 사용합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 백업 인스턴스를 검색합니다. | N | "testvm" |
| DisplayAllFields | 함수에서 반환된 필드의 하위 세트만 검색할지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 함수 출력에서 스토리지 및 보존 지점 관련 정보를 제거합니다. 더 큰 쿼리에서 이 함수를 중간 단계로 사용하고 분석에 필요하지 않은 열을 제거하여 쿼리 성능을 최적화해야 하는 경우에 유용합니다. 기본적으로 이 매개 변수의 값은 true입니다. 이 경우 함수가 백업 인스턴스와 관련된 모든 필드를 반환합니다. | N | true |
| AggregationType | 데이터를 검색해야 하는 시간 세분성을 지정하려는 경우 사용합니다. 이 매개 변수 값이 "Daily"인 경우 함수가 일별로 백업 인스턴스당 한 개의 레코드를 반환하므로 스토리지 사용량과 백업 인스턴스 수의 일별 추세를 분석할 수 있습니다. 이 매개 변수 값이 "Weekly"인 경우 함수가 주별로 백업 인스턴스당 한 개의 레코드를 반환하므로 주별 추세를 분석할 수 있습니다. 마찬가지로 "Monthly"를 지정하여 월별 추세를 분석할 수 있습니다. 기본값은 "Daily"입니다. 더 큰 시간 범위에서 데이터를 보는 경우 더 나은 쿼리 성능과 용이한 추세 분석을 위해 "Weekly" 또는 "Monthly"를 사용하는 것이 좋습니다. | N | "Weekly" |

**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 백업 인스턴스 ID를 나타내는 기본 키 |
| Id | 백업 인스턴스의 ARM(Azure Resource Manager) ID |
| FriendlyName | 백업 인스턴스의 식별 이름 |
| ProtectionInfo | 백업 인스턴스의 보호 설정에 대한 정보. 예: 보호 구성 됨, 보호 중지 됨, 초기 백업 보류 중 |
| LatestRecoveryPoint | 백업 인스턴스와 연결된 최신 복구 지점의 날짜 및 시간 |
| OldestRecoveryPoint | 백업 인스턴스와 연결된 가장 오래된 복구 지점의 날짜 및 시간 |
| SourceSizeInMBs | 백업 인스턴스의 프런트 엔드 크기(MB) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음-표준 계층의 백업 인스턴스에 사용된 총 클라우드 스토리지 |
| DataSourceFriendlyName | 백업 인스턴스에 해당하는 데이터 원본의 식별 이름 |
| BackupSolution | 백업 인스턴스가 연결된 백업 솔루션. 예: Azure VM 백업, Azure VM 백업의 SQL 등 |
| DatasourceType | 백업 인스턴스에 해당하는 데이터 원본의 유형. 예: "Microsoft.Compute/virtualMachines" |
| DatasourceResourceId | 백업 인스턴스에 해당하는 기본 데이터 원본의 ARM(Azure Resource Manager) ID. 예: VM의 ARM(Azure Resource Manager) ID |
| DatasourceSetFriendlyName | 데이터 원본의 부모 리소스에 대한 식별 이름(해당하는 경우). 예: SQL이 Azure VM 데이터 원본에 있는 경우 이 필드에는 SQL Database가 있는 VM의 이름이 포함됨 |
| DatasourceSetResourceId | 데이터 원본의 부모 리소스에 대한 ARM(Azure Resource Manager) ID(해당하는 경우). 예: SQL이 Azure VM 데이터 원본에 있는 경우 이 필드에는 SQL Database가 있는 VM의 ARM(Azure Resource Manager) ID가 포함됨 |
| DatasourceSetType | 데이터 원본의 부모 리소스 유형(해당하는 경우). 예: SAP HANA가 Azure VM 데이터 원본에 있는 경우 부모 리소스가 Azure VM이므로 이 필드는 Microsoft.Compute/virtualMachines가 됨 |
| PolicyName | 백업 인스턴스와 연결된 정책의 이름 |
| PolicyUniqueId | 백업 인스턴스와 연결된 정책을 참조하는 외래 키  |
| PolicyId | 백업 인스턴스와 연결된 정책의 ARM(Azure Resource Manager) ID |
| VaultResourceId | 백업 인스턴스와 연결된 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| VaultUniqueId | 백업 인스턴스와 연결된 자격 증명 모음을 참조하는 외래 키 |
| VaultName | 백업 인스턴스와 연결된 자격 증명 모음의 이름 |
| VaultTags | 백업 인스턴스와 연결된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 백업 인스턴스와 연결된 자격 증명 모음의 구독 ID |
| VaultLocation | 백업 인스턴스와 연결된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 백업 인스턴스와 연결된 자격 증명 모음의 스토리지 복제 유형 |
| VaultType | 자격 증명 모음 유형("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | 레코드 타임스탬프 |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends()

각 청구 엔터티에 대한 기록 레코드를 반환하므로 여러 세분성 수준에서 프런트 엔드 크기 및 스토리지 사용량과 관련된 주요 일별, 주별, 월별 추세를 볼 수 있습니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | RangeStart부터 RangeEnd까지의 청구 그룹 관련 레코드를 모두 검색하려는 경우 RangeEnd 매개 변수와 함께 이 매개 변수를 사용합니다. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | RangeStart부터 RangeEnd까지의 청구 그룹 관련 레코드를 모두 검색하려는 경우 RangeStart 매개 변수와 함께 이 매개 변수를 사용합니다. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | 백업 데이터가 있는 특정 구독 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 구독 ID 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 구독에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 구독에서 레코드를 검색합니다. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 백업 데이터가 있는 특정 지역 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 지역 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 지역에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 지역에서 레코드를 검색합니다. | N | "eastus,westus"|
| VaultList    |특정 자격 증명 모음 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 쉼표로 구분된 자격 증명 모음 이름 목록을 이 함수에 대한 매개 변수로 지정하면 지정된 자격 증명 모음과 관련된 백업 인스턴스 레코드만 검색할 수 있습니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 함수가 모든 자격 증명 모음에서 청구 그룹 레코드를 검색합니다. | N |"vault1,vault2,vault3"|
| VaultTypeList     | 특정 자격 증명 모음 유형과 관련된 레코드로 함수 출력을 필터링하려는 경우 사용합니다. 현재 유일하게 지원되는 자격 증명 모음 유형은 이 매개 변수의 기본값인 "Microsoft.RecoveryServices/vaults"입니다. | N | "Microsoft.RecoveryServices/vaults"|
| ExcludeLegacyEvent  | 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택하려는 경우 사용합니다. 이 매개 변수 값이 false이면 함수가 AzureDiagnostics 테이블과 리소스 관련 테이블에서 모두 데이터를 쿼리합니다. 이 매개 변수 값이 true이면 함수가 리소스 관련 테이블에서만 데이터를 쿼리합니다. 기본값은 true입니다. | N | true |
| BackupSolutionList | Azure 환경에서 사용되는 특정 백업 솔루션 세트에 대한 함수 출력을 필터링하려는 경우 사용합니다. 예를 들어 이 매개 변수 값으로 "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM"을 지정하면 함수가 Azure Backup에 Azure Virtual Machine 백업, Azure VM 백업의 SQL 또는 DPM을 사용하여 백업된 항목과 관련된 레코드만 반환합니다. 기본적으로 이 매개 변수의 값은 '*'입니다. 이 값을 사용하면 백업 보고서에서 지원하는 모든 백업 솔루션과 관련된 레코드를 반환합니다(지원되는 값: "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage(Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" 또는 쉼표로 구분된 이러한 값의 조합). | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| BillingGroupName | 이름별로 특정 청구 그룹을 검색하려는 경우 사용합니다. 기본적으로 이 값은 "*"입니다. 이 값을 사용하면 함수가 모든 청구 그룹을 검색합니다. | N | "testvm" |
| AggregationType | 데이터를 검색해야 하는 시간 세분성을 지정하려는 경우 사용합니다. 이 매개 변수 값이 "Daily"인 경우 함수가 일별로 청구 그룹당 한 개의 레코드를 반환하므로 스토리지 사용량과 프런트 엔드 크기의 일별 추세를 분석할 수 있습니다. 이 매개 변수 값이 "Weekly"인 경우 함수가 주별로 백업 인스턴스당 한 개의 레코드를 반환하므로 주별 추세를 분석할 수 있습니다. 마찬가지로 "Monthly"를 지정하여 월별 추세를 분석할 수 있습니다. 기본값은 "Daily"입니다. 더 큰 시간 범위에서 데이터를 보는 경우 더 나은 쿼리 성능과 용이한 추세 분석을 위해 "Weekly" 또는 "Monthly"를 사용하는 것이 좋습니다. | N | "Weekly" |

**반환된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 고유한 청구 그룹 ID를 나타내는 기본 키 |
| FriendlyName | 청구 그룹의 식별 이름 |
| 이름 | 청구 그룹의 이름 |
| 유형 | 청구 그룹의 유형. 예: ProtectedContainer 또는 BackupItem |
| SourceSizeInMBs | 청구 그룹의 프런트 엔드 크기(MB) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음-표준 계층의 청구 그룹에 사용된 총 클라우드 스토리지 |
| BackupSolution | 청구 그룹이 연결된 백업 솔루션. 예: Azure VM 백업, Azure VM 백업의 SQL 등 |
| VaultResourceId | 청구 그룹과 연결된 자격 증명 모음의 ARM(Azure Resource Manager) ID |
| VaultUniqueId | 청구 그룹과 연결된 자격 증명 모음을 참조하는 외래 키 |
| VaultName | 청구 그룹과 연결된 자격 증명 모음의 이름 |
| VaultTags | 청구 그룹과 연결된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 청구 그룹과 연결된 자격 증명 모음의 구독 ID |
| VaultLocation | 청구 그룹과 연결된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 청구 그룹과 연결된 자격 증명 모음의 스토리지 복제 유형 |
| VaultType | 자격 증명 모음 유형("Microsoft.RecoveryServices/vaults") |
| TimeGenerated | 레코드 타임스탬프 |
| ExtendedProperties | 청구 그룹의 추가 속성 |

## <a name="sample-queries"></a>샘플 쿼리

다음은 시스템 함수 사용을 시작하는 데 도움이 되는 몇 가지 샘플 쿼리입니다.

- 지정된 시간 범위 내에 실패한 모든 Azure VM 백업 작업

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- 지정된 시간 범위 내의 모든 SQL 로그 백업 작업

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- VM "testvm"에 사용된 백업 스토리지의 주별 추세

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>다음 단계
[백업 보고서에 대한 자세한 정보](./configure-reports.md)