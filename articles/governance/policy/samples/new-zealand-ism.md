---
title: 뉴질랜드 ISM 제한됨에 대한 규정 준수 세부 정보
description: 뉴질랜드 ISM 제한됨 규정 준수 기본 제공 이니셔티브의 세부 정보. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure Policy 정의에 매핑됩니다.
ms.date: 03/05/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 5f346f7755cf252ed15513d8dc0a60a9bc5cce3f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437249"
---
# <a name="details-of-the-new-zealand-ism-restricted-regulatory-compliance-built-in-initiative"></a>뉴질랜드 ISM 제한됨 규정 준수 기본 제공 이니셔티브의 세부 정보

다음 문서에서는 Azure Policy 규정 준수 기본 제공 이니셔티브 정의가 뉴질랜드 ISM 제한됨의 **규정 준수 도메인** 및 **컨트롤** 에 어떻게 대응하는지 자세히 설명합니다.
이 규정 준수 표준에 대한 자세한 내용은 [뉴질랜드 ISM 제한됨](https://www.nzism.gcsb.govt.nz/)을 참조하세요. _소유권_ 을 이해하려면 [Azure Policy 정책 정의](../concepts/definition-structure.md#type) 및 [클라우드의 공동 책임](../../../security/fundamentals/shared-responsibility.md)을 참조하세요.

다음 매핑은 **뉴질랜드 ISM 제한됨** 컨트롤에 대한 것입니다. 특정 **규정 준수 도메인** 으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 여러 컨트롤이 [Azure Policy](../overview.md) 이니셔티브 정의를 사용하여 구현됩니다. 전체 이니셔티브 정의를 검토하려면 Azure Portal에서 **정책** 을 열고 **정의** 페이지를 선택합니다.
그런 다음, **뉴질랜드 정보 보안 설명서** 규정 준수 기본 제공 이니셔티브 정의를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../overview.md) 정의와 연결되어 있습니다.
> 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 정의 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 표준에 대한 규정 준수 도메인, 컨트롤, Azure Policy 정의 간의 연결은 시간이 지나면 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/nz_ism.json)을 참조하세요.

## <a name="information-security-monitoring"></a>정보 보안 모니터링

### <a name="625-conducting-vulnerability-assessments"></a>6.2.5 취약성 평가 수행

**ID**: NZISM 보안 벤치마크 ISM-3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |가상 머신을 감사하여 지원되는 취약성 평가 솔루션을 실행하고 있는지 검색합니다. 모든 사이버 위험 및 보안 프로그램의 핵심 구성 요소는 취약성을 식별하고 분석하는 것입니다. Azure Security Center의 표준 가격 책정 계층에는 추가 비용 없이 가상 머신에 대한 취약성 검사가 포함됩니다. 또한 Security Center가 자동으로 도구를 배포할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[컨테이너 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker가 설치된 머신에서 보안 구성의 취약성을 감사하고 Azure Security Center에서 권장 사항으로 표시합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL 데이터베이스의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[SQL 서버에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |취약점 평가 설정에서 '검사 보고서 전송 대상' 필드에 이메일 주소가 지정되었는지 확인합니다. 이 이메일 주소는 SQL 서버에 대한 정기적인 검사기 실행된 후 검사 결과 요약을 받습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 Azure SQL 서버를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

## <a name="product-security"></a>제품 보안

### <a name="1244-patching-vulnerabilities-in-products"></a>12.4.4 제품의 취약성 패치

**ID**: NZISM 보안 벤치마크 PRS-5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |모니터링되지 않는 액세스를 방지하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Windows 및 Linux 가상 머신 확장 집합의 보안 유지를 위해 설치해야 하는 시스템 보안 업데이트 및 중요 업데이트가 누락되었는지 감사합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[시스템 업데이트를 머신에 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="software-security"></a>소프트웨어 보안

### <a name="1419-maintaining-hardened-soes"></a>14.1.9 확정된 SOE 유지 관리

**ID**: NZISM 보안 벤치마크 SS-2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 가상 머신 확장 집합용 Dependency Agent 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3be22e3b-d919-47aa-805e-8985dbeb0ad9) |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되어 있지 않은 경우 Windows 가상 머신 확장 집합용 Dependency Agent를 배포합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. 참고: 확장 집합 upgradePolicy를 수동으로 설정한 경우 이에 대한 업그레이드를 호출하여 집합의 모든 가상 머신에 확장을 적용해야 합니다. CLI에서는 az vmss update-instances입니다. |deployIfNotExists |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VMSS_Deploy.json) |
|[가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |가상 머신 확장 집합에서 엔드포인트 보호 솔루션의 존재 및 상태를 감사하여 위협 및 취약성으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft IaaSAntimalware 확장을 Windows Server에 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b597639-28e4-48eb-b506-56b05d366257) |이 정책은 Microsoft IaaSAntimalware 확장이 배포되지 않은 Windows Server VM을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="1424-application-whitelisting"></a>14.2.4 애플리케이션 허용 목록

**ID**: NZISM 보안 벤치마크 SS-4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="1458-web-applications"></a>14.5.8 웹 애플리케이션

**ID**: NZISM 보안 벤치마크 SS-8 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="access-control-and-passwords"></a>액세스 제어 및 암호

### <a name="16132-system-user-identification"></a>16.1.32 시스템 사용자 ID

**ID**: NZISM 보안 벤치마크 AC-2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[암호 없이 계정에서 원격 연결을 허용하는 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 암호 없이 계정에서 원격 연결을 허용하는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[암호가 없는 계정이 있는 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 암호가 없는 계정이 있는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[관리자 그룹에 지정된 구성원이 없는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함되어 있지 않은 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[관리자 그룹에 추가 계정이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열되지 않은 구성원이 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[관리자 그룹에 지정된 구성원이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Windows 가상 머신용 종속성 에이전트 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Windows 가상 머신용 종속성 에이전트를 배포합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. |deployIfNotExists |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 구성 확장은 모든 Linux 게스트 구성 할당에 대한 필수 구성 요소이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 구성 확장은 모든 Windows 게스트 구성 할당에 대한 필수 구성 요소이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="16135-methods-for-system-user-identification-and-authentication"></a>16.1.35 시스템 사용자 식별 및 인증 방법

**ID**: NZISM 보안 벤치마크 AC-3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="16140-password-selection-policy"></a>16.1.40 암호 선택 정책

**ID**: NZISM 보안 벤치마크 AC-4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 가상 머신용 종속성 에이전트 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Windows 가상 머신용 종속성 에이전트를 배포합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. |deployIfNotExists |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[Windows 머신은 '보안 설정 - 계정 정책'에 대한 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2143251-70de-4e81-87a8-36cee5a2f29d) |Windows 머신에서 암호 기록, 사용 기간, 길이, 복잡성 및 해독 가능한 암호화를 사용한 암호를 저장하려면 '보안 설정 - 계정 정책' 범주에 지정된 그룹 정책 설정이 있어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecuritySettingsAccountPolicies_AINE.json) |

### <a name="16146-suspension-of-access"></a>16.1.46 액세스 일시 중단

**ID**: NZISM 보안 벤치마크 AC-5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 가상 머신용 종속성 에이전트 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Windows 가상 머신용 종속성 에이전트를 배포합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. |deployIfNotExists |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="1625-protecting-compartmented-information-on-systems"></a>16.2.5 시스템에서 분리된 정보 보호

**ID**: NZISM 보안 벤치마크 AC-7 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="1635-use-of-privileged-accounts"></a>16.3.5 권한 있는 계정 사용

**ID**: NZISM 보안 벤치마크 AC-9 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리자 그룹에 지정된 구성원이 없는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함되어 있지 않은 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[관리자 그룹에 추가 계정이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열되지 않은 구성원이 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[관리자 그룹에 지정된 구성원이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Windows 가상 머신용 종속성 에이전트 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c210e94-a481-4beb-95fa-1571b434fb04) |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Windows 가상 머신용 종속성 에이전트를 배포합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. |deployIfNotExists |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VM_Deploy.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 구성 확장은 모든 Windows 게스트 구성 할당에 대한 필수 구성 요소이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |

### <a name="1666-maintaining-system-management-logs"></a>16.6.6 시스템 관리 로그 유지 관리

**ID**: NZISM 보안 벤치마크 AC-13 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 구독에는 활동 로그에 대한 로그 프로필이 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |이 정책은 로그 프로필이 활동 로그를 내보낼 수 있는지 확인합니다. 로그를 스토리지 계정 또는 이벤트 허브로 내보내기 위해 생성된 로그 프로필이 없는지 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="1667-content-of-system-management-logs"></a>16.6.7 시스템 관리 로그 내용

**ID**: NZISM 보안 벤치마크 AC-14 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[미리 보기\]: Log Analytics 에이전트 배포 감사 - 목록에 없는 VM 이미지(OS)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |VM 이미지(OS)가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 VM을 비준수로 보고합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. |auditIfNotExists |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[진단 설정 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |선택한 리소스 종류에 대한 감사 진단 설정 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[가상 머신 확장 집합에서 Log Analytics 에이전트 배포 감사 - VM 이미지(OS)가 나열 취소됨](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |VM 이미지(OS)가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비준수로 보고합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[VM에 대한 Log Analytics 작업 영역 감사 - 불일치 보고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |정책/이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로깅하고 있지 않은 경우 VM을 비준수로 보고합니다. |감사 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |

### <a name="16613-event-log-archives"></a>16.6.13 이벤트 로그 보관

**ID**: NZISM 보안 벤치마크 AC-15 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[활동 로그는 1년 이상 보존되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |이 정책은 보존이 365일 또는 계속(보존 기간이 0으로 설정)으로 설정되지 않은 경우 활동 로그를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="16734-system-architecture-and-security-controls"></a>16.7.34 시스템 아키텍처 및 보안 제어

**ID**: NZISM 보안 벤치마크 AC-17 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

## <a name="cryptography"></a>암호화

### <a name="17146-reducing-storage-and-physical-transfer-requirements"></a>17.1.46 스토리지 및 물리적 전송 요구 사항 감소

**ID**: NZISM 보안 벤치마크 CR-2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에서 디스크 암호화를 적용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |디스크 암호화를 사용하도록 설정되지 않은 가상 머신은 추천 사항으로 Azure Security Center에서 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="17416-using-tls"></a>17.4.16 TLS 사용

**ID**: NZISM 보안 벤치마크 CR-6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 구성 확장은 모든 Windows 게스트 구성 할당에 대한 필수 구성 요소이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](https://aka.ms/gcpol)을 방문하세요. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Windows 웹 서버는 보안 통신 프로토콜을 사용하도록 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |인터넷을 통해 전달되는 정보의 개인 정보를 보호하려면 웹 서버에서 최신 버전의 업계 표준 암호화 프로토콜인 TLS(전송 계층 보안)를 사용해야 합니다. TLS는 보안 인증서를 사용하여 컴퓨터 간의 연결을 암호화하여 네트워크를 통한 통신을 보호합니다. TLS 1.3은 모두 레거시 프로토콜로 간주되는 이전 버전(TLS 1.0-1.2 및 SSL 2-3)보다 빠르고 안전합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

## <a name="network-security"></a>네트워크 보안

### <a name="18113-limiting-network-access"></a>18.1.13 네트워크 액세스 제한

**ID**: NZISM 보안 벤치마크 NS-2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[스토리지 계정 공용 액세스가 허용되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage의 컨테이너 및 BLOB에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 원치 않는 익명 액세스로 인해 발생하는 데이터 위반을 방지하기 위해 Microsoft는 시나리오에 필요한 경우가 아니면 스토리지 계정에 대한 공개 액세스를 방지하는 것이 좋습니다 |감사, 거부, 사용 안 함 |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="18319-content-of-a-denial-of-service-dos-response-plan"></a>18.3.19 DoS(서비스 거부 공격) 대응 계획의 내용

**ID**: NZISM 보안 벤치마크 NS-5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection 표준을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |공용 IP를 사용하는 애플리케이션 게이트웨이의 일부인 서브넷이 포함된 모든 가상 네트워크에 DDoS 보호 표준을 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |

### <a name="18410-malicious-code-counter-measures"></a>18.4.10 악성 코드 대응책

**ID**: NZISM 보안 벤치마크 NS-7 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center에서 일부 서브넷이 차세대 방화벽으로 보호되지 않는 것으로 확인되었습니다. Azure Firewall 또는 지원되는 차세대 방화벽으로 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[WAF(Web Application Firewall)를 Azure Front Door Service 서비스에 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |
|[WAF(웹 애플리케이션 방화벽)는 Application Gateway에 지정된 모드를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12430be1-6cc8-4527-a9a8-e3d38f250096) |Application Gateway에 대한 모든 웹 애플리케이션 방화벽 정책에서 '검색' 또는 '방지' 모드를 사용해야 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayMode_Audit.json) |
|[WAF(웹 애플리케이션 방화벽)는 Azure Front Door Service에 지정된 모드를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F425bea59-a659-4cbb-8d31-34499bd030b8) |Azure Front Door Service에 대한 모든 웹 애플리케이션 방화벽 정책에서 '검색' 또는 '방지' 모드를 사용해야 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Mode_Audit.json) |

## <a name="data-management"></a>데이터 관리

### <a name="20310-antivirus-scans"></a>20.3.10 바이러스 백신 검사

**ID**: NZISM 보안 벤치마크 DM-4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 가상 머신 확장 집합용 Dependency Agent 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3be22e3b-d919-47aa-805e-8985dbeb0ad9) |VM 이미지(OS)가 정의된 목록에 있고 에이전트가 설치되어 있지 않은 경우 Windows 가상 머신 확장 집합용 Dependency Agent를 배포합니다. OS 이미지 목록은 향후 지원이 업데이트되면 업데이트됩니다. 참고: 확장 집합 upgradePolicy를 수동으로 설정한 경우 이에 대한 업그레이드를 호출하여 집합의 모든 가상 머신에 확장을 적용해야 합니다. CLI에서는 az vmss update-instances입니다. |deployIfNotExists |[1.3.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DependencyAgentExtension_Windows_VMSS_Deploy.json) |
|[가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |가상 머신 확장 집합에서 엔드포인트 보호 솔루션의 존재 및 상태를 감사하여 위협 및 취약성으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard는 Azure Policy 게스트 구성 에이전트를 사용합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 네 가지 구성 요소가 있습니다(Windows에만 해당). |AuditIfNotExists, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="2044-database-files"></a>20.4.4 데이터베이스 파일

**ID**: NZISM 보안 벤치마크 DM-6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[진단 설정 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |선택한 리소스 종류에 대한 감사 진단 설정 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[가상 머신에서 디스크 암호화를 적용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |디스크 암호화를 사용하도록 설정되지 않은 가상 머신은 추천 사항으로 Azure Security Center에서 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[Windows 웹 서버는 보안 통신 프로토콜을 사용하도록 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |인터넷을 통해 전달되는 정보의 개인 정보를 보호하려면 웹 서버에서 최신 버전의 업계 표준 암호화 프로토콜인 TLS(전송 계층 보안)를 사용해야 합니다. TLS는 보안 인증서를 사용하여 컴퓨터 간의 연결을 암호화하여 네트워크를 통한 통신을 보호합니다. TLS 1.3은 모두 레거시 프로토콜로 간주되는 이전 버전(TLS 1.0-1.2 및 SSL 2-3)보다 빠르고 안전합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

## <a name="enterprise-systems-security"></a>엔터프라이즈 시스템 보안

### <a name="22122-offshore-services"></a>22.1.22 역외 서비스

**ID**: NZISM 보안 벤치마크 ESS-2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[허용되는 위치](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |이 정책을 사용하면 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 지역 규정 준수 요구 사항을 적용하는 데 사용합니다. 리소스 그룹, Microsoft.AzureActiveDirectory/b2cDirectories 및 '글로벌' 지역을 사용하는 리소스를 제외합니다. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |
|[리소스 그룹에 허용된 위치](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |이 정책을 통해 조직에서 리소스 그룹을 만들 수 있는 위치를 제한할 수 있습니다. 지역 규정 준수 요구 사항을 적용하는 데 사용합니다. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |

### <a name="22126-backup-recovery-archiving-and-data-remanence"></a>22.1.26 백업, 복구 보관 및 데이터 다시 작성

**ID**: NZISM 보안 벤치마크 ESS-3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> 특정 Azure Policy 정의의 가용성은 Azure Government 및 기타 국가 클라우드에 따라 다를 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Policy에 대한 추가 문서:

- [규정 준수](../concepts/regulatory-compliance.md) 개요
- [이니셔티브 정의 구조](../concepts/initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](./index.md)의 다른 예제를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
