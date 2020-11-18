---
title: Azure Cognitive Search에 대 한 azure 보안 기준
description: Azure Cognitive Search 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e594dab3e4bf36fedee7a068068934501ca02f24
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842790"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 azure 보안 기준

이 보안 기준은 [Azure 보안 벤치 마크 버전 1.0](../security/benchmarks/overview.md) 에서 azure Cognitive Search에 대 한 지침을 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 azure 보안 벤치 마크에 정의 된 **보안 컨트롤과** azure Cognitive Search에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Cognitive Search에 적용할 수 없는 **컨트롤** 또는 고객이 제외 되었습니다.

Azure Cognitive Search 완전히 azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 azure Cognitive Search 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 모든 Microsoft Azure Virtual Network 서브넷 배포에 "최소 권한" 액세스 체계를 구현 하는 규칙을 적용 하는 네트워크 보안 그룹이 있는지 확인 합니다. 응용 프로그램의 신뢰할 수 있는 포트 및 IP 주소 범위에 대해서만 액세스를 허용 합니다. Azure 개인 끝점 (가능한 경우)을 사용 하 여 Azure Cognitive Search를 배포 하 여 가상 네트워크에서 서비스에 대 한 개인 액세스를 가능 하 게 합니다.

또한 Cognitive Search는 네트워크 액세스 제어 목록을 관리 하는 추가 네트워크 보안 기능을 지원 합니다. 방화벽 기능을 사용 하 여 특정 공용 IP 주소 범위에서 액세스를 제한 하 여 신뢰할 수 있는 원본으로의 통신만 허용 하도록 검색 서비스를 구성 합니다.

- [Azure Cognitive Search에 대 한 개인 끝점을 구성 하는 방법](./service-create-private-endpoint.md)

- [Azure Cognitive Search 방화벽을 구성 하는 방법](./service-configure-firewall.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Cognitive Search 가상 네트워크에 직접 배포할 수 없습니다. 그러나 클라이언트 응용 프로그램 또는 데이터 원본이 가상 네트워크에 있는 경우 클라우드의 검색 서비스로 전송 되는 요청을 포함 하 여 이러한 네트워크 내 구성 요소에 대 한 트래픽을 모니터링 하 고 기록할 수 있습니다. 표준 권장 사항에는 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 Azure Storage 또는 Log Analytics 작업 영역으로 로그를 전송 하는 작업이 포함 됩니다. 필요에 따라 트래픽 패턴에 대 한 정보를 얻기 위해 트래픽 분석를 사용할 수 있습니다.

- [네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석 사용 및 사용 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Cognitive Search은 배포 된 서비스 거부 공격을 방지할 수 있는 특정 기능을 제공 하지 않지만, 일반 보호를 위해 Cognitive Search 서비스와 연결 된 가상 네트워크에서 DDoS Protection 표준을 사용 하도록 설정할 수 있습니다.

- [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Cognitive Search 서비스에 연결할 Azure VIRTUAL MACHINES (VM)를 보호 하는 네트워크 보안 그룹에 대 한 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 합니다. 트래픽 감사를 위해 Azure Storage 계정으로 로그를 보냅니다. 

비정상적인 활동을 조사 하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher를 사용하도록 설정하는 방법](../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Cognitive Search는 네트워크 침입 검색을 지원 하지 않지만 침입 완화를 위해 Cognitive Search 서비스에서 허용 하는 IP 주소를 지정 하도록 방화벽 규칙을 구성할 수 있습니다. 공용 인터넷에서 검색 트래픽을 겹치지 않도록 개인 끝점을 구성 합니다.

- [데이터 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법](./search-security-manage-encryption-keys.md)

- [인덱스 및 동의어 맵에서 고객이 관리 하는 키 정보를 가져오는 방법](./search-security-get-encryption-keys.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Cognitive Search에서 인덱서 및 기술력과를 활용 하는 경우 서비스 태그를 사용 하 여 외부 리소스에 연결할 수 있는 권한이 있는 IP 주소 범위를 나타냅니다. 

규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: AzureCognitiveSearch)을 지정 하 여 리소스에 대 한 트래픽을 허용 하거나 거부 합니다. 

- [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Cognitive Search는 네트워크 리소스를 설계 하 여 제공 하지 않습니다. 검색 응용 프로그램과 관련 된 클라이언트 앱 및 데이터 원본은 가상 네트워크에 있을 수 있지만 검색 서비스 자체는 네트워크에 배포 되지 않습니다. 

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure 개인 끝점을 사용 하 여 Cognitive Search를 구성 하 여 Search 서비스를 가상 네트워크와 통합할 수 있습니다.  네트워크 보안 그룹 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 리소스 태그를 사용 합니다. 개별 네트워크 보안 그룹 규칙에 대해 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙을 문서화 합니다. 

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 값 요구" 효과)를 사용 합니다. 

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다. 

- [Cognitive Search에 대 한 개인 끝점을 만드는 방법](./service-create-private-endpoint.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Azure Virtual Network을 만드는 방법](../virtual-network/quick-create-portal.md)

- [네트워크 보안 그룹 규칙을 사용 하 여 네트워크 트래픽을 필터링 하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Cognitive Search는 네트워킹 구성 요소에 대해 또는 종속 되지 않으므로 이러한 리소스의 구성을 모니터링할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Cognitive Search는 사용자 고유의 시간 동기화 원본 구성을 지원 하지 않습니다. 검색 서비스는 Microsoft 시간 동기화 원본에 의존 하며, 구성에 대 한 고객에 게 노출 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 Cognitive Search 관련 된 로그를 수집 하 여 끝점 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.
또는이 데이터를 Azure 센티널 또는 타사 SIEM에 대해 사용 하도록 설정 하거나 온보드 할 수 있습니다.

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 진단 및 작업 로그는 Cognitive Search의 자세한 작업에 대 한 정보를 제공 하 고 서비스 및 서비스에 액세스 하는 워크 로드를 모니터링 하는 데 유용 합니다.  진단 데이터를 캡처하려면 로깅 정보가 저장 되는 위치를 지정 하 여 로깅을 사용 하도록 설정 합니다.

- [Azure Cognitive Search에 대 한 로그 데이터를 수집 하 고 분석 하는 방법](./search-monitor-logs.md)

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: 진단 메트릭으로 제공 되는 기록 데이터는 기본적으로 30 일 동안 Cognitive Search에 의해 보존 됩니다. 더 긴 보존을 위해서는 기록 된 이벤트 및 메트릭을 유지 하기 위한 저장소 옵션을 지정 하는 설정을 사용 하도록 설정 해야 합니다.

Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기 및 보관 저장소에 Azure Storage 계정을 사용 합니다. 

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 Cognitive Search 서비스의 로그를 분석 하 고 모니터링 합니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Cognitive Search에 대 한 로그 데이터를 수집 하 고 분석 하는 방법](./search-monitor-logs.md)

- [Power BI에서 검색 로그 데이터를 시각화 하는 방법](./search-monitor-logs-powerbi.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Log Analytics에 대해 알아보기](../azure-monitor/log-query/get-started-portal.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 보안 로그 및 이벤트에서 발견 된 비정상적인 활동을 모니터링 하 고 경고 하기 위해 Log Analytics 작업 영역에서 Security Center를 사용 합니다. 또는 온보드 데이터를 Azure 센티널로 사용 하도록 설정할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리 하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: Cognitive Search에는 적용 되지 않습니다. Microsoft는 기본 플랫폼용 맬웨어 방지 솔루션을 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: Cognitive Search에는 적용 되지 않습니다. DNS 로그를 생성 하거나 사용 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: Cognitive Search에는 적용 되지 않습니다. Cognitive Search에는 명령줄 감사를 사용할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: azure RBAC (역할 기반 액세스 제어)를 통해 역할 할당을 통해 azure 리소스에 대 한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대 한 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다.

Cognitive Search 역할은 서비스 수준 관리 작업을 지 원하는 사용 권한과 연결 됩니다.  이러한 역할은 서비스 끝점에 대 한 액세스 권한을 부여 하지 않습니다. 끝점에 대 한 작업 (예: 인덱스 관리, 인덱스 채우기 및 검색 데이터에 대 한 쿼리)에 대 한 액세스는 API 키를 사용 하 여 요청을 인증 합니다.

- [Azure Cognitive Search에 대 한 관리 액세스 역할 설정](./search-security-rbac.md)

- [Azure Cognitive Search 서비스에 대 한 api 키 만들기 및 관리](./search-security-api-keys.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)
- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Cognitive Search에는 적용 되지 않습니다. 기본 암호의 개념은 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Cognitive Search에는 인덱스와 작업을 관리 하는 데 사용할 수 있는 로컬 수준 또는 Azure Active Directory (Azure AD) 관리자 계정의 개념이 없습니다. 

관리 작업에 대해 명시적으로 할당 해야 하는 Azure AD 기본 제공 역할을 사용 합니다. Azure AD PowerShell 모듈을 호출 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

- [Cognitive Search에서 관리 액세스용 역할을 사용 하는 방법](./search-security-rbac.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure Active Directory (Azure AD)에서 SSO 인증을 사용 하 여 Azure Resource Manager을 통해 지원 되는 관리 작업에 대 한 검색 서비스 정보에 액세스 합니다. 

조직의 기존 id를 사용 하 여 서비스에 대해 SSO를 사용 하도록 설정 하 여 id 및 자격 증명의 수를 줄이는 프로세스를 설정 합니다.

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory (Azure AD) MULTI-FACTOR AUTHENTICATION (MFA) 기능을 사용 하도록 설정 하 고 Security Center의 Id 및 액세스 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: PAW (권한 Multi-Factor Authentication 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 액세스 하도록 구성 된 MFA (권한 있는 액세스 워크스테이션)를 사용 합니다.

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [Azure AD MFA를 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우를 감지 하려면 Azure Active Directory (Azure AD) 보안 보고서 및 모니터링을 사용 합니다. Security Center를 사용 하 여 id 및 액세스 작업을 모니터링할 수 있습니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: Cognitive Search에는 적용 되지 않습니다. 승인 된 위치를 액세스 조건으로 사용 하는 것은 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: azure Cognitive Search에서 서비스 수준 관리 작업을 위한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (azure AD)를 사용 합니다. Azure AD id는 검색 서비스 끝점에 대 한 액세스 권한을 부여 하지 않습니다.  인덱스 관리, 인덱스 채우기 및 검색 데이터에 대 한 쿼리와 같은 작업에 대 한 액세스는 API 키를 통해 사용할 수 있습니다.

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Cognitive Search 서비스에 대 한 api 키 만들기 및 관리](./search-security-api-keys.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. Azure AD의 Id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

인덱스 관리, 인덱스 채우기 및 쿼리와 같은 검색 서비스 끝점의 활동에 대 한 Cognitive Search의 진단 로그를 검토 합니다.

- [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure Cognitive Search의 작업 및 활동 모니터링](./search-monitor-usage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**참고**: Azure Active Directory에 액세스 (Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본을 사용 하 여 siem 또는 모니터링 도구와 통합할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화 합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성 합니다.

- [Azure Monitor와 Azure 활동 로그를 통합 하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory (Azure AD) id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 필요에 따라 추가 조사를 위해 데이터를 Azure 센티널로 수집 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md) 

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Cognitive Search에는 적용 되지 않습니다. 고객 Lockbox에서 Cognitive Search를 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 가상 네트워크/서브넷으로 구분 되며, 적절 하 게 태그가 지정 되 고, 네트워크 보안 그룹 또는 Azure 방화벽 내에서 보안이 유지 됩니다. 중요 한 데이터를 저장 하거나 처리 하는 리소스는 격리 되어야 합니다. Private 링크를 사용 하 여 Cognitive Search 개인 끝점을 구성 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md) 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [Cognitive Search에 대 한 개인 끝점을 만드는 방법](./service-create-private-endpoint.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: network 경계에서 Azure Marketplace의 타사 솔루션을 사용 하 여 중요 한 정보를 무단으로 전송 하는 행위를 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송을 차단 합니다.

Microsoft는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 처리 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Cognitive Search 전송 계층 보안 1.2을 사용 하 여 전송 중인 데이터를 암호화 하 고 모든 연결에 대해 항상 암호화 (SSL/TLS)를 적용 합니다. 이렇게 하면 모든 데이터가 클라이언트와 서비스 간에 "전송 중"으로 암호화 됩니다.

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Cognitive Search 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다. 

Microsoft는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 처리 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: 서비스 관리의 경우 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 키와 구성에 대 한 액세스를 관리 합니다. 인덱싱 및 쿼리와 같은 콘텐츠 작업의 경우 Cognitive Search는 id 기반 액세스 제어 모델 대신 키를 사용 합니다. Azure RBAC를 사용 하 여 키에 대 한 액세스를 제어 합니다.
- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md) 

 
- [Cognitive Search에 대 한 관리 액세스를 위해 역할을 사용 하는 방법](./search-security-rbac.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 지침은 계산 리소스를 위한 것입니다. 

Microsoft는 Cognitive Search에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Cognitive Search는 Microsoft 관리 키를 사용 하 여 미사용 상태의 인덱싱된 콘텐츠를 자동으로 암호화 합니다. 더 많은 보호가 필요한 경우 Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 두 번째 암호화 계층으로 기본 암호화를 보완할 수 있습니다.

- [Azure Cognitive Search에서 데이터 암호화를 위해 고객이 관리 하는 키 구성](./search-security-manage-encryption-keys.md)

- [Azure에서 미사용 암호화 이해](../security/fundamentals/encryption-atrest.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 프로덕션 인스턴스 Cognitive Search 및 기타 중요 한 리소스 또는 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

- [Cognitive Search 작업에 대 한 경고를 만드는 방법](./search-monitor-logs.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 현재 Cognitive Search 사용할 수 없습니다.  검색 서비스 콘텐츠를 저장 하는 클러스터의 경우 Microsoft는 해당 클러스터의 취약성 관리를 담당 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: Cognitive Search에는 적용 되지 않습니다. Microsoft는 Cognitive Search 서비스를 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Cognitive Search에는 적용 되지 않습니다. 취약성 검사 결과에 대 한 표준 위험 등급 또는 점수 매기기 시스템이 준비 되지 않았습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독의 모든 리소스 (예: 계산, 저장소, 네트워크, 포트, 프로토콜 등)를 쿼리하고 검색 합니다.  

테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독에 있는 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.  

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure 구독을 보는 방법](/powershell/module/az.accounts/get-azsubscription) 

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 사용 하 여 Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.
- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md) 

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md) 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: Cognitive Search에서 인덱싱 및 기술 처리와 관련 된 승인 된 Azure 리소스 목록을 정의 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: 이전에 조직 정책과 표준에 따라 사용 하도록 승인 된 azure 리소스의 인벤토리를 정의한 다음 Azure Policy 또는 Azure 리소스 그래프를 사용 하 여 승인 되지 않은 azure 리소스를 모니터링 하는 것이 좋습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 지침은 계산 리소스를 위한 것입니다.

조직 정책 및 보안 표준에 따라 승인 된 것으로 간주 되는 소프트웨어 응용 프로그램의 인벤토리가 있고 Azure 계산 리소스에 설치 된 승인 되지 않은 소프트웨어 타이틀을 모니터링 하는 것이 좋습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Cognitive Search에는 적용 되지 않습니다. 계산 리소스를 노출 하지 않거나 해당 리소스에 소프트웨어 응용 프로그램을 설치할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하거나 검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md) 

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스에서 실행 되는 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: 서비스 관리를 위해 Azure 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다. 

다른 모든 작업에 대 한 요청을 인증 하는 데 사용 되는 키에 대 한 액세스를 제어 합니다. 특히 Cognitive Search의 콘텐츠와 관련 된 작업

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft. Search" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Cognitive Search 리소스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같은 Cognitive Search 서비스에 대해 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

- Azure 리소스에 대한 감사 로깅 사용

Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다. 

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다. 

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](./security-controls-policy.md)

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [없는 경우 배포] 효과를 사용 하 여 Cognitive Search 서비스 리소스에서 보안 설정을 적용 합니다. 

Azure Resource Manager 템플릿은 조직에서 요구 하는 Azure 리소스의 보안 구성을 유지 하는 데 사용할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](./security-controls-policy.md)

- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/index)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy를 사용 하 여 Cognitive Search 서비스 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. 

별칭을 사용 하 여 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다. 

또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포 하 고 정책 예외를 관리할 수 있습니다. 

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](./security-controls-policy.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Security Center을 사용 하 여 Cognitive Search 서비스 리소스의 기준 검색을 수행할 수 있습니다.  또한 Azure Policy를 사용 하 여 리소스 구성을 경고 하 고 감사 합니다. 

- [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

- [Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤](./security-controls-policy.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure 관리 id를 Azure Key Vault와 함께 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 합니다.
- [Azure 리소스에 관리 되는 id를 사용 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Key Vault를 만드는 방법](../key-vault/general/quick-create-portal.md) 

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](../key-vault/general/assign-access-policy-portal.md) 

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure 관리 id를 사용 하 여 Azure Active Directory (azure AD)에서 자동으로 관리 되는 id를 사용 하 여 Key Vault 및 인덱서 데이터 원본 등의 다른 azure 서비스에 대 한 Cognitive Search 액세스를 제공 합니다. 관리 id를 사용 하면 코드에 자격 증명 없이 Azure Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다. 

- [관리 id를 사용 하 여 데이터 원본에 대 한 인덱서 연결 설정](./search-howto-managed-identities-data-sources.md)

- [관리 id를 사용 하 여 데이터 암호화에 대 한 고객 관리 키 구성](./search-security-manage-encryption-keys.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: Cognitive Search에는 적용 되지 않습니다. 코드를 호스팅하지 않으며 식별할 자격 증명이 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙에서 관리 되는 맬웨어 방지 소프트웨어 사용

**지침**: Cognitive Search에는 적용 되지 않습니다. 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 azure 서비스 (예: Azure Cognitive Search)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Cognitive Search, Blob Storage, Azure SQL Database 등의 비 계산 Azure 리소스에 업로드 되는 콘텐츠를 사전 검사 합니다. 

비 계산 Azure 리소스에 업로드 되는 모든 콘텐츠를 미리 검색 하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해 서 고객 콘텐츠의 맬웨어 방지 검색을 수행할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: Cognitive Search에는 적용 되지 않습니다. 맬웨어 방지 솔루션이 해당 리소스에 설치 되는 것을 허용 하지 않습니다. 기본 플랫폼의 경우 Microsoft는 맬웨어 방지 소프트웨어 및 서명 업데이트를 처리 합니다. 

조직에서 소유 하 고 검색 솔루션에서 사용 되는 모든 계산 리소스에 대해 Security Center, 계산 앱의 권장 사항을 따라 &amp; 모든 끝점이 최신 서명으로 최신 상태 인지 확인 합니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 검색 서비스에 저장 된 콘텐츠는 Azure Backup 또는 다른 기본 제공 메커니즘을 통해 백업할 수 없지만 응용 프로그램 소스 코드 및 기본 데이터 원본에서 인덱스를 다시 작성 하거나, 인덱싱된 콘텐츠를 검색 하 고 저장 하는 사용자 지정 도구를 빌드할 수 있습니다.

- [GitHub 인덱스-백업-복원 샘플](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 현재 Cognitive Search는 검색 서비스에서 데이터에 대 한 자동화 된 백업을 지원 하지 않으며 수동 프로세스를 통해 백업 해야 합니다.  Azure Key Vault에서 고객이 관리 하는 키를 백업할 수도 있습니다. 

- [Azure Cognitive Search 인덱스 백업 및 복원](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure에서 Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 현재 Cognitive Search는 검색 서비스에서 데이터에 대 한 자동화 된 백업을 지원 하지 않으며 수동 프로세스를 통해 백업 및 복원 해야 합니다.  백업 프로세스의 종단 간 무결성을 위해 수동으로 백업한 콘텐츠의 데이터 복원을 정기적으로 수행 합니다.

- [Azure Cognitive Search 인덱스 백업 및 복원](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure에서 Key Vault 키를 복원 하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: 현재 Cognitive Search는 검색 서비스의 데이터에 대해 자동화 된 백업을 지원 하지 않으며 수동 프로세스를 통해 백업 해야 합니다.  Azure Key Vault에서 고객이 관리 하는 키를 백업할 수도 있습니다. 

Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하 여 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 합니다. 수동 백업을 저장 하는 데 Azure Storage를 사용 하는 경우 blob 또는 blob 스냅숏이 삭제 될 때 일시 삭제를 사용 하 여 데이터를 저장 하 고 복구 합니다. 

- [Azure Cognitive Search 인덱스 백업 및 복원](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하는 방법](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Azure Blob 저장소에 대 한 일시 삭제](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 경고를 실행 하는 데 사용 되는 Security Center를 찾고 있는 경우와 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 따라 결정 됩니다.

또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다. 사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다.

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시, "IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드"를 참조 하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.
- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.