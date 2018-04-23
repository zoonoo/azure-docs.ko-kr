---
title: Azure Resource Manager 공급자 작업 | Microsoft Docs
description: Microsoft Azure Resource Manager 리소스 공급자에서 사용 가능한 작업에 대한 세부 정보
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 리소스 공급자 작업

이 문서에서는 각 Microsoft Azure Resource Manager 리소스 공급자에 대해 사용할 수 있는 작업을 설명합니다. 이러한 작업은 Azure의 리소스에 대해 보다 세분화된 RBAC(역할 기반 Access Control) 권한을 제공하기 위해 사용자 지정 역할에서 사용할 수 있습니다. 여기에 제공되는 목록이 전체 목록은 아니며 각 공급자가 업데이트될 때 작업이 추가되거나 제거될 수 있습니다. 작업 문자열은 `Microsoft.<ProviderName>/<ChildResourceType>/<action>` 형식을 따릅니다. 

> [!NOTE]
> 전체 및 현재 목록을 보려면 `Get-AzureRmProviderOperation`(PowerShell) 또는 `az provider operation list`(Azure CLI v2)를 사용하여 Azure 리소스 공급자에 대한 작업을 나열합니다.

## <a name="microsoftaad"></a>Microsoft.AAD

| 작업 | 설명 |
|---|---|
|/domainServices/delete|도메인 서비스를 삭제합니다.|
|/domainServices/read|도메인 서비스를 읽습니다.|
|/domainServices/write|도메인 서비스를 작성합니다.|
|/locations/operationresults/read|비동기 작업의 상태를 읽습니다.|
|/Operations/read|사용자에게 표시되므로 친숙하게 지역화된 작업 설명입니다.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| 작업 | 설명 |
|---|---|
|/diagnosticsettings/delete|진단 설정을 삭제합니다.|
|/diagnosticsettings/read|진단 설정을 읽습니다.|
|/diagnosticsettings/write|진단 설정을 작성합니다.|
|/diagnosticsettingscategories/read|진단 설정 범주를 읽습니다.|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|테넌트에 대해 사용 가능한 로그를 가져옵니다.|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| 작업 | 설명 |
|---|---|
|/configuration/action|테넌트 구성을 업데이트합니다.|
|/configuration/read|테넌트 구성을 읽습니다.|
|/configuration/write|테넌트 구성을 만듭니다.|
|/services/action|테넌트의 서비스 인스턴스를 업데이트합니다.|
|/services/alerts/read|서비스에 대한 경고를 읽습니다.|
|/services/alerts/read|서비스에 대한 경고를 읽습니다.|
|/services/delete|테넌트의 서비스 인스턴스를 삭제합니다.|
|/services/read|테넌트의 서비스 인스턴스를 읽습니다.|
|/services/servicemembers/action|서비스의 서비스 멤버 인스턴스를 만듭니다.|
|/services/servicemembers/alerts/read|서비스 멤버에 대한 경고를 읽습니다.|
|/services/servicemembers/delete|서비스의 서비스 멤버 인스턴스를 삭제합니다.|
|/services/servicemembers/read|서비스의 서비스 멤버 인스턴스를 읽습니다.|
|/services/write|테넌트의 서비스 인스턴스를 만듭니다.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| 작업 | 설명 |
|---|---|
|/configurations/read|구성 가져오기|
|/configurations/write|구성을 만듭니다/업데이트합니다.|
|/generateRecommendations/action|권장 사항을 생성합니다.|
|/generateRecommendations/read|권장 사항 생성 상태를 가져옵니다.|
|/operations/read|Microsoft Advisor에 대한 작업을 가져옵니다.|
|/recommendations/read|권장 사항을 읽습니다.|
|/recommendations/suppressions/delete|비표시 오류(Suppression)를 삭제합니다.|
|/recommendations/suppressions/read|비표시 오류(Suppression)를 가져옵니다.|
|/recommendations/suppressions/write|비표시 오류(Suppression)를 만들거나 업데이트합니다.|
|/register/action|Microsoft Advisor에 대한 구독을 등록합니다.|
|/suppressions/delete|비표시 오류(Suppression)를 삭제합니다.|
|/suppressions/read|비표시 오류(Suppression)를 가져옵니다.|
|/suppressions/write|비표시 오류(Suppression)를 만들거나 업데이트합니다.|
|/unregister/action|Microsoft Advisor에 대한 구독을 등록 취소합니다.|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| 작업 | 설명 |
|---|---|
|/alerts/read|입력 필터에 대한 모든 경고를 가져옵니다.|
|/alerts/resolve/action|경고 상태를 '확인'으로 변경합니다.|
|/alertsSummary/read|경고의 요약 정보를 가져옵니다.|
|/Operations/read|제공된 작업을 읽습니다.|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| 작업 | 설명 |
|---|---|
|/locations/checkNameAvailability/action|지정된 Analysis Server 이름이 올바른지와 사용되고 있지 않은지 확인합니다.|
|/locations/operationresults/read|지정된 작업 결과에 대한 정보를 검색합니다.|
|/locations/operationstatuses/read|지정된 작업 상태에 대한 정보를 검색합니다.|
|/operations/read|작업 정보를 검색합니다.|
|/register/action|Analysis Services 리소스 공급자를 등록합니다.|
|/servers/delete|Analysis Server를 삭제합니다.|
|/servers/listGatewayStatus/action|서버와 연결된 게이트웨이 상태를 나열합니다.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Analysis Server에 대한 진단 설정을 가져옵니다.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Analysis Server에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/providers/Microsoft.Insights/logDefinitions/read|서버에 사용 가능한 로그를 가져옵니다.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Analysis Server에 사용 가능한 메트릭을 가져옵니다.|
|/servers/read|지정된 Analysis Server의 정보를 검색합니다.|
|/servers/resume/action|Analysis Server를 계속합니다.|
|/servers/skus/read|서버에 사용 가능한 SKU 정보를 검색합니다.|
|/servers/suspend/action|Analysis Server를 일시 중단합니다.|
|/servers/write|지정된 Analysis Server를 만들거나 업데이트합니다.|
|/skus/read|SKU 정보를 검색합니다.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/read|제공된 서비스 이름을 사용할 수 있는지 확인합니다.|
|/operations/read|Microsoft.ApiManagement 리소스에 제공되는 모든 API 작업을 읽습니다.|
|/register/action|Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록합니다.|
|/reports/read|기간, 지역, 개발자, 제품, API, 작업, 구독 및 byRequest별로 집계된 보고서를 가져옵니다.|
|/service/apis/delete|기존 API를 제거합니다.|
|/service/apis/diagnostics/delete|기존 진단을 제거합니다.|
|/service/apis/diagnostics/loggers/delete|진단 설정을 사용하여 로거 매핑을 제거합니다.|
|/service/apis/diagnostics/loggers/read|기존 진단 로거 목록을 가져옵니다.|
|/service/apis/diagnostics/loggers/write|로거를 진단 설정에 매핑합니다.|
|/service/apis/diagnostics/read|진단 목록을 가져오거나 진단 세부 정보를 가져옵니다.|
|/service/apis/diagnostics/write|새 진단을 추가하거나 기존 진단 세부 정보를 업데이트합니다.|
|/service/apis/operations/delete|기존 API 작업을 제거합니다.|
|/service/apis/operations/policies/delete|API 작업 정책에서 정책 구성을 제거합니다.|
|/service/apis/operations/policies/read|API 작업에 대한 정책을 가져오거나 API 작업에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/apis/operations/policies/write|API 작업에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/apis/operations/policy/delete|작업에서 정책 구성을 제거합니다.|
|/service/apis/operations/policy/read|작업에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/apis/operations/policy/write|작업에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/apis/operations/read|기존 API 작업 목록을 가져오거나 API 작업의 세부 정보를 가져옵니다.|
|/service/apis/operations/tags/delete|기존 태그와 기존 작업의 연결을 삭제합니다.|
|/service/apis/operations/tags/read|작업과 연결된 태그를 가져오거나 태그 세부 정보를 가져옵니다.|
|/service/apis/operations/tags/write|기존 태그를 기존 작업과 연결합니다.|
|/service/apis/operations/write|새 API 작업을 만들거나 기존 API 작업을 업데이트합니다.|
|/service/apis/operationsByTags/read|작업/태그 연결 목록을 가져옵니다.|
|/service/apis/policies/delete|API 정책에서 정책 구성을 제거합니다.|
|/service/apis/policies/read|API에 대한 정책을 가져오거나 API에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/apis/policies/write|API에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/apis/policy/delete|API에서 정책 구성을 제거합니다.|
|/service/apis/policy/read|API에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/apis/policy/write|API에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/apis/products/read|API가 포함되어 있는 모든 제품을 가져옵니다.|
|/service/apis/read|등록된 모든 API의 목록을 가져오거나 API의 세부 정보를 가져옵니다.|
|/service/apis/releases/delete|API의 모든 릴리스를 제거하거나 API 릴리스를 제거합니다.|
|/service/apis/releases/read|API 릴리스를 가져오거나 API 릴리스 세부 정보를 가져옵니다.|
|/service/apis/releases/write|새 API 릴리스를 만들거나 기존 API 릴리스를 업데이트합니다.|
|/service/apis/revisions/delete|API의 모든 수정 버전을 제거합니다.|
|/service/apis/revisions/read|API에 속한 수정 버전을 가져옵니다.|
|/service/apis/schemas/delete|기존 스키마를 제거합니다.|
|/service/apis/schemas/document/read|스키마를 설명하는 문서를 가져옵니다.|
|/service/apis/schemas/document/write|스키마를 설명하는 문서를 업데이트합니다.|
|/service/apis/schemas/read|지정된 API에 대한 모든 스키마를 가져오거나 API에서 사용되는 스키마를 가져옵니다.|
|/service/apis/schemas/write|API에서 사용되는 스키마를 설정합니다.|
|/service/apis/tagDescriptions/delete|API에서 태그 설명을 제거합니다.|
|/service/apis/tagDescriptions/read|API 범위의 태그 설명을 가져옵니다.|
|/service/apis/tagDescriptions/write|API 범위의 태그 설명을 만듭니다/변경합니다.|
|/service/apis/tags/delete|기존 API/태그 연결을 제거합니다.|
|/service/apis/tags/read|모든 API/API의 태그 연결을 가져오거나 API/태그 연결 세부 정보를 가져옵니다.|
|/service/apis/tags/write|새 API/태그 연결 추가|
|/service/apis/write|새 API를 만들거나 기존 API의 세부 정보를 업데이트합니다.|
|/service/apisByTags/read|API/태그 연결 목록을 가져옵니다.|
|/service/api-version-sets/delete|기존 VersionSet를 제거합니다.|
|/service/api-version-sets/read|버전 그룹 엔터티 목록을 가져오거나 옵니다 VersionSet 세부 정보를 가져옵니다.|
|/service/api-version-sets/versions/read|버전 엔터티 목록을 가져옵니다.|
|/service/api-version-sets/write|새 VersionSet를 만들거나 기존 VersionSet 세부 정보를 업데이트합니다.|
|/service/applynetworkconfigurationupdates/action|Virtual Network에서 실행되는 Microsoft.ApiManagement 리소스를 업데이트하여 업데이트된 네트워크 설정을 선택합니다.|
|/service/authorizationServers/delete|기존 권한 부여 서버를 제거합니다.|
|/service/authorizationServers/read|권한 부여 서버 목록을 가져오거나 권한 부여 서버의 세부 정보를 가져옵니다.|
|/service/authorizationServers/write|새 권한 부여 서버를 만들거나 기존 권한 부여 서버의 세부 정보를 업데이트합니다.|
|/service/backends/delete|기존 백 엔드를 제거합니다.|
|/service/backends/read|백 엔드의 목록을 가져오거나 백 엔드의 세부 정보를 가져옵니다.|
|/service/backends/reconnect/action|다시 연결 요청을 만듭니다.|
|/service/backends/write|새 백 엔드를 추가하거나 기존 백 엔드 세부 정보를 업데이트합니다.|
|/service/backup/action|사용자가 제공한 저장소 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다.|
|/service/certificates/delete|기존 인증서를 제거합니다.|
|/service/certificates/read|인증서의 목록을 가져오거나 인증서의 세부 정보를 가져옵니다.|
|/service/certificates/write|새 인증서를 추가합니다.|
|/service/delete|API Management 서비스 인스턴스를 삭제합니다.|
|/service/diagnostics/delete|기존 진단을 제거합니다.|
|/service/diagnostics/loggers/delete|진단 설정을 사용하여 로거 매핑을 제거합니다.|
|/service/diagnostics/loggers/read|기존 진단 로거 목록을 가져옵니다.|
|/service/diagnostics/loggers/write|로거를 진단 설정에 매핑합니다.|
|/service/diagnostics/read|진단 목록을 가져오거나 진단 세부 정보를 가져옵니다.|
|/service/diagnostics/write|새 진단을 추가하거나 기존 진단 세부 정보를 업데이트합니다.|
|/service/getssotoken/action|API Management 서비스 레거시 포털에 관리자로 로그인하는 데 사용할 수 있는 SSO 토큰을 가져옵니다.|
|/service/groups/delete|기존 그룹을 제거합니다.|
|/service/groups/read|그룹 목록을 가져오거나 그룹의 세부 정보를 가져옵니다.|
|/service/groups/users/delete|기존 그룹에서 기존 사용자를 제거합니다.|
|/service/groups/users/read|그룹 사용자의 목록을 가져옵니다.|
|/service/groups/users/write|기존 그룹에 기존 사용자를 추가합니다.|
|/service/groups/write|새 그룹을 만들거나 기존 그룹 세부 정보를 업데이트합니다.|
|/service/identityProviders/delete|기존 ID 공급자를 제거합니다.|
|/service/identityProviders/read|ID 공급자 목록을 가져오거나 ID 공급자의 세부 정보를 가져옵니다.|
|/service/identityProviders/write|새 ID 공급자를 만들거나 기존 ID 공급자의 세부 정보를 업데이트합니다.|
|/service/locations/networkstatus/read|해당 위치에는 서비스가 종속되는 리소스의 네트워크 액세스 상태를 가져옵니다.|
|/service/loggers/delete|기존 로거를 제거합니다.|
|/service/loggers/read|로거 목록을 가져오거나 로거 세부 정보를 가져옵니다.|
|/service/loggers/write|새 로거를 추가하거나 기존 로거 세부 정보를 업데이트합니다.|
|/service/managedeployments/action|SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다.|
|/service/networkstatus/read|서비스가 종속되는 리소스의 네트워크 액세스 상태를 가져옵니다.|
|/service/notifications/action|지정된 사용자에게 알림을 보냅니다.|
|/service/notifications/read|모든 API Management 게시자 알림을 가져오거나 API Management 게시자 알림 세부 정보를 가져옵니다.|
|/service/notifications/recipientEmails/delete|알림과 연결된 기존 이메일을 제거합니다.|
|/service/notifications/recipientEmails/read|API Management 게시자 알림과 연결된 이메일 받는 사람을 가져옵니다.|
|/service/notifications/recipientEmails/write|알림의 새 이메일 받는 사람을 만듭니다.|
|/service/notifications/recipientUsers/delete|알림 받는 사람과 연결된 사용자를 제거합니다.|
|/service/notifications/recipientUsers/read|알림과 연결된 받는 사람 사용자를 가져옵니다.|
|/service/notifications/recipientUsers/write|알림 받는 사람에 사용자를 추가합니다.|
|/service/notifications/write|API Management 게시자 알림을 만들거나 업데이트합니다.|
|/service/openidConnectProviders/delete|기존 OpenID Connect 공급자를 제거합니다.|
|/service/openidConnectProviders/read|OpenID Connect 공급자 목록을 가져오거나 OpenID Connect 공급자의 세부 정보를 가져옵니다.|
|/service/openidConnectProviders/write|새 OpenID Connect 공급자를 만들거나 기존 OpenID Connect 공급자의 세부 정보를 업데이트합니다.|
|/service/operationresults/read|장기 실행 작업의 현재 상태를 가져옵니다.|
|/service/policies/delete|테넌트 정책에서 정책 구성을 제거합니다.|
|/service/policies/read|테넌트에 대한 정책을 가져오거나 테넌트에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/policies/write|테넌트에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/policySnippets/read|모든 정책 코드 조각을 가져옵니다.|
|/service/portalsettings/read|포털의 등록 설정을 가져오거나 포털의 로그인 설정을 가져오거나 포털의 위임 설정을 가져옵니다.|
|/service/portalsettings/write|등록 설정을 업데이트하거나 로그인 설정을 업데이트하거나 위임 설정을 업데이트합니다.|
|/service/products/apis/delete|기존 제품에서 기존 API를 제거합니다.|
|/service/products/apis/read|기존 제품에 추가되는 API 목록을 가져옵니다.|
|/service/products/apis/write|기존 제품에 기존 API를 추가합니다.|
|/service/products/delete|기존 제품을 제거합니다.|
|/service/products/groups/delete|기존 제품과의 기존 개발자 그룹 연결을 제거합니다.|
|/service/products/groups/read|제품과 연결된 개발자 그룹 목록을 가져옵니다.|
|/service/products/groups/write|기존 개발자 그룹을 제품 기존에 연결합니다.|
|/service/products/policies/delete|제품 정책에서 정책 구성을 제거합니다.|
|/service/products/policies/read|제품에 대한 정책을 가져오거나 제품에 대한 정책 구성 세부 정보를 가져옵니다.|
|/service/products/policies/write|제품에 대한 정책 구성 세부 정보를 설정합니다.|
|/service/products/policy/delete|기존 제품에서 정책 구성을 제거합니다.|
|/service/products/policy/read|기존 제품의 정책 구성을 가져옵니다.|
|/service/products/policy/write|기존 제품의 정책 구성을 설정합니다.|
|/service/products/read|제품 목록을 가져오거나 제품의 세부 정보를 가져옵니다.|
|/service/products/subscriptions/read|제품 구독 목록을 가져옵니다.|
|/service/products/tags/delete|기존 태그와 기존 제품의 연결을 삭제합니다.|
|/service/products/tags/read|제품과 연결된 태그를 가져오거나 태그 세부 정보를 가져옵니다.|
|/service/products/tags/write|기존 태그를 기존 제품과 연결합니다.|
|/service/products/write|새 제품을 만들거나 기존 제품 세부 정보를 업데이트합니다.|
|/service/properties/delete|기존 속성을 제거합니다.|
|/service/properties/read|모든 속성의 목록을 가져오거나 지정된 속성의 세부 정보를 가져옵니다.|
|/service/properties/write|새 속성을 만들거나 지정된 속성 값을 업데이트합니다.|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|API Management 서비스의 진단 설정을 가져옵니다.|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|API Management 서비스의 진단 설정을 만들거나 업데이트합니다.|
|/service/providers/Microsoft.Insights/logDefinitions/read|API Management 서비스에 사용 가능한 로그를 가져옵니다.|
|/service/providers/Microsoft.Insights/metricDefinitions/read|API Management 서비스에 사용 가능한 메트릭을 가져옵니다.|
|/service/quotas/periods/read|기간에 대한 할당량 카운터 값을 가져옵니다.|
|/service/quotas/periods/write|할당량 카운터 현재 값을 설정합니다.|
|/service/quotas/read|할당량에 대한 값을 가져옵니다.|
|/service/quotas/write|할당량 카운터 현재 값을 설정합니다.|
|/service/read|API Management 서비스 인스턴스에 대한 메타데이터 읽기|
|/service/reports/read|기간별로 집계된 보고서를 가져오거나 지역별로 집계된 보고서를 가져오거나 개발자별로 집계된 보고서를 가져옵니다. 또는 제품별로 집계된 보고서를 가져옵니다. 또는 API별로 집계된 보고서를 가져오거나 작업별로 집계된 보고서를 가져오거나 구독별로 집계된 보고서를 가져옵니다. 또는 데이터 보고 요청을 가져옵니다.|
|/service/restore/action|사용자가 제공한 저장소 계정의 지정된 컨테이너에서 API Management 서비스 복원|
|/service/subscriptions/delete|구독을 삭제합니다. 이 작업은 구독을 삭제하는 데 사용할 수 있습니다.|
|/service/subscriptions/read|제품 구독 목록을 가져오거나 제품 구독 세부 정보를 가져옵니다.|
|/service/subscriptions/regeneratePrimaryKey/action|구독 기본 키를 다시 생성합니다.|
|/service/subscriptions/regenerateSecondaryKey/action|구독 보조 키를 다시 생성합니다.|
|/service/subscriptions/write|기존 사용자를 기존 제품에 구독하거나 기존 구독 세부 정보를 업데이트합니다. 이 작업은 구독을 갱신하는 데 사용할 수 있습니다.|
|/service/tagResources/read|리소스와 연결된 태그 목록을 가져옵니다.|
|/service/tags/delete|기존 태그를 제거합니다.|
|/service/tags/read|태그 목록을 가져오거나 태그 세부 정보를 가져옵니다.|
|/service/tags/write|새 태그를 추가하거나 기존 태그 세부 정보를 업데이트합니다.|
|/service/templates/delete|기본 API Management 이메일 템플릿을 다시 설정합니다.|
|/service/templates/read|모든 이메일 템플릿을 가져오거나 API Management 이메일 템플릿 세부 정보를 가져옵니다.|
|/service/templates/write|API Management 이메일 템플릿을 만들거나/업데이트하거나 API Management 이메일 템플릿을 업데이트합니다.|
|/service/tenant/delete|테넌트에 대한 정책 구성을 제거합니다.|
|/service/tenant/deploy/action|배포 작업을 실행하여 지정된 git 분기에서 데이터베이스의 구성에 대해 변경된 내용을 적용합니다.|
|/service/tenant/operationResults/read|작업 결과 목록을 가져오거나 특정 작업의 결과를 가져옵니다.|
|/service/tenant/read|테넌트에 대한 정책 구성을 가져오거나 테넌트 액세스 세부 정보를 가져옵니다.|
|/service/tenant/regeneratePrimaryKey/action|기본 액세스 키를 다시 생성합니다.|
|/service/tenant/regenerateSecondaryKey/action|보조 액세스 키를 다시 생성합니다.|
|/service/tenant/save/action|리포지토리의 지정된 분기에 대한 구성 스냅숏을 사용하여 커밋을 만듭니다.|
|/service/tenant/syncState/read|마지막 git 동기화의 상태를 가져옵니다.|
|/service/tenant/validate/action|지정된 git 분기에서 변경된 내용이 유효한지 검사합니다.|
|/service/tenant/write|테넌트에 대한 정책 구성을 설정하거나 테넌트 액세스 세부 정보를 업데이트합니다.|
|/service/updatecertificate/action|API Management 서비스에 대한 SSL 인증서를 업로드합니다.|
|/service/updatehostname/action|API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다.|
|/service/users/action|새 사용자를 등록합니다.|
|/service/users/applications/attachments/delete|첨부 파일을 제거합니다.|
|/service/users/applications/attachments/read|응용 프로그램 첨부 파일을 가져오거나 첨부 파일을 가져옵니다.|
|/service/users/applications/attachments/write|응용 프로그램에 첨부 파일을 추가합니다.|
|/service/users/applications/delete|기존 응용 프로그램을 제거합니다.|
|/service/users/applications/read|모든 사용자 응용 프로그램 목록을 가져오거나 API Management 응용 프로그램 세부 정보를 가져옵니다.|
|/service/users/applications/write|API Management에 응용 프로그램을 등록하거나 응용 프로그램 세부 정보를 업데이트합니다.|
|/service/users/delete|사용자 계정을 제거합니다.|
|/service/users/generateSsoUrl/action|SSO URL을 생성합니다. 이 URL은 관리 포털에 액세스하는 데 사용할 수 있습니다.|
|/service/users/groups/read|사용자 그룹의 목록을 가져옵니다.|
|/service/users/keys/read|사용자 키의 목록을 가져옵니다.|
|/service/users/read|등록된 사용자 목록을 가져오거나 사용자의 계정 세부 정보를 가져옵니다.|
|/service/users/subscriptions/read|사용자 구독 목록을 가져옵니다.|
|/service/users/token/action|사용자에 대한 토큰 액세스 토큰을 가져옵니다.|
|/service/users/write|새 사용자를 등록하거나 기존 사용자의 계정 세부 정보를 업데이트합니다.|
|/service/write|API Management 서비스의 새 인스턴스 만들기|
|/unregister/action|Microsoft.ApiManagement 리소스 공급자에 대한 구독을 등록 취소합니다.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| 작업 | 설명 |
|---|---|
|/checkAccess/action|호출자에게 특정 작업을 수행할 권한이 있는지 확인합니다.|
|/classicAdministrators/delete|구독에서 관리자를 제거합니다.|
|/classicAdministrators/read|구독에 대한 관리자를 읽습니다.|
|/classicAdministrators/write|구독에 대한 관리자를 추가 또는 수정합니다.|
|/elevateAccess/action|테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한을 부여합니다.|
|/locks/delete|지정된 범위에서 잠금을 삭제합니다.|
|/locks/read|지정된 범위에서 잠금을 가져옵니다.|
|/locks/write|지정된 범위에서 잠금을 추가합니다.|
|/permissions/read|호출자가 지정된 범위에서 갖는 모든 사용 권한을 나열합니다.|
|/policyAssignments/delete|지정된 범위에서 정책 할당을 삭제합니다.|
|/policyAssignments/read|정책 할당에 대한 정보를 가져옵니다.|
|/policyAssignments/write|지정된 범위에서 정책 할당을 만듭니다.|
|/policyDefinitions/delete|정책 정의를 삭제합니다.|
|/policyDefinitions/read|정책 정의에 대한 정보를 가져옵니다.|
|/policyDefinitions/write|사용자 지정 정책 정의를 만듭니다.|
|/policySetDefinitions/delete|정책 집합 정의를 삭제합니다.|
|/policySetDefinitions/read|정책 집합 정의에 대한 정보를 가져옵니다.|
|/policySetDefinitions/write|사용자 지정 정책 집합 정의를 만듭니다.|
|/providerOperations/read|역할 정의에 사용할 수 있는 모든 리소스 공급자에 대한 작업을 가져옵니다.|
|/roleAssignments/delete|지정된 범위에서 역할 할당을 삭제합니다.|
|/roleAssignments/read|역할 할당에 대한 정보를 가져옵니다.|
|/roleAssignments/write|지정된 범위에서 역할 할당을 만듭니다.|
|/roleDefinitions/delete|지정된 사용자 지정 역할 정의를 삭제합니다.|
|/roleDefinitions/read|역할 정의에 대한 정보를 가져옵니다.|
|/roleDefinitions/write|지정된 사용 권한 및 할당 가능 범위를 사용하여 사용자 지정 역할 정의를 만들거나 업데이트합니다.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| 작업 | 설명 |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Azure Automation DSC의 등록 정보를 읽습니다.|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Azure Automation DSC 키를 다시 생성하는 요청을 작성합니다.|
|/automationAccounts/certificates/delete|Azure Automation 인증서 자산을 삭제합니다.|
|/automationAccounts/certificates/read|Azure Automation 인증서 자산을 가져옵니다.|
|/automationAccounts/certificates/write|Azure Automation 인증서 자산을 만들거나 업데이트합니다.|
|/automationAccounts/compilationjobs/read|Azure Automation DSC의 컴파일을 읽습니다.|
|/automationAccounts/compilationjobs/write|Azure Automation DSC의 컴파일을 작성합니다.|
|/automationAccounts/configurations/delete|Azure Automation DSC의 콘텐츠를 삭제합니다.|
|/automationAccounts/configurations/getCount/action|Azure Automation DSC의 콘텐츠 수를 읽습니다.|
|/automationAccounts/configurations/read|Azure Automation DSC의 콘텐츠를 가져옵니다.|
|/automationAccounts/configurations/write|Azure Automation DSC의 콘텐츠를 작성합니다.|
|/automationAccounts/connections/delete|Azure Automation 연결 자산을 삭제합니다.|
|/automationAccounts/connections/read|Azure Automation 연결 자산을 가져옵니다.|
|/automationAccounts/connections/write|Azure Automation 연결 자산을 만들거나 업데이트합니다.|
|/automationAccounts/connectionTypes/delete|Azure Automation 연결 형식 자산을 삭제합니다.|
|/automationAccounts/connectionTypes/read|Azure Automation 연결 형식 자산을 가져옵니다.|
|/automationAccounts/connectionTypes/write|Azure Automation 연결 형식 자산을 만듭니다.|
|/automationAccounts/credentials/delete|Azure Automation 자격 증명 자산을 삭제합니다.|
|/automationAccounts/credentials/read|Azure Automation 자격 증명 자산을 가져옵니다.|
|/automationAccounts/credentials/write|Azure Automation 자격 증명 자산을 만들거나 업데이트합니다.|
|/automationAccounts/delete|Azure Automation 계정을 삭제합니다.|
|/automationAccounts/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/automationAccounts/diagnosticSettings/write|리소스에 대한 진단 설정을 설정합니다.|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Hybrid Runbook Worker 리소스를 삭제합니다.|
|/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker 리소스를 읽습니다.|
|/automationAccounts/jobs/output/action|작업의 출력을 가져옵니다.|
|/automationAccounts/jobs/output/action|작업의 출력을 가져옵니다.|
|/automationAccounts/jobs/read|Azure Automation 작업을 가져옵니다.|
|/automationAccounts/jobs/read|Azure Automation 작업을 가져옵니다.|
|/automationAccounts/jobs/resume/action|Azure Automation 작업을 계속합니다.|
|/automationAccounts/jobs/resume/action|Azure Automation 작업을 계속합니다.|
|/automationAccounts/jobs/runbookContent/action|작업 실행 시 Azure Automation runbook의 콘텐츠를 가져옵니다.|
|/automationAccounts/jobs/runbookContent/action|작업 실행 시 Azure Automation runbook의 콘텐츠를 가져옵니다.|
|/automationAccounts/jobs/stop/action|Azure Automation 작업을 중지합니다.|
|/automationAccounts/jobs/stop/action|Azure Automation 작업을 중지합니다.|
|/automationAccounts/jobs/streams/read|Azure Automation 작업 스트림을 가져옵니다.|
|/automationAccounts/jobs/streams/read|Azure Automation 작업 스트림을 가져옵니다.|
|/automationAccounts/jobs/suspend/action|Azure Automation 작업을 일시 중단합니다.|
|/automationAccounts/jobs/suspend/action|Azure Automation 작업을 일시 중단합니다.|
|/automationAccounts/jobs/write|Azure Automation 작업을 만듭니다.|
|/automationAccounts/jobs/write|Azure Automation 작업을 만듭니다.|
|/automationAccounts/jobSchedules/delete|Azure Automation 작업 일정을 삭제합니다.|
|/automationAccounts/jobSchedules/read|Azure Automation 작업 일정을 가져옵니다.|
|/automationAccounts/jobSchedules/write|Azure Automation 작업 일정을 만듭니다.|
|/automationAccounts/linkedWorkspace/read|자동화 계정에 연결된 작업 영역 가져오기|
|/automationAccounts/logDefinitions/read|자동화 계정에 사용할 수 있는 로그를 가져옵니다.|
|/automationAccounts/modules/activities/read|Azure Automation 작업을 가져옵니다.|
|/automationAccounts/modules/delete|Azure Automation 모듈을 삭제합니다.|
|/automationAccounts/modules/read|Azure Automation 모듈을 가져옵니다.|
|/automationAccounts/modules/write|Azure Automation 모듈을 만들거나 업데이트합니다.|
|/automationAccounts/nodeConfigurations/delete|Azure Automation DSC의 노드 구성을 삭제합니다.|
|/automationAccounts/nodeConfigurations/read|Azure Automation DSC의 노드 구성을 읽습니다.|
|/automationAccounts/nodeConfigurations/readContent/action|Azure Automation DSC의 노드 구성 콘텐츠를 읽습니다.|
|/automationAccounts/nodeConfigurations/write|Azure Automation DSC의 노드 구성을 작성합니다.|
|/automationAccounts/nodes/delete|Azure Automation DSC 노드를 삭제합니다.|
|/automationAccounts/nodes/read|Azure Automation DSC 노드를 읽습니다.|
|/automationAccounts/nodes/reports/read|Azure Automation DSC 보고서 콘텐츠를 읽습니다.|
|/automationAccounts/nodes/reports/read|Azure Automation DSC 보고서를 읽습니다.|
|/automationAccounts/objectDataTypes/fields/read|Azure Automation TypeFields를 가져옵니다.|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Automation 메트릭 정의를 가져옵니다.|
|/automationAccounts/read|Azure Automation 계정을 가져옵니다.|
|/automationAccounts/runbooks/delete|Azure Automation Runbook을 삭제합니다.|
|/automationAccounts/runbooks/draft/publish/action|Azure Automation Runbook 초안을 게시합니다.|
|/automationAccounts/runbooks/draft/read|Azure Automation Runbook 초안을 가져옵니다.|
|/automationAccounts/runbooks/draft/readContent/action|Azure Automation Runbook 초안의 콘텐츠를 가져옵니다.|
|/automationAccounts/runbooks/draft/testJob/read|Azure Automation Runbook 초안 테스트 작업을 가져옵니다.|
|/automationAccounts/runbooks/draft/testJob/resume/action|Azure Automation Runbook 초안 테스트 작업을 계속합니다.|
|/automationAccounts/runbooks/draft/testJob/stop/action|Azure Automation Runbook 초안 테스트 작업을 중지합니다.|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Azure Automation Runbook 초안 테스트 작업을 일시 중단합니다.|
|/automationAccounts/runbooks/draft/testJob/write|Azure Automation Runbook 초안 테스트 작업을 만듭니다.|
|/automationAccounts/runbooks/draft/undoEdit/action|Azure Automation Runbook 초안에 대한 편집을 실행 취소합니다.|
|/automationAccounts/runbooks/draft/writeContent/action|Azure Automation Runbook 초안의 콘텐츠를 만듭니다.|
|/automationAccounts/runbooks/read|Azure Automation Runbook을 가져옵니다.|
|/automationAccounts/runbooks/readContent/action|Azure Automation runbook의 콘텐츠를 가져옵니다.|
|/automationAccounts/runbooks/write|Azure Automation Runbook을 만들거나 업데이트합니다.|
|/automationAccounts/schedules/delete|Azure Automation 일정 자산을 삭제합니다.|
|/automationAccounts/schedules/read|Azure Automation 일정 자산을 가져옵니다.|
|/automationAccounts/schedules/write|Azure Automation 일정 자산을 만들거나 업데이트합니다.|
|/automationAccounts/statistics/read|Azure Automation 통계를 가져옵니다.|
|/automationAccounts/usages/read|Azure Automation 사용량을 가져옵니다.|
|/automationAccounts/variables/delete|Azure Automation 변수 자산을 삭제합니다.|
|/automationAccounts/variables/read|Azure Automation 변수 자산을 읽습니다.|
|/automationAccounts/variables/write|Azure Automation 변수 자산을 만들거나 업데이트합니다.|
|/automationAccounts/watchers/streams/read|Azure Automation Watcher 작업 스트림을 가져옵니다.|
|/automationAccounts/webhooks/delete|Azure Automation 웹후크를 삭제합니다. |
|/automationAccounts/webhooks/generateUri/action|Azure Automation 웹후크에 대한 URI를 생성합니다.|
|/automationAccounts/webhooks/read|Azure Automation 웹후크를 읽습니다.|
|/automationAccounts/webhooks/write|Azure Automation 웹후크를 만들거나 업데이트합니다.|
|/automationAccounts/write|Azure Automation 계정을 만들거나 업데이트합니다.|
|/automationAccounts/write|Azure Automation 계정을 만들거나 업데이트합니다.|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| 작업 | 설명 |
|---|---|
|/b2cDirectories/delete|B2C 디렉터리 리소스를 삭제합니다.|
|/b2cDirectories/read|B2C 디렉터리 리소스를 봅니다.|
|/b2cDirectories/write|B2C 디렉터리 리소스를 만들거나 업데이트합니다.|
|/operations/read|Microsoft.AzureActiveDirectory 리소스 공급자에 사용할 수 있는 모든 API 작업을 읽습니다.|
|/register/action|Microsoft.AzureActiveDirectory 리소스 공급자 구독을 등록합니다.|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| 작업 | 설명 |
|---|---|
|/Operations/read|리소스 공급자 작업의 속성을 가져옵니다.|
|/register/action|Microsoft.AzureStack 리소스 공급자에 구독을 등록합니다.|
|/registrations/customerSubscriptions/delete|Azure Stack 고객 구독을 삭제합니다.|
|/registrations/customerSubscriptions/read|Azure Stack 고객 구독의 속성을 가져옵니다.|
|/registrations/customerSubscriptions/write|Azure Stack 고객 구독을 만들거나 업데이트합니다.|
|/registrations/delete|Azure Stack 등록을 삭제합니다.|
|/registrations/getActivationKey/action|최신 Azure Stack 활성화 키를 가져옵니다.|
|/registrations/products/listDetails/action|Azure Stack Marketplace 제품에 대한 세부 정보 검색|
|/registrations/products/read|Azure Stack Marketplace 제품의 속성 가져오기|
|/registrations/read|Azure Stack 등록의 속성 가져오기|
|/registrations/write|Azure Stack 등록을 만들거나 업데이트합니다.|

## <a name="microsoftbatch"></a>Microsoft.Batch

| 작업 | 설명 |
|---|---|
|/batchAccounts/applications/delete|응용 프로그램을 삭제합니다.|
|/batchAccounts/applications/read|응용 프로그램을 나열하거나 응용 프로그램의 속성을 가져옵니다.|
|/batchAccounts/applications/versions/activate/action|응용 프로그램 패키지를 활성화합니다.|
|/batchAccounts/applications/versions/delete|응용 프로그램 패키지를 삭제합니다.|
|/batchAccounts/applications/versions/read|응용 프로그램 패키지의 속성을 가져옵니다.|
|/batchAccounts/applications/versions/write|새 응용 프로그램 패키지를 만들거나 기존 응용 프로그램 패키지를 업데이트합니다.|
|/batchAccounts/applications/write|새 응용 프로그램을 만들거나 기존 응용 프로그램을 업데이트합니다.|
|/batchAccounts/certificateOperationResults/read|배치 계정에 대한 장기 실행 인증서 작업의 결과를 가져옵니다.|
|/batchAccounts/certificates/cancelDelete/action|배치 계정에서 실패한 인증서 삭제를 취소합니다.|
|/batchAccounts/certificates/delete|배치 계정에서 인증서를 삭제합니다.|
|/batchAccounts/certificates/read|배치 계정의 인증서를 나열하거나 인증서 속성을 가져옵니다.|
|/batchAccounts/certificates/write|배치 계정에 새 인증서를 만들거나 기존 인증서를 업데이트합니다.|
|/batchAccounts/delete|Batch 계정을 삭제합니다.|
|/batchAccounts/listkeys/action|Batch 계정에 대한 액세스 키를 나열합니다.|
|/batchAccounts/operationResults/read|장기 실행 배치 계정 작업의 결과를 가져옵니다.|
|/batchAccounts/poolOperationResults/read|배치 계정에 대한 장기 실행 풀 작업의 결과를 가져옵니다.|
|/batchAccounts/pools/delete|배치 계정에서 풀을 삭제합니다.|
|/batchAccounts/pools/disableAutoscale/action|배치 계정 풀에 대한 자동 크기 조정을 해제합니다.|
|/batchAccounts/pools/read|배치 계정의 풀을 나열하거나 풀 속성을 가져옵니다.|
|/batchAccounts/pools/stopResize/action|배치 계정 풀에서 진행 중인 크기 조정 작업을 중지합니다.|
|/batchAccounts/pools/upgradeOs/action|배치 계정 풀의 운영 체제를 업그레이드합니다.|
|/batchAccounts/pools/write|배치 계정에 새 풀을 만들거나 기존 풀을 업데이트합니다.|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Batch 서비스에 사용 가능한 로그를 가져옵니다.|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Batch 서비스에 사용 가능한 메트릭을 가져옵니다.|
|/batchAccounts/read|Batch 계정을 나열하거나 Batch 계정의 속성을 가져옵니다.|
|/batchAccounts/regeneratekeys/action|Batch 계정에 대한 액세스 키를 다시 생성합니다.|
|/batchAccounts/syncAutoStorageKeys/action|Batch 계정에 대해 구성된 자동 저장소 계정의 액세스 키를 동기화합니다.|
|/batchAccounts/write|새 Batch 계정을 만들거나 기존 Batch 계정을 업데이트합니다.|
|/locations/checkNameAvailability/action|계정 이름이 올바르고 사용되고 있지 않은지 확인합니다.|
|/locations/quotas/read|지정된 Azure 지역에서 지정된 구독의 Batch 할당량을 가져옵니다.|
|/register/action|Batch 리소스 공급자에 대한 구독을 등록하고 Batch 계정을 만들도록 설정합니다.|
|/unregister/action|Batch 리소스 공급자에 대한 구독을 등록 취소하여 Batch 계정 만들기를 차단합니다.|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| 작업 | 설명 |
|---|---|
|/clusters/delete|Batch AI 클러스터를 만듭니다.|
|/clusters/read|Batch AI 클러스터를 나열하거나 Batch AI 클러스터의 속성을 가져옵니다.|
|/clusters/remoteLoginInformation/action|Batch AI 클러스터에 대한 원격 로그인 정보를 나열합니다.|
|/clusters/write|새 Batch AI 클러스터를 만들거나 기존 Batch AI 클러스터를 업데이트합니다.|
|/fileservers/delete|Batch AI 파일 서버를 삭제합니다.|
|/fileservers/read|Batch AI 파일 서버를 나열하거나 Batch AI 파일 서버의 속성을 가져옵니다.|
|/fileservers/resume/action|Batch AI 파일 서버를 다시 시작합니다.|
|/fileservers/suspend/action|Batch AI 파일 서버를 일시 중단합니다.|
|/fileservers/write|새 Batch AI 파일 서버를 만들거나 기존 Batch AI 파일 서버를 업데이트합니다.|
|/jobs/delete|Batch AI 작업을 삭제합니다.|
|/jobs/read|Batch AI 작업을 나열하거나 Batch AI 작업의 속성을 가져옵니다.|
|/jobs/remoteLoginInformation/action|Batch AI 작업에 대한 원격 로그인 정보를 나열합니다.|
|/jobs/terminate/action|Batch AI 작업을 종료합니다.|
|/jobs/write|새 Batch AI 작업을 만들거나 기존 Batch AI 작업을 업데이트합니다.|
|/register/action|Batch AI 리소스 공급자에 대한 구독을 등록하고 Batch AI 리소스 만들기를 사용하도록 설정합니다.|

## <a name="microsoftbilling"></a>Microsoft.Billing

| 작업 | 설명 |
|---|---|
|/billingPeriods/read|사용할 수 있는 청구 기간을 나열합니다.|
|/invoices/read|사용 가능한 청구서를 나열합니다.|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| 작업 | 설명 |
|---|---|
|/mapApis/Delete|삭제 작업|
|/mapApis/listSecrets/action|암호를 나열합니다.|
|/mapApis/listSingleSignOnToken/action|리소스에 대한 Single Sign-On 권한 부여 토큰을 읽습니다.|
|/mapApis/Read|읽기 작업|
|/mapApis/regenerateKey/action|키 다시 생성|
|/mapApis/Write|쓰기 작업|
|/Operations/read|작업에 대한 설명입니다.|

## <a name="microsoftcache"></a>Microsoft.Cache

| 작업 | 설명 |
|---|---|
|/checknameavailability/action|이름을 새 Redis Cache에서 사용할 수 있는지 확인합니다.|
|/operations/read|'Microsoft.Cache' 공급자가 지원하는 작업을 나열합니다.|
|/redis/delete|전체 Redis Cache를 삭제합니다.|
|/redis/export/action|Redis 데이터를 지정된 형식의 접두사 지정 저장소 blob으로 내보냅니다.|
|/redis/firewallRules/delete|Redis Cache의 IP 방화벽 규칙을 삭제합니다.|
|/redis/firewallRules/read|Redis Cache의 IP 방화벽 규칙을 가져옵니다.|
|/redis/firewallRules/write|Redis Cache의 IP 방화벽 규칙을 편집합니다.|
|/redis/forceReboot/action|캐시 인스턴스를 강제로 다시 부팅합니다. 데이터가 손실될 수 있습니다.|
|redis/import/action|여러 blob에서 지정된 형식의 데이터를 Redis로 가져옵니다.|
|/redis/linkedservers/delete|Redis Cache에서 연결된 서버를 삭제합니다.|
|/redis/linkedservers/read|Redis Cache와 관련된 연결된 서버를 가져옵니다.|
|/redis/linkedservers/write|Redis Cache에 연결된 서버를 추가합니다.|
|/redis/listKeys/action|관리 포털에서 Redis Cache 액세스 키의 값을 봅니다.|
|/redis/listUpgradeNotifications/read|캐시 테넌트에 대한 최신 업그레이드 알림을 나열합니다.|
|/redis/locations/operationresults/read|이전에 'Location' 헤더가 클라이언트에 반환된 장기 실행 작업의 결과를 가져옵니다|
|/redis/metricDefinitions/read|Redis Cache에 대해 사용 가능한 메트릭을 가져옵니다.|
|/redis/patchSchedules/delete|Redis Cache의 패치 일정을 삭제합니다.|
|redis/patchSchedules/read|Redis Cache의 패치 일정을 가져옵니다.|
|/redis/patchSchedules/write|Redis Cache의 패치 일정을 수정합니다.|
|/redis/read|관리 콘솔에서 Redis Cache의 설정 및 구성을 봅니다.|
|/redis/regenerateKey/action|관리 포털에서 Redis Cache 액세스 키의 값을 변경합니다.|
|/redis/start/action|캐시 인스턴스를 시작합니다.|
|/redis/stop/action|캐시 인스턴스를 중지합니다.|
|/redis/write|관리 콘솔에서 Redis Cache의 설정 및 구성을 수정합니다.|
|/register/action|‘Microsoft.Cache’ 리소스 공급자를 구독에 등록합니다.|
|/unregister/action|‘Microsoft.Cache’ 리소스 공급자를 구독에서 등록 취소합니다.|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| 작업 | 설명 |
|---|---|
|/register/action|용량 리소스 공급자를 등록하고 용량 리소스 만들기를 사용하도록 설정합니다.|
|/reservationorders/action|예약을 업데이트합니다.|
|/reservationorders/delete|예약을 삭제합니다.|
|/reservationorders/read|모든 예약을 읽습니다.|
|/reservationorders/reservations/action|예약을 업데이트합니다.|
|/reservationorders/reservations/delete|예약을 삭제합니다.|
|/reservationorders/reservations/read|모든 예약을 읽습니다.|
|/reservationorders/reservations/revisions/read|모든 예약을 읽습니다.|
|/reservationorders/reservations/write|예약을 만듭니다.|
|/reservationorders/write|예약을 만듭니다.|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| 작업 | 설명 |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Microsoft.Cdn에 사용 가능한 로그를 가져옵니다.|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|CDN 리소스 공급자에 대한 구독을 등록하고 CDN 프로필 생성을 활성화합니다.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| 작업 | 설명 |
|---|---|
|/certificateOrders/certificates/Delete|기존 인증서를 삭제합니다.|
|/certificateOrders/certificates/Read|인증서 목록을 가져옵니다.|
|/certificateOrders/certificates/Write|새 인증서를 추가하거나 기존 인증서를 업데이트합니다.|
|/certificateOrders/Delete|기존 AppServiceCertificate를 삭제합니다.|
|/certificateOrders/operations/Read|앱 서비스 인증서 등록의 모든 작업을 나열합니다.|
|/certificateOrders/Read|CertificateOrders의 목록을 가져옵니다.|
|/certificateOrders/reissue/Action|기존 certificateorder를 재발급합니다.|
|/certificateOrders/renew/Action|기존 certificateorder를 갱신합니다.|
|/certificateOrders/resendEmail/Action|인증서 전자 메일을 다시 보냅니다.|
|/certificateOrders/resendRequestEmails/Action|요청 전자 메일을 다른 전자 메일 주소로 다시 보냅니다.|
|/certificateOrders/resendRequestEmails/Action|발급된 App Service Certificate에 대한 사이트 봉인을 검색합니다.|
|/certificateOrders/retrieveCertificateActions/Action|인증서 작업의 목록을 검색합니다.|
|/certificateOrders/retrieveEmailHistory/Action|인증서 전자 메일 기록을 검색합니다.|
|/certificateOrders/verifyDomainOwnership/Action|도메인 소유권 확인|
|/certificateOrders/Write|새 certificateOrder를 추가하거나 기존 certificateOrder를 업데이트합니다.|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|서비스 앱 사용자에 대한 서비스 주체를 프로비전합니다.|
|/register/action|구독에 대한 Microsoft 인증서 리소스 공급자를 등록합니다.|
|/validateCertificateRegistrationInformation/Action|인증서 구매 개체를 제출하지 않고 유효성을 검사합니다.|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| 작업 | 설명 |
|---|---|
|/capabilities/read|기능을 표시합니다.|
|/checkDomainNameAvailability/action|지정된 도메인 이름의 가용성을 확인합니다.|
|/domainNames/active/write|활성 도메인 이름을 설정합니다.|
|/domainNames/availabilitySets/read|리소스에 대한 가용성 집합을 표시합니다.|
|/domainNames/capabilities/read|도메인 이름 기능을 표시합니다.|
|/domainNames/delete|리소스에 대한 도메인 이름을 제거합니다.|
|/domainNames/extensions/delete|도메인 이름 확장을 제거합니다.|
|/domainNames/extensions/operationStatuses/read|도메인 이름 확장에 대한 작업 상태를 읽습니다.|
|/domainNames/extensions/read|도메인 이름 확장을 반환합니다.|
|/domainNames/extensions/write|도메인 이름 확장을 추가합니다.|
|/domainNames/internalLoadBalancers/delete|새 내부 부하 분산 장치를 제거합니다.|
|/domainNames/internalLoadBalancers/operationStatuses/read|도메인 이름 내부 부하 분산 장치에 대한 작업 상태를 읽습니다.|
|/domainNames/internalLoadBalancers/read|내부 부하 분산 장치를 가져옵니다.|
|/domainNames/internalLoadBalancers/write|새 내부 부하 분산 장치를 가져옵니다.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|도메인 이름 부하 분산 끝점 집합에 대한 작업 상태를 읽습니다.|
|/domainNames/loadBalancedEndpointSets/read|부하 분산된 끝점 집합을 표시합니다.|
|/domainNames/read|리소스에 대한 도메인 이름을 반환합니다.|
|/domainNames/serviceCertificates/delete|사용된 서비스 인증서를 삭제합니다.|
|/domainNames/serviceCertificates/operationStatuses/read|도메인 이름 서비스 인증서에 대한 작업 상태를 읽습니다.|
|/domainNames/serviceCertificates/read|사용된 서비스 인증서를 반환합니다.|
|/domainNames/serviceCertificates/write|사용된 서비스 인증서를 추가 또는 수정합니다.|
|/domainNames/slots/delete|지정된 배포 슬롯을 삭제합니다.|
|/domainNames/slots/operationStatuses/read|도메인 이름 슬롯에 대한 작업 상태를 읽습니다.|
|/domainNames/slots/read|배포 슬롯을 표시합니다.|
|/domainNames/slots/roles/extensionReferences/delete|배포 슬롯 역할에 대한 확장 참조를 제거합니다.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|도메인 이름 슬롯 확장 참조에 대한 작업 상태를 읽습니다.|
|/domainNames/slots/roles/extensionReferences/read|배포 슬롯 역할에 대한 확장 참조를 반환합니다.|
|/domainNames/slots/roles/extensionReferences/write|배포 슬롯 역할에 대한 확장 참조를 추가하거나 수정합니다.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|진단 설정을 가져옵니다.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|진단 설정을 추가하거나 수정합니다.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|메트릭 정의를 가져옵니다.|
|/domainNames/slots/roles/read|배포 슬롯에 대한 역할을 가져옵니다.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|도메인 이름 슬롯 역할 인스턴스에 대한 작업 상태를 읽습니다.|
|/domainNames/slots/roles/roleInstances/read|역할 인스턴스를 가져옵니다.|
|/domainNames/slots/roles/roleInstances/rebuild/action|역할 인스턴스를 다시 빌드합니다.|
|/domainNames/slots/roles/roleInstances/reimage/action|역할 인스턴스를 이미지로 다시 설치합니다.|
|/domainNames/slots/roles/roleInstances/restart/action|역할 인스턴스를 다시 시작합니다.|
|/domainNames/slots/start/action|배포 슬롯을 시작합니다.|
|/domainNames/slots/state/start/write|배포 슬롯 상태를 중지됨으로 변경합니다.|
|/domainNames/slots/state/stop/write|배포 슬롯 상태를 시작됨으로 변경합니다.|
|/domainNames/slots/stop/action|배포 슬롯을 일시 중단합니다.|
|/domainNames/slots/upgradeDomain/write|도메인 업그레이드를 탐색합니다.|
|/domainNames/slots/write|배포를 만들거나 업데이트합니다.|
|/domainNames/swap/action|스테이징 슬롯을 프로덕션 슬롯으로 바꿉니다.|
|/domainNames/write|리소스에 대한 도메인 이름을 추가 또는 수정합니다.|
|/moveSubscriptionResources/action|모든 클래식 리소스를 다른 구독으로 이동합니다.|
|/operatingSystemFamilies/read|Microsoft Azure에서 사용 가능한 게스트 운영 체제 제품군을 나열하고 각 제품군에서 사용 가능한 운영 체제 버전도 나열합니다.|
|/operatingSystems/read|Microsoft Azure에서 현재 사용할 수 있는 게스트 운영 체제의 버전을 나열합니다.|
|/quotas/read|구독에 대한 할당량을 가져옵니다.|
|/register/action|클래식 Compute에 등록|
|/resourceTypes/skus/read|지원되는 리소스 형식에 대한 SKU 목록을 가져옵니다.|
|/validateSubscriptionMoveAvailability/action|클래식 이동 작업에 대한 구독의 가용성이 유효한지 확인합니다.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|가상 머신과 연결된 네트워크 보안 그룹을 삭제합니다.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|네트워크 보안 그룹에 연결된 가상 머신에 대한 작업 상태를 읽습니다.|
|/virtualMachines/associatedNetworkSecurityGroups/read|가상 컴퓨터와 연결된 네트워크 보안 그룹을 가져옵니다.|
|/virtualMachines/associatedNetworkSecurityGroups/write|가상 머신과 연결된 네트워크 보안 그룹을 추가합니다.|
|/virtualMachines/asyncOperations/read|가능한 비동기 작업을 가져옵니다.|
|/virtualMachines/attachDisk/action|데이터 디스크를 가상 머신에 연결합니다.|
|/virtualMachines/delete|가상 머신을 제거합니다.|
|/virtualMachines/detachDisk/action|데이터 디스크를 가상 머신에서 분리합니다.|
|/virtualMachines/disks/read|데이터 디스크의 목록을 검색합니다.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|가상 머신에 대한 RDP 파일을 다운로드합니다.|
|/virtualMachines/extensions/operationStatuses/read|가상 머신 확장의 작업 상태를 읽습니다.|
|/virtualMachines/extensions/read|가상 머신 확장을 가져옵니다.|
|/virtualMachines/extensions/write|가상 머신 확장을 배치합니다.|
|/virtualMachines/metrics/read|메트릭을 가져옵니다.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|네트워크 인터페이스에 연결된 네트워크 보안 그룹을 삭제합니다.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|네트워크 보안 그룹에 연결된 가상 머신에 대한 작업 상태를 읽습니다.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|네트워크 인터페이스에 연결된 네트워크 보안 그룹을 가져옵니다.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|네트워크 인터페이스에 연결된 네트워크 보안 그룹을 추가합니다.|
|/virtualMachines/operationStatuses/read|가상 머신의 작업 상태를 읽습니다.|
|/virtualMachines/performMaintenance/action|가상 머신에서 유지 관리를 수행합니다.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|진단 설정을 가져옵니다.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|진단 설정을 추가하거나 수정합니다.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|메트릭 정의를 가져옵니다.|
|/virtualMachines/read|가상 머신 목록을 검색합니다.|
|/virtualMachines/redeploy/action|가상 머신을 다시 배포합니다.|
|/virtualMachines/restart/action|가상 머신을 다시 시작합니다.|
|/virtualMachines/shutdown/action|가상 머신을 종료합니다.|
|/virtualMachines/start/action|가상 머신을 시작합니다.|
|/virtualMachines/stop/action|가상 머신을 중지합니다.|
|/virtualMachines/write|가상 머신을 추가 또는 수정합니다.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| 작업 | 설명 |
|---|---|
|/gatewaySupportedDevices/read|지원되는 장치 목록을 검색합니다.|
|/networkSecurityGroups/delete|네트워크 보안 그룹을 삭제합니다.|
|/networkSecurityGroups/operationStatuses/read|네트워크 보안 그룹의 작업 상태를 읽습니다.|
|/networkSecurityGroups/read|네트워크 보안 그룹을 가져옵니다.|
|/networkSecurityGroups/securityRules/delete|보안 규칙을 삭제합니다.|
|/networkSecurityGroups/securityRules/operationStatuses/read|네트워크 보안 그룹 보안 규칙의 작업 상태를 읽습니다.|
|/networkSecurityGroups/securityRules/read|보안 규칙을 가져옵니다.|
|/networkSecurityGroups/securityRules/write|보안 규칙을 추가하거나 업데이트합니다.|
|/networkSecurityGroups/write|새 네트워크 보안 그룹을 추가합니다.|
|/quotas/read|구독에 대한 할당량을 가져옵니다.|
|/register/action|클래식 네트워크에 등록합니다.|
|/reservedIps/delete|예약된 IP를 삭제합니다.|
|reservedIps/join/action|예약된 IP를 조인합니다.|
|/reservedIps/link/action|예약된 IP를 연결합니다.|
|/reservedIps/operationStatuses/read|예약된 IP의 작업 상태를 읽습니다.|
|/reservedIps/read|예약된 IP를 가져옵니다.|
|/reservedIps/write|예약된 IP를 새로 추가합니다.|
|/virtualNetworks/capabilities/read|기능을 표시합니다.|
|/virtualNetworks/checkIPAddressAvailability/action|가상 네트워크에서 지정된 IP 주소의 가용성을 확인합니다.|
|/virtualNetworks/delete|가상 네트워크를 삭제합니다.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|클라이언트 인증서의 해지를 취소합니다.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|해지된 클라이언트 인증서를 읽습니다.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|클라이언트 인증서를 해지합니다.|
|/virtualNetworks/gateways/clientRootCertificates/delete|가상 네트워크 게이트웨이 클라이언트 인증서를 삭제합니다.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|지문으로 인증서를 다운로드합니다.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|가상 네트워크 게이트웨이 인증서 패키지를 나열합니다.|
|/virtualNetworks/gateways/clientRootCertificates/read|클라이언트 루트 인증서를 찾습니다.|
|/virtualNetworks/gateways/clientRootCertificates/write|새 클라이언트 루트 인증서를 업로드합니다.|
|/virtualNetworks/gateways/connections/connect/action|사이트 간 게이트웨이 연결을 연결합니다.|
|/virtualNetworks/gateways/connections/disconnect/action|사이트 간 게이트웨이 연결을 끊습니다.|
|/virtualNetworks/gateways/connections/read|연결 목록을 검색합니다.|
|/virtualNetworks/gateways/connections/test/action|사이트 간 게이트웨이 연결을 테스트합니다.|
|/virtualNetworks/gateways/delete|가상 네트워크 게이트웨이를 삭제합니다.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|장치 구성 스크립트를 다운로드합니다.|
|/virtualNetworks/gateways/downloadDiagnostics/action|게이트웨이 진단을 다운로드합니다.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|회로 서비스 키를 검색합니다.|
|/virtualNetworks/gateways/listPackage/action|가상 네트워크 게이트웨이 패키지를 나열합니다.|
|/virtualNetworks/gateways/operationStatuses/read|가상 네트워크 게이트웨이의 작업 상태를 읽습니다.|
|/virtualNetworks/gateways/packages/read|가상 네트워크 게이트웨이 패키지를 가져옵니다.|
|/virtualNetworks/gateways/read|가상 네트워크 게이트웨이를 가져옵니다.|
|/virtualNetworks/gateways/startDiagnostics/action|가상 네트워크 게이트웨이에 대한 진단을 시작합니다.|
|/virtualNetworks/gateways/stopDiagnostics/action|가상 네트워크 게이트웨이에 대한 진단을 중지합니다.|
|/virtualNetworks/gateways/write|가상 네트워크 게이트웨이를 추가합니다.|
|/virtualNetworks/join/action|가상 네트워크를 조인합니다.|
|/virtualNetworks/operationStatuses/read|가상 네트워크의 작업 상태를 읽습니다.|
|/virtualNetworks/peer/action|가상 네트워크를 다른 가상 네트워크와 피어링합니다.|
|/virtualNetworks/read|가상 네트워크를 가져옵니다.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|서브넷과 연결된 네트워크 보안 그룹을 삭제합니다.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|네트워크 보안 그룹과 연결된 가상 네트워크 서브넷에 대한 작업 상태를 읽습니다.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|서브넷과 연결된 네트워크 보안 그룹을 가져옵니다.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|서브넷과 연결된 네트워크 보안 그룹을 추가합니다.|
|/virtualNetworks/write|새 가상 네트워크를 추가합니다.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| 작업 | 설명 |
|---|---|
|/capabilities/read|기능을 표시합니다.|
|/checkStorageAccountAvailability/action|저장소 계정의 가용성을 확인합니다.|
|/disks/read|저장소 계정 디스크를 반환합니다.|
|/images/read|이미지를 반환합니다.|
|/osImages/read|운영 체제 이미지를 반환합니다.|
|/publicImages/read|공용 가상 컴퓨터 이미지를 가져옵니다.|
|/quotas/read|구독에 대한 할당량을 가져옵니다.|
|/register/action|클래식 저장소에 등록합니다.|
|/storageAccounts/delete|저장소 계정을 삭제합니다.|
|/storageAccounts/disks/delete|지정된 저장소 계정 디스크를 삭제합니다.|
|/storageAccounts/disks/operationStatuses/read|리소스의 작업 상태를 읽습니다.|
|/storageAccounts/disks/read|저장소 계정 디스크를 반환합니다.|
|/storageAccounts/disks/write|저장소 계정 디스크를 추가합니다.|
|/storageAccounts/images/delete|지정된 장소 계정 이미지를 삭제합니다.|
|/storageAccounts/images/read|저장소 계정 이미지를 반환합니다.|
|/storageAccounts/listKeys/action|저장소 계정의 액세스 키를 나열합니다.|
|/storageAccounts/operationStatuses/read|리소스의 작업 상태를 읽습니다.|
|/storageAccounts/osImages/delete|지정된 저장소 계정 운영 체제 이미지를 삭제합니다.|
|/storageAccounts/osImages/read|저장소 계정 운영 체제 이미지를 반환합니다.|
|/storageAccounts/read|지정된 계정의 저장소 계정을 반환합니다.|
|/storageAccounts/regenerateKey/action|저장소 계정에 대한 기존 액세스 키를 다시 생성합니다.|
|/storageAccounts/services/diagnosticSettings/read|진단 설정을 가져옵니다.|
|/storageAccounts/services/diagnosticSettings/write|진단 설정을 추가하거나 수정합니다.|
|/storageAccounts/services/metricDefinitions/read|메트릭 정의를 가져옵니다.|
|/storageAccounts/services/metrics/read|메트릭을 가져옵니다.|
|/storageAccounts/services/read|사용 가능한 서비스를 가져옵니다.|
|/storageAccounts/write|새 저장소 계정을 추가합니다.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 작업 | 설명 |
|---|---|
|/accounts/delete|API 계정을 삭제합니다.|
|/accounts/listKeys/action|키를 나열합니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Cognitive Services에 대해 사용 가능한 메트릭을 가져옵니다.|
|/accounts/read|API 계정을 읽습니다.|
|/accounts/regenerateKey/action|키를 다시 생성합니다.|
|/accounts/skus/read|기존 리소스에 대한 사용 가능한 SKU를 읽습니다.|
|/accounts/usages/read|기존 리소스에 대한 할당량 사용량을 가져옵니다.|
|/accounts/write|API 계정을 씁니다.|
|/locations/checkSkuAvailability/action|구독에 대해 사용 가능한 SKU를 읽습니다.|
|/Operations/read|사용 가능한 모든 작업을 나열합니다.|
|/register/action|Cognitive Services에 대한 구독을 등록합니다.|
|/skus/read|Cognitive Services에 사용 가능한 SKU를 읽습니다.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| 작업 | 설명 |
|---|---|
|/RateCard/read|지정된 구독에 대한 제공 데이터, 리소스/미터 메타데이터 및 요율을 제공합니다.|
|/UsageAggregates/read|구독별 Microsoft Azure 소비를 검색합니다. 결과에는 특정 시간 범위의 집계 사용 현황 데이터, 구독 및 리소스 관련 정보가 포함됩니다.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| 작업 | 설명 |
|---|---|
|/availabilitySets/delete|가용성 집합을 삭제합니다.|
|/availabilitySets/read|가용성 집합의 속성을 가져옵니다.|
|/availabilitySets/vmSizes/read|가용성 집합에서 가상 머신을 만들거나 업데이트하기 위한 사용 가능 크기를 나열합니다.|
|/availabilitySets/write|새 가용성 집합을 만들거나 기존 가용성 집합을 업데이트합니다.|
|/disks/beginGetAccess/action|Blob 액세스에 대한 디스크의 SAS URI를 가져옵니다.|
|/disks/delete|디스크를 삭제합니다.|
|/disks/endGetAccess/action|디스크의 SAS URI를 취소합니다.|
|/disks/read|디스크의 속성을 가져옵니다.|
|/disks/write|새 디스크를 만들거나 기존 디스크를 업데이트합니다.|
|/images/delete|이미지를 삭제합니다.|
|/images/read|이미지의 속성을 가져옵니다.|
|/images/write|새 이미지를 만들거나 기존 이미지를 업데이트합니다.|
|/locations/capsOperations/read|비동기 Caps 작업의 상태를 가져옵니다.|
|/locations/diskOperations/read|비동기 디스크 작업의 상태를 가져옵니다.|
|/locations/operations/read|비동기 작업의 상태를 가져옵니다.|
|/locations/publishers/artifacttypes/offers/read|플랫폼 이미지 제품의 속성을 가져옵니다.|
|/locations/publishers/artifacttypes/offers/skus/read|플랫폼 이미지 Sku의 속성을 가져옵니다.|
|/locations/publishers/artifacttypes/offers/skus/versions/read|플랫폼 이미지 버전의 속성을 가져옵니다.|
|/locations/publishers/artifacttypes/types/read|VMExtension 형식의 속성을 가져옵니다.|
|/locations/publishers/artifacttypes/types/versions/read|VMExtension 버전의 속성을 가져옵니다.|
|/locations/publishers/read|게시자의 속성을 가져옵니다.|
|/locations/runCommands/read|위치에 사용할 수 있는 실행 명령을 나열합니다.|
|/locations/usages/read|한 위치에 있는 구독의 Compute 리소스에 대한 서비스 제한 및 현재 사용 수량을 가져옵니다.|
|/locations/vmSizes/read|한 위치에서 사용 가능한 가상 머신 크기를 나열합니다.|
|/operations/read|Microsoft.Compute 리소스 공급자에서 사용 가능한 작업을 나열합니다.|
|/register/action|Microsoft.Compute 리소스 공급자에 구독을 등록합니다.|
|/restorePointCollections/delete|복원 지점 컬렉션 및 포함된 복원 지점을 삭제합니다.|
|/restorePointCollections/read|복원 지점 컬렉션의 속성을 가져옵니다.|
|/restorePointCollections/restorePoints/delete|복원 지점을 삭제합니다.|
|/restorePointCollections/restorePoints/read|복원 지점의 속성을 가져옵니다.|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Blob SAS URI와 함께 복원 지점의 속성을 가져옵니다.|
|/restorePointCollections/restorePoints/write|새 복원 지점을 만듭니다.|
|/restorePointCollections/write|새 복원 지점 컬렉션을 만들거나 기존 복원 지점 컬렉션을 업데이트합니다.|
|/sharedVMImages/delete|SharedVMImage를 삭제합니다.|
|/sharedVMImages/read|SharedVMImage의 속성을 가져옵니다.|
|/sharedVMImages/versions/delete|SharedVMImageVersion을 삭제합니다.|
|/sharedVMImages/versions/read|SharedVMImageVersion의 속성을 가져옵니다.|
|/sharedVMImages/versions/replicate/action|SharedVMImageVersion을 대상 지역에 복제합니다.|
|/sharedVMImages/versions/write|새 SharedVMImageVersion을 만들거나 기존 SharedVMImageVersion을 업데이트합니다.|
|/sharedVMImages/write|새 SharedVMImage를 만들거나 기존 SharedVMImage를 업데이트합니다.|
|/skus/read|구독에 사용할 수 있는 Microsoft.Compute SKU 목록을 가져옵니다.|
|/snapshots/beginGetAccess/action|Blob 액세스에 대한 스냅숏의 SAS URI를 가져옵니다.|
|/snapshots/delete|스냅숏을 삭제합니다.|
|/snapshots/endGetAccess/action|스냅숏의 SAS URI를 취소합니다.|
|/snapshots/read|스냅숏의 속성을 가져옵니다.|
|/snapshots/write|새 스냅숏을 만들거나 기존 스냅숏을 업데이트합니다.|
|/virtualMachines/capture/action|가상 하드 디스크를 복사하여 가상 머신을 캡처하고 유사한 가상 머신을 만드는 데 사용할 수 있는 템플릿을 생성합니다.|
|/virtualMachines/convertToManagedDisks/action|가상 머신의 blob 기반 디스크를 관리되는 디스크로 변환합니다.|
|/virtualMachines/deallocate/action|가상 머신을 끄고 Compute 리소스를 해제합니다.|
|/virtualMachines/delete|가상 머신을 삭제합니다.|
|/virtualMachines/extensions/delete|가상 머신 확장을 삭제합니다.|
|/virtualMachines/extensions/read|가상 머신 확장의 속성을 가져옵니다.|
|/virtualMachines/extensions/write|새 가상 머신 확장을 만들거나 기존 가상 머신 확장을 업데이트합니다.|
|/virtualMachines/generalize/action|가상 컴퓨터 상태를 일반화됨으로 설정하고 가상 컴퓨터 캡처를 준비합니다.|
|/virtualMachines/instanceView/read|가상 머신 및 해당 리소스의 자세한 런타임 상태를 가져옵니다.|
|/virtualMachines/performMaintenance/action|VM에서 유지 관리 작업을 수행합니다.|
|/virtualMachines/powerOff/action|가상 머신을 끕니다. 가상 컴퓨터에는 요금이 계속 청구됩니다.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|가상 머신 메트릭 정의를 읽습니다.|
|/virtualMachines/read|가상 머신의 속성을 가져옵니다.|
|/virtualMachines/redeploy/action|가상 머신을 다싯 배포합니다.|
|/virtualMachines/reimage/action|차이점 보관용 디스크를 사용하는 가상 머신을 이미지로 다시 설치합니다.|
|/virtualMachines/restart/action|가상 머신을 다시 시작합니다.|
|/virtualMachines/runCommand/action|가상 머신에서 미리 정의된 스크립트를 실행합니다.|
|/virtualMachines/start/action|가상 머신을 시작합니다.|
|/virtualMachines/vmSizes/read|가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다.|
|/virtualMachines/write|새 가상 머신을 만들거나 기존 가상 머신을 업데이트합니다.|
|/virtualMachineScaleSets/deallocate/action|가상 머신 확장 집합의 인스턴스에 대한 Compute 리소스를 끄고 해제합니다. |
|/virtualMachineScaleSets/delete|가상 머신 확장 집합을 삭제합니다.|
|/virtualMachineScaleSets/delete/action|가상 머신 확장 집합의 인스턴스를 삭제합니다.|
|/virtualMachineScaleSets/extensions/delete|가상 머신 확장 집합 확장을 삭제합니다.|
|/virtualMachineScaleSets/extensions/read|가상 머신 확장 집합 확장의 속성을 가져옵니다.|
|/virtualMachineScaleSets/extensions/write|새 가상 머신 확장 집합 확장을 만들거나 기존 가상 머신 확장 집합 확장을 업데이트합니다.|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|서비스 패브릭 가상 머신 확장 집합의 플랫폼 업데이트 도메인을 수동으로 탐색하여 중단된 보류 중인 업데이트를 마칩니다.|
|/virtualMachineScaleSets/instanceView/read|가상 머신 확장 집합의 인스턴스 보기를 가져옵니다.|
|/virtualMachineScaleSets/manualUpgrade/action|수동으로 인스턴스를 최신 모델의 가상 머신 확장 집합으로 업데이트합니다.|
|/virtualMachineScaleSets/networkInterfaces/read|가상 머신 확장 집합의 모든 네트워크 인터페이스 속성을 가져옵니다.|
|/virtualMachineScaleSets/osUpgradeHistory/read|가상 머신 확장 집합에 대한 OS 업그레이드의 기록을 가져옵니다.|
|/virtualMachineScaleSets/performMaintenance/action|가상 머신 확장 집합의 인스턴스에서 계획된 유지 관리를 수행합니다.|
|/virtualMachineScaleSets/powerOff/action|가상 머신 확장 집합의 인스턴스를 끕니다.|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|가상 머신 크기 집합 메트릭 정의를 읽습니다.|
|/virtualMachineScaleSets/publicIPAddresses/read|가상 머신 확장 집합의 모든 공용 IP 주소 속성을 가져옵니다.|
|/virtualMachineScaleSets/read|가상 머신 확장 집합의 속성을 가져옵니다.|
|/virtualMachineScaleSets/redeploy/action|가상 머신 확장 집합의 인스턴스를 다시 배포합니다.|
|/virtualMachineScaleSets/reimage/action|가상 머신 확장 집합의 인스턴스를 이미지로 다시 설치합니다.|
|/virtualMachineScaleSets/restart/action|가상 머신 확장 집합의 인스턴스를 다시 시작합니다.|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|가상 머신 확장 집합의 롤링 업그레이드를 취소합니다.|
|/virtualMachineScaleSets/rollingUpgrades/read|가상 머신 확장 집합의 최신 롤링 업그레이드 상태를 가져옵니다.|
|/virtualMachineScaleSets/scale/action|기존 가상 머신 확장 집합이 지정된 인스턴스 개수로 규모 감축/ 규모 확장할 수 있는지 확인합니다.|
|/virtualMachineScaleSets/skus/read|기존 가상 머신 확장 집합에 대한 유효한 SKU를 나열합니다.|
|/virtualMachineScaleSets/start/action|가상 머신 확장 집합의 인스턴스를 시작합니다.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|VM 확장 집합의 Virtual Machine에 대한 Compute 리소스를 끄고 해제합니다.|
|/virtualMachineScaleSets/virtualMachines/delete|VM 확장 집합에서 특정 Virtual Machine을 삭제합니다.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|VM 크기 집합에 있는 Virtual Machine의 인스턴스 보기를 검색합니다.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|가상 머신 확장 집합을 사용하여 만든 공용 IP 주소의 속성을 가져옵니다. 가상 머신 확장 집합은 ipconfiguration(개인 IP)당 공용 IP를 하나만 만들 수 있습니다.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|가상 머신 확장 집합을 사용하여 만든 네트워크 인터페이스의 한 IP 구성 또는 모든 IP 구성의 속성을 가져옵니다. IP 구성은 개인 IP를 나타냅니다.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|가상 머신 확장 집합을 사용하여 만든 가상 머신의 한 네트워크 인터페이스 또는 모든 네트워크 인터페이스의 속성을 가져옵니다.|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|가상 머신 확장 집합의 가상 머신 인스턴스에서 계획된 유지 관리를 수행합니다.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|VM 확장 집합에서 Virtual Machine 인스턴스를 끕니다.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|확장 집합 메트릭 정의의 가상 머신을 읽습니다.|
|/virtualMachineScaleSets/virtualMachines/read|VM 확장 집합에서 Virtual Machine의 속성을 가져옵니다.|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|가상 머신 확장 집합의 가상 머신 인스턴스를 이미지로 다시 배포합니다.|
|/virtualMachineScaleSets/virtualMachines/reimage/action|가상 머신 확장 집합의 가상 머신 인스턴스를 이미지로 다시 설치합니다.|
|/virtualMachineScaleSets/virtualMachines/restart/action|VM 확장 집합에서 Virtual Machine 인스턴스를 다시 시작합니다.|
|/virtualMachineScaleSets/virtualMachines/start/action|VM 확장 집합에서 Virtual Machine 인스턴스를 시작합니다.|
|/virtualMachineScaleSets/virtualMachines/write|VM 확장 집합에 있는 가상 머신의 속성을 업데이트합니다.|
|/virtualMachineScaleSets/write|새 가상 머신 확장 집합을 만들거나 기존 가상 머신 확장 집합을 업데이트합니다.|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| 작업 | 설명 |
|---|---|
|/balances/read|관리 그룹의 청구 기간에 대한 사용률 요약 정보를 나열합니다.|
|/budgets/read|구독 또는 관리 그룹별로 예산을 나열합니다.|
|/budgets/write|구독 또는 관리 그룹별로 예산을 만들고, 업데이트하고, 삭제합니다.|
|/marketplaces/read|EA 및 WebDirect 구독의 범위에 대한 마켓플레이스 리소스 사용 정보를 나열합니다.|
|/operations/read|Microsoft.Consumption 리소스 공급자가 지원하는 모든 작업을 나열합니다.|
|/pricesheets/read|구독 또는 관리 그룹의 가격표 데이터를 나열합니다.|
|/reservationDetails/read|예약된 인스턴스의 사용률 세부 정보를 예약 순서 또는 관리 그룹별로 나열합니다. 세부 정보 데이터는 일별 인스턴스 수준입니다.|
|/reservationSummaries/read|예약된 인스턴스의 사용률 요약 정보를 예약 순서 또는 관리 그룹별로 나열합니다. 요약 데이터는 월별 또는 일별 수준입니다.|
|/reservationTransactions/read|관리 그룹에서 예약한 인스턴스의 트랜잭션 기록을 나열합니다.|
|/terms/read|구독 또는 관리 그룹의 사용 약관을 나열합니다.|
|/usageDetails/read|EA 및 WebDirect 구독의 범위에 대한 사용량 세부 정보를 나열합니다.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| 작업 | 설명 |
|---|---|
|/containerGroups/containers/logs/read|특정 컨테이너에 대한 로그를 가져옵니다.|
|/containerGroups/delete|특정 컨테이너 그룹을 삭제합니다.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|컨테이너 그룹에 대한 진단 설정을 가져옵니다.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|컨테이너 그룹에 대한 진단 설정을 만들거나 업데이트합니다.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|컨테이너 그룹에 사용 가능한 메트릭을 가져옵니다.|
|/containerGroups/read|모든 컨테이너 그룹을 가져옵니다.|
|/containerGroups/write|특정 컨테이너 그룹을 만들거나 업데이트합니다.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/read|컨테이너 레지스트리 이름을 사용할 수 있는지 확인합니다.|
|/locations/operationResults/read|비동기 작업 결과를 가져옵니다.|
|/operations/read|사용 가능한 모든 Azure Container Registry REST API 작업을 나열합니다.|
|/register/action|컨테이너 레지스트리 리소스 공급자에 대한 구독을 등록하고 컨테이너 레지스트리를 만들도록 설정합니다.|
|/registries/delete|컨테이너 레지스트리를 삭제합니다.|
|/registries/eventGridFilters/delete|컨테이너 레지스트리에서 Event Grid 필터를 삭제합니다.|
|/registries/eventGridFilters/read|지정된 Event Grid 필터의 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 Event Grid 필터를 나열합니다.|
|/registries/eventGridFilters/write|지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 Event Grid 필터를 만들거나 업데이트합니다.|
|/registries/listCredentials/action|지정된 컨테이너 레지스트리에 대한 로그인 자격 증명을 나열합니다.|
|/registries/listUsages/read|지정된 컨테이너 레지스트리에 대한 할당량 사용량을 나열합니다.|
|/registries/operationStatuses/read|레지스트리 비동기 작업 상태를 가져옵니다.|
|/registries/read|지정된 컨테이너 레지스트리의 속성을 가져오거나 지정된 리소스 그룹 또는 구독에 속한 모든 컨테이너 레지스트리를 나열합니다.|
|/registries/regenerateCredential/action|지정된 컨테이너 레지스트리에 대한 로그인 자격 증명 중 하나를 다시 생성합니다.|
|/registries/replications/delete|컨테이너 레지스트리에서 복제를 삭제합니다.|
|/registries/replications/operationStatuses/read|복제 비동기 작업 상태를 가져옵니다.|
|/registries/replications/read|지정된 복제 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 복제를 나열합니다.|
|/registries/replications/write|지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 복제를 만들거나 업데이트합니다.|
|/registries/webhooks/delete|컨테이너 레지스트리에서 웹후크를 삭제합니다.|
|/registries/webhooks/getCallbackConfig/action|웹후크에 대한 서비스 URI 및 사용자 지정 헤더의 구성을 가져옵니다.|
|/registries/webhooks/listEvents/action|지정된 웹후크에 대한 최신 이벤트를 나열합니다.|
|/registries/webhooks/operationStatuses/read|웹후크 비동기 작업 상태를 가져옵니다.|
|/registries/webhooks/ping/action|웹후크로 보낼 ping 이벤트를 트리거합니다.|
|/registries/webhooks/read|지정된 웹후크 속성을 가져오거나 지정된 컨테이너 레지스트리의 모든 웹후크를 나열합니다.|
|/registries/webhooks/write|지정된 매개 변수를 사용하여 컨테이너 레지스트리에 대한 웹후크를 만들거나 업데이트합니다.|
|/registries/write|지정된 매개 변수를 사용하여 컨테이너 레지스트리를 만들거나 업데이트합니다.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| 작업 | 설명 |
|---|---|
|/containerServices/delete|지정된 컨테이너 서비스를 삭제합니다.|
|/containerServices/read|지정된 컨테이너 서비스를 가져옵니다.|
|/containerServices/write|지정된 컨테이너 서비스를 배치하거나 업데이트합니다.|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| 작업 | 설명 |
|---|---|
|/applications/delete|삭제 작업|
|/applications/listSecrets/action|암호를 나열합니다.|
|/applications/listSingleSignOnToken/action|Single Sign-on 토큰을 읽습니다.|
|/applications/read|읽기 작업|
|/applications/write|쓰기 작업|
|/applications/write|쓰기 작업|
|/listCommunicationPreference/action|통신 기본 설정을 나열합니다.|
|/operations/read|읽기 작업|
|/updateCommunicationPreference/action|통신 기본 설정을 업데이트합니다.|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| 작업 | 설명 |
|---|---|
|/hubs/adobemetadata/action|Azure Customer Insights Adobe 메타데이터를 만들거나 업데이트합니다.|
|/hubs/adobemetadata/read|Azure Customer Insights Adobe 메타데이터를 읽습니다.|
|/hubs/authorizationPolicies/delete|Azure Customer Insights 공유 액세스 서명 정책을 만들거나 삭제합니다.|
|/hubs/authorizationPolicies/read|Azure Customer Insights 공유 액세스 서명 정책을 읽습니다.|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Azure Customer Insights 공유 액세스 서명 정책 기본 키를 다시 생성합니다.|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Azure Customer Insights 공유 액세스 서명 정책 보조 키를 다시 생성합니다.|
|/hubs/authorizationPolicies/write|Azure Customer Insights 공유 액세스 서명 정책을 만들거나 업데이트합니다.|
|/hubs/connectors/activate/action|Azure Customer Insights 커넥터를 활성화합니다.|
|/hubs/connectors/activate/action|Azure Customer Insights 커넥터를 활성화합니다.|
|/hubs/connectors/delete|Azure Customer Insights 커넥터를 삭제합니다.|
|/hubs/connectors/getruntimestatus/action|Azure Customer Insights 커넥터 런타임 상태를 가져옵니다.|
|/hubs/connectors/mappings/activate/action|Azure Customer Insights 커넥터 매핑을 활성화합니다.|
|/hubs/connectors/mappings/delete|Azure Customer Insights 커넥터 매핑을 삭제합니다.|
|/hubs/connectors/mappings/operations/read|Azure Customer Insights 커넥터 매핑 작업 결과를 읽습니다.|
|/hubs/connectors/mappings/read|Azure Customer Insights 커넥터 매핑을 읽습니다.|
|/hubs/connectors/mappings/write|Azure Customer Insights 커넥터 매핑을 만들거나 업데이트합니다.|
|/hubs/connectors/operations/read|Azure Customer Insights 커넥터 작업 결과를 읽습니다.|
|/hubs/connectors/read|Azure Customer Insights 커넥터를 읽습니다.|
|/hubs/connectors/saveauthinfo/action|Azure Customer Insights 커넥터 인증 정보를 만들거나 업데이트합니다.|
|/hubs/connectors/update/action|Azure Customer Insights 커넥터를 업데이트합니다.|
|/hubs/connectors/write|Azure Customer Insights 커넥터를 만들거나 업데이트합니다.|
|/hubs/crmmetadata/action|Azure Customer Insights Crm 메타데이터를 만들거나 업데이트합니다.|
|/hubs/crmmetadata/read|Azure Customer Insights Crm 메타데이터를 읽습니다.|
|/hubs/delete|Azure Customer Insights Hub를 삭제합니다.|
|/hubs/gdpr/delete|Azure Customer Insights Gdpr을 삭제합니다.|
|/hubs/gdpr/read|Azure Customer Insights Gdpr을 읽습니다.|
|/hubs/gdpr/write|Azure Customer Insights Gdpr을 만들거나 업데이트합니다.|
|/hubs/getbillingcredits/read|Azure Customer Insights 허브 청구 크레딧을 가져옵니다.|
|/hubs/getbillinghistory/read|Azure Customer Insights 허브 청구 내역을 가져옵니다.|
|/hubs/images/delete|Azure Customer Insights 이미지를 삭제합니다.|
|/hubs/images/read|Azure Customer Insights 이미지를 읽습니다.|
|/hubs/images/write|Azure Customer Insights 이미지를 만들거나 업데이트합니다.|
|/hubs/interactions/delete|Azure Customer Insights 상호 작용을 삭제합니다.|
|/hubs/interactions/operations/read|Azure Customer Insights 상호 작용 작업 결과를 읽습니다.|
|/hubs/interactions/read|Azure Customer Insights 상호 작용을 읽습니다.|
|/hubs/interactions/suggestrelationshiplinks/action|Azure Customer Insights 상호 작용에 대한 관계 링크를 추천합니다.|
|/hubs/interactions/write|Azure Customer Insights 상호 작용을 만들거나 업데이트합니다.|
|/hubs/kpi/delete|모든 Azure Customer Insights 핵심 성과 지표를 삭제합니다.|
|/hubs/kpi/operations/read|Azure Customer Insights 핵심 성과 지표 작업 결과를 읽습니다.|
|/hubs/kpi/read|모든 Azure Customer Insights 핵심 성과 지표를 읽습니다.|
|/hubs/kpi/reprocess/action|Azure Customer Insights 핵심 성과 지표를 다시 처리합니다.|
|/hubs/kpi/write|모든 Azure Customer Insights 핵심 성과 지표를 만들거나 업데이트합니다.|
|/hubs/links/delete|Azure Customer Insights 링크를 삭제합니다.|
|/hubs/links/operations/read|Azure Customer Insights 링크 작업 결과를 읽습니다.|
|/hubs/links/read|Azure Customer Insights 링크를 읽습니다.|
|/hubs/links/write|Azure Customer 링크를 만들거나 업데이트합니다.|
|/hubs/msemetadata/action|Azure Customer Insights Mse 메타데이터를 만들거나 업데이트합니다.|
|/hubs/msemetadata/read|Azure Customer Insights Mse 메타데이터를 읽습니다.|
|/hubs/operationresults/read|Azure Customer Insights 허브 작업 결과를 가져옵니다.|
|/hubs/predictions/delete|Azure Customer Insights 예측을 삭제합니다.|
|/hubs/predictions/operations/read|Azure Customer Insights 예측 작업 결과를 읽습니다.|
|/hubs/predictions/read|Azure Customer Insights 예측을 읽습니다.|
|/hubs/predictions/write|Azure Customer 예측을 만들거나 업데이트합니다.|
|/hubs/predictivematchpolicies/delete|Azure Customer Insights 예측 매칭 정책을 삭제합니다.|
|/hubs/predictivematchpolicies/operations/read|Azure Customer Insights 예측 매칭 정책 작업 결과를 읽습니다.|
|/hubs/predictivematchpolicies/read|Azure Customer Insights 예측 매칭 정책을 읽습니다.|
|/hubs/predictivematchpolicies/write|Azure Customer Insights 예측 매칭 정책을 만들거나 업데이트합니다.|
|/hubs/profiles/delete|Azure Customer Insights 프로필을 삭제합니다.|
|/hubs/profiles/operations/read|Azure Customer Insights 프로필 작업 결과를 읽습니다.|
|/hubs/profiles/read|Azure Customer Insights 프로필을 읽습니다.|
|/hubs/profiles/write|Azure Customer Insights 프로필을 씁니다.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|리소스에 대해 사용 가능한 로그를 가져옵니다.|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|리소스에 대해 사용 가능한 메트릭을 가져옵니다.|
|/hubs/read|Azure Customer Insights Hub를 읽습니다.|
|/hubs/relationshiplinks/delete|Azure Customer Insights 관계 링크를 삭제합니다.|
|/hubs/relationshiplinks/operations/read|Azure Customer Insights 관계 링크 작업 결과를 읽습니다.|
|/hubs/relationshiplinks/read|Azure Customer Insights 관계 링크를 읽습니다.|
|/hubs/relationshiplinks/write|Azure Customer Insights 관계 링크를 만들거나 업데이트합니다.|
|/hubs/relationships/delete|Azure Customer Insights 관계를 삭제합니다.|
|/hubs/relationships/operations/read|Azure Customer Insights 관계 작업 결과를 읽습니다.|
|/hubs/relationships/read|Azure Customer Insights 관계를 읽습니다.|
|/hubs/relationships/write|Azure Customer Insights 관계를 만들거나 업데이트합니다.|
|/hubs/roleAssignments/delete|Azure Customer Insights RBAC 할당을 삭제합니다.|
|/hubs/roleAssignments/operations/read|Azure Customer Insights Rbac 할당 작업 결과를 읽습니다.|
|/hubs/roleAssignments/read|Azure Customer Insights RBAC 할당을 읽습니다.|
|/hubs/roleAssignments/write|Azure Customer Insights RBAC 할당을 만들거나 업데이트합니다.|
|/hubs/roles/read|Azure Customer Insights Rbac 역할을 읽습니다.|
|/hubs/salesforcemetadata/action|Azure Customer Insights SalesForce 메타데이터를 만들거나 업데이트합니다.|
|/hubs/salesforcemetadata/read|Azure Customer Insights SalesForce 메타데이터를 읽습니다.|
|/hubs/segments/delete|Azure Customer Insights 세그먼트를 삭제합니다.|
|/hubs/segments/dynamic/action|Azure Customer Insights 동적 세그먼트를 관리합니다.|
|/hubs/segments/read|Azure Customer Insights 세그먼트를 읽습니다.|
|/hubs/segments/static/action|Azure Customer Insights 정적 세그먼트를 관리합니다.|
|/hubs/segments/write|Azure Customer Insights 세그먼트를 만들거나 업데이트합니다.|
|/hubs/sqlconnectionstrings/delete|Azure Customer Insights SqlConnectionStrings를 삭제합니다.|
|/hubs/sqlconnectionstrings/read|Azure Customer Insights SqlConnectionStrings를 읽습니다.|
|/hubs/sqlconnectionstrings/write|Azure Customer Insights SqlConnectionStrings를 만들거나 업데이트합니다.|
|/hubs/suggesttypeschema/action|샘플 데이터로 추천 형식 스키마를 생성합니다.|
|/hubs/tenantmanagement/read|Azure Customer Insights 허브 설정을 관리합니다.|
|/hubs/views/delete|Azure Customer Insights 앱 보기를 삭제합니다.|
|/hubs/views/read|Azure Customer Insights 앱 보기를 읽습니다.|
|/hubs/views/write|Azure Customer Insights 앱 보기를 만들거나 업데이트합니다.|
|/hubs/widgettypes/read|Azure Customer Insights 앱 위젯 형식을 읽습니다.|
|/hubs/write|Azure Customer Insights Hub를 만들거나 업데이트합니다.|
|/operations/read|Azure Customer Insights Api 메타데이터를 읽습니다.|
|/register/action|Customer Insights 리소스 공급자에 대한 구독을 등록하고 Customer Insights 리소스를 만들도록 설정합니다.|
|/unregister/action|Customer Insights 리소스 공급자에 대한 구독을 등록을 취소합니다.|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| 작업 | 설명 |
|---|---|
|/catalogs/delete|카탈로그를 삭제합니다.|
|/catalogs/read|구독 또는 리소스 그룹에서 카탈로그에 대한 속성을 가져옵니다.|
|/catalogs/write|카탈로그를 만들거나 카탈로그에 대한 태그 및 속성을 업데이트합니다.|
|/checkNameAvailability/action|테넌트에 대한 카탈로그 이름 가용성을 확인합니다.|
|/operations/read|Microsoft.DataCatalog 리소스 공급자에서 사용 가능한 작업을 나열합니다.|
|/register/action|Microsoft.DataCatalog 리소스 공급자에 구독을 등록합니다.|
|/unregister/action|Microsoft.DataCatalog 리소스 공급자의 구독을 등록 취소합니다.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| 작업 | 설명 |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|datafactories에 사용 가능한 메트릭을 가져옵니다.|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/factories/providers/Microsoft.Insights/logDefinitions/read|팩터리에 사용 가능한 로그를 가져옵니다.|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|팩터리에 사용 가능한 메트릭을 가져옵니다.|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| 작업 | 설명 |
|---|---|
|/accounts/computePolicies/delete|계산 정책을 삭제합니다.|
|/accounts/computePolicies/read|계산 정책에 대한 정보를 가져옵니다.|
|/accounts/computePolicies/write|계산 정책을 만들거나 업데이트합니다.|
|/accounts/dataLakeStoreAccounts/delete|DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다.|
|/accounts/dataLakeStoreAccounts/read|DataLakeAnalytics 계정과 연결된 DataLakeStore 계정에 대한 정보를 가져옵니다.|
|/accounts/dataLakeStoreAccounts/write|DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다.|
|/accounts/delete|DataLakeAnalytics 계정을 삭제합니다.|
|/accounts/firewallRules/delete|방화벽 규칙을 삭제합니다.|
|/accounts/firewallRules/read|방화벽 규칙에 대한 정보를 가져옵니다.|
|/accounts/firewallRules/write|방화벽 규칙을 만들거나 업데이트합니다.|
|/accounts/operationResults/read|DataLakeAnalytics 계정 작업의 결과를 가져옵니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|DataLakeAnalytics 계정에 대한 진단 설정을 가져옵니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|DataLakeAnalytics 계정에 대한 진단 설정을 만들거나 업데이트합니다.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|DataLakeAnalytics 계정에 사용 가능한 로그를 가져옵니다.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|DataLakeAnalytics 계정에 사용 가능한 메트릭을 가져옵니다.|
|/accounts/read|기존 DataLakeAnalytics 계정에 대한 정보를 가져옵니다.|
|/accounts/storageAccounts/Containers/listSasTokens/action|DataLakeAnalytics 계정과 연결된 Storage 계정의 저장소 컨테이너 SAS 토큰을 나열합니다.|
|/accounts/storageAccounts/Containers/read|DataLakeAnalytics 계정과 연결된 Storage 계정의 컨테이너를 가져옵니다.|
|/accounts/storageAccounts/delete|DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다.|
|/accounts/storageAccounts/read|DataLakeAnalytics 계정과 연결된 Storage 계정에 대한 정보를 가져옵니다.|
|/accounts/storageAccounts/write|DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다.|
|/accounts/TakeOwnership/action|다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다.|
|/accounts/write|DataLakeAnalytics 계정을 만들거나 업데이트합니다.|
|/locations/capability/read|DataLakeAnalytics 사용과 관련된 구독의 기능 정보를 가져옵니다.|
|/locations/checkNameAvailability/action|DataLakeAnalytics 계정 이름의 가용성을 확인합니다.|
|/locations/operationResults/read|DataLakeAnalytics 계정 작업의 결과를 가져옵니다.|
|/operations/read|DataLakeAnalytics에서 사용 가능한 작업을 가져옵니다.|
|/register/action|DataLakeAnalytics에 구독을 등록합니다.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| 작업 | 설명 |
|---|---|
|/accounts/delete|DataLakeStore 계정을 삭제합니다.|
|/accounts/enableKeyVault/action|DataLakeStore 계정에 Keyvault를 사용하도록 설정합니다.|
|/accounts/firewallRules/delete|방화벽 규칙을 삭제합니다.|
|/accounts/firewallRules/read|방화벽 규칙에 대한 정보를 가져옵니다.|
|/accounts/firewallRules/write|방화벽 규칙을 만들거나 업데이트합니다.|
|/accounts/operationResults/read|DataLakeStore 계정 작업의 결과를 가져옵니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|DataLakeStore 계정에 대한 진단 설정을 가져옵니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|DataLakeStore 계정에 대한 진단 설정을 만들거나 업데이트합니다.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|DataLakeStore 계정에 사용 가능한 로그를 가져옵니다.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|DataLakeStore 계정에 사용 가능한 메트릭을 가져옵니다.|
|/accounts/read|기존 DataLakeStore 계정에 대한 정보를 가져옵니다.|
|/accounts/Superuser/action|Microsoft.Authorization/roleAssignments/write가 부여되면 Data Lake Store에 대한 슈퍼 사용자 권한을 부여합니다.|
|/accounts/trustedIdProviders/delete|신뢰할 수 있는 ID 공급자를 삭제합니다.|
|/accounts/trustedIdProviders/read|신뢰할 수 있는 ID 공급자에 대한 정보를 가져옵니다.|
|/accounts/trustedIdProviders/write|신뢰할 수 있는 ID 공급자를 만들거나 업데이트합니다.|
|/accounts/write|DataLakeStore 계정을 만들거나 업데이트합니다.|
|/locations/capability/read|DataLakeStore 사용과 관련된 구독의 기능 정보를 가져옵니다.|
|/locations/checkNameAvailability/action|DataLakeStore 계정 이름의 가용성을 확인합니다.|
|/locations/operationResults/read|DataLakeStore 계정 작업의 결과를 가져옵니다.|
|/operations/read|DataLakeStore에서 사용 가능한 작업을 가져옵니다.|
|/register/action|DataLakeStore에 구독을 등록합니다.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| 작업 | 설명 |
|---|---|
|/locations/performanceTiers/read|사용할 수 있는 성능 계층 목록을 반환합니다.|
|/performanceTiers/read|사용할 수 있는 성능 계층 목록을 반환합니다.|
|/servers/delete|기존 서버를 삭제합니다.|
|/servers/firewallRules/delete|기존 방화벽 규칙을 삭제합니다.|
|/servers/firewallRules/read|서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다.|
|/servers/firewallRules/write|지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/read|서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다.|
|/servers/recoverableServers/read|복구 가능한 MySQL 서버 정보를 반환합니다.|
|/servers/virtualNetworkRules/delete|기존 가상 네트워크 규칙을 삭제합니다.|
|/servers/virtualNetworkRules/read|가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다.|
|/servers/virtualNetworkRules/write|지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다.|
|/servers/write|지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| 작업 | 설명 |
|---|---|
|/locations/performanceTiers/read|사용할 수 있는 성능 계층 목록을 반환합니다.|
|/performanceTiers/read|사용할 수 있는 성능 계층 목록을 반환합니다.|
|/servers/delete|기존 서버를 삭제합니다.|
|/servers/firewallRules/delete|기존 방화벽 규칙을 삭제합니다.|
|/servers/firewallRules/read|서버에 대한 방화벽 규칙 목록을 반환하거나 지정된 방화벽 규칙에 대한 속성을 가져옵니다.|
|/servers/firewallRules/write|지정된 매개 변수를 사용하여 방화벽 규칙을 만들거나 기존 규칙을 업데이트합니다.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/providers/Microsoft.Insights/logDefinitions/read|데이터베이스에 사용할 수 있는 로그 형식을 반환합니다.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/read|서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다.|
|/servers/recoverableServers/read|복구 가능한 PostgreSQL 서버 정보를 반환합니다.|
|/servers/virtualNetworkRules/delete|기존 가상 네트워크 규칙을 삭제합니다.|
|/servers/virtualNetworkRules/read|가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다.|
|/servers/virtualNetworkRules/write|지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다.|
|/servers/write|지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/Action|IotHub 이름이 사용 가능한지 확인합니다.|
|/checkProvisioningServiceNameAvailability/Action|IotHub 이름이 사용 가능한지 확인합니다.|
|/ElasticPools/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/ElasticPools/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/elasticPools/iotHubTenants/Delete|IotHub 테넌트 리소스를 삭제합니다.|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|EventHub 소비자 그룹을 삭제합니다.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|EventHub 소비자 그룹을 가져옵니다.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|EventHub 소비자 그룹을 만듭니다.|
|/elasticPools/iotHubTenants/exportDevices/Action|장치를 내보냅니다.|
|/elasticPools/iotHubTenants/getStats/Read|IotHub 테넌트 통계 리소스를 가져옵니다.|
|/elasticPools/iotHubTenants/importDevices/Action|장치를 가져옵니다.|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|IotHub 테넌트 키를 가져옵니다.|
|/elasticPools/iotHubTenants/jobs/Read|지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다.|
|/elasticPools/iotHubTenants/listKeys/Action|IotHub 테넌트 키를 가져옵니다.|
|/ElasticPools/IotHubTenants/logDefinitions/read|IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다.|
|/ElasticPools/IotHubTenants/metricDefinitions/read|IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다.|
|/elasticPools/iotHubTenants/quotaMetrics/Read|할당량 메트릭을 가져옵니다.|
|/elasticPools/iotHubTenants/Read|IotHub 테넌트 리소스를 가져옵니다.|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|모든 기존 경로에 대해 메시지를 테스트합니다.|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|제공된 테스트 경로에 대해 메시지를 테스트합니다.|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|IotHub에 대한 모든 라우팅 끝점의 상태를 가져옵니다.|
|/elasticPools/iotHubTenants/Write|IotHub 테넌트 리소스를 만들거나 업데이트합니다.|
|/ElasticPools/metricDefinitions/read|IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다.|
|/iotHubs/certificates/generateVerificationCode/Action|확인 코드를 생성합니다.|
|/iotHubs/certificates/verify/Action|인증서 리소스를 확인합니다.|
|/iotHubs/Delete|IotHub 리소스를 삭제합니다.|
|/IotHubs/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/IotHubs/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/iotHubs/eventGridFilters/Delete|Event Grid 필터를 삭제합니다.|
|/iotHubs/eventGridFilters/Read|Event Grid 필터를 가져옵니다.|
|/iotHubs/eventGridFilters/Write|새 Event Grid 필터를 만들거나 기존 Event Grid 필터를 업데이트합니다.|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|EventHub 소비자 그룹을 삭제합니다.|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|EventHub 소비자 그룹을 가져옵니다.|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|EventHub 소비자 그룹을 만듭니다.|
|/iotHubs/exportDevices/Action|장치를 내보냅니다.|
|/iotHubs/importDevices/Action|장치를 가져옵니다.|
|/iotHubs/iotHubKeys/listkeys/Action|지정된 이름의 IotHub 키를 가져옵니다.|
|/iotHubs/iotHubStats/Read|IotHub 통계를 가져옵니다.|
|/iotHubs/jobs/Read|지정된 IotHub에 대해 제출된 작업 세부 정보를 가져옵니다.|
|/iotHubs/listkeys/Action|모든 IotHub 키를 가져옵니다.|
|/IotHubs/logDefinitions/read|IotHub 서비스에 사용 가능한 로그 정의를 가져옵니다.|
|/IotHubs/metricDefinitions/read|IotHub 서비스에 대해 사용 가능한 메트릭을 가져옵니다.|
|/iotHubs/quotaMetrics/Read|할당량 메트릭을 가져옵니다.|
|/iotHubs/Read|IotHub 리소스를 가져옵니다.|
|/iotHubs/routing/$testall/Action|모든 기존 경로에 대해 메시지를 테스트합니다.|
|/iotHubs/routing/$testnew/Action|제공된 테스트 경로에 대해 메시지를 테스트합니다.|
|/iotHubs/routingEndpointsHealth/Read|IotHub에 대한 모든 라우팅 끝점의 상태를 가져옵니다.|
|/iotHubs/skus/Read|유효한 IotHub SKU를 가져옵니다.|
|/iotHubs/Write|IotHub 리소스를 만들거나 업데이트합니다.|
|/operations/Read|모든 ResourceProvider 작업을 가져옵니다.|
|/provisioningServices/certificates/generateVerificationCode/Action|확인 코드를 생성합니다.|
|/provisioningServices/certificates/verify/Action|인증서 리소스를 확인합니다.|
|/provisioningServices/Delete|IotDps 리소스를 삭제합니다.|
|/provisioningServices/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/provisioningServices/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/provisioningServices/listkeys/Action|모든 IotDps 키를 가져옵니다.|
|/provisioningServices/logDefinitions/read|서비스를 프로비전하는 데 사용할 수 있는 로그 정의를 가져옵니다.|
|/provisioningServices/metricDefinitions/read|서비스를 프로비전하는 데 사용할 수 있는 메트릭을 가져옵니다.|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|키 이름에 대 한 IotDps 키를 가져옵니다.|
|/provisioningServices/Read|IotDps 리소스를 가져옵니다.|
|/provisioningServices/skus/Read|유효한 IotDps Sku를 가져옵니다.|
|/provisioningServices/Write|IotDps 리소스를 만듭니다.|
|/register/action|IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다.|
|/register/action|IotHub 리소스 공급자에 대한 구독을 등록하고 IotHub 리소스를 만들도록 설정합니다.|
|/usages/Read|이 공급자에 대한 구독 사용 정보를 가져옵니다.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| 작업 | 설명 |
|---|---|
|/labCenters/delete|랩 센터를 삭제합니다.|
|/labCenters/read|랩 센터를 읽습니다.|
|/labCenters/write|랩 센터를 추가 또는 수정합니다.|
|/labs/artifactSources/armTemplates/read|Azure Resource Manager 템플릿을 읽습니다.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|지정된 아티팩트에 대한 ARM 템플릿을 생성하고, 필요한 파일을 저장소 계정에 업로드하고, 생성된 아티팩트의 유효성을 검사합니다.|
|/labs/artifactSources/artifacts/read|아티팩트를 읽습니다.|
|/labs/artifactSources/delete|아티팩트 원본을 삭제합니다.|
|/labs/artifactSources/read|아티팩트 원본을 읽습니다.|
|/labs/artifactSources/write|아티팩트 원본을 추가하거나 수정합니다.|
|/labs/ClaimAnyVm/action|랩에서 임의 클레임 가능 가상 머신을 클레임합니다.|
|/labs/costs/read|비용을 읽습니다.|
|/labs/costs/write|비용을 추가하거나 수정합니다.|
|/labs/CreateEnvironment/action|랩에 가상 머신을 만듭니다.|
|/labs/customImages/delete|사용자 지정 이미지를 삭제합니다.|
|/labs/customImages/read|사용자 지정 이미지를 읽습니다.|
|/labs/customImages/write|사용자 지정 이미지를 추가하거나 수정합니다.|
|/labs/delete|랩을 삭제합니다.|
|/labs/ExportResourceUsage/action|랩 리소스 사용량을 저장소 계정으로 내보냅니다.|
|/labs/formulas/delete|수식을 삭제합니다.|
|/labs/formulas/read|수식을 읽습니다.|
|/labs/formulas/write|수식을 추가하거나 수정합니다.|
|/labs/galleryImages/read|갤러리 이미지를 읽습니다.|
|/labs/GenerateUploadUri/action|랩에 사용자 지정 디스크 이미지를 업로드하기 위한 URI를 생성합니다.|
|/labs/ImportVirtualMachine/action|가상 머신을 다른 랩으로 가져옵니다.|
|/labs/ListVhds/action|사용자 지정 이미지를 만드는 데 사용할 수 있는 디스크 이미지를 나열합니다.|
|/labs/notificationChannels/delete|notificationchannels를 삭제합니다.|
|/labs/notificationChannels/Notify/action|제공된 채널에 알림을 보냅니다.|
|/labs/notificationChannels/read|notificationchannels를 읽습니다.|
|/labs/notificationChannels/write|notificationchannels를 추가하거나 수정합니다.|
|/labs/policySets/EvaluatePolicies/action|랩 정책을 평가합니다.|
|/labs/policySets/policies/delete|정책을 삭제합니다.|
|/labs/policySets/policies/read|정책을 읽습니다.|
|/labs/policySets/policies/write|정책을 추가하거나 수정합니다.|
|/labs/read|랩을 읽습니다.|
|/labs/schedules/delete|일정을 삭제합니다.|
|/labs/schedules/Execute/action|일정을 실행합니다.|
|/labs/schedules/ListApplicable/action|적용 가능한 모든 일정을 나열합니다.|
|/labs/schedules/read|일정을 읽습니다.|
|/labs/schedules/write|일정을 추가하거나 수정합니다.|
|/labs/serviceRunners/delete|서비스 실행기를 삭제합니다.|
|/labs/serviceRunners/read|서비스 실행기를 읽습니다.|
|/labs/serviceRunners/write|서비스 실행기를 추가하거나 수정합니다.|
|/labs/users/delete|사용자 프로필을 삭제합니다.|
|/labs/users/disks/Attach/action|디스크의 임대를 만들고 가상 머신에 연결합니다.|
|/labs/users/disks/delete|디스크를 삭제합니다.|
|/labs/users/disks/Detach/action|가상 머신에 연결된 디스크의 임대를 분리하고 해제합니다.|
|/labs/users/disks/read|디스크를 읽습니다.|
|/labs/users/disks/write|디스크를 추가 또는 수정합니다.|
|/labs/users/environments/delete|환경을 삭제합니다.|
|/labs/users/environments/read|환경을 읽습니다.|
|/labs/users/environments/write|환경을 추가하거나 수정합니다.|
|/labs/users/read|사용자 프로필을 읽습니다.|
|/labs/users/secrets/delete|암호를 삭제합니다.|
|/labs/users/secrets/read|암호를 읽습니다.|
|/labs/users/secrets/write|암호를 추가 또는 수정합니다.|
|/labs/users/serviceFabrics/delete|서비스 패브릭을 삭제합니다.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|적용 가능한 모든 일정을 나열합니다.|
|/labs/users/serviceFabrics/read|서비스 패브릭을 읽습니다.|
|/labs/users/serviceFabrics/schedules/delete|일정을 삭제합니다.|
|/labs/users/serviceFabrics/schedules/Execute/action|일정을 실행합니다.|
|/labs/users/serviceFabrics/schedules/read|일정을 읽습니다.|
|/labs/users/serviceFabrics/schedules/write|일정을 추가하거나 수정합니다.|
|/labs/users/serviceFabrics/Start/action|서비스 패브릭을 시작합니다.|
|/labs/users/serviceFabrics/Stop/action|서비스 패브릭을 중지합니다.|
|/labs/users/serviceFabrics/write|서비스 패브릭을 추가하거나 수정합니다.|
|/labs/users/write|사용자 프로필을 추가하거나 수정합니다.|
|/labs/virtualMachines/AddDataDisk/action|가상 머신에 새 또는 기존 데이터 디스크를 연결합니다.|
|/labs/virtualMachines/ApplyArtifacts/action|가상 머신에 아티팩트를 적용합니다.|
|/labs/virtualMachines/Claim/action|기존 가상 머신의 소유권을 가져옵니다.|
|/labs/virtualMachines/delete|가상 머신을 삭제합니다.|
|/labs/virtualMachines/DetachDataDisk/action|가상 머신에서 지정된 디스크를 분리합니다.|
|/labs/virtualMachines/ListApplicableSchedules/action|적용 가능한 모든 일정을 나열합니다.|
|/labs/virtualMachines/read|가상 머신을 읽습니다.|
|/labs/virtualMachines/Restart/action|가상 머신을 다시 시작합니다.|
|/labs/virtualMachines/schedules/delete|일정을 삭제합니다.|
|/labs/virtualMachines/schedules/Execute/action|일정을 실행합니다.|
|/labs/virtualMachines/schedules/read|일정을 읽습니다.|
|/labs/virtualMachines/schedules/write|일정을 추가하거나 수정합니다.|
|/labs/virtualMachines/Start/action|가상 머신을 시작합니다.|
|/labs/virtualMachines/Stop/action|가상 머신 중지|
|/labs/virtualMachines/TransferDisks/action|가상 머신 데이터 디스크의 소유권을 자신에게 이전합니다.|
|/labs/virtualMachines/UnClaim/action|기존 가상 머신의 소유권을 해제합니다.|
|/labs/virtualMachines/write|가상 머신을 추가 또는 수정합니다.|
|/labs/virtualNetworks/delete|가상 네트워크를 삭제합니다.|
|/labs/virtualNetworks/read|가상 네트워크를 읽습니다.|
|/labs/virtualNetworks/write|가상 네트워크를 추가 또는 수정합니다.|
|/labs/write|랩을 추가 또는 수정합니다.|
|/locations/operations/read|읽기 작업|
|/register/action|구독을 등록합니다.|
|/schedules/delete|일정을 삭제합니다.|
|/schedules/Execute/action|일정을 실행합니다.|
|/schedules/read|일정을 읽습니다.|
|/schedules/Retarget/action|일정의 대상 리소스 ID를 업데이트합니다.|
|/schedules/write|일정을 추가하거나 수정합니다.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| 작업 | 설명 |
|---|---|
|/databaseAccountNames/read|이름 가용성을 확인합니다.|
|/databaseAccounts/changeResourceGroup/action|데이터베이스 계정의 리소스 그룹을 변경합니다.|
|/databaseAccounts/databases/collections/metricDefinitions/read|컬렉션 메트릭 정의를 읽습니다.|
|/databaseAccounts/databases/collections/metrics/read|컬렉션 메트릭을 읽습니다.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|데이터베이스 계정 파티션 키 수준 메트릭을 읽습니다.|
|/databaseAccounts/databases/collections/partitions/metrics/read|데이터베이스 계정 파티션 수준 메트릭을 읽습니다.|
|/databaseAccounts/databases/collections/partitions/usages/read|데이터베이스 계정 파티션 수준 사용량을 읽습니다.|
|/databaseAccounts/databases/collections/usages/read|컬렉션 사용 현황을 읽습니다.|
|/databaseAccounts/databases/metricDefinitions/read|데이터베이스 메트릭 정의를 읽습니다.|
|/databaseAccounts/databases/metrics/read|데이터베이스 메트릭을 읽습니다.|
|/databaseAccounts/databases/usages/read|데이터베이스 사용 현황을 읽습니다.|
|/databaseAccounts/delete|데이터베이스 계정을 삭제합니다.|
|/databaseAccounts/failoverPriorityChange/action|데이터베이스 계정 영역의 장애 조치 우선 순위를 변경합니다. 수동 장애 조치 작업을 수행하는 데 사용됩니다.|
|/databaseAccounts/listConnectionStrings/action|데이터베이스 계정에 대한 연결 문자열을 가져옵니다.|
|/databaseAccounts/listKeys/action|데이터베이스 계정의 키를 나열합니다.|
|/databaseAccounts/metricDefinitions/read|데이터베이스 계정 메트릭 정의를 읽습니다.|
|/databaseAccounts/metrics/read|데이터베이스 계정 메트릭을 읽습니다.|
|/databaseAccounts/operationResults/read|비동기 작업 상태를 읽습니다.|
|/databaseAccounts/percentile/metrics/read|대기 시간 메트릭을 읽습니다.|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|특정 소스 및 대상 지역에 대한 대기 시간 메트릭을 읽습니다.|
|/databaseAccounts/percentile/targetRegion/metrics/read|특정 대상 지역에 대한 대기 시간 메트릭을 읽습니다.|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|데이터베이스 계정에 사용 가능한 로그를 가져옵니다.|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|데이터베이스 계정에 사용 가능한 메트릭을 가져옵니다.|
|/databaseAccounts/read|데이터베이스 계정을 읽습니다.|
|/databaseAccounts/readonlykeys/action|데이터베이스 계정 읽기 전용 키를 읽습니다.|
|/databaseAccounts/readonlykeys/read|데이터베이스 계정 읽기 전용 키를 읽습니다.|
|/databaseAccounts/regenerateKey/action|데이터베이스 계정의 키를 순환합니다.|
|/databaseAccounts/region/databases/collections/metrics/read|지역 컬렉션 메트릭을 읽습니다.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|지역 데이터베이스 계정 파티션 키 수준 메트릭을 읽습니다.|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|지역 데이터베이스 계정 파티션 수준 메트릭을 읽습니다.|
|/databaseAccounts/region/databases/collections/partitions/read|컬렉션의 데이터베이스 계정 파티션을 읽습니다.|
|/databaseAccounts/region/metrics/read|지역 및 데이터베이스 계정 메트릭을 읽습니다.|
|/databaseAccounts/usages/read|데이터베이스 계정 사용 현황을 읽습니다.|
|/databaseAccounts/write|데이터베이스 계정을 업데이트합니다.|
|/locations/deleteVirtualNetworkOrSubnets/action|Microsoft.DocumentDB에 가상 네트워크 또는 서브넷이 삭제됨을 알립니다.|
|/operationResults/read|비동기 작업 상태를 읽습니다.|
|/operations/read|Microsoft DocumentDB에 사용 가능한 작업을 읽습니다. |
|/register/action| 구독에 대한 Microsoft DocumentDB 리소스 공급자를 등록합니다.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| 작업 | 설명 |
|---|---|
|/checkDomainAvailability/Action|도메인을 구매할 수 있는지를 확인합니다.|
|/domains/Delete|기존 도메인을 삭제합니다.|
|/domains/domainownershipidentifiers/Delete|소유권 식별자를 삭제합니다.|
|/domains/domainownershipidentifiers/Read|소유권 식별자를 나열합니다.|
|/domains/domainownershipidentifiers/Read|소유권 식별자를 가져옵니다.|
|/domains/domainownershipidentifiers/Write|업데이트 식별자를 만듭니다.|
|/domains/operationresults/Read|도메인 작업을 가져옵니다.|
|/domains/operations/Read|앱 서비스 도메인 등록의 모든 작업을 나열합니다.|
|/domains/Read|도메인 목록을 가져옵니다.|
|/domains/Read|도메인을 가져옵니다.|
|/domains/renew/Action|기존 도메인을 갱신합니다.|
|/domains/Write|새 도메인을 추가하거나 기존 도메인을 업데이트합니다.|
|/generateSsoRequest/Action|도메인 제어 센터에 대한 로그인 요청을 생성합니다.|
|/listDomainRecommendations/Action|키워드를 기준으로 목록 도메인 권장 사항을 검색합니다.|
|/register/action|구독에 대한 Microsoft 도메인 리소스 공급자를 등록합니다.|
|/topLevelDomains/listAgreements/Action|규약 작업을 나열합니다.|
|/topLevelDomains/Read|최상위 도메인을 가져옵니다.|
|/topLevelDomains/Read|최상위 도메인을 가져옵니다.|
|/validateDomainRegistrationInformation/Action|도메인 구매 개체를 제출하지 않고 유효성을 검사합니다.|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| 작업 | 설명 |
|---|---|
|/lcsprojects/clouddeployments/read|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트의 Microsoft Dynamics AX 2012 R3 평가판 배포를 표시합니다.|
|/lcsprojects/clouddeployments/write|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트에서 Microsoft Dynamics AX 2012 R3 평가판 배포를 만듭니다. Azure 관리 포털에서 배포를 관리할 수 있습니다.|
|/lcsprojects/connectors/read|Microsoft Dynamics Lifecycle Services 프로젝트에 속하는 커넥터를 읽습니다.|
|/lcsprojects/connectors/write|Microsoft Dynamics Lifecycle Services 프로젝트에 속하는 커넥터를 만들고 업데이트합니다.|
|/lcsprojects/delete|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트를 삭제합니다.|
|/lcsprojects/read|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트를 표시합니다.|
|/lcsprojects/write|사용자에 속하는 Microsoft Dynamics Lifecycle Services 프로젝트를 만들고 업데이트합니다. 이름 및 설명 속성만 업데이트할 수 있습니다. 만든 후 프로젝트와 연결된 구독 및 위치를 업데이트할 수 없습니다.|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| 작업 | 설명 |
|---|---|
|/eventSubscriptions/delete|eventSubscription을 삭제합니다.|
|/eventSubscriptions/getFullUrl/action|이벤트 구독의 전체 url을 가져옵니다.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|이벤트 구독에 대한 진단 설정을 가져옵니다.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|이벤트 구독에 대한 진단 설정을 만들거나 업데이트합니다.|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|eventSubscription에 사용 가능한 메트릭을 가져옵니다.|
|/eventSubscriptions/read|eventSubscription을 읽습니다.|
|/eventSubscriptions/write|eventSubscription을 만들거나 업데이트합니다.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|토픽에 대한 진단 설정을 가져옵니다.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|토픽에 대한 진단 설정을 만들거나 업데이트합니다.|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|토픽에 사용 가능한 메트릭을 가져옵니다.|
|/register/action|Event Grid 리소스 공급자에 대한 eventSubscription을 등록하고 Event Grid 구독을 만들도록 설정합니다.|
|/topics/delete|항목 삭제|
|/topics/listKeys/action|토픽에 대한 키를 나열합니다.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|토픽에 대한 진단 설정을 가져옵니다.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|토픽에 대한 진단 설정을 만들거나 업데이트합니다.|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|토픽에 사용 가능한 메트릭을 가져옵니다.|
|/topics/read|토픽을 읽습니다.|
|/topics/regenerateKey/action|토픽에 대한 키를 다시 생성합니다.|
|/topics/write|토픽을 만들거나 업데이트합니다.|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다.|
|/checkNamespaceAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailabiltiy를 대신 사용하세요.|
|/namespaces/authorizationRules/action|네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/namespaces/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다.|
|/namespaces/disasterRecoveryConfigs/delete|네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다.|
|/namespaces/disasterRecoveryConfigs/failover/action|GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다.|
|/namespaces/disasterRecoveryConfigs/read|네임스페이스와 연결된 재해 복구 구성을 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/write|네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다.|
|/namespaces/eventhubs/authorizationRules/action|EventHub를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요.|
|/namespaces/eventhubs/authorizationRules/delete|EventHub 권한 부여 규칙 삭제 작업|
|/namespaces/eventhubs/authorizationRules/listkeys/action|EventHub의 연결 문자열을 가져옵니다.|
|/namespaces/eventhubs/authorizationRules/read| EventHub 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/eventhubs/authorizationRules/write|EventHub 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다.|
|/namespaces/eventHubs/consumergroups/Delete|ConsumerGroup 리소스 삭제 작업|
|/namespaces/eventHubs/consumergroups/read|ConsumerGroup 리소스 설명의 목록을 가져옵니다.|
|/namespaces/eventHubs/consumergroups/write|ConsumerGroup 속성을 만들거나 업데이트합니다.|
|/namespaces/eventhubs/Delete|EventHub 리소스 삭제 작업|
|/namespaces/eventhubs/read|EventHub 리소스 설명의 목록을 가져옵니다.|
|/namespaces/eventhubs/write|EventHub 속성을 만들거나 업데이트합니다.|
|/namespaces/messagingPlan/read|네임스페이스에 대한 메시지 계획을 가져옵니다. 이 API는 더 이상 사용되지 않습니다. MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/messagingPlan/write|네임스페이스에 대한 메시지 계획을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/operationresults/read|네임스페이스 작업의 상태를 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|네임스페이스 로그 리소스 설명의 목록을 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다.|
|/operations/read|작업을 가져옵니다.|
|/register/action|EventHub 리소스 공급자에 대한 구독을 등록하고 EventHub 리소스를 만들도록 설정합니다.|
|/sku/read|Sku 리소스 설명의 목록을 가져옵니다.|
|/sku/regions/read|SkuRegions 리소스 설명의 목록을 가져옵니다.|
|/unregister/action|EventHub 리소스 공급자를 등록합니다.|

## <a name="microsoftfeatures"></a>Microsoft.Features

| 작업 | 설명 |
|---|---|
|/features/read|구독 기능을 가져옵니다.|
|/providers/features/read|지정된 리소스 공급자에서 구독의 기능을 가져옵니다.|
|/providers/features/register/action|지정된 리소스 공급자에서 구독의 기능을 등록합니다.|
|/providers/features/unregister/action|지정된 리소스 공급자에서 구독의 기능을 등록 취소합니다.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| 작업 | 설명 |
|---|---|
|/clusters/changerdpsetting/action|HDInsight 클러스터의 RDP 설정을 변경합니다.|
|/clusters/configurations/action|HDInsight 클러스터 구성을 업데이트합니다.|
|/clusters/configurations/read|HDInsight 클러스터 구성을 가져옵니다.|
|/clusters/delete|HDInsight 클러스터를 삭제합니다.|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|리소스 HDInsight 클러스터에 대한 진단 설정을 가져옵니다.|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|리소스 HDInsight 클러스터에 대한 진단 설정을 만들거나 업데이트합니다.|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|HDInsight 클러스터에 사용 가능한 메트릭을 가져옵니다.|
|/clusters/read|HDInsight 클러스터에 대한 세부 정보를 가져옵니다.|
|/clusters/roles/resize/action|HDInsight 클러스터 크기를 조정합니다.|
|/clusters/write|HDInsight 클러스터를 만들거나 업데이트합니다.|
|/locations/capabilities/read|구독 기능을 가져옵니다.|
|/locations/checkNameAvailability/read|이름 가용성을 확인합니다.|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| 작업 | 설명 |
|---|---|
|/jobs/delete|기존 작업을 삭제합니다.|
|/jobs/listBitLockerKeys/action|지정된 작업에 대한 BitLocker 키를 가져옵니다.|
|/jobs/read|지정된 작업에 대한 속성을 가져오거나 작업 목록을 반환합니다.|
|/jobs/write|지정된 매개 변수를 사용하여 작업을 만들거나 지정된 작업에 대한 속성 또는 태그를 업데이트합니다.|
|/locations/read|지정된 위치에 대한 속성을 가져오거나 위치 목록을 반환합니다.|
|/register/action|Import/Export 리소스 공급자에 대한 구독을 등록하고 Import/Export 작업을 만들도록 설정합니다.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| 작업 | 설명 |
|---|---|
|/ActionGroups/Delete|작업 그룹을 삭제합니다.|
|/ActionGroups/Read|작업 그룹을 읽습니다.|
|/ActionGroups/Write|작업 그룹을 씁니다.|
|/ActivityLogAlerts/Activated/Action|활동 로그 경고를 트리거함|
|/ActivityLogAlerts/Delete|활동 로그 경고를 삭제합니다.|
|/ActivityLogAlerts/Read|활동 로그 경고를 읽습니다.|
|/ActivityLogAlerts/Write|활동 로그 경고를 읽습니다.|
|/AlertRules/Activated/Action|경고 규칙 활성화됨|
|/AlertRules/Delete|경고 규칙 구성을 삭제하는 중|
|/AlertRules/Incidents/Read|경고 규칙 인시던트 구성을 읽는 중|
|/AlertRules/Read|경고 규칙 구성을 읽는 중|
|/AlertRules/Resolved/Action|경고 규칙 확인됨|
|/AlertRules/Throttled/Action|경고 규칙이 제한됨|
|/AlertRules/Write|경고 규칙 구성에 쓰는 중|
|/AutoscaleSettings/Delete|크기 자동 조정 설정 구성을 삭제하는 중|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|메트릭 정의 읽기|
|/AutoscaleSettings/Read|크기 자동 조정 설정 구성을 읽는 중|
|/AutoscaleSettings/Scaledown/Action|크기 자동 조정 규모 축소 작업|
|/AutoscaleSettings/Scaleup/Action|크기 자동 조정 규모 확대 작업|
|/AutoscaleSettings/Write|크기 자동 조정 설정 구성에 쓰는 중|
|/Components/AnalyticsItems/Delete|Application Insights 분석 항목을 삭제합니다.|
|/Components/AnalyticsItems/Read|Application Insights 분석 항목을 읽습니다.|
|/Components/AnalyticsItems/Write|Application Insights 분석 항목을 작성합니다.|
|/Components/AnalyticsTables/Action|Application Insights 분석 테이블 작업입니다.|
|/Components/AnalyticsTables/Delete|Application Insights 분석 테이블 스키마를 삭제합니다.|
|/Components/AnalyticsTables/Read|Application Insights 분석 테이블 스키마를 읽습니다.|
|/Components/AnalyticsTables/Write|Application Insights 분석 테이블 스키마를 작성합니다.|
|/Components/Annotations/Delete|Application Insights 주석을 삭제합니다.|
|/Components/Annotations/Read|Application Insights 주석을 읽습니다.|
|/Components/Annotations/Write|Application Insights 주석을 작성합니다.|
|/Components/Api/Read|Application Insights 구성 요소 데이터 API를 읽습니다.|
|/Components/ApiKeys/Action|Application Insights API 키를 생성합니다.|
|/Components/ApiKeys/Delete|Application Insights API 키를 삭제합니다.|
|/Components/ApiKeys/Read|Application Insights API 키를 읽습니다.|
|/Components/BillingPlanForComponent/Read|Application Insights 구성 요소에 대한 청구 계획을 읽습니다.|
|/Components/CurrentBillingFeatures/Read|Application Insights 구성 요소에 대한 현재 청구 기능을 읽습니다.|
|/Components/CurrentBillingFeatures/Write|Application Insights 구성 요소에 대한 현재 청구 기능을 작성합니다.|
|/Components/DefaultWorkItemConfig/Read|Application Insights 기본 ALM 통합 구성을 읽습니다.|
|/Components/Delete|Application Insights 구성 요소 구성을 삭제합니다.|
|/Components/ExportConfiguration/Action|Application Insights 내보내기 설정 작업입니다.|
|/Components/ExportConfiguration/Delete|Application Insights 내보내기 설정을 삭제합니다.|
|/Components/ExportConfiguration/Read|Application Insights 내보내기 설정을 읽습니다.|
|/Components/ExportConfiguration/Write|Application Insights 내보내기 설정을 작성합니다.|
|/Components/ExtendQueries/Read|Application Insights 구성 요소 확장 쿼리 결과를 읽습니다.|
|/Components/Favorites/Delete|Application Insights 즐겨찾기를 삭제합니다.|
|/Components/Favorites/Read|Application Insights 즐겨찾기를 읽습니다.|
|/Components/Favorites/Write|Application Insights 즐겨찾기를 작성합니다.|
|/Components/FeatureCapabilities/Read|Application Insights 구성 요소 기능을 읽습니다.|
|/Components/GetAvailableBillingFeatures/Read|청구 기능에 사용 가능한 Application Insights 구성 요소를 읽습니다.|
|/Components/GetToken/Read|Application Insights 구성 요소 토큰을 읽습니다.|
|/Components/ListMigrationDate/Action|구독 마이그레이션 날짜를 뒤로 돌립니다.|
|/Components/ListMigrationDate/Read|구독 마이그레이션 날짜를 뒤로 돌립니다.|
|/Components/MetricDefinitions/Read|Application Insights 구성 요소 메트릭 정의를 읽습니다.|
|/Components/Metrics/Read|Application Insights 구성 요소 메트릭을 읽습니다.|
|/Components/MigrateToNewpricingModel/Action|구독을 새로운 가격 책정 모델로 마이그레이션합니다.|
|/Components/Move/Action|Application Insights 구성 요소를 다른 리소스 그룹 또는 구독으로 이동합니다.|
|/Components/MyAnalyticsItems/Delete|Application Insights 개인 분석 항목을 삭제합니다.|
|/Components/MyAnalyticsItems/Read|Application Insights 개인 분석 항목을 읽습니다.|
|/Components/MyAnalyticsItems/Write|Application Insights 개인 분석 항목을 작성합니다.|
|/Components/MyFavorites/Read|Application Insights 개인 즐겨찾기를 읽습니다.|
|/Components/PricingPlans/Read|Application Insights 구성 요소 가격 책정 계획을 읽습니다.|
|/Components/PricingPlans/Write|Application Insights 구성 요소 가격 책정 계획을 작성합니다.|
|/Components/ProactiveDetectionConfigs/Read|Application Insights 자동 관리 검색 구성을 읽습니다.|
|/Components/ProactiveDetectionConfigs/Write|Application Insights 자동 관리 검색 구성을 작성합니다.|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|메트릭 정의 읽기|
|/Components/QuotaStatus/Read|Application Insights 구성 요소 할당량 상태를 읽습니다.|
|/Components/Read|Application Insights 구성 요소 구성을 읽습니다.|
|/Components/RollbackToLegacyPricingModel/Action|구독을 레거시 가격 책정 모델로 롤백합니다.|
|/Components/SyntheticMonitorLocations/Read|Application Insights 웹 테스트 위치를 읽습니다.|
|/Components/WorkItemConfigs/Delete|Application Insights ALM 통합 구성을 삭제합니다.|
|/Components/WorkItemConfigs/Read|Application Insights ALM 통합 구성을 읽습니다.|
|/Components/WorkItemConfigs/Write|Application Insights ALM 통합 구성을 작성합니다.|
|/Components/Write|Application Insights 구성 요소 구성에 씁니다.|
|/DiagnosticSettings/Delete|진단 설정 구성 삭제 중|
|/DiagnosticSettings/Read|진단 설정 구성 읽는 중|
|/DiagnosticSettings/Write|진단 설정 구성에 쓰는 중|
|/EventCategories/Read|이벤트 범주를 읽습니다.|
|/eventtypes/digestevents/Read|관리 이벤트 유형 다이제스트 읽기|
|/eventtypes/values/Read|관리 이벤트 유형 값 읽기|
|/ExtendedDiagnosticSettings/Delete|확장 진단 설정 구성을 삭제함|
|/ExtendedDiagnosticSettings/Read|확장 진단 설정 구성을 읽음|
|/ExtendedDiagnosticSettings/Write|확장 진단 설정 구성에 씀|
|/LogDefinitions/Read|로그 정의 읽기|
|/LogProfiles/Delete|로그 프로필 구성 삭제|
|/LogProfiles/Read|로그 프로필 읽기|
|/LogProfiles/Write|로그 프로필 구성에 쓰는 중|
|/MetricAlerts/Delete|메트릭 경고를 삭제합니다.|
|/MetricAlerts/Read|메트릭 경고를 읽습니다.|
|/MetricAlerts/Write|메트릭 경고를 작성합니다.|
|/MetricDefinitions/Microsoft.Insights/Read|메트릭 정의 읽기|
|/MetricDefinitions/providers/Microsoft.Insights/Read|메트릭 정의 읽기|
|/MetricDefinitions/Read|메트릭 정의 읽기|
|/Metrics/providers/Metrics/Read|메트릭 읽기|
|/Metrics/Read|메트릭 읽기|
|/Metrics/Write|메트릭을 작성합니다.|
|/Operations/Read|작업을 읽습니다.|
|/Register/Action|Microsoft Insights 공급자를 등록합니다.|
|/Tenants/Register/Action|Microsoft Insights 공급자를 초기화합니다.|
|/Unregister/Action|Microsoft Insights 공급자를 등록합니다.|
|/Webtests/Delete|웹 테스트 구성을 삭제합니다.|
|/Webtests/GetToken/Read|웹 테스트 토큰을 읽습니다.|
|/Webtests/MetricDefinitions/Read|웹 테스트 메트릭 정의를 읽습니다.|
|/Webtests/Metrics/Read|웹 테스트 메트릭을 읽습니다.|
|/Webtests/Read|웹 테스트 구성을 읽습니다.|
|/Webtests/Write|웹 테스트 구성에 씁니다.|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/read|Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다.|
|/deletedVaults/read|일시 삭제된 여러 Key Vault의 속성을 봅니다.|
|/hsmPools/delete|HSM 풀을 삭제합니다.|
|/hsmPools/joinVault/action|키 자격 증명 모음을 HSM 풀에 조인합니다.|
|/hsmPools/read|HSM 풀의 속성을 봅니다.|
|/hsmPools/write|기존 HSM 풀의 속성 업데이트에 대해 새 HSM 풀을 만듭니다.|
|/locations/deletedVaults/purge/action|일시 삭제된 Key Vault를 제거합니다.|
|/locations/deletedVaults/read|일시 삭제된 한 Key Vault의 속성을 봅니다.|
|/locations/deleteVirtualNetworkOrSubnets/action|가상 네트워크 또는 서브넷이 삭제됨을 Microsoft.KeyVault에 알립니다.|
|/locations/operationResults/read|장기 실행 작업의 결과를 확인합니다.|
|/operations/read|Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다.|
|/register/action|구독을 등록합니다.|
|/unregister/action|구독을 등록 취소합니다.|
|/vaults/accessPolicies/write|병합 또는 바꾸기를 통해 기존 액세스 정책을 업데이트하거나 새 액세스 정책을 자격 증명 모음에 추가합니다.|
|/vaults/delete|Key Vault를 삭제합니다.|
|/vaults/deploy/action|Azure Resource를 배포하는 동안 Key Vault의 비밀에 액세스할 수 있습니다.|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|리소스에 대한 진단 설정을 가져옵니다.|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|키 자격 증명 모음에 사용 가능한 로그를 가져옵니다.|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|키 자격 증명 모음에 사용 가능한 메트릭을 가져옵니다.|
|/vaults/read|Key Vault의 속성을 봅니다.|
|/vaults/secrets/read|비밀의 값이 아닌 비밀의 속성을 봅니다.|
|/vaults/secrets/write|새 비밀을 만들거나 기존 비밀의 값을 업데이트합니다.|
|/vaults/write|새 Key Vault를 만들거나 기존 Key Vault의 속성을 업데이트합니다.|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| 작업 | 설명 |
|---|---|
|/labAccounts/CreateLab/action|랩 계정에서 랩을 만듭니다.|
|/labAccounts/delete|랩 계정을 삭제합니다.|
|/labAccounts/labs/delete|랩을 삭제합니다.|
|/labAccounts/labs/environmentSettings/delete|환경 설정을 삭제합니다.|
|/labAccounts/labs/environmentSettings/environments/delete|환경을 삭제합니다.|
|/labAccounts/labs/environmentSettings/environments/read|환경을 읽습니다.|
|/labAccounts/labs/environmentSettings/environments/write|환경을 추가하거나 수정합니다.|
|/labAccounts/labs/environmentSettings/Publish/action|랩/환경 설정의 현재 상태에 따라 환경 설정에 필요한 리소스를 프로비전/프로비전 해제합니다.|
|/labAccounts/labs/environmentSettings/read|환경 설정을 읽습니다.|
|/labAccounts/labs/environmentSettings/write|환경 설정을 추가하거나 수정합니다.|
|/labAccounts/labs/read|랩을 읽습니다.|
|/labAccounts/labs/users/delete|사용자를 삭제합니다.|
|/labAccounts/labs/users/read|사용자를 읽습니다.|
|/labAccounts/labs/users/write|사용자를 추가하거나 수정합니다.|
|/labAccounts/labs/write|랩을 추가 또는 수정합니다.|
|/labAccounts/read|랩 계정을 읽습니다.|
|/labAccounts/write|랩 계정을 추가 또는 수정합니다.|
|/locations/operations/read|읽기 작업|
|/register/action|구독을 등록합니다.|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| 작업 | 설명 |
|---|---|
|/accounts/delete|Location Based Services 계정을 만듭니다.|
|/accounts/listKeys/action|Location Based Services 계정 키를 나열합니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Location Based Services 계정에 사용 가능한 메트릭을 가져옵니다.|
|/accounts/read|Location Based Services 계정을 가져옵니다.|
|/accounts/regenerateKey/action|새 Location Based Services 계정 기본 또는 보조 키를 생성합니다.|
|/accounts/write|Location Based Services 계정을 만들거나 업데이트합니다.|
|/register/action|공급자를 등록합니다.|

## <a name="microsoftlogic"></a>Microsoft.Logic

| 작업 | 설명 |
|---|---|
|/integrationAccounts/agreements/delete|통합 계정에서 규약을 삭제합니다.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|통합 계정의 규약 콘텐츠에 대한 콜백 URL을 가져옵니다.|
|/integrationAccounts/agreements/read|통합 계정에서 규약을 읽습니다.|
|/integrationAccounts/agreements/write|통합 계정에서 규약을 만들거나 업데이트합니다.|
|/integrationAccounts/assemblies/delete|통합 계정에서 어셈블리를 삭제합니다.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|통합 계정의 어셈블리 콘텐츠에 대한 콜백 URL을 가져옵니다.|
|/integrationAccounts/assemblies/read|통합 계정에서 어셈블리를 읽습니다.|
|/integrationAccounts/assemblies/write|통합 계정에서 어셈블리를 만들거나 업데이트합니다.|
|/integrationAccounts/batchConfigurations/delete|통합 계정에서 일괄 처리 구성을 삭제합니다.|
|/integrationAccounts/batchConfigurations/read|통합 계정에서 일괄 처리 구성을 읽습니다.|
|/integrationAccounts/batchConfigurations/write|통합 계정에서 일괄 처리 구성을 만들거나 업데이트합니다.|
|/integrationAccounts/certificates/delete|통합 계정에서 인증서를 삭제합니다.|
|/integrationAccounts/certificates/read|통합 계정에서 인증서를 읽습니다.|
|/integrationAccounts/certificates/write|통합 계정에서 인증서를 만들거나 업데이트합니다.|
|/integrationAccounts/delete|통합 계정을 삭제합니다.|
|/integrationAccounts/listCallbackUrl/action|통합 계정의 콜백 URL을 가져옵니다.|
|/integrationAccounts/listKeyVaultKeys/action|키 자격 증명 모음의 키를 가져옵니다.|
|/integrationAccounts/logTrackingEvents/action|통합 계정의 추적 이벤트를 기록합니다.|
|/integrationAccounts/maps/delete|통합 계정의 맵을 삭제합니다.|
|/integrationAccounts/maps/listContentCallbackUrl/action|통합 계정의 맵 콘텐츠에 대한 콜백 URL을 가져옵니다.|
|/integrationAccounts/maps/read|통합 계정에서 맵을 읽습니다.|
|/integrationAccounts/maps/write|통합 계정에서 맵을 만들거나 업데이트합니다.|
|/integrationAccounts/partners/delete|통합 계정에서 파트너를 삭제합니다.|
|/integrationAccounts/partners/listContentCallbackUrl/action|통합 계정의 파트너 콘텐츠에 대한 콜백 URL을 가져옵니다.|
|/integrationAccounts/partners/read|통합 계정에서 파트너를 읽습니다.|
|/integrationAccounts/partners/write|통합 계정에서 파트너를 만들거나 업데이트합니다.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|통합 계정 로그 정의를 읽습니다.|
|/integrationAccounts/read|통합 계정을 읽습니다.|
|/integrationAccounts/regenerateAccessKey/action|선택키 암호를 다시 생성합니다.|
|/integrationAccounts/schemas/delete|통합 계정에서 스키마를 삭제합니다.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|통합 계정의 스키마 콘텐츠에 대한 콜백 URL을 가져옵니다.|
|/integrationAccounts/schemas/read|통합 계정에서 스키마를 읽습니다.|
|/integrationAccounts/schemas/write|통합 계정에서 스키마를 만들거나 업데이트합니다.|
|/integrationAccounts/sessions/delete|통합 계정에서 세션을 삭제합니다.|
|/integrationAccounts/sessions/read|통합 계정에서 일괄 처리 구성을 읽습니다.|
|/integrationAccounts/sessions/write|통합 계정에서 세션을 만들거나 업데이트합니다.|
|/integrationAccounts/write|통합 계정을 만들거나 업데이트합니다.|
|/locations/workflows/validate/action|워크플로의 유효성을 검사합니다.|
|/operations/read|작업을 가져옵니다.|
|/register/action|지정된 구독에 대한 Microsoft.Logic 리소스 공급자를 등록합니다.|
|/workflows/accessKeys/delete|선택키를 삭제합니다.|
|/workflows/accessKeys/list/action|선택키 암호를 나열합니다.|
|/workflows/accessKeys/read|선택키를 읽습니다.|
|/workflows/accessKeys/regenerate/action|선택키 암호를 다시 생성합니다.|
|/workflows/accessKeys/write|선택키를 만들거나 업데이트합니다.|
|/workflows/delete|워크플로를 삭제합니다.|
|/workflows/disable/action|워크플로를 사용하지 않도록 설정합니다.|
|/workflows/enable/action|워크플로를 사용하도록 설정합니다.|
|/workflows/listCallbackUrl/action|워크플로의 콜백 URL을 가져옵니다.|
|/workflows/listSwagger/action|워크플로 Swagger 정의를 가져옵니다.|
|/workflows/move/action|워크플로를 기존의 구독 ID, 리소스 그룹 및/또는 이름에서 다른 구독 ID, 리소스 그룹 및/또는 이름으로 이동합니다.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|워크플로 진단 설정을 읽습니다.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|워크플로 진단 설정을 만들거나 업데이트합니다.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|워크플로 로그 정의를 읽습니다.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|워크플로 메트릭 정의를 읽습니다.|
|/workflows/read|워크플로를 읽습니다.|
|/workflows/regenerateAccessKey/action|선택키 암호를 다시 생성합니다.|
|/workflows/run/action|워크플로의 실행을 시작합니다.|
|/workflows/runs/actions/listExpressionTraces/action|워크플로 실행 작업 식 추적을 가져옵니다.|
|/workflows/runs/actions/read|워크플로 실행 작업을 읽습니다.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|워크플로 실행 작업 반복 식 추적을 가져옵니다.|
|/workflows/runs/actions/repetitions/read|워크플로 실행 작업 반복을 읽습니다.|
|/workflows/runs/actions/scoperepetitions/read|워크플로 실행 작업 범위 반복을 읽습니다.|
|/workflows/runs/cancel/action|워크플로 실행을 취소합니다.|
|/workflows/runs/operations/read|워크플로 실행 작업 상태를 읽습니다.|
|/workflows/runs/read|워크플로 실행을 읽습니다.|
|/workflows/suspend/action|워크플로를 일시 중단합니다.|
|/workflows/triggers/histories/read|트리거 기록을 읽습니다.|
|/workflows/triggers/histories/resubmit/action|워크플로 트리거를 다시 전송합니다.|
|/workflows/triggers/listCallbackUrl/action|워크플로의 콜백 URL을 가져옵니다.|
|/workflows/triggers/read|트리거를 읽습니다.|
|/workflows/triggers/reset/action|트리거를 다시 설정합니다.|
|/workflows/triggers/run/action|트리거를 실행합니다.|
|/workflows/triggers/setState/action|트리거 상태를 설정합니다.|
|/workflows/validate/action|워크플로의 유효성을 검사합니다.|
|/workflows/versions/read|워크플로 버전을 읽습니다.|
|/workflows/versions/triggers/listCallbackUrl/action|워크플로의 콜백 URL을 가져옵니다.|
|/workflows/write|워크플로를 만들거나 업데이트합니다.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| 작업 | 설명 |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Machine Learning 약정 요금 관계를 이동합니다.|
|/commitmentPlans/commitmentAssociations/read|Machine Learning 약정 요금 관계를 읽습니다.|
|/commitmentPlans/delete|Machine Learning 약정 요금을 삭제합니다.|
|/commitmentPlans/join/action|Machine Learning 약정 요금제에 가입합니다.|
|/commitmentPlans/read|Machine Learning 약정 요금을 읽습니다.|
|/commitmentPlans/write|Machine Learning 약정 요금을 만들거나 업데이트합니다.|
|/locations/operationresults/read|Machine Learning 작업의 결과를 가져옵니다.|
|/locations/operationsstatus/read|진행 중인 Machine Learning 작업의 상태를 가져옵니다.|
|/operations/read|Machine Learning 작업을 가져옵니다.|
|/register/action|Machine Learning 웹 서비스 리소스 공급자에 대한 구독을 등록하고 웹 서비스를 만들도록 설정합니다.|
|/skus/read|Machine Learning 약정 요금제 SKU를 가져옵니다.|
|/webServices/action|지원되는 지역에 대한 국가별 웹 서비스 속성을 만듭니다.|
|/webServices/delete|Machine Learning 웹 서비스를 삭제합니다.|
|/webServices/read|Machine Learning 웹 서비스를 읽습니다.|
|/webServices/write|Machine Learning 웹 서비스를 만들거나 업데이트합니다.|
|/Workspaces/delete|Machine Learning 작업 영역을 삭제합니다.|
|/Workspaces/listworkspacekeys/action|Machine Learning 작업 영역에 대한 키를 나열합니다.|
|/Workspaces/read|Machine Learning 작업 영역을 읽습니다.|
|/Workspaces/resyncstoragekeys/action|Machine Learning 작업 영역에 대해 구성된 저장소 계정의 키를 다시 동기화합니다.|
|/Workspaces/write|Machine Learning 작업 영역을 만들거나 업데이트합니다.|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| 작업 | 설명 |
|---|---|
|/operationalizationClusters/checkUpdate/action|업데이트를 운영화 클러스터용 시스템 서비스에 사용할 수 있는지 확인합니다.|
|/operationalizationClusters/delete|호스팅 계정을 삭제합니다.|
|/operationalizationClusters/listKeys/action|운영화 클러스터와 연결된 키를 나열합니다.|
|/operationalizationClusters/read|호스팅 계정을 읽습니다.|
|/operationalizationClusters/updateSystem/action|운영화 클러스터의 시스템 서비스를 업데이트합니다.|
|/operationalizationClusters/write|호스팅 계정을 만들거나 업데이트합니다.|
|/register/action|리소스 공급자에 대한 구독 ID를 등록하고 기계 학습 계산 리소스를 만들도록 설정합니다.|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| 작업 | 설명 |
|---|---|
|/accounts/delete|호스팅 계정을 삭제합니다.|
|/accounts/read|호스팅 계정을 읽습니다.|
|/accounts/write|호스팅 계정을 만들거나 업데이트합니다.|
|/register/action|리소스 공급자에 대한 구독 ID를 등록하고 호스팅 계정을 만들도록 설정합니다.|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| 작업 | 설명 |
|---|---|
|/userAssignedIdentities/assign/action|기존 사용자 할당 ID를 리소스에 할당하기 위한 RBAC 작업입니다.|
|/userAssignedIdentities/delete|기존 사용자 할당 ID를 삭제합니다.|
|/userAssignedIdentities/read|기존 사용자 할당 ID를 가져옵니다.|
|/userAssignedIdentities/write|새로운 사용자 할당 ID를 만들거나 기존 사용자 할당 ID와 연결된 태그를 업데이트합니다.|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| 작업 | 설명 |
|---|---|
|/labAccounts/CreateLab/action|랩 계정에서 랩을 만듭니다.|
|/labAccounts/delete|랩 계정을 삭제합니다.|
|/labAccounts/labs/delete|랩을 삭제합니다.|
|/labAccounts/labs/environmentSettings/delete|환경 설정을 삭제합니다.|
|/labAccounts/labs/environmentSettings/environments/delete|환경을 삭제합니다.|
|/labAccounts/labs/environmentSettings/environments/read|환경을 읽습니다.|
|/labAccounts/labs/environmentSettings/environments/write|환경을 추가하거나 수정합니다.|
|/labAccounts/labs/environmentSettings/read|환경 설정을 읽습니다.|
|/labAccounts/labs/environmentSettings/write|환경 설정을 추가하거나 수정합니다.|
|/labAccounts/labs/labVms/delete|랩 가상 머신을 삭제합니다.|
|/labAccounts/labs/labVms/read|랩 가상 머신을 읽습니다.|
|/labAccounts/labs/labVms/write|랩 가상 머신을 추가 또는 수정합니다.|
|/labAccounts/labs/read|랩을 읽습니다.|
|/labAccounts/labs/write|랩을 추가 또는 수정합니다.|
|/labAccounts/read|랩 계정을 읽습니다.|
|/labAccounts/write|랩 계정을 추가 또는 수정합니다.|
|/locations/operations/read|읽기 작업|
|/register/action|구독을 등록합니다.|

## <a name="microsoftmanagement"></a>Microsoft.Management

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 관리 그룹 이름이 유효하고 고유한지 확인합니다.|
|/getEntities/action|인증된 사용자에 대한 모든 엔터티(관리 그룹, 구독 등)를 나열합니다.|
|/managementGroups/delete|관리 그룹을 삭제합니다.|
|/managementGroups/read|인증된 사용자의 관리 그룹을 나열합니다.|
|/managementGroups/subscriptions/delete|관리 그룹에서 구독의 연결을 해제합니다.|
|/managementGroups/subscriptions/write|기존 구독을 관리 그룹과 연결합니다.|
|/managementGroups/write|관리 그룹을 만들거나 업데이트합니다.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| 작업 | 설명 |
|---|---|
|/ClassicDevServices/delete|클래식 개발 서비스 리소스에서 DELETE 작업을 수행합니다.|
|/ClassicDevServices/listSecrets/action|클래식 개발 서비스 리소스 관리 키를 가져옵니다.|
|/ClassicDevServices/listSingleSignOnToken/action|클래식 개발자 서비스에 대한 Single Sign-On URL을 가져옵니다.|
|/ClassicDevServices/read|클래식 개발 서비스에서 GET 작업을 수행합니다.|
|/ClassicDevServices/regenerateKey/action|클래식 개발 서비스 리소스 관리 키를 생성합니다.|
|/Operations/read|모든 리소스 종류에 대한 작업을 읽습니다.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| 작업 | 설명 |
|---|---|
|/agreements/offers/plans/cancel/action|지정된 Marketplace 항목에 대한 규약을 취소합니다.|
|/agreements/offers/plans/read|지정된 Marketplace 항목에 대한 규약을 반환합니다.|
|/agreements/offers/plans/sign/action|지정된 Marketplace 항목에 대한 규약에 서명합니다.|
|/agreements/read|지정된 구독의 모든 규약을 반환합니다.|
|/offertypes/publishers/offers/plans/agreements/read|지정된 마켓플레이스 가상 머신 항목에 대한 규약을 가져옵니다.|
|/offertypes/publishers/offers/plans/agreements/write|지정된 마켓플레이스 가상 머신 항목에 대한 규약에 서명하거나 규약을 취소합니다.|

## <a name="microsoftmedia"></a>Microsoft.Media

| 작업 | 설명 |
|---|---|
|/checknameavailability/action|Media Services 계정 이름을 사용할 수 있는지 확인합니다.|
|/mediaservices/delete|Media Services 계정을 삭제합니다.|
|/mediaservices/listKeys/action|Media Services 계정에 대한 ACS 키를 나열합니다.|
|/mediaservices/read|Media Services 계정을 읽습니다.|
|/mediaservices/regenerateKey/action|Media Services ACS 키를 생성합니다.|
|/mediaservices/syncStorageKeys/action|연결된 Azure Storage 계정에 대한 저장소 키를 동기화합니다.|
|/mediaservices/write|Media Services 계정을 만들거나 업데이트합니다.|
|/operations/read|Media Services 계정을 읽습니다.|
|/register/action|Media Services 리소스 공급자에 대한 구독을 등록하고 Media Services 계정을 만들도록 설정합니다.|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| 작업 | 설명 |
|---|---|
|/Operations/read|노출된 작업을 읽습니다.|

## <a name="microsoftnetwork"></a>Microsoft.Network

| 작업 | 설명 |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Application Gateway Ssl 미리 정의된 정책|
|/applicationGatewayAvailableSslOptions/read|Application Gateway에서 사용 가능한 Ssl 옵션|
|/applicationGatewayAvailableWafRuleSets/read|Application Gateway 사용 가능 WAF 규칙 집합을 가져옵니다.|
|/applicationGateways/backendAddressPools/join/action|Application Gateway 백 엔드 주소 풀을 연결합니다.|
|/applicationGateways/backendhealth/action|Application Gateway 백 엔드 상태를 가져옵니다.|
|/applicationGateways/delete|Application Gateway를 삭제합니다.|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Application Gateway에 구성된 경로 테이블을 가져옵니다.|
|/applicationGateways/effectiveRouteTable/action|Application Gateway에 구성된 경로 테이블을 가져옵니다.|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Application Gateway에 대한 이벤트를 가져옵니다.|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Application Gateway에 사용 가능한 메트릭을 가져옵니다.|
|/applicationGateways/read|Application Gateway를 가져옵니다.|
|/applicationGateways/setSecurityCenterConfiguration/action|Application Gateway Security Center 구성을 설정합니다.|
|/applicationGateways/start/action|Application Gateway를 시작합니다.|
|/applicationGateways/stop/action|Application Gateway를 중지합니다.|
|/applicationGateways/write|Application Gateway를 만들거나 Application Gateway를 업데이트합니다.|
|/applicationSecurityGroups/delete|응용 프로그램 보안 그룹을 삭제합니다.|
|/applicationSecurityGroups/joinIpConfiguration/action|IP 구성을 응용 프로그램 보안 그룹에 조인합니다.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|보안 규칙을 응용 프로그램 보안 그룹에 조인합니다.|
|/applicationSecurityGroups/read|응용 프로그램 보안 그룹 ID를 가져옵니다.|
|/applicationSecurityGroups/write|응용 프로그램 보안 그룹을 만들거나 기존 응용 프로그램 보안 그룹을 업데이트합니다.|
|/bgpServiceCommunities/read|BGP 서비스 커뮤니티를 가져옵니다.|
|/checkTrafficManagerNameAvailability/action|Traffic Manager 상대 DNS 이름의 가용성을 확인합니다.|
|/connections/delete|VirtualNetworkGatewayConnection을 삭제합니다.|
|/connections/read|VirtualNetworkGatewayConnection을 가져옵니다.|
|/connections/sharedkey/action|VirtualNetworkGatewayConnection SharedKey를 가져옵니다.|
|/connections/sharedKey/read|VirtualNetworkGatewayConnection SharedKey를 가져옵니다.|
|/connections/sharedKey/write|VirtualNetworkGatewayConnection SharedKey를 만들거나 기존 VirtualNetworkGatewayConnection SharedKey를 업데이트합니다.|
|/connections/vpndeviceconfigurationscript/read|VirtualNetworkGatewayConnection의 Vpn 장치 구성을 가져옵니다.|
|/connections/write|VirtualNetworkGatewayConnection을 만들거나 기존 VirtualNetworkGatewayConnection을 업데이트합니다.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|DDoS 보호 계획 프록시를 삭제합니다.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|DDoS 보호 계획 프록시 정의를 가져옵니다.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|DDoS 보호 계획 프록시를 만들거나 기존 DDoS 보호 계획 프록시를 업데이트합니다.|
|/ddosProtectionPlans/delete|DDoS 보호 계획을 삭제합니다.|
|/ddosProtectionPlans/join/action|DDoS 보호 계획을 조인합니다.|
|/ddosProtectionPlans/read|DDoS 보호 계획을 가져옵니다.|
|/ddosProtectionPlans/write|DDoS 보호 계획을 만들거나 DDoS 보호 계획을 업데이트합니다. |
|/dnsoperationresults/read|DNS 작업의 결과를 가져옵니다.|
|/dnsoperationstatuses/read|DNS 작업의 상태를 가져옵니다. |
|/dnszones/A/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'A' 형식을 제거합니다.|
|/dnszones/A/read|JSON 형식의 'A' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/A/write|DNS 영역 내에서 'A' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/AAAA/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'AAAA' 형식을 제거합니다.|
|/dnszones/AAAA/read|JSON 형식의 'AAAA' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/AAAA/write|DNS 영역 내에서 'AAAA' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/all/read|여러 형식의 DNS 레코드 집합을 가져옵니다.|
|/dnszones/CAA/delete|DNS 영역에서 이름이 지정된 ‘CAA’ 유형의 레코드 집합을 제거합니다.|
|/dnszones/CAA/read|'CAA' 유형의 레코드 집합을 JSON 형식으로 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다.|
|/dnszones/CAA/write|DNS 영역 내에서 'CAA' 유형의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/CNAME/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'CNAME' 형식을 제거합니다.|
|/dnszones/CNAME/read|JSON 형식의 'CNAME' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag가 포함됩니다.|
|/dnszones/CNAME/write|DNS 영역 내에서 'CNAME' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/delete|Json 형식의 DNS 영역을 삭제합니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다.|
|/dnszones/MX/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'MX' 형식을 제거합니다.|
|/dnszones/MX/read|JSON 형식의 'MX' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/MX/write|DNS 영역 내에서 'MX' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/NS/delete|NS 형식의 DNS 레코드 집합을 삭제합니다.|
|/dnszones/NS/read|NS 형식의 DNS 레코드 집합을 가져옵니다.|
|/dnszones/NS/write|NS 형식의 DNS 레코드 집합을 만들거나 업데이트합니다.|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|DNS 영역 진단 설정을 가져옵니다.|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|DNS 영역 진단 설정을 만들거나 업데이트합니다.|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|DNS 영역 메트릭 정의를 가져옵니다.|
|/dnszones/PTR/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'PTR' 형식을 제거합니다.|
|/dnszones/PTR/read|JSON 형식의 'PTR' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/PTR/write|DNS 영역 내에서 'PTR' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/read|Json 형식의 DNS 영역을 가져옵니다. 영역 속성에는 tags, etag, numberOfRecordSets 및 maxNumberOfRecordSets가 포함됩니다. 이 명령은 영역 내에 포함된 레코드 집합을 검색하지 않습니다.|
|/dnszones/recordsets/read|여러 형식의 DNS 레코드 집합을 가져옵니다.|
|/dnszones/SOA/read|SOA 형식의 DNS 레코드 집합을 가져옵니다.|
|/dnszones/SOA/write|SOA 형식의 DNS 레코드 집합을 만들거나 업데이트합니다.|
|/dnszones/SRV/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'SRV' 형식을 제거합니다.|
|/dnszones/SRV/read|JSON 형식의 'SRV' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/SRV/write|SRV 형식의 레코드 집합을 만들거나 업데이트합니다.|
|/dnszones/TXT/delete|DNS 영역에서 지정된 이름의 레코드 집합 및 'TXT' 형식을 제거합니다.|
|/dnszones/TXT/read|JSON 형식의 'TXT' 형식 레코드 집합을 가져옵니다. 레코드 집합에는 TTL, 태그 및 etag 뿐만 아니라 레코드 목록도 포함됩니다.|
|/dnszones/TXT/write|DNS 영역 내에서 'TXT' 형식의 레코드 집합을 만들거나 업데이트합니다. 지정된 레코드는 레코드 집합의 현재 레코드를 대체합니다.|
|/dnszones/write|리소스 그룹 내에 DNS 영역을 만들거나 업데이트합니다.  DNS 영역 리소스에서 태그를 업데이트하는 데 사용됩니다. 이 명령은 영역 내에서 레코드 집합을 만들거나 업데이트하는 데 사용할 수 없습니다.|
|/expressRouteCircuits/authorizations/delete|ExpressRouteCircuit Authorization을 삭제합니다.|
|/expressRouteCircuits/authorizations/read|ExpressRouteCircuit Authorization을 가져옵니다.|
|/expressRouteCircuits/authorizations/write|ExpressRouteCircuit Authorization을 만들거나 기존 ExpressRouteCircuit Authorization을 업데이트합니다.|
|/expressRouteCircuits/delete|ExpressRouteCircuit을 삭제합니다.|
|/expressRouteCircuits/peerings/arpTables/action|ExpressRouteCircuit Peering ArpTable을 가져옵니다.|
|/expressRouteCircuits/peerings/connections/delete|ExpressRouteCircuit 연결을 삭제합니다.|
|/expressRouteCircuits/peerings/connections/read|ExpressRouteCircuit 연결을 가져옵니다.|
|/expressRouteCircuits/peerings/connections/write|ExpressRouteCircuit 연결 리소스를 만들거나 기존 ExpressRouteCircuit 연결 리소스를 업데이트합니다.|
|/expressRouteCircuits/peerings/delete|ExpressRouteCircuit Peering을 삭제합니다.|
|/expressRouteCircuits/peerings/read|ExpressRouteCircuit Peering을 가져옵니다.|
|/expressRouteCircuits/peerings/routeTables/action|ExpressRouteCircuit Peering RouteTable을 가져옵니다.|
|/expressRouteCircuits/peerings/routeTablesSummary/action|ExpressRouteCircuit Peering RouteTable Summary를 가져옵니다.|
|/expressRouteCircuits/peerings/stats/read|ExpressRouteCircuit Peering Stat을 가져옵니다.|
|/expressRouteCircuits/peerings/write|ExpressRouteCircuit Peering을 만들거나 기존 ExpressRouteCircuit Peering을 업데이트합니다.|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|ExpressRoute 회로에 대한 진단 설정을 가져옵니다.|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|ExpressRoute 회로에 대한 진단 설정을 만들거나 업데이트합니다.|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|ExpressRoute 회로에 대한 이벤트를 가져옵니다.|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|ExpressRoute 회로에 대한 메트릭 정의를 가져옵니다.|
|/expressRouteCircuits/read|ExpressRouteCircuit을 가져옵니다.|
|/expressRouteCircuits/stats/read|ExpressRouteCircuit Stat을 가져옵니다.|
|/expressRouteCircuits/write|ExpressRouteCircuit을 만들거나 기존 ExpressRouteCircuit을 업데이트합니다.|
|/expressRouteCrossConnections/delete|Express Route 교차 연결을 삭제합니다.|
|/expressRouteCrossConnections/join/action|Express Route 교차 연결을 조인합니다.|
|/expressRouteCrossConnections/peerings/arpTables/action|Express Route 교차 연결 피어링 Arp 테이블을 가져옵니다.|
|/expressRouteCrossConnections/peerings/delete|Express Route 교차 연결 피어링을 삭제합니다.|
|/expressRouteCrossConnections/peerings/read|Express Route 교차 연결 피어링을 가져옵니다.|
|/expressRouteCrossConnections/peerings/routeTables/action|Express Route 교차 연결 피어링 경로 테이블을 가져옵니다.|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Express Route 교차 연결 피어링 경로 테이블 요약 정보를 가져옵니다.|
|/expressRouteCrossConnections/peerings/stats/read|Express Route 교차 연결 피어링 통계를 가져옵니다.|
|/expressRouteCrossConnections/peerings/write|Express Route 교차 연결 피어링을 만들거나 기존 Express Route 교차 연결 피어링을 업데이트 합니다.|
|/expressRouteCrossConnections/read|Express Route 교차 연결을 가져옵니다.|
|/expressRouteCrossConnections/write|Express Route 교차 연결을 만들거나 업데이트합니다.|
|/expressRouteServiceProviders/read|Express Route 서비스 공급자를 가져옵니다.|
|/loadBalancers/backendAddressPools/join/action|부하 분산 장치 백 엔드 주소 풀을 연결합니다.|
|/loadBalancers/backendAddressPools/read|부하 분산 장치 백 엔드 주소 풀 정의를 가져옵니다.|
|/loadBalancers/delete|부하 분산 장치를 삭제합니다.|
|/loadBalancers/frontendIPConfigurations/read|부하 분산 장치 프런트 엔드 IP 구성 정의를 가져옵니다.|
|/loadBalancers/inboundNatPools/join/action|부하 분산 장치 인바운드 NAT 풀을 연결합니다.|
|/loadBalancers/inboundNatPools/read|부하 분산 장치 인바운드 NAT 풀 정의를 가져옵니다.|
|/loadBalancers/inboundNatRules/delete|부하 분산 장치 인바운드 NAT 규칙을 삭제합니다.|
|/loadBalancers/inboundNatRules/join/action|부하 분산 장치 인바운드 NAT 규칙을 연결합니다.|
|/loadBalancers/inboundNatRules/read|부하 분산 장치 인바운드 NAT 규칙 정의를 가져옵니다.|
|/loadBalancers/inboundNatRules/write|부하 분산 장치 인바운드 NAT 규칙을 만들거나 기존 부하 분산 장치의 인바운드 NAT 규칙을 업데이트합니다.|
|/loadBalancers/loadBalancingRules/read|부하 분산 장치 부하 분산 규칙 정의를 가져옵니다.|
|/loadBalancers/networkInterfaces/read|부하 분산 장치의 모든 네트워크 인터페이스에 대한 참조를 가져옵니다.|
|/loadBalancers/outboundNatRules/read|부하 분산 장치 아웃바운드 NAT 규칙 정의를 가져옵니다.|
|/loadBalancers/probes/join/action|부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다.|
|/loadBalancers/probes/read|부하 분산 장치 프로브를 가져옵니다.|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Load Balancer 진단 설정을 가져옵니다.|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Load Balancer 진단 설정을 만들거나 업데이트합니다.|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Load Balancer에 대한 이벤트를 가져옵니다.|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Load Balancer에 사용 가능한 메트릭을 가져옵니다.|
|/loadBalancers/read|부하 분산 장치 정의를 가져옵니다.|
|/loadBalancers/virtualMachines/read|부하 분산 장치의 모든 가상 머신에 대한 참조를 가져옵니다.|
|/loadBalancers/write|부하 분산 장치를 만들거나 기존 부하 분산 장치를 업데이트합니다.|
|/localnetworkgateways/delete|LocalNetworkGateway를 삭제합니다.|
|/localnetworkgateways/read|LocalNetworkGateway를 가져옵니다.|
|/localnetworkgateways/write|LocalNetworkGateway를 만들거나 기존 LocalNetworkGateway를 업데이트합니다.|
|/locations/checkDnsNameAvailability/read|DNS 레이블을 지정된 위치에서 사용할 수 있는지 확인합니다.|
|/locations/operationResults/read|비동기 POST 또는 DELETE 작업의 작업 결과를 가져옵니다.|
|/locations/operations/read|비동기 작업의 상태를 나타내는 작업 리소스를 가져옵니다.|
|/locations/usages/read|리소스 사용 메트릭을 가져옵니다.|
|/locations/virtualNetworkAvailableEndpointServices/read|사용 가능한 가상 네트워크 엔드포인트 서비스 목록을 가져옵니다.|
|/networkInterfaces/delete|네트워크 인터페이스를 삭제합니다.|
|/networkInterfaces/diagnosticIdentity/read|리소스의 진단 ID를 가져옵니다.|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|VM의 네트워크 인터페이스에 구성된 네트워크 보안 그룹을 가져옵니다.|
|/networkInterfaces/effectiveRouteTable/action|VM의 네트워크 인터페이스에 구성된 경로 테이블을 가져옵니다.|
|/networkInterfaces/ipconfigurations/read|네트워크 인터페이스의 IP 구성 정의를 가져옵니다. |
|/networkInterfaces/join/action|네트워크 인터페이스에 Virtual Machine을 연결합니다.|
|/networkInterfaces/loadBalancers/read|네트워크 인터페이스가 속하는 모든 부하 분산 장치를 가져옵니다.|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|네트워크 인터페이스에 사용 가능한 메트릭을 가져옵니다.|
|/networkInterfaces/read|네트워크 인터페이스 정의를 가져옵니다. |
|/networkInterfaces/write|네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다. |
|/networkSecurityGroups/defaultSecurityRules/read|기본 보안 규칙 정의를 가져옵니다.|
|/networkSecurityGroups/delete|네트워크 보안 그룹을 삭제합니다.|
|/networkSecurityGroups/join/action|네트워크 보안 그룹을 연결합니다.|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|네트워크 보안 그룹 진단 설정을 가져옵니다.|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|네트워크 보안 그룹 진단 설정을 만들거나 업데이트하며, 이 작업은 Insights 리소스 공급자를 통해 보완됩니다.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|네트워크 보안 그룹에 대한 이벤트를 가져옵니다.|
|/networkSecurityGroups/read|네트워크 보안 그룹 정의를 가져옵니다.|
|/networkSecurityGroups/securityRules/delete|보안 규칙을 삭제합니다.|
|/networkSecurityGroups/securityRules/read|보안 규칙 정의를 가져옵니다.|
|/networkSecurityGroups/securityRules/write|보안 규칙을 만들거나 기존 보안 규칙을 업데이트합니다.|
|/networkSecurityGroups/write|네트워크 보안 그룹을 만들거나 기존 네트워크 보안 그룹을 업데이트합니다.|
|/networkWatchers/availableProvidersList/action|지정된 Azure 지역에 사용 가능한 모든 인터넷 서비스 공급자를 반환합니다.|
|/networkWatchers/azureReachabilityReport/action|지정된 위치에서 Azure 지역까지 인터넷 서비스 공급자의 상대 대기 시간을 반환합니다.|
|/networkWatchers/configureFlowLog/action|대상 리소스에 대한 흐름 로깅을 구성합니다.|
|/networkWatchers/connectionMonitors/delete|연결 모니터를 삭제합니다.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|연결 모니터의 진단 설정을 가져옵니다.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|연결 모니터 진단 설정을 만들거나 업데이트합니다.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|연결 모니터에 사용 가능한 메트릭을 가져옵니다.|
|/networkWatchers/connectionMonitors/query/action|지정된 엔드포인트 간의 연결 모니터링을 쿼리합니다.|
|/networkWatchers/connectionMonitors/read|연결 모니터 세부 정보를 가져옵니다.|
|/networkWatchers/connectionMonitors/start/action|지정된 엔드포인트 간의 연결 모니터링을 시작합니다.|
|/networkWatchers/connectionMonitors/stop/action|지정된 엔드포인트 간의 연결 모니터링을 중지/일시 중지합니다.|
|/networkWatchers/connectionMonitors/write|연결 모니터를 만듭니다.|
|/networkWatchers/connectivityCheck/action|가상 머신에서 다른 VM 또는 임의의 원격 서버를 포함한 특정 엔드포인트로 직접 TCP 연결을 설정할 수 있는지 확인합니다.|
|/networkWatchers/delete|Network Watcher를 삭제합니다.|
|/networkWatchers/ipFlowVerify/action|패킷이 특정 대상에서 허용될지 또는 거부될지를 반환합니다.|
|/networkWatchers/lenses/delete|렌즈를 삭제합니다.|
|/networkWatchers/lenses/query/action|지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 쿼리합니다.|
|/networkWatchers/lenses/read|렌즈 세부 정보를 가져옵니다.|
|/networkWatchers/lenses/start/action|지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 시작합니다.|
|/networkWatchers/lenses/stop/action|지정된 엔드포인트에 대한 네트워크 트래픽 모니터링을 중지/일시 중지합니다.|
|/networkWatchers/lenses/write|렌즈를 만듭니다.|
|/networkWatchers/nextHop/action|지정된 목표 및 대상 IP 주소에 대해 다음 홉 형식 및 다음 홉 IP 주소를 반환합니다.|
|/networkWatchers/packetCaptures/delete|패킷 캡처를 삭제합니다.|
|/networkWatchers/packetCaptures/queryStatus/action|패킷 캡처 리소스의 속성 및 상태에 대한 정보를 가져옵니다.|
|/networkWatchers/packetCaptures/read|패킷 캡처 정의를 가져옵니다.|
|/networkWatchers/packetCaptures/stop/action|실행 중인 패킷 캡처 세션을 중지합니다.|
|/networkWatchers/packetCaptures/write|패킷 캡처를 만듭니다.|
|/networkWatchers/queryFlowLogStatus/action|리소스에 대한 흐름 로깅의 상태를 가져옵니다.|
|/networkWatchers/queryTroubleshootResult/action|이전에 실행되었거나 현재 실행 중인 문제 해결 작업의 문제 해결 결과를 가져옵니다.|
|/networkWatchers/read|Network Watcher 정의를 가져옵니다.|
|/networkWatchers/securityGroupView/action|VM에 적용된 유효한 구성된 네트워크 보안 그룹 규칙을 봅니다.|
|/networkWatchers/topology/action|리소스 그룹에 포함된 리소스의 네트워크 수준 보기 및 해당 관계를 가져옵니다.|
|/networkWatchers/troubleshoot/action|Azure의 네트워킹 리소스에 대한 문제 해결을 시작합니다.|
|/networkWatchers/write|Network Watcher를 만들거나 기존 Network Watcher를 업데이트합니다.|
|/operations/read|사용 가능한 작업을 가져옵니다.|
|/publicIPAddresses/delete|공용 IP 주소를 삭제합니다.|
|/publicIPAddresses/join/action|공용 IP 주소를 연결합니다.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|공용 IP 주소의 진단 설정을 가져옵니다.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|공용 IP 주소의 진단 설정을 만들거나 업데이트합니다.|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|공용 IP 주소의 로그 정의를 가져옵니다.|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|공용 IP 주소의 메트릭 정의를 가져옵니다.|
|/publicIPAddresses/read|공용 IP 주소 정의를 가져옵니다.|
|/publicIPAddresses/write|공용 IP 주소를 만들거나 기존 공용 IP 주소를 업데이트합니다. |
|/register/action|구독을 등록합니다.|
|/routeFilters/delete|경로 필터 정의를 삭제합니다.|
|/routeFilters/join/action|경로 필터를 연결합니다.|
|/routeFilters/read|경로 필터 정의를 가져옵니다.|
|/routeFilters/routeFilterRules/delete|경로 필터 규칙 정의를 삭제합니다.|
|/routeFilters/routeFilterRules/read|경로 필터 규칙 정의를 가져옵니다.|
|/routeFilters/routeFilterRules/write|경로 필터 규칙을 만들거나 기존 경로 필터를 규칙을 업데이트합니다.|
|/routeFilters/write|경로 필터를 만들거나 기존 경로 필터를 업데이트합니다.|
|/routeTables/delete|경로 테이블 정의를 삭제합니다.|
|/routeTables/join/action|경로 테이블을 연결합니다.|
|/routeTables/read|경로 테이블 정의를 가져옵니다.|
|/routeTables/routes/delete|경로 정의를 삭제합니다.|
|/routeTables/routes/read|경로 정의를 가져옵니다.|
|/routeTables/routes/write|경로를 만들거나 기존 경로를 업데이트합니다.|
|/routeTables/write|경로 테이블을 만들거나 기존 경로 테이블을 업데이트합니다.|
|/securegateways/applicationRuleCollections/delete|보안 게이트웨이에 대한 응용 프로그램 규칙 컬렉션을 삭제합니다.|
|/securegateways/applicationRuleCollections/read|지정된 보안 게이트웨이에 대한 응용 프로그램 규칙 컬렉션을 검색합니다.|
|/securegateways/applicationRuleCollections/write|보안 게이트웨이에 대한 응용 프로그램 규칙 컬렉션을 만들거나 업데이트합니다.|
|/securegateways/delete|보안 게이트웨이를 삭제합니다.|
|/securegateways/networkRuleCollections/delete|보안 게이트웨이에 대한 네트워크 규칙 컬렉션을 삭제합니다.|
|/securegateways/networkRuleCollections/read|지정된 보안 게이트웨이에 대한 네트워크 규칙 컬렉션을 검색합니다.|
|/securegateways/networkRuleCollections/write|보안 게이트웨이에 대한 네트워크 규칙 컬렉션을 만들거나 업데이트합니다.|
|/securegateways/read|보안 게이트웨이를 가져옵니다.|
|/securegateways/write|보안 게이트웨이를 만들거나 업데이트합니다.|
|/serviceEndpointPolicies/delete|서비스 엔드포인트 정책을 삭제합니다.|
|/serviceEndpointPolicies/join/action|서비스 엔드포인트 정책을 조인합니다.|
|/serviceEndpointPolicies/joinSubnet/action|서브넷을 서비스 엔드포인트 정책에 조인합니다.|
|/serviceEndpointPolicies/read|서비스 엔드포인트 정책 설명을 가져옵니다.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|서비스 엔드포인트 정책 정의를 삭제합니다.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|서비스 엔드포인트 정책 정의 설명을 가져옵니다.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|서비스 엔드포인트 정책 정의를 만들거나 기존 서비스 엔드포인트 정책 정의를 업데이트합니다.|
|/serviceEndpointPolicies/write|서비스 엔드포인트 정책을 만들거나 기존 서비스 엔드포인트 정책을 업데이트합니다.|
|/trafficManagerGeographicHierarchies/read|지리적 트래픽 라우팅 메서드에서 사용할 수 있는 영역을 포함하는 Traffic Manager 지리적 계층 구조를 가져옵니다.|
|/trafficManagerProfiles/azureEndpoints/delete|기존 Traffic Manager 프로필에서 Azure 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 Azure 엔드포인트로의 트래픽 라우팅을 중지합니다.|
|/trafficManagerProfiles/azureEndpoints/read|Azure 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 Azure 엔드포인트를 가져옵니다.|
|/trafficManagerProfiles/azureEndpoints/write|기존 Traffic Manager 프로필에서 새 Azure 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 Azure 엔드포인트의 속성을 업데이트합니다.|
|/trafficManagerProfiles/delete|Traffic Manager 프로필을 삭제합니다. Traffic Manager 프로필과 연결된 모든 설정이 손실되고 프로필은 더 이상 트래픽을 라우팅하는 데 사용할 수 없습니다.|
|/trafficManagerProfiles/externalEndpoints/delete|기존 Traffic Manager 프로필에서 외부 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 외부 엔드포인트로의 트래픽 라우팅을 중지합니다.|
|/trafficManagerProfiles/externalEndpoints/read|해당 외부 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 외부 엔드포인트를 가져옵니다.|
|/trafficManagerProfiles/externalEndpoints/write|기존 Traffic Manager 프로필에서 새 외부 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 외부 엔드포인트의 속성을 업데이트합니다.|
|/trafficManagerProfiles/heatMaps/read|위치 및 소스 IP별 쿼리 수와 대기 시간 데이터를 포함하고 있는 특정 Traffic Manager의 Traffic Manager 열 지도를 가져옵니다.|
|/trafficManagerProfiles/nestedEndpoints/delete|기존 Traffic Manager 프로필에서 중첩 엔드포인트를 삭제합니다. Traffic Manager는 삭제된 중첩 엔드포인트로의 트래픽 라우팅을 중지합니다.|
|/trafficManagerProfiles/nestedEndpoints/read|중첩 엔드포인트의 속성을 포함하여 Traffic Manager 프로필에 속한 중첩 엔드포인트를 가져옵니다.|
|/trafficManagerProfiles/nestedEndpoints/write|기존 Traffic Manager 프로필에서 새 중첩 엔드포인트를 추가하거나 해당 Traffic Manager 프로필에서 기존 중첩 엔드포인트의 속성을 업데이트합니다.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Traffic Manager 진단 설정을 가져옵니다.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Traffic Manager 진단 설정을 만들거나 업데이트하며, 이 작업은 Insights 리소스 공급자를 통해 보완됩니다.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Traffic Manager에 대한 이벤트를 가져옵니다.|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Traffic Manager에 사용 가능한 메트릭을 가져옵니다.|
|/trafficManagerProfiles/read|Traffic Manager 프로필 구성을 가져옵니다. 여기에는 DNS 설정, 트래픽 라우팅 설정, 끝점 모니터링 설정 및 이 Traffic Manager 프로필이 라우팅하는 끝점 목록이 포함됩니다.|
|/trafficManagerProfiles/write|Traffic Manager 프로필을 만들거나 기존 Traffic Manager 프로필의 구성을 수정합니다. 여기에는 프로필을 사용하거나 사용하지 않도록 설정, DNS 설정, 트래픽 라우팅 설정 또는 끝점 모니터링 설정의 수정이 포함됩니다. Traffic Manager 프로필이 라우팅하는 끝점을 추가 및 제거하고, 사용하거나 사용하지 않도록 설정할 수 있습니다.|
|/trafficManagerUserMetricsKeys/delete|실시간 사용자 메트릭 수집에 사용되는 구독 수준 키를 삭제합니다.|
|/trafficManagerUserMetricsKeys/read|실시간 사용자 메트릭 수집에 사용되는 구독 수준 키를 가져옵니다.|
|/trafficManagerUserMetricsKeys/write|실시간 사용자 메트릭 수집에 사용할 새로운 구독 수준 키를 만듭니다.|
|/unregister/action|구독을 등록 취소합니다.|
|/virtualHubs/delete|가상 허브를 삭제합니다.|
|/virtualHubs/hubVirtualNetworkConnections/delete|HubVirtualNetworkConnection을 삭제합니다.|
|/virtualHubs/hubVirtualNetworkConnections/read|HubVirtualNetworkConnection을 가져옵니다.|
|/virtualHubs/hubVirtualNetworkConnections/write|HubVirtualNetworkConnection을 만들거나 업데이트합니다.|
|/virtualHubs/read|가상 허브를 가져옵니다.|
|/virtualHubs/write|가상 허브를 만들거나 업데이트합니다.|
|/virtualnetworkgateways/connections/read|VirtualNetworkGatewayConnection을 가져옵니다.|
|/virtualNetworkGateways/delete|virtualNetworkGateway를 삭제합니다.|
|/virtualnetworkgateways/generatevpnclientpackage/action|VirtualNetworkGateway용 VpnClient 패키지를 생성합니다.|
|/virtualnetworkgateways/generatevpnprofile/action|VirtualNetworkGateway용 VpnProfile 패키지를 생성합니다.|
|/virtualnetworkgateways/getadvertisedroutes/action|virtualNetworkGateway 보급된 경로를 가져옵니다.|
|/virtualnetworkgateways/getbgppeerstatus/action|virtualNetworkGateway bgp 피어 상태를 가져옵니다.|
|/virtualnetworkgateways/getlearnedroutes/action|virtualnetworkgateway 학습한 경로를 가져옵니다.|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient Ipsec 매개 변수를 가져옵니다.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|미리 생성된 vpn 클라이언트 프로필 패키지의 URL을 가져옵니다.|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Virtual Network Gateway 진단 설정을 가져옵니다.|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Virtual Network Gateway 진단 설정을 만들거나 업데이트하며, 이 작업은 Insights 리소스 공급자를 통해 보완됩니다.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Virtual Network Gateway에 대한 이벤트를 가져옵니다.|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Virtual Network Gateway에 사용 가능한 메트릭을 가져옵니다.|
|/virtualNetworkGateways/read|VirtualNetworkGateway를 가져옵니다.|
|/virtualnetworkgateways/reset/action|virtualNetworkGateway를 다시 설정합니다.|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|VirtualNetworkGateway P2S 클라이언트에 대한 Vpnclient Ipsec 매개 변수를 설정합니다.|
|/virtualnetworkgateways/supportedvpndevices/action|지원되는 Vpn 장치를 나열합니다.|
|/virtualNetworkGateways/write|VirtualNetworkGateway를 만들거나 업데이트합니다.|
|/virtualNetworks/checkIpAddressAvailability/read|IP 주소를 지정된 가상 네트워크에서 사용할 수 있는지 확인합니다.|
|/virtualNetworks/customViews/get/action|가상 네트워크 사용자 지정 보기 콘텐츠를 가져옵니다.|
|/virtualNetworks/customViews/read|가상 네트워크의 사용자 지정 보기 정의를 가져옵니다.|
|/virtualNetworks/delete|가상 네트워크를 삭제합니다.|
|/virtualNetworks/peer/action|가상 네트워크를 다른 가상 네트워크와 피어링합니다.|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|가상 네트워크의 진단 설정을 가져옵니다.|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|가상 네트워크의 진단 설정을 만들거나 업데이트합니다.|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|가상 네트워크의 로그 정의를 가져옵니다.|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|가상 네트워크의 메트릭 정의를 가져옵니다.|
|/virtualNetworks/read|가상 네트워크 정의를 가져옵니다.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|가상 네트워크 피어링 프록시를 삭제합니다.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|가상 네트워크 피어링 프록시 정의를 가져옵니다.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|가상 네트워크 피어링 프록시를 만들거나 기존 가상 네트워크 피어링 프록시를 업데이트합니다.|
|/virtualNetworks/subnets/delete|가상 네트워크 서브넷을 삭제합니다.|
|/virtualNetworks/subnets/join/action|가상 네트워크를 연결합니다.|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|저장소 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다.|
|/virtualNetworks/subnets/read|가상 네트워크 서브넷 정의를 가져옵니다.|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|리소스 탐색 링크를 삭제합니다.|
|/virtualNetworks/subnets/resourceNavigationLinks/read|리소스 탐색 링크 정의를 가져옵니다.|
|/virtualNetworks/subnets/resourceNavigationLinks/write|리소스 탐색 링크를 만들거나 기존 리소스 탐색 링크를 업데이트합니다.|
|/virtualNetworks/subnets/virtualMachines/read|가상 네트워크 서브넷의 모든 가상 머신에 대한 참조를 가져옵니다.|
|/virtualNetworks/subnets/write|가상 네트워크 서브넷을 만들거나 기존 가상 네트워크 서브넷을 업데이트합니다.|
|/virtualNetworks/taggedTrafficConsumers/delete|태그가 지정된 트래픽 소비자를 삭제합니다.|
|/virtualNetworks/taggedTrafficConsumers/read|태그가 지정된 트래픽 소비자 정의를 가져옵니다.|
|/virtualNetworks/taggedTrafficConsumers/validate/action|태그가 지정된 트래픽 소비자의 유효성을 검사합니다.|
|/virtualNetworks/taggedTrafficConsumers/write|태그가 지정된 트래픽 소비자를 만들거나 태그가 지정된 기존 트래픽 소비자를 업데이트합니다.|
|/virtualNetworks/usages/read|가상 네트워크의 각 서브넷에 대한 IP 사용량을 가져옵니다.|
|/virtualNetworks/virtualMachines/read|가상 네트워크의 모든 가상 머신에 대한 참조를 가져옵니다.|
|/virtualNetworks/virtualNetworkPeerings/delete|가상 네트워크 피어링을 삭제합니다.|
|/virtualNetworks/virtualNetworkPeerings/read|가상 네트워크 피어링 정의를 가져옵니다.|
|/virtualNetworks/virtualNetworkPeerings/write|가상 네트워크 피어링을 만들거나 기존 가상 네트워크 피어링을 업데이트합니다.|
|/virtualNetworks/write|가상 네트워크를 만들거나 기존 가상 네트워크를 업데이트합니다.|
|/virtualNetworkTaps/delete|가상 네트워크 탭을 삭제합니다.|
|/virtualNetworkTaps/join/action|가상 네트워크 탭을 조인합니다.|
|/virtualNetworkTaps/read|가상 네트워크 탭을 가져옵니다.|
|/virtualNetworkTaps/write|가상 네트워크 탭을 만들거나 업데이트합니다.|
|/virtualwans/delete|가상 Wan을 삭제합니다.|
|/virtualwans/read|가상 Wan을 가져옵니다.|
|/virtualWans/virtualHubProxies/delete|가상 허브를 프록시를 삭제합니다.|
|/virtualWans/virtualHubProxies/read|가상 허브 프록시 정의를 가져옵니다.|
|/virtualWans/virtualHubProxies/write|가상 허브 프록시를 만들거나 가상 허브 프록시를 업데이트합니다.|
|/virtualwans/virtualHubs/read|가상 Wan에 연결된 모든 가상 허브를 가져옵니다.|
|/virtualwans/vpnconfiguration/read|VPN 구성을 가져옵니다.|
|/virtualWans/vpnSiteProxies/delete|Vpn 사이트 프록시를 삭제합니다.|
|/virtualWans/vpnSiteProxies/read|Vpn 사이트 프록시 정의를 가져옵니다.|
|/virtualWans/vpnSiteProxies/write|Vpn 사이트 프록시를 만들거나 Vpn 사이트 프록시를 업데이트합니다.|
|/virtualwans/vpnSites/read|가상 Wan에 연결된 모든 VPN 사이트를 가져옵니다.|
|/virtualwans/write|가상 Wan을 만들거나 업데이트합니다.|
|/vpnGateways/read|VpnGateway를 가져옵니다.|
|/vpnGateways/vpnConnections/read|VpnConnection을 가져옵니다.|
|/vpnGateways/vpnConnections/write|VpnConnection을 씁니다.|
|/vpnGateways/write|VpnGateway를 씁니다.|
|/vpnsites/delete|Vpn 사이트 리소스를 삭제합니다.|
|/vpnsites/read|Vpn 사이트 리소스를 가져옵니다.|
|/vpnsites/write|Vpn 사이트 리소스를 만들거나 업데이트합니다.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| 작업 | 설명 |
|---|---|
|/CheckNamespaceAvailability/action|주어진 네임스페이스 리소스 이름을 NotificationHub 서비스 내에서 사용할 수 있는지 여부를 확인합니다.|
|/Namespaces/authorizationRules/action|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/Namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/Namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/Namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/Namespaces/authorizationRules/regenerateKeys/action|네임스페이스 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요.|
|/Namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/Namespaces/CheckNotificationHubAvailability/action|주어진 NotificationHub 이름을 네임스페이스 내에서 사용할 수 있는지 여부를 확인합니다.|
|/Namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/Namespaces/NotificationHubs/authorizationRules/action|알림 허브 권한 부여 규칙 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/authorizationRules/delete|알림 허브 권한 부여 규칙을 삭제합니다.|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|알림 허브 연결 문자열을 가져옵니다.|
|/Namespaces/NotificationHubs/authorizationRules/read|알림 허브 권한 부여 규칙 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|알림 허브 권한 부여 규칙 다시 생성 Primary/SecondaryKey, 다시 생성할 키를 지정하세요.|
|/Namespaces/NotificationHubs/authorizationRules/write|알림 허브 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/Namespaces/NotificationHubs/debugSend/action|테스트 푸시 알림을 보냅니다.|
|/Namespaces/NotificationHubs/Delete|알림 허브 리소스를 삭제합니다.|
|/Namespaces/NotificationHubs/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/pnsCredentials/action|모든 알림 허브 PNS 자격 증명을 가져옵니다. 여기에는 WNS, MPNS, APNS, GCM 및 Baidu 자격 증명이 포함됩니다.|
|/Namespaces/NotificationHubs/read|알림 허브 리소스 설명 목록을 가져옵니다.|
|/Namespaces/NotificationHubs/write|알림 허브를 만들고 해당 속성을 업데이트합니다. 속성에는 주로 PNS 자격 증명, 권한 부여 규칙 및 TTL이 포함됩니다. 권한 부여 규칙 및 TTL|
|/Namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/Namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다.|
|/register/action|NotifciationHubs 리소스 공급자에 대한 구독을 등록하고 네임스페이스와 NotificationHubs 만들기를 사용하도록 설정합니다.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| 작업 | 설명 |
|---|---|
|/linkTargets/read|Azure 구독과 연결되지 않은 기존 계정 목록을 표시합니다. Azure 구독을 작업 영역에 연결하려면 이 작업에서 반환된 고객 ID를 작업 영역 만들기 작업의 고객 ID 속성에 사용하세요.|
|/register/action|리소스 공급자에 구독을 등록합니다.|
|/workspaces/analytics/query/action|새 엔진을 사용하여 검색합니다.|
|/workspaces/analytics/query/schema/read|검색 스키마 V2를 가져옵니다.|
|/workspaces/api/query/action|새 엔진을 사용하여 검색합니다.|
|/workspaces/api/query/schema/read|검색 스키마 V2를 가져옵니다.|
|/workspaces/configurationScopes/delete|구성 범위를 삭제합니다.|
|/workspaces/configurationScopes/read|구성 범위를 가져옵니다.|
|/workspaces/configurationScopes/write|구성 범위를 설정합니다.|
|/workspaces/datasources/delete|작업 영역의 데이터 원본을 삭제합니다.|
|/workspaces/datasources/read|작업 영역의 데이터 원본을 가져옵니다.|
|/workspaces/datasources/write|작업 영역의 데이터 원본을 생성/업데이트합니다.|
|/workspaces/delete|작업 영역을 삭제합니다. 생성 시 작업 영역이 기존 작업 영역에 연결된 경우 연결된 작업 영역은 삭제되지 않습니다.|
|/workspaces/generateregistrationcertificate/action|작업 영역에 대한 등록 인증서를 생성합니다. 이 인증서는 Microsoft System Center Operation Manager를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/intelligencepacks/disable/action|지정된 작업 영역에 인텔리전스 팩을 사용하지 않도록 설정합니다.|
|/workspaces/intelligencepacks/enable/action|지정된 작업 영역에 인텔리전스 팩을 사용하도록 설정합니다.|
|/workspaces/intelligencepacks/read|지정된 작업 영역에 대해 표시되는 모든 인텔리전스 팩 목록과 해당 작업 영역에 팩을 사용할 수 있는지 여부를 표시합니다.|
|/workspaces/linkedServices/delete|지정된 작업 영역에서 연결된 서비스를 삭제합니다.|
|/workspaces/linkedServices/read|지정된 작업 영역에서 연결된 서비스를 가져옵니다.|
|/workspaces/linkedServices/write|지정된 작업 영역에서 연결된 서비스를 만듭니다/업데이트합니다.|
|/workspaces/listKeys/action|작업 영역에 대한 목록 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/listKeys/read|작업 영역에 대한 목록 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/managementGroups/read|이 작업 영역에 연결된 System Center Operations Manager 관리 그룹의 이름 및 메타데이터를 가져옵니다.|
|/workspaces/metricDefinitions/read|작업 영역의 메트릭 정의를 가져옵니다.|
|/workspaces/notificationSettings/delete|작업 영역에 대한 사용자 알림 설정을 삭제합니다.|
|/workspaces/notificationSettings/read|작업 영역에 대한 사용자 알림 설정을 가져옵니다.|
|/workspaces/notificationSettings/write|작업 영역에 대한 사용자 알림 설정을 지정합니다.|
|/workspaces/purge/action|작업 영역에서 지정된 데이터를 삭제합니다.|
|/workspaces/read|기존 작업 영역을 가져옵니다.|
|/workspaces/savedSearches/delete|저장된 검색 쿼리를 삭제합니다.|
|/workspaces/savedSearches/read|저장된 검색 쿼리를 가져옵니다.|
|/workspaces/savedSearches/write|저장된 검색 쿼리를 만듭니다.|
|/workspaces/schema/read|작업 영역에 대한 검색 스키마를 가져옵니다.  검색 스키마에는 노출된 필드와 필드 형식이 포함되어 있습니다.|
|/workspaces/search/action|검색 쿼리를 실행합니다.|
|/workspaces/sharedKeys/action|작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/sharedKeys/read|작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다.|
|/workspaces/storageinsightconfigs/delete|저장소 구성을 삭제합니다. 이렇게 하면 Microsoft Operational Insights에서 저장소 계정의 데이터 읽기를 중지합니다.|
|/workspaces/storageinsightconfigs/read|저장소 구성을 가져옵니다.|
|/workspaces/storageinsightconfigs/write|새 저장소 구성을 만듭니다. 이러한 구성은 기존 저장소 계정의 위치에서 데이터를 가져오는 데 사용됩니다.|
|/workspaces/usages/read|작업 영역에서 읽는 데이터 양을 포함하여 작업 영역에 대한 사용 현황 데이터를 가져옵니다.|
|/workspaces/write|새 작업 영역을 만들거나 기존 작업 영역의 고객 ID를 제공하여 기존 작업 영역에 연결합니다.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| 작업 | 설명 |
|---|---|
|/managementAssociations/delete|기존 관리 연결을 삭제합니다.|
|/managementAssociations/read|기존 관리 연결을 가져옵니다.|
|/managementAssociations/write|새 관리 연결을 만듭니다.|
|/managementConfigurations/delete|기존 관리 구성을 삭제합니다.|
|/managementConfigurations/read|기존 관리 구성을 가져옵니다.|
|/managementConfigurations/write|새 기존 관리 구성을 만듭니다.|
|/register/action|리소스 공급자에 구독을 등록합니다.|
|/solutions/delete|기존 OMS 솔루션을 삭제합니다.|
|/solutions/read|기존 OMS 솔루션을 가져옵니다.|
|/solutions/write|새 OMS 솔루션을 만듭니다.|

## <a name="microsoftportal"></a>Microsoft.Portal

| 작업 | 설명 |
|---|---|
|/dashboards/delete|구독에서 대시보드를 제거합니다.|
|/dashboards/read|구독에 대한 대시보드를 읽습니다.|
|/dashboards/write|구독에 대해 대시보드를 추가하거나 수정합니다.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| 작업 | 설명 |
|---|---|
|/capacities/checkNameAvailability/action|지정된 Power BI 전용 용량 이름이 올바르고 사용 중이 아닌지 확인합니다.|
|/capacities/delete|Power BI 전용 용량을 삭제합니다.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Power BI 전용 용량에 사용 가능한 메트릭을 가져옵니다.|
|/capacities/read|지정된 Power BI 전용 용량에 대한 정보를 검색합니다.|
|/capacities/write|지정된 Power BI 전용 용량을 만들거나 업데이트합니다.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| 작업 | 설명 |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다.|
|/locations/allocateStamp/action|AllocateStamp는 서비스에서 사용하는 내부 작업입니다.|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Recovery Services 자격 증명 모음의 백업 상태를 반환합니다.|
|/locations/backupValidateFeatures/action|기능의 유효성을 검사합니다.|
|/operations/read|작업에서 리소스 공급자에 대한 작업 목록을 반환합니다.|
|/register/action|지정된 리소스 공급자에 대한 구독을 등록합니다.|
|/Vaults/backupconfig/read|Recovery Services 자격 증명 모음에 구성을 반환합니다.|
|/Vaults/backupconfig/write|Recovery Services 자격 증명 모음에 대한 구성을 업데이트합니다.|
|/Vaults/backupEngines/read|자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|컨테이너의 모든 항목을 가져옵니다.|
|/Vaults/backupFabrics/backupProtectionIntent/write|백업 보호 의도 만들기|
|/Vaults/backupFabrics/operationResults/read|작업의 상태를 반환합니다.|
|/Vaults/backupFabrics/protectableContainers/read|보호 가능한 컨테이너를 모두 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/inquire/action|컨테이너 내의 워크로드를 조회합니다.|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|보호 항목 Backup을 수행합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|보호된 항목을 삭제합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|보호 항목에 대해 수행된 작업의 결과를 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|보호 항목에 대해 수행된 작업의 상태를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|보호된 항목의 개체 정보를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|보호된 항목에 대한 빠른 항목 복구를 프로비전합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|보호 항목의 복구 지점을 가져옵니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|보호 항목의 복구 지점을 복원합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|보호된 항목에 대한 빠른 항목 복구를 취소합니다.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|백업 보호 항목을 만듭니다.|
|/Vaults/backupFabrics/protectionContainers/read|등록된 모든 컨테이너를 반환합니다.|
|/Vaults/backupFabrics/protectionContainers/write|등록된 컨테이너를 만듭니다.|
|/Vaults/backupFabrics/refreshContainers/action|컨테이너 목록을 새로 고칩니다.|
|/Vaults/backupJobs/cancel/action|작업을 취소합니다.|
|/Vaults/backupJobs/operationResults/read|작업의 작업 결과를 반환합니다.|
|/Vaults/backupJobs/read|모든 작업 개체를 반환합니다.|
|/Vaults/backupJobsExport/action|작업을 내보냅니다.|
|/Vaults/backupJobsExport/operationResults/read|내보내기 작업의 작업 결과를 반환합니다.|
|/Vaults/backupManagementMetaData/read|Recovery Services 자격 증명 모음의 Backup 관리 메타데이터를 반환합니다.|
|/Vaults/backupOperationResults/read|Recovery Services 자격 증명 모음의 Backup 작업 결과를 반환합니다.|
|/Vaults/backupOperations/read|Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다.|
|/Vaults/backupPolicies/delete|보호 정책을 삭제합니다.|
|/Vaults/backupPolicies/operationResults/read|정책 작업의 결과를 가져옵니다.|
|/Vaults/backupPolicies/operations/read|정책 작업의 상태를 가져옵니다.|
|/Vaults/backupPolicies/read|모든 보호 정책을 반환합니다.|
|/Vaults/backupPolicies/write|보호 정책을 만듭니다.|
|/Vaults/backupProtectableItems/read|모든 보호 가능한 항목 목록을 반환합니다.|
|/Vaults/backupProtectedItems/read|모든 보호 항목 목록을 반환합니다.|
|/Vaults/backupProtectionContainers/read|구독에 속하는 컨테이너를 모두 반환합니다.|
|/Vaults/backupSecurityPIN/action|Recovery Services 자격 증명 모음에 대한 보안 PIN 정보를 반환합니다.|
|/Vaults/backupstorageconfig/read|Recovery Services 자격 증명 모음에 대한 저장소 구성을 반환합니다.|
|/Vaults/backupstorageconfig/write|Recovery Services 자격 증명 모음에 대한 저장소 구성을 업데이트합니다.|
|/Vaults/backupUsageSummaries/read|Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다.|
|/Vaults/certificates/write|리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다.|
|/Vaults/delete|자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다.|
|/Vaults/extendedInformation/delete|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Vaults/extendedInformation/read|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Vaults/extendedInformation/write|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Vaults/monitoringAlerts/read|Recovery Services 자격 증명 모음에 대한 경고를 받습니다.|
|/Vaults/monitoringAlerts/write|경고를 해결합니다.|
|/Vaults/monitoringConfigurations/read|Recovery Services 자격 증명 모음 알림 구성을 가져옵니다.|
|/Vaults/monitoringConfigurations/write|Recovery Services 자격 증명 모음에 이메일 알림을 구성합니다.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Azure Backup 진단|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Azure Backup 진단|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Azure Backup 로그|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure Backup 메트릭|
|/Vaults/read|자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다.|
|/Vaults/registeredIdentities/delete|컨테이너 등록 취소 작업을 사용하여 컨테이너 등록을 취소할 수 있습니다.|
|/Vaults/registeredIdentities/operationResults/read|작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다.|
|/Vaults/registeredIdentities/read|컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다.|
|/Vaults/registeredIdentities/write|서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다.|
|/vaults/replicationAlertSettings/read|경고 설정을 읽습니다.|
|/vaults/replicationAlertSettings/write|경고 설정을 만들거나 업데이트합니다.|
|/vaults/replicationEvents/read|이벤트를 읽습니다.|
|/vaults/replicationFabrics/checkConsistency/action|패브릭의 일관성을 검사합니다.|
|/vaults/replicationFabrics/delete|패브릭을 삭제합니다.|
|/vaults/replicationFabrics/deployProcessServerImage/action|프로세스 서버 이미지를 배포합니다.|
|/vaults/replicationFabrics/read|패브릭을 읽습니다.|
|/vaults/replicationFabrics/reassociateGateway/action|게이트웨이를 다시 연결합니다.|
|/vaults/replicationFabrics/remove/action|패브릭을 제거합니다.|
|/vaults/replicationFabrics/renewcertificate/action|패브릭용 인증서 갱신|
|/vaults/replicationFabrics/replicationNetworks/read|네트워크를 읽습니다.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|네트워크 매핑을 삭제합니다.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|네트워크 매핑을 읽습니다.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|네트워크 매핑을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|보호 가능한 항목을 검색합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/read|보호 컨테이너를 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|보호 컨테이너를 제거합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|보호 가능한 항목을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|복구 지점을 적용합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|보호된 항목을 삭제합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|장애 조치(Failover) 커밋|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|계획된 장애 조치(Failover)|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|보호된 항목을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|복제 복구 지점을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|보호된 항목을 제거합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|복제를 복구합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|보호된 항목을 다시 보호합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|테스트 장애 조치(Failover)|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|테스트 장애 조치(Failover) 정리|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|장애 조치(failover)|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|모바일 서비스를 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|보호된 항목을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|보호 컨테이너 매핑을 삭제합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|보호 컨테이너 매핑을 읽습니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|보호 컨테이너 매핑을 제거합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|보호 컨테이너 매핑을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|보호 컨테이너를 전환합니다.|
|/vaults/replicationFabrics/replicationProtectionContainers/write|보호 컨테이너를 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Recovery Services 공급자를 삭제합니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Recovery Services 공급자를 읽습니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|공급자를 새로 고칩니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Recovery Services 공급자를 제거합니다.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Recovery Services 공급자를 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/read|저장소 분류를 읽습니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|저장소 분류 매핑을 삭제합니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|저장소 분류 매핑을 읽습니다.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|저장소 분류 매핑을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/replicationvCenters/delete|작업을 삭제합니다.|
|/vaults/replicationFabrics/replicationvCenters/read|작업을 읽습니다.|
|/vaults/replicationFabrics/replicationvCenters/write|작업을 만들거나 업데이트합니다.|
|/vaults/replicationFabrics/write|패브릭을 만들거나 업데이트합니다.|
|/vaults/replicationJobs/cancel/action|작업을 취소합니다.|
|/vaults/replicationJobs/read|작업을 읽습니다.|
|/vaults/replicationJobs/restart/action|작업을 다시 시작합니다.|
|/vaults/replicationJobs/resume/action|작업을 계속합니다.|
|/vaults/replicationPolicies/delete|정책을 삭제합니다.|
|/vaults/replicationPolicies/read|정책을 읽습니다.|
|/vaults/replicationPolicies/write|정책을 만들거나 업데이트합니다.|
|/vaults/replicationRecoveryPlans/delete|복구 계획을 삭제합니다.|
|/vaults/replicationRecoveryPlans/failoverCommit/action|장애 조치(Failover) 커밋 복구 계획|
|/vaults/replicationRecoveryPlans/plannedFailover/action|계획된 장애 조치(Failover) 복구 계획|
|/vaults/replicationRecoveryPlans/read|복구 계획을 읽습니다.|
|/vaults/replicationRecoveryPlans/reProtect/action|복구 계획을 다시 보호합니다.|
|/vaults/replicationRecoveryPlans/testFailover/action|테스트 장애 조치(failover) 복구 계획|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|테스트 장애 조치(failover) 정리 복구 계획|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|장애 조치(Failover) 복구 계획|
|/vaults/replicationRecoveryPlans/write|복구 계획을 만들거나 업데이트합니다.|
|/Vaults/tokenInfo/read|Recovery Services 자격 증명 모음에 대한 토큰 정보를 반환합니다.|
|/vaults/usages/read|자격 증명 모음 사용 현황을 읽습니다.|
|/Vaults/usages/read|Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다.|
|/Vaults/vaultTokens/read|자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다.|
|/Vaults/write|자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다.|

## <a name="microsoftrelay"></a>Microsoft.Relay

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다.|
|/checkNamespaceAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailabiltiy를 대신 사용하세요.|
|/namespaces/authorizationRules/action|네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/namespaces/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다.|
|/namespaces/disasterRecoveryConfigs/delete|네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다.|
|/namespaces/disasterRecoveryConfigs/failover/action|GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다.|
|/namespaces/disasterRecoveryConfigs/read|네임스페이스와 연결된 재해 복구 구성을 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/write|네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다.|
|/namespaces/HybridConnections/authorizationRules/action|HybridConnection을 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요.|
|/namespaces/HybridConnections/authorizationRules/delete|HybridConnection 권한 부여 규칙 삭제 작업|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|HybridConnection 연결 문자열을 가져옵니다.|
|/namespaces/HybridConnections/authorizationRules/read| HybridConnection 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/HybridConnections/authorizationRules/write|HybridConnection 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다.|
|/namespaces/HybridConnections/Delete|HybridConnection 리소스 삭제 작업|
|/namespaces/HybridConnections/read|HybridConnection 리소스 설명의 목록을 가져옵니다.|
|/namespaces/HybridConnections/write|HybridConnection 속성을 만들거나 업데이트합니다.|
|/namespaces/messagingPlan/read|네임스페이스에 대한 메시지 계획을 가져옵니다. 이 API는 더 이상 사용되지 않습니다. MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/messagingPlan/write|네임스페이스에 대한 메시지 계획을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/operationresults/read|네임스페이스 작업의 상태를 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/namespaces/WcfRelays/authorizationRules/action|WcfRelay를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요.|
|/namespaces/WcfRelays/authorizationRules/delete|WcfRelay 권한 부여 규칙 삭제 작업|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|WcfRelay의 연결 문자열을 가져옵니다.|
|/namespaces/WcfRelays/authorizationRules/read| WcfRelay 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/WcfRelays/authorizationRules/write|WcfRelay 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다.|
|/namespaces/WcfRelays/Delete|WcfRelay 리소스 삭제 작업|
|/namespaces/WcfRelays/read|WcfRelay 리소스 설명의 목록을 가져옵니다.|
|/namespaces/WcfRelays/write|WcfRelay 속성을 만들거나 업데이트합니다.|
|/namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다.|
|/operations/read|작업을 가져옵니다.|
|/register/action|릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다.|
|/unregister/action|릴레이 리소스 공급자에 대한 구독을 등록하고 릴레이 리소스를 만들도록 설정합니다.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| 작업 | 설명 |
|---|---|
|/AvailabilityStatuses/current/read|지정된 리소스에 대한 가용성 상태를 가져옵니다.|
|/AvailabilityStatuses/read|지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다.|
|/healthevent/action|지정된 리소스의 상태 변경 사항을 나타냅니다.|
|/healthevent/Activated/action|지정된 리소스의 상태 변경 사항을 나타냅니다.|
|/healthevent/InProgress/action|지정된 리소스의 상태 변경 사항을 나타냅니다.|
|/healthevent/Pending/action|지정된 리소스의 상태 변경 사항을 나타냅니다.|
|/healthevent/Resolved/action|지정된 리소스의 상태 변경 사항을 나타냅니다.|
|/healthevent/Updated/action|지정된 리소스의 상태 변경 사항을 나타냅니다.|
|/register/action|Microsoft ResourceHealth에 대한 구독을 등록합니다.|

## <a name="microsoftresources"></a>Microsoft.Resources

| 작업 | 설명 |
|---|---|
|/checkResourceName/action|리소스 이름의 유효성을 확인합니다.|
|/deployments/cancel/action|배포를 취소합니다.|
|/deployments/delete|배포를 삭제합니다.|
|/deployments/operations/read|배포 작업을 가져오거나 나열합니다.|
|/deployments/read|배포를 가져오거나 나열합니다.|
|/deployments/validate/action|배포의 유효성을 검사합니다.|
|/deployments/write|배포를 만들거나 업데이트합니다.|
|/links/delete|리소스 링크를 삭제합니다.|
|/links/read|리소스 링크를 가져오거나 나열합니다.|
|/links/write|리소스 링크를 만들거나 업데이트합니다.|
|/marketplace/purchase/action|마켓플레이스에서 리소스를 구매합니다.|
|/providers/read|공급자 목록을 가져옵니다.|
|/resources/read|필터를 기준으로 리소스 목록을 가져옵니다.|
|/subscriptions/locations/read|지원되는 위치를 가져오거나 나열합니다.|
|/subscriptions/operationresults/read|구독 작업 결과를 가져옵니다.|
|/subscriptions/providers/read|리소스 공급자를 가져오거나 나열합니다.|
|/subscriptions/read|구독 목록을 가져옵니다.|
|/subscriptions/resourceGroups/delete|리소스 그룹과 해당하는 모든 리소스를 삭제합니다.|
|/subscriptions/resourcegroups/deployments/operations/read|배포 작업을 가져오거나 나열합니다.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|배포 작업 상태를 가져오거나 나열합니다.|
|/subscriptions/resourcegroups/deployments/read|배포를 가져오거나 나열합니다.|
|/subscriptions/resourcegroups/deployments/write|배포를 만들거나 업데이트합니다.|
|/subscriptions/resourceGroups/moveResources/action|한 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동합니다.|
|/subscriptions/resourceGroups/read|리소스 그룹을 가져오거나 나열합니다.|
|/subscriptions/resourcegroups/resources/read|리소스 그룹에 대한 리소스를 가져옵니다.|
|/subscriptions/resourceGroups/validateMoveResources/action|한 리소스 그룹에서 다른 리소스 그룹으로 리소스 이동의 유효성을 검사합니다.|
|/subscriptions/resourceGroups/write|리소스 그룹을 만들거나 업데이트합니다.|
|/subscriptions/resources/read|구독 리소스를 가져옵니다.|
|/subscriptions/tagNames/delete|구독 태그를 삭제합니다.|
|/subscriptions/tagNames/read|구독 태그를 가져오거나 나열합니다.|
|/subscriptions/tagNames/tagValues/delete|구독 태그 값을 삭제합니다.|
|/subscriptions/tagNames/tagValues/read|구독 태그 값을 가져오거나 나열합니다.|
|/subscriptions/tagNames/tagValues/write|구독 태그 값을 추가합니다.|
|/subscriptions/tagNames/write|구독 태그를 추가합니다.|
|/tenants/read|테넌트 목록을 가져옵니다.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| 작업 | 설명 |
|---|---|
|/jobcollections/delete|작업 컬렉션을 삭제합니다.|
|/jobcollections/disable/action|작업 컬렉션을 사용하지 않도록 설정합니다.|
|/jobcollections/enable/action|작업 컬렉션을 사용하도록 설정합니다.|
|/jobcollections/jobs/delete|작업을 삭제합니다.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Scheduler 작업을 기반으로 하여 논리 앱 정의를 생성합니다.|
|/jobcollections/jobs/jobhistories/read|작업 내역을 가져옵니다.|
|/jobcollections/jobs/read|작업을 가져옵니다.|
|/jobcollections/jobs/run/action|작업을 실행합니다.|
|/jobcollections/jobs/write|작업을 만들거나 업데이트합니다.|
|/jobcollections/read|작업 컬렉션을 가져옵니다.|
|/jobcollections/write|작업 컬렉션을 만들거나 업데이트합니다.|

## <a name="microsoftsearch"></a>Microsoft.Search

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|서비스 이름의 가용성을 확인합니다.|
|/register/action|Search 리소스 공급자에 대한 구독을 등록하고 Search 서비스를 만들도록 설정합니다.|
|/searchServices/createQueryKey/action|쿼리 키를 만듭니다.|
|/searchServices/delete|Search 서비스를 삭제합니다.|
|/searchServices/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/searchServices/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/searchServices/listAdminKeys/action|관리 키를 읽습니다.|
|/searchServices/logDefinitions/read|검색 서비스에 사용 가능한 로그를 가져옵니다.|
|/searchServices/metricDefinitions/read|검색 서비스에 사용 가능한 메트릭을 가져옵니다.|
|/searchServices/queryKey/delete|쿼리 키를 삭제합니다.|
|/searchServices/queryKey/read|쿼리 키를 읽습니다.|
|/searchServices/read|Search 서비스를 읽습니다.|
|/searchServices/regenerateAdminKey/action|관리 키를 다시 생성합니다.|
|/searchServices/start/action|Search 서비스를 시작합니다.|
|/searchServices/stop/action|Search 서비스를 중지합니다.|
|/searchServices/write|Search 서비스를 만들거나 업데이트합니다.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| 작업 | 설명 |
|---|---|
|/alerts/read|모든 사용 가능한 보안 경고를 가져옵니다.|
|/applicationWhitelistings/read|응용 프로그램 허용 목록을 가져옵니다.|
|/applicationWhitelistings/write|새 응용 프로그램 허용 목록을 만들거나 기존 응용 프로그램 허용 목록을 업데이트합니다.|
|/complianceResults/read|리소스에 대한 준수 결과를 가져옵니다.|
|/locations/alerts/activate/action|보안 경고를 활성화합니다.|
|/locations/alerts/dismiss/action|보안 경고를 해제합니다.|
|/locations/alerts/read|모든 사용 가능한 보안 경고를 가져옵니다.|
|/locations/jitNetworkAccessPolicies/initiate/action|Just-In-Time 네트워크 액세스 정책을 시작합니다.|
|/locations/jitNetworkAccessPolicies/read|Just-In-Time 네트워크 액세스 정책을 가져옵니다.|
|/locations/jitNetworkAccessPolicies/write|새 Just-In-Time 네트워크 액세스 정책을 만들거나 기존 Just-In-Time 네트워크 액세스 정책을 업데이트합니다.|
|/locations/read|보안 데이터 위치를 가져옵니다.|
|/locations/tasks/activate/action|보안 권장 사항을 활성화합니다.|
|/locations/tasks/dismiss/action|보안 권장 사항을 해제합니다.|
|/locations/tasks/read|모든 사용 가능한 보안 권장 사항을 가져옵니다.|
|/locations/tasks/resolve/action|보안 권장 사항을 해결합니다.|
|/locations/tasks/start/action|보안 권장 사항을 시작합니다.|
|/policies/read|보안 규칙을 가져옵니다.|
|/policies/write|보안 규칙을 업데이트합니다.|
|/pricings/delete|범위에 대한 가격 책정 설정을 삭제합니다.|
|/pricings/read|범위에 대한 가격 책정 설정을 가져옵니다.|
|/pricings/write|범위에 대한 가격 책정 설정을 업데이트합니다.|
|/register/action|Azure Security Center에 대한 구독을 등록합니다.|
|/securityContacts/delete|보안 연락처를 삭제합니다.|
|/securityContacts/read|보안 연락처를 가져옵니다.|
|/securityContacts/write|보안 연락처를 업데이트합니다.|
|/securitySolutions/delete|보안 솔루션을 삭제합니다.|
|/securitySolutions/read|보안 솔루션을 가져옵니다.|
|/securitySolutions/write|새 보안 솔루션을 만들거나 기존 보안 솔루션을 업데이트합니다.|
|/securitySolutionsReferenceData/read|보안 솔루션 참조 데이터를 가져옵니다.|
|/securityStatuses/read|Azure 리소스의 보안 상태를 가져옵니다.|
|/securityStatusesSummaries/read|범위에 대한 보안 상태 요약 정보를 가져옵니다.|
|/tasks/read|모든 사용 가능한 보안 권장 사항을 가져옵니다.|
|/webApplicationFirewalls/delete|웹 응용 프로그램 방화벽을 삭제합니다.|
|/webApplicationFirewalls/read|웹 응용 프로그램 방화벽을 가져옵니다.|
|/webApplicationFirewalls/write|새 웹 응용 프로그램 방화벽을 만들거나 기존 웹 응용 프로그램 방화벽을 업데이트합니다.|
|/workspaceSettings/connect/action|작업 영역 설정 재연결 설정을 변경합니다.|
|/workspaceSettings/delete|작업 영역 설정을 삭제합니다.|
|/workspaceSettings/read|작업 영역 설정을 가져옵니다.|
|/workspaceSettings/write|작업 영역 설정을 업데이트합니다.|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다.|
|/checkNamespaceAvailability/action|지정된 구독에서 네임스페이스의 가용성을 확인합니다. 이 API는 사용되지 않으므로 CheckNameAvailabiltiy를 대신 사용하세요.|
|/namespaces/authorizationRules/action|네임스페이스 권한 부여 규칙을 업데이트합니다. 이 API는 사용되지 않습니다. 그 대신 PUT 호출을 사용하여 네임스페이스 권한 부여 규칙을 업데이트하세요. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/authorizationRules/delete|네임스페이스 권한 부여 규칙을 삭제합니다. 기본 네임스페이스 권한 부여 규칙은 삭제할 수 없습니다. |
|/namespaces/authorizationRules/listkeys/action|네임스페이스 연결 문자열을 가져옵니다.|
|/namespaces/authorizationRules/read|네임스페이스 권한 부여 규칙 설명 목록을 가져옵니다.|
|/namespaces/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/authorizationRules/write|네임스페이스 수준 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한, 기본 키 및 보조 키를 업데이트할 수 있습니다.|
|/namespaces/Delete|네임스페이스 리소스를 삭제합니다.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|재해 복구 기본 네임스페이스의 권한 부여 규칙 키를 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|재해 복구 기본 네임스페이스의 권한 부여 규칙을 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|재해 복구를 사용하지 않도록 설정하고 기본 네임스페이스에서 보조 네임스페이스로의 변경 복제를 중지합니다.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|지정된 구독에서 네임스페이스 별칭의 가용성을 확인합니다.|
|/namespaces/disasterRecoveryConfigs/delete|네임스페이스와 연결된 재해 복구 구성을 삭제합니다. 이 작업은 기본 네임스페이스를 통해서만 호출할 수 있습니다.|
|/namespaces/disasterRecoveryConfigs/failover/action|GEO DR 장애 조치(failover)를 호출하고, 네임스페이스 별칭이 보조 네임스페이스를 가리키도록 다시 구성합니다.|
|/namespaces/disasterRecoveryConfigs/read|네임스페이스와 연결된 재해 복구 구성을 가져옵니다.|
|/namespaces/disasterRecoveryConfigs/write|네임스페이스와 연결된 재해 복구 구성을 만들거나 업데이트합니다.|
|/namespaces/eventGridFilters/delete|네임스페이스와 연결된 Event Grid 필터를 삭제합니다.|
|/namespaces/eventGridFilters/read|네임스페이스와 연결된 Event Grid 필터를 가져옵니다.|
|/namespaces/eventGridFilters/write|네임스페이스와 연결된 Event Grid 필터를 만들거나 업데이트합니다.|
|/namespaces/eventhubs/read|EventHub 리소스 설명의 목록을 가져옵니다.|
|/namespaces/messagingPlan/read|네임스페이스에 대한 메시지 계획을 가져옵니다. 이 API는 더 이상 사용되지 않습니다. MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/messagingPlan/write|네임스페이스에 대한 메시지 계획을 업데이트합니다. 이 API는 더 이상 사용되지 않습니다. MessagingPlan 리소스를 통해 노출된 속성은 이후 API 버전의 (부모) 네임스페이스 리소스로 이동됩니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다.|
|/namespaces/migrate/action|네임스페이스 작업을 마이그레이션합니다.|
|/namespaces/operationresults/read|네임스페이스 작업의 상태를 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|네임스페이스 진단 설정 리소스 설명의 목록을 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|네임스페이스 로그 리소스 설명의 목록을 가져옵니다.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|네임스페이스 메트릭 리소스 설명의 목록을 가져옵니다.|
|/namespaces/queues/authorizationRules/action|큐를 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요.|
|/namespaces/queues/authorizationRules/delete|큐 권한 부여 규칙 삭제 작업|
|/namespaces/queues/authorizationRules/listkeys/action|큐의 연결 문자열을 가져옵니다.|
|/namespaces/queues/authorizationRules/read| 큐 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/queues/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/queues/authorizationRules/write|큐 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다.|
|/namespaces/queues/Delete|큐 리소스 삭제 작업|
|/namespaces/queues/read|큐 리소스 설명의 목록을 가져옵니다.|
|/namespaces/queues/write|큐 속성을 만들거나 업데이트합니다.|
|/namespaces/read|네임스페이스 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/authorizationRules/action|토픽을 업데이트하는 작업입니다. 이 작업은 API 버전 2017-04-01에서 지원되지 않습니다. 권한 부여 규칙. PUT 호출을 사용하여 권한 부여 규칙을 업데이트하세요.|
|/namespaces/topics/authorizationRules/delete|토픽 권한 부여 규칙 삭제 작업|
|/namespaces/topics/authorizationRules/listkeys/action|토픽의 연결 문자열을 가져옵니다.|
|/namespaces/topics/authorizationRules/read| 토픽 권한 부여 규칙 목록을 가져옵니다.|
|/namespaces/topics/authorizationRules/regenerateKeys/action|리소스에 대한 기본 또는 보조 키를 다시 생성합니다.|
|/namespaces/topics/authorizationRules/write|토픽 권한 부여 규칙을 만들고 해당 속성을 업데이트합니다. 권한 부여 규칙 액세스 권한을 업데이트할 수 있습니다.|
|/namespaces/topics/Delete|토픽 리소스 삭제 작업|
|/namespaces/topics/read|토픽 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/subscriptions/Delete|TopicSubscription 리소스 삭제 작업|
|/namespaces/topics/subscriptions/read|TopicSubscription 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/subscriptions/rules/Delete|규칙 리소스 삭제 작업|
|/namespaces/topics/subscriptions/rules/read|규칙 리소스 설명의 목록을 가져옵니다.|
|/namespaces/topics/subscriptions/rules/write|규칙 속성을 만들거나 업데이트합니다.|
|/namespaces/topics/subscriptions/write|TopicSubscription 속성을 만들거나 업데이트합니다.|
|/namespaces/topics/write|토픽 속성을 만들거나 업데이트합니다.|
|/namespaces/write|네임스페이스 리소스를 만들고 해당 속성을 업데이트합니다. 네임스페이스의 태그 및 용량은 업데이트할 수 있는 속성입니다.|
|/operations/read|작업을 가져옵니다.|
|/register/action|ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다.|
|/sku/read|Sku 리소스 설명의 목록을 가져옵니다.|
|/sku/regions/read|SkuRegions 리소스 설명의 목록을 가져옵니다.|
|/unregister/action|ServiceBus 리소스 공급자에 대한 구독을 등록하고 ServiceBus 리소스를 만들도록 설정합니다.|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| 작업 | 설명 |
|---|---|
|/clusters/applications/delete|응용 프로그램을 삭제합니다.|
|/clusters/applications/read|응용 프로그램을 읽습니다.|
|/clusters/applications/services/delete|서비스를 삭제합니다.|
|/clusters/applications/services/partitions/read|파티션을 읽습니다.|
|/clusters/applications/services/partitions/replicas/read|복제본을 읽습니다.|
|/clusters/applications/services/read|서비스를 읽습니다.|
|/clusters/applications/services/statuses/read|서비스 상태를 읽습니다.|
|/clusters/applications/services/write|서비스를 만들거나 업데이트합니다.|
|/clusters/applications/write|응용 프로그램을 만들거나 업데이트합니다.|
|/clusters/applicationTypes/delete|응용 프로그램 유형을 삭제합니다.|
|/clusters/applicationTypes/read|응용 프로그램 유형을 읽습니다.|
|/clusters/applicationTypes/versions/delete|응용 프로그램 유형 버전을 삭제합니다.|
|/clusters/applicationTypes/versions/read|응용 프로그램 유형 버전을 읽습니다.|
|/clusters/applicationTypes/versions/write|응용 프로그램 유형 버전을 만들거나 업데이트합니다.|
|/clusters/applicationTypes/write|응용 프로그램 유형을 만들거나 업데이트합니다.|
|/clusters/delete|클러스터를 삭제합니다.|
|/clusters/nodes/read|노드를 읽습니다.|
|/clusters/read|클러스터를 읽습니다.|
|/clusters/statuses/read|클러스터 상태를 읽습니다.|
|/clusters/write|클러스터를 만들거나 업데이트합니다.|
|/locations/clusterVersions/read|클러스터 버전을 읽습니다.|
|/locations/environments/clusterVersions/read|특정 환경에 대한 클러스터 버전을 읽습니다.|
|/locations/operationresults/read|작업 결과를 읽습니다.|
|/locations/operations/read|위치별로 작업을 읽습니다.|
|/operations/read|사용 가능한 작업을 읽습니다.|
|/register/action|작업을 등록합니다.|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| 작업 | 설명 |
|---|---|
|/applicationDefinitions/delete|응용 프로그램 정의를 제거합니다.|
|/applicationDefinitions/read|응용 프로그램 정의 목록을 검색합니다.|
|/applicationDefinitions/write|응용 프로그램 정의를 추가 또는 수정합니다.|
|/applications/delete|응용 프로그램을 제거합니다.|
|/applications/read|응용 프로그램 목록을 검색합니다.|
|/applications/write|응용 프로그램을 만듭니다.|
|/locations/operationStatuses/read|리소스의 작업 상태를 읽습니다.|
|/register/action|솔루션에 등록합니다.|

## <a name="microsoftsql"></a>Microsoft.Sql

| 작업 | 설명 |
|---|---|
|/checkNameAvailability/action|지정된 서버 이름을 지정된 구독에 대해 전 세계적으로 프로비전에 사용할 수 있는지 확인합니다.|
|/locations/auditingSettingsAzureAsyncOperation/read|확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다.|
|/locations/auditingSettingsOperationResults/read|서버 blob 감사 정책 집합 작업의 결과를 검색합니다.|
|/locations/capabilities/read|지정된 위치에서 이 구독에 대한 기능을 가져옵니다.|
|/locations/databaseAzureAsyncOperation/read|데이터베이스 작업의 상태를 가져옵니다.|
|/locations/databaseOperationResults/read|데이터베이스 작업의 상태를 가져옵니다.|
|/locations/deletedServerAsyncOperation/read|삭제된 서버에서 진행 중인 작업을 가져옵니다.|
|/locations/deletedServerOperationResults/read|삭제된 서버에서 진행 중인 작업을 가져옵니다.|
|/locations/deletedServers/read|삭제된 서버 목록을 반환하거나 지정된 서버에 대한 속성을 가져옵니다.|
|/locations/deletedServers/recover/action|삭제된 서버를 복구합니다.|
|/locations/deleteVirtualNetworkOrSubnets/action|가상 네트워크 또는 서브넷에 연결된 가상 네트워크 규칙을 삭제합니다.|
|/locations/elasticPoolAzureAsyncOperation/read|탄력적 풀 비동기 작업에 대한 azure 비동기 작업을 가져옵니다.|
|/locations/elasticPoolOperationResults/read|탄력적 풀 작업의 결과를 가져옵니다.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다.|
|/locations/extendedAuditingSettingsOperationResults/read|확장된 서버 blob 감사 정책 집합 작업의 결과를 검색합니다.|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|관리되는 데이터베이스 복원 작업을 완료합니다.|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|관리되는 데이터베이스 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다.|
|/locations/managedTransparentDataEncryptionOperationResults/read|관리되는 데이터베이스 투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다.|
|/locations/read|지정된 구독에 사용 가능한 위치를 가져옵니다.|
|/locations/syncAgentOperationResults/read|동기화 에이전트 리소스 작업의 결과를 검색합니다.|
|/locations/syncDatabaseIds/read|특정 지역 및 구독에 대한 동기화 데이터베이스 id를 검색합니다.|
|/locations/syncGroupOperationResults/read|동기화 그룹 리소스 작업의 결과를 검색합니다.|
|/locations/syncMemberOperationResults/read|동기화 멤버 리소스 작업의 결과를 검색합니다.|
|/locations/usages/read|한 위치에서 이 구독의 사용량 메트릭 컬렉션을 가져옵니다.|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|지정된 가상 네트워크 규칙 azure 비동기 작업의 세부 정보를 반환합니다. |
|/locations/virtualNetworkRulesOperationResults/read|지정된 가상 네트워크 규칙 작업의 세부 정보를 반환합니다. |
|/managedInstances/administrators/delete|관리되는 인스턴스의 기존 관리자를 삭제합니다.|
|/managedInstances/administrators/read|관리되는 인스턴스 관리자 목록을 가져옵니다.|
|/managedInstances/administrators/write|지정된 매개 변수를 사용하여 관리되는 인스턴스 관리자를 만들거나 업데이트합니다.|
|/managedInstances/databases/delete|기존 관리되는 데이터베이스를 삭제합니다.|
|/managedInstances/databases/read|기존 관리되는 데이터베이스를 가져옵니다.|
|/managedInstances/databases/securityAlertPolicies/read|지정된 관리되는 데이터베이스에 구성된 데이터베이스 위협 검색 정책의 세부 정보를 검색합니다.|
|/managedInstances/databases/securityAlertPolicies/write|지정된 관리되는 데이터베이스에 대한 데이터베이스 위협 검색 정책을 변경합니다.|
|/managedInstances/databases/securityEvents/read|관리되는 데이터베이스 보안 이벤트를 검색합니다.|
|/managedInstances/databases/transparentDataEncryption/read|지정된 관리되는 데이터베이스에서 데이터베이스 투명한 데이터 암호화의 세부 정보를 검색합니다.|
|/managedInstances/databases/transparentDataEncryption/write|지정된 관리되는 데이터베이스에 대한 데이터베이스 투명한 데이터 암호화를 변경합니다.|
|/managedInstances/databases/write|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|/managedInstances/delete|기존 관리되는 인스턴스를 삭제합니다.|
|/managedInstances/metricDefinitions/read|관리되는 인스턴스 메트릭 정의를 가져옵니다.|
|/managedInstances/metrics/read|관리되는 인스턴스 메트릭을 가져옵니다.|
|/managedInstances/read|관리되는 인스턴스 목록을 반환하거나 지정된 관리되는 인스턴스에 대한 속성을 가져옵니다.|
|/managedInstances/securityAlertPolicies/read|지정된 관리되는 서버에 구성된 관리되는 서버 위협 검색 정책의 세부 정보를 검색합니다.|
|/managedInstances/securityAlertPolicies/write|지정된 관리되는 서버에 대한 관리되는 서버 위협 검색 정책을 변경합니다.|
|/managedInstances/write|지정된 매개 변수를 사용하여 관리되는 인스턴스를 만들거나 지정된 관리되는 인스턴스에 대한 속성 또는 태그를 업데이트합니다.|
|/operations/read|사용 가능한 REST 작업을 가져옵니다.|
|/register/action|Microsoft SQL Database 리소스 공급자에 대한 구독을 등록하고 Microsoft SQL Databases를 만들도록 설정합니다.|
|/servers/administratorOperationResults/read|서버 관리자에 대해 진행 중인 작업을 가져옵니다.|
|/servers/administrators/delete|서버 관리자를 삭제합니다.|
|/servers/administrators/read|서버 관리자의 세부 정보를 검색합니다.|
|/servers/administrators/write|서버 관리자를 만들거나 업데이트합니다.|
|/servers/advisors/read|서버에 사용할 수 있는 Advisor의 목록을 반환합니다.|
|/servers/advisors/recommendedActions/read|서버에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다.|
|/servers/advisors/recommendedActions/write|서버에 권장 작업을 적용합니다.|
|/servers/advisors/write|서버 수준에서 Advisor의 자동 실행 상태를 업데이트합니다.|
|/servers/auditingPolicies/read|지정된 서버에 구성된 기본 서버 테이블 감사 정책의 세부 정보를 검색합니다.|
|/servers/auditingPolicies/write|지정된 서버에 대한 기본 서버 테이블 감사를 변경합니다.|
|/servers/auditingSettings/operationResults/read|서버 blob 감사 정책 집합 작업의 결과를 검색합니다.|
|/servers/auditingSettings/read|지정된 서버에 구성된 서버 Blob 감사 정책의 세부 정보를 검색합니다.|
|/servers/auditingSettings/write|지정된 서버에 대한 서버 Blob 감사를 변경합니다.|
|/servers/automaticTuning/read|서버에 대한 자동 조정 설정을 반환합니다.|
|/servers/automaticTuning/write|서버에 대한 자동 조정 설정을 업데이트하고 업데이트된 설정을 반환합니다.|
|/servers/backupLongTermRetentionVaults/delete|기존 백업 보관 자격 증명 모음을 삭제합니다.|
|/servers/backupLongTermRetentionVaults/read|이 작업은 백업 장기 보존 자격 증명 모음을 가져오는 데 사용됩니다. 서버에 등록된 자격 증명 모음에 대한 정보를 반환합니다.|
|/servers/backupLongTermRetentionVaults/write|이 작업은 백업 장기 보존 자격 증명 모음을 서버에 등록하는 데 사용됩니다.|
|/servers/communicationLinks/delete|기존 서버 통신 연결을 삭제합니다.|
|/servers/communicationLinks/read|지정된 서버의 통신 연결 목록을 반환합니다.|
|/servers/communicationLinks/write|서버 통신 연결을 만들거나 업데이트합니다.|
|/servers/connectionPolicies/read|지정된 서버의 서버 연결 정책 목록을 반환합니다.|
|/servers/connectionPolicies/write|서버 연결 정책을 만들거나 업데이트합니다.|
|/servers/databases/advisors/read|데이터베이스에 사용할 수 있는 Advisor의 목록을 반환합니다.|
|/servers/databases/advisors/recommendedActions/read|데이터베이스에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다.|
|/servers/databases/advisors/recommendedActions/write|데이터베이스에 권장 작업을 적용합니다.|
|/servers/databases/advisors/write|데이터베이스 수준에서 Advisor의 자동 실행 상태를 업데이트합니다.|
|/servers/databases/auditingPolicies/read|지정된 데이터베이스에 구성된 테이블 감사 정책의 세부 정보를 검색합니다.|
|/servers/databases/auditingPolicies/write|지정된 데이터베이스에 대한 테이블 감사 정책을 변경합니다.|
|/servers/databases/auditingSettings/read|지정된 데이터베이스에 구성된 Blob 감사 정책의 세부 정보를 검색합니다.|
|/servers/databases/auditingSettings/write|지정된 데이터베이스에 대한 Blob 감사 정책을 변경합니다.|
|/servers/databases/auditRecords/read|데이터베이스 blob 감사 레코드를 검색합니다.|
|/servers/databases/automaticTuning/read|데이터베이스에 대한 자동 조정 설정을 반환합니다.|
|/servers/databases/automaticTuning/write|데이터베이스에 대한 자동 조정 설정을 업데이트하고 업데이트된 설정을 반환합니다.|
|/servers/databases/azureAsyncOperation/read|데이터베이스 작업의 상태를 가져옵니다.|
|/servers/databases/backupLongTermRetentionPolicies/read|지정된 데이터베이스의 백업 보관 정책 목록을 반환합니다.|
|/servers/databases/backupLongTermRetentionPolicies/write|데이터베이스 백업 보관 정책을 만들거나 업데이트합니다.|
|/servers/databases/connectionPolicies/read|지정된 데이터베이스에 구성된 연결 정책의 세부 정보를 검색합니다.|
|/servers/databases/connectionPolicies/write|지정된 데이터베이스에 대한 연결 정책을 변경합니다.|
|/servers/databases/dataMaskingPolicies/read|데이터베이스 데이터 마스킹 정책 목록을 반환합니다.|
|/servers/databases/dataMaskingPolicies/rules/delete|지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 삭제합니다.|
|/servers/databases/dataMaskingPolicies/rules/read|지정된 데이터베이스에 구성된 데이터 마스킹 정책 규칙의 세부 정보를 검색합니다.|
|/servers/databases/dataMaskingPolicies/rules/write|지정된 데이터베이스에 대한 데이터 마스킹 정책 규칙을 변경합니다.|
|/servers/databases/dataMaskingPolicies/write|지정된 데이터베이스에 대한 데이터 마스킹 정책을 변경합니다.|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|선택한 단계 ID에 대한 데이터 웨어하우스 쿼리의 분산 쿼리 단계 정보를 반환합니다.|
|/servers/databases/dataWarehouseQueries/read|선택한 쿼리 ID에 대한 데이터 웨어하우스 배포 쿼리 정보를 반환합니다.|
|/servers/databases/dataWarehouseUserActivities/read|실행 중인 쿼리와 일시 중단된 쿼리를 포함하고 있는 SQL Data Warehouse 인스턴스의 사용자 작업을 검색합니다.|
|/servers/databases/delete|기존 데이터베이스를 삭제합니다.|
|/servers/databases/export/action|Azure SQL Database를 내보냅니다.|
|/servers/databases/extendedAuditingSettings/read|지정된 데이터베이스에 구성된 확장 Blob 감사 정책의 세부 정보를 검색합니다.|
|/servers/databases/extendedAuditingSettings/write|지정된 데이터베이스에 대한 확장 Blob 감사 정책을 변경합니다.|
|/servers/databases/extensions/read|데이터베이스에 대한 확장 컬렉션을 가져옵니다.|
|/servers/databases/extensions/write|지정된 데이터베이스에 대한 확장을 변경합니다.|
|/servers/databases/geoBackupPolicies/read|지정된 데이터베이스에 대한 지역 백업 정책을 검색합니다.|
|/servers/databases/geoBackupPolicies/write|데이터베이스 지역 백업 정책을 만들거나 업데이트합니다.|
|/servers/databases/importExportOperationResults/read|진행 중인 가져오기/내보내기 작업을 가져옵니다.|
|/servers/databases/metricDefinitions/read|데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/databases/metrics/read|데이터베이스에 대한 메트릭을 반환합니다.|
|/servers/databases/move/action|Azure SQL Database 이름을 바꿉니다.|
|/servers/databases/operationResults/read|데이터베이스 작업의 상태를 가져옵니다.|
|/servers/databases/operations/cancel/action|아직 완료되지 않은 Azure SQL Database 보류 중인 비동기 작업을 취소합니다.|
|/servers/databases/operations/read|데이터베이스에서 수행된 작업 목록을 반환합니다.|
|/servers/databases/pause/action|Azure SQL Datawarehouse 데이터베이스를 일시 중지합니다.|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|데이터베이스에 대해 사용 가능한 로그를 가져옵니다.|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|데이터베이스에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/databases/queryStore/queryTexts/read|지정된 매개 변수에 해당하는 쿼리 텍스트 컬렉션을 반환합니다.|
|/servers/databases/queryStore/read|데이터베이스에 대한 쿼리 저장소 설정의 현재 값을 반환합니다.|
|/servers/databases/queryStore/write|데이터베이스에 대한 쿼리 저장소 설정을 업데이트합니다.|
|/servers/databases/read|데이터베이스 목록을 가져오거나 지정된 데이터베이스에 대한 속성을 가져옵니다.|
|/servers/databases/replicationLinks/delete|잠재적 데이터 손실이 발생하는 상태로 강제로 복제 관계를 종료합니다.|
|/servers/databases/replicationLinks/failover/action|주 데이터베이스의 모든 변경 내용을 동기화한 후에 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다.|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|잠재적 데이터 손실이 발생하는 상태로 즉시 장애 조치(failover)하고, 이 데이터베이스를 복제 관계의 주 데이터베이스로 만들고, 원격 주 데이터베이스를 보조 데이터베이스로 만듭니다.|
|/servers/databases/replicationLinks/read|특정 데이터베이스에 설정된 복제 링크에 대한 세부 정보를 반환합니다.|
|/servers/databases/replicationLinks/unlink/action|강제로 또는 파트너와 동기화한 후에 복제 관계를 종료합니다.|
|/servers/databases/replicationLinks/updateReplicationMode/action|연결에 대한 복제 모드를 동기 또는 비동기 모드로 업데이트합니다.|
|/servers/databases/restorePoints/action|새 복원 지점을 만듭니다.|
|/servers/databases/restorePoints/read|데이터베이스의 복원 지점을 반환합니다.|
|/servers/databases/resume/action|Azure SQL Datawarehouse 데이터베이스를 다시 시작합니다.|
|/servers/databases/schemas/read|데이터베이스의 스키마 목록을 검색합니다.|
|/servers/databases/schemas/tables/columns/read|테이블의 열 목록을 검색합니다.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|지정된 열의 민감도 레이블을 삭제합니다.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|지정된 열의 민감도 레이블을 가져옵니다.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|지정된 열의 민감도 레이블을 만들거나 업데이트합니다.|
|/servers/databases/schemas/tables/read|데이터베이스의 테이블 목록을 검색합니다.|
|/servers/databases/schemas/tables/recommendedIndexes/read|데이터베이스에 대한 인덱스 권장 사항 목록을 검색합니다.|
|/servers/databases/schemas/tables/recommendedIndexes/write|인덱스 권장 사항을 적용합니다.|
|/servers/databases/securityAlertPolicies/read|지정된 데이터베이스에 구성된 위협 검색 정책의 세부 정보를 검색합니다.|
|/servers/databases/securityAlertPolicies/write|지정된 데이터베이스에 대한 위협 검색 정책을 변경합니다.|
|/servers/databases/securityMetrics/read|데이터베이스 보안 메트릭 컬렉션을 가져옵니다.|
|/servers/databases/sensitivityLabels/read|지정된 데이터베이스의 민감도 레이블을 나열합니다.|
|/servers/databases/serviceTierAdvisors/read|성능을 향상시키거나 비용을 절감하기 위해 쿼리 실행 통계를 기준으로 데이터베이스를 확장하거나 축소하기 위한 제안 사항을 반환합니다.|
|/servers/databases/syncGroups/cancelSync/action|동기화 그룹 동기화를 취소합니다.|
|/servers/databases/syncGroups/delete|기존 동기화 그룹을 삭제합니다.|
|/servers/databases/syncGroups/hubSchemas/read|동기화 허브 데이터베이스 스키마 목록을 반환합니다.|
|/servers/databases/syncGroups/logs/read|동기화 그룹 로그 목록을 반환합니다.|
|/servers/databases/syncGroups/read|동기화 그룹 목록을 가져오거나 지정된 동기화 그룹에 대한 속성을 가져옵니다.|
|/servers/databases/syncGroups/refreshHubSchema/action|동기화 허브 데이터베이스 스키마를 새로 고칩니다.|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|동기화 허브 스키마 새로 고침 작업의 결과를 검색합니다.|
|/servers/databases/syncGroups/syncMembers/delete|기존 동기화 멤버를 삭제합니다.|
|/servers/databases/syncGroups/syncMembers/read|동기화 멤버 목록을 가져오거나 지정된 동기화 멤버에 대한 속성을 가져옵니다.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|동기화 멤버 스키마를 새로 고칩니다.|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|동기화 멤버 스키마 새로 고침 작업의 결과를 검색합니다.|
|/servers/databases/syncGroups/syncMembers/schemas/read|동기화 멤버 데이터베이스 스키마 목록을 반환합니다.|
|/servers/databases/syncGroups/syncMembers/write|지정된 매개 변수를 사용하여 동기화 멤버를 만들거나 지정된 동기화 멤버에 대한 속성을 업데이트합니다.|
|/servers/databases/syncGroups/triggerSync/action|동기화 그룹 동기화를 트리거합니다.|
|/servers/databases/syncGroups/write|지정된 매개 변수를 사용하여 동기화 그룹을 만들거나 지정된 동기화 그룹에 대한 속성을 업데이트합니다.|
|/servers/databases/topQueries/queryText/action|선택한 쿼리 ID에 대한 Transact-SQL 텍스트를 반환합니다.|
|/servers/databases/topQueries/read|선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다.|
|/servers/databases/topQueries/statistics/read|선택한 기간에 선택한 쿼리에 대해 집계된 런타임 통계를 반환합니다.|
|/servers/databases/transparentDataEncryption/operationResults/read|투명한 데이터 암호화에 대해 진행 중인 작업을 가져옵니다.|
|/servers/databases/transparentDataEncryption/read|지정된 데이터베이스에 대한 투명한 데이터 암호화 보안 기능의 상태 및 세부 정보를 검색합니다.|
|/servers/databases/transparentDataEncryption/write|투명한 데이터 암호화 상태를 변경합니다.|
|/servers/databases/upgradeDataWarehouse/action|Azure SQL Datawarehouse 데이터베이스를 업그레이드합니다.|
|/servers/databases/usages/read|Azure SQL Database 사용 정보를 가져옵니다.|
|/servers/databases/vulnerabilityAssessments/delete|지정된 데이터베이스에 대한 취약성 평가를 제거합니다.|
|/servers/databases/vulnerabilityAssessments/read|지정된 데이터베이스에 구성된 취약성 평가 세부 정보를 검색합니다.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 제거합니다.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 가져옵니다.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|지정된 데이터베이스에 대한 취약성 평가 규칙 기준을 변경합니다.|
|/servers/databases/vulnerabilityAssessments/scans/action|취약점 평가 데이터베이스 검사를 실행합니다.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|기존 검사 결과를 사람이 읽을 수 있는 형식으로 변환합니다. 이미 있는 경우 아무 일도 발생하지 않습니다.|
|/servers/databases/vulnerabilityAssessments/scans/read|데이터베이스 취약성 평가 검사 레코드 목록을 반환하거나 지정된 검사 ID에 대한 검사 레코드를 가져옵니다.|
|/servers/databases/vulnerabilityAssessments/write|지정된 데이터베이스에 대한 취약성 평가를 변경합니다.|
|/servers/databases/vulnerabilityAssessmentScans/action|취약점 평가 데이터베이스 검사를 실행합니다.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|데이터베이스 취약성 평가 검사 실행 작업의 결과를 검색합니다.|
|/servers/databases/vulnerabilityAssessmentSettings/read|지정된 데이터베이스에 구성된 취약성 평가 세부 정보를 검색합니다.|
|/servers/databases/vulnerabilityAssessmentSettings/write|지정된 데이터베이스에 대한 취약성 평가를 변경합니다.|
|/servers/databases/write|지정된 매개 변수를 사용하여 데이터베이스를 만들거나 지정된 데이터베이스에 대한 속성 또는 태그를 업데이트합니다.|
|/servers/delete|기존 서버를 삭제합니다.|
|/servers/disasterRecoveryConfiguration/delete|지정된 서버에 대한 기존 재해 복구 구성을 삭제합니다.|
|/servers/disasterRecoveryConfiguration/failover/action|DisasterRecoveryConfiguration을 장애 조치(failover)합니다.|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|DisasterRecoveryConfiguration을 강제 장애 조치(failover)합니다.|
|/servers/disasterRecoveryConfiguration/read|이 서버를 포함하는 재해 복구 구성 컬렉션을 가져옵니다.|
|/servers/disasterRecoveryConfiguration/write|서버 재해 복구 구성을 변경합니다.|
|/servers/elasticPoolEstimates/read|이 서버에 대해 이미 작성된 탄력적 풀 예상치의 목록을 반환합니다.|
|/servers/elasticPoolEstimates/write|제공된 데이터베이스 목록에 대한 새 탄력적 풀 예상치를 만듭니다.|
|/servers/elasticPools/advisors/read|탄력적 풀에 사용할 수 있는 Advisor의 목록을 반환합니다.|
|/servers/elasticPools/advisors/recommendedActions/read|탄력적 풀에 대해 지정된 Advisor의 권장 작업 목록을 반환합니다.|
|/servers/elasticPools/advisors/recommendedActions/write|탄력적 풀에 권장 작업을 적용합니다.|
|/servers/elasticPools/advisors/write|탄력적 풀 수준에서 Advisor의 자동 실행 상태를 업데이트합니다.|
|/servers/elasticPools/databases/read|탄력적 풀에 대한 데이터베이스 목록을 가져옵니다.|
|/servers/elasticPools/delete|기존 탄력적 풀을 삭제합니다.|
|/servers/elasticPools/elasticPoolActivity/read|지정된 Elastic Database 풀에 대한 활동 및 세부 정보를 검색합니다.|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Elastic Database 풀에 속하는 지정된 데이터베이스에 대한 활동 및 세부 정보를 검색합니다.|
|/servers/elasticPools/metricDefinitions/read|Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/elasticPools/metrics/read|탄력적 데이터베이스 풀에 대한 메트릭을 반환합니다.|
|/servers/elasticPools/operations/cancel/action|아직 완료되지 않은 Azure SQL 탄력적 풀 보류 중인 비동기 작업을 취소합니다.|
|/servers/elasticPools/operations/read|탄력적 풀에서 수행된 작업 목록을 반환합니다.|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Elastic Database 풀에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/elasticPools/read|지정된 서버에서 탄력적 풀의 세부 정보를 검색합니다.|
|/servers/elasticPools/skus/read|이 탄력적 풀에 사용 가능한 sku 컬렉션을 가져옵니다.|
|/servers/elasticPools/write|탄력적 풀을 새로 만들거나 기존 탄력적 풀의 속성을 변경합니다.|
|/servers/encryptionProtector/read|서버 암호화 보호기 목록을 반환하거나 지정된 서버 암호화 보호기에 대한 속성을 가져옵니다.|
|/servers/encryptionProtector/write|지정된 서버 암호화 보호기에 대한 속성을 업데이트합니다.|
|/servers/extendedAuditingSettings/read|지정된 서버에 구성된 확장 서버 Blob 감사 정책의 세부 정보를 검색합니다.|
|/servers/extendedAuditingSettings/write|지정된 서버에 대한 확장 서버 Blob 감사를 변경합니다.|
|/servers/failoverGroups/delete|기존 장애 조치(failover) 그룹을 삭제합니다.|
|/servers/failoverGroups/failover/action|기존 장애 조치(failover) 그룹에서 계획된 장애 조치(failover)를 실행합니다.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|기존 장애 조치(failover) 그룹에서 강제 장애 조치(failover)를 실행합니다.|
|/servers/failoverGroups/read|장애 조치(failover) 그룹 목록을 가져오거나 지정된 장애 조치(failover) 그룹에 대한 속성을 가져옵니다.|
|/servers/failoverGroups/write|지정된 매개 변수를 사용하여 장애 조치(failover) 그룹을 만들거나 지정된 장애 조치(failover) 그룹에 대한 속성 또는 태그를 업데이트합니다.|
|/servers/firewallRules/delete|기존 서버 방화벽 규칙을 삭제합니다.|
|/servers/firewallRules/read|서버 방화벽 규칙 목록을 반환하거나 지정된 서버 방화벽 규칙에 대한 속성을 가져옵니다.|
|/servers/firewallRules/write|지정된 매개 변수를 사용하여 서버 방화벽 규칙을 만들고 지정된 규칙에 대한 속성을 업데이트하거나 모든 기존 규칙을 새로운 서버 방화벽 규칙으로 덮어씁니다.|
|/servers/import/action|서버에서 새 데이터베이스를 만들고 DacPac 패키지의 스키마 및 데이터를 배포합니다.|
|/servers/importExportOperationResults/read|진행 중인 가져오기/내보내기 작업을 가져옵니다.|
|/servers/keys/delete|기존 서버 키를 삭제합니다.|
|/servers/keys/read|서버 키 목록을 가져오거나 지정된 서버 키에 대한 속성을 가져옵니다.|
|/servers/keys/write|지정된 매개 변수를 사용하여 키를 만들거나 지정된 서버 키에 대한 속성 또는 태그를 업데이트합니다.|
|/servers/operationResults/read|진행 중인 서버 작업을 가져옵니다.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|서버에 사용할 수 있는 메트릭 형식을 반환합니다.|
|/servers/read|서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다.|
|/servers/recommendedElasticPools/databases/read|지정된 서버에 대해 권장되는 Elastic Database 풀의 메트릭을 검색합니다.|
|/servers/recommendedElasticPools/read|기록 리소스 사용률에 따라 비용을 절감하거나 성능을 향상시키기 위한 Elastic Database 풀에 대한 권장 사항을 검색합니다.|
|/servers/recoverableDatabases/read|이 작업은 라이브 데이터베이스의 재해 복구 기능이 데이터베이스를 마지막 정상 지점으로 복원하는 데 사용합니다. 마지막 정상 백업에 대한 정보가 반환되지만 실제로 데이터베이스는 복원되지 않습니다.|
|/servers/restorableDroppedDatabases/read|지정된 서버에서 삭제되었지만 여전히 보존 정책에 속하는 데이터베이스 목록을 가져옵니다.|
|/servers/securityAlertPolicies/operationResults/read|서버 위협 검색 정책 쓰기 작업의 결과를 검색합니다.|
|/servers/securityAlertPolicies/read|지정된 서버에 구성된 서버 위협 검색 정책의 세부 정보를 검색합니다.|
|/servers/securityAlertPolicies/write|지정된 서버에 대한 서버 위협 검색 정책을 변경합니다.|
|/servers/serviceObjectives/read|지정된 서버에서 사용할 수 있는 서비스 수준 목표(성능 계층이라고도 함) 목록을 검색합니다.|
|/servers/syncAgents/delete|기존 동기화 에이전트를 삭제합니다.|
|/servers/syncAgents/generateKey/action|동기화 에이전트 등록 키를 생성합니다.|
|/servers/syncAgents/linkedDatabases/read|동기화 에이전트 연결 데이터베이스 목록을 반환합니다.|
|/servers/syncAgents/read|동기화 에이전트 목록을 가져오거나 지정된 동기화 에이전트에 대한 속성을 가져옵니다.|
|/servers/syncAgents/write|지정된 매개 변수를 사용하여 동기화 에이전트를 만들거나 지정된 동기화 에이전트에 대한 속성을 업데이트합니다.|
|/servers/usages/read|서버 내의 모든 데이터베이스별 서버 DTU 할당량 및 현재 DTU 소비량을 반환합니다.|
|/servers/virtualNetworkRules/delete|기존 가상 네트워크 규칙을 삭제합니다.|
|/servers/virtualNetworkRules/read|가상 네트워크 규칙 목록을 반환하거나 지정된 가상 네트워크 규칙에 대한 속성을 가져옵니다.|
|/servers/virtualNetworkRules/write|지정된 매개 변수를 사용하여 가상 네트워크 규칙을 만들거나 지정된 가상 네트워크 규칙에 대한 속성 또는 태그를 업데이트합니다.|
|/servers/write|지정된 매개 변수를 사용하여 서버를 만들거나 지정된 서버에 대한 속성 또는 태그를 업데이트합니다.|
|/unregister/action|Microsoft SQL Database 리소스 공급자에 대한 구독을 등록 취소하고 Microsoft SQL Databases를 만들도록 설정합니다.|
|/virtualClusters/read|가상 클러스터 목록을 반환하거나 지정된 가상 클러스터에 대한 속성을 가져옵니다.|
|/virtualClusters/write|가상 클러스터 태그를 업데이트합니다.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| 작업 | 설명 |
|---|---|
|/checknameavailability/read|해당 계정 이름이 올바른지와 사용되고 있지 않은지 확인합니다.|
|/locations/deleteVirtualNetworkOrSubnets/action|가상 네트워크 또는 서브넷을 삭제 중임을 Microsoft.Storage에 알립니다.|
|/operations/read|비동기 작업의 상태를 폴링합니다.|
|/register/action|저장소 리소스 공급자에 대한 구독을 등록하고 저장소 계정을 만들도록 설정합니다.|
|/skus/read|Microsoft Storage에서 지원하는 SKU를 나열합니다.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Blob 컨테이너 법적 보존을 지웁니다.|
|/storageAccounts/blobServices/containers/delete|컨테이너 삭제 결과를 반환합니다.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Blob 컨테이너 불변성 정책을 삭제합니다.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Blob 컨테이너 불변성 정책을 확장합니다.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Blob 컨테이너 불변성 정책을 잠급니다.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Blob 컨테이너 불변성 정책을 가져옵니다.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Blob 컨테이너 불변성 정책을 넣습니다.|
|/storageAccounts/blobServices/containers/read|컨테이너 또는 컨테이너 목록을 반환합니다.|
|/storageAccounts/blobServices/containers/setLegalHold/action|Blob 컨테이너 법적 보존을 설정합니다.|
|/storageAccounts/blobServices/containers/write|Blob 컨테이너 넣기 또는 임대 결과를 반환합니다.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft 저장소 메트릭 정의 목록을 가져옵니다.|
|/storageAccounts/blobServices/read|Blob service 속성 또는 통계를 반환합니다.|
|/storageAccounts/blobServices/write|Blob service 속성 넣기의 결과를 반환합니다.|
|/storageAccounts/delete|기존 저장소 계정을 삭제합니다.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft 저장소 메트릭 정의 목록을 가져옵니다.|
|/storageAccounts/listAccountSas/action|지정된 저장소 계정에 대한 계정 SAS 토큰을 반환합니다.|
|/storageAccounts/listkeys/action|지정된 저장소 계정에 대한 액세스 키를 반환합니다.|
|/storageAccounts/listServiceSas/action|지정된 저장소 계정에 대한 서비스 SAS 토큰을 반환합니다.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Microsoft 저장소 메트릭 정의 목록을 가져옵니다.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft 저장소 메트릭 정의 목록을 가져옵니다.|
|/storageAccounts/queueServices/queues/delete|큐 삭제 결과를 반환합니다.|
|/storageAccounts/queueServices/queues/read|큐 또는 큐 목록을 반환합니다.|
|/storageAccounts/queueServices/queues/write|큐 쓰기 결과를 반환합니다.|
|/storageAccounts/queueServices/read|큐 서비스 속성 또는 통계를 반환합니다.|
|/storageAccounts/queueServices/write|큐 서비스 속성 설정 결과를 반환합니다.|
|/storageAccounts/read|저장소 계정의 목록을 반환하거나 지정된 저장소 계정의 속성을 가져옵니다.|
|/storageAccounts/regeneratekey/action|지정된 저장소 계정에 대한 액세스 키를 다시 생성합니다.|
|/storageAccounts/services/diagnosticSettings/write|저장소 계정 진단 설정을 만들거나 업데이트합니다.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Microsoft 저장소 메트릭 정의 목록을 가져옵니다.|
|/storageAccounts/write|지정된 매개 변수를 사용하여 저장소 계정을 만들거나, 속성 또는 태그를 업데이트하거나, 지정된 저장소 계정의 사용자 지정 도메인을 추가합니다.|
|/usages/read|지정한 구독의 리소스에 대한 한도 및 현재 사용 수를 반환합니다.|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| 작업 | 설명 |
|---|---|
|/storageSyncServices/delete|Storage Sync Services를 삭제합니다.|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Storage Sync Services에 사용 가능한 메트릭을 가져옵니다.|
|/storageSyncServices/read|Storage Sync Services를 읽습니다.|
|/storageSyncServices/registeredServers/delete|등록된 서버를 삭제합니다.|
|/storageSyncServices/registeredServers/read|등록된 서버를 읽습니다.|
|/storageSyncServices/registeredServers/write|등록된 서버를 만들거나 업데이트합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|클라우드 엔드포인트를 삭제합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|비동기 백업 호출에 대한 위치 api입니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|백업 후 이 작업을 호출합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|복원 후 이 작업을 호출합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|백업 전에 이 작업을 호출합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|복원 전에 이 작업을 호출합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/read|클라우드 엔드포인트를 준비합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|하트비트를 복원합니다.|
|/storageSyncServices/syncGroups/cloudEndpoints/write|클라우드 엔드포인트를 만들거나 업데이트합니다.|
|/storageSyncServices/syncGroups/delete|동기화 그룹을 삭제합니다.|
|/storageSyncServices/syncGroups/read|동기화 그룹을 읽습니다.|
|/storageSyncServices/syncGroups/serverEndpoints/delete|서버 엔드포인트를 삭제합니다.|
|/storageSyncServices/syncGroups/serverEndpoints/read|서버 엔드포인트를 읽습니다.|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|파일을 서버로 회수하려면 이 작업을 호출합니다.|
|/storageSyncServices/syncGroups/serverEndpoints/write|서버 엔드포인트를 만들거나 업데이트합니다.|
|/storageSyncServices/syncGroups/write|동기화 그룹을 만들거나 업데이트합니다.|
|/storageSyncServices/write|Storage Sync Services를 만들거나 업데이트합니다.|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| 작업 | 설명 |
|---|---|
|/managers/accessControlRecords/delete|Access Control 레코드를 삭제합니다.|
|/managers/accessControlRecords/read|Access Control 레코드를 나열하거나 가져옵니다.|
|/managers/accessControlRecords/write|Access Control 레코드를 만들거나 업데이트합니다.|
|/managers/alerts/read|경고를 나열하거나 가져옵니다.|
|/managers/bandwidthSettings/delete|기존 대역폭 설정을 삭제합니다(8000 시리즈만 해당).|
|/managers/bandwidthSettings/read|대역폭 설정을 나열합니다(8000 시리즈만 해당).|
|/managers/bandwidthSettings/write|새 대역폭 설정을 만들거나 대역폭 설정을 업데이트합니다(8000 시리즈만 해당).|
|/Managers/certificates/write|리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다.|
|/managers/clearAlerts/action|장치 관리자와 관련된 모든 경고를 선택 취소합니다.|
|/managers/cloudApplianceConfigurations/read|클라우드 어플라이언스 지원 구성을 나열합니다.|
|/managers/configureDevice/action|장치를 구성합니다.|
|/managers/delete|장치 관리자를 삭제합니다.|
|/Managers/delete|자격 증명 모음 삭제 작업에서는 '자격 증명 모음' 형식의 지정된 Azure 리소스를 삭제합니다.|
|/managers/devices/alertSettings/read|경고 설정을 나열하거나 가져옵니다.|
|/managers/devices/alertSettings/write|경고 설정을 만들거나 업데이트합니다.|
|/managers/devices/backupPolicies/backup/action|정책으로 보호되는 모든 볼륨의 요청 시 백업을 만들기 위해 수동 백업을 수행합니다.|
|/managers/devices/backupPolicies/delete|기존 Backup 정책을 삭제합니다(8000 시리즈만 해당).|
|/managers/devices/backupPolicies/read|Backup 정책을 나열합니다(8000 시리즈만 해당).|
|/managers/devices/backupPolicies/schedules/delete|기존 일정을 삭제합니다.|
|/managers/devices/backupPolicies/schedules/read|일정을 나열합니다.|
|/managers/devices/backupPolicies/schedules/write|새 일정을 만들거나 일정을 업데이트합니다.|
|/managers/devices/backupPolicies/write|새 Backup 정책을 만들거나 Backup 정책을 업데이트합니다(8000 시리즈만 해당).|
|/managers/devices/backups/delete|Backup 세트를 삭제합니다.|
|/managers/devices/backups/elements/clone/action|백업 요소를 사용하여 공유 또는 볼륨을 복제합니다.|
|/managers/devices/backups/read|Backup 세트를 나열하거나 가져옵니다.|
|/managers/devices/backups/restore/action|백업 세트에서 모든 볼륨을 복원합니다.|
|/managers/devices/backupScheduleGroups/delete|Backup 일정 그룹을 삭제합니다.|
|/managers/devices/backupScheduleGroups/read|Backup 일정 그룹을 나열하거나 가져옵니다.|
|/managers/devices/backupScheduleGroups/write|Backup 일정 그룹을 만들거나 업데이트합니다.|
|/managers/devices/chapSettings/delete|CHAP 설정을 삭제합니다.|
|/managers/devices/chapSettings/read|CHAP 설정을 나열하거나 가져옵니다.|
|/managers/devices/chapSettings/write|CHAP 설정을 만들거나 업데이트합니다.|
|/managers/devices/deactivate/action|장치를 비활성화합니다.|
|/managers/devices/delete|장치를 삭제합니다.|
|/managers/devices/download/action|장치에 대한 업데이트를 다운로드합니다.|
|/managers/devices/failover/action|장치의 장애 조치(Failover)입니다.|
|/managers/devices/fileservers/backup/action|파일 서버 백업을 수행합니다.|
|/managers/devices/fileservers/delete|파일 서버를 삭제합니다.|
|/managers/devices/fileservers/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/fileservers/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/read|파일 서버를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/delete|공유를 삭제합니다.|
|/managers/devices/fileservers/shares/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/read|공유를 나열하거나 가져옵니다.|
|/managers/devices/fileservers/shares/write|공유를 만들거나 업데이트합니다.|
|/managers/devices/fileservers/write|파일 서버를 만들거나 업데이트합니다.|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|하드웨어 구성 요소 그룹의 컨트롤러 전원 상태를 변경합니다.|
|/managers/devices/hardwareComponentGroups/read|하드웨어 구성 요소 그룹을 나열합니다.|
|/managers/devices/install/action|장치에 업데이트를 설치합니다.|
|/managers/devices/installUpdates/action|장치에 업데이트를 설치합니다.|
|/managers/devices/iscsiservers/backup/action|ISCSI 서버 백업을 수행합니다.|
|/managers/devices/iscsiservers/delete|ISCSI 서버를 삭제합니다.|
|/managers/devices/iscsiservers/disks/delete|디스크를 삭제합니다.|
|/managers/devices/iscsiservers/disks/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/disks/read|디스크를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/disks/write|디스크를 만들거나 업데이트합니다.|
|/managers/devices/iscsiservers/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/read|iSCSI 서버를 나열하거나 가져옵니다.|
|/managers/devices/iscsiservers/write|iSCSI 서버를 만들거나 업데이트합니다.|
|/managers/devices/jobs/cancel/action|실행 중인 작업을 취소합니다.|
|/managers/devices/jobs/read|작업을 나열하거나 가져옵니다.|
|/managers/devices/listFailoverSets/action|기존 장치에 대한 장애 조치(Failover) 설정을 나열합니다.|
|/managers/devices/listFailoverTargets/action|장치의 장애 조치(Failover) 대상을 나열합니다.|
|/managers/devices/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/devices/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|성공적인 마이그레이션을 확인하고 커밋합니다.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|마이그레이션 확인 상태를 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|마이그레이션 예측 작업의 상태를 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|마이그레이션의 상태를 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/import/action|마이그레이션 위한 원본 구성을 가져옵니다.|
|/managers/devices/migrationSourceConfigurations/startMigration/action|원본 구성을 사용하여 마이그레이션을 시작합니다.|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|마이그레이션 프로세스 기간을 예측하는 작업을 시작합니다.|
|/managers/devices/networkSettings/read|네트워크 설정을 나열하거나 가져옵니다.|
|/managers/devices/networkSettings/write|새 네트워크 설정을 만들거나 네트워크 설정을 업데이트합니다.|
|/managers/devices/publicEncryptionKey/action|장치 관리자의 공용 암호화 키를 나열합니다.|
|/managers/devices/publishSupportPackage/action|Microsoft 지원 문제 해결을 위해 장치의 지원 패키지를 게시합니다.|
|/managers/devices/read|장치를 나열하거나 가져옵니다.|
|/managers/devices/scanForUpdates/action|장치에서 업데이트를 검색합니다.|
|/managers/devices/securitySettings/read|보안 설정을 나열합니다.|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|장치에 대한 원격 관리 인증서를 동기화합니다.|
|/managers/devices/securitySettings/update/action|보안 설정을 업데이트합니다.|
|/managers/devices/securitySettings/write|새 보안 설정을 만들거나 보안 설정을 업데이트합니다.|
|/managers/devices/sendTestAlertEmail/action|구성된 전자 메일 받는 사람에게 테스트 경고 전자 메일을 보냅니다.|
|/managers/devices/timeSettings/read|시간 설정을 나열하거나 가져옵니다.|
|/managers/devices/timeSettings/write|새 시간 설정을 만들거나 시간 설정을 업데이트합니다.|
|/managers/devices/updateSummary/read|업데이트 요약을 나열하거나 가져옵니다.|
|/managers/devices/volumeContainers/delete|기존 볼륨 컨테이너를 삭제합니다(8000 시리즈만 해당).|
|/managers/devices/volumeContainers/listEncryptionKeys/action|볼륨 컨테이너의 암호화 키를 나열합니다.|
|/managers/devices/volumeContainers/metrics/read|메트릭을 나열합니다|
|/managers/devices/volumeContainers/metricsDefinitions/read|메트릭 정의를 나열합니다.|
|/managers/devices/volumeContainers/read|볼륨 컨테이너를 나열합니다(8000 시리즈만 해당).|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|볼륨 컨테이너의 암호화 키를 롤오버합니다.|
|/managers/devices/volumeContainers/volumes/delete|기존 볼륨을 삭제합니다.|
|/managers/devices/volumeContainers/volumes/metrics/read|메트릭을 나열합니다|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|메트릭 정의를 나열합니다.|
|/managers/devices/volumeContainers/volumes/read|볼륨을 나열합니다.|
|/managers/devices/volumeContainers/volumes/write|새 볼륨을 만들거나 볼륨을 업데이트합니다.|
|/managers/devices/volumeContainers/write|새 볼륨 컨테이너를 만들거나 볼륨 컨테이너를 업데이트합니다(8000 시리즈만 해당).|
|/managers/devices/write|장치를 만들거나 업데이트합니다.|
|/managers/encryptionSettings/read|암호화 설정을 나열하거나 가져옵니다.|
|/Managers/extendedInformation/delete|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Managers/extendedInformation/read|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/Managers/extendedInformation/write|확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다.|
|/managers/getActivationKey/action|장치 관리자에 대한 활성화 키를 가져옵니다.|
|/managers/getEncryptionKey/action|장치 관리자에 대한 암호화 키를 가져옵니다.|
|/managers/listActivationKey/action|StorSimple 장치 관리자의 활성화 키를 가져옵니다.|
|/managers/listPrivateEncryptionKey/action|StorSimple 장치 관리자의 개인 암호화 키를 가져옵니다.|
|/managers/listPublicEncryptionKey/action|StorSimple 장치 관리자의 공용 암호화 키를 나열합니다.|
|/managers/metrics/read|메트릭을 나열하거나 가져옵니다.|
|/managers/metricsDefinitions/read|메트릭 정의를 나열하거나 가져옵니다.|
|/managers/provisionCloudAppliance/action|새 클라우드 어플라이언스를 만듭니다.|
|/managers/read|장치 관리자를 나열하거나 가져옵니다.|
|/Managers/read|자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다.|
|/managers/regenarateRegistationCertificate/action|장치 관리자에 대한 등록 인증서를 다시 생성합니다.|
|/managers/regenerateActivationKey/action|장치 관리자에 대한 활성화 키를 다시 생성합니다.|
|/managers/storageAccountCredentials/delete|Storage 계정 자격 증명을 삭제합니다.|
|/managers/storageAccountCredentials/listAccessKey/action|Storage 계정 자격 증명의 액세스 키를 나열합니다.|
|/managers/storageAccountCredentials/read|Storage 계정 자격 증명을 나열하거나 가져옵니다.|
|/managers/storageAccountCredentials/write|Storage 계정 자격 증명을 만들거나 업데이트합니다.|
|/managers/storageDomains/delete|저장소 도메인을 삭제합니다.|
|/managers/storageDomains/read|저장소 도메인을 나열하거나 가져옵니다.|
|/managers/storageDomains/write|저장소 도메인을 만들거나 업데이트합니다.|
|/managers/write|장치 관리자를 만들거나 업데이트합니다.|
|/Managers/write|자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다.|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| 작업 | 설명 |
|---|---|
|/locations/quotas/Read|Stream Analytics 구독 할당량을 읽습니다.|
|/operations/Read|Stream Analytics 작업을 읽습니다.|
|/Register/action|구독을 Stream Analytics 리소스 공급자에 등록합니다.|
|/streamingjobs/Delete|Stream Analytics 작업을 삭제합니다.|
|/streamingjobs/functions/Delete|Stream Analytics 작업 함수를 삭제합니다.|
|/streamingjobs/functions/operationresults/Read|Stream Analytics 작업 함수에 대한 작업 결과를 읽습니다.|
|/streamingjobs/functions/Read|Stream Analytics 작업 함수를 읽습니다.|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Stream Analytics 작업 함수의 기본 정의를 검색합니다.|
|/streamingjobs/functions/Test/action|Stream Analytics 작업 함수를 테스트합니다.|
|/streamingjobs/functions/Write|Stream Analytics 작업 함수를 씁니다.|
|/streamingjobs/inputs/Delete|Stream Analytics 작업 입력을 삭제합니다.|
|/streamingjobs/inputs/operationresults/Read|Stream Analytics 작업 입력에 대한 작업 결과를 읽습니다.|
|/streamingjobs/inputs/Read|Stream Analytics 작업 입력을 읽습니다.|
|/streamingjobs/inputs/Sample/action|Stream Analytics 작업 입력을 샘플링합니다.|
|/streamingjobs/inputs/Test/action|Stream Analytics 작업 입력을 테스트합니다.|
|/streamingjobs/inputs/Write|Stream Analytics 작업 입력을 씁니다.|
|/streamingjobs/metricdefinitions/Read|메트릭 정의를 읽습니다.|
|/streamingjobs/operationresults/Read|Stream Analytics 작업에 대한 작업 결과를 읽습니다.|
|/streamingjobs/outputs/Delete|Stream Analytics 작업 출력을 삭제합니다.|
|/streamingjobs/outputs/operationresults/Read|Stream Analytics 작업 출력에 대한 작업 결과를 읽습니다.|
|/streamingjobs/outputs/Read|Stream Analytics 작업 출력을 읽습니다.|
|/streamingjobs/outputs/Test/action|Stream Analytics 작업 출력을 테스트합니다.|
|/streamingjobs/outputs/Write|Stream Analytics 작업 출력을 씁니다.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|진단 설정을 읽습니다.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|진단 설정을 씁니다.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|streamingjobs에 대해 사용 가능한 로그를 가져옵니다.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|streamingjobs에 대해 사용 가능한 메트릭을 가져옵니다.|
|/streamingjobs/Read|Stream Analytics 작업을 읽습니다.|
|/streamingjobs/Start/action|Stream Analytics 작업을 시작합니다.|
|/streamingjobs/Stop/action|Stream Analytics 작업을 중지합니다.|
|/streamingjobs/transformations/Delete|Stream Analytics 작업 변환을 삭제합니다.|
|/streamingjobs/transformations/Read|Stream Analytics 작업 변환을 읽습니다.|
|/streamingjobs/transformations/Write|Stream Analytics 작업 변환을 씁니다.|
|/streamingjobs/Write|Stream Analytics 작업을 씁니다.|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| 작업 | 설명 |
|---|---|
|/SubscriptionDefinitions/read|관리 그룹의 Azure 구독 정의를 가져옵니다.|
|/SubscriptionDefinitions/write|Azure 구독 정의를 만듭니다.|

## <a name="microsoftsupport"></a>Microsoft.Support

| 작업 | 설명 |
|---|---|
|/register/action|지원 리소스 공급자에 등록합니다.|
|/supportTickets/read|상태, 심각도, 연락처 세부 정보, 통신 등 지원 티켓 세부 정보를 얻거나 구독의 지원 티켓 목록을 얻습니다.|
|/supportTickets/write|지원 티켓을 만들거나 업데이트합니다. 기술, 청구, 할당량 또는 구독 관리 관련 문제에 대한 지원 티켓을 만들 수 있습니다. 기존 지원 티켓의 심각도, 연락처 세부 정보 및 통신을 업데이트할 수 있습니다.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| 작업 | 설명 |
|---|---|
|/environments/accesspolicies/delete|액세스 정책을 삭제합니다.|
|/environments/accesspolicies/read|액세스 정책 속성을 가져옵니다.|
|/environments/accesspolicies/write|환경에 대한 새 액세스 정책을 만들거나 기존 액세스 정책을 업데이트합니다.|
|/environments/delete|환경을 삭제합니다.|
|/environments/eventsources/delete|이벤트 원본을 삭제합니다.|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|이벤트 원본에 사용 가능한 메트릭을 가져옵니다.|
|/environments/eventsources/read|이벤트 원본 속성을 가져옵니다.|
|/environments/eventsources/write|환경에 대한 새 이벤트 원본을 만들거나 기존 이벤트 원본을 업데이트합니다.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|환경에 사용 가능한 메트릭을 가져옵니다.|
|/environments/read|환경 속성을 가져옵니다.|
|/environments/referencedatasets/delete|참조 데이터 집합을 삭제합니다.|
|/environments/referencedatasets/read|참조 데이터 집합의 속성을 가져옵니다.|
|/environments/referencedatasets/write|환경에 대한 새 참조 데이터 집합을 만들거나 기존 참조 데이터 집합을 업데이트합니다.|
|/environments/status/read|환경의 상태 및 환경과 연결된 작업 상태(예: 수신)를 가져옵니다.|
|/environments/write|새 환경을 만들거나 기존 환경을 업데이트합니다.|
|/register/action|Time Series Insights 리소스 공급자에 대한 구독을 등록하고 Time Series Insights 환경을 만들도록 설정합니다.|

## <a name="microsoftweb"></a>microsoft.web

| 작업 | 설명 |
|---|---|
|/apimanagementaccounts/apiacls/read|API Management 계정 Apiacls를 가져옵니다.|
|/apimanagementaccounts/apis/apiacls/delete|API Management 계정 API Apiacls를 삭제합니다.|
|/apimanagementaccounts/apis/apiacls/read|API Management 계정 API Apiacls를 가져옵니다.|
|/apimanagementaccounts/apis/apiacls/write|API Management 계정 API Apiacls를 업데이트합니다.|
|/apimanagementaccounts/apis/connectionacls/read|API Management 계정 API Connectionacls를 가져옵니다.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|동의 코드 API Management 계정 API 연결을 확인합니다.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|API Management 계정 API 연결 Connectionacls를 삭제합니다.|
|/apimanagementaccounts/apis/connections/connectionacls/read|API Management 계정 API 연결 Connectionacls를 가져옵니다.|
|/apimanagementaccounts/apis/connections/connectionacls/write|API Management 계정 API 연결 Connectionacls를 업데이트합니다.|
|/apimanagementaccounts/apis/connections/delete|API Management 계정 API 연결을 삭제합니다.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|API Management 계정 API 연결에 대한 동의 링크를 가져옵니다.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|연결 키 API Management 계정 API 연결을 나열합니다.|
|/apimanagementaccounts/apis/connections/listsecrets/action|비밀 API Management 계정 API 연결을 나열합니다.|
|/apimanagementaccounts/apis/connections/read|API Management 계정 API 연결을 가져옵니다.|
|/apimanagementaccounts/apis/connections/write|API Management 계정 API 연결을 업데이트합니다.|
|/apimanagementaccounts/apis/delete|API Management 계정 API를 삭제합니다.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|API Management 계정 API 지역화된 정의를 삭제합니다.|
|/apimanagementaccounts/apis/localizeddefinitions/read|API Management 계정 API 지역화된 정의를 가져옵니다.|
|/apimanagementaccounts/apis/localizeddefinitions/write|API Management 계정 API 지역화된 정의를 업데이트합니다.|
|/apimanagementaccounts/apis/read|API Management 계정 API를 가져옵니다.|
|/apimanagementaccounts/apis/write|API Management 계정 API를 업데이트합니다.|
|/apimanagementaccounts/connectionacls/read|API Management 계정 Connectionacls를 가져옵니다.|
|/availablestacks/read|사용 가능한 스택을 가져옵니다.|
|/billingmeters/read|요금 청구 미터 목록을 가져옵니다.|
|/certificates/Delete|기존 인증서를 삭제합니다.|
|/certificates/Read|인증서 목록을 가져옵니다.|
|/certificates/Write|새 인증서를 추가하거나 기존 인증서를 업데이트합니다.|
|/checknameavailability/read|리소스 이름이 사용 가능한지 확인합니다.|
|/classicmobileservices/read|클래식 Mobile Services를 가져옵니다.|
|/connectionGateways/Delete|연결 게이트웨이를 삭제합니다.|
|/connectionGateways/Join/Action|연결 게이트웨이를 연결합니다.|
|/connectiongateways/liststatus/action|상태 연결 게이트웨이를 나열합니다.|
|/connectionGateways/ListStatus/Action|연결 게이트웨이 상태를 나열합니다.|
|/connectionGateways/Move/Action|연결 게이트웨이를 이동합니다.|
|/connectionGateways/Read|연결 게이트웨이 목록을 가져옵니다.|
|/connectionGateways/Write|연결 게이트웨이를 만들거나 업데이트합니다.|
|/connections/confirmconsentcode/action|연결 동의 코드를 확인합니다.|
|/connections/Delete|연결을 삭제합니다.|
|/connections/Join/Action|연결을 연결합니다.|
|/connections/listconsentlinks/action|연결에 대한 동의 링크를 나열합니다.|
|/connections/Move/Action|연결을 이동합니다.|
|/connections/Read|연결 목록을 가져옵니다.|
|/connections/Write|연결을 만들거나 업데이트합니다.|
|/customApis/Delete|사용자 지정 API를 삭제합니다.|
|/customApis/extractApiDefinitionFromWsdl/Action|WSDL에서 API 정의를 추출합니다.|
|/customApis/Join/Action|사용자 지정 API를 조인합니다.|
|/customApis/listWsdlInterfaces/Action|사용자 지정 API에 대한 WSDL 인터페이스를 나열합니다.|
|/customApis/Move/Action|사용자 지정 API를 이동합니다.|
|/customApis/Read|사용자 지정 API 목록을 가져옵니다.|
|/customApis/Write|사용자 지정 API를 만들거나 업데이트합니다.|
|/deploymentlocations/read|배포 위치를 가져옵니다.|
|/geoRegions/Read|지리적 영역 목록을 가져옵니다.|
|/hostingenvironments/capacities/read|호스팅 환경 용량을 가져옵니다.|
|/hostingEnvironments/Delete|App Service 환경을 삭제합니다.|
|/hostingenvironments/diagnostics/read|호스팅 환경 진단을 가져옵니다.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|모든 인바운드 종속성의 네트워크 엔드포인트를 가져옵니다.|
|/hostingenvironments/metricdefinitions/read|호스팅 환경 메트릭 정의를 가져옵니다.|
|/hostingenvironments/multirolepools/metricdefinitions/read|호스팅 환경 MultiRole 풀 메트릭 정의를 가져옵니다.|
|/hostingenvironments/multirolepools/metrics/read|호스팅 환경 MultiRole 풀 메트릭을 가져옵니다.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|App Service Environment MultiRole에 사용 가능한 메트릭을 가져옵니다.|
|/hostingEnvironments/multiRolePools/Read|App Service 환경의 FrontEnd 풀 속성을 가져옵니다.|
|/hostingenvironments/multirolepools/skus/read|호스팅 환경 MultiRole 풀 SKU를 가져옵니다.|
|/hostingenvironments/multirolepools/usages/read|호스팅 환경 MultiRole 풀 사용 현황을 가져옵니다.|
|/hostingEnvironments/multiRolePools/Write|App Service 환경에 새 FrontEnd 풀을 만들거나 기존 FrontEnd 풀을 업데이트합니다.|
|/hostingenvironments/operations/read|호스팅 환경 작업을 가져옵니다.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|모든 아웃바운드 종속성의 네트워크 엔드포인트를 가져옵니다.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/hostingEnvironments/Read|App Service 환경의 속성을 가져옵니다.|
|/hostingEnvironments/reboot/Action|App Service 환경의 모든 컴퓨터를 다시 부팅합니다.|
|/hostingenvironments/resume/action|호스팅 환경을 계속합니다.|
|/hostingenvironments/serverfarms/read|호스팅 환경 App Service 계획을 가져옵니다.|
|/hostingenvironments/sites/read|호스팅 환경 Web Apps를 가져옵니다.|
|/hostingenvironments/suspend/action|호스팅 환경을 일시 중단합니다.|
|/hostingenvironments/usages/read|호스팅 환경 사용 현황을 가져옵니다.|
|/hostingenvironments/workerpools/metricdefinitions/read|호스팅 환경 Workerpools 메트릭 정의를 가져옵니다.|
|/hostingenvironments/workerpools/metrics/read|호스팅 환경 Workerpools 메트릭을 가져옵니다.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|App Service Environment WorkerPool에 사용 가능한 메트릭을 가져옵니다.|
|/hostingEnvironments/workerPools/Read|App Service 환경의 작업자 풀 속성을 가져옵니다.|
|/hostingenvironments/workerpools/skus/read|호스팅 환경 Workerpools SKU를 가져옵니다.|
|/hostingenvironments/workerpools/usages/read|호스팅 환경 Workerpools 사용 현황을 가져옵니다.|
|/hostingEnvironments/workerPools/Write|App Service 환경에 새 작업자 풀을 만들거나 기존 작업자 풀을 업데이트합니다.|
|/hostingEnvironments/Write|새 App Service 환경을 만들거나 기존 App Service 환경을 업데이트합니다.|
|/ishostingenvironmentnameavailable/read|호스팅 환경 이름을 사용할 수 있는지 여부를 가져옵니다.|
|/ishostnameavailable/read|호스트 이름을 사용할 수 있는지를 확인합니다.|
|/isusernameavailable/read|사용자 이름을 사용할 수 있는지를 확인합니다.|
|/listSitesAssignedToHostName/Read|호스트 이름에 할당된 사이트의 이름을 가져옵니다.|
|/locations/apioperations/read|위치 API 작업을 가져옵니다.|
|/locations/connectiongatewayinstallations/read|위치 연결 게이트웨이 설치를 가져옵니다.|
|/locations/extractapidefinitionfromwsdl/action|위치에 대한 WSDL에서 Api 정의를 추출합니다.|
|/locations/listwsdlinterfaces/action|위치에 대한 WSDL 인터페이스를 나열합니다.|
|/locations/managedapis/apioperations/read|위치 관리되는 API 작업을 가져옵니다.|
|/locations/managedapis/Join/Action|관리되는 API를 조인합니다.|
|/locations/managedapis/read|위치 관리되는 AAPI를 가져옵니다.|
|/operations/read|작업을 가져옵니다.|
|/publishingusers/read|게시 사용자를 가져옵니다.|
|/publishingusers/write|게시 사용자를 업데이트합니다.|
|/recommendations/Read|구독에 대한 권장 사항 목록을 가져옵니다.|
|/register/action|구독에 대한 Microsoft.Web 리소스 공급자를 등록합니다.|
|/resourcehealthmetadata/read|Resource Health 메타데이터를 가져옵니다.|
|/serverfarms/capabilities/read|App Service 계획 기능을 가져옵니다.|
|/serverfarms/Delete|기존 App Service 계획 삭제|
|/serverfarms/firstpartyapps/settings/delete|App Service 계획 자사 앱 설정을 삭제합니다.|
|/serverfarms/firstpartyapps/settings/read|App Service 계획 자사 앱 설정을 가져옵니다.|
|/serverfarms/firstpartyapps/settings/write|App Service 계획 자사 앱 설정을 업데이트합니다.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|App Service 계획 하이브리드 연결 네임스페이스 릴레이를 삭제합니다.|
|/serverfarms/hybridconnectionnamespaces/relays/read|App Service 계획 하이브리드 연결 네임스페이스 릴레이를 가져옵니다.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|App Service 계획 하이브리드 연결 네임스페이스 릴레이 Web Apps를 가져옵니다.|
|/serverfarms/hybridconnectionplanlimits/read|App Service 계획 하이브리드 연결 계획 제한을 가져옵니다.|
|/serverfarms/hybridconnectionrelays/read|App Service 계획 하이브리드 연결 릴레이를 가져옵니다.|
|/serverfarms/metricdefinitions/read|App Service 계획 메트릭 정의를 가져옵니다.|
|/serverfarms/metrics/read|App Service 계획 메트릭을 가져옵니다.|
|/serverfarms/operationresults/read|App Service 계획 작업 결과를 가져옵니다.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|App Service 계획에 사용 가능한 메트릭을 가져옵니다.|
|/serverfarms/Read|App Service 계획의 속성을 가져옵니다.|
|/serverfarms/restartSites/Action|App Service 계획의 모든 Web Apps를 다시 시작합니다.|
|/serverfarms/sites/read|App Service 계획 Web Apps를 가져옵니다.|
|/serverfarms/skus/read|App Service 계획 SKU를 가져옵니다.|
|/serverfarms/usages/read|App Service 계획 사용 현황을 가져옵니다.|
|/serverfarms/virtualnetworkconnections/gateways/write|App Service 계획 Virtual Network 연결 게이트웨이를 업데이트합니다.|
|/serverfarms/virtualnetworkconnections/read|App Service 계획 Virtual Network 연결을 가져옵니다.|
|/serverfarms/virtualnetworkconnections/routes/delete|App Service 계획 Virtual Network 연결 경로를 삭제합니다.|
|/serverfarms/virtualnetworkconnections/routes/read|App Service 계획 Virtual Network 연결 경로를 가져옵니다.|
|/serverfarms/virtualnetworkconnections/routes/write|App Service 계획 Virtual Network 연결 경로를 업데이트합니다.|
|/serverfarms/workers/reboot/action|App Service 계획 작업자를 다시 부팅합니다.|
|/serverfarms/Write|새 App Service 계획을 만들거나 기존 App Service 계획을 업데이트합니다.|
|/sites/analyzecustomhostname/read|사용자 지정 호스트 이름을 분석합니다.|
|/sites/applySlotConfig/Action|대상 슬롯의 웹앱 슬롯 구성을 현재 웹앱에 적용합니다.|
|/sites/backup/Action|새 웹앱 백업을 만듭니다.|
|/sites/backup/read|Web Apps Backup을 가져옵니다.|
|/sites/backup/write|Web Apps Backup을 업데이트합니다.|
|/sites/backups/delete|Web Apps Backup을 삭제합니다.|
|/sites/backups/list/action|Web Apps Backup을 나열합니다.|
|/sites/backups/Read|웹앱 백업의 속성을 가져옵니다.|
|/sites/backups/restore/action|Web Apps Backup을 복원합니다.|
|/sites/config/delete|Web Apps 구성을 삭제합니다.|
|/sites/config/list/Action|게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 보안 중요 설정을 나열합니다.|
|/sites/config/Read|웹앱 구성 설정을 가져옵니다.|
|/sites/config/Write|웹앱 구성 설정을 업데이트합니다.|
|/sites/continuouswebjobs/delete|Web Apps 연속 웹 작업을 삭제합니다.|
|/sites/continuouswebjobs/read|Web Apps 연속 웹 작업을 가져옵니다.|
|/sites/continuouswebjobs/start/action|Web Apps 연속 웹 작업을 시작합니다.|
|/sites/continuouswebjobs/stop/action|Web Apps 연속 웹 작업을 중지합니다.|
|/sites/Delete|기존 웹앱 삭제|
|/sites/deployments/delete|Web Apps 배포를 삭제합니다.|
|/sites/deployments/log/read|Web Apps 배포 로그를 가져옵니다.|
|/sites/deployments/read|Web Apps 배포를 가져옵니다.|
|/sites/deployments/write|Web Apps 배포를 업데이트합니다.|
|/sites/diagnostics/analyses/execute/Action|Web Apps 진단 분석을 실행합니다.|
|/sites/diagnostics/analyses/read|Web Apps 진단 분석을 가져옵니다.|
|/sites/diagnostics/aspnetcore/read|ASP.NET Core 앱용 Web Apps 진단을 가져옵니다.|
|/sites/diagnostics/autoheal/read|Web Apps 진단 Autoheal을 가져옵니다.|
|/sites/diagnostics/deployment/read|Web Apps 진단 배포를 가져옵니다.|
|/sites/diagnostics/deployments/read|Web Apps 진단 배포를 가져옵니다.|
|/sites/diagnostics/detectors/execute/Action|Web Apps 진단 감지기를 실행합니다.|
|/sites/diagnostics/detectors/read|Web Apps 진단 감지기를 가져옵니다.|
|/sites/diagnostics/failedrequestsperuri/read|Web Apps 진단 Uri당 실패한 요청을 가져옵니다.|
|/sites/diagnostics/frebanalysis/read|Web Apps 진단 FREB 분석을 가져옵니다.|
|/sites/diagnostics/loganalyzer/read|Web Apps 진단 로그 분석기를 가져옵니다.|
|/sites/diagnostics/read|Web Apps 진단 로그 범주를 가져옵니다.|
|/sites/diagnostics/runtimeavailability/read|Web Apps 진단 런타임 가용성을 가져옵니다.|
|/sites/diagnostics/servicehealth/read|Web Apps 진단 서비스 상태를 가져옵니다.|
|/sites/diagnostics/sitecpuanalysis/read|Web Apps 진단 사이트 CPU 분석을 가져옵니다.|
|/sites/diagnostics/sitecrashes/read|Web Apps 진단 사이트 크래시를 가져옵니다.|
|/sites/diagnostics/sitelatency/read|Web Apps 진단 사이트 대기 시간을 가져옵니다.|
|/sites/diagnostics/sitememoryanalysis/read|Web Apps 진단 사이트 메모리 분석을 가져옵니다.|
|/sites/diagnostics/siterestartsettingupdate/read|Web Apps 진단 사이트 다시 시작 설정 업데이트를 가져옵니다.|
|/sites/diagnostics/siterestartuserinitiated/read|Web Apps 진단 사이트 시작 사용자 다시 시작을 가져옵니다.|
|/sites/diagnostics/siteswap/read|Web Apps 진단 사이트 스왑을 가져옵니다.|
|/sites/diagnostics/threadcount/read|Web Apps 진단 사이트 스레드 수를 가져옵니다.|
|/sites/diagnostics/workeravailability/read|Web Apps 진단 Workeravailability를 가져옵니다.|
|/sites/diagnostics/workerprocessrecycle/read|Web Apps 진단 작업자 프로세스 재활용을 가져옵니다.|
|/sites/domainownershipidentifiers/read|Web Apps 도메인 소유권 식별자를 가져옵니다.|
|/sites/domainownershipidentifiers/write|Web Apps 도메인 소유권 식별자를 업데이트합니다.|
|/sites/functions/action|합수 Web Apps입니다.|
|/sites/functions/delete|Web Apps 함수를 삭제합니다.|
|/sites/functions/listsecrets/action|비밀 Web Apps 함수를 나열합니다.|
|/sites/functions/masterkey/read|Web Apps 함수 Masterkey를 가져옵니다.|
|/sites/functions/read|Web Apps 함수를 가져옵니다.|
|/sites/functions/token/read|Web Apps 함수 토큰을 가져옵니다.|
|/sites/functions/write|Web Apps 함수를 업데이트합니다.|
|/sites/hostnamebindings/delete|Web Apps 호스트 이름 바인딩을 삭제합니다.|
|/sites/hostnamebindings/read|Web Apps 호스트 이름 바인딩을 가져옵니다.|
|/sites/hostnamebindings/write|Web Apps 호스트 이름 바인딩을 업데이트합니다.|
|/sites/hybridconnection/delete|Web Apps 하이브리드 연결을 삭제합니다.|
|/sites/hybridconnection/read|Web Apps 하이브리드 연결을 가져옵니다.|
|/sites/hybridconnection/write|Web Apps 하이브리드 연결을 업데이트합니다.|
|/sites/hybridconnectionnamespaces/relays/delete|Web Apps 하이브리드 연결 네임스페이스 릴레이를 삭제합니다.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|키 Web Apps 하이브리드 연결 네임스페이스 릴레이를 나열합니다.|
|/sites/hybridconnectionnamespaces/relays/read|Web Apps 하이브리드 연결 네임스페이스 릴레이를 가져옵니다.|
|/sites/hybridconnectionnamespaces/relays/write|Web Apps 하이브리드 연결 네임스페이스 릴레이를 업데이트합니다.|
|/sites/hybridconnectionrelays/read|Web Apps 하이브리드 연결 릴레이를 가져옵니다.|
|/sites/instances/deployments/delete|Web Apps 인스턴스 배포를 삭제합니다.|
|/sites/instances/deployments/read|Web Apps 인스턴스 배포를 가져옵니다.|
|/sites/instances/extensions/log/read|Web Apps 인스턴스 확장 로그를 가져옵니다.|
|/sites/instances/extensions/read|Web Apps 인스턴스 확장을 가져옵니다.|
|/sites/instances/processes/delete|Web Apps 인스턴스 프로세스를 삭제합니다.|
|/sites/instances/processes/read|Web Apps 인스턴스 프로세스를 가져옵니다.|
|/sites/instances/read|Web Apps 인스턴스를 가져옵니다.|
|/sites/listsyncfunctiontriggerstatus/action|함수 트리거 상태 동기화 Web Apps를 나열합니다.|
|/sites/metricdefinitions/read|Web Apps 메트릭 정의를 가져옵니다.|
|/sites/metrics/read|Web Apps 메트릭을 가져옵니다.|
|/sites/metricsdefinitions/read|Web Apps 메트릭 정의를 가져옵니다.|
|/sites/migratemysql/action|MySql Web Apps를 마이그레이션합니다.|
|/sites/migratemysql/read|Web Apps 마이그레이션 MySql을 가져옵니다.|
|/sites/networktrace/action|네트워크 추적 Web Apps입니다.|
|/sites/newpassword/action|Newpassword Web Apps입니다.|
|/sites/operationresults/read|Web Apps 작업 결과를 가져옵니다.|
|/sites/operations/read|Web Apps 작업을 가져옵니다.|
|/sites/perfcounters/read|Web Apps 성능 카운터를 가져옵니다.|
|/sites/premieraddons/delete|Web Apps 프리미어 추가 기능을 삭제합니다.|
|/sites/premieraddons/read|Web Apps 프리미어 추가 기능을 가져옵니다.|
|/sites/premieraddons/write|Web Apps 프리미어 추가 기능을 업데이트합니다.|
|/sites/processes/read|Web Apps 프로세스를 가져옵니다.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|웹앱에 사용 가능한 메트릭을 가져옵니다.|
|/sites/publiccertificates/delete|Web Apps 공용 인증서를 삭제합니다.|
|/sites/publiccertificates/read|Web Apps 공용 인증서를 가져옵니다.|
|/sites/publiccertificates/write|Web Apps 공용 인증서를 업데이트합니다.|
|/sites/publish/Action|웹앱을 게시합니다.|
|/sites/publishxml/Action|웹앱에 대한 게시 프로필 xml을 가져옵니다.|
|/sites/publishxml/read|Web Apps 게시 XML을 가져옵니다.|
|/sites/Read|웹앱의 속성을 가져옵니다.|
|/sites/recommendationhistory/read|Web Apps 권장 사항 기록을 가져옵니다.|
|/sites/recommendations/disable/action|Web Apps 권장 사항을 사용하지 않도록 설정합니다.|
|/sites/recommendations/Read|웹앱에 대한 권장 사항 목록을 가져옵니다.|
|/sites/recover/action|Web Apps을 복구합니다.|
|/sites/resetSlotConfig/Action|웹앱 구성을 다시 설정합니다.|
|/sites/resourcehealthmetadata/read|Web Apps Resource Health 메타데이터를 가져옵니다.|
|/sites/restart/Action|웹앱을 다시 시작합니다.|
|/sites/restore/read|Web Apps 복원을 가져옵니다.|
|/sites/restore/write|Web Apps을 복원합니다.|
|/sites/siteextensions/delete|Web Apps 사이트 확장을 삭제합니다.|
|/sites/siteextensions/read|Web Apps 사이트 확장을 가져옵니다.|
|/sites/siteextensions/write|Web Apps 사이트 확장을 업데이트합니다.|
|/sites/slots/analyzecustomhostname/read|Web Apps 슬롯 사용자 지정 호스트 이름 분석을 가져옵니다.|
|/sites/slots/applySlotConfig/Action|대상 슬롯의 웹앱 슬롯 구성을 현재 슬롯에 적용합니다.|
|/sites/slots/backup/Action|새 웹앱 슬롯 백업을 만듭니다.|
|/sites/slots/backup/read|Web Apps 슬롯 백업을 가져옵니다.|
|/sites/slots/backup/write|Web Apps 슬롯 백업을 업데이트합니다.|
|/sites/slots/backups/delete|Web Apps 슬롯 백업을 삭제합니다.|
|/sites/slots/backups/list/action|Web Apps 슬롯 백업을 나열합니다.|
|/sites/slots/backups/Read|웹앱 슬롯 백업의 속성을 가져옵니다.|
|/sites/slots/backups/restore/action|Web Apps 슬롯 백업을 복원합니다.|
|/sites/slots/config/delete|Web Apps 슬롯 구성을 삭제합니다.|
|/sites/slots/config/list/Action|게시 자격 증명, 앱 설정 및 연결 문자열과 같은 웹앱 슬롯 보안 중요 설정을 나열합니다.|
|/sites/slots/config/Read|웹앱 슬롯의 구성 설정을 가져옵니다.|
|/sites/slots/config/Write|웹앱 슬롯의 구성 설정을 업데이트합니다.|
|/sites/slots/continuouswebjobs/delete|Web Apps 슬롯 연속 웹 작업을 삭제합니다.|
|/sites/slots/continuouswebjobs/read|Web Apps 슬롯 연속 웹 작업을 가져옵니다.|
|/sites/slots/continuouswebjobs/start/action|Web Apps 슬롯 연속 웹 작업을 시작합니다.|
|/sites/slots/continuouswebjobs/stop/action|Web Apps 슬롯 연속 웹 작업을 중지합니다.|
|/sites/slots/Delete|기존 웹앱 슬롯을 삭제합니다.|
|/sites/slots/deployments/delete|Web Apps 슬롯 배포를 삭제합니다.|
|/sites/slots/deployments/log/read|Web Apps 슬롯 배포 로그를 가져옵니다.|
|/sites/slots/deployments/read|Web Apps 슬롯 배포를 가져옵니다.|
|/sites/slots/deployments/write|Web Apps 슬롯 배포를 업데이트합니다.|
|/sites/slots/diagnostics/analyses/execute/Action|Web Apps 슬롯 진단 분석을 실행합니다.|
|/sites/slots/diagnostics/analyses/read|Web Apps 슬롯 진단 분석을 가져옵니다.|
|/sites/slots/diagnostics/aspnetcore/read|ASP.NET Core 앱용 Web Apps 슬롯 진단을 가져옵니다.|
|/sites/slots/diagnostics/autoheal/read|Web Apps 슬롯 진단 Autoheal을 가져옵니다.|
|/sites/slots/diagnostics/deployment/read|Web Apps 슬롯 진단 배포를 가져옵니다.|
|/sites/slots/diagnostics/deployments/read|Web Apps 슬롯 진단 배포를 가져옵니다.|
|/sites/slots/diagnostics/detectors/execute/Action|Web Apps 슬롯 진단 감지기를 실행합니다.|
|/sites/slots/diagnostics/detectors/read|Web Apps 슬롯 진단 감지기를 가져옵니다.|
|/sites/slots/diagnostics/frebanalysis/read|Web Apps 슬롯 진단 FREB 분석을 가져옵니다.|
|/sites/slots/diagnostics/loganalyzer/read|Web Apps 슬롯 진단 로그 분석기를 가져옵니다.|
|/sites/slots/diagnostics/read|Web Apps 슬롯 진단을 가져옵니다.|
|/sites/slots/diagnostics/runtimeavailability/read|Web Apps 슬롯 진단 런타임 가용성을 가져옵니다.|
|/sites/slots/diagnostics/servicehealth/read|Web Apps 슬롯 진단 서비스 상태를 가져옵니다.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Web Apps 슬롯 진단 사이트 CPU 분석을 가져옵니다.|
|/sites/slots/diagnostics/sitecrashes/read|Web Apps 슬롯 진단 사이트 크래시를 가져옵니다.|
|/sites/slots/diagnostics/sitelatency/read|Web Apps 슬롯 진단 사이트 대기 시간을 가져옵니다.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Web Apps 슬롯 진단 사이트 메모리 분석을 가져옵니다.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Web Apps 슬롯 진단 사이트 다시 시작 설정 업데이트를 가져옵니다.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Web Apps 슬롯 진단 사이트 시작된 사용자 다시 시작을 가져옵니다.|
|/sites/slots/diagnostics/siteswap/read|Web Apps 슬롯 진단 사이트 스왑을 가져옵니다.|
|/sites/slots/diagnostics/threadcount/read|Web Apps 슬롯 진단 스레드 수를 가져옵니다.|
|/sites/slots/diagnostics/workeravailability/read|Web Apps 슬롯 진단 Workeravailability를 가져옵니다.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Web Apps 슬롯 진단 작업자 프로세스 재활용을 가져옵니다.|
|/sites/slots/domainownershipidentifiers/read|Web Apps 슬롯 도메인 소유권 식별자를 가져옵니다.|
|/sites/slots/hostnamebindings/delete|Web Apps 슬롯 호스트 이름 바인딩을 삭제합니다.|
|/sites/slots/hostnamebindings/read|Web Apps 슬롯 호스트 이름 바인딩을 가져옵니다.|
|/sites/slots/hostnamebindings/write|Web Apps 슬롯 호스트 이름 바인딩을 업데이트합니다.|
|/sites/slots/hybridconnection/delete|Web Apps 슬롯 하이브리드 연결을 삭제합니다.|
|/sites/slots/hybridconnection/read|Web Apps 슬롯 하이브리드 연결을 가져옵니다.|
|/sites/slots/hybridconnection/write|Web Apps 슬롯 하이브리드 연결을 업데이트합니다.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Web Apps 슬롯 하이브리드 연결 네임스페이스 릴레이를 삭제합니다.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Web Apps 슬롯 하이브리드 연결 네임스페이스 릴레이를 업데이트합니다.|
|/sites/slots/hybridconnectionrelays/read|Web Apps 슬롯 하이브리드 연결 릴레이를 가져옵니다.|
|/sites/slots/instances/deployments/read|Web Apps 슬롯 인스턴스 배포를 가져옵니다.|
|/sites/slots/instances/processes/delete|Web Apps 슬롯 인스턴스 프로세스를 삭제합니다.|
|/sites/slots/instances/processes/read|Web Apps 슬롯 인스턴스 프로세스를 가져옵니다.|
|/sites/slots/instances/read|Web Apps 슬롯 인스턴스를 가져옵니다.|
|/sites/slots/metricdefinitions/read|Web Apps 슬롯 메트릭 정의를 가져옵니다.|
|/sites/slots/metrics/read|Web Apps 슬롯 메트릭을 가져옵니다.|
|/sites/slots/migratemysql/read|Web Apps 슬롯 마이그레이션 MySql을 가져옵니다.|
|/sites/slots/networktrace/action|네트워크 추적 Web Apps 슬롯입니다.|
|/sites/slots/newpassword/action|Newpassword Web Apps 슬롯입니다.|
|/sites/slots/operationresults/read|Web Apps 슬롯 작업 결과를 가져옵니다.|
|/sites/slots/operations/read|Web Apps 슬롯 작업을 가져옵니다.|
|/sites/slots/perfcounters/read|Web Apps 슬롯 성능 카운터를 가져옵니다.|
|/sites/slots/phplogging/read|Web Apps 슬롯 Phplogging을 가져옵니다.|
|/sites/slots/premieraddons/delete|Web Apps 슬롯 프리미어 추가 기능을 삭제합니다.|
|/sites/slots/premieraddons/read|Web Apps 슬롯 프리미어 추가 기능을 가져옵니다.|
|/sites/slots/premieraddons/write|Web Apps 슬롯 프리미어 추가 기능을 업데이트합니다.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|리소스에 대한 진단 설정을 가져옵니다.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|리소스에 대한 진단 설정을 만들거나 업데이트합니다.|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|웹앱 슬롯에 사용 가능한 메트릭을 가져옵니다.|
|/sites/slots/publiccertificates/read|Web Apps 슬롯 공용 인증서를 가져옵니다.|
|/sites/slots/publiccertificates/write|Web Apps 슬롯 공용 인증서를 만들거나 업데이트합니다.|
|/sites/slots/publish/Action|웹앱 슬롯을 게시합니다.|
|/sites/slots/publishxml/Action|웹앱 슬롯에 대한 게시 프로필 xml을 가져옵니다.|
|/sites/slots/Read|웹앱 배포 슬롯의 속성을 가져옵니다.|
|/sites/slots/resetSlotConfig/Action|웹앱 슬롯 구성을 다시 설정합니다.|
|/sites/slots/resourcehealthmetadata/read|Web Apps 슬롯 Resource Health 메타데이터를 가져옵니다.|
|/sites/slots/restart/Action|웹앱 슬롯을 다시 시작합니다.|
|/sites/slots/restore/read|Web Apps 슬롯 복원을 가져옵니다.|
|/sites/slots/restore/write|Web Apps 슬롯을 복원합니다.|
|/sites/slots/siteextensions/delete|Web Apps 슬롯 사이트 확장을 삭제합니다.|
|/sites/slots/siteextensions/read|Web Apps 슬롯 사이트 확장을 가져옵니다.|
|/sites/slots/siteextensions/write|Web Apps 슬롯 사이트 확장을 업데이트합니다.|
|/sites/slots/slotsdiffs/Action|웹앱 및 슬롯 간 구성 차이를 가져옵니다.|
|/sites/slots/slotsswap/Action|웹앱 배포 슬롯을 교환합니다.|
|/sites/slots/snapshots/read|Web Apps 슬롯 스냅숏을 가져옵니다.|
|/sites/slots/sourcecontrols/Delete|웹앱 슬롯의 소스 제어 구성 설정을 삭제합니다.|
|/sites/slots/sourcecontrols/Read|웹앱 슬롯의 소스 제어 구성 설정을 가져옵니다.|
|/sites/slots/sourcecontrols/Write|웹앱 슬롯의 소스 제어 구성 설정을 업데이트합니다.|
|/sites/slots/start/Action|웹앱 슬롯을 시작합니다.|
|/sites/slots/stop/Action|웹앱 슬롯을 중지합니다.|
|/sites/slots/sync/action|Web Apps 슬롯을 동기화합니다.|
|/sites/slots/triggeredwebjobs/delete|Web Apps 슬롯 트리거 Webjob을 삭제합니다.|
|/sites/slots/triggeredwebjobs/read|Web Apps 슬롯 트리거 Webjob을 가져옵니다.|
|/sites/slots/triggeredwebjobs/run/action|Web Apps 슬롯 트리거 Webjob을 실행합니다.|
|/sites/slots/usages/read|Web Apps 슬롯 사용 현황을 가져옵니다.|
|/sites/slots/virtualnetworkconnections/delete|Web Apps 슬롯 Virtual Network 연결을 삭제합니다.|
|/sites/slots/virtualnetworkconnections/gateways/write|Web Apps 슬롯 Virtual Network 연결 게이트웨이를 업데이트합니다.|
|/sites/slots/virtualnetworkconnections/read|Web Apps 슬롯 Virtual Network 연결을 가져옵니다.|
|/sites/slots/virtualnetworkconnections/write|Web Apps 슬롯 Virtual Network 연결을 업데이트합니다.|
|/sites/slots/webjobs/read|Web Apps 슬롯 Webjob을 가져옵니다.|
|/sites/slots/Write|새 웹앱 슬롯을 만들거나 기존 웹앱 슬롯을 업데이트합니다.|
|/sites/slotsdiffs/Action|웹앱 및 슬롯 간 구성 차이를 가져옵니다.|
|/sites/slotsswap/Action|웹앱 배포 슬롯을 교환합니다.|
|/sites/snapshots/read|Web Apps 스냅숏을 가져옵니다.|
|/sites/sourcecontrols/Delete|웹앱의 소스 제어 구성 설정을 삭제합니다.|
|/sites/sourcecontrols/Read|웹앱의 소스 제어 구성 설정을 가져옵니다.|
|/sites/sourcecontrols/Write|웹앱의 소스 제어 구성 설정을 업데이트합니다.|
|/sites/start/Action|웹앱을 시작합니다.|
|/sites/stop/Action|웹앱을 중지합니다.|
|/sites/sync/action|Web Apps를 동기화합니다.|
|/sites/syncfunctiontriggers/action|Web Apps에 대한 함수 트리거를 동기화합니다.|
|/sites/triggeredwebjobs/delete|Web Apps 트리거 Webjob을 삭제합니다.|
|/sites/triggeredwebjobs/history/read|Web Apps 트리거 WebJobs 기록을 가져옵니다.|
|/sites/triggeredwebjobs/read|Web Apps 트리거 Webjob을 가져옵니다.|
|/sites/triggeredwebjobs/run/action|Web Apps 트리거 Webjob을 실행합니다.|
|/sites/usages/read|Web Apps 사용 현황을 가져옵니다.|
|/sites/virtualnetworkconnections/delete|Web Apps Virtual Network 연결을 삭제합니다.|
|/sites/virtualnetworkconnections/gateways/read|Web Apps Virtual Network 연결 게이트웨이를 가져옵니다.|
|/sites/virtualnetworkconnections/gateways/write|Web Apps Virtual Network 연결 게이트웨이를 업데이트합니다.|
|/sites/virtualnetworkconnections/read|Web Apps Virtual Network 연결을 가져옵니다.|
|/sites/virtualnetworkconnections/write|Web Apps Virtual Network 연결을 업데이트합니다.|
|/sites/webjobs/read|Web Apps Webjob을 가져옵니다.|
|/sites/Write|새 웹앱을 만들거나 기존 웹앱을 업데이트합니다.|
|/skus/read|SKU를 가져옵니다.|
|/sourcecontrols/read|소스 제어를 가져옵니다.|
|/sourcecontrols/write|소스 제어를 업데이트합니다.|
|/unregister/action|구독에 대한 Microsoft.Web 리소스 공급자를 등록 취소합니다.|
|/validate/action|유효성 검사를 수행합니다.|
|/verifyhostingenvironmentvnet/action|호스팅 환경 Vnet을 확인합니다.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| 작업 | 설명 |
|---|---|
|/components/read|작업 리소스를 읽습니다.|
|/healthInstances/read|작업 리소스를 읽습니다.|
|/Operations/read|작업 리소스를 읽습니다.|
|/workloads/delete|워크로드 리소스를 삭제합니다.|
|/workloads/read|워크로드 리소스를 읽습니다.|
|/workloads/write|워크로드 리소스를 씁니다.|

## <a name="next-steps"></a>다음 단계

- [사용자 지정 역할을 만드는](custom-roles.md) 방법을 알아봅니다.
- [기본 제공 RBAC 역할](built-in-roles.md)을 검토합니다.
- [사용자](role-assignments-users.md) 또는 [리소스](role-assignments-portal.md)를 기준으로 액세스 할당을 관리하는 방법을 알아봅니다. 
- [리소스에 대한 작업을 감사하기 위해 활동 로그를 보는](~/articles/azure-resource-manager/resource-group-audit.md) 방법을 알아봅니다.
