---
title: Azure Security Center에 대한 릴리스 정보
description: Azure Security Center의 새로운 기능과 변경된 기능에 대한 설명입니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2020
ms.author: memildin
ms.openlocfilehash: 6de06e4ab9a302517a09b34de56c1b5535b245a9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93357813"
---
# <a name="whats-new-in-azure-security-center"></a>Azure Security Center의 새로운 기능

Security Center는 현재 개발 중이며 지속적으로 향상된 기능을 수용합니다. 이 페이지에서는 최신 개발 정보를 제공하기 위해 새로운 기능, 버그 수정 및 사용되지 않는 기능에 대한 정보를 제공합니다.

이 페이지는 자주 업데이트되므로 자주 다시 방문하세요. 

곧 Security Center에 적용되는 *계획된* 변경 내용에 대한 자세한 내용은 [Azure Security Center의 예정된 중요 변경 내용](upcoming-changes.md)을 참조하세요. 

> [!TIP]
> 6개월 이상된 항목을 찾으려는 경우 [Azure Security Center의 새로운 기능 아카이브](release-notes-archive.md)에서 찾을 수 있습니다.


## <a name="november-2020"></a>2020년 11월

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Azure 보안 벤치마크의 적용 범위를 늘리기 위해 29개의 미리 보기 권장 사항 추가됨

Azure 보안 벤치마크는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. [Azure 보안 벤치마크에 대해 자세히 알아보세요](../security/benchmarks/introduction.md).

이 벤치마크의 적용 범위를 넓히기 위해 다음 29개의 새로운 추천 사항이 Security Center에 추가되었습니다.

미리 보기 추천 사항은 리소스를 비정상으로 렌더링하지 않으며 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 끝나면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정합니다. [Azure Security Center의 추천 사항 수정](security-center-remediate-recommendations.md)에서 이러한 추천 사항에 대응하는 방법에 대해 자세히 알아보세요.

| 보안 컨트롤                     | 새로운 권장 사항                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 전송 중인 데이터 암호화              | - PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br>- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br>- TLS를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- TLS를 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- TLS를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- API 앱에서 FTPS를 요구해야 합니다.<br>- 함수 앱에서 FTPS를 요구해야 합니다.<br>- 웹앱에서 FTPS를 요구해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 액세스 및 사용 권한 관리        | - 웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다.<br>- API 앱에서 관리 ID를 사용해야 합니다.<br>- 함수 앱에서 관리 ID를 사용해야 합니다.<br>- 웹앱에서 관리 ID를 사용해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 무단 네트워크 액세스 제한 | - 프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.<br>- 프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.<br>- 프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 감사 및 로깅 사용          | - App Services에서 진단 로그를 사용하도록 설정해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 보안 모범 사례 구현    | - Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.<br>- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br>- PHP를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- PHP를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 API 앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- Java를 최신 웹앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 API 앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 함수 앱 버전으로 업데이트해야 합니다.<br>- Python을 최신 웹앱 버전으로 업데이트해야 합니다.<br>- SQL 서버에 대한 감사 보존 기간은 90일 이상으로 설정해야 합니다. |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

관련 링크:

- [Azure 보안 벤치마크에 대한 자세한 정보](../security/benchmarks/introduction.md)
- [Azure API 앱에 대한 자세한 정보](../app-service/app-service-web-tutorial-rest-api.md)
- [Azure 함수 앱에 대한 자세한 정보](../azure-functions/functions-overview.md)
- [Azure 웹앱에 대한 자세한 정보](../app-service/overview.md)
- [Azure Database for MariaDB에 대한 자세한 정보](../mariadb/overview.md)
- [Azure Database for MySQL에 대한 자세한 정보](../mysql/overview.md)
- [Azure Database for PostgreSQL에 대한 자세한 정보](../postgresql/overview.md)




## <a name="october-2020"></a>2020년 10월

- [온-프레미스 및 다중 클라우드 머신의 취약성 평가(미리 보기)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall 권장 사항 추가(미리 보기)](#azure-firewall-recommendation-added-preview)
- [[Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항의 빠른 픽스가 업데이트됨](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [이제 규정 준수 대시보드에는 표준을 제거하는 옵션이 포함됨](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [ARG(Azure Resource Graph)에서 Microsoft.Security/securityStatuses 테이블이 제거됨](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>온-프레미스 및 다중 클라우드 머신의 취약성 평가(미리 보기)

[서버용 Azure Defender](defender-for-servers-introduction.md)의 통합 취약성 평가 스캐너(Qualys 기반)는 이제 Azure Arc 사용 서버를 검색합니다.

Azure가 아닌 머신에서 Azure Arc를 사용하도록 설정한 경우 Security Center는 이러한 머신에 통합 취약성 스캐너를 수동 및 대규모로 배포할 수 있습니다.

이번 업데이트를 통해 모든 Azure 및 비-Azure 자산의 취약성 관리 프로그램을 통합하는 **서버용 Azure Defender** 의 강력한 성능을 발휘할 수 있습니다.

주요 기능:

- Azure Arc 머신의 VA(취약성 평가) 스캐너 프로비저닝 상태 모니터링
- 보호되지 않는 Windows 및 Linux Azure Arc 머신에 통합 VA 에이전트 프로비저닝(수동 및 대규모로)
- 배포된 에이전트에서 탐지된 취약성 수신 및 분석(수동 및 대규모로)
- Azure VM 및 Azure Arc 머신에 대한 통합 환경

[하이브리드 머신에 통합 취약성 스캐너를 배포하는 내용을 자세히 알아보세요](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Azure Arc 사용 서버에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall 권장 사항 추가(미리 보기)

Azure Firewall을 통해 모든 가상 네트워크를 보호하기 위해 새로운 권장 사항이 추가되었습니다.

**가상 네트워크는 Azure Firewall로 보호해야 합니다** 권장 사항은 Azure Firewall을 사용하여 가상 네트워크에 대한 액세스를 제한하고 잠재적 위협을 차단할 것을 조언합니다.

[Azure Firewall](https://azure.microsoft.com/services/azure-firewall/)에 대해 자세히 알아보세요.


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>[Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항의 빠른 픽스가 업데이트됨

**Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함** 권장 사항은 이제 빠른 픽스 옵션을 제공합니다.

이 권장 사항 및 그 밖의 모든 Security Center 권장 사항에 대한 자세한 내용은 [보안 권장 사항 - 참조 가이드](recommendations-reference.md)를 확인하세요.

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="빠른 픽스 옵션을 제공하는 [Kubernetes 서비스에서 권한 있는 IP 범위를 정의해야 함] 권장 사항":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>이제 규정 준수 대시보드에는 표준을 제거하는 옵션이 포함됨

Security Center의 규정 준수 대시보드는 특정 규정 준수 제어 및 요구 사항을 충족하는 방법에 따라 규정 준수 상태에 대한 인사이트를 제공합니다.

대시보드에는 규정 표준의 기본 세트가 포함되어 있습니다. 제공된 표준이 조직과 관련이 없는 경우 이제 구독에 대한 UI에서 해당 표준을 간단히 제거할 수 있는 간단한 프로세스입니다. 표준은 관리 그룹 범위가 아닌 *구독* 수준에서만 제거할 수 있습니다.

[대시보드에서 표준 제거](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard)에서 자세히 알아봅니다.


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>ARG(Azure Resource Graph)에서 Microsoft.Security/securityStatuses 테이블이 제거됨

Azure Resource Graph는 작업 환경을 효과적으로 관리할 수 있도록 특정 구독 세트에서 대규모로 쿼리를 수행할 수 있는 효율적인 리소스 탐색 기능을 제공하도록 디자인된 Azure 서비스입니다. 

Azure Security Center의 경우 ARG 및 [KQL(Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)을 사용하여 다양한 보안 태세 데이터를 쿼리할 수 있습니다. 예를 들어:

- 자산 인벤토리 활용(ARG)
- [MFA(다단계 인증)를 사용하지 않고 계정을 식별](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)하는 방법에 대한 샘플 ARG 쿼리가 문서로 준비되어 있습니다.

ARG 내에는 쿼리에 사용할 수 있는 데이터 테이블이 들어 있습니다.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer 및 사용 가능한 테이블":::

> [!TIP]
> ARG 설명서에는 [Azure Resource Graph 테이블 및 리소스 종류 참조](../governance/resource-graph/reference/supported-tables-resources.md)에서 사용 가능한 모든 테이블이 나열되어 있습니다.

이번 업데이트에서 **Microsoft.Security/securityStatuses** 테이블이 제거되었습니다. securityStatuses API는 계속 사용할 수 있습니다.

데이터 교체는 Microsoft.Security/Assessments에서 사용할 수 있습니다.

Microsoft.Security/securityStatuses는 평가의 집계를 보여주고 Microsoft.Security/Assessments는 각 평가에 대한 단일 레코드를 포함한다는 것이 가장 큰 차이점입니다.

예를 들어 Microsoft.Security/securityStatuses는 다음과 같은 두 개의 policyAssessments 배열이 포함된 결과를 반환합니다.

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
반면 Microsoft.Security/Assessments는 다음과 같이 각 정책 평가에 대한 레코드를 보유합니다.

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**이제 assessments 테이블을 사용하도록 securityStatuses를 사용하여 기존 ARG 쿼리를 변환하는 예제:**

SecurityStatuses를 참조하는 쿼리:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Assessments 테이블의 대체 쿼리:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

다음 링크에서 자세한 내용을 알아보세요.
- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language(KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020년 9월

9월의 업데이트는 다음과 같습니다.
- [Security Center가 새로운 모습으로 바뀌었습니다!](#security-center-gets-a-new-look)
- [Azure Defender가 릴리스됨](#azure-defender-released)
- [Key Vault용 Azure Defender가 일반 공급됨](#azure-defender-for-key-vault-is-generally-available)
- [Files 및 ADLS Gen2에 대한 Storage용 Azure Defender 보호가 일반 공급됨](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [이제 자산 인벤토리 도구가 일반 공급됨](#asset-inventory-tools-are-now-generally-available)
- [컨테이너 레지스트리 및 가상 머신 검사에 대한 특정 취약성 결과 사용 안 함](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [권장 사항에서 리소스 제외](#exempt-a-resource-from-a-recommendation)
- [Security Center의 AWS 및 GCP 커넥터에서 다중 클라우드 환경을 제공함](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes 워크로드 보호 추천 사항 번들](#kubernetes-workload-protection-recommendation-bundle)
- [이제 취약성 평가 결과를 연속 내보내기에서 사용할 수 있음](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [새 리소스를 만들 때 추천 사항을 적용하여 보안 구성 오류 방지](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [네트워크 보안 그룹 추천 사항이 향상됨](#network-security-group-recommendations-improved)
- ["Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다."라는 미리 보기 AKS 추천 사항이 더 이상 사용되지 않음](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Azure Security Center의 이메일 알림이 향상됨](#email-notifications-from-azure-security-center-improved)
- [보안 점수에 미리 보기 추천 사항이 포함되지 않음](#secure-score-doesnt-include-preview-recommendations)
- [이제 추천 사항에 심각도 표시기 및 새로 고침 간격이 포함됨](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center가 새로운 모습으로 바뀌었습니다!

Security Center의 포털 페이지에 대해 새로 고친 UI를 릴리스했습니다. 새 페이지에는 보안 점수, 자산 인벤토리 및 Azure Defender 대시보드뿐만 아니라 새 개요 페이지도 포함되었습니다.

다시 설계된 개요 페이지에는 이제 보안 점수, 자산 인벤토리 및 Azure Defender 대시보드에 액세스하기 위한 타일이 있습니다. 또한 규정 준수 대시보드에 연결되는 타일도 있습니다.

[개요 페이지](overview-page.md)에 대해 자세히 알아보세요.


### <a name="azure-defender-released"></a>Azure Defender가 릴리스됨

**Azure Defender** 는 Security Center 내부에 통합되어 Azure 및 하이브리드 워크로드에 대한 고급 인텔리전트 보호를 제공하는 CWPP(클라우드 워크로드 보호 플랫폼)입니다. Security Center의 표준 가격 책정 계층 옵션을 대체합니다. 

Azure Security Center의 **가격 책정 및 설정** 영역에서 Azure Defender를 사용하도록 설정하면 다음 Defender 플랜이 동시에 사용하도록 설정되어 환경의 컴퓨팅, 데이터 및 서비스 계층에 대한 포괄적인 방어 기능을 제공합니다.

- [서버용 Azure Defender](defender-for-servers-introduction.md)
- [App Service용 Azure Defender](defender-for-app-service-introduction.md)
- [스토리지용 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Key Vault용 Azure Defender](defender-for-key-vault-introduction.md)
- [Kubernetes용 Azure Defender](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리용 Azure Defender](defender-for-container-registries-introduction.md)

이러한 플랜 각각은 Security Center 설명서에서 별도로 설명하고 있습니다.

Azure Defender는 전용 대시보드를 사용하여 가상 머신, SQL 데이터베이스, 컨테이너, 웹 애플리케이션, 네트워크 등에 대한 보안 경고 및 지능형 위협 방지 기능을 제공합니다.

[Azure Defender](azure-defender.md)에 대해 자세히 알아보세요.

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Key Vault용 Azure Defender가 일반 공급됨

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

**Key Vault용 Azure Defender** 는 Azure Key Vault용 Azure 네이티브 Advanced Threat Protection 기능을 통해 추가 보안 인텔리전스 계층을 제공합니다. Key Vault용 Azure Defender는 확장을 통해 결과적으로 Key Vault 계정에 종속된 많은 리소스를 보호합니다.

선택적 플랜은 이제 GA입니다. 이 기능은 미리 보기에서 "Azure Key Vault용 Advanced Threat Protection"으로 제공되었습니다.

또한 Azure Portal의 Key Vault 페이지에는 이제 **Security Center** 추천 사항 및 경고에 대한 전용 **보안** 페이지가 포함됩니다.

[Key Vault용 Azure Defender](defender-for-key-vault-introduction.md)에서 자세히 알아보세요.


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Files 및 ADLS Gen2에 대한 Storage용 Azure Defender 보호가 일반 공급됨 

**Storage용 Azure Defender** 는 Azure Storage 계정에서 잠재적으로 유해한 활동을 탐지합니다. Blob 컨테이너, 파일 공유 또는 데이터 레이크로 저장되는지에 관계없이 데이터를 보호할 수 있습니다.

이제 [Azure Files](../storage/files/storage-files-introduction.md) 및 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)에 대한 지원이 일반 공급됩니다.

2020년 10월 1일부터 이러한 서비스에서 리소스를 보호하는 데 드는 요금이 청구됩니다.

[Storage용 Azure Defender](defender-for-storage-introduction.md)에서 자세히 알아보세요.


### <a name="asset-inventory-tools-are-now-generally-available"></a>이제 자산 인벤토리 도구가 일반 공급됨

Azure Security Center의 자산 인벤토리 페이지는 Security Center에 연결한 리소스의 보안 상태를 확인할 수 있는 단일 페이지를 제공합니다.

Security Center는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다.

리소스에 수정되지 않은 추천 사항이 있으면 인벤토리에 표시됩니다.

[자산 인벤토리 및 관리 도구를 사용하여 리소스 검색 및 관리](asset-inventory.md)에서 자세히 알아보세요.



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>컨테이너 레지스트리 및 가상 머신 검사에 대한 특정 취약성 결과 사용 안 함

Azure Defender에는 Azure Container Registry 및 가상 머신의 이미지를 검사하는 취약성 스캐너가 포함되어 있습니다.

조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

결과가 사용 안 함 규칙에 정의한 조건과 일치하면 검색 결과 목록에 표시되지 않습니다.

이 옵션은 다음에 대한 추천 사항 세부 정보 페이지에서 사용할 수 있습니다.

- **Azure Container Registry 이미지의 취약성을 수정해야 함**
- **가상 머신의 취약성을 수정해야 함**

[컨테이너 이미지에 대한 특정 결과 사용 안 함](defender-for-container-registries-usage.md#disable-specific-findings-preview) 및 [가상 머신에 대한 특정 결과 사용 안 함](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)에서 자세히 알아보세요.


### <a name="exempt-a-resource-from-a-recommendation"></a>권장 사항에서 리소스 제외

경우에 따라 리소스가 특정 추천 사항과 관련하여 비정상 상태가 아니라고 생각하더라도 비정상 상태로 표시됩니다. 이로 인해 보안 점수가 낮아집니다. Security Center에서 추적하지 않는 프로세스를 통해 수정되었을 수 있습니다. 또는 조직에서 특정 리소스에 대한 위험을 감수하기로 결정했을 수도 있습니다. 

이러한 경우 예외 규칙을 만들고 나중에 해당 리소스가 비정상 리소스에 나열되지 않도록 할 수 있습니다. 이러한 규칙에는 아래에서 설명한 대로 문서화된 근거가 포함될 수 있습니다.

[추천 사항 및 보안 점수에서 리소스 제외](exempt-resource.md)에서 자세히 알아보세요.


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Security Center의 AWS 및 GCP 커넥터에서 다중 클라우드 환경을 제공함

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼에 걸쳐 있는 경우 클라우드 보안 서비스도 동일한 작업을 수행해야 합니다.

Azure Security Center는 이제 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)에서 워크로드를 보호합니다.

AWS 및 GCP 계정을 Security Center에 온보딩하면 AWS Security Hub, GCP Security Command 및 Azure Security Center를 통합합니다. 

[Azure Security Center에 AWS 계정 연결](quickstart-onboard-aws.md) 및 [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)에서 자세히 알아보세요.


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes 워크로드 보호 추천 사항 번들

Kubernetes 워크로드에서 기본적으로 보안을 유지할 수 있도록 하기 위해 Security Center에서 Kubernetes 허용 제어를 사용하는 적용 옵션을 포함하여 Kubernetes 수준 보안 강화 추천 사항을 추가합니다.

Kubernetes용 Azure Policy 추가 기능을 AKS 클러스터에 설치한 경우 Kubernetes API 서버에 대한 모든 요청은 클러스터에 유지되기 전에 미리 정의된 모범 사례 세트에 대해 모니터링됩니다. 그런 다음, 모범 사례를 적용하고 향후 워크로드에 대해 위임하도록 구성할 수 있습니다.

예를 들어 권한 있는 컨테이너를 만들지 않도록 위임할 수 있습니다. 그러면, 이러한 작업에 대한 이후의 모든 요청이 차단됩니다.

[Kubernetes 허용 제어를 사용하여 워크로드 보호 모범 사례](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)에서 자세히 알아보세요.


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>이제 취약성 평가 결과를 연속 내보내기에서 사용할 수 있음

연속 내보내기를 사용하여 경고 및 추천 사항을 Azure Event Hubs, Log Analytics 작업 영역 또는 Azure Monitor에 실시간으로 스트리밍합니다. 여기서는 이 데이터를 SIEM(예: Azure Sentinel, Power BI, Azure Data Explorer 등)과 통합할 수 있습니다.

Security Center의 통합 취약성 평가 도구는 "가상 머신의 취약성을 수정해야 함"과 같은 '부모' 추천 사항 내에서 리소스에 대한 결과를 실행 가능한 추천 사항으로 반환합니다. 

이제 추천 사항을 선택하고 **보안 결과 포함** 옵션을 사용하도록 설정하면 연속 내보내기를 통해 보안 결과를 내보낼 수 있습니다.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="연속 내보내기 구성의 보안 결과 포함 설정/해제" :::

관련 페이지:

- [Azure 가상 머신에 대한 Security Center의 통합 취약성 평가 솔루션](deploy-vulnerability-assessment-vm.md)
- [Azure Container Registry 이미지에 대한 Security Center의 통합 취약성 평가 솔루션](defender-for-container-registries-usage.md)
- [연속 내보내기](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>새 리소스를 만들 때 추천 사항을 적용하여 보안 구성 오류 방지

보안 구성 오류는 보안 인시던트의 주요 원인입니다. 이제 Security Center에는 특정 추천 사항과 관련하여 새 리소스의 구성 오류를 *방지* 하는 데 도움이 되는 기능이 있습니다. 

이 기능을 통해 워크로드를 안전하게 유지하고 보안 점수를 안정화할 수 있습니다.

특정 추천 사항에 따라 보안 구성을 적용하는 방법은 다음 두 가지 모드로 제공됩니다.

- Azure Policy의 **거부** 효과를 사용하여 비정상 리소스가 만들어지는 것을 중지할 수 있습니다.

- **적용** 옵션을 사용하여 Azure Policy의 **DeployIfNotExist** 효과를 활용하고, 비준수 리소스를 만들 때 자동으로 수정할 수 있습니다.
 
이는 선택한 보안 추천 사항에 사용할 수 있으며 리소스 세부 정보 페이지의 위쪽에서 찾을 수 있습니다.

[적용/거부 추천 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md)에서 자세히 알아보세요.

###  <a name="network-security-group-recommendations-improved"></a>네트워크 보안 그룹 추천 사항이 향상됨

일부 가양성 인스턴스를 줄이기 위해 네트워크 보안 그룹과 관련된 다음과 같은 보안 추천 사항이 향상되었습니다.

- VM에 연결된 NSG에서 모든 네트워크 포트를 제한해야 함
- 가상 머신에서 관리 포트를 닫아야 합니다.
- 인터넷 연결 가상 머신은 네트워크 보안 그룹과 함께 보호되어야 합니다.
- 서브넷을 네트워크 보안 그룹과 연결해야 합니다.


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>"Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다."라는 미리 보기 AKS 추천 사항이 더 이상 사용되지 않음

[Azure Kubernetes Service](../aks/use-pod-security-policies.md) 설명서에서 설명한 대로 "Kubernetes Services에서 Pod 보안 정책을 정의해야 합니다."라는 미리 보기 추천 사항이 더 이상 사용되지 않습니다.

Pod 보안 정책(미리 보기) 기능은 더 이상 사용하지 않도록 설정되며, AKS에 대한 Azure Policy에 따라 2020년 10월 15일 이후에는 더 이상 사용할 수 없습니다.

Pod 보안 정책(미리 보기)이 더 이상 사용되지 않는 경우 향후 클러스터 업그레이드를 수행하고 Azure 지원을 유지하려면 더 이상 사용되지 않는 기능을 사용하여 기존 클러스터에서 이 기능을 사용하지 않도록 설정해야 합니다.


### <a name="email-notifications-from-azure-security-center-improved"></a>Azure Security Center의 이메일 알림이 향상됨

보안 경고와 관련하여 향상된 이메일 영역은 다음과 같습니다. 

- 모든 심각도 수준에 대한 경고와 관련된 이메일 알림을 보내는 기능이 추가되었습니다.
- 구독에서 다른 RBAC 역할의 사용자에게 알리는 기능이 추가되었습니다.
- 심각도가 높은 경고(진짜 위반일 가능성이 높음)는 기본적으로 구독 소유자에게 사전에 알려줍니다.
- 이메일 알림 구성 페이지에서 전화 번호 필드가 제거되었습니다.

[보안 경고에 대한 이메일 알림 설정](security-center-provide-security-contact-details.md)에서 자세히 알아보세요.


### <a name="secure-score-doesnt-include-preview-recommendations"></a>보안 점수에 미리 보기 추천 사항이 포함되지 않음 

Security Center는 리소스, 구독 및 조직의 보안 이슈를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다.

새 위협이 발견되면 Security Center에서 새 추천 사항을 통해 새로운 보안 제안이 제공됩니다. 보안 점수가 예기치 않게 변경되지 않도록 방지하고 새 추천 사항이 점수에 영향을 주기 전에 이러한 새 추천 사항을 검색할 수 있는 유예 기간을 제공하기 위해 **미리 보기** 플래그로 지정된 추천 사항은 더 이상 보안 점수 계산에 포함되지 않습니다. 미리 보기 기간이 종료되면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정해야 합니다.

또한 **미리 보기** 추천 사항은 리소스를 "비정상"으로 렌더링하지 않습니다.

미리 보기 추천 사항의 예는 다음과 같습니다.

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="미리 보기 플래그가 있는 추천 사항":::

[보안 점수](secure-score-security-controls.md)에 대해 자세히 알아보세요.


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>이제 추천 사항에 심각도 표시기 및 새로 고침 간격이 포함됨

추천 사항에 대한 세부 정보 페이지에는 이제 새로 고침 간격 표시기(관련될 때마다) 및 추천 사항의 심각도에 대한 명확한 표시가 포함됩니다.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="새고 고침 및 심각도를 보여 주는 추천 사항 페이지":::



## <a name="august-2020"></a>2020년 8월

8월의 업데이트는 다음과 같습니다.

- [자산 인벤토리 - 자산의 보안 상태에 대한 강력하고 새로운 보기](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Azure Active Directory 보안 기본값에 대한 지원(다단계 인증용)이 추가됨](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [서비스 주체 추천 사항이 추가됨](#service-principals-recommendation-added)
- [VM에 대한 취약성 평가 - 추천 사항과 정책이 통합됨](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [새 AKS 보안 정책이 ASC_default 이니셔티브에 추가됨 - 프라이빗 미리 보기 고객만 사용](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>자산 인벤토리 - 자산의 보안 상태에 대한 강력하고 새로운 보기

Security Center의 자산 인벤토리(현재 미리 보기 상태)는 Security Center에 연결한 리소스의 보안 상태를 확인할 수 있는 방법을 제공합니다.

Security Center는 Azure 리소스의 보안 상태를 정기적으로 분석하여 잠재적인 보안 취약성을 식별합니다. 그런 다음, 이러한 취약성을 수정하는 방법에 대한 추천 사항을 제공합니다. 리소스에 수정되지 않은 추천 사항이 있으면 인벤토리에 표시됩니다.

보기 및 해당 필터를 사용하여 보안 상태 데이터를 검색하고 결과에 따라 추가 작업을 수행할 수 있습니다.

[자산 인벤토리](asset-inventory.md)에 대해 자세히 알아보세요.


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Azure Active Directory 보안 기본값에 대한 지원(다단계 인증용)이 추가됨

Security Center에는 Microsoft의 무료 ID 보안 보호인 [보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)에 대한 완전한 지원이 추가되었습니다.

보안 기본값은 일반적인 ID 관련 공격으로부터 조직을 보호하기 위해 미리 구성된 ID 보안 설정을 제공합니다. 보안 기본값은 이미 전체적으로 500만 개 이상의 테넌트를 보호하고 있으며, 5만 개의 테넌트도 Security Center를 통해 보호됩니다.

Security Center는 이제 보안 기본값을 사용하도록 설정되지 않은 Azure 구독을 식별할 때마다 보안 추천 사항을 제공합니다. 지금까지 Security Center는 AD(Azure Active Directory) Premium 라이선스의 일부인 조건부 액세스를 사용하는 다단계 인증을 사용하도록 추천했습니다. Azure AD 체험 라이선스를 사용하는 고객의 경우 이제 보안 기본값을 사용하도록 설정하는 것이 좋습니다. 

Microsoft의 목표는 더 많은 고객이 MFA를 사용하여 클라우드 환경을 보호하도록 권장하고, [보안 점수](secure-score-security-controls.md)에 가장 큰 영향을 미치는 가장 높은 위험 중 하나를 완화하는 것입니다.

[보안 기본값](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)에 대해 자세히 알아보세요.


### <a name="service-principals-recommendation-added"></a>서비스 주체 추천 사항이 추가됨

관리 인증서를 사용하여 구독을 관리하는 Security Center 고객에게 서비스 주체로 전환하도록 추천하는 새 추천 사항이 추가되었습니다.

**관리 인증서 대신 서비스 주체를 사용하여 구독을 보호해야 합니다.** 라는 추천 사항은 서비스 주체 또는 Azure Resource Manager를 사용하여 구독을 더 안전하게 관리하도록 추천합니다. 

[Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)에 대해 자세히 알아보세요.


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>VM에 대한 취약성 평가 - 추천 사항과 정책이 통합됨

Security Center는 VM을 검사하여 해당 VM에서 취약성 평가 솔루션을 실행하고 있는지 여부를 검색합니다. 취약성 평가 솔루션을 찾을 수 없는 경우 Security Center는 배포를 간소화하기 위한 추천 사항을 제공합니다.

취약성이 발견되면 Security Center에서 필요에 따라 조사하고 수정할 수 있도록 결과를 요약한 추천 사항을 제공합니다.

사용하는 스캐너 유형에 관계없이 모든 사용자에게 일관된 환경을 보장하기 위해 4가지 추천 사항이 다음 2가지 추천 사항으로 통합되었습니다.

|통합 추천 사항|변경 내용 설명|
|----|:----|
|**취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함**|다음 두 가지 추천 사항을 대체합니다.<br> **•** 가상 머신에서 기본 제공 취약성 평가 솔루션 사용(Qualys에서 제공)(현재 사용되지 않음)(표준 계층에 포함됨)<br> **•** 가상 머신에 취약성 평가 솔루션을 설치해야 함(현재 사용되지 않음)(표준 및 체험 계층)|
|**가상 머신의 취약성을 수정해야 함**|다음 두 가지 추천 사항을 대체합니다.<br>**•** 가상 머신에서 발견한 취약성 수정(Qualys 제공)(현재 사용되지 않음)<br>**•** 취약성 평가 솔루션으로 취약성을 수정해야 함(현재 사용되지 않음)|
|||

이제 동일한 추천 사항을 사용하여 Qualys 또는 Rapid7과 같은 파트너로부터 Security Center의 취약성 평가 확장 또는 개인적으로 사용이 허가된 솔루션("BYOL")을 배포합니다.

또한 취약성이 발견되어 Security Center에 보고되면 이를 식별한 취약성 평가 솔루션에 관계없이 단일 추천 사항에서 결과를 알려줍니다.

#### <a name="updating-dependencies"></a>종속성 업데이트

이전 추천 사항 또는 정책 키/이름을 참조하는 스크립트, 쿼리 또는 자동화가 있는 경우 아래 표를 사용하여 참조를 업데이트합니다.

##### <a name="before-august-2020"></a>2020년 8월 이전

|권장|Scope|
|----|:----|
|**가상 머신에서 기본 제공 취약성 평가 솔루션 사용(Qualys에서 제공)**<br>키: 550e890b-e652-4d22-8274-60b3bdb24c63|기본 제공|
|**가상 머신에서 발견한 취약성 수정(Qualys 제공)**<br>키: 1195afff-c881-495e-9bc5-1486211ae03f|기본 제공|
|**가상 머신에 취약성 평가 솔루션을 설치해야 함**<br>키: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**취약성 평가 솔루션으로 취약성을 수정해야 합니다.**<br>키: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|정책|Scope|
|----|:----|
|**가상 머신에서 취약성 평가를 사용하도록 설정해야 함**<br>정책 ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|기본 제공|
|**취약성 평가 솔루션으로 취약성을 수정해야 함**<br>정책 ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>2020년 8월부터

|권장|Scope|
|----|:----|
|**취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함**<br>키: ffff0522-1e88-47fc-8382-2a80ba848f5d|기본 제공 + BYOL|
|**가상 머신의 취약성을 수정해야 함**<br>키: 1195afff-c881-495e-9bc5-1486211ae03f|기본 제공 + BYOL|
||||

|정책|Scope|
|----|:----|
|[**가상 머신에서 취약성 평가를 사용하도록 설정해야 함**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>정책 ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |기본 제공 + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>새 AKS 보안 정책이 ASC_default 이니셔티브에 추가됨 - 프라이빗 미리 보기 고객만 사용

Kubernetes 워크로드에서 기본적으로 보안을 유지할 수 있도록 하기 위해 Security Center에서 Kubernetes 허용 제어를 사용하는 적용 옵션을 포함하여 Kubernetes 수준 정책 보안 강화 추천 사항을 추가합니다.

이 프로젝트의 초기 단계에는 프라이빗 미리 보기 및 ASC_default 이니셔티브에 추가된 새 정책(기본적으로 사용 안 함)이 포함됩니다.

이러한 정책은 무시해도 되지만 환경에는 영향을 주지 않습니다. 이러한 정책을 사용하도록 설정하려면 https://aka.ms/SecurityPrP 에서 미리 보기에 가입하고 다음 옵션 중에서 선택합니다.

1. **단일 미리 보기** – 이 프라이빗 미리 보기에만 조인합니다. "ASC 연속 검사"를 조인하려는 미리 보기로 명시적으로 언급합니다.
1. **진행 중인 프로그램** - 현재 및 향후 프라이빗 미리 보기에 추가됩니다. 프로필 및 개인 정보 계약을 완료해야 합니다.


## <a name="july-2020"></a>2020년 7월

7월의 업데이트는 다음과 같습니다.
- [이제 가상 머신에 대한 취약성 평가를 비 마켓플레이스 이미지에 사용할 수 있음](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Azure Storage에 대한 위협 방지 기능이 Azure Files 및 Azure Data Lake Storage Gen2(미리 보기)로 확장됨](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [위협 방지 기능을 사용하도록 설정하기 위한 새로운 8가지 추천 사항](#eight-new-recommendations-to-enable-threat-protection-features)
- [컨테이너 보안이 향상됨 - 더 빠른 레지스트리 검사 및 새로 고친 설명서](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [적응형 애플리케이션 제어가 새 추천 사항 및 경로 규칙의 와일드카드 지원으로 업데이트됨](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [SQL Advanced Data Security에 대한 6가지 정책이 더 이상 사용되지 않음](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>이제 가상 머신에 대한 취약성 평가를 비 마켓플레이스 이미지에 사용할 수 있음

이전에는 취약성 평가 솔루션을 배포할 때 Security Center에서 배포하기 전에 유효성 검사를 수행했습니다. 이 검사는 대상 가상 머신의 마켓플레이스 SKU를 확인하는 것이었습니다. 

이 업데이트에서는 검사가 제거되었으며, 이제 취약성 평가 도구를 '사용자 지정' Windows 및 Linux 컴퓨터에 배포할 수 있습니다. 사용자 지정 이미지는 마켓플레이스 기본값에서 수정한 이미지입니다.

이제 통합 취약성 평가 확장(Qualys에서 제공)을 더 많은 컴퓨터에 배포할 수 있지만, 지원은 [표준 계층 VM에 통합 취약성 스캐너 배포](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)에 나열된 OS를 사용하는 경우에만 지원을 사용할 수 있습니다.

[가상 머신용 통합 취약성 스캐너(Azure Defender 필요)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)에 대해 자세히 알아보세요.

개인적으로 사용이 허가된 Qualys 또는 Rapid7의 취약성 평가 솔루션을 사용하는 방법은 [파트너 취약성 검사 솔루션 배포](deploy-vulnerability-assessment-vm.md)에서 자세히 알아보세요.


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Azure Storage에 대한 위협 방지 기능이 Azure Files 및 Azure Data Lake Storage Gen2(미리 보기)로 확장됨

Azure Storage에 대한 위협 방지는 Azure Storage 계정에서 잠재적으로 유해한 활동을 탐지합니다. Security Center는 스토리지 계정에 액세스하거나 이를 악용하려는 시도를 탐지하면 경고를 표시합니다. 

Blob 컨테이너, 파일 공유 또는 데이터 레이크로 저장되는지에 관계없이 데이터를 보호할 수 있습니다.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>위협 방지 기능을 사용하도록 설정하기 위한 새로운 8가지 추천 사항

가상 머신, App Service 요금제, Azure SQL Database 서버, 컴퓨터의 SQL 서버, Azure Storage 계정, Azure Kubernetes Service 클러스터, Azure Container Registry 레지스트리 및 Azure Key Vault 자격 증명 모음과 같은 리소스 종류에 대해 Azure Security Center의 위협 방지 기능을 사용하도록 설정하는 간단히 방법을 제공하기 위해 8가지 추천 사항이 새로 추가되었습니다.

새 추천 사항은 다음과 같습니다.

- **Azure SQL Database 서버에서 Advanced Data Security를 사용하도록 설정해야 함**
- **머신의 SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 함**
- **Azure App Service 계획에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Container Registry 레지스트리에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Key Vault 자격 증명 모음에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Kubernetes Service 클러스터에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Azure Storage 계정에서 지능형 위협 방지를 사용하도록 설정해야 함**
- **Virtual Machines에서 Advanced Threat Protection을 사용하도록 설정해야 함**

이러한 새 추천 사항은 **Azure Defender 사용** 보안 제어에 속합니다.

추천 사항에는 빠른 수정 기능도 포함됩니다. 

> [!IMPORTANT]
> 이러한 추천 사항 중 하나를 수정하면 관련 리소스를 보호하는 데 드는 요금이 청구됩니다. 현재 구독에 관련 리소스가 있는 경우 이러한 청구가 즉시 시작됩니다. 또는 나중에 추가하는 경우가 있습니다.
> 
> 예를 들어 구독에 Azure Kubernetes Service 클러스터가 없고 위협 방지를 사용하도록 설정하면 요금이 발생하지 않습니다. 나중에 클러스터를 동일한 구독에 추가하면 해당 클러스터가 자동으로 보호되고 해당 시간에 요금이 청구됩니다.

이러한 리소스 각각에 대해 [보안 추천 사항 참조 페이지](recommendations-reference.md)에서 자세히 알아보세요.

[Azure Security Center의 위협 방지](azure-defender.md)에 대해 자세히 알아보세요.




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>컨테이너 보안이 향상됨 - 더 빠른 레지스트리 검사 및 새로 고친 설명서

컨테이너 보안 도메인에 대한 지속적인 투자의 일환으로, Azure Container Registry에 저장된 컨테이너 이미지에 대한 Security Center의 동적 검사에서 상당히 향상된 성능을 공유할 수 있습니다. 이제 검사는 일반적으로 약 2분 안에 완료됩니다. 경우에 따라 최대 15분이 걸릴 수 있습니다.

Azure Security Center의 컨테이너 보안 기능에 대한 명확성과 지침을 향상시키기 위해 컨테이너 보안 문서 페이지도 새로 고쳤습니다. 

Security Center의 컨테이너 보안에 대해 다음 문서에서 자세히 알아보세요.

- [Security Center의 컨테이너 보안 기능에 대한 개요](container-security.md)
- [Azure Container Registry와의 통합에 대한 세부 정보](defender-for-container-registries-introduction.md)
- [Azure Kubernetes Service와의 통합에 대한 세부 정보](defender-for-kubernetes-introduction.md)
- [레지스트리를 검사하고 Docker 호스트를 강화하는 방법](container-security.md)
- [Azure Kubernetes Service 클러스터에 대한 위협 방지 기능의 보안 경고](alerts-reference.md#alerts-akscluster)
- [Azure Kubernetes Service 호스트에 대한 위협 방지 기능의 보안 경고](alerts-reference.md#alerts-containerhost)
- [컨테이너에 대한 보안 추천 사항](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>적응형 애플리케이션 제어가 새 추천 사항 및 경로 규칙의 와일드카드 지원으로 업데이트됨

적응형 애플리케이션 제어 기능에는 다음과 같은 두 가지 중요한 업데이트가 제공되었습니다.

* 새 추천 사항은 이전에 허용되지 않은 잠재적으로 합법적인 동작을 식별합니다. **적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 합니다.** 라는 새 추천 사항은 적응형 애플리케이션 제어 위반 경고의 가양성의 수를 줄이기 위해 새 규칙을 기존 정책에 추가하도록 요청합니다.

* 경로 규칙에서 이제 와일드카드를 지원합니다. 이 업데이트에서는 와일드카드를 사용하여 허용되는 경로 규칙을 구성할 수 있습니다. 지원되는 두 가지 시나리오는 다음과 같습니다.

    * 와일드카드를 경로 끝에 사용하여 이 폴더 및 하위 폴더에 있는 모든 실행 파일 허용

    * 와일드카드를 경로 중간에 사용하여 변경된 폴더 이름(예: 알려진 실행 파일이 있는 개인 사용자 폴더, 자동으로 생성된 폴더 이름 등)으로 알려진 실행 파일 이름을 사용할 수 있도록 설정


[적응형 애플리케이션 제어에 대해 자세히 알아봅니다](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>SQL Advanced Data Security에 대한 6가지 정책이 더 이상 사용되지 않음

SQL 컴퓨터의 고급 데이터 보안과 관련된 6가지 정책은 더 이상 사용되지 않습니다.

- SQL Managed Instance Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL Server Advanced Data Security 설정에서 Advanced Threat Protection 유형을 '모두'로 설정해야 합니다.
- SQL 관리형 인스턴스에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL Server에 대한 Advanced Data Security 설정에는 보안 경고를 받을 이메일 주소가 포함되어야 합니다.
- SQL 관리형 인스턴스 Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.
- SQL Server Advanced Data Security 설정에는 관리자 및 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.

[기본 제공 정책](./policy-reference.md)에 대해 자세히 알아보세요.





## <a name="june-2020"></a>2020년 6월

6월의 업데이트는 다음과 같습니다.
- [보안 점수 API(미리 보기)](#secure-score-api-preview)
- [SQL 컴퓨터(Azure, 기타 클라우드 및 온-프레미스)에 대한 고급 데이터 보안(미리 보기)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Log Analytics 에이전트를 Azure Arc 컴퓨터에 배포하기 위한 두 가지 새 추천 사항(미리 보기)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [연속 내보내기 및 워크플로 자동화 구성을 규모에 맞게 만들기 위한 새 정책](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [NSG를 사용하여 인터넷에 연결되지 않은 가상 머신을 보호하기 위한 새 추천 사항](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [위협 방지 및 고급 데이터 보안을 사용하도록 설정하기 위한 새 정책](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>보안 점수 API(미리 보기)

이제 [보안 점수 API](/rest/api/securitycenter/securescores/)(현재 미리 보기 상태)를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들어 **보안 점수** API를 사용하여 특정 구독에 대한 점수를 가져올 수 있습니다. 또한 **보안 점수 제어** API를 사용하여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

보안 점수 API를 사용하여 가능한 외부 도구의 예는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조하세요.

[Azure Security Center의 보안 점수 및 보안 제어](secure-score-security-controls.md)에 대해 자세히 알아보세요.



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>SQL 컴퓨터(Azure, 기타 클라우드 및 온-프레미스)에 대한 고급 데이터 보안(미리 보기)

SQL 컴퓨터에 대한 Azure Security Center의 고급 데이터 보안 기능은 이제 Azure, 다른 클라우드 환경 및 심지어 온-프레미스 컴퓨터에서 호스팅되는 SQL Server를 보호합니다. 이렇게 하면 Azure 네이티브 SQL Server에 대한 보호 기능이 확장되어 하이브리드 환경을 완벽하게 지원합니다.

고급 데이터 보안은 SQL 컴퓨터가 어디에 있든 취약성 평가 및 지능형 위협 방지 기능을 제공합니다.

설정에는 다음 두 가지 단계가 포함됩니다.

1. Log Analytics 에이전트를 SQL Server의 호스트 컴퓨터에 배포하여 Azure 계정에 대한 연결을 제공합니다.

1. Security Center의 가격 책정 및 설정 페이지에서 선택적 번들을 사용하도록 설정합니다.

[SQL 컴퓨터에 대한 고급 데이터 보안](defender-for-sql-usage.md)에 대해 자세히 알아보세요.



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Log Analytics 에이전트를 Azure Arc 컴퓨터에 배포하기 위한 두 가지 새 추천 사항(미리 보기)

[Log Analytics 에이전트](../azure-monitor/platform/log-analytics-agent.md)를 Azure Arc 컴퓨터에 배포하고 Azure Security Center에서 보호하도록 하는 데 도움이 되는 두 가지 추천 사항이 새로 추가되었습니다.

- **Windows 기반 Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치해야 함(미리 보기)**
- **Linux 기반 Azure Arc 컴퓨터에 Log Analytics 에이전트를 설치해야 함(미리 보기)**

이러한 새 추천 사항은 기존(관련) 추천 사항( **컴퓨터에 모니터링 에이전트를 설치해야 합니다.** )과 동일한 네 가지 보안 제어(보안 구성 수정, 적응형 애플리케이션 제어 적용, 시스템 업데이트 적용 및 엔드포인트 보호 사용)에 나타납니다.

또한 추천 사항에는 배포 프로세스 속도를 높이는 데 도움이 되는 빠른 수정 기능도 포함됩니다. 

이러한 두 가지 새 추천 사항에 대해 [컴퓨팅 및 앱 추천 사항](recommendations-reference.md#recs-computeapp) 표에서 자세히 알아보세요.

Azure Security Center에서 에이전트를 사용하는 방법에 대해 [Log Analytics 에이전트는 무엇인가요?](faq-data-collection-agents.md#what-is-the-log-analytics-agent)에서 자세히 알아보세요.

[Azure Arc 컴퓨터용 확장](../azure-arc/servers/manage-vm-extensions.md)에 대해 자세히 알아보세요.


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>연속 내보내기 및 워크플로 자동화 구성을 규모에 맞게 만들기 위한 새 정책

조직의 모니터링 및 인시던트 대응 프로세스를 자동화하면 보안 인시던트를 조사하고 완화하는 데 걸리는 시간을 크게 향상시킬 수 있습니다.

자동화 구성을 조직 전체에 배포하려면 다음과 같은 기본 제공 'DeployIfdNotExist' Azure 정책을 사용하여 [연속 내보내기](continuous-export.md) 및 [워크플로 자동화](workflow-automation.md) 프로시저를 만들고 구성합니다.

정책은 Azure Policy에서 찾을 수 있습니다.


|목표  |정책  |정책 ID  |
|---------|---------|---------|
|이벤트 허브로 연속 내보내기|[Azure Security Center 경고 및 권장 사항에 대한 Event Hub로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Log Analytics 작업 영역으로 연속 내보내기|[Azure Security Center 경고 및 권장 사항에 대한 Log Analytics 작업 영역으로 내보내기 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|보안 경고에 대한 워크플로 자동화|[Azure Security Center 경고에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|보안 추천 사항에 대한 워크플로 자동화|[Azure Security Center 권장 사항에 대한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

[워크플로 자동화 템플릿](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)을 시작합니다.

[제공된 정책을 사용하여 대규모 워크플로 자동화 구성](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) 및 [연속 내보내기 설정](continuous-export.md#set-up-a-continuous-export)에서 두 가지 내보내기 정책 사용에 대해 자세히 알아봅니다.


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>NSG를 사용하여 인터넷에 연결되지 않은 가상 머신을 보호하기 위한 새 추천 사항

"보안 모범 사례 구현" 보안 제어에는 이제 다음과 같은 새 추천 사항이 포함됩니다.

- **네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함**

**네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함** 이라는 기존 추천 사항은 VM을 대상으로 하는 인터넷 연결 VM과 인터넷에 연결되지 않은 VM을 구분하지 않았습니다. 두 경우 모두에서 VM이 네트워크 보안 그룹에 할당되지 않은 경우 높은 심각도 추천 사항이 생성되었습니다. 이 새 추천 사항은 인터넷에 연결되지 않은 머신을 분리하여 가양성을 줄이고 불필요한 높은 심각도 경고를 방지합니다.

[네트워크 추천 사항](recommendations-reference.md#recs-network) 표에서 자세히 알아보세요.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>위협 방지 및 고급 데이터 보안을 사용하도록 설정하기 위한 새 정책

아래의 새 정책은 ASC 기본 이니셔티브에 추가되었으며, 관련 리소스 종류에 대한 위협 방지 또는 고급 데이터 보안을 지원하도록 설계되었습니다.

정책은 Azure Policy에서 찾을 수 있습니다.


| 정책                                                                                                                                                                                                                                                                | 정책 ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Azure SQL Database 서버에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [머신의 SQL 서버에서 Advanced Data Security를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Azure Storage 계정에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Azure Key Vault 자격 증명 모음에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Azure App Service 계획에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Azure Container Registry 레지스트리에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Azure Kubernetes Service 클러스터에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Virtual Machines에서 지능형 위협 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

[Azure Security Center의 위협 방지](azure-defender.md)에 대해 자세히 알아보세요.