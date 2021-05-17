---
title: Azure Database for MariaDB에 대한 Azure 보안 기준
description: Azure Database for MariaDB 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/29/2021
ms.service: mariadb
ms.topic: conceptual
ms.custom: subject-security-benchmark
ms.openlocfilehash: fd169738c138d56932dbab87047b89e373b9c6ec
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227673"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대한 Azure 보안 기준

이 보안 기준에서는 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Azure Database for MariaDB에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Azure Database for MariaDB에 적용되는 관련 지침에서 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Azure Database for MariaDB에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다.

Azure Database for MariaDB가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Azure Database for MariaDB 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 프라이빗 엔드포인트로 Azure Database for MariaDB에 대한 Private Link를 구성합니다. Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure의 다양한 PaaS 서비스에 연결할 수 있습니다. Azure Private Link는 기본적으로 개인 VNet(Virtual Network) 내에 Azure 서비스를 제공합니다. 가상 네트워크와 MariaDB 인스턴스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동합니다.

또는 가상 네트워크 서비스 엔드포인트를 사용하여 Azure Database for MariaDB 구현에 대한 네트워크 액세스를 보호하고 제한할 수 있습니다. 가상 네트워크 규칙은 가상 네트워크의 특정 서브넷에서 보낸 통신을 Azure Database for MariaDB에서 허용할지 여부를 제어하는 하나의 방화벽 보안 기능입니다.

방화벽 규칙을 사용하여 Azure Database for MariaDB를 보호할 수도 있습니다. 서버 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

- [Azure Database for MariaDB에 대한 Private Link를 구성하는 방법](howto-configure-privatelink-portal.md)

- [Azure Database for MariaDB 서버에서 VNet 서비스 엔드포인트 및 VNet 규칙을 만들고 관리하는 방법](howto-manage-vnet-portal.md)

- [Azure Database for MariaDB 방화벽 규칙을 구성하는 방법](concepts-firewall-rules.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 관한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: Azure Database for MariaDB 서버를 프라이빗 엔드포인트에서 보호하는 경우 가상 머신을 동일한 가상 네트워크에 배포할 수 있습니다. 데이터 반출의 위험을 줄이기 위해 NSG(네트워크 보안 그룹)를 사용할 수 있습니다. NSG 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [Azure Database for MariaDB에 대한 Private Link를 구성하는 방법](howto-configure-privatelink-portal.md)

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Database for MariaDB용 Advanced Threat Protection을 사용합니다. Advanced Threat Protection은 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상 활동을 탐지합니다.

DDoS 공격으로부터 보호하기 위해 Azure Database for MariaDB 인스턴스에 연결된 가상 네트워크에서 DDoS Protection 표준을 사용하도록 설정합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [Azure Database for MariaDB용 Advanced Threat Protection을 구성하는 방법](howto-database-threat-protection-portal.md)

- [DDoS 보호 구성 방법](/azure/virtual-network/manage-ddos-protection)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Azure Database for MariaDB 서버를 프라이빗 엔드포인트에서 보호하는 경우 가상 머신을 동일한 가상 네트워크에 배포할 수 있습니다. 그런 다음, 데이터 반출의 위험을 줄이기 위해 NSG(네트워크 보안 그룹)를 구성할 수 있습니다. NSG 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure Database for MariaDB용 Advanced Threat Protection을 사용합니다. Advanced Threat Protection은 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상 활동을 탐지합니다.

- [Azure Database for MariaDB용 Advanced Threat Protection을 구성하는 방법](howto-database-threat-protection-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Database for MariaDB 인스턴스에 액세스해야 하는 리소스의 경우 가상 네트워크 서비스 태그를 사용하여 NSG 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: SQL.WestUs)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

Azure Database for MariaDB는 "Microsoft.Sql" 서비스 태그를 사용합니다.

- [서비스 태그를 사용하는 방법에 대한 자세한 정보](../virtual-network/service-tags-overview.md)

- [Azure Database for MariaDB에 대한 서비스 태그 사용 이해](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하여 Azure Database for MariaDB 인스턴스에 연결된 네트워크 설정 및 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.DBforMariaDB" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Database for MariaDB 인스턴스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 네트워킹 또는 Azure Database for MariaDB 인스턴스와 관련된 기본 제공 정책 정의를 사용할 수도 있습니다.

- DDoS Protection 표준을 사용하도록 설정해야 합니다.

- 프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.

- MariaDB 서버는 가상 네트워크 서비스 엔드포인트를 사용해야 함

참조 설명서:

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: MariaDB 인스턴스에 대한 네트워크 보안 및 트래픽 흐름과 관련된 리소스를 위해 태그를 사용하여 메타데이터 및 논리적 조직을 제공합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure Database for MariaDB 인스턴스와 관련된 네트워크 리소스에 대한 변경을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 진단 설정 및 서버 로그를 사용하도록 설정하고, 로그를 수집하여 Azure Database for MariaDB 인스턴스에서 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, Azure Storage 계정을 장기/보관 스토리지에 사용할 수 있습니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Azure Database for MariaDB에 대한 서버 로그를 구성하고 액세스하는 방법](concepts-server-logs.md)

- [Azure Database for MariaDB에 대한 감사 로그를 구성하고 액세스하는 방법](howto-configure-audit-logs-portal.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사, 보안 및 진단 로그에 액세스하기 위해 Azure Database for MariaDB 인스턴스에서 진단 설정을 사용하도록 설정합니다. 특히 MariaDB 감사 로그를 사용하도록 설정했는지 확인합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다. Azure 활동 로그 진단 설정을 사용하도록 설정하고, 로그를 동일한 Log Analytics 작업 영역 또는 스토리지 계정에 보낼 수도 있습니다.

- [Azure Database for MariaDB에 대한 서버 로그를 구성하고 액세스하는 방법](concepts-server-logs.md)

- [Azure Database for MariaDB에 대한 감사 로그를 구성하고 액세스하는 방법](howto-configure-audit-logs-portal.md)

- [Azure 활동 로그에 대한 진단 설정을 구성하는 방법](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor 내에서 조직의 준수 규정에 따라 Azure Database for MariaDB 로그를 보관하는 데 사용되는 Log Analytics 작업 영역에 대한 보존 기간을 설정합니다. 장기/보관 스토리지에 Azure Storage 계정을 사용합니다.

- [Log Analytics 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Azure Storage 계정에 리소스 로그 저장](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상 동작에 대한 MariaDB 인스턴스의 로그를 분석하고 모니터링합니다. Azure Monitor의 Log Analytics 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Log Analytics 작업 영역에 대한 자세한 정보](/azure/azure-monitor/log-query/get-started-portal)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](/azure/azure-monitor/log-query/get-started-queries)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: MariaDB용 Advanced Threat Protection을 사용하도록 설정합니다. Azure Database for MariaDB용 Advanced Threat Protection은 비정상적이며 유해할 가능성이 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다.

또한 MariaDB에 대한 서버 로그 및 진단 설정을 사용하도록 설정하고 Log Analytics 작업 영역으로 로그를 보낼 수 있습니다. SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공하므로 Log Analytics 작업 영역을 Azure Sentinel에 온보딩합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

- [MariaDB용 Advanced Threat Protection을 사용하도록 설정하는 방법](howto-database-threat-protection-portal.md)

- [MariDB용 서버 로그 구성 및 액세스 방법](concepts-server-logs.md)

- [MariDB용 감사 로그 구성 및 액세스 방법](howto-configure-audit-logs-portal.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: MariaDB 인스턴스의 관리 평면(예: Azure portal/Azure Resource Manager)에 대한 관리 액세스 권한이 있는 사용자 계정의 인벤토리를 유지 관리합니다. 또한 MariaDB 인스턴스의 데이터 평면에 대한 액세스 권한이 있는 관리 계정의 인벤토리를 유지 관리합니다. (MariaDB 서버를 만들 때 관리자 사용자에 대한 자격 증명을 제공합니다. 이 관리자를 사용하여 추가 MariaDB 사용자를 만들 수 있습니다.)

- [MariaDB에 대한 액세스 관리 이해](https://docs.microsoft.com/azure/mariadb/concepts-security#access-management)

- [Azure 구독에 대한 Azure 기본 제공 역할 이해](../role-based-access-control/built-in-roles.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure AD(Azure Active Directory)에는 기본 암호 개념이 없습니다.

Azure는 MariaDB 리소스 자체를 만들 때 강력한 암호를 사용하여 관리 사용자를 만들도록 합니다. 그러나 MariaDB 인스턴스가 만들어지면 계정을 만든 첫 번째 서버 관리자 계정을 사용하여 추가 사용자를 만들고 해당 사용자에게 관리 액세스 권한을 부여할 수 있습니다. 이러한 계정을 만드는 경우 각 계정마다 서로 다른 강력한 암호를 구성해야 합니다.

- [MariaDB에 대한 추가 계정을 만드는 방법](howto-create-users.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: MariaDB 인스턴스에 액세스할 수 있는 전용 관리 계정의 사용에 대한 표준 운영 프로시저를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

- [Azure Security Center ID 및 액세스 이해](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: MariaDB에 대한 데이터 평면 액세스는 데이터베이스 내에 저장된 ID로 제어되며 SSO를 지원하지 않습니다. MariaDB에 대한 제어 평면 액세스는 REST API를 통해 사용할 수 있으며 SSO를 지원합니다. 인증하려면 요청에 대한 인증 헤더를 Azure AD(Azure Active Directory)에서 가져온 JSON Web Token으로 설정합니다.

- [Azure Database for MariaDB REST API 이해](/rest/api/mariadb/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: Azure 리소스에 로그인하여 구성하도록 다단계 인증이 구성된 PAW(Privileged Access Workstation)를 사용합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: MariaDB에 대한 Advanced Threat Protection을 사용하여 의심스러운 활동에 대한 경고를 생성합니다.

또한 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용할 수 있습니다. Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [MariaDB용 Advanced Threat Protection을 설정하는 방법](howto-database-threat-protection-portal.md)

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](/azure/active-directory/reports-monitoring/concept-risk-events)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 액세스를 허용하여 MariaDB와 같은 Azure 리소스에 대한 액세스를 제한합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure AD 인증은 MariaDB 데이터 평면에 직접 액세스하는 데 사용할 수 없지만, Azure AD 자격 증명은 MariaDB 관리자 계정을 제어하기 위해 관리 평면 수준(예: Azure Portal)에서 관리하는 데 사용할 수 있습니다.

- [MariaDB용 관리자 암호를 업데이트하는 방법](https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory) 로그를 검토하여 MariaDB 관리 역할이 있는 계정을 비롯한 부실 계정을 검색할 수 있습니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, MariaDB에 액세스하는 데 사용할 수 있는 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 적절한 사용자만 계속 액세스할 수 있도록 사용자 액세스를 90일마다 정기적으로 검토해야 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: MariaDB및 Azure AD(Azure Active Directory)에 대한 진단 설정을 사용하도록 설정하여 모든 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 작업 영역 내에서 원하는 경고(예: 인증 시도 실패)를 구성합니다.

- [MariaDB용 서버 로그 구성 및 액세스 방법](concepts-server-logs.md)

- [MariDB용 감사 로그 구성 및 액세스 방법](howto-configure-audit-logs-portal.md)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: MariaDB용 Advanced Threat Protection을 사용하도록 설정합니다. Azure Database for MariaDB용 Advanced Threat Protection은 비정상적이며 유해할 가능성이 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다.

Azure AD(Azure Active Directory)의 ID 보호 및 위험 검색 기능을 사용하여 탐지된 의심스러운 작업에 대해 자동화된 대응을 구성할 수 있습니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통해 자동화된 대응을 사용하도록 설정할 수 있습니다.

- [MariaDB용 Advanced Threat Protection을 사용하도록 설정하는 방법](howto-database-threat-protection-portal.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure Database for MariaDB 인스턴스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Private Link, 서비스 엔드포인트 및/또는 MariaDB 방화벽 규칙의 조합을 사용하여 MariaDB 인스턴스에 대한 네트워크 액세스를 격리하고 제한합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [Azure Database for MariaDB에 대한 Private Link를 구성하는 방법](concepts-data-access-security-private-link.md)

- [Azure Database for MariaDB에 대한 서비스 엔드포인트를 구성하는 방법](howto-manage-vnet-portal.md)

- [Azure Database for MariaDB에 대한 방화벽 규칙을 구성하는 방법](concepts-firewall-rules.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Azure VM을 사용하여 MariaDB 인스턴스에 액세스하는 경우 데이터 반출 가능성을 완화하기 위해 Private Link, MariaDB 네트워크 구성, 네트워크 보안 그룹 및 서비스 태그를 활용합니다.

Microsoft는 MariaDB의 기본 인프라를 관리하고, 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

- [Azure Database for MariaDB에 대한 데이터 반출을 완화하는 방법](concepts-data-access-security-private-link.md)

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Database for MariaDB는 이전에 SSL(Secure Sockets Layer)이라고 알려진 TLS(전송 계층 보안)를 사용하여 Azure Database for MariaDB 서버를 클라이언트 애플리케이션에 연결할 수 있도록 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간에 TLS 연결을 적용하면 서버와 애플리케이션 간의 데이터 스트림을 암호화하여 "메시지 가로채기(man in the middle)" 공격으로부터 보호할 수 있습니다. Azure Portal에서 모든 MariaDB 인스턴스에 대해 **SSL 연결 적용** 이 사용하도록 설정되어 있는지 확인합니다.

- [MariaDB에 대한 전송 중 암호화를 구성하는 방법](howto-configure-ssl.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Database for MariaDB에서는 아직 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 MariaDB 관리 평면(Azure Portal/Azure Resource Manager)용 Azure Database에 대한 액세스를 관리합니다. 데이터 평면 액세스(데이터베이스 자체 내)의 경우 SQL 쿼리를 사용하여 사용자를 만들고 사용자 권한을 구성합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

- [MariaDB용 SQL을 사용하여 사용자 액세스를 구성하는 방법](howto-create-users.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 Azure Database for MariaDB의 프로덕션 인스턴스 및 기타 중요하거나 관련된 리소스에 변경되는 경우에 대한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 현재 사용할 수 없음. Azure Security Center는 Azure Database for MariaDB 서버에 대한 취약성 평가를 아직 지원하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(Azure Database for MariaDB 서버 포함)를 쿼리하고 검색합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure Database for MariaDB 서버 및 기타 관련 리소스에 태그를 적용하여 논리적인 분류 체계로 구성합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure Database for MariaDB 서버 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스와 Azure 전체를 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 중요한 정보를 포함하는 Azure Database for MariaDB 서버와 같이 높은 수준의 보안 환경에서 리소스를 만들고 변경하는 것을 방지할 수 있습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy로 Azure Database for MariaDB 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.DBforMariaDB" 네임스페이스에 Azure Policy 별칭을 사용하여 Azure Database for MariaDB 서버의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Database for MariaDB 서버와 관련된 기본 제공 정책 정의를 활용할 수도 있습니다.

- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 함

자세한 내용은 다음 참조 문서를 참조하세요.

- [사용 가능한 Azure Policy 별칭 확인 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 Azure Database for MariaDB 서버 및 관련 리소스에 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: "Microsoft.DBforMariaDB" 네임스페이스에 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.DBforMariaDB" 네임스페이스에 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure Database for MariaDB 인스턴스 및 관련 리소스에 대한 구성을 자동으로 적용하려면 Azure Policy [감사], [거부] 및 [없는 경우 배포]를 사용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Database for MariaDB 서버에 액세스하는 데 사용되는 Azure App Service에서 실행되는 Azure Virtual Machines 또는 웹 애플리케이션의 경우 Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 Azure Database for MariaDB 서버 비밀 관리를 간소화하고 보호합니다. Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다.

- [Azure 관리 ID와 통합하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault를 만드는 방법](../key-vault/general/quick-create-portal.md)

- [Key Vault에 인증하는 방법](../key-vault/general/authentication.md)

- [Key Vault 액세스 정책을 할당하는 방법](../key-vault/general/assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Database for MariaDB 서버는 현재 데이터베이스에 액세스할 수 있는 Azure AD(Azure Active Directory) 인증을 지원하지 않습니다. Azure Database for MariaDB 서버를 만드는 동안 관리자 사용자에 대한 자격 증명을 제공합니다. 이 관리자를 사용하여 추가 MariaDB 사용자를 만들 수 있습니다.

Azure Database for MariaDB 서버에 액세스하는 데 사용되는 Azure App Service에서 실행되는 Azure Virtual Machines 또는 웹 애플리케이션의 경우 Azure Key Vault와 함께 관리 ID를 사용하여 Azure Database for MariaDB 서버에 대한 자격 증명을 저장하고 검색합니다. Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다.

관리 ID를 사용하여 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [관리 ID를 구성하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure 관리 ID와 통합하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure Database for MariaDB 서버)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

비컴퓨팅 Azure 리소스(예: App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB 서버 등)로 업로드되는 콘텐츠를 미리 검사합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Database for MariaDB는 전체, 차등 및 트랜잭션 로그 백업을 수행합니다.  Azure Database for MariaDB는 자동으로 서버 백업을 만들어 사용자가 로컬로 구성한 중복 스토리지 또는 지역 중복 스토리지에 저장합니다. 백업을 사용하여 특정 시점의 서버를 복원할 수 있습니다. 백업 및 복원은 실수로 인한 손상이나 삭제로부터 데이터를 보호하므로 비즈니스 연속성 전략의 필수적인 부분입니다.  기본 백업 보존 기간은 7일입니다. 필요에 따라 최대 35일까지 구성할 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

- [MariaDB에 대한 백업 이해](concepts-backup.md)

- [MariaDB 초기 구성 이해](tutorial-design-database-using-portal.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Database for MariaDB는 자동으로 서버 백업을 만들어 사용자가 로컬로 구성한 중복 스토리지 또는 지역 중복 스토리지에 저장합니다. 백업을 사용하여 특정 시점의 서버를 복원할 수 있습니다.  백업 및 복원은 실수로 인한 손상이나 삭제로부터 데이터를 보호하므로 비즈니스 연속성 전략의 필수적인 부분입니다.

MariaDB 서버에 저장된 데이터에 대해 클라이언트 쪽 데이터 암호화에 Key Vault를 사용하는 경우 키의 자동 백업을 정기적으로 수행해야 합니다.

- [MariaDB에 대한 백업 이해](concepts-backup.md)

- [Key Vault 키를 백업하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**책임**: 공유됨

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 제어와 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure Database for MariaDB에서는 정기적으로 백업 테스트를 위해 원래 서버의 백업에서 복원을 수행합니다. 사용할 수 있는 두 가지 유형의 복원으로 특정 시점 복원 및 지리적 복원이 있습니다. 특정 시점 복원은 백업 중복 옵션을 통해 사용할 수 있으며, 새 서버를 원본 서버와 동일한 지역에 만듭니다. 지리적 복원은 서버를 지역 중복 스토리지로 구성하고, 이 서버를 다른 지역으로 복원할 수 있는 경우에만 사용할 수 있습니다.

예상 복구 시간은 데이터베이스 크기, 트랜잭션 로그 크기, 네트워크 대역폭 및 동일한 지역에서 동시에 복구되는 데이터베이스의 총 수를 포함한 여러 요소에 따라 달라집니다. 복구 시간은 일반적으로 12시간 미만입니다.

- [Azure Database for MariaDB의 백업 및 복원 이해](https://docs.microsoft.com/azure/mariadb/concepts-backup#restore)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure Database for MariaDB는 전체, 차등 및 트랜잭션 로그 백업을 수행합니다. 이러한 백업을 사용하면 서버를 구성된 백업 보존 기간 내의 특정 시점으로 복원할 수 있습니다. 기본 백업 보존 기간은 7일입니다. 필요에 따라 최대 35일까지 구성할 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

- [Azure Database for MariaDB의 백업 및 복원 이해](concepts-backup.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [고객이 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획을 만들 수도 있음](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 메트릭의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한, 태그를 사용하여 구독을 명확히 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 확실히 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 'Logic Apps'를 통해 응답을 자동으로 트리거함으로써 Azure 리소스를 보호합니다.
    

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft 클라우드 침투 테스트 시행 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
