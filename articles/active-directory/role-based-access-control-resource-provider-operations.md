---
title: "Azure Resource Manager 공급자 작업 | Microsoft Docs"
description: "Microsoft Azure Resource Manager 리소스 공급자에서 사용 가능한 작업에 대한 세부 정보"
services: active-directory
documentationcenter: 
author: jboeshart
manager: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 95c6a1e9cfadeb19a0ec5a53fffb15ef220bfec0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 리소스 공급자 작업

이 문서에서는 각 Microsoft Azure Resource Manager 리소스 공급자에 대해 사용할 수 있는 작업을 설명합니다. 이러한 작업은 Azure의 리소스에 대해 보다 세분화된 RBAC(역할 기반 액세스 제어) 권한을 제공하기 위해 사용자 지정 역할에서 사용할 수 있습니다. 여기에 제공되는 목록이 전체 목록은 아니며 각 공급자가 업데이트될 때 작업이 추가되거나 제거될 수 있습니다. 작업 문자열은 `Microsoft.<ProviderName>/<ChildResourceType>/<action>` 형식을 따릅니다. 전체 및 현재 목록을 보려면 `Get-AzureRmProviderOperation`(PowerShell) 또는 `azure provider operations show`(Azure CLI)를 사용하여 Azure 리소스 공급자에 대한 작업을 나열합니다.

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| 작업 | 설명 |
|---|---|
|/configuration/action|테넌트 구성을 업데이트합니다.|
|/services/action|테넌트의 서비스 인스턴스를 업데이트합니다.|
|/configuration/write|테넌트 구성을 만듭니다.|
|/configuration/read|테넌트 구성을 읽습니다.|
|/services/write|테넌트의 서비스 인스턴스를 만듭니다.|
|/services/read|테넌트의 서비스 인스턴스를 읽습니다.|
|/services/delete|테넌트의 서비스 인스턴스를 삭제합니다.|
|/services/servicemembers/action|서비스의 서비스 멤버 인스턴스를 만듭니다.|
|/services/servicemembers/read|서비스의 서비스 멤버 인스턴스를 읽습니다.|
|/services/servicemembers/delete|서비스의 서비스 멤버 인스턴스를 삭제합니다.|
|/services/servicemembers/alerts/read|서비스 멤버에 대한 경고를 읽습니다.|
|/services/alerts/read|서비스에 대한 경고를 읽습니다.|
|/services/alerts/read|서비스에 대한 경고를 읽습니다.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| 작업 | 설명 |
|---|---|
|/generateRecommendations/action|권장 사항을 생성합니다.|
|/suppressions/action|비표시 오류(Suppression)를 만들거나 업데이트합니다.|
|/register/action|Microsoft Advisor에 대한 구독을 등록합니다.|
|/generateRecommendations/read|권장 사항 생성 상태를 가져옵니다.|
|/recommendations/read|권장 사항을 읽습니다.|
|/suppressions/read|비표시 오류(Suppression)를 가져옵니다.|
|/suppressions/delete|비표시 오류(Suppression)를 삭제합니다.|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| 작업 | 설명 |
|---|---|
|/servers/read|지정된 Analysis Server의 정보를 검색합니다.|
|/servers/write|지정된 Analysis Server를 만들거나 업데이트합니다.|
|/servers/delete|Analysis Server를 삭제합니다.|
|/servers/suspend/action|Analysis Server를 일시 중단합니다.|
|/servers/resume/action|Analysis Server를 계속합니다.|
|/servers/checkNameAvailability<br>/action|지정된 Analysis Server 이름이 올바른지와 사용되고 있지 않은지 확인합니다.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|제공된 서비스 이름을 사용할 수 있는지 확인합니다.|
|/register/action|Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록합니다.|
|/unregister/action|Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록 취소합니다.|
|/service/write|API Management 서비스의 새 인스턴스를 만듭니다.|
|/service/read|API Management 서비스 인스턴스에 대한 메타데이터를 읽습니다.|
|/service/delete|API Management 서비스 인스턴스를 삭제합니다.|
|/service/updatehostname/action|API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다.|
|/service/uploadcertificate/action|API Management 서비스에 대한 SSL 인증서를 업로드합니다.|
|/service/backup/action|사용자가 제공한 저장소 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다.|
|/service/restore/action|사용자가 제공한 저장소 계정의 지정된 컨테이너에서 API Management 서비스를 복원합니다.|
|/service/managedeployments/action|SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다.|
|/service/getssotoken/action|API Management 서비스 레거시 포털에 관리자로 로그인하는 데 사용할 수 있는 SSO 토큰을 가져옵니다.|
|/service/applynetworkconfigurationupdates/action|가상 네트워크에서 실행되는 Microsoft.ApiManagement 리소스를 업데이트하여 업데이트된 네트워크 설정을 선택합니다.|
|/service/operationresults/read|장기 실행 작업의 현재 상태를 가져옵니다.|
|/service/networkStatus/read|리소스의 네트워크 액세스 상태를 가져옵니다.|
|/service/loggers/read|로거 목록을 가져오거나 로거 세부 정보를 가져옵니다.|
|/service/loggers/write|새 로거를 추가하거나 기존 로거 세부 정보를 업데이트합니다.|
|/service/loggers/delete|기존 로거를 제거합니다.|
|/service/users/read|등록된 사용자 목록을 가져오거나 사용자의 계정 세부 정보를 가져옵니다.|
|/service/users/write|새 사용자를 등록하거나 기존 사용자의 계정 세부 정보를 업데이트합니다.|
|/service/users/delete|사용자 계정을 제거합니다.|
|/service/users/generateSsoUrl/action|SSO URL을 생성합니다. 이 URL은 관리 포털에 액세스하는 데 사용할 수 있습니다.|
|/service/users/subscriptions/read|사용자 구독 목록을 가져옵니다.|
|/service/users/keys/read|사용자 키의 목록을 가져옵니다.|
|/service/users/groups/read|사용자 그룹의 목록을 가져옵니다.|
|/service/tenant/operationResults/read|작업 결과 목록을 가져오거나 특정 작업의 결과를 가져옵니다.|
|/service/tenant/policy/read|테넌트에 대한 정책 구성을 가져옵니다.|
|/service/tenant/policy/write|테넌트에 대한 정책 구성을 설정합니다.|
|/service/tenant/policy/delete|테넌트에 대한 정책 구성을 제거합니다.|
|/service/tenant/configuration/save/action|리포지토리의 지정된 분기에 대한 구성 스냅숏을 사용하여 커밋을 만듭니다.|
|/service/tenant/configuration/deploy/action|배포 작업을 실행하여 지정된 git 분기에서 데이터베이스의 구성에 대해 변경된 내용을 적용합니다.|
|/service/tenant/configuration/validate/action|지정된 git 분기에서 변경된 내용이 유효한지 검사합니다.|
|/service/tenant/configuration/operationResults/read|작업 결과 목록을 가져오거나 특정 작업의 결과를 가져옵니다.|
|/service/tenant/configuration/syncState/read|마지막 git 동기화의 상태를 가져옵니다.|
|/service/tenant/access/read|테넌트 액세스 세부 정보를 가져옵니다.|
|/service/tenant/access/write|테넌트 액세스 세부 정보를 업데이트합니다.|
|/service/tenant/access/regeneratePrimaryKey/action|기본 액세스 키를 다시 생성합니다.|
|/service/tenant/access/regenerateSecondaryKey/action|보조 액세스 키를 다시 생성합니다.|
|/service/identityProviders/read|ID 공급자 목록을 가져오거나 ID 공급자의 세부 정보를 가져옵니다.|
|/service/identityProviders/write|새 ID 공급자를 만들거나 기존 ID 공급자의 세부 정보를 업데이트합니다.|
|/service/identityProviders/delete|기존 ID 공급자를 제거합니다.|
|/service/subscriptions/read|제품 구독 목록을 가져오거나 제품 구독 세부 정보를 가져옵니다.|
|/service/subscriptions/write|기존 사용자를 기존 제품에 구독하거나 기존 구독 세부 정보를 업데이트합니다. 이 작업은 구독을 갱신하는 데 사용할 수 있습니다.|
|/service/subscriptions/delete|구독을 삭제합니다. 이 작업은 구독을 삭제하는 데 사용할 수 있습니다.|
|/service/subscriptions/regeneratePrimaryKey/action|구독 기본 키를 다시 생성합니다.|
|/service/subscriptions/regenerateSecondaryKey/action|구독 보조 키를 다시 생성합니다.|
|/service/backends/read|백 엔드의 목록을 가져오거나 백 엔드의 세부 정보를 가져옵니다.|
|/service/backends/write|새 백 엔드를 추가하거나 기존 백 엔드 세부 정보를 업데이트합니다.|
|/service/backends/delete|기존 백 엔드를 제거합니다.|
|/service/apis/read|등록된 모든 API의 목록을 가져오거나 API의 세부 정보를 가져옵니다.|
|/service/apis/write|새 API를 만들거나 기존 API의 세부 정보를 업데이트합니다.|
|/service/apis/delete|기존 API를 제거합니다.|
|/service/apis/policy/read|API에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/apis/policy/write|API에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/apis/policy/delete|API에서 정책 구성을 제거합니다.|
|/service/apis/operations/read|기존 API 작업 목록을 가져오거나 API 작업의 세부 정보를 가져옵니다.|
|/service/apis/operations/write|새 API 작업을 만들거나 기존 API 작업을 업데이트합니다.|
|/service/apis/operations/delete|기존 API 작업을 제거합니다.|
|/service/apis/operations/policy/read|작업에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/apis/operations/policy/write|작업에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/apis/operations/policy/delete|작업에서 정책 구성을 제거합니다.|
|/service/products/read|제품 목록을 가져오거나 제품의 세부 정보를 가져옵니다.|
|/service/products/write|새 제품을 만들거나 기존 제품 세부 정보를 업데이트합니다.|
|/service/products/delete|기존 제품을 제거합니다.|
|/service/products/subscriptions/read|제품 구독 목록을 가져옵니다.|
|/service/products/apis/read|기존 제품에 추가되는 API 목록을 가져옵니다.|
|/service/products/apis/write|기존 제품에 기존 API를 추가합니다.|
|/service/products/apis/delete|기존 제품에서 기존 API를 제거합니다.|
|/service/products/policy/read|기존 제품의 정책 구성을 가져옵니다.|
|/service/products/policy/write|기존 제품의 정책 구성을 설정합니다.|
|/service/products/policy/delete|기존 제품에서 정책 구성을 제거합니다.|
|/service/products/groups/read|제품과 연결된 개발자 그룹 목록을 가져옵니다.|
|/service/products/groups/write|기존 개발자 그룹을 제품 기존에 연결합니다.|
|/service/products/groups/delete|기존 제품과의 기존 개발자 그룹 연결을 제거합니다.|
|/service/openidConnectProviders/read|OpenID Connect 공급자 목록을 가져오거나 OpenID Connect 공급자의 세부 정보를 가져옵니다.|
|/service/openidConnectProviders/write|새 OpenID Connect 공급자를 만들거나 기존 OpenID Connect 공급자의 세부 정보를 업데이트합니다.|
|/service/openidConnectProviders/delete|기존 OpenID Connect 공급자를 제거합니다.|
|/service/certificates/read|인증서의 목록을 가져오거나 인증서의 세부 정보를 가져옵니다.|
|/service/certificates/write|새 인증서를 추가합니다.|
|/service/certificates/delete|기존 인증서를 제거합니다.|
|/service/properties/read|모든 속성의 목록을 가져오거나 지정된 속성의 세부 정보를 가져옵니다.|
|/service/properties/write|새 속성을 만들거나 지정된 속성 값을 업데이트합니다.|
|/service/properties/delete|기존 속성을 제거합니다.|
|/service/groups/read|그룹 목록을 가져오거나 그룹의 세부 정보를 가져옵니다.|
|/service/groups/write|새 그룹을 만들거나 기존 그룹 세부 정보를 업데이트합니다.|
|/service/groups/delete|기존 그룹을 제거합니다.|
|/service/groups/users/read|그룹 사용자의 목록을 가져옵니다.|
|/service/groups/users/write|기존 그룹에 기존 사용자를 추가합니다.|
|/service/groups/users/delete|기존 그룹에서 기존 사용자를 제거합니다.|
|/service/authorizationServers/read|권한 부여 서버 목록을 가져오거나 권한 부여 서버의 세부 정보를 가져옵니다.|
|/service/authorizationServers/write|새 권한 부여 서버를 만들거나 기존 권한 부여 서버의 세부 정보를 업데이트합니다.|
|/service/authorizationServers/delete|기존 권한 부여 서버를 제거합니다.|
|/service/reports/bySubscription/read|구독별로 집계된 보고서를 가져옵니다.|
|/service/reports/byRequest/read|데이터 보고 요청을 가져옵니다.|
|/service/reports/byOperation/read|작업별로 집계된 보고서를 가져옵니다.|
|/service/reports/byGeo/read|지역별로 집계된 보고서를 가져옵니다.|
|/service/reports/byUser/read|개발자별로 집계된 보고서를 가져옵니다.|
|/service/reports/byTime/read|기간별로 집계된 보고서를 가져옵니다.|
|/service/reports/byApi/read|API별로 집계된 보고서를 가져옵니다.|
|/service/reports/byProduct/read|제품별로 집계된 보고서를 가져옵니다.|

## <a name="microsoftappservice"></a>Microsoft.AppService

| 작업 | 설명 |
|---|---|
|/appidentities/Read|게이트웨이에 등록된 리소스(웹 사이트)를 반환합니다.|
|/appidentities/Write|새 앱 ID를 만듭니다.|
|/appidentities/Delete|기존 앱 ID를 삭제합니다.|
|/deploymenttemplates/listMetadata/Action|API 앱 패키지와 연결된 UI 메타데이터를 나열합니다.|
|/deploymenttemplates/generate/Action|API 앱 인스턴스를 프로비전하는 배포 템플릿을 반환합니다.|
|/gateways/Read|게이트웨이 인스턴스를 반환합니다.|
|/gateways/Write|새 게이트웨이 만들거나 기존 게이트웨이를 업데이트합니다.|
|/gateways/Delete|기존 게이트웨이 인스턴스를 삭제합니다.|
|/gateways/listLoginUris/Action|토큰 저장소를 채우고 OAuth 로그인 URI를 반환합니다.|
|/gateways/listKeys/Action|게이트웨이 암호를 반환합니다.|
|/gateways/tokens/Write|지정한 이름을 가진 새 Zumo 토큰을 만듭니다.|
|/gateways/registrations/Read|게이트웨이에 등록된 리소스(웹 사이트)를 반환합니다.|
|/gateways/registrations/Write|게이트웨이에 리소스(웹 사이트)를 등록합니다.|
|/gateways/registrations/Delete|게이트웨이에 리소스(웹 사이트)를 등록 취소합니다.|
|/apiapps/Read|API 앱 인스턴스를 반환합니다.|
|/apiapps/Write|새 API 앱을 만들거나 기존 API 앱을 업데이트합니다.|
|/apiapps/Delete|기존 API 앱 인스턴스를 삭제합니다.|
|/apiapps/listStatus/Action|API 앱 상태를 반환합니다.|
|/apiapps/listKeys/Action|API 앱 암호를 반환합니다.|
|/apiapps/apidefinitions/Read|API 앱의 API 정의를 반환합니다.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| 작업 | 설명 |
|---|---|
|/elevateAccess/action|테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한을 부여합니다.|
|/classicAdministrators/read|구독에 대한 관리자를 읽습니다.|
|/classicAdministrators/write|구독에 대한 관리자를 추가 또는 수정합니다.|
|/classicAdministrators/delete|구독에서 관리자를 제거합니다.|
|/locks/read|지정된 범위에서 잠금을 가져옵니다.|
|/locks/write|지정된 범위에서 잠금을 추가합니다.|
|/locks/delete|지정된 범위에서 잠금을 삭제합니다.|
|/policyAssignments/read|정책 할당에 대한 정보를 가져옵니다.|
|/policyAssignments/write|지정된 범위에서 정책 할당을 만듭니다.|
|/policyAssignments/delete|지정된 범위에서 정책 할당을 삭제합니다.|
|/permissions/read|호출자가 지정된 범위에서 갖는 모든 사용 권한을 나열합니다.|
|/roleDefinitions/read|역할 정의에 대한 정보를 가져옵니다.|
|/roleDefinitions/write|지정된 사용 권한 및 할당 가능 범위를 사용하여 사용자 지정 역할 정의를 만들거나 업데이트합니다.|
|/roleDefinitions/delete|지정된 사용자 지정 역할 정의를 삭제합니다.|
|/providerOperations/read|역할 정의에 사용할 수 있는 모든 리소스 공급자에 대한 작업을 가져옵니다.|
|/policyDefinitions/read|정책 정의에 대한 정보를 가져옵니다.|
|/policyDefinitions/write|사용자 지정 정책 정의를 만듭니다.|
|/policyDefinitions/delete|정책 정의를 삭제합니다.|
|/roleAssignments/read|역할 할당에 대한 정보를 가져옵니다.|
|/roleAssignments/write|지정된 범위에서 역할 할당을 만듭니다.|
|/roleAssignments/delete|지정된 범위에서 역할 할당을 삭제합니다.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| 작업 | 설명 |
|---|---|
|/automationAccounts/read|Azure Automation 계정을 가져옵니다.|
|/automationAccounts/write|Azure Automation 계정을 만들거나 업데이트합니다.|
|/automationAccounts/delete|Azure Automation 계정을 삭제합니다.|
|/automationAccounts/configurations/readContent/action|Azure Automation DSC의 콘텐츠를 가져옵니다.|
|/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker 리소스를 읽습니다.|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Hybrid Runbook Worker 리소스를 삭제합니다.|
|/automationAccounts/jobSchedules/read|Azure Automation 작업 일정을 가져옵니다.|
|/automationAccounts/jobSchedules/write|Azure Automation 작업 일정을 만듭니다.|
|/automationAccounts/jobSchedules/delete|Azure Automation 작업 일정을 삭제합니다.|
|/automationAccounts/connectionTypes/read|Azure Automation 연결 형식 자산을 가져옵니다.|
|/automationAccounts/connectionTypes/write|Azure Automation 연결 형식 자산을 만듭니다.|
|/automationAccounts/connectionTypes/delete|Azure Automation 연결 형식 자산을 삭제합니다.|
|/automationAccounts/modules/read|Azure Automation 모듈을 가져옵니다.|
|/automationAccounts/modules/write|Azure Automation 모듈을 만들거나 업데이트합니다.|
|/automationAccounts/modules/delete|Azure Automation 모듈을 삭제합니다.|
|/automationAccounts/credentials/read|Azure Automation 자격 증명 자산을 가져옵니다.|
|/automationAccounts/credentials/write|Azure Automation 자격 증명 자산을 만들거나 업데이트합니다.|
|/automationAccounts/credentials/delete|Azure Automation 자격 증명 자산을 삭제합니다.|
|/automationAccounts/certificates/read|Azure Automation 인증서 자산을 가져옵니다.|
|/automationAccounts/certificates/write|Azure Automation 인증서 자산을 만들거나 업데이트합니다.|
|/automationAccounts/certificates/delete|Azure Automation 인증서 자산을 삭제합니다.|
|/automationAccounts/schedules/read|Azure Automation 일정 자산을 가져옵니다.|
|/automationAccounts/schedules/write|Azure Automation 일정 자산을 만들거나 업데이트합니다.|
|/automationAccounts/schedules/delete|Azure Automation 일정 자산을 삭제합니다.|
|/automationAccounts/jobs/read|Azure Automation 작업을 가져옵니다.|
|/automationAccounts/jobs/write|Azure Automation 작업을 만듭니다.|
|/automationAccounts/jobs/stop/action|Azure Automation 작업을 중지합니다.|
|/automationAccounts/jobs/suspend/action|Azure Automation 작업을 일시 중단합니다.|
|/automationAccounts/jobs/resume/action|Azure Automation 작업을 계속합니다.|
|/automationAccounts/jobs/runbookContent/action|작업 실행 시 Azure Automation runbook의 콘텐츠를 가져옵니다.|
|/automationAccounts/jobs/output/action|작업의 출력을 가져옵니다.|
|/automationAccounts/jobs/read|Azure Automation 작업을 가져옵니다.|
|/automationAccounts/jobs/write|Azure Automation 작업을 만듭니다.|
|/automationAccounts/jobs/stop/action|Azure Automation 작업을 중지합니다.|
|/automationAccounts/jobs/suspend/action|Azure Automation 작업을 일시 중단합니다.|
|/automationAccounts/jobs/resume/action|Azure Automation 작업을 계속합니다.|
|/automationAccounts/jobs/streams/read|Azure Automation 작업 스트림을 가져옵니다.|
|/automationAccounts/connections/read|Azure Automation 연결 자산을 가져옵니다.|
|/automationAccounts/connections/write|Azure Automation 연결 자산을 만들거나 업데이트합니다.|
|/automationAccounts/connections/delete|Azure Automation 연결 자산을 삭제합니다.|
|/automationAccounts/variables/read|Azure Automation 변수 자산을 읽습니다.|
|/automationAccounts/variables/write|Azure Automation 변수 자산을 만들거나 업데이트합니다.|
|/automationAccounts/variables/delete|Azure Automation 변수 자산을 삭제합니다.|
|/automationAccounts/runbooks/readContent/action|Azure Automation runbook의 콘텐츠를 가져옵니다.|
|/automationAccounts/runbooks/read|Azure Automation Runbook을 가져옵니다.|
|/automationAccounts/runbooks/write|Azure Automation Runbook을 만들거나 업데이트합니다.|
|/automationAccounts/runbooks/delete|Azure Automation Runbook을 삭제합니다.|
|/automationAccounts/runbooks/draft/readContent/action|Azure Automation Runbook 초안의 콘텐츠를 가져옵니다.|
|/automationAccounts/runbooks/draft/writeContent/action|Azure Automation Runbook 초안의 콘텐츠를 만듭니다.|
|/automationAccounts/runbooks/draft/read|Azure Automation Runbook 초안을 가져옵니다.|
|/automationAccounts/runbooks/draft/publish/action|Azure Automation Runbook 초안을 게시합니다.|
|/automationAccounts/runbooks/draft/undoEdit/action|Azure Automation Runbook 초안에 대한 편집을 실행 취소합니다.|
|/automationAccounts/runbooks/draft/testJob/read|Azure Automation Runbook 초안 테스트 작업을 가져옵니다.|
|/automationAccounts/runbooks/draft/testJob/write|Azure Automation Runbook 초안 테스트 작업을 만듭니다.|
|/automationAccounts/runbooks/draft/testJob/stop/action|Azure Automation Runbook 초안 테스트 작업을 중지합니다.|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Azure Automation Runbook 초안 테스트 작업을 일시 중단합니다.|
|/automationAccounts/runbooks/draft/testJob/resume/action|Azure Automation Runbook 초안 테스트 작업을 계속합니다.|
|/automationAccounts/webhooks/read|Azure Automation 웹후크를 읽습니다.|
|/automationAccounts/webhooks/write|Azure Automation 웹후크를 만들거나 업데이트합니다.|
|/automationAccounts/webhooks/delete|Azure Automation 웹후크를 삭제합니다. |
|/automationAccounts/webhooks/generateUri/action|Azure Automation 웹후크에 대한 URI를 생성합니다.|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

이 공급자는 전체 ARM 공급자가 아니며 ARM 작업을 제공하지 않습니다.

## <a name="microsoftbatch"></a>Microsoft.Batch

| 작업 | 설명 |
|---|---|
|/register/action|배치 리소스 공급자에 대한 구독을 등록하고 배치 계정을 만들도록 설정합니다.|
|/batchAccounts/write|새 배치 계정을 만들거나 기존 배치 계정을 업데이트합니다.|
|/batchAccounts/read|배치 계정을 나열하거나 배치 계정의 속성을 가져옵니다.|
|/batchAccounts/delete|배치 계정을 삭제합니다.|
|/batchAccounts/listkeys/action|배치 계정에 대한 액세스 키를 나열합니다.|
|/batchAccounts/regeneratekeys/action|배치 계정에 대한 액세스 키를 다시 생성합니다.|
|/batchAccounts/syncAutoStorageKeys/action|배치 계정에 대해 구성된 자동 저장소 계정의 액세스 키를 동기화합니다.|
|/batchAccounts/applications/read|응용 프로그램을 나열하거나 응용 프로그램의 속성을 가져옵니다.|
|/batchAccounts/applications/write|새 응용 프로그램을 만들거나 기존 응용 프로그램을 업데이트합니다.|
|/batchAccounts/applications/delete|응용 프로그램을 삭제합니다.|
|/batchAccounts/applications/versions/read|응용 프로그램 패키지의 속성을 가져옵니다.|
|/batchAccounts/applications/versions/write|새 응용 프로그램 패키지를 만들거나 기존 응용 프로그램 패키지를 업데이트합니다.|
|/batchAccounts/applications/versions/activate/action|응용 프로그램 패키지를 활성화합니다.|
|/batchAccounts/applications/versions/delete|응용 프로그램 패키지를 삭제합니다.|
|/locations/quotas/read|지정된 Azure 지역에서 지정된 구독의 배치 할당량을 가져옵니다.|

## <a name="microsoftbilling"></a>Microsoft.Billing

| 작업 | 설명 |
|---|---|
|/invoices/read|사용 가능한 청구서를 나열합니다.|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| 작업 | 설명 |
|---|---|
|/mapApis/Read|읽기 작업|
|/mapApis/Write|쓰기 작업|
|/mapApis/Delete|삭제 작업|
|/mapApis/regenerateKey/action|키 다시 생성|
|/mapApis/listSecrets/action|암호를 나열합니다.|
|/mapApis/listSingleSignOnToken/action|리소스에 대한 Single Sign-On 권한 부여 토큰을 읽습니다.|
|/Operations/read|작업에 대한 설명입니다.|

## <a name="microsoftcache"></a>Microsoft.Cache

| 작업 | 설명 |
|---|---|
|/checknameavailability/action|이름을 새 Redis Cache에서 사용할 수 있는지 확인합니다.|
|/register/action|‘Microsoft.Cache’ 리소스 공급자를 구독에 등록합니다.|
|/unregister/action|‘Microsoft.Cache’ 리소스 공급자를 구독에서 등록 취소합니다.|
|/redis/write|관리 콘솔에서 Redis Cache의 설정 및 구성을 수정합니다.|
|/redis/read|관리 콘솔에서 Redis Cache의 설정 및 구성을 봅니다.|
|/redis/delete|전체 Redis Cache를 삭제합니다.|
|/redis/listKeys/action|관리 포털에서 Redis Cache 액세스 키의 값을 봅니다.|
|/redis/regenerateKey/action|관리 포털에서 Redis Cache 액세스 키의 값을 변경합니다.|
|redis/import/action|여러 blob에서 지정된 형식의 데이터를 Redis로 가져옵니다.|
|/redis/export/action|Redis 데이터를 지정된 형식의 접두사 지정 저장소 blob으로 내보냅니다.|
|/redis/forceReboot/action|캐시 인스턴스를 강제로 다시 부팅합니다. 데이터가 손실될 수 있습니다.|
|/redis/stop/action|캐시 인스턴스를 중지합니다.|
|/redis/start/action|캐시 인스턴스를 시작합니다.|
|/redis/metricDefinitions/read|Redis Cache에 대해 사용 가능한 메트릭을 가져옵니다.|
|/redis/firewallRules/read|Redis Cache의 IP 방화벽 규칙을 가져옵니다.|
|/redis/firewallRules/write|Redis Cache의 IP 방화벽 규칙을 편집합니다.|
|/redis/firewallRules/delete|Redis Cache의 IP 방화벽 규칙을 삭제합니다.|
|/redis/listUpgradeNotifications/read|캐시 테넌트에 대한 최신 업그레이드 알림을 나열합니다.|
|/redis/linkedservers/read|Redis Cache와 관련된 연결된 서버를 가져옵니다.|
|/redis/linkedservers/write|Redis Cache에 연결된 서버를 추가합니다.|
|/redis/linkedservers/delete|Redis Cache에서 연결된 서버를 삭제합니다.|
|redis/patchSchedules/read|Redis Cache의 패치 일정을 가져옵니다.|
|/redis/patchSchedules/write|Redis Cache의 패치 일정을 수정합니다.|
|/redis/patchSchedules/delete|Redis Cache의 패치 일정을 삭제합니다.|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| 작업 | 설명 |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|서비스 앱 사용자에 대한 서비스 주체를 프로비전합니다.|
|/validateCertificateRegistrationInformation/Action|인증서 구매 개체를 제출하지 않고 유효성을 검사합니다.|
|/register/action|구독에 대한 Microsoft 인증서 리소스 공급자를 등록합니다.|
|/certificateOrders/Write|새 certificateOrder를 추가하거나 기존 certificateOrder를 업데이트합니다.|
|/certificateOrders/Delete|기존 AppServiceCertificate를 삭제합니다.|
|/certificateOrders/Read|CertificateOrders의 목록을 가져옵니다.|
|/certificateOrders/reissue/Action|기존 certificateorder를 재발급합니다.|
|/certificateOrders/renew/Action|기존 certificateorder를 갱신합니다.|
|/certificateOrders/retrieveCertificateActions/Action|인증서 작업의 목록을 검색합니다.|
|/certificateOrders/retrieveEmailHistory/Action|인증서 전자 메일 기록을 검색합니다.|
|/certificateOrders/resendEmail/Action|인증서 전자 메일을 다시 보냅니다.|
|/certificateOrders/verifyDomainOwnership/Action|도메인 소유권 확인|
|/certificateOrders/resendRequestEmails/Action|요청 전자 메일을 다른 전자 메일 주소로 다시 보냅니다.|
|/certificateOrders/resendRequestEmails/Action|발급된 App Service Certificate에 대한 사이트 봉인을 검색합니다.|
|/certificateOrders/certificates/Write|새 인증서를 추가하거나 기존 인증서를 업데이트합니다.|
|/certificateOrders/certificates/Delete|기존 인증서를 삭제합니다.|
|/certificateOrders/certificates/Read|인증서 목록을 가져옵니다.|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| 작업 | 설명 |
|---|---|
|/register/action|클래식 Compute에 등록|
|/checkDomainNameAvailability/action|지정된 도메인 이름의 가용성을 확인합니다.|
|/moveSubscriptionResources/action|모든 클래식 리소스를 다른 구독으로 이동합니다.|
|/validateSubscriptionMoveAvailability/action|클래식 이동 작업에 대한 구독의 가용성이 유효한지 확인합니다.|
|/operatingSystemFamilies/read|Microsoft Azure에서 사용할 수 있는 게스트 운영 체제 제품군을 나열하고 각 제품군에서 사용할 수 있는 운영 체제 버전도 나열합니다.
|/capabilities/read|기능을 표시합니다.|
|/operatingSystems/read|Microsoft Azure에서 현재 사용할 수 있는 게스트 운영 체제의 버전을 나열합니다.|
|/resourceTypes/skus/read|지원되는 리소스 형식에 대한 SKU 목록을 가져옵니다.|
|/domainNames/read|리소스에 대한 도메인 이름을 반환합니다.|
|/domainNames/write|리소스에 대한 도메인 이름을 추가 또는 수정합니다.|
|/domainNames/delete|리소스에 대한 도메인 이름을 제거합니다.|
|/domainNames/swap/action|스테이징 슬롯을 프로덕션 슬롯으로 바꿉니다.|
|/domainNames/serviceCertificates/read|사용된 서비스 인증서를 반환합니다.|
|/domainNames/serviceCertificates/write|사용된 서비스 인증서를 추가 또는 수정합니다.|
|/domainNames/serviceCertificates/delete|사용된 서비스 인증서를 삭제합니다.|
|/domainNames/serviceCertificates/operationStatuses/read|도메인 이름 서비스 인증서에 대한 작업 상태를 읽습니다.|
|/domainNames/capabilities/read|도메인 이름 기능을 표시합니다.|
|/domainNames/extensions/read|도메인 이름 확장을 반환합니다.|
|/domainNames/extensions/write|도메인 이름 확장을 추가합니다.|
|/domainNames/extensions/delete|도메인 이름 확장을 제거합니다.|
|/domainNames/extensions/operationStatuses/read|도메인 이름 확장에 대한 작업 상태를 읽습니다.|
|/domainNames/active/write|활성 도메인 이름을 설정합니다.|
|/domainNames/slots/read|배포 슬롯을 표시합니다.|
|/domainNames/slots/write|배포를 만들거나 업데이트합니다.|
|/domainNames/slots/delete|지정된 배포 슬롯을 삭제합니다.|
|/domainNames/slots/start/action|배포 슬롯을 시작합니다.|
|/domainNames/slots/stop/action|배포 슬롯을 일시 중단합니다.|
|/domainNames/slots/operationStatuses/read|도메인 이름 슬롯에 대한 작업 상태를 읽습니다.|
|/domainNames/slots/roles/read|배포 슬롯에 대한 역할을 가져옵니다.|
|/domainNames/slots/roles/extensionReferences/read|배포 슬롯 역할에 대한 확장 참조를 반환합니다.|
|/domainNames/slots/roles/extensionReferences/write|배포 슬롯 역할에 대한 확장 참조를 추가하거나 수정합니다.|
|/domainNames/slots/roles/extensionReferences/delete|배포 슬롯 역할에 대한 확장 참조를 제거합니다.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|도메인 이름 슬롯 확장 참조에 대한 작업 상태를 읽습니다.|
|/domainNames/slots/roles/roleInstances/read|역할 인스턴스를 가져옵니다.|
|/domainNames/slots/roles/roleInstances/restart/action|역할 인스턴스를 다시 시작합니다.|
|/domainNames/slots/roles/roleInstances/reimage/action|역할 인스턴스를 이미지로 다시 설치합니다.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|도메인 이름 슬롯 역할 인스턴스에 대한 작업 상태를 읽습니다.|
|/domainNames/slots/state/start/write|배포 슬롯 상태를 중지됨으로 변경합니다.|
|/domainNames/slots/state/stop/write|배포 슬롯 상태를 시작됨으로 변경합니다.|
|/domainNames/slots/upgradeDomain/write|도메인 업그레이드를 탐색합니다.|
|/domainNames/internalLoadBalancers/read|내부 부하 분산 장치를 가져옵니다.|
|/domainNames/internalLoadBalancers/write|새 내부 부하 분산 장치를 가져옵니다.|
|/domainNames/internalLoadBalancers/delete|새 내부 부하 분산 장치를 제거합니다.|
|/domainNames/internalLoadBalancers/operationStatuses/read|도메인 이름 내부 부하 분산 장치에 대한 작업 상태를 읽습니다.|
|/domainNames/loadBalancedEndpointSets/read|부하 분산된 끝점 집합을 표시합니다.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|도메인 이름 부하 분산 끝점 집합에 대한 작업 상태를 읽습니다.|
|/domainNames/availabilitySets/read|리소스에 대한 가용성 집합을 표시합니다.|
|/quotas/read|구독에 대한 할당량을 가져옵니다.|
|/virtualMachines/read|가상 컴퓨터 목록을 검색합니다.|
|/virtualMachines/write|가상 컴퓨터를 추가 또는 수정합니다.|
|/virtualMachines/delete|가상 컴퓨터를 제거합니다.|
|/virtualMachines/start/action|가상 컴퓨터를 시작합니다.|
|/virtualMachines/redeploy/action|가상 컴퓨터를 다시 배포합니다.|
|/virtualMachines/restart/action|가상 컴퓨터를 다시 시작합니다.|
|/virtualMachines/stop/action|가상 컴퓨터를 중지합니다.|
|/virtualMachines/shutdown/action|가상 컴퓨터를 종료합니다.|
|/virtualMachines/attachDisk/action|데이터 디스크를 가상 컴퓨터에 연결합니다.|
|/virtualMachines/detachDisk/action|데이터 디스크를 가상 컴퓨터에서 분리합니다.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|가상 컴퓨터에 대한 RDP 파일을 다운로드합니다.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|네트워크 인터페이스에 연결된 네트워크 보안 그룹을 가져옵니다.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|네트워크 인터페이스에 연결된 네트워크 보안 그룹을 추가합니다.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|네트워크 인터페이스에 연결된 네트워크 보안 그룹을 삭제합니다.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|네트워크 보안 그룹에 연결된 가상 컴퓨터에 대한 작업 상태를 읽습니다.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|메트릭 정의를 가져옵니다.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|진단 설정을 가져옵니다.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|진단 설정을 추가하거나 수정합니다.|
|/virtualMachines/metrics/read|메트릭을 가져옵니다.|
|/virtualMachines/operationStatuses/read|가상 컴퓨터의 작업 상태를 읽습니다.|
|/virtualMachines/extensions/read|가상 컴퓨터 확장을 가져옵니다.|
|/virtualMachines/extensions/write|가상 컴퓨터 확장을 배치합니다.|
|/virtualMachines/extensions/operationStatuses/read|가상 컴퓨터 확장의 작업 상태를 읽습니다.|
|/virtualMachines/asyncOperations/read|가능한 비동기 작업을 가져옵니다.|
|/virtualMachines/disks/read|데이터 디스크의 목록을 검색합니다.|
|/virtualMachines/associatedNetworkSecurityGroups/read|가상 컴퓨터와 연결된 네트워크 보안 그룹을 가져옵니다.|
|/virtualMachines/associatedNetworkSecurityGroups/write|가상 컴퓨터와 연결된 네트워크 보안 그룹을 추가합니다.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|가상 컴퓨터와 연결된 네트워크 보안 그룹을 삭제합니다.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|네트워크 보안 그룹에 연결된 가상 컴퓨터에 대한 작업 상태를 읽습니다.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| 작업 | 설명 |
|---|---|
|/register/action|클래식 네트워크에 등록합니다.|
|/gatewaySupportedDevices/read|지원되는 장치 목록을 검색합니다.|
|/reservedIps/read|예약된 IP를 가져옵니다.|
|/reservedIps/write|예약된 IP를 새로 추가합니다.|
|/reservedIps/delete|예약된 IP를 삭제합니다.|
|/reservedIps/link/action|예약된 IP를 연결합니다.|
|reservedIps/join/action|예약된 IP를 조인합니다.|
|/reservedIps/operationStatuses/read|예약된 IP의 작업 상태를 읽습니다.|
|/virtualNetworks/read|가상 네트워크를 가져옵니다.|
|/virtualNetworks/write|새 가상 네트워크를 추가합니다.|
|/virtualNetworks/delete|가상 네트워크를 삭제합니다.|
|/virtualNetworks/peer/action|가상 네트워크를 다른 가상 네트워크와 피어링합니다.|
|/virtualNetworks/join/action|가상 네트워크를 조인합니다.|
|/virtualNetworks/checkIPAddressAvailability/action|가상 네트워크에서 지정된 IP 주소의 가용성을 확인합니다.|
|/virtualNetworks/capabilities/read|기능을 표시합니다.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|서브넷과 연결된 네트워크 보안 그룹을 가져옵니다.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|서브넷과 연결된 네트워크 보안 그룹을 추가합니다.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|서브넷과 연결된 네트워크 보안 그룹을 삭제합니다.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|네트워크 보안 그룹과 연결된 가상 네트워크 서브넷에 대한 작업 상태를 읽습니다.|
|/virtualNetworks/operationStatuses/read|가상 네트워크의 작업 상태를 읽습니다.|
|/virtualNetworks/gateways/read|가상 네트워크 게이트웨이를 가져옵니다.|
|/virtualNetworks/gateways/write|가상 네트워크 게이트웨이를 추가합니다.|
|/virtualNetworks/gateways/delete|가상 네트워크 게이트웨이를 삭제합니다.|
|/virtualNetworks/gateways/startDiagnostics/action|가상 네트워크 게이트웨이에 대한 진단을 시작합니다.|
|/virtualNetworks/gateways/stopDiagnostics/action|가상 네트워크 게이트웨이에 대한 진단을 중지합니다.|
|/virtualNetworks/gateways/downloadDiagnostics/action|게이트웨이 진단을 다운로드합니다.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|회로 서비스 키를 검색합니다.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|장치 구성 스크립트를 다운로드합니다.|
|/virtualNetworks/gateways/listPackage/action|가상 네트워크 게이트웨이 패키지를 나열합니다.|
|/virtualNetworks/gateways/operationStatuses/read|가상 네트워크 게이트웨이의 작업 상태를 읽습니다.|
|/virtualNetworks/gateways/packages/read|가상 네트워크 게이트웨이 패키지를 가져옵니다.|
|/virtualNetworks/gateways/connections/read|연결 목록을 검색합니다.|
|/virtualNetworks/gateways/connections/connect/action|사이트 간 게이트웨이 연결을 연결합니다.|
|/virtualNetworks/gateways/connections/disconnect/action|사이트 간 게이트웨이 연결을 끊습니다.|
|/virtualNetworks/gateways/connections/test/action|사이트 간 게이트웨이 연결을 테스트합니다.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|해지된 클라이언트 인증서를 읽습니다.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|클라이언트 인증서를 해지합니다.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|클라이언트 인증서의 해지를 취소합니다.|
|/virtualNetworks/gateways/clientRootCertificates/read|클라이언트 루트 인증서를 찾습니다.|
|/virtualNetworks/gateways/clientRootCertificates/write|새 클라이언트 루트 인증서를 업로드합니다.|
|/virtualNetworks/gateways/clientRootCertificates/delete|가상 네트워크 게이트웨이 클라이언트 인증서를 삭제합니다.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|지문으로 인증서를 다운로드합니다.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|가상 네트워크 게이트웨이 인증서 패키지를 나열합니다.|
|/networkSecurityGroups/read|네트워크 보안 그룹을 가져옵니다.|
|/networkSecurityGroups/write|새 네트워크 보안 그룹을 추가합니다.|
|/networkSecurityGroups/delete|네트워크 보안 그룹을 삭제합니다.|
|/networkSecurityGroups/operationStatuses/read|네트워크 보안 그룹의 작업 상태를 읽습니다.|
|/networkSecurityGroups/securityRules/read|보안 규칙을 가져옵니다.|
|/networkSecurityGroups/securityRules/write|보안 규칙을 추가하거나 업데이트합니다.|
|/networkSecurityGroups/securityRules/delete|보안 규칙을 삭제합니다.|
|/networkSecurityGroups/securityRules/operationStatuses/read|네트워크 보안 그룹 보안 규칙의 작업 상태를 읽습니다.|
|/quotas/read|구독에 대한 할당량을 가져옵니다.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| 작업 | 설명 |
|---|---|
|/register/action|클래식 저장소에 등록합니다.|
|/checkStorageAccountAvailability/action|저장소 계정의 가용성을 확인합니다.|
|/capabilities/read|기능을 표시합니다.|
|/publicImages/read|공용 가상 컴퓨터 이미지를 가져옵니다.|
|/images/read|이미지를 반환합니다.|
|/storageAccounts/read|지정된 계정의 저장소 계정을 반환합니다.|
|/storageAccounts/write|새 저장소 계정을 추가합니다.|
|/storageAccounts/delete|저장소 계정을 삭제합니다.|
|/storageAccounts/listKeys/action|저장소 계정의 액세스 키를 나열합니다.|
|/storageAccounts/regenerateKey/action|저장소 계정에 대한 기존 액세스 키를 다시 생성합니다.|
|/storageAccounts/operationStatuses/read|리소스의 작업 상태를 읽습니다.|
|/storageAccounts/images/read|저장소 계정 이미지를 반환합니다.|
|/storageAccounts/images/delete|지정된 장소 계정 이미지를 삭제합니다.|
|/storageAccounts/disks/read|저장소 계정 디스크를 반환합니다.|
|/storageAccounts/disks/write|저장소 계정 디스크를 추가합니다.|
|/storageAccounts/disks/delete|지정된 저장소 계정 디스크를 삭제합니다.|
|/storageAccounts/disks/operationStatuses/read|리소스의 작업 상태를 읽습니다.|
|/storageAccounts/osImages/read|저장소 계정 운영 체제 이미지를 반환합니다.|
|/storageAccounts/osImages/delete|지정된 저장소 계정 운영 체제 이미지를 삭제합니다.|
|/storageAccounts/services/read|사용 가능한 서비스를 가져옵니다.|
|/storageAccounts/services/metricDefinitions/read|메트릭 정의를 가져옵니다.|
|/storageAccounts/services/metrics/read|메트릭을 가져옵니다.|
|/storageAccounts/services/diagnosticSettings/read|진단 설정을 가져옵니다.|
|/storageAccounts/services/diagnosticSettings/write|진단 설정을 추가하거나 수정합니다.|
|/disks/read|저장소 계정 디스크를 반환합니다.|
|/osImages/read|운영 체제 이미지를 반환합니다.|
|/quotas/read|구독에 대한 할당량을 가져옵니다.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 작업 | 설명 |
|---|---|
|/accounts/read|API 계정을 읽습니다.|
|/accounts/write|API 계정을 씁니다.|
|/accounts/delete|API 계정을 삭제합니다.|
|/accounts/listKeys/action|키를 나열합니다.|
|/accounts/regenerateKey/action|키를 다시 생성합니다.|
|/accounts/skus/read|기존 리소스에 대한 사용 가능한 SKU를 읽습니다.|
|/accounts/usages/read|기존 리소스에 대한 할당량 사용량을 가져옵니다.|
|/Operations/read|작업에 대한 설명입니다.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| 작업 | 설명 |
|---|---|
|/RateCard/read|지정된 구독에 대한 제공 데이터, 리소스/미터 메타데이터 및 요율을 제공합니다.|
|/UsageAggregates/read|구독별 Microsoft Azure 소비를 검색합니다. 결과에는 특정 시간 범위의 집계 사용 현황 데이터, 구독 및 리소스 관련 정보가 포함됩니다.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| 작업 | 설명 |
|---|---|
|/register/action|Microsoft.Compute 리소스 공급자에 구독을 등록합니다.|
|/restorePointCollections/read|복원 지점 컬렉션의 속성을 가져옵니다.|
|/restorePointCollections/write|새 복원 지점 컬렉션을 만들거나 기존 복원 지점 컬렉션을 업데이트합니다.|
|/restorePointCollections/delete|복원 지점 컬렉션 및 포함된 복원 지점을 삭제합니다.|
|/restorePointCollections/restorePoints/read|복원 지점의 속성을 가져옵니다.|
|/restorePointCollections/restorePoints/write|새 복원 지점을 만듭니다.|
|/restorePointCollections/restorePoints/delete|복원 지점을 삭제합니다.|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Blob SAS URI와 함께 복원 지점의 속성을 가져옵니다.|
|/virtualMachineScaleSets/read|가상 컴퓨터 확장 집합의 속성을 가져옵니다.|
|/virtualMachineScaleSets/write|새 가상 컴퓨터 확장 집합을 만들거나 기존 가상 컴퓨터 확장 집합을 업데이트합니다.|
|/virtualMachineScaleSets/delete|가상 컴퓨터 확장 집합을 삭제합니다.|
|/virtualMachineScaleSets/start/action|가상 컴퓨터 확장 집합의 인스턴스를 시작합니다.|
|/virtualMachineScaleSets/powerOff/action|가상 컴퓨터 확장 집합의 인스턴스를 끕니다.|
|/virtualMachineScaleSets/restart/action|가상 컴퓨터 확장 집합의 인스턴스를 다시 시작합니다.|
|/virtualMachineScaleSets/deallocate/action|가상 컴퓨터 확장 집합의 인스턴스에 대한 Compute 리소스를 끄고 해제합니다. |
|/virtualMachineScaleSets/manualUpgrade/action|수동으로 인스턴스를 최신 모델의 가상 컴퓨터 확장 집합으로 업데이트합니다.|
|/virtualMachineScaleSets/scale/action|기존 가상 컴퓨터 확장 집합의 인스턴스 수 규모를 감축/확장합니다.|
|/virtualMachineScaleSets/instanceView/read|가상 컴퓨터 확장 집합의 인스턴스 보기를 가져옵니다.|
|/virtualMachineScaleSets/skus/read|기존 가상 컴퓨터 확장 집합에 대한 유효한 SKU를 나열합니다.|
|/virtualMachineScaleSets/virtualMachines/read|VM 확장 집합에서 가상 컴퓨터의 속성을 가져옵니다.|
|/virtualMachineScaleSets/virtualMachines/delete|VM 확장 집합에서 특정 가상 컴퓨터를 삭제합니다.|
|/virtualMachineScaleSets/virtualMachines/start/action|VM 확장 집합에서 가상 컴퓨터 인스턴스를 시작합니다.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|VM 확장 집합에서 가상 컴퓨터 인스턴스를 끕니다.|
|/virtualMachineScaleSets/virtualMachines/restart/action|VM 확장 집합에서 가상 컴퓨터 인스턴스를 다시 시작합니다.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|VM 확장 집합의 가상 컴퓨터에 대한 Compute 리소스를 끄고 해제합니다.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|VM 크기 집합에 있는 가상 컴퓨터의 인스턴스 보기를 검색합니다.|
|/images/read|이미지의 속성을 가져옵니다.|
|/images/write|새 이미지를 만들거나 기존 이미지를 업데이트합니다.|
|/images/delete|이미지를 삭제합니다.|
|/operations/read|Microsoft.Compute 리소스 공급자에서 사용 가능한 작업을 나열합니다.|
|/disks/read|디스크의 속성을 가져옵니다.|
|/disks/write|새 디스크를 만들거나 기존 디스크를 업데이트합니다.|
|/disks/delete|디스크를 삭제합니다.|
|/disks/beginGetAccess/action|Blob 액세스에 대한 디스크의 SAS URI를 가져옵니다.|
|/disks/endGetAccess/action|디스크의 SAS URI를 취소합니다.|
|/snapshots/read|스냅숏의 속성을 가져옵니다.|
|/snapshots/write|새 스냅숏을 만들거나 기존 스냅숏을 업데이트합니다.|
|/snapshots/delete|스냅숏을 삭제합니다.|
|/availabilitySets/read|가용성 집합의 속성을 가져옵니다.|
|/availabilitySets/write|새 가용성 집합을 만들거나 기존 가용성 집합을 업데이트합니다.|
|/availabilitySets/delete|가용성 집합을 삭제합니다.|
|/availabilitySets/vmSizes/read|가용성 집합에서 가상 컴퓨터를 만들거나 업데이트하기 위한 사용 가능 크기를 나열합니다.|
|/virtualMachines/read|가상 컴퓨터의 속성을 가져옵니다.|
|/virtualMachines/write|새 가상 컴퓨터를 만들거나 기존 가상 컴퓨터를 업데이트합니다.|
|/virtualMachines/delete|가상 컴퓨터를 삭제합니다.|
|/virtualMachines/start/action|가상 컴퓨터를 시작합니다.|
|/virtualMachines/powerOff/action|가상 컴퓨터를 끕니다. 가상 컴퓨터에는 요금이 계속 청구됩니다.|
|/virtualMachines/redeploy/action|가상 컴퓨터를 다싯 배포합니다.|
|/virtualMachines/restart/action|가상 컴퓨터를 다시 시작합니다.|
|/virtualMachines/deallocate/action|가상 컴퓨터를 끄고 Compute 리소스를 해제합니다.|
|/virtualMachines/generalize/action|가상 컴퓨터 상태를 일반화됨으로 설정하고 가상 컴퓨터 캡처를 준비합니다.|
|/virtualMachines/capture/action|가상 하드 디스크를 복사하여 가상 컴퓨터를 캡처하고 유사한 가상 컴퓨터를 만드는 데 사용할 수 있는 템플릿을 생성합니다.|
|/virtualMachines/convertToManagedDisks/action|가상 컴퓨터의 blob 기반 디스크를 관리되는 디스크로 변환합니다.|
|/virtualMachines/vmSizes/read|가상 컴퓨터가 업데이트될 수 있는 사용 가능한 크기를 나열합니다.|
|/virtualMachines/instanceView/read|가상 컴퓨터 및 해당 리소스의 자세한 런타임 상태를 가져옵니다.|
|/virtualMachines/extensions/read|가상 컴퓨터 확장의 속성을 가져옵니다.|
|/virtualMachines/extensions/write|새 가상 컴퓨터 확장을 만들거나 기존 가상 컴퓨터 확장을 업데이트합니다.|
|/virtualMachines/extensions/delete|가상 컴퓨터 확장을 삭제합니다.|
|/locations/vmSizes/read|한 위치에서 사용 가능한 가상 컴퓨터 크기를 나열합니다.|
|/locations/usages/read|한 위치에 있는 구독의 Compute 리소스에 대한 서비스 제한 및 현재 사용 수량을 가져옵니다.|
|/locations/operations/read|비동기 작업의 상태를 가져옵니다.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| 작업 | 설명 |
|---|---|
|/register/action|컨테이너 레지스트리 리소스 공급자에 대한 구독을 등록하고 컨테이너 레지스트리를 만들도록 설정합니다.|
|/checknameavailability/read|해당 레지스트리 이름이 올바른지와 사용되고 있지 않은지 확인합니다.|
|/registries/read|컨테이너 레지스트리 목록을 반환하거나 지정된 컨테이너 레지스트리에 대한 속성을 가져옵니다.|
|/registries/write|지정된 매개 변수를 사용하여 컨테이너 레지스트리를 만들거나 지정된 컨테이너 레지스트리에 대한 속성 또는 태그를 업데이트합니다.|
|/registries/delete|기존 컨테이너 레지스트리를 삭제합니다.|
|/registries/listCredentials/action|지정된 컨테이너 레지스트리에 대한 로그인 자격 증명을 나열합니다.|
|/registries/regenerateCredential/action|지정된 컨테이너 레지스트리에 대한 로그인 자격 증명을 다시 생성합니다.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| 작업 | 설명 |
|---|---|
|/containerServices/subscriptions/read|구독에 따라 지정된 컨테이너 서비스를 가져옵니다.|
|/containerServices/resourceGroups/read|리소스 그룹에 따라 지정된 컨테이너 서비스를 가져옵니다.|
|/containerServices/resourceGroups/ContainerServiceName/read|지정된 컨테이너 서비스를 가져옵니다.|
|/containerServices/resourceGroups/ContainerServiceName/write|지정된 컨테이너 서비스를 배치하거나 업데이트합니다.|
|/containerServices/resourceGroups/ContainerServiceName/delete|지정된 컨테이너 서비스를 삭제합니다.|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| 작업 | 설명 |
|---|---|
|/updateCommunicationPreference/action|통신 기본 설정을 업데이트합니다.|
|/listCommunicationPreference/action|통신 기본 설정을 나열합니다.|
|/applications/read|읽기 작업|
|/applications/write|쓰기 작업|
|/applications/write|쓰기 작업|
|/applications/delete|삭제 작업|
|/applications/listSecrets/action|암호를 나열합니다.|
|/applications/listSingleSignOnToken/action|Single Sign-on 토큰을 읽습니다.|
|/operations/read|읽기 작업|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| 작업 | 설명 |
|---|---|
|/hubs/read|Azure Customer Insights Hub를 읽습니다.|
|/hubs/write|Azure Customer Insights Hub를 만들거나 업데이트합니다.|
|/hubs/delete|Azure Customer Insights Hub를 삭제합니다.|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|리소스에 대해 사용 가능한 메트릭을 가져옵니다.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|리소스에 대해 사용 가능한 로그를 가져옵니다.|
|/hubs/authorizationPolicies/read|Azure Customer Insights 공유 액세스 서명 정책을 읽습니다.|
|/hubs/authorizationPolicies/write|Azure Customer Insights 공유 액세스 서명 정책을 만들거나 업데이트합니다.|
|/hubs/authorizationPolicies/delete|Azure Customer Insights 공유 액세스 서명 정책을 만들거나 삭제합니다.|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Azure Customer Insights 공유 액세스 서명 정책 기본 키를 다시 생성합니다.|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Azure Customer Insights 공유 액세스 서명 정책 보조 키를 다시 생성합니다.|
|/hubs/profiles/read|Azure Customer Insights 프로필을 읽습니다.|
|/hubs/profiles/write|Azure Customer Insights 프로필을 씁니다.|
|/hubs/kpi/read|모든 Azure Customer Insights 핵심 성과 지표를 읽습니다.|
|/hubs/kpi/write|모든 Azure Customer Insights 핵심 성과 지표를 만들거나 업데이트합니다.|
|/hubs/kpi/delete|모든 Azure Customer Insights 핵심 성과 지표를 삭제합니다.|
|/hubs/views/read|Azure Customer Insights 앱 보기를 읽습니다.|
|/hubs/views/write|Azure Customer Insights 앱 보기를 만들거나 업데이트합니다.|
|/hubs/views/delete|Azure Customer Insights 앱 보기를 삭제합니다.|
|/hubs/interactions/read|Azure Customer Insights 상호 작용을 읽습니다.|
|/hubs/interactions/write|Azure Customer Insights 상호 작용을 만들거나 업데이트합니다.|
|/hubs/roleAssignments/read|Azure Customer Insights RBAC 할당을 읽습니다.|
|/hubs/roleAssignments/write|Azure Customer Insights RBAC 할당을 만들거나 업데이트합니다.|
|/hubs/roleAssignments/delete|Azure Customer Insights RBAC 할당을 삭제합니다.|
|/hubs/connectors/read|Azure Customer Insights 커넥터를 읽습니다.|
|/hubs/connectors/write|Azure Customer Insights 커넥터를 만들거나 업데이트합니다.|
|/hubs/connectors/delete|Azure Customer Insights 커넥터를 삭제합니다.|
|/hubs/connectors/mappings/read|Azure Customer Insights 커넥터 매핑을 읽습니다.|
|/hubs/connectors/mappings/write|Azure Customer Insights 커넥터 매핑을 만들거나 업데이트합니다.|
|/hubs/connectors/mappings/delete|Azure Customer Insights 커넥터 매핑을 삭제합니다.|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|테넌트에 대한 카탈로그 이름 가용성을 확인합니다.|
|/catalogs/read|구독 또는 리소스 그룹에서 카탈로그에 대한 속성을 가져옵니다.|
|/catalogs/write|카탈로그를 만들거나 카탈로그에 대한 태그 및 속성을 업데이트합니다.|
|/catalogs/delete|카탈로그를 삭제합니다.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| 작업 | 설명 |
|---|---|
|/datafactories/read|데이터 팩터리를 읽습니다.|
|/datafactories/write|데이터 팩터리를 만들거나 업데이트합니다.|
|/datafactories/delete|데이터 팩터리를 삭제합니다.|
|/datafactories/datapipelines/read|파이프라인을 읽습니다.|
|/datafactories/datapipelines/delete|파이프라인을 삭제합니다.|
|/datafactories/datapipelines/pause/action|파이프라인을 일시 중지합니다.|
|/datafactories/datapipelines/resume/action|파이프라인을 계속합니다.|
|/datafactories/datapipelines/update/action|파이프라인을 업데이트합니다.|
|/datafactories/datapipelines/write|파이프라인을 만들거나 업데이트합니다.|
|/datafactories/linkedServices/read|연결된 서비스를 읽습니다.|
|/datafactories/linkedServices/delete|연결된 서비스를 삭제합니다.|
|/datafactories/linkedServices/write|연결된 서비스를 만들거나 업데이트합니다.|
|/datafactories/tables/read|테이블을 읽습니다.|
|/datafactories/tables/delete|테이블을 삭제합니다.|
|/datafactories/tables/write|테이블을 만들거나 업데이트합니다.|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| 작업 | 설명 |
|---|---|
|/accounts/read|DataLakeAnalytics 계정에 대한 정보를 가져옵니다.|
|/accounts/write|DataLakeAnalytics 계정을 만들거나 업데이트합니다.|
|/accounts/delete|DataLakeAnalytics 계정을 삭제합니다.|
|/accounts/firewallRules/read|방화벽 규칙에 대한 정보를 가져옵니다.|
|/accounts/firewallRules/write|방화벽 규칙을 만들거나 업데이트합니다.|
|/accounts/firewallRules/delete|방화벽 규칙을 삭제합니다.|
|/accounts/storageAccounts/read|DataLakeAnalytics 계정에 대한 연결된 저장소 계정을 가져옵니다.|
|/accounts/storageAccounts/write|DataLakeAnalytics 계정에 저장소 계정을 연결합니다.|
|/accounts/storageAccounts/delete|DataLakeAnalytics 계정에서 저장소 계정을 연결 해제합니다.|
|/accounts/storageAccounts/Containers/read|저장소 계정의 컨테이너를 가져옵니다.|
|/accounts/storageAccounts/Containers/listSasTokens/action|저장소 컨테이너에 대한 SAS 토큰을 나열합니다.|
|/accounts/dataLakeStoreAccounts/read|DataLakeAnalytics 계정에 대한 연결된 DataLakeStore 계정을 가져옵니다.|
|/accounts/dataLakeStoreAccounts/write|DataLakeAnalytics 계정에 DataLakeStore 계정을 연결합니다.|
|/accounts/dataLakeStoreAccounts/delete|DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| 작업 | 설명 |
|---|---|
|/accounts/read|기존 DataLakeStore 계정에 대한 정보를 가져옵니다.|
|/accounts/write|새 DataLakeStore 계정을 만들거나 기존 DataLakeStore 계정을 업데이트합니다.|
|/accounts/delete|기존 DataLakeStore 계정을 삭제합니다.|
|/accounts/firewallRules/read|방화벽 규칙에 대한 정보를 가져옵니다.|
|/accounts/firewallRules/write|방화벽 규칙을 만들거나 업데이트합니다.|
|/accounts/firewallRules/delete|방화벽 규칙을 삭제합니다.|
|/accounts/trustedIdProviders/read|신뢰할 수 있는 ID 공급자에 대한 정보를 가져옵니다.|
|/accounts/trustedIdProviders/write|신뢰할 수 있는 ID 공급자를 만들거나 업데이트합니다.|
|/accounts/trustedIdProviders/delete|신뢰할 수 있는 ID 공급자를 삭제합니다.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| 작업 | 설명 |
|---|---|
|/register/action|IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다.|
|/checkNameAvailability/Action|IotHub 이름이 사용 가능한지 확인합니다.|
|/usages/Read|이 공급자에 대한 구독 사용 정보를 가져옵니다.|
|/operations/Read|모든 ResourceProvider 작업을 가져옵니다.|
|/iotHubs/Read|IotHub 리소스를 가져옵니다.|
|/iotHubs/Write|IotHub 리소스를 만들거나 업데이트합니다.|
|/iotHubs/Delete|IotHub 리소스를 삭제합니다.|
|/iotHubs/listkeys/Action|모든 IotHub 키를 가져옵니다.|
|/iotHubs/exportDevices/Action|장치를 내보냅니다.|
|/iotHubs/importDevices/Action|장치를 가져옵니다.|
|/IotHubs/metricDefinitions/read|IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다.|
|/iotHubs/iotHubKeys/listkeys/Action|지정된 이름의 IotHub 키를 가져옵니다.|
|/iotHubs/iotHubStats/Read|IotHub 통계를 가져옵니다.|
|/iotHubs/quotaMetrics/Read|할당량 메트릭을 가져옵니다.|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|EventHub 소비자 그룹을 만듭니다.|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|EventHub 소비자 그룹을 가져옵니다.|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|EventHub 소비자 그룹을 삭제합니다.|
|/iotHubs/routing/routes/$testall/Action|모든 기존 경로에 대해 메시지를 테스트합니다.|
|/iotHubs/routing/routes/$testnew/Action|제공된 테스트 경로에 대해 메시지를 테스트합니다.|
|/IotHubs/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/IotHubs/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/iotHubs/skus/Read|유효한 IotHub SKU를 가져옵니다.|
|/iotHubs/jobs/Read|지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다.|
|/iotHubs/routingEndpointsHealth/Read|IotHub에 대한 모든 라우팅 끝점의 상태를 가져옵니다.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| 작업 | 설명 |
|---|---|
|/Subscription/register/action|구독을 등록합니다.|
|/labs/delete|랩을 삭제합니다.|
|/labs/read|랩을 읽습니다.|
|/labs/write|랩을 추가 또는 수정합니다.|
|/labs/ListVhds/action|사용자 지정 이미지를 만드는 데 사용할 수 있는 디스크 이미지를 나열합니다.|
|/labs/GenerateUploadUri/action|랩에 사용자 지정 디스크 이미지를 업로드하기 위한 URI를 생성합니다.|
|/labs/CreateEnvironment/action|랩에 가상 컴퓨터를 만듭니다.|
|/labs/ClaimAnyVm/action|랩에서 임의 클레임 가능 가상 컴퓨터를 클레임합니다.|
|/labs/ExportResourceUsage/action|랩 리소스 사용량을 저장소 계정으로 내보냅니다.|
|/labs/users/delete|사용자 프로필을 삭제합니다.|
|/labs/users/read|사용자 프로필을 읽습니다.|
|/labs/users/write|사용자 프로필을 추가하거나 수정합니다.|
|/labs/users/secrets/delete|암호를 삭제합니다.|
|/labs/users/secrets/read|암호를 읽습니다.|
|/labs/users/secrets/write|암호를 추가 또는 수정합니다.|
|/labs/users/environments/delete|환경을 삭제합니다.|
|/labs/users/environments/read|환경을 읽습니다.|
|/labs/users/environments/write|환경을 추가하거나 수정합니다.|
|/labs/users/disks/delete|디스크를 삭제합니다.|
|/labs/users/disks/read|디스크를 읽습니다.|
|/labs/users/disks/write|디스크를 추가 또는 수정합니다.|
|/labs/users/disks/Attach/action|디스크의 임대를 만들고 가상 컴퓨터에 연결합니다.|
|/labs/users/disks/Detach/action|가상 컴퓨터에 연결된 디스크의 임대를 분리하고 해제합니다.|
|/labs/customImages/delete|사용자 지정 이미지를 삭제합니다.|
|/labs/customImages/read|사용자 지정 이미지를 읽습니다.|
|/labs/customImages/write|사용자 지정 이미지를 추가하거나 수정합니다.|
|/labs/serviceRunners/delete|서비스 실행기를 삭제합니다.|
|/labs/serviceRunners/read|서비스 실행기를 읽습니다.|
|/labs/serviceRunners/write|서비스 실행기를 추가하거나 수정합니다.|
|/labs/artifactSources/delete|아티팩트 원본을 삭제합니다.|
|/labs/artifactSources/read|아티팩트 원본을 읽습니다.|
|/labs/artifactSources/write|아티팩트 원본을 추가하거나 수정합니다.|
|/labs/artifactSources/artifacts/read|아티팩트를 읽습니다.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|지정된 아티팩트에 대한 ARM 템플릿을 생성하고, 필요한 파일을 저장소 계정에 업로드하고, 생성된 아티팩트의 유효성을 검사합니다.|
|/labs/artifactSources/armTemplates/read|Azure Resource Manager 템플릿을 읽습니다.|
|/labs/costs/read|비용을 읽습니다.|
|/labs/costs/write|비용을 추가하거나 수정합니다.|
|/labs/virtualNetworks/delete|가상 네트워크를 삭제합니다.|
|/labs/virtualNetworks/read|가상 네트워크를 읽습니다.|
|/labs/virtualNetworks/write|가상 네트워크를 추가 또는 수정합니다.|
|/labs/formulas/delete|수식을 삭제합니다.|
|/labs/formulas/read|수식을 읽습니다.|
|/labs/formulas/write|수식을 추가하거나 수정합니다.|
|/labs/schedules/delete|일정을 삭제합니다.|
|/labs/schedules/read|일정을 읽습니다.|
|/labs/schedules/write|일정을 추가하거나 수정합니다.|
|/labs/schedules/Execute/action|일정을 실행합니다.|
|/labs/schedules/ListApplicable/action|적용 가능한 모든 일정을 나열합니다.|
|/labs/galleryImages/read|갤러리 이미지를 읽습니다.|
|/labs/policySets/EvaluatePolicies/action|랩 정책을 평가합니다.|
|/labs/policySets/policies/delete|정책을 삭제합니다.|
|/labs/policySets/policies/read|정책을 읽습니다.|
|/labs/policySets/policies/write|정책을 추가하거나 수정합니다.|
|/labs/virtualMachines/delete|가상 컴퓨터를 삭제합니다.|
|/labs/virtualMachines/read|가상 컴퓨터를 읽습니다.|
|/labs/virtualMachines/write|가상 컴퓨터를 추가 또는 수정합니다.|
|/labs/virtualMachines/Start/action|가상 컴퓨터를 시작합니다.|
|/labs/virtualMachines/Stop/action|가상 컴퓨터 중지|
|/labs/virtualMachines/ApplyArtifacts/action|가상 컴퓨터에 아티팩트를 적용합니다.|
|/labs/virtualMachines/AddDataDisk/action|가상 컴퓨터에 새 또는 기존 데이터 디스크를 연결합니다.|
|/labs/virtualMachines/DetachDataDisk/action|가상 컴퓨터에서 지정된 디스크를 분리합니다.|
|/labs/virtualMachines/Claim/action|기존 가상 컴퓨터의 소유권을 가져옵니다.|
|/labs/virtualMachines/ListApplicableSchedules/action|적용 가능한 모든 일정을 나열합니다.|
|/labs/virtualMachines/schedules/delete|일정을 삭제합니다.|
|/labs/virtualMachines/schedules/read|일정을 읽습니다.|
|/labs/virtualMachines/schedules/write|일정을 추가하거나 수정합니다.|
|/labs/virtualMachines/schedules/Execute/action|일정을 실행합니다.|
|/labs/notificationChannels/delete|notificationchannels를 삭제합니다.|
|/labs/notificationChannels/read|notificationchannels를 읽습니다.|
|/labs/notificationChannels/write|notificationchannels를 추가하거나 수정합니다.|
|/labs/notificationChannels/Notify/action|제공된 채널에 알림을 보냅니다.|
|/schedules/delete|일정을 삭제합니다.|
|/schedules/read|일정을 읽습니다.|
|/schedules/write|일정을 추가하거나 수정합니다.|
|/schedules/Execute/action|일정을 실행합니다.|
|/schedules/Retarget/action|일정의 대상 리소스 ID를 업데이트합니다.|
|/locations/operations/read|읽기 작업|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| 작업 | 설명 |
|---|---|
|/databaseAccountNames/read|이름 가용성을 확인합니다.|
|/databaseAccounts/read|데이터베이스 계정을 읽습니다.|
|/databaseAccounts/write|데이터베이스 계정을 업데이트합니다.|
|/databaseAccounts/listKeys/action|데이터베이스 계정의 키를 나열합니다.|
|/databaseAccounts/regenerateKey/action|데이터베이스 계정의 키를 순환합니다.|
|/databaseAccounts/listConnectionStrings/action|데이터베이스 계정에 대한 연결 문자열을 가져옵니다.|
|/databaseAccounts/changeResourceGroup/action|데이터베이스 계정의 리소스 그룹을 변경합니다.|
|/databaseAccounts/failoverPriorityChange/action|데이터베이스 계정 영역의 장애 조치 우선 순위를 변경합니다. 수동 장애 조치 작업을 수행하는 데 사용됩니다.|
|/databaseAccounts/delete|데이터베이스 계정을 삭제합니다.|
|/databaseAccounts/metricDefinitions/read|데이터베이스 계정 메트릭 정의를 읽습니다.|
|/databaseAccounts/metrics/read|데이터베이스 계정 메트릭을 읽습니다.|
|/databaseAccounts/usages/read|데이터베이스 계정 사용 현황을 읽습니다.|
|/databaseAccounts/databases/collections/metricDefinitions/read|컬렉션 메트릭 정의를 읽습니다.|
|/databaseAccounts/databases/collections/metrics/read|컬렉션 메트릭을 읽습니다.|
|/databaseAccounts/databases/collections/usages/read|컬렉션 사용 현황을 읽습니다.|
|/databaseAccounts/databases/metricDefinitions/read|데이터베이스 메트릭 정의를 읽습니다.|
|/databaseAccounts/databases/metrics/read|데이터베이스 메트릭을 읽습니다.|
|/databaseAccounts/databases/usages/read|데이터베이스 사용 현황을 읽습니다.|
|/databaseAccounts/readonlykeys/read|데이터베이스 계정 읽기 전용 키를 읽습니다.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| 작업 | 설명 |
|---|---|
|/generateSsoRequest/Action|도메인 제어 센터에 대한 로그인 요청을 생성합니다.|
|/validateDomainRegistrationInformation/Action|도메인 구매 개체를 제출하지 않고 유효성을 검사합니다.|
|/checkDomainAvailability/Action|도메인을 구매할 수 있는지를 확인합니다.|
|/listDomainRecommendations/Action|키워드를 기준으로 목록 도메인 권장 사항을 검색합니다.|
|/register/action|구독에 대한 Microsoft 도메인 리소스 공급자를 등록합니다.|
|/domains/Read|도메인 목록을 가져옵니다.|
|/domains/Write|새 도메인을 추가하거나 기존 도메인을 업데이트합니다.|
|/domains/Delete|기존 도메인을 삭제합니다.|
|/domains/operationresults/Read|도메인 작업을 가져옵니다.|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| 작업 | 설명 |
|---|---|
|/lcsprojects/read|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트를 표시합니다.|
|/lcsprojects/write|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트를 만들고 업데이트합니다. 이름 및 설명 속성만 업데이트할 수 있습니다. 만든 후 프로젝트와 연결된 구독 및 위치를 업데이트할 수 없습니다.|
|/lcsprojects/delete|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트를 삭제합니다.|
|/lcsprojects/clouddeployments/read|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트의 Microsoft Dynamics AX 2012 R3 평가판 배포를 표시합니다.|
|/lcsprojects/clouddeployments/write|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트에서 Microsoft Dynamics AX 2012 R3 평가판 배포를 만듭니다. Azure 관리 포털에서 배포를 관리할 수 있습니다.|
|/lcsprojects/connectors/read|Microsoft Dynamics Lifecycle Services 프로젝트에 속하는 커넥터를 읽습니다.|
|/lcsprojects/connectors/write|Microsoft Dynamics Lifecycle Services 프로젝트에 속하는 커넥터를 만들고 업데이트합니다.|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다.|
|/register/action|EventHub 리소스 공급자에 대한 구독을 등록하고 EventHub 리소스를 만들도록 설정합니다.|
|/namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 상태는 업데이트할 수 있는 속성입니다.|
|/namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/namespaces/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/namespaces/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/eventhubs/write|EventHub 속성을 만들거나 업데이트합니다.|
|/namespaces/eventhubs/read|EventHub 리소스 설명의 목록을 가져옵니다.|
|/namespaces/eventhubs/Delete|EventHub 리소스 삭제 작업|
|/namespaces/eventHubs/consumergroups/write|ConsumerGroup 속성을 만들거나 업데이트합니다.|
|/namespaces/eventHubs/consumergroups/read|ConsumerGroup 리소스 설명의 목록을 가져옵니다.|
|/namespaces/eventHubs/consumergroups/Delete|ConsumerGroup 리소스 삭제 작업|
|/namespaces/eventhubs/authorizationRules/read| EventHub 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/eventhubs/authorizationRules/write|EventHub 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/eventhubs/authorizationRules/delete|EventHub 권한 부여 규칙 삭제 작업|
|/namespaces/eventhubs/authorizationRules/listkeys/action|EventHub의 연결 문자열을 가져옵니다.|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/diagnosticSettings/read|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/diagnosticSettings/write|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/logDefinitions/read|네임스페이스 로그 리소스 설명의 목록을 가져옵니다.|

## <a name="microsoftfeatures"></a>Microsoft.Features

| 작업 | 설명 |
|---|---|
|/providers/features/read|지정된 리소스 공급자에서 구독의 기능을 가져옵니다.|
|/providers/features/register/action|지정된 리소스 공급자에서 구독의 기능을 등록합니다.|
|/features/read|구독 기능을 가져옵니다.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| 작업 | 설명 |
|---|---|
|/clusters/write|HDInsight 클러스터를 만들거나 업데이트합니다.|
|/clusters/read|HDInsight 클러스터에 대한 세부 정보를 가져옵니다.|
|/clusters/delete|HDInsight 클러스터를 삭제합니다.|
|/clusters/changerdpsetting/action|HDInsight 클러스터의 RDP 설정을 변경합니다.|
|/clusters/configurations/action|HDInsight 클러스터 구성을 업데이트합니다.|
|/clusters/configurations/read|HDInsight 클러스터 구성을 가져옵니다.|
|/clusters/roles/resize/action|HDInsight 클러스터 크기를 조정합니다.|
|/locations/capabilities/read|구독 기능을 가져옵니다.|
|/locations/checkNameAvailability/read|이름 가용성을 확인합니다.|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| 작업 | 설명 |
|---|---|
|/register/action|Import/Export 리소스 공급자에 대한 구독을 등록하고 Import/Export 작업을 만들도록 설정합니다.|
|/jobs/write|지정된 매개 변수를 사용하여 작업을 만들거나 지정된 작업에 대한 속성 또는 태그를 업데이트합니다.|
|/jobs/read|지정된 작업에 대한 속성을 가져오거나 작업 목록을 반환합니다.|
|/jobs/listBitLockerKeys/action|지정된 작업에 대한 BitLocker 키를 가져옵니다.|
|/jobs/delete|기존 작업을 삭제합니다.|
|/locations/read|지정된 위치에 대한 속성을 가져오거나 위치 목록을 반환합니다.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| 작업 | 설명 |
|---|---|
|/Register/Action|Microsoft Insights 공급자를 등록합니다.|
|/AlertRules/Write|경고 규칙 구성에 쓰는 중|
|/AlertRules/Delete|경고 규칙 구성을 삭제하는 중|
|/AlertRules/Read|경고 규칙 구성을 읽는 중|
|/AlertRules/Activated/Action|경고 규칙 활성화됨|
|/AlertRules/Resolved/Action|경고 규칙 확인됨|
|/AlertRules/Throttled/Action|경고 규칙이 제한됨|
|/AlertRules/Incidents/Read|경고 규칙 인시던트 구성을 읽는 중|
|/MetricDefinitions/Read|메트릭 정의 읽기|
|/eventtypes/values/Read|관리 이벤트 유형 값 읽기|
|/eventtypes/digestevents/Read|관리 이벤트 유형 다이제스트 읽기|
|/Metrics/Read|메트릭 읽기|
|/LogProfiles/Write|로그 프로필 구성에 쓰는 중|
|/LogProfiles/Delete|로그 프로필 구성 삭제|
|/LogProfiles/Read|로그 프로필 읽기|
|/AutoscaleSettings/Write|크기 자동 조정 설정 구성에 쓰는 중|
|/AutoscaleSettings/Delete|크기 자동 조정 설정 구성을 삭제하는 중|
|/AutoscaleSettings/Read|크기 자동 조정 설정 구성을 읽는 중|
|/AutoscaleSettings/Scaleup/Action|크기 자동 조정 규모 확대 작업|
|/AutoscaleSettings/Scaledown/Action|크기 자동 조정 규모 축소 작업|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|메트릭 정의 읽기|
|/ActivityLogAlerts/Activated/Action|활동 로그 경고를 트리거함|
|/DiagnosticSettings/Write|진단 설정 구성에 쓰는 중|
|/DiagnosticSettings/Delete|진단 설정 구성 삭제 중|
|/DiagnosticSettings/Read|진단 설정 구성 읽는 중|
|/LogDefinitions/Read|로그 정의 읽기|
|/ExtendedDiagnosticSettings/Write|확장 진단 설정 구성에 씀|
|/ExtendedDiagnosticSettings/Delete|확장 진단 설정 구성을 삭제함|
|/ExtendedDiagnosticSettings/Read|확장 진단 설정 구성을 읽음|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| 작업 | 설명 |
|---|---|
|/register/action|구독을 등록합니다.|
|/checkNameAvailability/read|Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다.|
|/vaults/read|Key Vault의 속성을 봅니다.|
|/vaults/write|새 Key Vault를 만들거나 기존 Key Vault의 속성을 업데이트합니다.|
|/vaults/delete|Key Vault를 삭제합니다.|
|/vaults/deploy/action|Azure Resource를 배포하는 동안 Key Vault의 비밀에 액세스할 수 있습니다.|
|/vaults/secrets/read|비밀의 값이 아닌 비밀의 속성을 봅니다.|
|/vaults/secrets/write|새 비밀을 만들거나 기존 비밀의 값을 업데이트합니다.|
|/vaults/accessPolicies/write|병합 또는 바꾸기를 통해 기존 액세스 정책을 업데이트하거나 새 액세스 정책을 자격 증명 모음에 추가합니다.|
|/deletedVaults/read|일시 삭제된 여러 Key Vault의 속성을 봅니다.|
|/locations/operationResults/read|장기 실행 작업의 결과를 확인합니다.|
|/locations/deletedVaults/read|일시 삭제된 한 Key Vault의 속성을 봅니다.|
|/locations/deletedVaults/purge/action|일시 삭제된 Key Vault를 제거합니다.|

## <a name="microsoftlogic"></a>Microsoft.Logic

| 작업 | 설명 |
|---|---|
|/workflows/read|워크플로를 읽습니다.|
|/workflows/write|워크플로를 만들거나 업데이트합니다.|
|/workflows/delete|워크플로를 삭제합니다.|
|/workflows/run/action|워크플로의 실행을 시작합니다.|
|/workflows/disable/action|워크플로를 사용하지 않도록 설정합니다.|
|/workflows/enable/action|워크플로를 사용하도록 설정합니다.|
|/workflows/validate/action|워크플로의 유효성을 검사합니다.|
|/workflows/move/action|워크플로를 기존의 구독 ID, 리소스 그룹 및/또는 이름에서 다른 구독 ID, 리소스 그룹 및/또는 이름으로 이동합니다.|
|/workflows/listSwagger/action|워크플로 Swagger 정의를 가져옵니다.|
|/workflows/regenerateAccessKey/action|선택키 암호를 다시 생성합니다.|
|/workflows/listCallbackUrl/action|워크플로의 콜백 URL을 가져옵니다.|
|/workflows/versions/read|워크플로 버전을 읽습니다.|
|/workflows/versions/triggers/listCallbackUrl/action|워크플로의 콜백 URL을 가져옵니다.|
|/workflows/runs/read|워크플로 실행을 읽습니다.|
|/workflows/runs/cancel/action|워크플로 실행을 취소합니다.|
|/workflows/runs/actions/read|워크플로 실행 작업을 읽습니다.|
|/workflows/runs/operations/read|워크플로 실행 작업 상태를 읽습니다.|
|/workflows/triggers/read|트리거를 읽습니다.|
|/workflows/triggers/run/action|트리거를 실행합니다.|
|/workflows/triggers/listCallbackUrl/action|워크플로의 콜백 URL을 가져옵니다.|
|/workflows/triggers/histories/read|트리거 기록을 읽습니다.|
|/workflows/triggers/histories/resubmit/action|워크플로 트리거를 다시 전송합니다.|
|/workflows/accessKeys/read|선택키를 읽습니다.|
|/workflows/accessKeys/write|선택키를 만들거나 업데이트합니다.|
|/workflows/accessKeys/delete|선택키를 삭제합니다.|
|/workflows/accessKeys/list/action|선택키 암호를 나열합니다.|
|/workflows/accessKeys/regenerate/action|선택키 암호를 다시 생성합니다.|
|/locations/workflows/validate/action|워크플로의 유효성을 검사합니다.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| 작업 | 설명 |
|---|---|
|/register/action|Machine Learning 웹 서비스 리소스 공급자에 대한 구독을 등록하고 웹 서비스를 만들도록 설정합니다.|
|/webServices/action|지원되는 지역에 대한 국가별 웹 서비스 속성을 만듭니다.|
|/commitmentPlans/read|Machine Learning 약정 요금을 읽습니다.|
|/commitmentPlans/write|Machine Learning 약정 요금을 만들거나 업데이트합니다.|
|/commitmentPlans/delete|Machine Learning 약정 요금을 삭제합니다.|
|/commitmentPlans/join/action|Machine Learning 약정 요금제에 가입합니다.|
|/commitmentPlans/commitmentAssociations/read|Machine Learning 약정 요금 관계를 읽습니다.|
|/commitmentPlans/commitmentAssociations/move/action|Machine Learning 약정 요금 관계를 이동합니다.|
|/Workspaces/read|Machine Learning 작업 영역을 읽습니다.|
|/Workspaces/write|Machine Learning 작업 영역을 만들거나 업데이트합니다.|
|/Workspaces/delete|Machine Learning 작업 영역을 삭제합니다.|
|/Workspaces/listworkspacekeys/action|Machine Learning 작업 영역에 대한 키를 나열합니다.|
|/Workspaces/resyncstoragekeys/action|Machine Learning 작업 영역에 대해 구성된 저장소 계정의 키를 다시 동기화합니다.|
|/webServices/read|Machine Learning 웹 서비스를 읽습니다.|
|/webServices/write|Machine Learning 웹 서비스를 만들거나 업데이트합니다.|
|/webServices/delete|Machine Learning 웹 서비스를 삭제합니다.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| 작업 | 설명 |
|---|---|
|/agreements/offers/plans/read|지정된 Marketplace 항목에 대한 규약을 반환합니다.|
|/agreements/offers/plans/sign/action|지정된 Marketplace 항목에 대한 규약에 서명합니다.|
|/agreements/offers/plans/cancel/action|지정된 Marketplace 항목에 대한 규약을 취소합니다.|

## <a name="microsoftmedia"></a>Microsoft.Media

| 작업 | 설명 |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| 작업 | 설명 |
|---|---|
|/register/action|구독을 등록합니다.|
|/unregister/action|구독을 등록 취소합니다.|
|/checkTrafficManagerNameAvailability/action|Traffic Manager 상대 DNS 이름의 가용성을 확인합니다.|
|/dnszones/read|Json 형식의 DNS 영역을 가져옵니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다. 이 명령은 영역 내에 포함된 레코드 집합을 검색하지 않습니다.|
|/dnszones/write|리소스 그룹 내에 DNS 영역을 만들거나 업데이트합니다.  DNS 영역 리소스에서 태그를 업데이트하는 데 사용됩니다. 이 명령은 영역 내에서 레코드 집합을 만들거나 업데이트하는 데 사용할 수 없습니다.|
|/dnszones/delete|Json 형식의 DNS 영역을 삭제합니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다.|
|/dnszones/MX/read|JSON 형식의 'MX' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/MX/write|DNS 영역 내에서 'MX' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/MX/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'MX' 형식을 제거합니다.|
|/dnszones/NS/read|NS 형식의 DNS 레코드 집합을 가져옵니다.|
|/dnszones/NS/write|NS 형식의 DNS 레코드 집합을 만들거나 업데이트합니다.|
|/dnszones/NS/delete|NS 형식의 DNS 레코드 집합을 삭제합니다.|
|/dnszones/AAAA/read|JSON 형식의 'AAAA' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/AAAA/write|DNS 영역 내에서 'AAAA' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/AAAA/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'AAAA' 형식을 제거합니다.|
|/dnszones/CNAME/read|JSON 형식의 'CNAME' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다.|
|/dnszones/CNAME/write|DNS 영역 내에서 'CNAME' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/CNAME/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'CNAME' 형식을 제거합니다.|
|/dnszones/SOA/read|SOA 형식의 DNS 레코드 집합을 가져옵니다.|
|/dnszones/SOA/write|SOA 형식의 DNS 레코드 집합을 만들거나 업데이트합니다.|
|/dnszones/SRV/read|JSON 형식의 'SRV' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/SRV/write|SRV 형식의 레코드 집합을 만들거나 업데이트합니다.|
|/dnszones/SRV/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'SRV' 형식을 제거합니다.|
|/dnszones/PTR/read|JSON 형식의 'PTR' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/PTR/write|DNS 영역 내에서 'PTR' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/PTR/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'PTR' 형식을 제거합니다.|
|/dnszones/A/read|JSON 형식의 'A' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/A/write|DNS 영역 내에서 'A' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/A/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'A' 형식을 제거합니다.|
|/dnszones/TXT/read|JSON 형식의 'TXT' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/TXT/write|DNS 영역 내에서 'TXT' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/TXT/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'TXT' 형식을 제거합니다.|
|/dnszones/recordsets/read|여러 형식의 DNS 레코드 집합을 가져옵니다.|
|/networkInterfaces/read|네트워크 인터페이스 정의를 가져옵니다. |
|/networkInterfaces/write|네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다. |
|/networkInterfaces/join/action|네트워크 인터페이스에 가상 컴퓨터를 연결합니다.|
|/networkInterfaces/delete|네트워크 인터페이스를 삭제합니다.|
|/networkInterfaces/effectiveRouteTable/action|VM의 네트워크 인터페이스에 구성된 경로 테이블을 가져옵니다.|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|VM의 네트워크 인터페이스에 구성된 네트워크 보안 그룹을 가져옵니다.|
|/networkInterfaces/loadBalancers/read|네트워크 인터페이스가 속하는 모든 부하 분산 장치를 가져옵니다.|
|/networkInterfaces/ipconfigurations/read|네트워크 인터페이스의 IP 구성 정의를 가져옵니다. |
|/publicIPAddresses/read|공용 IP 주소 정의를 가져옵니다.|
|/publicIPAddresses/write|공용 IP 주소를 만들거나 기존 공용 IP 주소를 업데이트합니다. |
|/publicIPAddresses/delete|공용 IP 주소를 삭제합니다.|
|/publicIPAddresses/join/action|공용 IP 주소를 연결합니다.|
|/routeFilters/read|경로 필터 정의를 가져옵니다.|
|/routeFilters/join/action|경로 필터를 연결합니다.|
|/routeFilters/delete|경로 필터 정의를 삭제합니다.|
|/routeFilters/write|경로 필터를 만들거나 기존 경로 필터를 업데이트합니다.|
|/routeFilters/rules/read|경로 필터 규칙 정의를 가져옵니다.|
|/routeFilters/rules/write|경로 필터 규칙을 만들거나 기존 경로 필터를 규칙을 업데이트합니다.|
|/routeFilters/rules/delete|경로 필터 규칙 정의를 삭제합니다.|
|/networkWatchers/read|Network Watcher 정의를 가져옵니다.|
|/networkWatchers/write|Network Watcher를 만들거나 기존 Network Watcher를 업데이트합니다.|
|/networkWatchers/delete|Network Watcher를 삭제합니다.|
|/networkWatchers/configureFlowLog/action|대상 리소스에 대한 흐름 로깅을 구성합니다.|
|/networkWatchers/ipFlowVerify/action|패킷이 특정 대상에서 허용될지 또는 거부될지를 반환합니다.|
|/networkWatchers/nextHop/action|지정된 목표 및 대상 IP 주소에 대해 다음 홉 형식 및 다음 홉 IP 주소를 반환합니다.|
|/networkWatchers/queryFlowLogStatus/action|리소스에 대한 흐름 로깅의 상태를 가져옵니다.|
|/networkWatchers/queryTroubleshootResult/action|이전에 실행되었거나 현재 실행 중인 문제 해결 작업의 문제 해결 결과를 가져옵니다.|
|/networkWatchers/securityGroupView/action|VM에 적용된 유효한 구성된 네트워크 보안 그룹 규칙을 봅니다.|
|/networkWatchers/topology/action|리소스 그룹에 포함된 리소스의 네트워크 수준 보기 및 해당 관계를 가져옵니다.|
|/networkWatchers/troubleshoot/action|Azure의 네트워킹 리소스에 대한 문제 해결을 시작합니다.|
|/networkWatchers/packetCaptures/queryStatus/action|패킷 캡처 리소스의 속성 및 상태에 대한 정보를 가져옵니다.|
|/networkWatchers/packetCaptures/stop/action|실행 중인 패킷 캡처 세션을 중지합니다.|
|/networkWatchers/packetCaptures/read|패킷 캡처 정의를 가져옵니다.|
|/networkWatchers/packetCaptures/write|패킷 캡처를 만듭니다.|
|/networkWatchers/packetCaptures/delete|패킷 캡처를 삭제합니다.|
|/loadBalancers/read|부하 분산 장치 정의를 가져옵니다.|
|/loadBalancers/write|부하 분산 장치를 만들거나 기존 부하 분산 장치를 업데이트합니다.|
|/loadBalancers/delete|부하 분산 장치를 삭제합니다.|
|/loadBalancers/networkInterfaces/read|부하 분산 장치의 모든 네트워크 인터페이스에 대한 참조를 가져옵니다.|
|/loadBalancers/loadBalancingRules/read|부하 분산 장치 부하 분산 규칙 정의를 가져옵니다.|
|/loadBalancers/backendAddressPools/read|부하 분산 장치 백 엔드 주소 풀 정의를 가져옵니다.|
|/loadBalancers/backendAddressPools/join/action|부하 분산 장치 백 엔드 주소 풀을 연결합니다.|
|/loadBalancers/inboundNatPools/read|부하 분산 장치 인바운드 NAT 풀 정의를 가져옵니다.|
|/loadBalancers/inboundNatPools/join/action|부하 분산 장치 인바운드 NAT 풀을 연결합니다.|
|/loadBalancers/inboundNatRules/read|부하 분산 장치 인바운드 NAT 규칙 정의를 가져옵니다.|
|/loadBalancers/inboundNatRules/write|부하 분산 장치 인바운드 NAT 규칙을 만들거나 기존 부하 분산 장치의 인바운드 NAT 규칙을 업데이트합니다.|
|/loadBalancers/inboundNatRules/delete|부하 분산 장치 인바운드 NAT 규칙을 삭제합니다.|
|/loadBalancers/inboundNatRules/join/action|부하 분산 장치 인바운드 NAT 규칙을 연결합니다.|
|/loadBalancers/outboundNatRules/read|부하 분산 장치 아웃바운드 NAT 규칙 정의를 가져옵니다.|
|/loadBalancers/probes/read|부하 분산 장치 프로브를 가져옵니다.|
|/loadBalancers/virtualMachines/read|부하 분산 장치의 모든 가상 컴퓨터에 대한 참조를 가져옵니다.|
|/loadBalancers/frontendIPConfigurations/read|부하 분산 장치 프런트 엔드 IP 구성 정의를 가져옵니다.|
|/trafficManagerGeographicHierarchies/read|지리적 트래픽 라우팅 메서드에서 사용할 수 있는 영역을 포함하는 Traffic Manager 지리적 계층 구조를 가져옵니다.|
|/bgpServiceCommunities/read|BGP 서비스 커뮤니티를 가져옵니다.|
|/applicationGatewayAvailableWafRuleSets/read|Application Gateway 사용 가능 WAF 규칙 집합을 가져옵니다.|
|/virtualNetworks/read|가상 네트워크 정의를 가져옵니다.|
|/virtualNetworks/write|가상 네트워크를 만들거나 기존 가상 네트워크를 업데이트합니다.|
|/virtualNetworks/delete|가상 네트워크를 삭제합니다.|
|/virtualNetworks/peer/action|가상 네트워크를 다른 가상 네트워크와 피어링합니다.|
|/virtualNetworks/virtualNetworkPeerings/read|가상 네트워크 피어링 정의를 가져옵니다.|
|/virtualNetworks/virtualNetworkPeerings/write|가상 네트워크 피어링을 만들거나 기존 가상 네트워크 피어링을 업데이트합니다.|
|/virtualNetworks/virtualNetworkPeerings/delete|가상 네트워크 피어링을 삭제합니다.|
|/virtualNetworks/subnets/read|가상 네트워크 서브넷 정의를 가져옵니다.|
|/virtualNetworks/subnets/write|가상 네트워크 서브넷을 만들거나 기존 가상 네트워크 서브넷을 업데이트합니다.|
|/virtualNetworks/subnets/delete|가상 네트워크 서브넷을 삭제합니다.|
|/virtualNetworks/subnets/join/action|가상 네트워크를 연결합니다.|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|저장소 계정 또는 SQL Database와 같은 리소스를 서비스 터널링 지원 서브넷에 연결합니다.|
|/virtualNetworks/subnets/virtualMachines/read|가상 네트워크 서브넷의 모든 가상 컴퓨터에 대한 참조를 가져옵니다.|
|/virtualNetworks/checkIpAddressAvailability/read|IP 주소를 지정된 가상 네트워크에서 사용할 수 있는지 확인합니다.|
|/virtualNetworks/virtualMachines/read|가상 네트워크의 모든 가상 컴퓨터에 대한 참조를 가져옵니다.|
|/expressRouteServiceProviders/read|Express Route 서비스 공급자를 가져옵니다.|
|/dnsoperationresults/read|DNS 작업의 결과를 가져옵니다.|
|/localnetworkgateways/read|LocalNetworkGateway를 가져옵니다.|
|/localnetworkgateways/write|LocalNetworkGateway를 만들거나 기존 LocalNetworkGateway를 업데이트합니다.|
|/localnetworkgateways/delete|LocalNetworkGateway를 삭제합니다.|
|/trafficManagerProfiles/read|Traffic Manager 프로필 구성을 가져옵니다. 여기에는 DNS 설정, 트래픽 라우팅 설정, 끝점 모니터링 설정 및 이 Traffic Manager 프로필이 라우팅하는 끝점 목록이 포함됩니다.|
|/trafficManagerProfiles/write|Traffic Manager 프로필을 만들거나 기존 Traffic Manager 프로필의 구성을 수정합니다. 여기에는 프로필을 사용하거나 사용하지 않도록 설정, DNS 설정, 트래픽 라우팅 설정 또는 끝점 모니터링 설정의 수정이 포함됩니다. Traffic Manager 프로필이 라우팅하는 끝점을 추가 및 제거하고, 사용하거나 사용하지 않도록 설정할 수 있습니다.|
|/trafficManagerProfiles/delete|Traffic Manager 프로필을 삭제합니다. Traffic Manager 프로필과 연결된 모든 설정이 손실되고 프로필은 더 이상 트래픽을 라우팅하는 데 사용할 수 없습니다.|
|/dnsoperationstatuses/read|DNS 작업의 상태를 가져옵니다. |
|/operations/read|사용 가능한 작업을 가져옵니다.|
|/expressRouteCircuits/read|ExpressRouteCircuit을 가져옵니다.|
|/expressRouteCircuits/write|ExpressRouteCircuit을 만들거나 기존 ExpressRouteCircuit을 업데이트합니다.|
|/expressRouteCircuits/delete|ExpressRouteCircuit을 삭제합니다.|
|/expressRouteCircuits/stats/read|ExpressRouteCircuit Stat을 가져옵니다.|
|/expressRouteCircuits/peerings/read|ExpressRouteCircuit Peering을 가져옵니다.|
|/expressRouteCircuits/peerings/write|ExpressRouteCircuit Peering을 만들거나 기존 ExpressRouteCircuit Peering을 업데이트합니다.|
|/expressRouteCircuits/peerings/delete|ExpressRouteCircuit Peering을 삭제합니다.|
|/expressRouteCircuits/peerings/arpTables/action|ExpressRouteCircuit Peering ArpTable을 가져옵니다.|
|/expressRouteCircuits/peerings/routeTables/action|ExpressRouteCircuit Peering RouteTable을 가져옵니다.|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|ExpressRouteCircuit Peering RouteTable Summary를 가져옵니다.|
|/expressRouteCircuits/peerings/stats/read|ExpressRouteCircuit Peering Stat을 가져옵니다.|
|/expressRouteCircuits/authorizations/read|ExpressRouteCircuit Authorization을 가져옵니다.|
|/expressRouteCircuits/authorizations/write|ExpressRouteCircuit Authorization을 만들거나 기존 ExpressRouteCircuit Authorization을 업데이트합니다.|
|/expressRouteCircuits/authorizations/delete|ExpressRouteCircuit Authorization을 삭제합니다.|
|/connections/read|VirtualNetworkGatewayConnection을 가져옵니다.|
|/connections/write|VirtualNetworkGatewayConnection을 만들거나 기존 VirtualNetworkGatewayConnection을 업데이트합니다.|
|/connections/delete|VirtualNetworkGatewayConnection을 삭제합니다.|
|/connections/sharedKey/read|VirtualNetworkGatewayConnection SharedKey를 가져옵니다.|
|/connections/sharedKey/write|VirtualNetworkGatewayConnection SharedKey를 만들거나 기존 VirtualNetworkGatewayConnection SharedKey를 업데이트합니다.|
|/networkSecurityGroups/read|네트워크 보안 그룹 정의를 가져옵니다.|
|/networkSecurityGroups/write|네트워크 보안 그룹을 만들거나 기존 네트워크 보안 그룹을 업데이트합니다.|
|/networkSecurityGroups/delete|네트워크 보안 그룹을 삭제합니다.|
|/networkSecurityGroups/join/action|네트워크 보안 그룹을 연결합니다.|
|/networkSecurityGroups/defaultSecurityRules/read|기본 보안 규칙 정의를 가져옵니다.|
|/networkSecurityGroups/securityRules/read|보안 규칙 정의를 가져옵니다.|
|/networkSecurityGroups/securityRules/write|보안 규칙을 만들거나 기존 보안 규칙을 업데이트합니다.|
|/networkSecurityGroups/securityRules/delete|보안 규칙을 삭제합니다.|
|/applicationGateways/read|Application Gateway를 가져옵니다.|
|/applicationGateways/write|Application Gateway를 만들거나 Application Gateway를 업데이트합니다.|
|/applicationGateways/delete|Application Gateway를 삭제합니다.|
|/applicationGateways/backendhealth/action|Application Gateway 백 엔드 상태를 가져옵니다.|
|/applicationGateways/start/action|Application Gateway를 시작합니다.|
|/applicationGateways/stop/action|Application Gateway를 중지합니다.|
|/applicationGateways/backendAddressPools/join/action|Application Gateway 백 엔드 주소 풀을 연결합니다.|
|/routeTables/read|경로 테이블 정의를 가져옵니다.|
|/routeTables/write|경로 테이블을 만들거나 기존 경로 테이블을 업데이트합니다.|
|/routeTables/delete|경로 테이블 정의를 삭제합니다.|
|/routeTables/join/action|경로 테이블을 연결합니다.|
|/routeTables/routes/read|경로 정의를 가져옵니다.|
|/routeTables/routes/write|경로를 만들거나 기존 경로를 업데이트합니다.|
|/routeTables/routes/delete|경로 정의를 삭제합니다.|
|/locations/operationResults/read|비동기 POST 또는 DELETE 작업의 작업 결과를 가져옵니다.|
|/locations/checkDnsNameAvailability/read|DNS 레이블을 지정된 위치에서 사용할 수 있는지 확인합니다.|
|/locations/usages/read|리소스 사용 메트릭을 가져옵니다.|
|/locations/operations/read|비동기 작업의 상태를 나타내는 작업 리소스를 가져옵니다.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| 작업 | 설명 |
|---|---|
|/register/action|NotifciationHubs 리소스 공급자에 대한 구독을 등록하고 네임스페이스와 NotificationHubs 만들기를 사용하도록 설정합니다.|
|/CheckNamespaceAvailability/action|주어진 네임스페이스 리소스 이름을 NotificationHub 서비스 내에서 사용할 수 있는지 여부를 확인합니다.|
|/Namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 상태는 업데이트할 수 있는 속성입니다.|
|/Namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/Namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/Namespaces/authorizationRules/action|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/Namespaces/CheckNotificationHubAvailability/action|주어진 NotificationHub 이름을 네임스페이스 내에서 사용할 수 있는지 여부를 확인합니다.|
|/Namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/Namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/Namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/Namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/Namespaces/authorizationRules/regenerateKeys/action|네임스페이스 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요.|
|/Namespaces/NotificationHubs/write|알림 허브를 만들고 해당 속성을 업데이트합니다. 속성에는 주로 PNS 자격 증명, 권한 부여 규칙 및 TTL이 포함됩니다. 권한 부여 규칙 및 TTL|
|/Namespaces/NotificationHubs/read|알림 허브 리소스 설명 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/Delete|알림 허브 리소스를 삭제합니다.|
|/Namespaces/NotificationHubs/authorizationRules/action|알림 허브 권한 부여 규칙 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/pnsCredentials/action|모든 알림 허브 PNS 자격 증명을 가져옵니다. 여기에는 WNS, MPNS, APNS, GCM 및 Baidu 자격 증명이 포함됩니다.|
|/Namespaces/NotificationHubs/debugSend/action|테스트 푸시 알림을 보냅니다.|
|/Namespaces/NotificationHubs/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|알림 허브 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|알림 허브 권한 부여 규칙 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|알림 허브 권한 부여 규칙을 삭제합니다.|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|알림 허브 연결 문자열을 가져옵니다.|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|알림 허브 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| 작업 | 설명 |
|---|---|
|/register/action|리소스 공급자에 구독을 등록합니다.|
|/linkTargets/read|Azure 구독과 연결되지 않은 기존 계정 목록을 표시합니다. Azure 구독을 작업 영역에 연결하려면 이 작업에서 반환된 고객 ID를 작업 영역 만들기 작업의 고객 ID 속성에 사용하세요.|
|/workspaces/write|새 작업 영역을 만들거나 기존 작업 영역의 고객 ID를 제공하여 기존 작업 영역에 연결합니다.|
|/workspaces/read|기존 작업 영역을 가져옵니다.|
|/workspaces/delete|작업 영역을 삭제합니다. 생성 시 작업 영역이 기존 작업 영역에 연결된 경우 연결된 작업 영역은 삭제되지 않습니다.|
|/workspaces/generateregistrationcertificate/action|작업 영역에 대한 등록 인증서를 생성합니다. 이 인증서는 Microsoft System Center Operation Manager를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/sharedKeys/action|작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/search/action|검색 쿼리를 실행합니다.|
|/workspaces/datasources/read|작업 영역의 데이터 원본을 가져옵니다.|
|/workspaces/datasources/write|작업 영역의 데이터 원본을 생성/업데이트합니다.|
|/workspaces/datasources/delete|작업 영역의 데이터 원본을 삭제합니다.|
|/workspaces/managementGroups/read|이 작업 영역에 연결된 System Center Operations Manager 관리 그룹의 이름 및 메타데이터를 가져옵니다.|
|/workspaces/schema/read|작업 영역에 대한 검색 스키마를 가져옵니다.  검색 스키마에는 노출된 필드와 필드 형식이 포함되어 있습니다.|
|/workspaces/usages/read|작업 영역에서 읽는 데이터 양을 포함하여 작업 영역에 대한 사용 현황 데이터를 가져옵니다.|
|/workspaces/intelligencepacks/read|지정된 작업 영역에 대해 표시되는 모든 인텔리전스 팩 목록과 해당 작업 영역에 팩을 사용할 수 있는지 여부를 표시합니다.|
|/workspaces/intelligencepacks/enable/action|지정된 작업 영역에 인텔리전스 팩을 사용하도록 설정합니다.|
|/workspaces/intelligencepacks/disable/action|지정된 작업 영역에 인텔리전스 팩을 사용하지 않도록 설정합니다.|
|/workspaces/sharedKeys/read|작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/savedSearches/read|저장된 검색 쿼리를 가져옵니다.|
|/workspaces/savedSearches/write|저장된 검색 쿼리를 만듭니다.|
|/workspaces/savedSearches/delete|저장된 검색 쿼리를 삭제합니다.|
|/workspaces/storageinsightconfigs/write|새 저장소 구성을 만듭니다. 이러한 구성은 기존 저장소 계정의 위치에서 데이터를 가져오는 데 사용됩니다.|
|/workspaces/storageinsightconfigs/read|저장소 구성을 가져옵니다.|
|/workspaces/storageinsightconfigs/delete|저장소 구성을 삭제합니다. 이렇게 하면 Microsoft Operational Insights에서 저장소 계정의 데이터 읽기를 중지합니다.|
|/workspaces/configurationScopes/read|구성 범위를 가져옵니다.|
|/workspaces/configurationScopes/write|구성 범위를 설정합니다.|
|/workspaces/configurationScopes/delete|구성 범위를 삭제합니다.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| 작업 | 설명 |
|---|---|
|/register/action|리소스 공급자에 구독을 등록합니다.|
|/solutions/write|새 OMS 솔루션을 만듭니다.|
|/solutions/read|기존 OMS 솔루션을 가져옵니다.|
|/solutions/delete|기존 OMS 솔루션을 삭제합니다.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| 작업 | 설명 |
|---|---|
|/Vaults/backupJobsExport/action|작업을 내보냅니다.|
|/Vaults/write|자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다.|
|/Vaults/read|자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다.|
|/Vaults/delete|자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다.|
|/Vaults/refreshContainers/read|컨테이너 목록을 새로 고칩니다.|
|/Vaults/backupJobsExport/operationResults/read|내보내기 작업의 작업 결과를 반환합니다.|
|/Vaults/backupOperationResults/read|Recovery Services 자격 증명 모음의 백업 작업 결과를 반환합니다.|
|/Vaults/monitoringAlerts/read|Recovery Services 자격 증명 모음에 대한 경고를 받습니다.|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|경고에 대한 세부 정보를 가져옵니다.|
|/Vaults/backupSecurityPIN/read|Recovery Services 자격 증명 모음에 대한 보안 PIN 정보를 반환합니다.|
|/vaults/replicationEvents/read|이벤트를 읽습니다.|
|/Vaults/backupProtectableItems/read|모든 보호 가능한 항목 목록을 반환합니다.|
|/vaults/replicationFabrics/read|패브릭을 읽습니다.|
|/vaults/replicationFabrics/write|패브릭을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/remove/action|패브릭을 제거합니다.|
|/vaults/replicationFabrics/checkConsistency/action|패브릭의 일관성을 검사합니다.|
|/vaults/replicationFabrics/delete|패브릭을 삭제합니다.|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|프로세스 서버 이미지를 배포합니다.|
|/vaults/replicationFabrics/reassociateGateway/action|게이트웨이를 다시 연결합니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|Recovery Services 공급자를 읽습니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Recovery Services 공급자를 제거합니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>delete|Recovery Services 공급자를 삭제합니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|공급자를 새로 고칩니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/read|저장소 분류를 읽습니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|저장소 분류 매핑을 읽습니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|저장소 분류 매핑을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|저장소 분류 매핑을 삭제합니다.|
|/vaults/replicationFabrics/replicationvCenters/read|작업을 읽습니다.|
|/vaults/replicationFabrics/replicationvCenters/write|작업을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationvCenters/delete|작업을 삭제합니다.|
|/vaults/replicationFabrics/replicationNetworks/read|네트워크를 읽습니다.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|네트워크 매핑을 읽습니다.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|네트워크 매핑을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|네트워크 매핑을 삭제합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|보호 컨테이너를 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|보호 가능한 항목을 검색합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|보호 컨테이너를 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|보호 컨테이너를 제거합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|보호 컨테이너를 전환합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|보호 가능한 항목을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|보호 컨테이너 매핑을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|보호 컨테이너 매핑을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|보호 컨테이너 매핑을 제거합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|보호 컨테이너 매핑을 삭제합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|보호된 항목을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|보호된 항목을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|보호된 항목을 삭제합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|보호된 항목을 제거합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|계획된 장애 조치(Failover)|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|장애 조치(Failover)|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|테스트 장애 조치(Failover)|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|테스트 장애 조치(Failover) 정리|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|장애 조치(Failover) 커밋|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|보호된 항목을 다시 보호합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|모바일 서비스를 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|복제를 복구합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|복구 지점을 적용합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|복제 복구 지점을 읽습니다.|
|/vaults/replicationPolicies/read|정책을 읽습니다.|
|/vaults/replicationPolicies/write|정책을 만들거나 업데이트합니다.|
|/vaults/replicationPolicies/delete|정책을 삭제합니다.|
|/vaults/replicationRecoveryPlans/read|복구 계획을 읽습니다.|
|/vaults/replicationRecoveryPlans/write|복구 계획을 만들거나 업데이트합니다.|
|/vaults/replicationRecoveryPlans/delete|복구 계획을 삭제합니다.|
|/vaults/replicationRecoveryPlans/plannedFailover/action|계획된 장애 조치(Failover) 복구 계획|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|장애 조치(Failover) 복구 계획|
|/vaults/replicationRecoveryPlans/testFailover/action|테스트 장애 조치(failover) 복구 계획|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|테스트 장애 조치(failover) 정리 복구 계획|
|/vaults/replicationRecoveryPlans/failoverCommit/action|장애 조치(Failover) 커밋 복구 계획|
|/vaults/replicationRecoveryPlans/reProtect/action|복구 계획을 다시 보호합니다.|
|/Vaults/extendedInformation/read|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Vaults/extendedInformation/write|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Vaults/extendedInformation/delete|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Vaults/backupManagementMetaData/read|Recovery Services 자격 증명 모음의 백업 관리 메타데이터를 반환합니다.|
|/Vaults/backupProtectionContainers/read|구독에 속하는 컨테이너를 모두 반환합니다.|
|/Vaults/backupFabrics/operationResults/read|작업의 상태를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/read|등록된 모든 컨테이너를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|보호된 항목의 개체 정보를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|백업 보호 항목을 만듭니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|보호된 항목을 삭제합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|보호 항목 백업을 수행합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|보호 항목에 대해 수행된 작업의 결과를 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|보호 항목에 대해 수행된 작업의 상태를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|보호 항목의 복구 지점을 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|보호 항목의 복구 지점을 복원합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|보호된 항목에 대한 빠른 항목 복구를 프로비전합니다.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|보호된 항목에 대한 빠른 항목 복구를 취소합니다.|
|/Vaults/usages/read|Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다.|
|/vaults/usages/read|자격 증명 모음 사용 현황을 읽습니다.|
|/Vaults/certificates/write|리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다.|
|/Vaults/tokenInfo/read|Recovery Services 자격 증명 모음에 대한 토큰 정보를 반환합니다.|
|/vaults/replicationAlertSettings/read|경고 설정을 읽습니다.|
|/vaults/replicationAlertSettings/write|경고 설정을 만들거나 업데이트합니다.|
|/Vaults/backupOperations/read|Recovery Services 자격 증명 모음의 백업 작업 상태를 반환합니다.|
|/Vaults/storageConfig/read|Recovery Services 자격 증명 모음에 대한 저장소 구성을 반환합니다.|
|/Vaults/storageConfig/write|Recovery Services 자격 증명 모음에 대한 저장소 구성을 업데이트합니다.|
|/Vaults/backupUsageSummaries/read|Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다.|
|/Vaults/backupProtectedItems/read|모든 보호 항목 목록을 반환합니다.|
|/Vaults/backupconfig/vaultconfig/read|Recovery Services 자격 증명 모음에 구성을 반환합니다.|
|/Vaults/backupconfig/vaultconfig/write|Recovery Services 자격 증명 모음에 대한 구성을 업데이트합니다.|
|/Vaults/registeredIdentities/write|서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다.|
|/Vaults/registeredIdentities/read|컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다.|
|/Vaults/registeredIdentities/delete|컨테이너 등록 취소 작업을 사용하여 컨테이너 등록을 취소할 수 있습니다.|
|/Vaults/registeredIdentities/operationResults/read|작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다.|
|/vaults/replicationJobs/read|작업을 읽습니다.|
|/vaults/replicationJobs/cancel/action|작업을 취소합니다.|
|/vaults/replicationJobs/restart/action|작업을 다시 시작합니다.|
|/vaults/replicationJobs/resume/action|작업을 계속합니다.|
|/Vaults/backupPolicies/read|모든 보호 정책을 반환합니다.|
|/Vaults/backupPolicies/write|보호 정책을 만듭니다.|
|/Vaults/backupPolicies/delete|보호 정책을 삭제합니다.|
|/Vaults/backupPolicies/operationResults/read|정책 작업의 결과를 가져옵니다.|
|/Vaults/backupPolicies/operationStatus/read|정책 작업의 상태를 가져옵니다.|
|/Vaults/vaultTokens/read|자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다.|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Recovery Services 자격 증명 모음 알림 구성을 가져옵니다.|
|/Vaults/backupJobs/read|모든 작업 개체를 반환합니다.|
|/Vaults/backupJobs/cancel/action|작업을 취소합니다.|
|/Vaults/backupJobs/operationResults/read|작업의 작업 결과를 반환합니다.|
|/locations/allocateStamp/action|AllocateStamp는 서비스에서 사용하는 내부 작업입니다.|
|/locations/allocatedStamp/read|GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다.|

## <a name="microsoftrelay"></a>Microsoft.Relay

| 작업 | 설명 |
|---|---|
|/checkNamespaceAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다.|
|/register/action|릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다.|
|/namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 상태는 업데이트할 수 있는 속성입니다.|
|/namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/namespaces/HybridConnections/write|HybridConnection 속성을 만들거나 업데이트합니다.|
|/namespaces/HybridConnections/read|HybridConnection 리소스 설명의 목록을 가져옵니다.|
|/namespaces/HybridConnections/Delete|HybridConnection 리소스 삭제 작업|
|/namespaces/HybridConnections/authorizationRules/write|HybridConnection 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/HybridConnections/authorizationRules/delete|HybridConnection 권한 부여 규칙 삭제 작업|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|HybridConnection 연결 문자열을 가져옵니다.|
|/namespaces/WcfRelays/write|WcfRelay 속성을 만들거나 업데이트합니다.|
|/namespaces/WcfRelays/read|WcfRelay 리소스 설명의 목록을 가져옵니다.|
|/namespaces/WcfRelays/Delete|WcfRelay 리소스 삭제 작업|
|/namespaces/WcfRelays/authorizationRules/write|WcfRelay 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/WcfRelays/authorizationRules/delete|WcfRelay 권한 부여 규칙 삭제 작업|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|WcfRelay의 연결 문자열을 가져옵니다.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| 작업 | 설명 |
|---|---|
|/AvailabilityStatuses/read|지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다.|
|/AvailabilityStatuses/current/read|지정된 리소스에 대한 가용성 상태를 가져옵니다.|

## <a name="microsoftresources"></a>Microsoft.Resources

| 작업 | 설명 |
|---|---|
|/checkResourceName/action|리소스 이름의 유효성을 확인합니다.|
|/providers/read|공급자 목록을 가져옵니다.|
|/subscriptions/read|구독 목록을 가져옵니다.|
|/subscriptions/operationresults/read|구독 작업 결과를 가져옵니다.|
|/subscriptions/providers/read|리소스 공급자를 가져오거나 나열합니다.|
|/subscriptions/tagNames/read|구독 태그를 가져오거나 나열합니다.|
|/subscriptions/tagNames/write|구독 태그를 추가합니다.|
|/subscriptions/tagNames/delete|구독 태그를 삭제합니다.|
|/subscriptions/tagNames/tagValues/read|구독 태그 값을 가져오거나 나열합니다.|
|/subscriptions/tagNames/tagValues/write|구독 태그 값을 추가합니다.|
|/subscriptions/tagNames/tagValues/delete|구독 태그 값을 삭제합니다.|
|/subscriptions/resources/read|구독 리소스를 가져옵니다.|
|/subscriptions/resourceGroups/read|리소스 그룹을 가져오거나 나열합니다.|
|/subscriptions/resourceGroups/write|리소스 그룹을 만들거나 업데이트합니다.|
|/subscriptions/resourceGroups/delete|리소스 그룹과 해당하는 모든 리소스를 삭제합니다.|
|/subscriptions/resourceGroups/moveResources/action|한 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동합니다.|
|/subscriptions/resourceGroups/validateMoveResources/action|한 리소스 그룹에서 다른 리소스 그룹으로 리소스 이동의 유효성을 검사합니다.|
|/subscriptions/resourcegroups/resources/read|리소스 그룹에 대한 리소스를 가져옵니다.|
|/subscriptions/resourcegroups/deployments/read|배포를 가져오거나 나열합니다.|
|/subscriptions/resourcegroups/deployments/write|배포를 만들거나 업데이트합니다.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|배포 작업 상태를 가져오거나 나열합니다.|
|/subscriptions/resourcegroups/deployments/operations/read|배포 작업을 가져오거나 나열합니다.|
|/subscriptions/locations/read|지원되는 위치를 가져오거나 나열합니다.|
|/links/read|리소스 링크를 가져오거나 나열합니다.|
|/links/write|리소스 링크를 만들거나 업데이트합니다.|
|/links/delete|리소스 링크를 삭제합니다.|
|/tenants/read|테넌트 목록을 가져옵니다.|
|/resources/read|필터를 기준으로 리소스 목록을 가져옵니다.|
|/deployments/read|배포를 가져오거나 나열합니다.|
|/deployments/write|배포를 만들거나 업데이트합니다.|
|/deployments/delete|배포를 삭제합니다.|
|/deployments/cancel/action|배포를 취소합니다.|
|/deployments/validate/action|배포의 유효성을 검사합니다.|
|/deployments/operations/read|배포 작업을 가져오거나 나열합니다.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| 작업 | 설명 |
|---|---|
|/jobcollections/read|작업 컬렉션을 가져옵니다.|
|/jobcollections/write|작업 컬렉션을 만들거나 업데이트합니다.|
|/jobcollections/delete|작업 컬렉션을 삭제합니다.|
|/jobcollections/enable/action|작업 컬렉션을 사용하도록 설정합니다.|
|/jobcollections/disable/action|작업 컬렉션을 사용하지 않도록 설정합니다.|
|/jobcollections/jobs/read|작업을 가져옵니다.|
|/jobcollections/jobs/write|작업을 만들거나 업데이트합니다.|
|/jobcollections/jobs/delete|작업을 삭제합니다.|
|/jobcollections/jobs/run/action|작업을 실행합니다.|
|/jobcollections/jobs/generateLogicAppDefinition/action|스케줄러 작업을 기반으로 하여 논리 앱 정의를 생성합니다.|
|/jobcollections/jobs/jobhistories/read|작업 내역을 가져옵니다.|

## <a name="microsoftsearch"></a>Microsoft.Search

| 작업 | 설명 |
|---|---|
|/register/action|Search 리소스 공급자에 대한 구독을 등록하고 Search 서비스를 만들도록 설정합니다.|
|/checkNameAvailability/action|서비스 이름의 가용성을 확인합니다.|
|/searchServices/write|Search 서비스를 만들거나 업데이트합니다.|
|/searchServices/read|Search 서비스를 읽습니다.|
|/searchServices/delete|Search 서비스를 삭제합니다.|
|/searchServices/start/action|Search 서비스를 시작합니다.|
|/searchServices/stop/action|Search 서비스를 중지합니다.|
|/searchServices/listAdminKeys/action|관리 키를 읽습니다.|
|/searchServices/regenerateAdminKey/action|관리 키를 다시 생성합니다.|
|/searchServices/createQueryKey/action|쿼리 키를 만듭니다.|
|/searchServices/queryKey/read|쿼리 키를 읽습니다.|
|/searchServices/queryKey/delete|쿼리 키를 삭제합니다.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| 작업 | 설명 |
|---|---|
|/jitNetworkAccessPolicies/read|Just-In-Time 네트워크 액세스 정책을 가져옵니다.|
|/jitNetworkAccessPolicies/write|새 Just-In-Time 네트워크 액세스 정책을 만들거나 기존 Just-In-Time 네트워크 액세스 정책을 업데이트합니다.|
|/jitNetworkAccessPolicies/initiate/action|Just-In-Time 네트워크 액세스 정책을 시작합니다.|
|/securitySolutionsReferenceData/read|보안 솔루션 참조 데이터를 가져옵니다.|
|/securityStatuses/read|Azure 리소스의 보안 상태를 가져옵니다.|
|/webApplicationFirewalls/read|웹 응용 프로그램 방화벽을 가져옵니다.|
|/webApplicationFirewalls/write|새 웹 응용 프로그램 방화벽을 만들거나 기존 웹 응용 프로그램 방화벽을 업데이트합니다.|
|/webApplicationFirewalls/delete|웹 응용 프로그램 방화벽을 삭제합니다.|
|/securitySolutions/read|보안 솔루션을 가져옵니다.|
|/securitySolutions/write|새 보안 솔루션을 만들거나 기존 보안 솔루션을 업데이트합니다.|
|/securitySolutions/delete|보안 솔루션을 삭제합니다.|
|/tasks/read|모든 사용 가능한 보안 권장 사항을 가져옵니다.|
|/tasks/dismiss/action|보안 권장 사항을 해제합니다.|
|/tasks/activate/action|보안 권장 사항을 활성화합니다.|
|/policies/read|보안 규칙을 가져옵니다.|
|/policies/write|보안 규칙을 업데이트합니다.|
|/applicationWhitelistings/read|응용 프로그램 허용 목록을 가져옵니다.|
|/applicationWhitelistings/write|새 응용 프로그램 허용 목록을 만들거나 기존 응용 프로그램 허용 목록을 업데이트합니다.|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| 작업 | 설명 |
|---|---|
|/subscriptions/write|구독을 만들거나 업데이트합니다.|
|/gateways/write|게이트웨이를 만들거나 업데이트합니다.|
|/gateways/delete|게이트웨이를 삭제합니다.|
|/gateways/read|게이트웨이를 가져옵니다.|
|/gateways/regenerateprofile/action|게이트웨이 프로필을 다시 생성합니다.|
|/gateways/upgradetolatest/action|게이트웨이를 최신 버전으로 업그레이드합니다.|
|/nodes/write|노드를 만들거나 업데이트합니다.|
|/nodes/delete|노드를 삭제합니다.|
|/nodes/read|노드를 가져옵니다.|
|/sessions/write|세션을 만들거나 업데이트합니다.|
|/sessions/read|세션을 가져옵니다.|
|/sessions/delete|세션을 삭제합니다.|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다.|
|/register/action|ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다.|
|/namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 상태는 업데이트할 수 있는 속성입니다.|
|/namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/namespaces/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/namespaces/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/diagnosticSettings/read|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/diagnosticSettings/write|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/queues/write|큐 속성을 만들거나 업데이트합니다.|
|/namespaces/queues/read|큐 리소스 설명의 목록을 가져옵니다.|
|/namespaces/queues/Delete|큐 리소스 삭제 작업|
|/namespaces/queues/authorizationRules/write|큐 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/queues/authorizationRules/read| 큐 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/queues/authorizationRules/delete|큐 권한 부여 규칙 삭제 작업|
|/namespaces/queues/authorizationRules/listkeys/action|큐의 연결 문자열을 가져옵니다.|
|/namespaces/queues/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/logDefinitions/read|네임스페이스 로그 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/write|토픽 속성을 만들거나 업데이트합니다.|
|/namespaces/topics/read|토픽 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/Delete|토픽 리소스 삭제 작업|
|/namespaces/topics/authorizationRules/write|토픽 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/topics/authorizationRules/read| 토픽 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/topics/authorizationRules/delete|토픽 권한 부여 규칙 삭제 작업|
|/namespaces/topics/authorizationRules/listkeys/action|토픽의 연결 문자열을 가져옵니다.|
|/namespaces/topics/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/topics/subscriptions/write|TopicSubscription 속성을 만들거나 업데이트합니다.|
|/namespaces/topics/subscriptions/read|TopicSubscription 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/subscriptions/Delete|TopicSubscription 리소스 삭제 작업|
|/namespaces/topics/subscriptions/rules/write|규칙 속성을 만들거나 업데이트합니다.|
|/namespaces/topics/subscriptions/rules/read|규칙 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/subscriptions/rules/Delete|규칙 리소스 삭제 작업|

## <a name="microsoftsql"></a>Microsoft.Sql

| 작업 | 설명 |
|---|---|
|/servers/read|구독의 리소스 그룹에 있는 서버 목록을 반환합니다.|
|/servers/write|구독의 리소스 그룹에서 새 서버를 만들거나 기존 서버의 속성을 수정합니다.|
|/servers/delete|서버 및 포함된 모든 데이터베이스 및 탄력적 풀을 삭제합니다.|
|/servers/import/action|서버에서 새 데이터베이스를 만들고 DacPac 패키지의 스키마 및 데이터를 배포합니다.|
|/servers/upgrade/action|최신 버전의 서버에서 사용할 수 있는 새로운 기능을 사용하도록 설정하고 데이터베이스 버전 변환 맵을 지정합니다.|
|/servers/VulnerabilityAssessmentScans/action|취약점 평가 서버 검사를 실행합니다.|
|/servers/operationResults/read|작업은 하위 버전에서 상위 버전으로의 서버 업그레이드 진행률을 추적하는 데 사용됩니다.|
|/servers/operationResults/delete|진행 중인 서버 버전 업그레이드를 중단합니다.|
|/servers/securityAlertPolicies/read|지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다.|
|/servers/securityAlertPolicies/write|지정된 서버에 대한 서버 위협 검색을 변경합니다.|
|/servers/securityAlertPolicies/operationResults/read|서버 위협 검색 정책 설정 작업의 결과를 검색합니다.|
|/servers/administrators/read|서버 관리자의 세부 정보를 검색합니다.|
|/servers/administrators/write|서버 관리자를 만들거나 업데이트합니다.|
|/servers/administrators/delete|서버에서 서버 관리자를 삭제합니다.|
|/servers/recoverableDatabases/read|이 작업은 라이브 데이터베이스의 재해 복구 기능이 데이터베이스를 마지막 정상 지점으로 복원하는 데 사용합니다. 마지막 정상 백업에 대한 정보가 반환되지만 실제로 데이터베이스는 복원되지 않습니다.|
|/servers/serviceObjectives/read|지정된 서버에서 사용할 수 있는 서비스 수준 목표(성능 계층이라고도 함) 목록을 검색합니다.|
|/servers/firewallRules/read|서버 방화벽 규칙 세부 정보를 검색합니다.|
|/servers/firewallRules/write|서버에 연결하도록 허용되는 IP 주소 범위를 제어하는 서버 방화벽 규칙을 만들거나 업데이트합니다.|
|/servers/firewallRules/delete|서버에서 방화벽 규칙을 삭제합니다.|
|/servers/administratorOperationResults/read|서버 관리자의 작업 결과를 검색합니다.|
|/servers/recommendedElasticPools/read|기록 리소스 사용률에 따라 비용을 절감하거나 성능을 향상시키기 위한 Elastic Database 풀에 대한 권장 사항을 검색합니다.|
|/servers/recommendedElasticPools/metrics/read|지정된 서버에 대해 권장되는 Elastic Database 풀의 메트릭을 검색합니다.|
|/servers/recommendedElasticPools/databases/read|지정된 서버에 대해 권장되는 Elastic Database 풀에 추가할 데이터베이스를 검색합니다.|
|/servers/elasticPools/read|지정된 서버에서 Elastic Database 풀의 세부 정보를 검색합니다.|
|/servers/elasticPools/write|Elastic Database 풀을 새로 만들거나 기존 Elastic Database 풀의 속성을 변경합니다.|
|/servers/elasticPools/delete|기존 Elastic Database 풀을 삭제합니다.|
|/servers/elasticPools/operationResults/read|지정된 Elastic Database 풀 작업에 대한 세부 정보를 검색합니다.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/elasticPools/metrics/read|Elastic Database 풀 리소스 사용량 메트릭을 반환합니다.|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Elastic Database 풀에 속하는 지정된 데이터베이스에 대한 활동 및 세부 정보를 검색합니다.|
|/servers/elasticPools/advisors/read|탄력적 풀에 사용할 수 있는 Advisor의 목록을 반환합니다.|
|/servers/elasticPools/advisors/write|탄력적 풀 수준에서 Advisor의 자동 실행 상태를 업데이트합니다.|
|/servers/elasticPools/advisors/recommendedActions/read|탄력적 풀에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다.|
|/servers/elasticPools/advisors/recommendedActions/write|탄력적 풀에 권장 작업을 적용합니다.|
|/servers/elasticPools/elasticPoolActivity/read|지정된 Elastic Database 풀에 대한 활동 및 세부 정보를 검색합니다.|
|/servers/elasticPools/databases/read|지정된 서버에서 Elastic Database 풀에 속하는 데이터베이스의 목록 및 세부 정보를 검색합니다.|
|/servers/auditingPolicies/read|지정된 서버에 구성된 기본 서버 테이블 감사 정책의 세부 정보를 검색합니다.|
|/servers/auditingPolicies/write|지정된 서버에 대한 기본 서버 테이블 감사를 변경합니다.|
|/servers/disasterRecoveryConfiguration/operationResults/read|재해 복구 구성 작업 결과를 가져옵니다.|
|/servers/advisors/read|서버에 사용할 수 있는 Advisor의 목록을 반환합니다.|
|/servers/advisors/write|서버 수준에서 Advisor의 자동 실행 상태를 업데이트합니다.|
|/servers/advisors/recommendedActions/read|서버에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다.|
|/servers/advisors/recommendedActions/write|서버에 권장 작업을 적용합니다.|
|/servers/usages/read|서버 내의 모든 데이터베이스별 서버 DTU 할당량 및 현재 DTU 소비량을 반환합니다.|
|/servers/elasticPoolEstimates/read|이 서버에 대해 이미 작성된 탄력적 풀 예상치의 목록을 반환합니다.|
|/servers/elasticPoolEstimates/write|제공된 데이터베이스 목록에 대한 새 탄력적 풀 예상치를 만듭니다.|
|/servers/auditingSettings/read|지정된 서버에 구성된 서버 Blob 감사 정책의 세부 정보를 검색합니다.|
|/servers/auditingSettings/write|지정된 서버에 대한 서버 Blob 감사를 변경합니다.|
|/servers/auditingSettings/operationResults/read|서버 blob 감사 정책 집합 작업의 결과를 검색합니다.|
|/servers/backupLongTermRetentionVaults/read|이 작업은 백업 장기 보존 자격 증명 모음을 가져오는 데 사용됩니다. 서버에 등록된 자격 증명 모음에 대한 정보를 반환합니다.|
|/servers/backupLongTermRetentionVaults/write|백업 장기 보존 자격 증명 모음을 등록합니다.|
|/servers/restorableDroppedDatabases/read|여전히 보존 정책에 속하는 지정된 서버에서 삭제된 데이터베이스 목록을 검색합니다. 이 작업은 데이터베이스 목록 및 관련된 메타데이터(예: 삭제 날짜)를 반환합니다.|
|/servers/databases/read|구독의 리소스 그룹에 있는 서버 목록을 반환합니다.|
|/servers/databases/write|구독의 리소스 그룹에서 새 서버를 만들거나 기존 서버의 속성을 수정합니다.|
|/servers/databases/delete|서버 및 포함된 모든 데이터베이스 및 탄력적 풀을 삭제합니다.|
|/servers/databases/export/action|서버에서 새 데이터베이스를 만들고 DacPac 패키지의 스키마 및 데이터를 배포합니다.|
|/servers/databases/VulnerabilityAssessmentScans/action|취약점 평가 데이터베이스 검사를 실행합니다.|
|/servers/databases/pause/action|DataWarehouse 버전 데이터베이스를 일시 중지합니다.|
|/servers/databases/resume/action|DataWarehouse 버전 데이터베이스를 계속합니다.|
|/servers/databases/operationResults/read|작업은 크기 조정과 같은 장기 실행 데이터베이스 작업의 진행률을 추적하는 데 사용됩니다.|
|/servers/databases/replicationLinks/read|특정 데이터베이스에 설정된 복제 링크에 대한 세부 정보를 반환합니다.|
|/servers/databases/replicationLinks/delete|잠재적 데이터 손실이 발생하는 상태로 강제로 복제 관계를 종료합니다.|
|/servers/databases/replicationLinks/unlink/action|강제로 또는 파트너와 동기화한 후에 복제 관계를 종료합니다.|
|/servers/databases/replicationLinks/failover/action|주 데이터베이스의 모든 변경 내용을 동기화한 후에 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다.|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|잠재적 데이터 손실이 발생하는 상태로 즉시 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다.|
|/servers/databases/replicationLinks/updateReplicationMode/action|연결에 대한 복제 모드를 동기 또는 비동기 모드로 업데이트합니다.|
|/servers/databases/replicationLinks/operationResults/read|데이터베이스 복제 링크에 대한 장기 실행 작업의 상태를 가져옵니다.|
|/servers/databases/dataMaskingPolicies/read|지정된 데이터베이스에 구성된 데이터 마스킹 정책의 세부 정보를 검색합니다.|
|/servers/databases/dataMaskingPolicies/write|지정된 데이터베이스에 대한 데이터 마스킹 정책을 변경합니다.|
|/servers/databases/dataMaskingPolicies/rules/read|지정된 데이터베이스에 구성된 데이터 마스킹 정책 규칙의 세부 정보를 검색합니다.|
|/servers/databases/dataMaskingPolicies/rules/write|지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 변경합니다.|
|/servers/databases/securityAlertPolicies/read|지정된 데이터베이스에 구성된 위협 검색 정책의 세부 정보를 검색합니다.|
|/servers/databases/securityAlertPolicies/write|지정된 데이터베이스에 대한 위협 검색 정책을 변경합니다.|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|데이터베이스에 대해 사용 가능한 로그를 가져옵니다.|
|/servers/databases/topQueries/read|선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다.|
|/servers/databases/topQueries/queryText/read|선택한 쿼리 ID에 대한 Transact-SQL 텍스트를 반환합니다.|
|/servers/databases/topQueries/statistics/read|선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다.|
|/servers/databases/connectionPolicies/read|지정된 데이터베이스에 구성된 연결 정책의 세부 정보를 검색합니다.|
|/servers/databases/connectionPolicies/write|지정된 데이터베이스에 대한 연결 정책을 변경합니다.|
|/servers/databases/metrics/read|데이터베이스 리소스 사용량 메트릭을 반환합니다.|
|/servers/databases/auditRecords/read|데이터베이스 blob 감사 레코드를 검색합니다.|
|/servers/databases/transparentDataEncryption/read|지정된 데이터베이스에 대한 투명한 데이터 암호화 보안 기능의 상태 및 세부 정보를 검색합니다.|
|/servers/databases/transparentDataEncryption/write|지정된 데이터베이스에 대한 투명한 데이터 암호화를 사용하거나 사용하지 않도록 설정합니다.|
|/servers/databases/transparentDataEncryption/operationResults/read|지정된 데이터베이스에 대한 투명한 데이터 암호화 보안 기능의 상태 및 세부 정보를 검색합니다.|
|/servers/databases/auditingPolicies/read|지정된 데이터베이스에 구성된 테이블 감사 정책의 세부 정보를 검색합니다.|
|/servers/databases/auditingPolicies/write|지정된 데이터베이스에 대한 테이블 감사 정책을 변경합니다.|
|/servers/databases/dataWarehouseQueries/read|선택한 쿼리 ID에 대한 데이터 웨어하우스 배포 쿼리 정보를 반환합니다.|
|servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|선택한 단계 ID에 대한 데이터 웨어하우스 쿼리의 분산 쿼리 단계 정보를 반환합니다.|
|/servers/databases/serviceTierAdvisors/read|성능을 향상시키거나 비용을 절감하기 위해 쿼리 실행 통계를 기준으로 데이터베이스를 확장하거나 축소하기 위한 제안 사항을 반환합니다.|
|/servers/databases/advisors/read|데이터베이스에 사용할 수 있는 Advisor의 목록을 반환합니다.|
|/servers/databases/advisors/write|데이터베이스 수준에서 Advisor의 자동 실행 상태를 업데이트합니다.|
|/servers/databases/advisors/recommendedActions/read|데이터베이스에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다.|
|/servers/databases/advisors/recommendedActions/write|데이터베이스에 권장 작업을 적용합니다.|
|/servers/databases/usages/read|도달할 수 있는 데이터베이스 최대 크기 및 데이터가 차지하는 현재 크기를 반환합니다.|
|/servers/databases/queryStore/read|데이터베이스에 대한 쿼리 저장소 설정의 현재 값을 반환합니다.|
|/servers/databases/queryStore/write|데이터베이스에 대한 쿼리 저장소 설정을 업데이트합니다.|
|/servers/databases/auditingSettings/read|지정된 데이터베이스에 구성된 Blob 감사 정책의 세부 정보를 검색합니다.|
|/servers/databases/auditingSettings/write|지정된 데이터베이스에 대한 Blob 감사 정책을 변경합니다.|
|/servers/databases/schemas/tables/recommendedIndexes/read|데이터베이스에 대한 인덱스 권장 사항 목록을 검색합니다.|
|/servers/databases/schemas/tables/recommendedIndexes/write|인덱스 권장 사항을 적용합니다.|
|/servers/databases/schemas/tables/columns/read|테이블의 열 목록을 검색합니다.|
|/servers/databases/missingindexes/read|쿼리 성능을 개선하기 위해 만들거나, 수정하거나 삭제할 데이터베이스 인덱스에 대한 권장 사항을 반환합니다.|
|/servers/databases/missingindexes/write|특정 데이터베이스의 데이터베이스 인덱스 권장 사항을 사용합니다.|
|/servers/databases/importExportOperationResults/read|저장소 계정에 있는 DacPac에서의 데이터베이스 가져오기 또는 내보내기 작업에 대한 세부 정보를 반환합니다.|
|/servers/importExportOperationResults/read|지정된 서버의 저장소 계정에서의 데이터베이스 가져오기 작업에 대한 세부 정보 목록을 반환합니다.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| 작업 | 설명 |
|---|---|
|/register/action|저장소 리소스 공급자에 대한 구독을 등록하고 저장소 계정을 만들도록 설정합니다.|
|/checknameavailability/read|해당 계정 이름이 올바른지와 사용되고 있지 않은지 확인합니다.|
|/storageAccounts/write|지정된 매개 변수를 사용하여 저장소 계정을 만들거나, 속성 또는 태그를 업데이트하거나, 지정된 저장소 계정의 사용자 지정 도메인을 추가합니다.|
|/storageAccounts/delete|기존 저장소 계정을 삭제합니다.|
|/storageAccounts/listkeys/action|지정된 저장소 계정에 대한 액세스 키를 반환합니다.|
|/storageAccounts/regeneratekey/action|지정된 저장소 계정에 대한 액세스 키를 다시 생성합니다.|
|/storageAccounts/read|저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다.|
|/storageAccounts/listAccountSas/action|지정된 저장소 계정에 대한 계정 SAS 토큰을 반환합니다.|
|/storageAccounts/listServiceSas/action|저장소 서비스 SAS 토큰|
|/storageAccounts/services/diagnosticSettings/write|저장소 계정 진단 설정을 만들거나 업데이트합니다.|
|/skus/read|Microsoft Storage에서 지원하는 SKU를 나열합니다.|
|/usages/read|지정한 구독의 리소스에 대한 한도 및 현재 사용 수를 반환합니다.|
|/operations/read|비동기 작업의 상태를 폴링합니다.|
|/locations/deleteVirtualNetworkOrSubnets/action|가상 네트워크 또는 서브넷을 삭제 중임을 Microsoft.Storage에 알립니다.|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| 작업 | 설명 |
|---|---|
|/managers/clearAlerts/action|장치 관리자와 관련된 모든 경고를 선택 취소합니다.|
|/managers/getActivationKey/action|장치 관리자에 대한 활성화 키를 가져옵니다.|
|/managers/regenerateActivationKey/action|장치 관리자에 대한 활성화 키를 다시 생성합니다.|
|/managers/regenarateRegistationCertificate/action|장치 관리자에 대한 등록 인증서를 다시 생성합니다.|
|/managers/getEncryptionKey/action|장치 관리자에 대한 암호화 키를 가져옵니다.|
|/managers/read|장치 관리자를 나열하거나 가져옵니다.|
|/managers/delete|장치 관리자를 삭제합니다.|
|/managers/write|장치 관리자를 만들거나 업데이트합니다.|
|/managers/configureDevice/action|장치를 구성합니다.|
|/managers/listActivationKey/action|StorSimple 장치 관리자의 활성화 키를 가져옵니다.|
|/managers/listPublicEncryptionKey/action|StorSimple 장치 관리자의 공용 암호화 키를 나열합니다.|
|/managers/listPrivateEncryptionKey/action|StorSimple 장치 관리자의 개인 암호화 키를 가져옵니다.|
|/managers/provisionCloudAppliance/action|새 클라우드 어플라이언스를 만듭니다.|
|/Managers/write|자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다.|
|/Managers/read|자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다.|
|/Managers/delete|자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다.|
|/managers/storageAccountCredentials/write|저장소 계정 자격 증명을 만들거나 업데이트합니다.|
|/managers/storageAccountCredentials/read|저장소 계정 자격 증명을 나열하거나 가져옵니다.|
|/managers/storageAccountCredentials/delete|저장소 계정 자격 증명을 삭제합니다.|
|/managers/storageAccountCredentials/listAccessKey/action|저장소 계정 자격 증명의 액세스 키를 나열합니다.|
|/managers/accessControlRecords/read|액세스 제어 레코드를 나열하거나 가져옵니다.|
|/managers/accessControlRecords/write|액세스 제어 레코드를 만들거나 업데이트합니다.|
|/managers/accessControlRecords/delete|액세스 제어 레코드를 삭제합니다.|
|/managers/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/bandwidthSettings/read|대역폭 설정을 나열합니다(8000 시리즈만 해당).|
|/managers/bandwidthSettings/write|새 대역폭 설정을 만들거나 대역폭 설정을 업데이트합니다(8000 시리즈만 해당).|
|/managers/bandwidthSettings/delete|기존 대역폭 설정을 삭제합니다(8000 시리즈만 해당).|
|/Managers/extendedInformation/read|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Managers/extendedInformation/write|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Managers/extendedInformation/delete|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/managers/alerts/read|경고를 나열하거나 가져옵니다.|
|/managers/storageDomains/read|저장소 도메인을 나열하거나 가져옵니다.|
|/managers/storageDomains/write|저장소 도메인을 만들거나 업데이트합니다.|
|/managers/storageDomains/delete|저장소 도메인을 삭제합니다.|
|/managers/devices/scanForUpdates/action|장치에서 업데이트를 검색합니다.|
|/managers/devices/download/action|장치에 대한 업데이트를 다운로드합니다.|
|/managers/devices/install/action|장치에 업데이트를 설치합니다.|
|/managers/devices/read|장치를 나열하거나 가져옵니다.|
|/managers/devices/write|장치를 만들거나 업데이트합니다.|
|/managers/devices/delete|장치를 삭제합니다.|
|/managers/devices/deactivate/action|장치를 비활성화합니다.|
|/managers/devices/publishSupportPackage/action|Microsoft 지원 문제 해결을 위해 장치의 지원 패키지를 게시합니다.|
|/managers/devices/failover/action|장치의 장애 조치(Failover)입니다.|
|/managers/devices/sendTestAlertEmail/action|구성된 전자 메일 받는 사람에게 테스트 경고 전자 메일을 보냅니다.|
|/managers/devices/installUpdates/action|장치에 업데이트를 설치합니다.|
|/managers/devices/listFailoverSets/action|기존 장치에 대한 장애 조치(Failover) 설정을 나열합니다.|
|/managers/devices/listFailoverTargets/action|장치의 장애 조치(Failover) 대상을 나열합니다.|
|/managers/devices/publicEncryptionKey/action|장치 관리자의 공용 암호화 키를 나열합니다.|
|/managers/devices/hardwareComponentGroups/<br>read|하드웨어 구성 요소 그룹을 나열합니다.|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|하드웨어 구성 요소 그룹의 컨트롤러 전원 상태를 변경합니다.|
|/managers/devices/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/chapSettings/write|CHAP 설정을 만들거나 업데이트합니다.|
|/managers/devices/chapSettings/read|CHAP 설정을 나열하거나 가져옵니다.|
|/managers/devices/chapSettings/delete|CHAP 설정을 삭제합니다.|
|/managers/devices/backupScheduleGroups/read|백업 일정 그룹을 나열하거나 가져옵니다.|
|/managers/devices/backupScheduleGroups/write|백업 일정 그룹을 만들거나 업데이트합니다.|
|/managers/devices/backupScheduleGroups/delete|백업 일정 그룹을 삭제합니다.|
|/managers/devices/updateSummary/read|업데이트 요약을 나열하거나 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/<br>import/action|마이그레이션 위한 원본 구성을 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|마이그레이션 프로세스 기간을 예측하는 작업을 시작합니다.|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|원본 구성을 사용하여 마이그레이션을 시작합니다.|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|성공적인 마이그레이션을 확인하고 커밋합니다.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|마이그레이션 예측 작업의 상태를 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|마이그레이션의 상태를 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|마이그레이션 확인 상태를 가져옵니다.|
|/managers/devices/alertSettings/read|경고 설정을 나열하거나 가져옵니다.|
|/managers/devices/alertSettings/write|경고 설정을 만들거나 업데이트합니다.|
|/managers/devices/networkSettings/read|네트워크 설정을 나열하거나 가져옵니다.|
|/managers/devices/networkSettings/write|새 네트워크 설정을 만들거나 네트워크 설정을 업데이트합니다.|
|/managers/devices/jobs/read|작업을 나열하거나 가져옵니다.|
|/managers/devices/jobs/cancel/action|실행 중인 작업을 취소합니다.|
|/managers/devices/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/volumeContainers/write|새 볼륨 컨테이너를 만들거나 볼륨 컨테이너를 업데이트합니다(8000 시리즈만 해당).|
|/managers/devices/volumeContainers/read|볼륨 컨테이너를 나열합니다(8000 시리즈만 해당).|
|/managers/devices/volumeContainers/delete|기존 볼륨 컨테이너를 삭제합니다(8000 시리즈만 해당).|
|/managers/devices/volumeContainers/listEncryptionKeys/action|볼륨 컨테이너의 암호화 키를 나열합니다.|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|볼륨 컨테이너의 암호화 키를 롤오버합니다.|
|/managers/devices/volumeContainers/metrics/read|메트릭을 나열합니다|
|/managers/devices/volumeContainers/volumes/read|볼륨을 나열합니다.|
|/managers/devices/volumeContainers/volumes/write|새 볼륨을 만들거나 볼륨을 업데이트합니다.|
|/managers/devices/volumeContainers/volumes/delete|기존 볼륨을 삭제합니다.|
|/managers/devices/volumeContainers/volumes/metrics/read|메트릭을 나열합니다|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|메트릭 정의를 나열합니다.|
|/managers/devices/volumeContainers/metricsDefinitions/read|메트릭 정의를 나열합니다.|
|/managers/devices/iscsiservers/read|iSCSI 서버를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/write|iSCSI 서버를 만들거나 업데이트합니다.|
|/managers/devices/iscsiservers/delete|ISCSI 서버를 삭제합니다.|
|/managers/devices/iscsiservers/backup/action|ISCSI 서버 백업을 수행합니다.|
|/managers/devices/iscsiservers/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/disks/read|디스크를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/disks/write|디스크를 만들거나 업데이트합니다.|
|/managers/devices/iscsiservers/disks/delete|디스크를 삭제합니다.|
|/managers/devices/iscsiservers/disks/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/backups/read|백업 세트를 나열하거나 가져옵니다.|
|/managers/devices/backups/delete|백업 세트를 삭제합니다.|
|/managers/devices/backups/restore/action|백업 세트에서 모든 볼륨을 복원합니다.|
|/managers/devices/backups/elements/clone/action|백업 요소를 사용하여 공유 또는 볼륨을 복제합니다.|
|/managers/devices/backupPolicies/write|새 백업 정책을 만들거나 백업 정책을 업데이트합니다(8000 시리즈만 해당).|
|/managers/devices/backupPolicies/read|백업 정책을 나열합니다(8000 시리즈만 해당).|
|/managers/devices/backupPolicies/delete|기존 백업 정책을 삭제합니다(8000 시리즈만 해당).|
|/managers/devices/backupPolicies/backup/action|정책으로 보호되는 모든 볼륨의 요청 시 백업을 만들기 위해 수동 백업을 수행합니다.|
|/managers/devices/backupPolicies/schedules/write|새 일정을 만들거나 일정을 업데이트합니다.|
|/managers/devices/backupPolicies/schedules/read|일정을 나열합니다.|
|/managers/devices/backupPolicies/schedules/delete|기존 일정을 삭제합니다.|
|/managers/devices/securitySettings/update/action|보안 설정을 업데이트합니다.|
|/managers/devices/securitySettings/read|보안 설정을 나열합니다.|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|장치에 대한 원격 관리 인증서를 동기화합니다.|
|/managers/devices/securitySettings/write|새 보안 설정을 만들거나 보안 설정을 업데이트합니다.|
|/managers/devices/fileservers/read|파일 서버를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/write|파일 서버를 만들거나 업데이트합니다.|
|/managers/devices/fileservers/delete|파일 서버를 삭제합니다.|
|/managers/devices/fileservers/backup/action|파일 서버 백업을 수행합니다.|
|/managers/devices/fileservers/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/write|공유를 만들거나 업데이트합니다.|
|/managers/devices/fileservers/shares/read|공유를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/delete|공유를 삭제합니다.|
|/managers/devices/fileservers/shares/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/timeSettings/read|시간 설정을 나열하거나 가져옵니다.|
|/managers/devices/timeSettings/write|새 시간 설정을 만들거나 시간 설정을 업데이트합니다.|
|/Managers/certificates/write|리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다.|
|/managers/cloudApplianceConfigurations/read|클라우드 어플라이언스 지원 구성을 나열합니다.|
|/managers/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/encryptionSettings/read|암호화 설정을 나열하거나 가져옵니다.|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| 작업 | 설명 |
|---|---|
|/streamingjobs/Start/action|Stream Analytics 작업을 시작합니다.|
|/streamingjobs/Stop/action|Stream Analytics 작업을 중지합니다.|
|/streamingjobs/Read|Stream Analytics 작업을 읽습니다.|
|/streamingjobs/Write|Stream Analytics 작업을 씁니다.|
|/streamingjobs/Delete|Stream Analytics 작업을 삭제합니다.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|streamingjobs에 대해 사용 가능한 메트릭을 가져옵니다.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|진단 설정을 읽습니다.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|진단 설정을 씁니다.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|streamingjobs에 대해 사용 가능한 로그를 가져옵니다.|
|/streamingjobs/transformations/Read|Stream Analytics 작업 변환을 읽습니다.|
|/streamingjobs/transformations/Write|Stream Analytics 작업 변환을 씁니다.|
|/streamingjobs/transformations/Delete|Stream Analytics 작업 변환을 삭제합니다.|
|/streamingjobs/inputs/Read|Stream Analytics 작업 입력을 읽습니다.|
|/streamingjobs/inputs/Write|Stream Analytics 작업 입력을 씁니다.|
|/streamingjobs/inputs/Delete|Stream Analytics 작업 입력을 삭제합니다.|
|/streamingjobs/outputs/Read|Stream Analytics 작업 출력을 읽습니다.|
|/streamingjobs/outputs/Write|Stream Analytics 작업 출력을 씁니다.|
|/streamingjobs/outputs/Delete|Stream Analytics 작업 출력을 삭제합니다.|

## <a name="microsoftsupport"></a>Microsoft.Support

| 작업 | 설명 |
|---|---|
|/register/action|지원 리소스 공급자에 등록합니다.|
|/supportTickets/read|상태, 심각도, 연락처 세부 정보, 통신 등 지원 티켓 세부 정보를 얻거나 구독의 지원 티켓 목록을 얻습니다.|
|/supportTickets/write|지원 티켓을 만들거나 업데이트합니다. 기술, 청구, 할당량 또는 구독 관리 관련 문제에 대한 지원 티켓을 만들 수 있습니다. 기존 지원 티켓의 심각도, 연락처 세부 정보 및 통신을 업데이트할 수 있습니다.|

## <a name="microsoftweb"></a>Microsoft.Web

| 작업 | 설명 |
|---|---|
|/unregister/action|구독에 대한 Microsoft.Web 리소스 공급자를 등록 취소합니다.|
|/validate/action|유효성 검사를 수행합니다.|
|/register/action|구독에 대한 Microsoft.Web 리소스 공급자를 등록합니다.|
|/hostingEnvironments/Read|App Service 환경의 속성을 가져옵니다.|
|/hostingEnvironments/Write|새 App Service 환경을 만들거나 기존 App Service 환경을 업데이트합니다.|
|/hostingEnvironments/Delete|App Service 환경을 삭제합니다.|
|/hostingEnvironments/reboot/Action|App Service 환경의 모든 컴퓨터를 다시 부팅합니다.|
|/hostingenvironments/resume/action|호스팅 환경을 계속합니다.|
|/hostingenvironments/suspend/action|호스팅 환경을 일시 중단합니다.|
|/hostingenvironments/metricdefinitions/read|호스팅 환경 메트릭 정의를 가져옵니다.|
|/hostingEnvironments/workerPools/Read|App Service 환경의 작업자 풀 속성을 가져옵니다.|
|/hostingEnvironments/workerPools/Write|App Service 환경에 새 작업자 풀을 만들거나 기존 작업자 풀을 업데이트합니다.|
|/hostingenvironments/workerpools/metricdefinitions/read|호스팅 환경 Workerpools 메트릭 정의를 가져옵니다.|
|/hostingenvironments/workerpools/metrics/read|호스팅 환경 Workerpools 메트릭을 가져옵니다.|
|/hostingenvironments/workerpools/skus/read|호스팅 환경 Workerpools SKU를 가져옵니다.|
|/hostingenvironments/workerpools/usages/read|호스팅 환경 Workerpools 사용 현황을 가져옵니다.|
|/hostingenvironments/sites/read|호스팅 환경 웹앱을 가져옵니다.|
|/hostingenvironments/serverfarms/read|호스팅 환경 App Service 계획을 가져옵니다.|
|/hostingenvironments/usages/read|호스팅 환경 사용 현황을 가져옵니다.|
|/hostingenvironments/capacities/read|호스팅 환경 용량을 가져옵니다.|
|/hostingenvironments/operations/read|호스팅 환경 작업을 가져옵니다.|
|/hostingEnvironments/multiRolePools/Read|App Service 환경의 FrontEnd 풀 속성을 가져옵니다.|
|/hostingEnvironments/multiRolePools/Write|App Service 환경에 새 FrontEnd 풀을 만들거나 기존 FrontEnd 풀을 업데이트합니다.|
|/hostingenvironments/multirolepools/metricdefinitions/read|호스팅 환경 MultiRole 풀 메트릭 정의를 가져옵니다.|
|/hostingenvironments/multirolepools/metrics/read|호스팅 환경 MultiRole 풀 메트릭을 가져옵니다.|
|/hostingenvironments/multirolepools/skus/read|호스팅 환경 MultiRole 풀 SKU를 가져옵니다.|
|/hostingenvironments/multirolepools/usages/read|호스팅 환경 MultiRole 풀 사용 현황을 가져옵니다.|
|/hostingenvironments/diagnostics/read|호스팅 환경 진단을 가져옵니다.|
|/publishingusers/read|게시 사용자를 가져옵니다.|
|/publishingusers/write|게시 사용자를 업데이트합니다.|
|/checknameavailability/read|리소스 이름이 사용 가능한지 확인합니다.|
|/geoRegions/Read|지리적 영역 목록을 가져옵니다.|
|/sites/Read|웹앱의 속성을 가져옵니다.|
|/sites/Write|새 웹앱을 만들거나 기존 웹앱을 업데이트합니다.|
|/sites/Delete|기존 웹앱 삭제|
|/sites/backup/Action|새 웹앱 백업을 만듭니다.|
|/sites/publishxml/Action|웹앱에 대한 게시 프로필 xml을 가져옵니다.|
|/sites/publish/Action|웹앱을 게시합니다.|
|/sites/restart/Action|웹앱을 다시 시작합니다.|
|/sites/start/Action|웹앱을 시작합니다.|
|/sites/stop/Action|웹앱을 중지합니다.|
|/sites/slotsswap/Action|웹앱 배포 슬롯을 교환합니다.|
|/sites/slotsdiffs/Action|웹앱 및 슬롯 간 구성 차이를 가져옵니다.|
|/sites/applySlotConfig/Action|대상 슬롯의 웹앱 슬롯 구성을 현재 웹앱에 적용합니다.|
|/sites/resetSlotConfig/Action|웹앱 구성을 다시 설정합니다.|
|/sites/functions/action|합수 웹앱입니다.|
|/sites/listsyncfunctiontriggerstatus/action|함수 트리거 상태 동기화 웹앱을 나열합니다.|
|/sites/networktrace/action|네트워크 추적 웹앱입니다.|
|/sites/newpassword/action|Newpassword 웹앱입니다.|
|/sites/sync/action|웹앱을 동기화합니다.|
|/sites/operationresults/read|웹앱 작업 결과를 가져옵니다.|
|/sites/webjobs/read|웹앱 Webjob을 가져옵니다.|
|/sites/backup/read|웹앱 백업을 가져옵니다.|
|/sites/backup/write|웹앱 백업을 업데이트합니다.|
|/sites/metricdefinitions/read|웹앱 메트릭 정의를 가져옵니다.|
|/sites/metrics/read|웹앱 메트릭을 가져옵니다.|
|/sites/continuouswebjobs/delete|웹앱 연속 웹 작업을 삭제합니다.|
|/sites/continuouswebjobs/read|웹앱 연속 웹 작업을 가져옵니다.|
|/sites/continuouswebjobs/start/action|웹앱 연속 웹 작업을 시작합니다.|
|/sites/continuouswebjobs/stop/action|웹앱 연속 웹 작업을 중지합니다.|
|/sites/domainownershipidentifiers/read|웹앱 도메인 소유권 식별자를 가져옵니다.|
|/sites/domainownershipidentifiers/write|웹앱 도메인 소유권 식별자를 업데이트합니다.|
|/sites/premieraddons/delete|웹앱 프리미어 추가 기능을 삭제합니다.|
|/sites/premieraddons/read|웹앱 프리미어 추가 기능을 가져옵니다.|
|/sites/premieraddons/write|웹앱 프리미어 추가 기능을 업데이트합니다.|
|/sites/triggeredwebjobs/delete|웹앱 트리거 Webjob을 삭제합니다.|
|/sites/triggeredwebjobs/read|웹앱 트리거 Webjob을 가져옵니다.|
|/sites/triggeredwebjobs/run/action|웹앱 트리거 Webjob을 실행합니다.|
|/sites/hostnamebindings/delete|웹앱 호스트 이름 바인딩을 삭제합니다.|
|/sites/hostnamebindings/read|웹앱 호스트 이름 바인딩을 가져옵니다.|
|/sites/hostnamebindings/write|웹앱 호스트 이름 바인딩을 업데이트합니다.|
|/sites/virtualnetworkconnections/delete|웹앱 가상 네트워크 연결을 삭제합니다.|
|/sites/virtualnetworkconnections/read|웹앱 가상 네트워크 연결을 가져옵니다.|
|/sites/virtualnetworkconnections/write|웹앱 가상 네트워크 연결을 업데이트합니다.|
|/sites/virtualnetworkconnections/gateways/read|웹앱 가상 네트워크 연결 게이트웨이를 가져옵니다.|
|/sites/virtualnetworkconnections/gateways/write|웹앱 가상 네트워크 연결 게이트웨이를 업데이트합니다.|
|/sites/publishxml/read|웹앱 게시 XML을 가져옵니다.|
|/sites/hybridconnectionrelays/read|웹앱 하이브리드 연결 릴레이를 가져옵니다.|
|/sites/perfcounters/read|웹앱 성능 카운터를 가져옵니다.|
|/sites/usages/read|웹앱 사용 현황을 가져옵니다.|
|/sites/slots/Write|새 웹앱 슬롯을 만들거나 기존 웹앱 슬롯을 업데이트합니다.|
|/sites/slots/Delete|기존 웹앱 슬롯을 삭제합니다.|
|/sites/slots/backup/Action|새 웹앱 슬롯 백업을 만듭니다.|
|/sites/slots/publishxml/Action|웹앱 슬롯에 대한 게시 프로필 xml을 가져옵니다.|
|/sites/slots/publish/Action|웹앱 슬롯을 게시합니다.|
|/sites/slots/restart/Action|웹앱 슬롯을 다시 시작합니다.|
|/sites/slots/start/Action|웹앱 슬롯을 시작합니다.|
|/sites/slots/stop/Action|웹앱 슬롯을 중지합니다.|
|/sites/slots/slotsswap/Action|웹앱 배포 슬롯을 교환합니다.|
|/sites/slots/slotsdiffs/Action|웹앱 및 슬롯 간 구성 차이를 가져옵니다.|
|/sites/slots/applySlotConfig/Action|대상 슬롯의 웹앱 슬롯 구성을 현재 슬롯에 적용합니다.|
|/sites/slots/resetSlotConfig/Action|웹앱 슬롯 구성을 다시 설정합니다.|
|/sites/slots/Read|웹앱 배포 슬롯의 속성을 가져옵니다.|
|/sites/slots/newpassword/action|Newpassword 웹앱 슬롯입니다.|
|/sites/slots/sync/action|웹앱 슬롯을 동기화합니다.|
|/sites/slots/operationresults/read|웹앱 슬롯 작업 결과를 가져옵니다.|
|/sites/slots/webjobs/read|웹앱 슬롯 Webjob을 가져옵니다.|
|/sites/slots/backup/write|웹앱 슬롯 백업을 업데이트합니다.|
|/sites/slots/metricdefinitions/read|웹앱 슬롯 메트릭 정의를 가져옵니다.|
|/sites/slots/metrics/read|웹앱 슬롯 메트릭을 가져옵니다.|
|/sites/slots/continuouswebjobs/delete|웹앱 슬롯 연속 웹 작업을 삭제합니다.|
|/sites/slots/continuouswebjobs/read|웹앱 슬롯 연속 웹 작업을 가져옵니다.|
|/sites/slots/continuouswebjobs/start/action|웹앱 슬롯 연속 웹 작업을 시작합니다.|
|/sites/slots/continuouswebjobs/stop/action|웹앱 슬롯 연속 웹 작업을 중지합니다.|
|/sites/slots/premieraddons/delete|웹앱 슬롯 프리미어 추가 기능을 삭제합니다.|
|/sites/slots/premieraddons/read|웹앱 슬롯 프리미어 추가 기능을 가져옵니다.|
|/sites/slots/premieraddons/write|웹앱 슬롯 프리미어 추가 기능을 업데이트합니다.|
|/sites/slots/triggeredwebjobs/delete|웹앱 슬롯 트리거 Webjob을 삭제합니다.|
|/sites/slots/triggeredwebjobs/read|웹앱 슬롯 트리거 Webjob을 가져옵니다.|
|/sites/slots/triggeredwebjobs/run/action|웹앱 슬롯 트리거 Webjob을 실행합니다.|
|/sites/slots/hostnamebindings/delete|웹앱 슬롯 호스트 이름 바인딩을 삭제합니다.|
|/sites/slots/hostnamebindings/read|웹앱 슬롯 호스트 이름 바인딩을 가져옵니다.|
|/sites/slots/hostnamebindings/write|웹앱 슬롯 호스트 이름 바인딩을 업데이트합니다.|
|/sites/slots/phplogging/read|웹앱 슬롯 Phplogging을 가져옵니다.|
|/sites/slots/virtualnetworkconnections/delete|웹앱 슬롯 가상 네트워크 연결을 삭제합니다.|
|/sites/slots/virtualnetworkconnections/read|웹앱 슬롯 가상 네트워크 연결을 가져옵니다.|
|/sites/slots/virtualnetworkconnections/write|웹앱 슬롯 가상 네트워크 연결을 업데이트합니다.|
|/sites/slots/virtualnetworkconnections/gateways/write|웹앱 슬롯 가상 네트워크 연결 게이트웨이를 업데이트합니다.|
|/sites/slots/usages/read|웹앱 슬롯 사용 현황을 가져옵니다.|
|/sites/slots/hybridconnection/delete|웹앱 슬롯 하이브리드 연결을 삭제합니다.|
|/sites/slots/hybridconnection/read|웹앱 슬롯 하이브리드 연결을 가져옵니다.|
|/sites/slots/hybridconnection/write|웹앱 슬롯 하이브리드 연결을 업데이트합니다.|
|/sites/slots/config/Read|웹앱 슬롯의 구성 설정을 가져옵니다.|
|/sites/slots/config/list/Action|게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 슬롯 보안 중요 설정을 나열합니다.|
|/sites/slots/config/Write|웹앱 슬롯의 구성 설정을 업데이트합니다.|
|/sites/slots/config/delete|웹앱 슬롯 구성을 삭제합니다.|
|/sites/slots/instances/read|웹앱 슬롯 인스턴스를 가져옵니다.|
|/sites/slots/instances/processes/read|웹앱 슬롯 인스턴스 프로세스를 가져옵니다.|
|/sites/slots/instances/deployments/read|웹앱 슬롯 인스턴스 배포를 가져옵니다.|
|/sites/slots/sourcecontrols/Read|웹앱 슬롯의 소스 제어 구성 설정을 가져옵니다.|
|/sites/slots/sourcecontrols/Write|웹앱 슬롯의 소스 제어 구성 설정을 업데이트합니다.|
|/sites/slots/sourcecontrols/Delete|웹앱 슬롯의 소스 제어 구성 설정을 삭제합니다.|
|/sites/slots/restore/read|웹앱 슬롯 복원을 가져옵니다.|
|/sites/slots/analyzecustomhostname/read|웹앱 슬롯 사용자 지정 호스트 이름 분석을 가져옵니다.|
|/sites/slots/backups/Read|웹앱 슬롯 백업의 속성을 가져옵니다.|
|/sites/slots/backups/list/action|웹앱 슬롯 백업을 나열합니다.|
|/sites/slots/backups/restore/action|웹앱 슬롯 백업을 복원합니다.|
|/sites/slots/deployments/delete|웹앱 슬롯 배포를 삭제합니다.|
|/sites/slots/deployments/read|웹앱 슬롯 배포를 가져옵니다.|
|/sites/slots/deployments/write|웹앱 슬롯 배포를 업데이트합니다.|
|/sites/slots/deployments/log/read|웹앱 슬롯 배포 로그를 가져옵니다.|
|/sites/hybridconnection/delete|웹앱 하이브리드 연결을 삭제합니다.|
|/sites/hybridconnection/read|웹앱 하이브리드 연결을 가져옵니다.|
|/sites/hybridconnection/write|웹앱 하이브리드 연결을 업데이트합니다.|
|/sites/recommendationhistory/read|웹앱 권장 사항 기록을 가져옵니다.|
|/sites/recommendations/Read|웹앱에 대한 권장 사항 목록을 가져옵니다.|
|/sites/recommendations/disable/action|웹앱 권장 사항을 사용하지 않도록 설정합니다.|
|/sites/config/Read|웹앱 구성 설정을 가져옵니다.|
|/sites/config/list/Action|게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 보안 중요 설정을 나열합니다.|
|/sites/config/Write|웹앱 구성 설정을 업데이트합니다.|
|/sites/config/delete|웹앱 구성을 삭제합니다.|
|/sites/instances/read|웹앱 인스턴스를 가져옵니다.|
|/sites/instances/processes/delete|웹앱 인스턴스 프로세스를 삭제합니다.|
|/sites/instances/processes/read|웹앱 인스턴스 프로세스를 가져옵니다.|
|/sites/instances/deployments/read|웹앱 인스턴스 배포를 가져옵니다.|
|/sites/sourcecontrols/Read|웹앱의 소스 제어 구성 설정을 가져옵니다.|
|/sites/sourcecontrols/Write|웹앱의 소스 제어 구성 설정을 업데이트합니다.|
|/sites/sourcecontrols/Delete|웹앱의 소스 제어 구성 설정을 삭제합니다.|
|/sites/restore/read|웹앱 복원을 가져옵니다.|
|/sites/analyzecustomhostname/read|사용자 지정 호스트 이름을 분석합니다.|
|/sites/backups/Read|웹앱 백업의 속성을 가져옵니다.|
|/sites/backups/list/action|웹앱 백업을 나열합니다.|
|/sites/backups/restore/action|웹앱 백업을 복원합니다.|
|/sites/snapshots/read|웹앱 스냅숏을 가져옵니다.|
|/sites/functions/delete|웹앱 함수를 삭제합니다.|
|/sites/functions/listsecrets/action|비밀 웹앱 함수를 나열합니다.|
|/sites/functions/read|웹앱 함수를 가져옵니다.|
|/sites/functions/write|웹앱 함수를 업데이트합니다.|
|/sites/deployments/delete|웹앱 배포를 삭제합니다.|
|/sites/deployments/read|웹앱 배포를 가져옵니다.|
|/sites/deployments/write|웹앱 배포를 업데이트합니다.|
|/sites/deployments/log/read|웹앱 배포 로그를 가져옵니다.|
|/sites/diagnostics/read|웹앱 진단을 가져옵니다.|
|/sites/diagnostics/workerprocessrecycle/read|웹앱 진단 작업자 프로세스 재활용을 가져옵니다.|
|/sites/diagnostics/workeravailability/read|웹앱 진단 Workeravailability를 가져옵니다.|
|/sites/diagnostics/runtimeavailability/read|웹앱 진단 런타임 가용성을 가져옵니다.|
|/sites/diagnostics/cpuanalysis/read|웹앱 진단 Cpuanalysis를 가져옵니다.|
|/sites/diagnostics/servicehealth/read|웹앱 진단 서비스 상태를 가져옵니다.|
|/sites/diagnostics/frebanalysis/read|웹앱 진단 FREB 분석을 가져옵니다.|
|/availablestacks/read|사용 가능한 스택을 가져옵니다.|
|/isusernameavailable/read|사용자 이름을 사용할 수 있는지를 확인합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|API 목록을 가져옵니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|새 API를 추가하거나 기존 API를 업데이트합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|기존 API를 삭제합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|연결 목록을 가져옵니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|새 연결을 저장하거나 기존 연결을 업데이트합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|기존 연결을 삭제합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|ConnectionAcl을 읽습니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|ConnectionAcl을 추가하거나 업데이트합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|ConnectionAcl을 삭제합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|API에 대한 ConnectionAcl을 읽습니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|ConnectionAcl을 읽습니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|API Acl을 만들거나 업데이트합니다.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|API Acl을 삭제합니다.|
|/serverfarms/Read|App Service 계획의 속성을 가져옵니다.|
|/serverfarms/Write|새 App Service 계획을 만들거나 기존 App Service 계획을 업데이트합니다.|
|/serverfarms/Delete|기존 앱 서비스 계획 삭제|
|/serverfarms/restartSites/Action|App Service 계획의 모든 웹앱을 다시 시작합니다.|
|/serverfarms/operationresults/read|App Service 계획 작업 결과를 가져옵니다.|
|/serverfarms/capabilities/read|App Service 계획 기능을 가져옵니다.|
|/serverfarms/metricdefinitions/read|App Service 계획 메트릭 정의를 가져옵니다.|
|/serverfarms/metrics/read|App Service 계획 메트릭을 가져옵니다.|
|/serverfarms/hybridconnectionplanlimits/read|App Service 계획 하이브리드 연결 계획 제한을 가져옵니다.|
|/serverfarms/virtualnetworkconnections/read|App Service 계획 가상 네트워크 연결을 가져옵니다.|
|/serverfarms/virtualnetworkconnections/routes/delete|App Service 계획 가상 네트워크 연결 경로를 삭제합니다.|
|/serverfarms/virtualnetworkconnections/routes/read|App Service 계획 가상 네트워크 연결 경로를 가져옵니다.|
|/serverfarms/virtualnetworkconnections/routes/write|App Service 계획 가상 네트워크 연결 경로를 업데이트합니다.|
|/serverfarms/virtualnetworkconnections/gateways/write|App Service 계획 가상 네트워크 연결 게이트웨이를 업데이트합니다.|
|/serverfarms/firstpartyapps/settings/delete|App Service 계획 자사 앱 설정을 삭제합니다.|
|/serverfarms/firstpartyapps/settings/read|App Service 계획 자사 앱 설정을 가져옵니다.|
|/serverfarms/firstpartyapps/settings/write|App Service 계획 자사 앱 설정을 업데이트합니다.|
|/serverfarms/sites/read|App Service 계획 웹앱을 가져옵니다.|
|/serverfarms/workers/reboot/action|App Service 계획 작업자를 다시 부팅합니다.|
|/serverfarms/hybridconnectionrelays/read|App Service 계획 하이브리드 연결 릴레이를 가져옵니다.|
|/serverfarms/skus/read|App Service 계획 SKU를 가져옵니다.|
|/serverfarms/usages/read|App Service 계획 사용 현황을 가져옵니다.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|App Service 계획 하이브리드 연결 네임스페이스 릴레이 웹앱을 가져옵니다.|
|/ishostnameavailable/read|호스트 이름을 사용할 수 있는지를 확인합니다.|
|/connectionGateways/Read|연결 게이트웨이 목록을 가져옵니다.|
|/connectionGateways/Write|연결 게이트웨이를 만들거나 업데이트합니다.|
|/connectionGateways/Delete|연결 게이트웨이를 삭제합니다.|
|/connectionGateways/Join/Action|연결 게이트웨이를 연결합니다.|
|/classicmobileservices/read|클래식 Mobile Services를 가져옵니다.|
|/skus/read|SKU를 가져옵니다.|
|/certificates/Read|인증서 목록을 가져옵니다.|
|/certificates/Write|새 인증서를 추가하거나 기존 인증서를 업데이트합니다.|
|/certificates/Delete|기존 인증서를 삭제합니다.|
|/operations/read|작업을 가져옵니다.|
|/recommendations/Read|구독에 대한 권장 사항 목록을 가져옵니다.|
|/ishostingenvironmentnameavailable/read|호스팅 환경 이름을 사용할 수 있는지 여부를 가져옵니다.|
|/apiManagementAccounts/Read|ApiManagementAccount 목록을 가져옵니다.|
|/apiManagementAccounts/Write|새 ApiManagementAccount를 추가하거나 기존 ApiManagementAccount를 업데이트합니다.|
|/apiManagementAccounts/Delete|기존 ApiManagementAccount를 삭제합니다.|
|/apiManagementAccounts/connectionAcls/Read|연결 ACL 가져옵니다.|
|/apiManagementAccounts/apiAcls/Read|ConnectionAcl을 읽습니다.|
|/connections/Read|연결 목록을 가져옵니다.|
|/connections/Write|연결을 만들거나 업데이트합니다.|
|/connections/Delete|연결을 삭제합니다.|
|/connections/Join/Action|연결을 연결합니다.|
|/connections/confirmconsentcode/action|연결 동의 코드를 확인합니다.|
|/connections/listconsentlinks/action|연결에 대한 동의 링크를 나열합니다.|
|/deploymentlocations/read|배포 위치를 가져옵니다.|
|/sourcecontrols/read|소스 제어를 가져옵니다.|
|/sourcecontrols/write|소스 제어를 업데이트합니다.|
|/managedhostingenvironments/read|관리되는 호스팅 환경을 가져옵니다.|
|/managedhostingenvironments/sites/read|관리되는 호스팅 환경 웹앱을 가져옵니다.|
|/managedhostingenvironments/serverfarms/read|관리되는 호스팅 환경 App Service 계획을 가져옵니다.|
|/locations/managedapis/read|위치 관리되는 AAPI를 가져옵니다.|
|/locations/apioperations/read|위치 API 작업을 가져옵니다.|
|/locations/connectiongatewayinstallations/read|위치 연결 게이트웨이 설치를 가져옵니다.|
|/listSitesAssignedToHostName/Read|호스트 이름에 할당된 사이트의 이름을 가져옵니다.|

## <a name="next-steps"></a>다음 단계

- [사용자 지정 역할을 만드는](role-based-access-control-custom-roles.md) 방법을 알아봅니다.

- [기본 제공 RBAC 역할](role-based-access-built-in-roles.md)을 검토합니다.

- [사용자](role-based-access-control-manage-assignments.md) 또는 [리소스](role-based-access-control-configure.md)를 기준으로 액세스 할당을 관리하는 방법을 알아봅니다. 
