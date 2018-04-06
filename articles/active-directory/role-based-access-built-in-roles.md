---
title: Azure RBAC(역할 기반 액세스 제어)의 기본 제공 역할 | Microsoft Docs
description: 이 항목에서는 역할 기반 액세스 제어(RBAC)에 대한 기본 제공 역할에 대해 설명합니다. 역할은 지속적으로 추가되므로 설명서가 최신 상태인지 확인합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 0e8f1d97f51fbfedbca1619ef5e7f28a3a0570b9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어의 기본 제공 역할
Azure 역할 기반 Access Control(RBAC)에는 사용자, 그룹 및 서비스에 할당할 수 있는 다음 기본 제공 역할이 포함되었습니다. 기본 제공 역할의 정의는 수정할 수 없습니다. 그러나 조직의 특정 요구 사항에 맞게 [Azure RBAC에서 사용자 지정 역할](role-based-access-control-custom-roles.md) 을 만들 수 있습니다.

## <a name="built-in-role-descriptions"></a>기본 제공 역할 설명
다음 테이블은 기본 제공 역할을 간략하게 설명합니다. 역할 이름을 클릭하면 역할에 대한 **작업** 및 **작업 안 함** 목록을 자세히 볼 수 있습니다. **작업** 속성은 Azure 리소스에 허용되는 작업을 지정합니다. 작업 문자열에는 와일드카드 문자를 사용할 수 있습니다. **작업 안 함** 속성은 허용된 작업에서 제외되는 작업을 지정합니다.

이 동작은 지정된 리소스 형식에 대해 수행할 수 있는 작업의 유형을 정의합니다. 예: 
- **쓰기**를 사용하여 PUT, POST, PATCH 및 DELETE 작업을 수행할 수 있습니다.
- **읽기**를 사용하여 GET 작업을 수행할 수 있습니다.

이 문서에서는 현재 존재하는 다양한 역할만 소개합니다. 그렇지만 사용자에게 역할을 할당할 때 범위를 정의하여 허용되는 동작을 추가로 제한할 수 있습니다. 이러한 기능은 누군가를 단일 리소스 그룹에 대해서만 웹 사이트 참가자로 지정하려는 경우에 유용합니다.

> [!NOTE]
> Azure 역할 정의는 끊임없이 진화하고 있습니다. 이 문서는 가능한 최신 상태로 유지되지만 Azure PowerShell에서 항상 최신 역할 정의를 확인할 수 있습니다. 현재의 모든 역할을 나열하려면 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) cmdlet을 사용합니다. `(get-azurermroledefinition "<role name>").actions` 또는 `(get-azurermroledefinition "<role name>").notactions`를 적절히 사용하여 특정 역할을 자세히 알아볼 수 있습니다. 특정 Azure 리소스 공급자의 작업을 나열하려면 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation)을 사용합니다.


| 기본 제공 역할 | 설명 |
| --- | --- |
| [소유자](#owner) | 액세스를 제외한 모든 것을 관리할 수 있음 |
| [참여자](#contributor) | 액세스를 제외한 모든 것을 관리할 수 있음 |
| [판독기](#reader) | 모든 항목을 볼 수 있지만 변경할 수는 없음 |
| [API Management 서비스 참여자](#api-management-service-contributor) | API Management 서비스를 관리할 수 있음 |
| [API Management 서비스 운영자 역할](#api-management-service-operator-role) | API Management 서비스를 관리할 수 있음 |
| [Azure API Management 읽기 권한자 역할](#api-management-service-reader-role) | API Management 서비스를 관리할 수 있음 |
| [Application Insights 구성 요소 참여자](#application-insights-component-contributor) | Application Insights 구성 요소를 관리할 수 있음 |
| [Application Insights 스냅숏 디버거](#application-insights-snapshot-debugger) | 사용자에게 Application Insights 스냅숏 디버거 기능을 사용할 수 있는 권한을 부여합니다. |
| [Automation 작업 연산자](#automation-job-operator) | Automation Runbook을 사용하여 작업을 만들고 관리합니다. |
| [Automation 운영자](#automation-operator) | 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있음 |
| [Automation Runbook 연산자](#automation-runbook-operator) | Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. |
| [Azure Stack 등록 소유자](#azure-stack-registration-owner) | Azure Stack 등록을 관리할 수 있습니다. |
| [Backup 참여자](#backup-contributor) | Recovery Services 자격 증명 모음 생성 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 백업 관리 작업을 관리할 수 있습니다. |
| [Backup 운영자](#backup-operator) | 자격 증명 모음 생성, 백업 제거 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 백업 관리 작업을 관리할 수 있습니다. |
| [Backup 읽기 권한자](#backup-reader) | Recovery Services 자격 증명 모음의 백업 관리를 모니터링할 수 있습니다. |
| [청구 읽기 권한자](#billing-reader) | 대금 청구 정보를 볼 수 있음 |
| [BizTalk 참여자](#biztalk-contributor) | BizTalk 서비스를 관리할 수 있음 |
| [CDN 엔드포인트 참가자](#cdn-endpoint-contributor) | CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
| [CDN 엔드포인트 독자](#cdn-endpoint-reader) | CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. |
| [CDN 프로필 참가자](#cdn-profile-contributor) | CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. |
| [CDN 프로필 독자](#cdn-profile-reader) | CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. |
| [클래식 네트워크 참여자](#classic-network-contributor) | 클래식 가상 네트워크 및 예약된 IP를 관리할 수 있음 |
| [클래식 Storage 계정 참여자](#classic-storage-account-contributor) | 클래식 저장소 계정을 관리할 수 있음 |
| [클래식 저장소 계정 키 운영자 서비스 역할](#classic-storage-account-key-operator-service-role) | 클래식 저장소 계정 키 운영자가 클래식 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다. |
| [Classic Virtual Machine 참여자](#classic-virtual-machine-contributor) | 클래식 가상 머신을 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음 |
| [ClearDB MySQL DB 참여자](#cleardb-mysql-db-contributor) | ClearDB MySQL 데이터베이스를 관리할 수 있음 |
| [Cosmos DB 계정 독자 역할](#cosmos-db-account-reader-role) | Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. |
| [데이터 팩터리 참여자](#data-factory-contributor) | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
| [Data Lake Analytics 개발자](#data-lake-analytics-developer) | 사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. |
| [DevTest Lab 사용자](#devtest-labs-user) | 모든 항목을 볼 수 있으며 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있음 |
| [DNS 영역 참여자](#dns-zone-contributor) | DNS 영역 및 레코드를 관리할 수 있음 |
| [DocumentDB 계정 참여자](#documentdb-account-contributor) | Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. |
| [지능형 시스템 계정 참여자](#intelligent-systems-account-contributor) | 지능형 시스템 계정을 관리할 수 있음 |
| [키 자격 증명 모음 참가자](#key-vault-contributor) | 키 자격 증명 모음을 관리할 수 있지만 액세스할 수는 없습니다. |
| [랩 작성자](#lab-creator) | Azure Lab 계정 하에서 관리 랩을 만들고, 관리하고, 삭제할 수 있습니다. |
| [Log Analytics 참가자](#log-analytics-contributor) | Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 저장소 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. |
| [Log Analytics 독자](#log-analytics-reader) | Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. |
| [논리 앱 참가자](#logic-app-contributor) | 논리 앱을 관리할 수 있지만 액세스할 수는 없습니다. |
| [논리 앱 운영자](#logic-app-operator) | 논리 앱을 읽고, 설정하고, 해제할 수 있습니다. |
| [관리 ID 참가자](#managed-identity-contributor) | 사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다. |
| [관리 ID 운영자](#managed-identity-operator) | 사용자 할당 ID를 읽고 할당합니다. |
| [Monitoring Contributor](#monitoring-contributor) | 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. |
| [Monitoring Reader](#monitoring-reader) | 모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. |
| [네트워크 참여자](#network-contributor) | 모든 네트워크 리소스를 관리할 수 있음 |
| [NewRelic APM 계정 참여자](#new-relic-apm-account-contributor) | New Relic Application Performance Management 계정을 관리할 수 있지만 액세스할 수는 없습니다. |
| [Redis 캐시 참여자](#redis-cache-contributor) | Redis 캐시를 관리할 수 있음 |
| [Scheduler 작업 컬렉션 참여자](#scheduler-job-collections-contributor) | Scheduler 작업 컬렉션을 관리할 수 있음 |
| [Search 서비스 참여자](#search-service-contributor) | Search 서비스를 관리할 수 있음 |
| [보안 관리자](#security-admin) | Security Center에서만: 보안 정책 보기, 보안 상태 보기, 보안 정책 편집, 경고 및 권장 사항 보기, 경고 및 권장 사항 해제 |
| [보안 관리자](#security-manager) | 보안 구성 요소, 보안 정책 및 가상 머신을 관리할 수 있음 |
| [보안 판독기](#security-reader) | Security Center에서만: 권장 사항 및 경고 보기, 보안 정책 보기, 보안 상태 보기 가능, 변경 불가 |
| [Site Recovery 참가자](#site-recovery-contributor) | Recovery Services 자격 증명 모음 생성 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 Site Recovery 관리 작업을 관리할 수 있음 |
| [Site Recovery 운영자](#site-recovery-operator) | 장애 조치(failover) 및 장애 복구(failback)는 가능하지만 다른 Site Recovery 관리 작업을 수행하거나 다른 사용자에게 액세스 권한을 할당할 수는 없음 |
| [Site Recovery 구독자](#site-recovery-reader) | Recovery Services 자격 증명 모음의 Site Recovery 상태를 모니터링하고 지원 티켓을 생성할 수 있음 |
| [SQL DB 참여자](#sql-db-contributor) | 해당 보안 관련 정책을 제외한 SQL 데이터베이스를 관리할 수 있음 |
| [SQL 보안 관리자](#sql-security-manager) | SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있음 |
| [SQL Server 참여자](#sql-server-contributor) | 해당 보안 관련 정책을 제외한 SQL 서버 및 데이터베이스를 관리할 수 있음 |
| [Storage 계정 참여자](#storage-account-contributor) | 저장소 계정을 관리할 수 있지만 액세스할 수 없습니다. |
| [저장소 계정 키 운영자 서비스 역할](#storage-account-key-operator-service-role) | 저장소 계정 키 운영자가 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다. |
| [지원 요청 참가자](#support-request-contributor) | 구독 범위에서 지원 티켓을 만들고 관리할 수 있음 |
| [Traffic Manager 참가자](#traffic-manager-contributor) | Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. |
| [사용자 액세스 관리자](#user-access-administrator) | Azure 리소스에 대한 사용자 액세스를 관리할 수 있음 |
| [가상 머신 관리자 로그인](#virtual-machine-administrator-login) | - 이 역할이 할당된 사용자는 Windows 관리자 또는 Linux 루트 사용자 권한으로 가상 머신에 로그인할 수 있습니다. |
| [Virtual Machine 참여자](#virtual-machine-contributor) | 가상 머신을 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음 |
| [가상 머신 사용자 로그인](#virtual-machine-user-login) | 이 역할이 할당된 사용자는 가상 머신에 일반 사용자로 로그인 할 수 있습니다. |
| [웹 계획 참여자](#web-plan-contributor) | 웹 계획을 관리할 수 있음 |
| [웹 사이트 참여자](#website-contributor) | 웹 사이트를 관리할 수 있으나 여기에 연결된 웹 계획은 관리할 수 없음 |

다음 표에서는 각 역할에 부여되는 특정 권한에 대해 설명합니다. 여기에는 권한을 부여하는 **작업**과 권한을 제한하는 **작업 안 함**이 포함될 수 있습니다.

## <a name="owner"></a>소유자
액세스를 제외한 모든 것을 관리할 수 있음

| **actions** |  |
| --- | --- |
| * | 모든 유형의 리소스 만들기 및 관리 |

## <a name="contributor"></a>참가자
액세스를 제외한 모든 것을 관리할 수 있음

| **actions** |  |
| --- | --- |
| * | 모든 유형의 리소스 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | 역할 및 역할 할당을 삭제할 수 없음 |
| Microsoft.Authorization/*/Write | 역할 및 역할 할당을 만들 수 없음 |
| Microsoft.Authorization/elevateAccess/Action | 테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한을 부여합니다. |

## <a name="reader"></a>판독기
모든 항목을 볼 수 있지만 변경할 수는 없음

| **actions** |  |
| --- | --- |
| */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |

## <a name="api-management-service-contributor"></a>API Management 서비스 참여자
API Management 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | API Management 서비스 만들기 및 관리 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 역할 및 역할 할당 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="api-management-service-operator-role"></a>API Management 서비스 운영자 역할
API Management 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | API Management 서비스 인스턴스 읽기 |
| Microsoft.ApiManagement/service/backup/action | 사용자가 제공한 저장소 계정의 지정된 컨테이너로 API Management 서비스 백업 |
| Microsoft.ApiManagement/service/delete | API Management 서비스 인스턴스 삭제 |
| Microsoft.ApiManagement/service/managedeployments/action | SKU/단위 변경, API Management 서비스의 지역별 배포를 추가 또는 제거 |
| Microsoft.ApiManagement/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
| Microsoft.ApiManagement/service/restore/action | 사용자가 제공한 저장소 계정의 지정된 컨테이너에서 API Management 서비스 복원 |
| Microsoft.ApiManagement/service/updatecertificate/action | API Management 서비스에 대한 SSL 인증서를 업로드합니다. |
| Microsoft.ApiManagement/service/updatehostname/action | API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거 |
| Microsoft.ApiManagement/service/write | API Management 서비스의 새 인스턴스 만들기 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 역할 및 역할 할당 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | 사용자 키의 목록을 가져옵니다. |

## <a name="api-management-service-reader-role"></a>Azure API Management 읽기 권한자 역할
API Management 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | API Management 서비스 인스턴스 읽기 |
| Microsoft.ApiManagement/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 역할 및 역할 할당 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | 사용자 키의 목록을 가져옵니다. |

## <a name="application-insights-component-contributor"></a>Application Insights 구성 요소 참여자
Application Insights 구성 요소를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/components/* | Insights 구성 요소 만들기 및 관리 |
| Microsoft.Insights/webtests/* | 웹 테스트 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="application-insights-snapshot-debugger"></a>Application Insights 스냅숏 디버거
사용자에게 Application Insights 스냅숏 디버거 기능을 사용할 수 있는 권한을 부여합니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="automation-job-operator"></a>Automation 작업 연산자
Automation Runbook을 사용하여 작업을 만들고 관리합니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Automation/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
| Microsoft.Automation/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="automation-operator"></a>Automation 운영자
작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Automation/automationAccounts/jobs/read | 자동화 계정 작업 읽기 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | 자동화 계정 작업 다시 시작 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | 자동화 계정 작업 중지 |
| Microsoft.Automation/automationAccounts/jobs/streams/read | 자동화 계정 작업 스트림 읽기 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | 자동화 계정 작업 일시 중단 |
| Microsoft.Automation/automationAccounts/jobs/write | 자동화 계정 작업 쓰기 |
| Microsoft.Automation/automationAccounts/jobSchedules/read | 자동화 계정 작업 일정 읽기 |
| Microsoft.Automation/automationAccounts/jobSchedules/write | 자동화 계정 작업 일정 읽기 |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | 자동화 계정에 연결된 작업 영역 가져오기 |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
| Microsoft.Automation/automationAccounts/read | 자동화 계정 읽기 |
| Microsoft.Automation/automationAccounts/runbooks/read | 자동화 Runbook 읽기 |
| Microsoft.Automation/automationAccounts/schedules/read | 자동화 계정 일정 읽기 |
| Microsoft.Automation/automationAccounts/schedules/write | 자동화 계정 일정 쓰기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="automation-runbook-operator"></a>Automation Runbook 연산자
Runbook 작업을 만들려면 Runbook 속성을 읽어보세요.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="azure-stack-registration-owner"></a>Azure Stack 등록 소유자
Azure Stack 등록을 관리할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace 제품에 대한 세부 정보 검색 |
| Microsoft.AzureStack/registrations/products/read | Azure Stack Marketplace 제품의 속성 가져오기 |
| Microsoft.AzureStack/registrations/read | Azure Stack 등록의 속성 가져오기 |

## <a name="backup-contributor"></a>Backup 참여자
Recovery Services 자격 증명 모음 생성 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 백업 관리 작업을 관리할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
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
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | 내보내기 작업의 작업 결과를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="backup-operator"></a>Backup 운영자
자격 증명 모음 생성, 백업 제거 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 백업 관리 작업을 관리할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 백업 관리에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | 보호 컨테이너에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | 백업된 항목에 대해 주문형 백업 작업 수행 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | 백업된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | 백업된 항목의 복구 지점 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | 백업된 항목의 복구 지점을 사용하여 복원 작업 수행 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | 백업 항목 만들기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 백업 항목을 보유하는 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 작업을 취소합니다. |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 작업의 작업 결과를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | 모든 작업 개체를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 백업 작업을 Excel로 내보내기 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | 백업 관리와 관련된 메타데이터 읽기 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 백업 정책에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | 백업 정책 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 모든 보호 가능한 항목 목록을 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 백업 항목을 보유하는 백업된 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
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
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 프로비전합니다. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 취소합니다. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | 내보내기 작업의 작업 결과를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="backup-reader"></a>Backup 읽기 권한자
Recovery Services 자격 증명 모음의 백업 관리를 모니터링할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 백업 관리에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | 보호 컨테이너에 대한 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | 백업된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 백업 항목을 보유하는 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 백업 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | 백업 작업 읽기 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 백업 작업을 Excel로 내보내기 |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | 백업 관리와 관련된 메타데이터 읽기 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | 백업 관리 작업 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 백업 정책에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | 백업 정책 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 백업된 항목 읽기 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 백업 항목을 보유하는 백업된 컨테이너 읽기 |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | 자격 증명 모음과 관련된 확장된 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 새로 만든 컨테이너를 가져오기 위한 검색 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 자격 증명 모음의 등록된 항목에 대해 수행된 작업의 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 자격 증명 모음의 등록된 항목 읽기 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | 내보내기 작업의 작업 결과를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음의 사용 현황 읽기 |

## <a name="billing-reader"></a>청구 읽기 권한자
대금 청구 정보를 볼 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Billing/*/read | 대금 청구 정보 읽기 |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="biztalk-contributor"></a>BizTalk 참여자
BizTalk 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.BizTalkServices/BizTalk/* | BizTalk 서비스 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="cdn-endpoint-contributor"></a>CDN 엔드포인트 참가자
CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="cdn-endpoint-reader"></a>CDN 엔드포인트 독자
CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="cdn-profile-contributor"></a>CDN 프로필 참가자
CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="cdn-profile-reader"></a>CDN 프로필 독자
CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="classic-network-contributor"></a>클래식 네트워크 참여자
클래식 가상 네트워크 및 예약된 IP를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.ClassicNetwork/* | 클래식 네트워크 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="classic-storage-account-contributor"></a>클래식 Storage 계정 참여자
클래식 저장소 계정을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.ClassicStorage/storageAccounts/* | 저장소 계정 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="classic-storage-account-key-operator-service-role"></a>클래식 저장소 계정 키 운영자 서비스 역할
클래식 저장소 계정 키 운영자가 클래식 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | 저장소 계정의 액세스 키를 나열합니다. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | 저장소 계정에 대한 기존 액세스 키를 다시 생성합니다. |

## <a name="classic-virtual-machine-contributor"></a>클래식 Virtual Machine 참여자
클래식 가상 머신을 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.ClassicCompute/domainNames/* | 클래식 계산 도메인 이름 만들기 및 관리 |
| Microsoft.ClassicCompute/virtualMachines/* | 가상 머신 만들기 및 관리 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | 네트워크 보안 그룹 연결 |
| Microsoft.ClassicNetwork/reservedIps/link/action | 예약된 IP 연결 |
| Microsoft.ClassicNetwork/reservedIps/read | 예약된 IP 주소 읽기 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 가상 네트워크 연결 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.ClassicStorage/storageAccounts/disks/read | 저장소 계정 디스크 읽기 |
| Microsoft.ClassicStorage/storageAccounts/images/read | 저장소 계정 이미지 읽기 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정 키 나열 |
| Microsoft.ClassicStorage/storageAccounts/read | 클래식 저장소 계정 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB 참여자
ClearDB MySQL 데이터베이스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| successbricks.cleardb/databases/* | ClearDB MySQL 데이터베이스 만들기 및 관리 |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB 계정 독자 역할
Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 역할 및 역할 할당은 각 사용자에게 부여된 사용 권한을 읽을 수 있습니다. |
| Microsoft.DocumentDB/*/read | 컬렉션 읽기 |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 읽기 전용 키 읽기 창 |
| Microsoft.Insights/MetricDefinitions/read | 메트릭 정의 읽기 |
| Microsoft.Insights/Metrics/read | 계정 메트릭 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="data-factory-contributor"></a>데이터 팩터리 참여자
데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.DataFactory/dataFactories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics 개발자
사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | DataLakeAnalytics 계정을 삭제합니다. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다. |
| Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics 계정을 만들거나 업데이트합니다. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | 방화벽 규칙을 만들거나 업데이트합니다. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | 방화벽 규칙을 삭제합니다. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | 계산 정책을 만들거나 업데이트합니다. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | 계산 정책을 삭제합니다. |

## <a name="devtest-labs-user"></a>DevTest Lab 사용자
모든 항목을 볼 수 있으며 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Compute/availabilitySets/read | 가용성 집합의 속성 읽기 |
| Microsoft.Compute/virtualMachines/*/read | 가상 머신(VM 크기, 런타임 상태, VM 확장 등)의 속성 읽기 |
| Microsoft.Compute/virtualMachines/deallocate/action | 가상 머신 할당 취소 |
| Microsoft.Compute/virtualMachines/read | 가상 머신의 속성 읽기 |
| Microsoft.Compute/virtualMachines/restart/action | 가상 머신 다시 시작 |
| Microsoft.Compute/virtualMachines/start/action | 가상 머신 시작 |
| Microsoft.DevTestLab/*/read | 랩의 속성 읽기 |
| Microsoft.DevTestLab/labs/createEnvironment/action | 랩 환경 만들기 |
| Microsoft.DevTestLab/labs/claimAnyVm/action | 랩에서 임의 클레임 가능 가상 머신을 클레임합니다. |
| Microsoft.DevTestLab/labs/formulas/delete | 수식 삭제 |
| Microsoft.DevTestLab/labs/formulas/read | 수식 읽기 |
| Microsoft.DevTestLab/labs/formulas/write | 수식 추가 또는 수정 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | 랩 정책 평가 |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | 기존 가상 머신의 소유권을 가져옵니다. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙 연결 |
| Microsoft.Network/networkInterfaces/*/read | 네트워크 인터페이스(예: 네트워크 인터페이스의 일부인 모든 부하 분산 장치)의 속성 읽기 |
| Microsoft.Network/networkInterfaces/join/action | 네트워크 인터페이스에 Virtual Machine 연결 |
| Microsoft.Network/networkInterfaces/read | 네트워크 인터페이스 읽기 |
| Microsoft.Network/networkInterfaces/write | 네트워크 인터페이스 작성 |
| Microsoft.Network/publicIPAddresses/*/read | 공용 IP 주소의 속성 읽기 |
| Microsoft.Network/publicIPAddresses/join/action | 공용 IP 주소 연결 |
| Microsoft.Network/publicIPAddresses/read | 네트워크 공용 IP 주소 읽기 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크 연결 |
| Microsoft.Resources/deployments/operations/read | 배포 작업 읽기 |
| Microsoft.Resources/deployments/read | 배포 읽기 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/listKeys/action | 저장소 계정 키 나열 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | 가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다. |

## <a name="dns-zone-contributor"></a>DNS 영역 참여자
DNS 영역 및 레코드를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Network/dnsZones/* | DNS 영역 및 레코드 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="documentdb-account-contributor"></a>DocumentDB 계정 참여자
Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB 계정 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="intelligent-systems-account-contributor"></a>지능형 시스템 계정 참여자
지능형 시스템 계정을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.IntelligentSystems/accounts/* | 지능형 시스템 계정 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="key-vault-contributor"></a>키 자격 증명 모음 참가자
키 자격 증명 모음을 관리할 수 있지만 액세스할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | 일시 삭제된 Key Vault를 제거합니다. |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>랩 작성자
Azure Lab 계정 하에서 관리 랩을 만들고, 관리하고, 삭제할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.ManagedLab/labAccounts/createLab/action | 랩 계정에서 랩을 만듭니다. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="log-analytics-contributor"></a>Log Analytics 참가자
Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 저장소 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다.

| **actions** |  |
| --- | --- |
| */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정의 액세스 키를 나열합니다. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="log-analytics-reader"></a>Log Analytics 독자
Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다.

| **actions** |  |
| --- | --- |
| */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
| Microsoft.OperationalInsights/workspaces/search/action | 검색 쿼리를 실행합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |

## <a name="logic-app-contributor"></a>논리 앱 참가자
논리 앱을 관리할 수 있지만 액세스할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 저장소 계정의 액세스 키를 나열합니다. |
| Microsoft.ClassicStorage/storageAccounts/read | 지정된 계정의 저장소 계정을 반환합니다. |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
| Microsoft.Insights/logdefinitions/* | 이 권한은 사용자 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
| Microsoft.Insights/metricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
| Microsoft.Logic/* | Logic Apps 리소스를 관리합니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Storage/storageAccounts/listkeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
| Microsoft.Storage/storageAccounts/read | 저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Web/connectionGateways/* | 연결 게이트웨이를 만들고 관리합니다. |
| Microsoft.Web/connections/* | 연결을 만들고 관리합니다. |
| Microsoft.Web/customApis/* | 사용자 지정 API를 만들고 관리합니다. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
| Microsoft.Web/sites/functions/listSecrets/action | 비밀 Web Apps 함수를 나열합니다. |

## <a name="logic-app-operator"></a>논리 앱 운영자
논리 앱을 읽고, 설정하고, 해제할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/*/read | Insights 경고 규칙을 읽습니다. |
| Microsoft.Insights/diagnosticSettings/*/read | Logic Apps에 대한 진단 설정을 가져옵니다. |
| Microsoft.Insights/metricDefinitions/*/read | Logic Apps에 사용 가능한 메트릭을 가져옵니다. |
| Microsoft.Logic/*/read | Logic Apps 리소스를 읽습니다. |
| Microsoft.Logic/workflows/disable/action | 워크플로를 사용하지 않도록 설정합니다. |
| Microsoft.Logic/workflows/enable/action | 워크플로를 사용하도록 설정합니다. |
| Microsoft.Logic/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
| Microsoft.Resources/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
| Microsoft.Resources/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Web/connectionGateways/*/read | 연결 게이트웨이를 읽습니다. |
| Microsoft.Web/connections/*/read | 연결을 읽습니다. |
| Microsoft.Web/customApis/*/read | 사용자 지정 API를 읽습니다. |
| Microsoft.Web/serverFarms/read | App Service 계획의 속성을 가져옵니다. |

## <a name="managed-identity-contributor"></a>관리 ID 참가자
사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다.

| **actions** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="managed-identity-operator"></a>관리 ID 운영자
사용자 할당 ID를 읽고 할당합니다.

| **actions** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="monitoring-contributor"></a>Monitoring Contributor
모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요.

| **actions** |  |
| --- | --- |
| */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | 경고 규칙 읽기/쓰기/삭제 |
| Microsoft.Insights/components/* | Application Insights 구성 요소 읽기/쓰기/삭제 |
| Microsoft.Insights/DiagnosticSettings/* | 진단 설정 읽기/쓰기/삭제 |
| Microsoft.Insights/eventtypes/* | 구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다. |
| Microsoft.Insights/LogDefinitions/* | 이 권한은 사용자 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
| Microsoft.Insights/MetricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
| Microsoft.Insights/Metrics/* | 리소스에 대한 메트릭을 읽습니다. |
| Microsoft.Insights/Register/Action | Microsoft Insights 공급자 등록 |
| Microsoft.Insights/webtests/* | Application Insights 웹 테스트 읽기/쓰기/삭제 |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Log Analytics 솔루션 팩 읽기/쓰기/삭제 |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Log Analytics의 저장된 검색 읽기/쓰기/삭제 |
| Microsoft.OperationalInsights/workspaces/search/action | Log Analytics 작업 영역 검색 |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Log Analytics 작업 영역에 대한 키 나열 |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Log Analytics 저장소 정보 구성 읽기/쓰기/삭제 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Monitoring Reader
모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요.

| **actions** |  |
| --- | --- |
| */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
| Microsoft.OperationalInsights/workspaces/search/action | Log Analytics 데이터 검색 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="network-contributor"></a>네트워크 참여자
모든 네트워크 리소스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Network/* | 네트워크 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="new-relic-apm-account-contributor"></a>NewRelic APM 계정 참여자
New Relic Application Performance Management 계정을 관리할 수 있지만 액세스할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Redis 캐시 참여자
Redis 캐시를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Cache/redis/* | Redis 캐시 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="scheduler-job-collections-contributor"></a>Scheduler 작업 컬렉션 참여자
Scheduler 작업 컬렉션을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Scheduler/jobcollections/* | 스케줄러 작업 컬렉션 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="search-service-contributor"></a>Search 서비스 참여자
Search 서비스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Search/searchServices/* | 검색 서비스 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="security-admin"></a>보안 관리자
Security Center에서만: 보안 정책 보기, 보안 상태 보기, 보안 정책 편집, 경고 및 권장 사항 보기, 경고 및 권장 사항 해제

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Authorization/policyAssignments/* | 정책 할당 만들기 및 관리 |
| Microsoft.Authorization/policyDefinitions/* | 정책 정의 만들기 및 관리 |
| Microsoft.Authorization/policySetDefinitions/* | 정책 집합 만들기 및 관리 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.operationalInsights/workspaces/*/read | Log Analytics 데이터 보기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Security/*/read | 보안 구성 요소 및 정책 읽기 |
| Microsoft.Security/locations/alerts/dismiss/action | 보안 경고를 해제합니다. |
| Microsoft.Security/locations/tasks/dismiss/action | 보안 권장 사항을 해제합니다. |
| Microsoft.Security/policies/write | 보안 규칙을 업데이트합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="security-manager"></a>보안 관리자
보안 구성 요소, 보안 정책 및 가상 머신을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.ClassicCompute/*/read | 클래식 가상 머신에 대한 구성 정보 읽기 |
| Microsoft.ClassicCompute/virtualMachines/*/write | 클래식 가상 머신에 대한 구성 정보 쓰기 |
| Microsoft.ClassicNetwork/*/read | 클래식 네트워크에 대한 구성 정보 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Security/* | 보안 구성 요소 및 정책 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="security-reader"></a>보안 판독기
Security Center에서만: 권장 사항 및 경고 보기, 보안 정책 보기, 보안 상태 보기 가능, 변경 불가

| **actions** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.operationalInsights/workspaces/*/read | Log Analytics 데이터 보기 |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Security/*/read | 보안 구성 요소 및 정책 읽기 |

## <a name="site-recovery-contributor"></a>Site Recovery 참가자
Recovery Services 자격 증명 모음 생성 및 다른 사용자에게 액세스 권한 부여를 제외한 모든 Site Recovery 관리 작업을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/Vaults/certificates/write | 자격 증명 모음 자격 증명 인증서 업데이트 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 새로 만든 컨테이너를 가져오기 위한 검색 작업 관리 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | 복제 경고 설정 만들기 또는 업데이트 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | 복제 이벤트 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | 복제 패브릭 만들기 및 관리 |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | 복제 정책 만들기 및 관리 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 복구 계획 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services 자격 증명 모음의 저장소 구성 만들기 및 관리 |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services 자격 증명 모음 토큰 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음 사용 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Recovery Services 자격 증명 모음 알림 구성 읽기 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="site-recovery-operator"></a>Site Recovery 운영자
장애 조치(failover) 및 장애 복구(failback)는 가능하지만 다른 Site Recovery 관리 작업을 수행하거나 다른 사용자에게 액세스 권한을 할당할 수는 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | 자격 증명 모음과 관련된 확장된 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 새로 만든 컨테이너를 가져오기 위한 검색 작업 관리 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 상태 및 제출된 작업 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 리소스에 대해 등록된 컨테이너 읽기 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 복제 경고 설정 읽기 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | 복제 이벤트 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 패브릭의 일관성 검사 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | 복제 패브릭 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 복제 게이트웨이 다시 연결 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 복제 패브릭 인증서 갱신 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 복제 패브릭 네트워크 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | 복제 패브릭 네트워크 매핑 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 보호 컨테이너 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 모든 항목 목록 가져오기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 복구 지점을 적용합니다. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 장애 조치(Failover) 커밋 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 계획된 장애 조치(Failover) |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | 보호된 모든 항목 목록 가져오기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 사용 가능한 복구 지점 목록 가져오기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 복제를 복구합니다. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | 보호된 항목에 대해 다시 보호 시작 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | 보호된 항목에 대해 테스트 장애 조치(failover) 시작 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 테스트 장애 조치(Failover) 정리 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 장애 조치(failover) |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 모바일 서비스를 업데이트합니다. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services 공급자 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Recovery Services 공급자 새로 고침 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | 복제 패브릭에 대한 저장소 분류 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | 저장소 분류 매핑 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 등록된 vCenter 정보 읽기 |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | 복제 정책 읽기 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 복구 계획 장애 조치(failover)에 대한 장애 조치(failover) 커밋 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 복구 계획 장애 조치(failover) 시작 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 복구 계획 읽기 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 복구 계획에 대한 다시 보호 시작 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 복구 계획에 대한 테스트 장애 조치(failover) 시작 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 복구 계획 테스트 장애 조치(failover) 정리 시작 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 복구 계획에 대해 계획되지 않은 장애 조치(failover) 시작 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Recovery Services 자격 증명 모음 알림 구성 읽기 |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Recovery Services 자격 증명 모음의 저장소 구성 읽기 |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services 자격 증명 모음 토큰 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음 사용 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="site-recovery-reader"></a>Site Recovery 구독자
Recovery Services 자격 증명 모음의 Site Recovery 상태를 모니터링하고 지원 티켓을 생성할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | 자격 증명 모음과 관련된 확장된 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read | Recovery Services 자격 증명 모음 알림 구성 읽기 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services 자격 증명 모음 읽기 |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 새로 만든 컨테이너를 가져오기 위한 검색 작업 관리 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 작업 상태 및 제출된 작업 결과 읽기 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | 리소스에 대해 등록된 컨테이너 읽기 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 복제 경고 설정 읽기 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | 복제 이벤트 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | 복제 패브릭 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 복제 패브릭 네트워크 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | 복제 패브릭 네트워크 매핑 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 보호 컨테이너 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 모든 항목 목록 가져오기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | 보호된 모든 항목 목록 가져오기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 사용 가능한 복구 지점 목록 가져오기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services 공급자 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | 복제 패브릭에 대한 저장소 분류 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | 저장소 분류 매핑 읽기 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 등록된 vCenter 정보 읽기 |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | 복제 작업의 상태 읽기 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | 복제 정책 읽기 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 복구 계획 읽기 |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Recovery Services 자격 증명 모음의 저장소 구성 읽기 |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services 자격 증명 모음 토큰 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음 사용 정보 읽기 |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="sql-db-contributor"></a>SQL DB 참여자
해당 보안 관련 정책을 제외한 SQL 데이터베이스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | SQL 데이터베이스 만들기 및 관리 |
| Microsoft.Sql/servers/read | SQL Server 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingSettings/* | 감사 설정을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditRecords/read | 감사 레코드를 읽을 수 없음 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 연결 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 데이터 마스킹 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | 보안 경고 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 보안 메트릭을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL 보안 관리자
SQL 서버 및 데이터베이스의 보안 관련 정책을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Microsoft 권한 부여 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 저장소 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Sql/servers/auditingPolicies/* | SQL 서버 감사 정책 만들기 및 관리 |
| Microsoft.Sql/servers/auditingSettings/* | SQL 서버 감사 설정 만들기 및 관리 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 만들기 및 관리 |
| Microsoft.Sql/servers/databases/auditRecords/read | 감사 레코드 읽기 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/read | SQL 데이터베이스 읽기 |
| Microsoft.Sql/servers/databases/schemas/read | SQL 서버 데이터베이스 스키마 읽기 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | SQL 서버 데이터베이스 테이블 열 읽기 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | SQL 서버 데이터베이스 테이블 읽기 |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 만들기 및 관리 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 만들기 및 관리 |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | SQL Server 읽기 |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="sql-server-contributor"></a>SQL Server 참여자
해당 보안 관련 정책을 제외한 SQL 서버 및 데이터베이스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | SQL 서버 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | SQL 서버 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/auditingSettings/* | SQL 서버 감사 설정을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | SQL 서버 데이터베이스 감사 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/auditRecords/read | 감사 레코드를 읽을 수 없음 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | SQL 서버 데이터베이스 연결 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭을 편집할 수 없음 |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책을 편집할 수 없음 |

## <a name="storage-account-contributor"></a>Storage 계정 참여자
저장소 계정을 관리할 수 있지만 액세스할 수 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 모든 권한 부여 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/diagnosticSettings/* | 진단 설정 관리 |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 저장소 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/* | 저장소 계정 만들기 및 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="storage-account-key-operator-service-role"></a>저장소 계정 키 운영자 서비스 역할
저장소 계정 키 운영자가 저장소 계정에서 키를 나열하고 다시 생성할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | 지정된 저장소 계정에 대한 액세스 키를 반환합니다. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | 지정된 저장소 계정에 대한 액세스 키를 다시 생성합니다. |

## <a name="support-request-contributor"></a>지원 요청 참가자
구독 범위에서 지원 티켓을 만들고 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 역할 및 역할 할당 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="traffic-manager-contributor"></a>Traffic Manager 참가자
Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 역할 및 역할 할당 읽기 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="user-access-administrator"></a>사용자 액세스 관리자
Azure 리소스에 대한 사용자 액세스를 관리할 수 있음

| **actions** |  |
| --- | --- |
| */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
| Microsoft.Authorization/* | 권한 부여 관리 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="virtual-machine-administrator-login"></a>가상 머신 관리자 로그인
-   이 역할이 할당된 사용자는 Windows 관리자 또는 Linux 루트 사용자 권한으로 가상 머신에 로그인할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Virtual Machine 참가자
가상 머신을 관리할 수 있으나 여기에 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Compute/availabilitySets/* | 계산 가용성 집합 만들기 및 관리 |
| Microsoft.Compute/locations/* | 계산 위치 만들기 및 관리 |
| Microsoft.Compute/virtualMachines/* | 가상 머신 만들기 및 관리 |
| Microsoft.Compute/virtualMachineScaleSets/* | 가상 머신 크기 집합 만들기 및 관리 |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | 네트워크 응용 프로그램 게이트웨이 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | 부하 분산 장치 인바운드 NAT 풀 연결 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙 연결 |
| Microsoft.Network/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. |
| Microsoft.Network/loadBalancers/read | 부하 분산 장치 읽기 |
| Microsoft.Network/locations/* | 네트워크 위치 만들기 및 관리 |
| Microsoft.Network/networkInterfaces/* | 네트워크 인터페이스 만들기 및 관리 |
| Microsoft.Network/networkSecurityGroups/join/action | 네트워크 보안 그룹 연결 |
| Microsoft.Network/networkSecurityGroups/read | 네트워크 보안 그룹 읽기 |
| Microsoft.Network/publicIPAddresses/join/action | 네트워크 공용 IP 주소 연결 |
| Microsoft.Network/publicIPAddresses/read | 네트워크 공용 IP 주소 읽기 |
| Microsoft.Network/virtualNetworks/read | 가상 네트워크 읽기 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 가상 네트워크 서브넷 연결 |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | 백업 보호 항목을 만듭니다. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | 보호 정책을 만듭니다. |
| Microsoft.RecoveryServices/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
| Microsoft.RecoveryServices/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Storage/storageAccounts/listKeys/action | 저장소 계정 키 나열 |
| Microsoft.Storage/storageAccounts/read | 저장소 계정 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |

## <a name="virtual-machine-user-login"></a>가상 머신 사용자 로그인
이 역할이 할당된 사용자는 가상 머신에 일반 사용자로 로그인 할 수 있습니다.

| **actions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>웹 계획 참여자
웹 계획을 관리할 수 있음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Web/serverFarms/* | 서버 팜 만들기 및 관리 |

## <a name="website-contributor"></a>웹 사이트 참여자
웹 사이트를 관리할 수 있으나 여기에 연결된 웹 계획은 관리할 수 없음

| **actions** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 읽기 권한 부여 |
| Microsoft.Insights/alertRules/* | Insights 경고 규칙 만들기 및 관리 |
| Microsoft.Insights/components/* | Insights 구성 요소 만들기 및 관리 |
| Microsoft.ResourceHealth/availabilityStatuses/read | 리소스 상태 읽기 |
| Microsoft.Resources/deployments/* | 리소스 그룹 배포 만들기 및 관리 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 읽기 |
| Microsoft.Support/* | 지원 티켓 만들기 및 관리 |
| Microsoft.Web/certificates/* | 웹 사이트 인증서 만들기 및 관리 |
| Microsoft.Web/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트 읽기 |
| Microsoft.Web/serverFarms/join/action | 서버 팜 연결 |
| Microsoft.Web/serverFarms/read | 서버 팜 읽기 |
| Microsoft.Web/sites/* | 웹 사이트 만들기 및 관리(사이트 만들기도 관련 App Service 계획에 대한 쓰기 권한이 필요) |

## <a name="see-also"></a>참고 항목
* [역할 기반 Access Control](role-based-access-control-configure.md): Azure Portal에서 RBAC를 통해 시작합니다.
* [Azure RBAC에서 사용자 지정 역할](role-based-access-control-custom-roles.md): 액세스 요구 사항에 맞게 사용자 지정 역할을 만드는 방법에 대해 알아봅니다.
* [액세스 변경 기록 보고서 만들기](role-based-access-control-access-change-history-report.md): RBAC에서 역할 할당 변경을 추적합니다.
* [역할 기반 Access Control 문제 해결](role-based-access-control-troubleshooting.md): 일반적인 문제를 수정하기 위한 제안 사항을 봅니다.
* [Azure Security Center의 권한](../security-center/security-center-permissions.md)
