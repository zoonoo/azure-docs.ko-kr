---
title: Security Center에 대한 Azure 보안 기준
description: Security Center 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c486a27cc5d10d2a3cbf93cfca0c1e1eeef454c8
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285872"
---
# <a name="azure-security-baseline-for-security-center"></a>Security Center에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 참고 자료를 Azure Security Center에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 제어** 및 Azure Security Center에 적용되는 관련 지침에 따라 그룹화됩니다. 

> [!NOTE]
> Security Center에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다. Security Center가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 **[전체 Security Center 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/security-center-security-baseline-v1.1.xlsx)** 을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Security Center는 핵심 Azure 제품입니다. 가상 네트워크, 서브넷 또는 네트워크 보안 그룹은 Security Center에 직접 연결할 수 없습니다. 컴퓨팅 리소스에 대한 데이터 수집을 사용하도록 설정한 다음, Security Center에서 Log Analytics 작업 영역을 통해 수집한 데이터를 저장하는 경우 Private Link를 사용하여 가상 네트워크의 프라이빗 엔드포인트를 통해 작업 영역 데이터에 액세스하도록 해당 작업 영역을 구성할 수 있습니다. 또한 데이터 수집을 사용하는 경우 Security Center는 서버에 배포되는 Log Analytics 에이전트를 사용하여 보안 데이터를 수집하고 이러한 컴퓨팅 리소스에 대한 보호를 제공합니다. Log Analytics 에이전트를 사용하려면 Security Center에서 제대로 작동하기 위해 특정 포트 및 프로토콜을 열어야 합니다. 이러한 필수 포트 및 프로토콜만 허용하도록 네트워크를 잠그고, 애플리케이션이 네트워크 보안 그룹을 통해 작동하는 데 필요한 추가 규칙만 추가합니다.

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)

- [Log Analytics 에이전트를 사용하기 위한 방화벽 요구 사항](/azure/azure-monitor/platform/log-analytics-agent#firewall-requirements)

- [Azure Private Link 이해](../private-link/private-link-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.security-1-1.md)]

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Security Center 제품은 가상 네트워크와 직접 통합되지 않지만 네트워크에 배포된 Log Analytics 에이전트로 구성된 서버에서 데이터를 수집할 수 있습니다. 데이터를 Security Center에 보내도록 구성된 서버에는 제대로 통신하기 위해 허용되는 특정 포트 및 프로토콜이 필요합니다. Azure Policy를 사용하여 이러한 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.

또한 Azure Blueprints를 사용하여 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, 역할 할당 및 Azure Policy 할당)를 단일 청사진 정의로 패키지하여 대규모 Azure 배포를 간소화할 수 있습니다. 청사진을 새 구독에 적용하여 Security Center 구성 및 관련 네트워킹 리소스를 일관되고 안전하게 배포할 수 있습니다.

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [Log Analytics 에이전트를 사용하기 위한 방화벽 요구 사항](/azure/azure-monitor/platform/log-analytics-agent#firewall-requirements)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [네트워킹을 위한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Security Center 제품은 가상 네트워크와 직접 통합되지 않지만 네트워크에 배포된 Log Analytics 에이전트로 구성된 서버에서 데이터를 수집할 수 있습니다. 데이터를 Security Center에 보내도록 구성된 서버에는 제대로 통신하기 위해 허용되는 특정 포트 및 프로토콜이 필요합니다. Azure Policy를 사용하여 이러한 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.

보안 로그를 Azure Security Center에 보내도록 구성된 네트워크 보안 그룹 및 기타 리소스(네트워크의 서버)에 대한 리소스 태그를 사용합니다. 개별 네트워크 보안 그룹 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 보내고 받는 트래픽을 허용하는 규칙을 문서화합니다. 

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스를 태그가 지정된 상태로 만들고 태그가 지정되지 않은 기존 리소스를 알려줍니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다. 

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [Log Analytics 에이전트를 사용하기 위한 방화벽 요구 사항](/azure/azure-monitor/platform/log-analytics-agent#firewall-requirements)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md) 

- [Azure Virtual Network를 만드는 방법](../virtual-network/quick-create-portal.md) 

- [네트워크 보안 그룹 규칙을 사용하여 네트워크 트래픽을 필터링하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 리소스 구성을 모니터링하고, Azure Security Center와 관련된 네트워크 리소스에 대한 변경 내용을 검색합니다. Azure Monitor에서 중요한 리소스가 변경될 때 사용자에게 알리는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 중앙 Log Analytics 작업 영역을 사용하여 Azure Security Center 및 연결된 해당 원본에서 생성된 보안 데이터를 집계합니다. 

보안 데이터 및 이벤트를 연결된 Azure 컴퓨팅 리소스에서 중앙 Log Analytics 작업 영역으로 보내도록 Security Center의 데이터 수집을 구성합니다. 데이터 수집 외에도 연속 내보내기 기능을 사용하여 Security Center에서 생성된 보안 경고 및 권장 사항을 중앙 Log Analytics 작업 영역으로 스트림합니다. Azure Monitor에서는 Security Center 및 연결된 Azure 리소스에서 생성된 보안 데이터를 쿼리하고 분석을 수행할 수 있습니다. 

또는 Security Center에서 생성된 데이터를 Azure Sentinel 또는 타사 SIEM에 보낼 수 있습니다.

- [Security Center 데이터 연속 내보내기](continuous-export.md)

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](/azure/azure-monitor/platform/diagnostic-settings) 

- [Azure Monitor를 사용하여 Azure 가상 머신 내부 호스트 로그를 수집하는 방법](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.security-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Monitor 활동 로그는 자동으로 사용할 수 있으며, 이러한 로그에는 수행된 작업, 작업을 시작한 사용자 및 수행된 시기를 포함하여 Azure Security Center와 같은 리소스에 대한 모든 쓰기 작업이 포함됩니다. 로그 통합 및 보존 기간 증가를 위해 Azure 활동 로그를 Log Analytics 작업 영역에 보냅니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](/azure/azure-monitor/platform/diagnostic-settings) 

- [Azure의 로깅 및 기타 로그 형식 이해](/azure/azure-monitor/platform/platform-logs-overview)

- [활동 로그를 Log Analytics 작업 영역에 보내기](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에서 조직의 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기 보관 스토리지에 사용합니다. 

- [Log Analytics에서 데이터 보존 기간 변경](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) 

- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상 동작에 대해 Azure Security Center 및 연결된 해당 원본에서 생성된 로그를 분석 및 모니터링하고, 결과를 정기적으로 검토합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Log Analytics 쿼리 시작](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](/azure/azure-monitor/log-query/get-started-queries)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 활동 로그 또는 Azure Security Center에서 생성된 데이터에서 기록된 원치 않거나 비정상 활동을 쿼리하도록 Azure Monitor 로그 경고를 구성합니다. 조직에서 알림을 받고 비정상 활동에 대한 로그 경고가 시작되는 경우 작업을 수행할 수 있도록 작업 그룹을 설정합니다. Security Center 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 논리 앱을 트리거합니다. Security Center 워크플로를 사용하여 사용자에게 인시던트 대응을 알리거나 경고 정보에 따라 리소스를 수정하는 작업을 수행할 수 있습니다.

또는 Azure Security Center에서 생성된 관련 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수 있습니다. Azure Sentinel은 보안 관련 문제에 대한 자동화된 위협 대응을 허용하는 플레이북을 지원합니다.

- [Azure Security Center 워크플로 자동화](workflow-automation.md)

- [Azure Security Center에서 경고를 관리하는 방법](security-center-managing-and-responding-alerts.md) 

- [로그 분석 로그 데이터에 대해 경고하는 방법](/azure/azure-monitor/learn/tutorial-response)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

- [Azure Monitor의 로그 경고](/azure/azure-monitor/platform/alerts-unified-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure RBAC(역할 기반 액세스 제어)를 사용하면 역할 할당을 통해 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 주체, 관리 ID에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. Azure Security Center에는 사용자가 보안 정책을 읽거나 업데이트하고 경고 및 권장 사항 서비스를 해제할 수 있는 기본 제공 '보안 읽기 권한자' 또는 '보안 관리자' 역할이 있습니다.

- [Azure Security Center의 권한](security-center-permissions.md)

- [PowerShell을 사용하여 Azure AD(Azure Active Directory)에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 3.1](../../includes/policy/standards/asb/rp-controls/microsoft.security-3-1.md)]

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure 플랫폼 전용 관리 계정 또는 Azure Security Center 제품 관련 전용 관리 계정을 사용하는 방법에 관한 표준 운영 절차를 만듭니다. Azure Security Center ID 및 액세스 관리를 사용하여 Azure AD(Azure Active Directory)의 관리 계정 수를 모니터링합니다. Security Center에는 사용자가 보안 정책을 업데이트하고 경고 및 권장 사항 서비스를 해제할 수 있는 기본 제공 '보안 관리자' 역할도 있습니다. 이 역할이 할당된 사용자를 정기적으로 검토하고 조정해야 합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

- [Azure Security Center의 권한](security-center-permissions.md)

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.security-3-3.md)]

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure AD(Azure Active Directory) SSO를 사용합니다. Azure Security Center의 ID 및 액세스 권장 사항을 사용합니다.

- [Azure AD에서 사용하는 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Security Center 및 Azure Portal에 액세스하기 위해 Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고 Security Center ID 및 액세스 권장 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 3.5](../../includes/policy/standards/asb/rp-controls/microsoft.security-3-5.md)]

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 상승된 권한이 필요한 관리 작업에 안전한 Azure 관리형 워크스테이션(Privileged Access Workstation 또는 PAW라고도 함)을 사용합니다.

- [안전한 Azure 관리형 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서를 사용하고 모니터링하여 사용 환경에서 의심스럽거나 안전하지 않은 활동 발생을 탐지합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: Azure AD(Azure Active Directory) 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

- [Azure AD 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Security Center를 사용하는 경우 Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다. Azure Security Center에는 사용자가 보안 정책을 업데이트하고 경고 및 권장 사항 서비스를 해제할 수 있는 '보안 관리자'와 같이 할당할 수 있는 기본 제공 역할이 있습니다.

- [Azure Security Center의 권한](security-center-permissions.md)

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure AD ID 및 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. Azure Security Center와 관련된 사용자 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있는지 확인합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 3.10](../../includes/policy/standards/asb/rp-controls/microsoft.security-3-10.md)]

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: 모든 SIEM/모니터링 도구와 통합할 수 있는 Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고 

**지침**: Azure AD(Azure Active Directory) ID 보호 기능을 사용하여 사용자 ID와 관련해 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 Azure Security Center의 중요한 보안 정보를 저장하는 Log Analytics 작업 영역과 같은 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.security-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형, 데이터 민감도 수준 등의 개별 보안 도메인에 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다. 애플리케이션 및 엔터프라이즈 환경에서 요구하는 Azure 리소스에 대한 액세스 수준을 제한할 수 있습니다. Azure AD(Azure Active Directory) RBAC를 통해 Azure 리소스에 대한 액세스를 제어할 수 있습니다.

기본적으로 Azure Security Center 데이터는 Security Center 백 엔드 서비스에 저장됩니다. 이 데이터를 사용자 고유의 리소스에 저장하기 위한 요구 사항이 조직에 추가된 경우 Security Center 데이터, 경고 및 권장 사항을 저장하도록 Log Analytics 작업 영역을 구성할 수 있습니다. 사용자 고유의 작업 영역을 사용하는 경우 데이터가 시작된 환경에 따라 다른 작업 영역을 구성하여 분리를 더 추가할 수 있습니다.

- [Security Center 데이터 연속 내보내기](continuous-export.md)

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요한 정보를 암호화합니다. Azure 리소스에 연결하는 모든 클라이언트에서 TLS 1.2 이상을 협상할 수 있는지 확인합니다. Log Analytics 에이전트를 사용하여 구성되고 데이터를 Azure Security Center에 보내도록 구성된 모든 가상 머신은 TLS 1.2를 사용하도록 구성해야 합니다.

해당하는 경우 미사용 암호화 및 전송 중 암호화에 대한 Azure Security Center 권장 사항을 따릅니다. 

- [데이터를 Log Analytics에 안전하게 보내기](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

- [Azure를 사용한 전송 중 암호화 이해](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-role-based-access-controls-to-control-access-to-resources"></a>4.6: Azure 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스 제어 

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure Security Center 관련 데이터 및 리소스에 대한 액세스를 관리합니다. Azure Security Center에는 사용자가 보안 정책을 읽거나 업데이트하고 경고 및 권장 사항 서비스를 해제할 수 있는 기본 제공 '보안 읽기 권한자' 또는 '보안 관리자' 역할이 있습니다. Security Center에서 수집한 데이터를 저장하는 Log Analytics 작업 영역에는 'Log Analytics 읽기 권한자', 'Log Analytics 기여자' 등과 같이 할당할 수 있는 기본 제공 역할도 있습니다. 사용자가 필요한 작업을 완료하는 데 필요한 최소 허용 역할을 할당합니다. 예를 들어, 리소스의 보안 상태에 대한 정보를 보기만 하고 권장 사항 적용이나 정책 편집 등의 조치는 취하지 않는 사용자에게 독자 역할을 할당합니다.

- [Azure Log Analytics 작업 영역에 대한 권한](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader)

- [Azure Security Center의 권한](security-center-permissions.md)

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Security Center는 구성된 Log Analytics 작업 영역을 사용하여 생성되는 데이터, 경고 및 권장 사항을 저장합니다. Security Center 데이터 수집을 위해 구성한 작업 영역에 대한 CMK(고객 관리형 키)를 구성합니다. CMK를 사용하면 사용자가 만들고 소유하는 Azure Key Vault 키를 사용하여 작업 영역에 저장되거나 전송된 모든 데이터를 암호화할 수 있습니다. 

- [Azure Monitor 고객 관리형 키](/azure/azure-monitor/platform/customer-managed-keys)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.security-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor를 사용하여 Azure Security Center와 관련된 중요한 Azure 리소스에 대한 변경이 발생하는 경우에 대한 경고를 만듭니다. 이러한 변경에는 경고 또는 권장 사항의 사용 해제 또는 데이터 저장소의 업데이트 또는 삭제와 같은 Security Center와 관련된 구성을 수정하는 작업이 포함될 수 있습니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 채점 프로그램(예: Common Vulnerability Scoring System) 또는 타사 검사 도구에서 제공하는 기본 위험 등급을 사용합니다.

- [NIST 게시 - 일반적인 취약성 점수 매기기 시스템](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.security-5-5.md)]

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독에서 Azure Security Center와 관련된 모든 리소스를 쿼리하고 검색합니다. 테넌트에서 적절한(읽기) 권한을 확인하고, 모든 Azure 구독을 열거하여 Security Center 리소스를 검색합니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 태그를 사용하여 Azure Security Center의 중요한 보안 정보를 저장하는 Log Analytics 작업 영역과 같은 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure Security Center 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인된 Azure 리소스 및 컴퓨팅 리소스에 승인된 소프트웨어의 인벤토리를 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정합니다. 

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 조직의 인벤토리 및 검토 프로세스의 일부로 더 이상 필요하지 않은 경우 Azure Security Center와 관련된 Azure 리소스를 제거합니다.

- [Azure 리소스 그룹 및 리소스 삭제](../azure-resource-manager/management/delete-resource-group.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음과 같은 기본 제공 정책 정의를 사용하여 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 통해 Azure Security Center 및 연결된 해당 작업 영역에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.OperationalInsights" 및 "Microsoft.Security" 네임스페이스에서 Azure Policy 별칭을 사용하여 Security Center 및 해당 Log Analytics 작업 영역의 구성을 감사하거나 적용하는 사용자 지정 Azure Policy 정의를 만듭니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: '거부' 및 '존재하지 않으면 배포' Azure Policy를 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다. 또한 Azure Resource Manager 템플릿을 사용하여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md) 

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 원하는 상태 구성 스크립트와 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 하나 이상의 그룹에 권한을 부여하거나 거부할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure DevOps의 그룹 및 사용 권한 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Azure 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Policy 별칭을 사용하여 Azure Security Center 관련 리소스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 또한 Azure Automation을 사용하여 구성 변경 내용을 배포할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [별칭을 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.OperationalInsights" 및 "Microsoft. Security" 네임스페이스에서 기본 제공 Azure Policy 정의 및 Azure Policy 별칭을 사용하여 Azure 리소스 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. "감사", "거부", "존재하지 않으면 배포" Azure Policy 효과를 사용하여 Azure 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Security Center는 구성된 Log Analytics 작업 영역을 사용하여 생성되는 데이터, 경고 및 권장 사항을 저장합니다. Security Center 데이터 수집을 위해 구성한 작업 영역에 대한 CMK(고객 관리형 키)를 구성합니다. CMK를 사용하면 사용자가 만들고 소유하는 Azure Key Vault 키를 사용하여 작업 영역에 저장되거나 전송된 모든 데이터를 암호화할 수 있습니다. 

- [Azure Monitor 고객 관리형 키](/azure/azure-monitor/platform/customer-managed-keys)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 검사기를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Azure Security Center는 파일을 저장하거나 처리하기 위한 것이 아닙니다. Log Analytics 작업 영역을 포함하여 비 컴퓨팅 Azure 리소스에 업로드되는 콘텐츠를 미리 검사하는 것은 사용자의 책임입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장 

**지침**: IAC(infrastructure as code) 방법을 따르고, Azure Resource Manager를 사용하여 리소스 관련 구성의 백업으로 사용할 수 있는 Azure Security Center 관련 리소스를 JSON(JavaScript Object Notification) 템플릿에 배포합니다.

- [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 리소스에 대한 Azure Resource Manager 템플릿](/azure/templates/microsoft.security/allversions)

- [Azure Automation 소개](../automation/automation-intro.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Security Center는 Log Analytics 작업 영역을 사용하여 생성되는 데이터, 경고 및 권장 사항을 저장합니다. Azure Monitor 및 Security Center에서 고객 관리형 키를 사용하도록 설정하는 데 사용하는 작업 영역을 구성할 수 있습니다. Azure Key Vault를 사용하여 고객 관리형 키를 저장하는 경우 키에 대한 자동화된 정기 백업을 수행해야 합니다.

- [Key Vault 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure Resource Manager 지원 템플릿 파일을 사용하여 정기적으로 복원할 수 있는지 확인합니다. 백업된 고객 관리형 키의 복원을 테스트합니다.

- [Azure Resource Manager 템플릿을 사용하여 Log Analytics 작업 영역 관리](/azure/azure-monitor/samples/resource-manager-workspace)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure Policy 정의 및 Azure Resource Manager 템플릿과 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스를 보호하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 하나 이상의 그룹에 권한을 부여하거나 거부할 수 있습니다. Azure 역할 기반 액세스 제어를 사용하여 고객 관리형 키를 보호합니다.

또한 Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다. Azure Storage를 사용하여 Azure Resource Manager 템플릿 백업을 저장하는 경우 Blob 또는 Blob 스냅샷이 삭제될 때 데이터를 저장하고 복구할 수 있도록 일시 삭제를 사용하도록 설정합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

- [Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

- [Azure Storage Blob에 대한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 개발합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리 및 관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다. 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 메트릭의 신뢰도 및 경고가 발생한 활동 배후에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 구독을 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다. 

- [Azure Security Center의 보안 경고](security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별한 다음, 필요에 따라 대응 계획을 수정합니다. 

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 교육 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Security**:

[!INCLUDE [Resource Policy for Microsoft.Security 10.4](../../includes/policy/standards/asb/rp-controls/microsoft.security-10-4.md)]

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별할 수 있도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 권장 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 응답을 자동으로 트리거하여 Azure 리소스를 보호합니다. 

- [Security Center에서 워크플로 자동화를 구성하는 방법](workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft Cloud 침투 테스트 참여 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
