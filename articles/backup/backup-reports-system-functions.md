---
title: Azure Monitor 로그의 시스템 함수
description: 시스템 함수를 사용 하 여 Azure Monitor 로그에 사용자 지정 쿼리 작성
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 1d26adfd2bd1a3fc1506a334b4b661b66172192d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510552"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Azure Monitor 로그의 시스템 함수

Azure Backup은 LA (Log Analytics) 작업 영역에서 기본적으로 사용할 수 있는 시스템 함수 또는 솔루션 함수 라는 일련의 함수를 제공 합니다.
 
이러한 함수는 LA의 [원시 Azure Backup 테이블](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model) 에 있는 데이터에 대해 작동 하며 간단한 쿼리를 사용 하 여 모든 백업 관련 엔터티의 정보를 쉽게 검색 하는 데 도움이 되는 형식이 지정 된 데이터를 반환 합니다. 사용자는 이러한 함수에 매개 변수를 전달 하 여 이러한 함수에서 반환 되는 데이터를 필터링 할 수 있습니다. 

아래 섹션에 설명 된 대로 사용자 지정 보고서를 만들기 위해 LA 작업 영역에서 백업 데이터를 쿼리 하는 데 시스템 함수를 사용 하는 것이 좋습니다.

## <a name="benefits-of-using-system-functions"></a>시스템 함수 사용의 이점

* **간단한 쿼리**: 함수를 사용 하면 쿼리에 필요한 조인 수를 줄일 수 있습니다. 기본적으로 함수는 쿼리 중인 엔터티 (백업 인스턴스, 작업, 자격 증명 모음 등)와 관련 된 모든 정보를 통합 하는 ' 평면화 된 ' 스키마를 반환 합니다. 예를 들어 백업 항목 이름 및 연결 된 컨테이너를 기준으로 성공한 백업 작업 목록을 가져와야 하는 경우 **_AzureBackup_getJobs ()** 함수를 간단히 호출 하면 각 작업에 대 한이 모든 정보가 제공 됩니다. 반면에 원시 테이블을 직접 쿼리하면 [AddonAzureBackupJobs](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#addonazurebackupjobs) 테이블과 [CoreAzureBackup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#coreazurebackup) 테이블 간에 여러 조인을 수행 해야 합니다.

* **레거시 진단 이벤트의 원활한 전환**: 시스템 함수를 사용 하면 [레거시 진단 이벤트](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event) (azurediagnostics 모드의 azurebackupreport)에서 [리소스 관련 이벤트](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)로 원활 하 게 전환할 수 있습니다. Azure Backup에서 제공 하는 모든 시스템 함수를 사용 하면 함수에서 리소스 관련 테이블의 데이터만 쿼리 하거나 레거시 테이블 및 리소스 관련 테이블 (레코드 중복 제거) 모두에서 데이터를 쿼리 해야 할지 여부를 선택할 수 있는 매개 변수를 지정할 수 있습니다.
    * 리소스 관련 테이블로 성공적으로 마이그레이션한 경우 함수에서 레거시 테이블을 쿼리하지 않도록 선택할 수 있습니다.
    * 현재 마이그레이션 프로세스를 진행 중 이며 분석에 필요한 일부 데이터가 레거시 테이블에 있는 경우 레거시 테이블을 포함 하도록 선택할 수 있습니다. 전환이 완료 되 고 레거시 테이블의 데이터가 더 이상 필요 하지 않은 경우 쿼리에서 함수로 전달 된 매개 변수 값을 업데이트 하 여 레거시 테이블을 제외할 수 있습니다.
    * 기존 테이블만 사용 하 고 있는 경우에도 동일한 매개 변수를 통해 레거시 테이블을 포함 하도록 선택 하면 함수는 계속 작동 합니다. 그러나 가장 빨리 [리소스 관련 테이블로 전환](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) 하는 것이 좋습니다.

* **사용자 지정 쿼리 중단 가능성 감소**: 향후 보고 시나리오를 수용 하기 위해 기본 LA 테이블의 스키마가 향상 된 기능을 도입 하는 Azure Backup 경우 스키마 변경을 고려 하도록 함수 정의가 업데이트 될 수도 있습니다. 따라서 시스템 함수를 사용 하 여 사용자 지정 쿼리를 만드는 경우 테이블의 기본 스키마가 변경 되더라도 쿼리는 중단 되지 않습니다.

> [!NOTE]
> 시스템 함수는 Microsoft에 의해 유지 관리 되며 사용자는 해당 정의를 편집할 수 없습니다. 편집 가능한 함수가 필요한 경우 LA에서 [저장 된 함수](https://docs.microsoft.com/azure/azure-monitor/logs/functions) 를 만들 수 있습니다.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Azure Backup에서 제공 하는 시스템 함수 유형

* **핵심 함수**: 백업 인스턴스, 자격 증명 모음, 정책, 작업 및 청구 엔터티 등 모든 키 Azure Backup 엔터티를 쿼리 하는 데 도움이 되는 함수입니다. 예를 들어 **_AzureBackup_getBackupInstances** 함수는 최근 완료 된 날짜 (UTC)로 현재 환경에 있는 모든 백업 인스턴스 목록을 반환 합니다. 이러한 각 핵심 기능에 대 한 매개 변수 및 반환 된 스키마는이 문서의 아래에 요약 되어 있습니다.

* **추세 함수**: 백업 관련 엔터티에 대 한 기록 레코드를 반환 하는 함수 이며 (예: 백업 인스턴스, 청구 그룹) 해당 엔터티와 관련 된 주요 메트릭 (예: 개수, 저장소 사용)에 대 한 일별, 주별 및 월별 추세 정보를 얻을 수 있습니다. 이러한 각 추세 함수에 대 한 매개 변수 및 반환 된 스키마는이 문서의 아래에 요약 되어 있습니다.

> [!NOTE]
> 현재 시스템 함수는 마지막으로 완료 된 일 (UTC) 까지의 데이터를 반환 합니다. 현재 날짜에 대 한 데이터가 반환 되지 않습니다. 따라서 현재 날짜에 대 한 레코드를 검색 하려는 경우 원시 LA 테이블을 사용 해야 합니다.


## <a name="list-of-system-functions"></a>시스템 함수 목록

### <a name="core-functions"></a>핵심 함수

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

이 함수는 Azure 환경에서 LA 작업 영역에 연결 된 모든 Recovery Services 자격 증명 모음의 목록을 반환 합니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 범위에 있는 모든 자격 증명 모음 관련 레코드를 범위 시작에서 범위 끝으로 인출 해야 하는 경우에만이 매개 변수를 범위 끝 매개 변수와 함께 사용 합니다. 기본적으로 범위 시작 및 범위 끝 값은 null입니다 .이 값은 함수가 각 자격 증명 모음에 대 한 최신 레코드만 검색 하도록 합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | 범위에 있는 모든 자격 증명 모음 관련 레코드를 범위 시작에서 범위 끝으로 인출 해야 하는 경우에만이 매개 변수를 범위 시작 매개 변수와 함께 사용 합니다. 기본적으로 범위 시작 및 범위 끝 값은 null입니다 .이 값은 함수가 각 자격 증명 모음에 대 한 최신 레코드만 검색 하도록 합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독에 있는 자격 증명 모음만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 자격 증명 모음만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    |이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 레코드를 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 레코드를 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은 "Microsoft. RecoveryServices/자격 증명 모음"입니다 .이 매개 변수는이 매개 변수의 기본값입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |

**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 자격 증명 모음의 고유 ID를 나타내는 기본 키입니다. |
| Id | 자격 증명 모음의 ARM (Azure Resource Manager) ID |
| Name | 자격 증명 모음의 이름 |
| SubscriptionId | 자격 증명 모음이 존재 하는 구독의 ID입니다. |
| 위치 | 자격 증명 모음이 있는 위치 |
| VaultStore_StorageReplicationType | 자격 증명 모음과 연결 된 저장소 복제 유형 |
| 태그들 | 자격 증명 모음의 태그 |
| TimeGenerated | 레코드의 타임 스탬프 |
| Type |  자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음")|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

이 함수는 데이터 원본 유형, 저장소 복제 유형 등의 각 정책에 대 한 세부 정보와 함께 Azure 환경에서 사용 중인 백업 정책 목록을 반환 합니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 이 매개 변수는 범위 내에 있는 모든 정책 관련 레코드를 범위 시작에서 범위 끝으로 인출 해야 하는 경우에만 범위 시작 매개 변수와 함께 사용 합니다. 기본적으로 범위 시작 및 범위 끝 값은 null입니다 .이 값은 함수가 각 정책에 대 한 최신 레코드만 검색 하도록 합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | 범위에 있는 모든 정책 관련 레코드를 범위 시작부터 범위 끝까지 인출 해야 하는 경우에만이 매개 변수를 범위 시작 매개 변수와 함께 사용 합니다. 기본적으로 범위 시작 및 범위 끝 값은 null입니다 .이 값은 함수가 각 정책에 대 한 최신 레코드만 검색 하도록 합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독에 있는 정책만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 정책만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    |이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 정책 레코드를 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 정책의 레코드를 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은이 매개 변수의 기본값 인 "Microsoft RecoveryServices/자격 증명 모음"입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |
| Backup솔루션 목록 | 이 매개 변수를 사용 하 여 Azure 환경에서 사용 되는 특정 백업 솔루션 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 예를 들어이 매개 변수 값으로 "Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL" DPM을 지정 하면 함수는 Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL 또는 DPM에서 Azure backup을 사용 하 여 백업 된 항목과 관련 된 레코드만 반환 합니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 백업 보고서에서 지 원하는 모든 백업 솔루션과 관련 된 레코드를 반환 합니다 (지원 되는 값은 "Azure 가상 컴퓨터 백업", "Azure VM 백업에서의 SQL", "Azure VM 백업에서의 SAP HANA", "Azure Storage (Azure Files) 백업", "Azure Backup 에이전트", "DPM", "Azure Backup Server" 또는 쉼표로 구분 된 값의 조합). | N | "Azure 가상 컴퓨터 백업, Azure VM 백업, DPM, Azure Backup 에이전트의 SQL |

**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 정책의 고유 ID를 나타내는 기본 키 |
| Id | 정책에 대 한 Azure Resource Manager (ARM) ID |
| Name | 정책 이름 |
| 백업 솔루션 | 정책이 연결 된 백업 솔루션입니다. 예를 들어 Azure VM 백업, Azure VM Backup의 SQL 등이 여기에 해당 합니다. |
| TimeGenerated | 레코드의 타임 스탬프 |
| VaultUniqueId | 정책과 연결 된 자격 증명 모음을 참조 하는 외래 키 |
| VaultResourceId | 정책에 연결 된 자격 증명 모음의 ARM (Azure Resource Manager) ID입니다. |
| VaultName | 정책과 연결 된 자격 증명 모음의 이름 |
| VaultTags | 정책과 연결 된 자격 증명 모음의 태그 |
| VaultLocation | 정책과 연결 된 자격 증명 모음의 위치 |
| VaultSubscriptionId | 정책과 연결 된 자격 증명 모음의 구독 ID |
| VaultStore_StorageReplicationType | 정책과 연결 된 자격 증명 모음의 저장소 복제 유형 |
| VaultType | 자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음") |
| ExtendedProperties | 정책의 추가 속성 |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

이 함수는 각 작업에 대 한 세부 정보 (예: 작업 상태, 작업 기간, 전송 데이터 등)와 함께 지정 된 시간 범위 내에 트리거된 모든 백업 및 복원 관련 작업의 목록을 반환 합니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 이 매개 변수를 범위 끝 매개 변수와 함께 사용 하 여 기간에서 시작 된 모든 작업의 목록을 범위 시작부터 범위 끝까지 검색할 수 있습니다. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | 이 매개 변수를 범위 시작 매개 변수와 함께 사용 하 여 기간에서 시작 된 모든 작업의 목록을 범위 시작부터 범위 끝까지 검색할 수 있습니다. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독의 자격 증명 모음과 연결 된 작업만 검색 하는 데 도움이 됩니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 자격 증명 모음과 연결 된 작업만 검색 하는 데 도움이 됩니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    | 이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 작업을 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 작업을 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은이 매개 변수의 기본값 인 "Microsoft RecoveryServices/자격 증명 모음"입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |
| Backup솔루션 목록 | 이 매개 변수를 사용 하 여 Azure 환경에서 사용 되는 특정 백업 솔루션 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 예를 들어이 매개 변수 값으로 "Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL" DPM을 지정 하면 함수는 Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL 또는 DPM에서 Azure backup을 사용 하 여 백업 된 항목과 관련 된 레코드만 반환 합니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 백업 보고서에서 지 원하는 모든 백업 솔루션과 관련 된 레코드를 반환 합니다 (지원 되는 값은 "Azure 가상 컴퓨터 백업", "Azure VM 백업에서의 SQL", "Azure VM 백업에서의 SAP HANA", "Azure Storage (Azure Files) 백업", "Azure Backup 에이전트", "DPM", "Azure Backup Server" 또는 쉼표로 구분 된 값의 조합). | N | "Azure 가상 컴퓨터 백업, Azure VM 백업, DPM, Azure Backup 에이전트의 SQL |
| JobOperationList | 이 매개 변수를 사용 하 여 특정 작업 유형에 대 한 함수의 출력을 필터링 할 수 있습니다. 예를 들어 백업 또는 복원입니다. 기본적으로이 매개 변수의 값은 "*"입니다 .이를 통해 함수는 백업 및 복원 작업을 모두 검색 합니다. | N | 백 | 
| JobStatusList | 이 매개 변수를 사용 하 여 특정 작업 상태의 함수 출력을 필터링 할 수 있습니다. 예: Completed, Failed 등. 기본적으로이 매개 변수의 값은 "*" 이며,이는 함수에서 상태와 관계 없이 모든 작업을 검색 하는 데 사용 됩니다. | N | "Failed, CompletedWithWarnings" |
| JobFailureCodeList | 이 매개 변수를 사용 하 여 특정 오류 코드에 대 한 함수의 출력을 필터링 합니다. 기본적으로이 매개 변수의 값은 "*" 이며,이는 함수에서 오류 코드에 관계 없이 모든 작업을 검색 하는 데 사용 됩니다. | N | "Success"
| DatasourceSetName | 이 매개 변수를 사용 하 여 함수의 출력을 특정 부모 리소스로 필터링 할 수 있습니다. 예를 들어 가상 컴퓨터 "testvm"에 속하는 Azure VM 백업 인스턴스에서 SQL을 반환 하려면 _testvm_ 을이 매개 변수 값으로 지정 합니다. 기본적으로이 값은 "*"입니다 .이를 통해 함수는 모든 백업 인스턴스에서 레코드를 검색 합니다. | N | testvm |
| BackupInstanceName | 이 매개 변수를 사용 하 여 특정 백업 인스턴스에서 이름을 기준으로 작업을 검색 합니다. 기본적으로이 값은 "*"입니다 .이를 통해 함수는 모든 백업 인스턴스에서 레코드를 검색 합니다. | N | testvm |
| ExcludeLog | 이 매개 변수를 사용 하 여 함수에서 로그 작업을 반환 하지 않도록 제외할 수 있습니다 (쿼리 성능에 도움이 됨). 기본적으로이 매개 변수의 값은 true로,이 함수는 로그 작업을 제외 합니다. | N | true


**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 작업의 고유 ID를 나타내는 기본 키입니다. |
| OperationCategory | 수행 중인 작업의 범주입니다. 예: 백업, 복원 |
| 작업(Operation) | 수행 중인 작업의 세부 정보입니다. 예: 로그 백업용|
| 상태 | 작업의 상태입니다. 예: Completed, Failed, CompletedWithWarnings |
| ErrorTitle | 작업의 오류 코드 |
| StartTime | 작업이 시작 된 날짜 및 시간 |
| DurationInSecs | 작업 기간 (초) |
| DataTransferredInMBs | 작업에서 전송 된 데이터 (Mb) |
| RestoreJobRPDateTime | 복구할 복구 지점을 만든 날짜 및 시간 |
| RestoreJobRPLocation | 복구 중인 복구 지점이 저장 된 위치입니다. |
| BackupInstanceUniqueId | 작업과 연결 된 백업 인스턴스를 참조 하는 외래 키입니다. |
| BackupInstanceId | 작업에 연결 된 백업 인스턴스의 ARM (Azure Resource Manager) ID입니다. |
| BackupInstanceFriendlyName | 작업과 연결 된 백업 인스턴스의 이름입니다. |
| DatasourceResourceId | 작업에 연결 된 기본 데이터 원본의 ARM (Azure Resource Manager) ID입니다. 예를 들어 VM의 Azure Resource Manager (ARM) ID |
| DatasourceFriendlyName | 작업과 연결 된 기본 데이터 원본의 이름 |
| DatasourceType | 작업과 연결 된 데이터 원본의 유형입니다. 예: "virtualMachines/" |
| BackupSolution | 작업이 연결 된 백업 솔루션입니다. 예를 들어 Azure VM 백업, Azure VM Backup의 SQL 등이 여기에 해당 합니다. |
| DatasourceSetResourceId | 데이터 원본에 대 한 부모 리소스의 ARM (Azure Resource Manager) ID입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SQL의 경우이 필드에는 SQL Database 존재 하는 VM의 ARM (Azure Resource Manager) ID가 포함 됩니다. |
| DatasourceSetType | Datasource의 부모 리소스 유형입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SAP HANA의 경우 부모 리소스가 Azure VM 이기 때문에이 필드는 Microsoft. Compute/virtualMachines입니다. |
| VaultResourceId | 작업에 연결 된 자격 증명 모음의 ARM (Azure Resource Manager) ID입니다. |
| VaultUniqueId | 작업과 연결 된 자격 증명 모음을 참조 하는 외래 키입니다. |
| VaultName | 작업에 연결 된 자격 증명 모음의 이름 |
| VaultTags | 작업에 연결 된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 작업에 연결 된 자격 증명 모음의 구독 ID |
| VaultLocation | 작업에 연결 된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 작업에 연결 된 자격 증명 모음의 저장소 복제 유형 |
| VaultType | 자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음") |
| TimeGenerated | 레코드의 타임 스탬프 |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

이 함수는 Recovery Services 자격 증명 모음과 연결 된 백업 인스턴스 목록과 클라우드 저장소 사용, 연결 된 정책 등의 각 백업 인스턴스에 대 한 자세한 정보를 반환 합니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 범위에 있는 모든 백업 인스턴스 관련 레코드를 범위 시작에서 범위 끝으로 인출 해야 하는 경우에만이 매개 변수를 범위 끝 매개 변수와 함께 사용 합니다. 기본적으로 범위 시작 및 범위 끝 값은 null입니다 .이 값은 함수가 각 백업 인스턴스에 대 한 최신 레코드만 검색 하도록 합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | 범위에 있는 모든 백업 인스턴스 관련 레코드를 범위 시작에서 범위 끝으로 인출 해야 하는 경우에만이 매개 변수를 범위 시작 매개 변수와 함께 사용 합니다. 기본적으로 범위 시작 및 범위 끝 값은 null입니다 .이 값은 함수가 각 백업 인스턴스에 대 한 최신 레코드만 검색 하도록 합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독에 있는 백업 인스턴스만 검색 하는 데 도움이 됩니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 백업 인스턴스만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    |이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 백업 인스턴스의 레코드를 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 백업 인스턴스의 레코드를 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은이 매개 변수의 기본값 인 "Microsoft RecoveryServices/자격 증명 모음"입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |
| Backup솔루션 목록 | 이 매개 변수를 사용 하 여 Azure 환경에서 사용 되는 특정 백업 솔루션 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 예를 들어이 매개 변수 값으로 "Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL" DPM을 지정 하면 함수는 Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL 또는 DPM에서 Azure backup을 사용 하 여 백업 된 항목과 관련 된 레코드만 반환 합니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 백업 보고서에서 지 원하는 모든 백업 솔루션과 관련 된 레코드를 반환 합니다 (지원 되는 값은 "Azure 가상 컴퓨터 백업", "Azure VM 백업에서의 SQL", "Azure VM 백업에서의 SAP HANA", "Azure Storage (Azure Files) 백업", "Azure Backup 에이전트", "DPM", "Azure Backup Server" 또는 쉼표로 구분 된 값의 조합). | N | "Azure 가상 컴퓨터 백업, Azure VM 백업, DPM, Azure Backup 에이전트의 SQL |
| ProtectionInfoList | 이 매개 변수를 사용 하 여 적극적으로 보호 되는 백업 인스턴스만 포함할지 아니면 보호가 중지 된 인스턴스와 초기 백업이 보류 중인 인스턴스를 포함할지 여부를 선택할 수 있습니다. 지원 되는 값은 "Protected", "Protectionstopped)", "InitialBackupPending" 또는 이러한 값의 쉼표로 구분 된 조합입니다. 기본적으로이 값은 "*" 이며,이를 통해 함수는 보호 세부 정보에 관계 없이 모든 백업 인스턴스를 검색 합니다. | N | 보호 |
| DatasourceSetName | 이 매개 변수를 사용 하 여 함수의 출력을 특정 부모 리소스로 필터링 할 수 있습니다. 예를 들어 가상 컴퓨터 "testvm"에 속하는 Azure VM 백업 인스턴스에서 SQL을 반환 하려면 _testvm_ 을이 매개 변수 값으로 지정 합니다. 기본적으로이 값은 "*"입니다 .이를 통해 함수는 모든 백업 인스턴스에서 레코드를 검색 합니다. | N | testvm |
| BackupInstanceName | 이 매개 변수를 사용 하 여 이름으로 특정 백업 인스턴스를 검색 합니다. 기본적으로이 값은 "*"입니다. 그러면 함수에서 모든 백업 인스턴스를 검색 합니다. | N | testvm |
| DisplayAllFields | 이 매개 변수를 사용 하 여 함수에서 반환 된 필드의 하위 집합만 검색할지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 함수 출력에서 저장소 및 보존 지점 관련 정보를 제거 합니다. 이 기능은 더 큰 쿼리에서이 함수를 중간 단계로 사용 하 고 분석에 필요 하지 않은 열을 제거 하 여 쿼리 성능을 최적화 해야 하는 경우에 유용 합니다. 기본적으로이 매개 변수의 값은 true 이며,이 경우 함수는 백업 인스턴스와 관련 된 모든 필드를 반환 합니다. | N | true |

**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 백업 인스턴스의 고유 ID를 나타내는 기본 키입니다. |
| Id | 백업 인스턴스의 ARM (Azure Resource Manager) ID입니다. |
| FriendlyName | 백업 인스턴스의 이름입니다. |
| ProtectionInfo | 백업 인스턴스의 보호 설정에 대 한 정보입니다. 예: 보호 구성 됨, 보호 중지 됨, 초기 백업 보류 중 |
| LatestRecoveryPoint | 백업 인스턴스와 연결 된 최신 복구 지점의 날짜 및 시간 |
| OldestRecoveryPoint | 백업 인스턴스와 연결 된 가장 오래 된 복구 지점의 날짜 및 시간 |
| SourceSizeInMBs | 백업 인스턴스의 프런트 엔드 크기 (Mb) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음-표준 계층의 백업 인스턴스에서 사용 되는 총 클라우드 저장소 |
| DataSourceFriendlyName | 백업 인스턴스에 해당 하는 데이터 원본의 이름입니다. |
| BackupSolution | 백업 인스턴스가 연결 된 백업 솔루션입니다. 예를 들어 Azure VM 백업, Azure VM Backup의 SQL 등이 여기에 해당 합니다. |
| DatasourceType | 백업 인스턴스에 해당 하는 데이터 원본의 유형입니다. 예: "virtualMachines/" |
| DatasourceResourceId | 백업 인스턴스에 해당 하는 기본 데이터 원본의 ARM (Azure Resource Manager) ID입니다. 예를 들어 VM의 Azure Resource Manager (ARM) ID |
| DatasourceSetFriendlyName | Datasource의 부모 리소스에 대 한 이름입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SQL의 경우이 필드는 SQL Database 존재 하는 VM의 이름을 포함 합니다. |
| DatasourceSetResourceId | 데이터 원본에 대 한 부모 리소스의 ARM (Azure Resource Manager) ID입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SQL의 경우이 필드에는 SQL Database 존재 하는 VM의 ARM (Azure Resource Manager) ID가 포함 됩니다. |
| DatasourceSetType | Datasource의 부모 리소스 유형입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SAP HANA의 경우 부모 리소스가 Azure VM 이기 때문에이 필드는 Microsoft. Compute/virtualMachines입니다. |
| PolicyName | 백업 인스턴스와 연결 된 정책의 이름입니다. |
| PolicyUniqueId | 백업 인스턴스와 연결 된 정책을 참조 하는 외래 키입니다.  |
| PolicyId | 백업 인스턴스와 연결 된 정책의 ARM (Azure Resource Manager) ID입니다. |
| VaultResourceId | 백업 인스턴스와 연결 된 자격 증명 모음의 ARM (Azure Resource Manager) ID입니다. |
| VaultUniqueId | 백업 인스턴스와 연결 된 자격 증명 모음을 참조 하는 외래 키 |
| VaultName | 백업 인스턴스와 연결 된 자격 증명 모음의 이름 |
| VaultTags | 백업 인스턴스와 연결 된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 백업 인스턴스와 연결 된 자격 증명 모음의 구독 ID |
| VaultLocation | 백업 인스턴스와 연결 된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 백업 인스턴스와 연결 된 자격 증명 모음의 저장소 복제 유형 |
| VaultType | 자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음") |
| TimeGenerated | 레코드의 타임 스탬프 |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

이 함수는 모든 백업 관련 청구 엔터티 (청구 그룹)의 목록과 함께 프런트 엔드 크기 및 총 클라우드 저장소와 같은 주요 청구 구성 요소에 대 한 정보를 반환 합니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 범위에 있는 모든 청구 그룹 관련 레코드를 범위 시작부터 범위 끝까지 인출 해야 하는 경우에만이 매개 변수를 범위 끝 매개 변수와 함께 사용 합니다. 기본적으로 범위 지정 Start 및 범위 끝 값은 null 이며이는 함수가 각 청구 그룹의 최신 레코드만 검색 하도록 합니다. | N | "2021-03-03 00:00:00" |
| RangeEnd     | 범위에 있는 모든 청구 그룹 관련 레코드를 범위 시작부터 범위 끝까지 인출 해야 하는 경우에만이 매개 변수를 범위 시작 매개 변수와 함께 사용 합니다. 기본적으로 범위 지정 Start 및 범위 끝 값은 null 이며이는 함수가 각 청구 그룹의 최신 레코드만 검색 하도록 합니다. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    |이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 백업 인스턴스의 레코드를 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 청구 그룹의 레코드를 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은이 매개 변수의 기본값 인 "Microsoft RecoveryServices/자격 증명 모음"입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |
| Backup솔루션 목록 | 이 매개 변수를 사용 하 여 Azure 환경에서 사용 되는 특정 백업 솔루션 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 예를 들어이 매개 변수 값으로 "Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL" DPM을 지정 하면 함수는 Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL 또는 DPM에서 Azure backup을 사용 하 여 백업 된 항목과 관련 된 레코드만 반환 합니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 백업 보고서에서 지 원하는 모든 백업 솔루션과 관련 된 레코드를 반환 합니다 (지원 되는 값은 "Azure 가상 컴퓨터 백업", "Azure VM 백업에서의 SQL", "Azure VM 백업에서의 SAP HANA", "Azure Storage (Azure Files) 백업", "Azure Backup 에이전트", "DPM", "Azure Backup Server" 또는 쉼표로 구분 된 값의 조합). | N | "Azure 가상 컴퓨터 백업, Azure VM 백업, DPM, Azure Backup 에이전트의 SQL |
| BillingGroupName | 이 매개 변수를 사용 하 여 이름별로 특정 청구 그룹을 검색 합니다. 기본적으로이 값은 "*"입니다. 그러면 함수에서 모든 청구 그룹을 검색 합니다. | N | testvm |

**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 청구 그룹의 고유 ID를 나타내는 기본 키 |
| FriendlyName | 청구 그룹의 이름 |
| Name | 청구 그룹의 이름입니다. |
| Type | 청구 그룹의 유형입니다. 예: ProtectedContainer 또는 BackupItem |
| SourceSizeInMBs | 요금 청구 그룹의 프런트 엔드 크기 (Mb) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음에서 청구 그룹에서 사용한 총 클라우드 저장소-표준 계층 |
| BackupSolution | 청구 그룹이 연결 된 백업 솔루션입니다. 예를 들어 Azure VM 백업, Azure VM Backup의 SQL 등이 여기에 해당 합니다. |
| VaultResourceId | 청구 그룹과 연결 된 자격 증명 모음의 ARM (Azure Resource Manager) ID |
| VaultUniqueId | 청구 그룹과 연결 된 자격 증명 모음을 참조 하는 외래 키 |
| VaultName | 청구 그룹과 연결 된 자격 증명 모음의 이름 |
| VaultTags | 청구 그룹과 연결 된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 청구 그룹과 연결 된 자격 증명 모음의 구독 ID |
| VaultLocation | 청구 그룹과 연결 된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 청구 그룹과 연결 된 자격 증명 모음의 저장소 복제 유형 |
| VaultType | 자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음") |
| TimeGenerated | 레코드의 타임 스탬프 |
| ExtendedProperties | 청구 그룹의 추가 속성 |

### <a name="trend-functions"></a>추세 함수

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

이 함수는 각 백업 인스턴스에 대 한 기록 레코드를 반환 하 여 여러 세분성 수준에서 백업 인스턴스 수 및 저장소 사용량과 관련 된 주요 일별, 주별 및 월별 추세를 볼 수 있습니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 이 매개 변수를 범위 끝 매개 변수와 함께 사용 하 여 기간에 있는 모든 백업 인스턴스 관련 레코드를 범위 시작부터 범위 끝까지 검색할 수 있습니다. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | 이 매개 변수를 범위 시작 매개 변수와 함께 사용 하 여 기간에 있는 모든 백업 인스턴스 관련 레코드를 범위 시작부터 범위 끝까지 검색할 수 있습니다. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독에 있는 백업 인스턴스만 검색 하는 데 도움이 됩니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 백업 인스턴스만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    |이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 백업 인스턴스의 레코드를 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 백업 인스턴스의 레코드를 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은이 매개 변수의 기본값 인 "Microsoft RecoveryServices/자격 증명 모음"입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |
| Backup솔루션 목록 | 이 매개 변수를 사용 하 여 Azure 환경에서 사용 되는 특정 백업 솔루션 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 예를 들어이 매개 변수 값으로 "Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL" DPM을 지정 하면 함수는 Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL 또는 DPM에서 Azure backup을 사용 하 여 백업 된 항목과 관련 된 레코드만 반환 합니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 백업 보고서에서 지 원하는 모든 백업 솔루션과 관련 된 레코드를 반환 합니다 (지원 되는 값은 "Azure 가상 컴퓨터 백업", "Azure VM 백업에서의 SQL", "Azure VM 백업에서의 SAP HANA", "Azure Storage (Azure Files) 백업", "Azure Backup 에이전트", "DPM", "Azure Backup Server" 또는 쉼표로 구분 된 값의 조합). | N | "Azure 가상 컴퓨터 백업, Azure VM 백업, DPM, Azure Backup 에이전트의 SQL |
| ProtectionInfoList | 이 매개 변수를 사용 하 여 적극적으로 보호 되는 백업 인스턴스만 포함할지 아니면 보호가 중지 된 인스턴스와 초기 백업이 보류 중인 인스턴스를 포함할지 여부를 선택할 수 있습니다. 지원 되는 값은 "Protected", "Protectionstopped)", "InitialBackupPending" 또는 이러한 값의 쉼표로 구분 된 조합입니다. 기본적으로이 값은 "*" 이며,이를 통해 함수는 보호 세부 정보에 관계 없이 모든 백업 인스턴스를 검색 합니다. | N | 보호 |
| DatasourceSetName | 이 매개 변수를 사용 하 여 함수의 출력을 특정 부모 리소스로 필터링 할 수 있습니다. 예를 들어 가상 컴퓨터 "testvm"에 속하는 Azure VM 백업 인스턴스에서 SQL을 반환 하려면 _testvm_ 을이 매개 변수 값으로 지정 합니다. 기본적으로이 값은 "*"입니다 .이를 통해 함수는 모든 백업 인스턴스에서 레코드를 검색 합니다. | N | testvm |
| BackupInstanceName | 이 매개 변수를 사용 하 여 이름으로 특정 백업 인스턴스를 검색 합니다. 기본적으로이 값은 "*"입니다. 그러면 함수에서 모든 백업 인스턴스를 검색 합니다. | N | testvm |
| DisplayAllFields | 이 매개 변수를 사용 하 여 함수에서 반환 된 필드의 하위 집합만 검색할지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 함수 출력에서 저장소 및 보존 지점 관련 정보를 제거 합니다. 이 기능은 더 큰 쿼리에서이 함수를 중간 단계로 사용 하 고 분석에 필요 하지 않은 열을 제거 하 여 쿼리 성능을 최적화 해야 하는 경우에 유용 합니다. 기본적으로이 매개 변수의 값은 true 이며,이 경우 함수는 백업 인스턴스와 관련 된 모든 필드를 반환 합니다. | N | true |
| AggregationType | 이 매개 변수를 사용 하 여 데이터를 검색 해야 하는 시간 세분성을 지정할 수 있습니다. 이 매개 변수 값이 "Daily" 인 경우 함수는 매일 백업 인스턴스당 레코드를 반환 하므로 저장소 사용량과 백업 인스턴스 수의 일일 추세를 분석할 수 있습니다. 이 매개 변수 값이 "매주" 인 경우이 함수는 주별 백업 인스턴스당 레코드를 반환 하므로 주별 추세를 분석할 수 있습니다. 마찬가지로 "매월"을 지정 하 여 월별 추세를 분석할 수 있습니다. 기본값은 "매일"입니다. 더 큰 시간 범위에서 데이터를 보는 경우 더 나은 쿼리 성능과 추세 분석의 용이성을 위해 "주별" 또는 "월간"를 사용 하는 것이 좋습니다. | N | 단위의 |

**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 백업 인스턴스의 고유 ID를 나타내는 기본 키입니다. |
| Id | 백업 인스턴스의 ARM (Azure Resource Manager) ID입니다. |
| FriendlyName | 백업 인스턴스의 이름입니다. |
| ProtectionInfo | 백업 인스턴스의 보호 설정에 대 한 정보입니다. 예: 보호 구성 됨, 보호 중지 됨, 초기 백업 보류 중 |
| LatestRecoveryPoint | 백업 인스턴스와 연결 된 최신 복구 지점의 날짜 및 시간 |
| OldestRecoveryPoint | 백업 인스턴스와 연결 된 가장 오래 된 복구 지점의 날짜 및 시간 |
| SourceSizeInMBs | 백업 인스턴스의 프런트 엔드 크기 (Mb) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음-표준 계층의 백업 인스턴스에서 사용 되는 총 클라우드 저장소 |
| DataSourceFriendlyName | 백업 인스턴스에 해당 하는 데이터 원본의 이름입니다. |
| BackupSolution | 백업 인스턴스가 연결 된 백업 솔루션입니다. 예를 들어 Azure VM 백업, Azure VM Backup의 SQL 등이 여기에 해당 합니다. |
| DatasourceType | 백업 인스턴스에 해당 하는 데이터 원본의 유형입니다. 예: "virtualMachines/" |
| DatasourceResourceId | 백업 인스턴스에 해당 하는 기본 데이터 원본의 ARM (Azure Resource Manager) ID입니다. 예를 들어 VM의 Azure Resource Manager (ARM) ID |
| DatasourceSetFriendlyName | Datasource의 부모 리소스에 대 한 이름입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SQL의 경우이 필드는 SQL Database 존재 하는 VM의 이름을 포함 합니다. |
| DatasourceSetResourceId | 데이터 원본에 대 한 부모 리소스의 ARM (Azure Resource Manager) ID입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SQL의 경우이 필드에는 SQL Database 존재 하는 VM의 ARM (Azure Resource Manager) ID가 포함 됩니다. |
| DatasourceSetType | Datasource의 부모 리소스 유형입니다 (해당 하는 경우). 예를 들어 Azure VM 데이터 원본에 있는 SAP HANA의 경우 부모 리소스가 Azure VM 이기 때문에이 필드는 Microsoft. Compute/virtualMachines입니다. |
| PolicyName | 백업 인스턴스와 연결 된 정책의 이름입니다. |
| PolicyUniqueId | 백업 인스턴스와 연결 된 정책을 참조 하는 외래 키입니다.  |
| PolicyId | 백업 인스턴스와 연결 된 정책의 ARM (Azure Resource Manager) ID입니다. |
| VaultResourceId | 백업 인스턴스와 연결 된 자격 증명 모음의 ARM (Azure Resource Manager) ID입니다. |
| VaultUniqueId | 백업 인스턴스와 연결 된 자격 증명 모음을 참조 하는 외래 키 |
| VaultName | 백업 인스턴스와 연결 된 자격 증명 모음의 이름 |
| VaultTags | 백업 인스턴스와 연결 된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 백업 인스턴스와 연결 된 자격 증명 모음의 구독 ID |
| VaultLocation | 백업 인스턴스와 연결 된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 백업 인스턴스와 연결 된 자격 증명 모음의 저장소 복제 유형 |
| VaultType | 자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음") |
| TimeGenerated | 레코드의 타임 스탬프 |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

이 함수는 각 청구 엔터티에 대 한 기록 레코드를 반환 하 여 여러 세분성 수준에서 프런트 엔드 크기 및 저장소 사용량과 관련 된 주요 일별, 주별 및 월별 추세를 볼 수 있습니다.

**매개 변수**

| **매개 변수 이름** | **설명** | **필수 여부** | **예제 값** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 이 매개 변수를 범위 끝 매개 변수와 함께 사용 하 여 기간에 대 한 모든 청구 그룹 관련 레코드를 범위 시작에서 범위 끝으로 검색할 수 있습니다. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | 이 매개 변수를 범위 시작 매개 변수와 함께 사용 하 여 기간에 있는 모든 청구 그룹 관련 레코드를 범위 시작에서 범위 끝으로 검색할 수 있습니다. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 구독 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 구독 Id의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 구독에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 구독에서 레코드를 검색 합니다. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | 이 매개 변수를 사용 하 여 백업 데이터가 존재 하는 특정 지역 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 쉼표로 구분 된 지역 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 지역에 있는 해당 청구 그룹만 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 지역에서 레코드를 검색 합니다. | N | "eastus, westus"|
| VaultList    |이 매개 변수를 사용 하 여 특정 자격 증명 모음 집합의 함수 출력을 필터링 합니다. 자격 증명 모음 이름의 쉼표로 구분 된 목록을이 함수에 대 한 매개 변수로 지정 하면 지정 된 자격 증명 모음에만 관련 된 백업 인스턴스의 레코드를 검색할 수 있습니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 모든 자격 증명 모음에서 청구 그룹의 레코드를 검색 합니다. | N |"vault1, contoso-vault2, vault3"|
| VaultTypeList     | 이 매개 변수를 사용 하 여 함수 출력을 특정 자격 증명 모음 유형과 관련 된 레코드로 필터링 할 수 있습니다. 현재 유일 하 게 지원 되는 자격 증명 모음 유형은이 매개 변수의 기본값 인 "Microsoft RecoveryServices/자격 증명 모음"입니다. | N | "Microsoft RecoveryServices/자격 증명 모음"|
| ExcludeLegacyEvent  | 이 매개 변수를 사용 하 여 레거시 AzureDiagnostics 테이블의 데이터를 쿼리할 것인지 여부를 선택할 수 있습니다. 이 매개 변수 값이 false 이면 함수는 AzureDiagnostics 테이블과 리소스 관련 테이블의 데이터를 쿼리 합니다. 이 매개 변수 값이 true 이면 함수는 리소스 관련 테이블의 데이터만 쿼리 합니다. 기본값은 true입니다. | N | true |
| Backup솔루션 목록 | 이 매개 변수를 사용 하 여 Azure 환경에서 사용 되는 특정 백업 솔루션 집합에 대해 함수의 출력을 필터링 할 수 있습니다. 예를 들어이 매개 변수 값으로 "Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL" DPM을 지정 하면 함수는 Azure 가상 컴퓨터 백업, Azure VM 백업에서 SQL 또는 DPM에서 Azure backup을 사용 하 여 백업 된 항목과 관련 된 레코드만 반환 합니다. 기본적으로이 매개 변수의 값은 ' * '입니다 .이를 통해 함수는 백업 보고서에서 지 원하는 모든 백업 솔루션과 관련 된 레코드를 반환 합니다 (지원 되는 값은 "Azure 가상 컴퓨터 백업", "Azure VM 백업에서의 SQL", "Azure VM 백업에서의 SAP HANA", "Azure Storage (Azure Files) 백업", "Azure Backup 에이전트", "DPM", "Azure Backup Server" 또는 쉼표로 구분 된 값의 조합). | N | "Azure 가상 컴퓨터 백업, Azure VM 백업, DPM, Azure Backup 에이전트의 SQL |
| BillingGroupName | 이 매개 변수를 사용 하 여 이름별로 특정 청구 그룹을 검색 합니다. 기본적으로이 값은 "*"입니다. 그러면 함수에서 모든 청구 그룹을 검색 합니다. | N | testvm |
| AggregationType | 이 매개 변수를 사용 하 여 데이터를 검색 해야 하는 시간 세분성을 지정할 수 있습니다. 이 매개 변수 값이 "Daily" 인 경우이 함수는 매일 청구 그룹당 레코드를 반환 하 여 저장소 사용량과 프런트 엔드 크기의 일일 추세를 분석할 수 있습니다. 이 매개 변수 값이 "매주" 인 경우이 함수는 주별 백업 인스턴스당 레코드를 반환 하므로 주별 추세를 분석할 수 있습니다. 마찬가지로 "매월"을 지정 하 여 월별 추세를 분석할 수 있습니다. 기본값은 "매일"입니다. 더 큰 시간 범위에서 데이터를 보는 경우 더 나은 쿼리 성능과 추세 분석의 용이성을 위해 "주별" 또는 "월간"를 사용 하는 것이 좋습니다. | N | 단위의 |

**반환 된 필드**

| **필드 이름** | **설명** |
| -------------- | --------------- |
| UniqueId | 청구 그룹의 고유 ID를 나타내는 기본 키 |
| FriendlyName | 청구 그룹의 이름 |
| Name | 청구 그룹의 이름입니다. |
| Type | 청구 그룹의 유형입니다. 예: ProtectedContainer 또는 BackupItem |
| SourceSizeInMBs | 요금 청구 그룹의 프런트 엔드 크기 (Mb) |
| VaultStore_StorageConsumptionInMBs | 자격 증명 모음에서 청구 그룹에서 사용한 총 클라우드 저장소-표준 계층 |
| BackupSolution | 청구 그룹이 연결 된 백업 솔루션입니다. 예를 들어 Azure VM 백업, Azure VM Backup의 SQL 등이 여기에 해당 합니다. |
| VaultResourceId | 청구 그룹과 연결 된 자격 증명 모음의 ARM (Azure Resource Manager) ID |
| VaultUniqueId | 청구 그룹과 연결 된 자격 증명 모음을 참조 하는 외래 키 |
| VaultName | 청구 그룹과 연결 된 자격 증명 모음의 이름 |
| VaultTags | 청구 그룹과 연결 된 자격 증명 모음의 태그 |
| VaultSubscriptionId | 청구 그룹과 연결 된 자격 증명 모음의 구독 ID |
| VaultLocation | 청구 그룹과 연결 된 자격 증명 모음의 위치 |
| VaultStore_StorageReplicationType | 청구 그룹과 연결 된 자격 증명 모음의 저장소 복제 유형 |
| VaultType | 자격 증명 모음의 유형 ("Microsoft RecoveryServices/자격 증명 모음") |
| TimeGenerated | 레코드의 타임 스탬프 |
| ExtendedProperties | 청구 그룹의 추가 속성 |

## <a name="sample-queries"></a>샘플 쿼리

다음은 시스템 함수 사용을 시작 하는 데 도움이 되는 몇 가지 샘플 쿼리입니다.

- 지정 된 시간 범위 내에 실패 한 모든 Azure VM 백업 작업

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- 지정 된 시간 범위의 모든 SQL 로그 백업 작업

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- VM "testvm"에 사용 되는 백업 저장소의 주별 추세

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>다음 단계
[백업 보고서에 대 한 자세한 정보](https://docs.microsoft.com/azure/backup/configure-reports)
