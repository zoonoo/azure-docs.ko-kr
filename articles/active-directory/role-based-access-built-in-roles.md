---
title: "Actions 및 NotActions - Azure RBAC의 역할 | Microsoft Docs"
description: "이 항목에서는 역할 기반 액세스 제어(RBAC)에 대한 기본 제공 역할에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/31/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: becd7fbcfa094257408ed96eda0c62deefceb44d
ms.openlocfilehash: 59067ef58d276265e0431119986774ff14212067


---
# <a name="rbac-built-in-roles"></a>RBAC: 기본 제공 역할
Azure 역할 기반 액세스 제어(RBAC)에는 사용자, 그룹 및 서비스에 할당할 수 있는 다음 기본 제공 역할이 포함되었습니다. 기본 제공 역할의 정의는 수정할 수 없습니다. 그러나 조직의 특정 요구 사항에 맞게 [Azure RBAC에서 사용자 지정 역할](role-based-access-control-custom-roles.md) 을 만들 수 있습니다.

## <a name="roles-in-azure"></a>Azure의 역할
다음 테이블은 기본 제공 역할을 간략하게 설명합니다. 역할 이름을 클릭하면 역할에 대한 **작업** 및 **작업 안 함** 목록을 자세히 볼 수 있습니다. **작업** 속성은 Azure 리소스에 허용되는 작업을 지정합니다. 작업 문자열에는 와일드카드 문자를 사용할 수 있습니다. **작업 안 함** 속성은 허용된 작업에서 제외되는 작업을 지정합니다.

> [!NOTE]
> Azure 역할 정의는 끊임없이 진화하고 있습니다. 이 문서는 가능한 최신 상태로 유지되지만 Azure PowerShell에서 항상 최신 역할 정의를 확인할 수 있습니다. 가능한 cmdlets `(get-azurermroledefinition "<role name>").actions` 또는 `(get-azurermroledefinition "<role name>").notactions`을(를) 사요ㅛㅇ합니다.
>
>

| 역할 이름 | 설명 |
| --- | --- |
| [API 관리 서비스 참여자](#api-management-service-contributor) |API 관리 서비스를 관리할 수 있음 |
| [Application Insights 구성 요소 참여자](#application-insights-component-contributor) |Application Insights 구성 요소를 관리할 수 있음 |
| [자동화 운영자](#automation-operator) |작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있음 |
| [백업 참여자](#backup-contributor) | Recovery Services 자격 증명 모음에서 백업을 관리할 수 있습니다. |
| [백업 운영자](#backup-operator) | Recovery Services 자격 증명 모음에서 백업 제거를 제외한 백업 관리를 수행할 수 있습니다. |
| [백업 읽기 권한자](#backup-reader) | 모든 백업 관리 서비스를 볼 수 있습니다.  |
| [BizTalk 참여자](#biztalk-contributor) |BizTalk 서비스를 관리할 수 있음 |
| [ClearDB MySQL DB 참여자](#cleardb-mysql-db-contributor) |ClearDB MySQL 데이터베이스를 관리할 수 있음 |
| [참여자](#contributor) |액세스를 제외한 모든 것을 관리할 수 있음 |
| [데이터 팩터리 참여자](#data-factory-contributor) |데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리할 수 있습니다. |
| [DevTest Lab 사용자](#devtest-labs-user) |모든 항목을 볼 수 있으며 가상 컴퓨터를 연결, 시작, 다시 시작 및 종료할 수 있음 |
| [DNS 영역 참여자](#dns-zone-contributor) |DNS 영역 및 레코드를 관리할 수 있음 |
| [DocumentDB 계정 참여자](#documentdb-account-contributor) |DocumentDB 계정을 관리할 수 있음 |
| [지능형 시스템 계정 참여자](#intelligent-systems-account-contributor) |지능형 시스템 계정을 관리할 수 있음 |
| [네트워크 참여자](#network-contributor) |모든 네트워크 리소스를 관리할 수 있음 |
| [NewRelic APM 계정 참여자](#new-relic-apm-account-contributor) |New Relic 응용 프로그램 성능 관리 계정 및 응용 프로그램을 관리할 수 있음 |
| [소유자](#owner) |액세스를 제외한 모든 것을 관리할 수 있음 |
| [판독기](#reader) |모든 항목을 볼 수 있지만 변경할 수는 없음 |
| [Redis 캐시 참여자](#redis-cache-contributor) |Redis 캐시를 관리할 수 있음 |
| [스케줄러 작업 컬렉션 참여자](#scheduler-job-collections-contributor) |스케줄러 작업 컬렉션을 관리할 수 있음 |
| [검색 서비스 참여자](#search-service-contributor) |검색 서비스를 관리할 수 있음 |
| [보안 관리자](#security-manager) |보안 구성 요소, 보안 정책 및 가상 컴퓨터를 관리할 수 있음 |
| [SQL DB 참여자](#sql-db-contributor) |해당 보안 관련 정책을 제외한 SQL 데이터베이스를 관리할 수 있음 |
| [SQL 보안 관리자](#sql-security-manager) |SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있음 |
| [SQL Server 참여자](#sql-server-contributor) |해당 보안 관련 정책을 제외한 SQL Server 및 데이터베이스를 관리할 수 있음 |
| [클래식 저장소 계정 참여자](#classic-storage-account-contributor) |클래식 저장소 계정을 관리할 수 있음 |
| [저장소 계정 참여자](#storage-account-contributor) |저장소 계정을 관리할 수 있음 |
| [사용자 액세스 관리자](#user-access-administrator) |Azure 리소스에 대한 사용자 액세스를 관리할 수 있음 |
| [클래식 가상 컴퓨터 참여자](#classic-virtual-machine-contributor) |클래식 가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음 |
| [가상 컴퓨터 참여자](#virtual-machine-contributor) |가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음 |
| [클래식 네트워크 참여자](#classic-network-contributor) |클래식 가상 네트워크 및 예약된 IP를 관리할 수 있음 |
| [웹 계획 참여자](#web-plan-contributor) |웹 계획을 관리할 수 있음 |
| [웹 사이트 참여자](#website-contributor) |웹 사이트를 관리할 수 있으나 여기에 연결된 웹 계획은 관리할 수 없음 |

## <a name="role-permissions"></a>역할 권한
다음 표에서는 각 역할에 부여되는 특정 권한에 대해 설명합니다. 여기에는 권한을 부여하는 **작업**과 권한을 제한하는 **작업 안 함**이 포함될 수 있습니다.

### <a name="api-management-service-contributor"></a>API 관리 서비스 참여자
API 관리 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |API 관리 서비스 만들기 및 관리 |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |역할 및 역할 할당 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="application-insights-component-contributor"></a>Application Insights 구성 요소 참여자
Application Insights 구성 요소를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.Insights/components/* |Insights 구성 요소 만들기 및 관리 |
| Microsoft.Insights/webtests/* |웹 테스트 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="automation-operator"></a>자동화 운영자
작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Automation/automationAccounts/jobs/read |자동화 계정 작업 읽기 |
| Microsoft.Automation/automationAccounts/jobs/resume/action |자동화 계정 작업 다시 시작 |
| Microsoft.Automation/automationAccounts/jobs/stop/action |자동화 계정 작업 중지 |
| Microsoft.Automation/automationAccounts/jobs/streams/read |자동화 계정 작업 스트림 읽기 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |자동화 계정 작업 일시 중단 |
| Microsoft.Automation/automationAccounts/jobs/write |자동화 계정 작업 쓰기 |
| Microsoft.Automation/automationAccounts/jobSchedules/read |자동화 계정 작업 일정 읽기 |
| Microsoft.Automation/automationAccounts/jobSchedules/write |자동화 계정 작업 일정 읽기 |
| Microsoft.Automation/automationAccounts/read |자동화 계정 읽기 |
| Microsoft.Automation/automationAccounts/runbooks/read |자동화 Runbook 읽기 |
| Microsoft.Automation/automationAccounts/schedules/read |자동화 계정 일정 읽기 |
| Microsoft.Automation/automationAccounts/schedules/write |자동화 계정 일정 쓰기 |
| Microsoft.Insights/components/* |Insights 구성 요소 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="backup-contributor"></a>백업 참여자
Recovery Services 자격 증명 모음 생성 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 백업 관리 작업을 관리할 수 있습니다.

| **actions** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | 백업 관리에 대한 작업의 결과 관리 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services 자격 증명 모음의 백업 패브릭 내에서 백업 컨테이너 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 백업 작업을 Excel로 내보내기 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | 백업 관리와 관련된 메타데이터 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | 백업 정책 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | 백업한 항목 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | 백업 항목을 보유하는 컨테이너 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services 자격 증명 모음의 백업과 관련된 인증서 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 | 
| Microsoft.RecoveryServices/Vaults/read | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 새로 만든 컨테이너를 가져오기 위한 검색 작업 관리 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services 자격 증명 모음 만들기 및 사용 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="backup-operator"></a>백업 운영자
자격 증명 모음 생성, 백업 제거 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 백업 관리 작업을 관리할 수 있습니다.

| **actions** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 백업 관리에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 백업된 항목에 대해 주문형 백업 작업 수행 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 백업된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | 백업된 항목에 대해 수행된 작업의 상태 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 백업된 항목의 복구 지점 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 백업된 항목의 복구 지점을 사용하여 복원 작업 수행 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 항목 만들기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 백업 항목을 보유하는 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 백업 작업을 Excel로 내보내기 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | 백업 관리와 관련된 메타데이터 읽기 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 백업 정책에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | 백업 정책 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 백업 항목을 보유하는 백업된 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | 자격 증명 모음과 관련된 확장된 정보 읽기 | 
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | 자격 증명 모음과 관련된 확장된 정보 쓰기 | 
| Microsoft.RecoveryServices/Vaults/read | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 새로 만든 컨테이너를 가져오기 위한 검색 작업 관리 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 자격 증명 모음의 등록된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 자격 증명 모음의 등록된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | 자격 증명 모음에 등록된 항목 쓰기 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음의 사용 현황 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

### <a name="backup-reader"></a>백업 읽기 권한자
Recovery Services 자격 증명 모음의 백업 관리를 모니터링할 수 있습니다.

| **actions** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | 백업 관리에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | 보호 컨테이너에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | 백업된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | 백업된 항목에 대해 수행된 작업의 상태 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | 백업 항목을 보유하는 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | 백업 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | 백업 작업 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 백업 작업을 Excel로 내보내기 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | 백업 관리와 관련된 메타데이터 읽기 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | 백업 관리 작업 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | 백업 정책에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | 백업 정책 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | 백업 항목을 보유하는 백업된 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | 자격 증명 모음과 관련된 확장된 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/read  | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | 새로 만든 컨테이너를 가져오기 위한 검색 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | 자격 증명 모음의 등록된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | 자격 증명 모음의 등록된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Recovery Services 자격 증명 모음의 사용 현황 읽기 |

### <a name="biztalk-contributor"></a>BizTalk 참여자
BizTalk 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.BizTalkServices/BizTalk/* |BizTalk 서비스 만들기 및 관리 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB 참여자
ClearDB MySQL 데이터베이스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |
| successbricks.cleardb/databases/* |ClearDB MySQL 데이터베이스 만들기 및 관리 |

### <a name="contributor"></a>참여자
액세스를 제외한 모든 것을 관리할 수 있음

| **actions** |  |
| --- | --- |
| * |모든 유형의 리소스 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |역할 및 역할 할당을 삭제할 수 없음 |
| Microsoft.Authorization/*/Write |역할 및 역할 할당을 만들 수 없음 |

### <a name="data-factory-contributor"></a>데이터 팩터리 참여자
데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.DataFactory/dataFactories/* |데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="devtest-labs-user"></a>DevTest Lab 사용자
모든 항목을 볼 수 있으며 가상 컴퓨터를 연결, 시작, 다시 시작 및 종료할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Compute/availabilitySets/read |가용성 집합의 속성 읽기 |
| Microsoft.Compute/virtualMachines/*/read |가상 컴퓨터(VM 크기, 런타임 상태, VM 확장 등)의 속성 읽기 |
| Microsoft.Compute/virtualMachines/deallocate/action |가상 컴퓨터 할당 취소 |
| Microsoft.Compute/virtualMachines/read |가상 컴퓨터의 속성 읽기 |
| Microsoft.Compute/virtualMachines/restart/action |가상 컴퓨터 다시 시작 |
| Microsoft.Compute/virtualMachines/start/action |가상 컴퓨터 시작 |
| Microsoft.DevTestLab/*/read |랩의 속성 읽기 |
| Microsoft.DevTestLab/labs/createEnvironment/action |랩 환경 만들기 |
| Microsoft.DevTestLab/labs/formulas/delete |수식 삭제 |
| Microsoft.DevTestLab/labs/formulas/read |수식 읽기 |
| Microsoft.DevTestLab/labs/formulas/write |수식 추가 또는 수정 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |랩 정책 평가 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |부하 분산 장치 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |부하 분산 장치 인바운드 NAT 규칙 연결 |
| Microsoft.Network/networkInterfaces/*/read |네트워크 인터페이스(예: 네트워크 인터페이스의 일부인 모든 부하 분산 장치)의 속성 읽기 |
| Microsoft.Network/networkInterfaces/join/action |네트워크 인터페이스에 가상 컴퓨터 연결 |
| Microsoft.Network/networkInterfaces/read |네트워크 인터페이스 읽기 |
| Microsoft.Network/networkInterfaces/write |네트워크 인터페이스 작성 |
| Microsoft.Network/publicIPAddresses/*/read |공용 IP 주소의 속성 읽기 |
| Microsoft.Network/publicIPAddresses/join/action |공용 IP 주소 연결 |
| Microsoft.Network/publicIPAddresses/read |네트워크 공용 IP 주소 읽기 |
| Microsoft.Network/virtualNetworks/subnets/join/action |가상 네트워크 연결 |
| Microsoft.Resources/deployments/operations/read |배포 작업 읽기 |
| Microsoft.Resources/deployments/read |배포 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/listKeys/action |저장소 계정 키 나열 |

### <a name="dns-zone-contributor"></a>DNS 영역 참여자
DNS 영역 및 레코드를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/\*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/\* |경고 규칙 만들기 및 관리 |
| Microsoft.Network/dnsZones/\* |DNS 영역 및 레코드 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/\* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/\* |지원 티켓 만들기 및 관리 |

### <a name="documentdb-account-contributor"></a>DocumentDB 계정 참여자
DocumentDB 계정을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.DocumentDb/databaseAccounts/* |DocumentDB 계정 만들기 및 관리 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="intelligent-systems-account-contributor"></a>지능형 시스템 계정 참여자
지능형 시스템 계정을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.IntelligentSystems/accounts/* |지능형 시스템 계정 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="network-contributor"></a>네트워크 참여자
모든 네트워크 리소스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.Network/* |네트워크 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="new-relic-apm-account-contributor"></a>NewRelic APM 계정 참여자
New Relic 응용 프로그램 성능 관리 계정 및 응용 프로그램을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |
| NewRelic.APM/accounts/* |New Relic 응용 프로그램 성능 관리 계정 만들기 및 관리 |

### <a name="owner"></a>소유자
액세스를 제외한 모든 것을 관리할 수 있음

| **actions** |  |
| --- | --- |
| * |모든 유형의 리소스 만들기 및 관리 |

### <a name="reader"></a>판독기
모든 항목을 볼 수 있지만 변경할 수는 없음

| **actions** |  |
| --- | --- |
| */read |암호를 제외한 모든 유형의 리소스를 읽습니다. |

### <a name="redis-cache-contributor"></a>Redis 캐시 참여자
Redis 캐시를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Cache/redis/* |Redis 캐시 만들기 및 관리 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="scheduler-job-collections-contributor"></a>스케줄러 작업 컬렉션 참여자
스케줄러 작업 컬렉션을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Scheduler/jobcollections/* |스케줄러 작업 컬렉션 만들기 및 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="search-service-contributor"></a>검색 서비스 참여자
검색 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Search/searchServices/* |검색 서비스 만들기 및 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="security-manager"></a>보안 관리자
보안 구성 요소, 보안 정책 및 가상 컴퓨터를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.ClassicCompute/*/read |클래식 계산 가상 컴퓨터에 대한 구성 정보 읽기 |
| Microsoft.ClassicCompute/virtualMachines/*/write |가상 컴퓨터에 대한 구성 작성 |
| Microsoft.ClassicNetwork/*/read |클래식 네트워크에 대한 구성 정보 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Security/* |보안 구성 요소 및 정책 만들기 및 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="sql-db-contributor"></a>SQL DB 참여자
해당 보안 관련 정책을 제외한 SQL 데이터베이스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* |경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Sql/servers/databases/* |SQL 데이터베이스 만들기 및 관리 |
| Microsoft.Sql/servers/read |SQL Server 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingSettings/* |감사 설정을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditRecords/read |감사 레코드를 읽을 수 없음 |
| Microsoft.Sql/servers/databases/connectionPolicies/* |연결 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |데이터 마스킹 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |보안 경고 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityMetrics/* |보안 메트릭을 편집할 수 없음 |

### <a name="sql-security-manager"></a>SQL 보안 관리자
SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Microsoft 권한 부여 읽기 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Sql/servers/auditingPolicies/* |SQL 서버 감사 정책 만들기 및 관리 |
| Microsoft.Sql/servers/auditingSettings/* |SQL 서버 감사 설정 만들기 및 관리 |
| Microsoft.Sql/servers/databases/auditingPolicies/* |SQL 서버 데이터베이스 감사 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/auditingSettings/* |SQL 서버 데이터베이스 감사 설정 만들기 및 관리 |
| Microsoft.Sql/servers/databases/auditRecords/read |감사 레코드 읽기 |
| Microsoft.Sql/servers/databases/connectionPolicies/* |SQL 서버 데이터베이스 연결 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |SQL 서버 데이터베이스 데이터 마스킹 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/read |SQL 데이터베이스 읽기 |
| Microsoft.Sql/servers/databases/schemas/read |SQL 서버 데이터베이스 스키마 읽기 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |SQL 서버 데이터베이스 테이블 열 읽기 |
| Microsoft.Sql/servers/databases/schemas/tables/read |SQL 서버 데이터베이스 테이블 읽기 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |SQL 서버 데이터베이스 보안 경고 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/securityMetrics/* |SQL 서버 데이터베이스 보안 메트릭 만들기 및 관리 |
| Microsoft.Sql/servers/read |SQL Server 읽기 |
| Microsoft.Sql/servers/securityAlertPolicies/* |SQL 서버 보안 경고 정책 만들기 및 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="sql-server-contributor"></a>SQL Server 참여자
해당 보안 관련 정책을 제외한 SQL 서버 및 데이터베이스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Sql/servers/* |SQL 서버 만들기 및 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |SQL 서버 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/auditingSettings/* |SQL 서버 감사 설정을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingPolicies/* |SQL 서버 데이터베이스 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingSettings/* |SQL 서버 데이터베이스 감사 설정을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditRecords/read |감사 레코드를 읽을 수 없음 |
| Microsoft.Sql/servers/databases/connectionPolicies/* |SQL 서버 데이터베이스 연결 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |SQL 서버 데이터베이스 데이터 마스킹 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |SQL 서버 데이터베이스 보안 경고 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityMetrics/* |SQL 서버 데이터베이스 보안 메트릭을 편집할 수 없음 |
| Microsoft.Sql/servers/securityAlertPolicies/* |SQL 서버 보안 경고 정책을 편집할 수 없음 |

### <a name="classic-storage-account-contributor"></a>클래식 저장소 계정 참여자
클래식 저장소 계정을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.ClassicStorage/storageAccounts/* |저장소 계정 만들기 및 관리 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="storage-account-contributor"></a>저장소 계정 참여자
저장소 계정을 관리할 수 있지만 액세스할 수 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |모든 권한 부여 읽기 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/diagnosticSettings/* |진단 설정 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/* |저장소 계정 만들기 및 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="user-access-administrator"></a>사용자 액세스 관리자
Azure 리소스에 대한 사용자 액세스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| */read |암호를 제외한 모든 유형의 리소스를 읽습니다. |
| Microsoft.Authorization/* |권한 부여 관리 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="classic-virtual-machine-contributor"></a>클래식 가상 컴퓨터 참여자
클래식 가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.ClassicCompute/domainNames/* |클래식 계산 도메인 이름 만들기 및 관리 |
| Microsoft.ClassicCompute/virtualMachines/* |가상 컴퓨터 만들기 및 관리 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |네트워크 보안 그룹 연결 |
| Microsoft.ClassicNetwork/reservedIps/link/action |예약된 IP 연결 |
| Microsoft.ClassicNetwork/reservedIps/read |예약된 IP 주소 읽기 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |가상 네트워크 연결 |
| Microsoft.ClassicNetwork/virtualNetworks/read |가상 네트워크 읽기 |
| Microsoft.ClassicStorage/storageAccounts/disks/read |저장소 계정 디스크 읽기 |
| Microsoft.ClassicStorage/storageAccounts/images/read |저장소 계정 이미지 읽기 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |저장소 계정 키 나열 |
| Microsoft.ClassicStorage/storageAccounts/read |클래식 저장소 계정 읽기 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="virtual-machine-contributor"></a>가상 컴퓨터 참여자
가상 컴퓨터를 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.Compute/availabilitySets/* |계산 가용성 집합 만들기 및 관리 |
| Microsoft.Compute/locations/* |계산 위치 만들기 및 관리 |
| Microsoft.Compute/virtualMachines/* |가상 컴퓨터 만들기 및 관리 |
| Microsoft.Compute/virtualMachineScaleSets/* |가상 컴퓨터 크기 집합 만들기 및 관리 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |네트워크 응용 프로그램 게이트웨이 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |부하 분산 장치 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |부하 분산 장치 인바운드 NAT 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |부하 분산 장치 인바운드 NAT 규칙 연결 |
| Microsoft.Network/loadBalancers/read |부하 분산 장치 읽기 |
| Microsoft.Network/locations/* |네트워크 위치 만들기 및 관리 |
| Microsoft.Network/networkInterfaces/* |네트워크 인터페이스 만들기 및 관리 |
| Microsoft.Network/networkSecurityGroups/join/action |네트워크 보안 그룹 연결 |
| Microsoft.Network/networkSecurityGroups/read |네트워크 보안 그룹 읽기 |
| Microsoft.Network/publicIPAddresses/join/action |네트워크 공용 IP 주소 연결 |
| Microsoft.Network/publicIPAddresses/read |네트워크 공용 IP 주소 읽기 |
| Microsoft.Network/virtualNetworks/read |가상 네트워크 읽기 |
| Microsoft.Network/virtualNetworks/subnets/join/action |가상 네트워크 서브넷 연결 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/listKeys/action |저장소 계정 키 나열 |
| Microsoft.Storage/storageAccounts/read |저장소 계정 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="classic-network-contributor"></a>클래식 네트워크 참여자
클래식 가상 네트워크 및 예약된 IP를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.ClassicNetwork/* |클래식 네트워크 만들기 및 관리 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |

### <a name="web-plan-contributor"></a>웹 계획 참여자
웹 계획을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |
| Microsoft.Web/serverFarms/* |서버 팜 만들기 및 관리 |

### <a name="website-contributor"></a>웹 사이트 참여자
웹 사이트를 관리할 수 있으나 여기에 연결된 웹 계획은 관리할 수 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read |읽기 권한 부여 |
| Microsoft.Insights/alertRules/* |Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/components/* |Insights 구성 요소 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read |리소스 상태 읽기 |
| Microsoft.Resources/deployments/* |리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read |리소스 그룹 읽기 |
| Microsoft.Support/* |지원 티켓 만들기 및 관리 |
| Microsoft.Web/certificates/* |웹 사이트 인증서 만들기 및 관리 |
| Microsoft.Web/listSitesAssignedToHostName/read |호스트 이름에 할당된 사이트 읽기 |
| Microsoft.Web/serverFarms/join/action |서버 팜 연결 |
| Microsoft.Web/serverFarms/read |서버 팜 읽기 |
| Microsoft.Web/sites/* |웹 사이트 만들기 및 관리(사이트 만들기도 관련 App Service 계획에 대한 쓰기 권한이 필요) |

## <a name="see-also"></a>참고 항목
* [역할 기반 액세스 제어](role-based-access-control-configure.md): Azure 포털에서 RBAC를 통해 시작합니다.
* [Azure RBAC에서 사용자 지정 역할](role-based-access-control-custom-roles.md): 액세스 요구 사항에 맞게 사용자 지정 역할을 만드는 방법에 대해 알아봅니다.
* [액세스 변경 기록 보고서 만들기](role-based-access-control-access-change-history-report.md): RBAC에서 역할 할당 변경을 추적합니다.
* [역할 기반 액세스 제어 문제 해결](role-based-access-control-troubleshooting.md): 일반적인 문제를 수정하기 위한 제안 사항을 봅니다.



<!--HONumber=Feb17_HO1-->


