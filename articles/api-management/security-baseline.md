---
title: API Management에 대 한 Azure 보안 기준
description: API Management에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aa59a37f3a4413c92a483746ed4a08c363ab8457
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796526"
---
# <a name="azure-security-baseline-for-api-management"></a>API Management에 대 한 Azure 보안 기준

API Management에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

모범 사례 지침을 사용 하 여 Azure에서 클라우드 솔루션을 보호할 수 있는 방법에 대 한 권장 사항을 제공 하는 [Azure Security 벤치 마크 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서이 서비스에 대 한 기준을 가져옵니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용 하 여 리소스를 보호 합니다.

**지침**: azure API Management는 azure Virtual Network (Vnet) 내에 배포할 수 있으므로 네트워크 내의 백 엔드 서비스에 액세스할 수 있습니다. 개발자 포털 및 API Management 게이트웨이는 인터넷 (외부)에서 또는 Vnet (내부) 내 에서만 액세스할 수 있도록 구성할 수 있습니다.
- 외부: 외부 부하 분산 장치를 통해 공용 인터넷에서 API Management 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.
- 내부: 내부 부하 분산 장치를 통해 가상 네트워크 내에서만 API Management 게이트웨이 및 개발자 포털에 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

네트워크 보안 그룹을 사용 하 여 API Management를 배포 하는 서브넷에 대 한 인바운드 및 아웃 바운드 트래픽을 제어할 수 있습니다.

* [가상 네트워크에서 Azure API Management를 사용하는 방법](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [내부 가상 네트워크에서 Azure API Management를 사용하는 방법](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [Application Gateway를 사용 하 여 내부 VNET에 API Management 통합](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 Nic의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: API Management 배포 되는 서브넷에 대 한 인바운드 및 아웃 바운드 트래픽은 Nsgs (네트워크 보안 그룹)를 사용 하 여 제어할 수 있습니다. API Management 서브넷에 NSG를 배포 하 고, NSG 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

주의: API Management 서브넷에 NSG를 구성 하는 경우 열려 있어야 하는 포트 집합이 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다.

* [Azure API Management에 대 한 NSG 구성 이해](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [NSG 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요 한 웹 응용 프로그램 보호

**지침**: 중요 한 웹/HTTP api를 보호 하려면 내부 모드에서 Virtual Network (Vnet) 내의 API Management를 구성 하 고 Azure 애플리케이션 게이트웨이를 구성 합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시 역할을 하며 L7 부하 분산, 라우팅, WAF (웹 응용 프로그램 방화벽) 및 기타 서비스를 제공 합니다.

내부 Vnet에서 프로 비전 된 API Management Application Gateway 프런트 엔드로 결합 하면 다음과 같은 시나리오를 사용할 수 있습니다.
- 단일 API Management 리소스를 사용 하 여 내부 소비자와 외부 소비자 모두에 게 모든 Api를 노출 합니다.
- 단일 API Management 리소스를 사용 하 여 Api의 하위 집합을 외부 소비자에 게 노출 합니다.
- 공용 인터넷에서 API Management에 대 한 액세스를 전환 하는 방법을 제공 합니다.

참고:이 기능은 API Management 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

* [Application Gateway를 사용 하 여 내부 VNET에 API Management를 통합 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Azure 애플리케이션 게이트웨이 이해](https://docs.microsoft.com/azure/application-gateway/)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 내부 모드에서 Vnet (Virtual Network) 내의 API Management를 구성 하 고 Azure 애플리케이션 게이트웨이를 구성 합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시 역할을 하며 L7 부하 분산, 라우팅, WAF (웹 응용 프로그램 방화벽) 및 기타 서비스를 제공 합니다.

내부 Vnet에서 프로 비전 된 API Management Application Gateway 프런트 엔드로 결합 하면 다음과 같은 시나리오를 사용할 수 있습니다.
- 단일 API Management 리소스를 사용 하 여 내부 소비자와 외부 소비자 모두에 게 모든 Api를 노출 합니다.
- 단일 API Management 리소스를 사용 하 여 Api의 하위 집합을 외부 소비자에 게 노출 합니다.
- 공용 인터넷에서 API Management에 대 한 액세스를 전환 하는 방법을 제공 합니다.

참고:이 기능은 API Management 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

DDoS (분산 서비스 거부) 공격 으로부터 보호 하기 위해 API Management 배포와 연결 된 Vnet에 Azure DDoS Protection 표준을 사용 하도록 설정 합니다.

Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 또는 사용 하지 않는 인터넷 IP 주소와의 통신을 거부 합니다.

* [Application Gateway를 사용 하 여 내부 VNET에 API Management를 통합 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Azure 애플리케이션 게이트웨이 이해](https://docs.microsoft.com/azure/application-gateway/)

* [Azure DDoS Protection Standard를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: API Management 배포 되는 서브넷에 대 한 인바운드 및 아웃 바운드 트래픽은 Nsg (네트워크 보안 그룹)를 사용 하 여 제어할 수 있습니다. API Management 서브넷에 NSG를 배포 하 고, NSG 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

주의: API Management 서브넷에 NSG를 구성 하는 경우 열려 있어야 하는 포트 집합이 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다.

* [Azure API Management에 대 한 NSG 구성 이해](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [NSG 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS)을 배포 합니다.

**지침**: 내부 모드에서 Vnet (Virtual Network) 내의 API Management를 구성 하 고 Azure 애플리케이션 게이트웨이를 구성 합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시 역할을 하며 L7 부하 분산, 라우팅, WAF (웹 응용 프로그램 방화벽) 및 기타 서비스를 제공 합니다. Application Gateway WAF는 일반적인 보안 익스플로잇 및 취약성 으로부터 보호 하 고 다음과 같은 두 가지 모드에서 실행할 수 있습니다.
- 탐지 모드: 모든 위협 경고를 모니터링하고 기록합니다. 진단 섹션에서 Application Gateway에 대 한 로깅 진단 기능을 켤 수 있습니다. WAF 로그가 선택 되어 켜져 있는지 확인 해야 합니다. 웹 애플리케이션 방화벽을 탐지 모드로 실행하면 수신 요청을 차단하지 않습니다.
- 방지 모드: 규칙에서 탐지하는 침입 및 공격을 차단합니다. 공격자는 "403 무단 액세스" 예외를 수신하고, 연결이 종료됩니다. 방지 모드는 이러한 공격을 WAF 로그에 기록합니다.

내부 Vnet에서 프로 비전 된 API Management Application Gateway 프런트 엔드로 결합 하면 다음과 같은 시나리오를 사용할 수 있습니다.
- 단일 API Management 리소스를 사용 하 여 내부 소비자와 외부 소비자 모두에 게 모든 Api를 노출 합니다.
- 단일 API Management 리소스를 사용 하 여 Api의 하위 집합을 외부 소비자에 게 노출 합니다.
- 공용 인터넷에서 API Management에 대 한 액세스를 전환 하는 방법을 제공 합니다.

참고:이 기능은 API Management 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

* [Application Gateway를 사용 하 여 내부 VNET에 API Management를 통합 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Azure 애플리케이션 게이트웨이 WAF 이해](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 응용 프로그램에 대 한 트래픽 관리

**지침**: WEB/HTTP api로 흐르는 트래픽을 관리 하려면 외부 또는 내부 모드의 App Service Environment와 연결 된 Virtual Network (Vnet)에 API Management를 배포 합니다.

내부 모드에서 API Management 앞에 Azure 애플리케이션 게이트웨이를 구성 합니다. Application Gateway는 PaaS 서비스입니다. 역방향 프록시 역할을 하며 L7 부하 분산, 라우팅, WAF (웹 응용 프로그램 방화벽) 및 기타 서비스를 제공 합니다. Application Gateway WAF는 일반적인 보안 익스플로잇 및 취약성 으로부터 보호를 제공 합니다.

내부 Vnet에서 프로 비전 된 API Management Application Gateway 프런트 엔드로 결합 하면 다음과 같은 시나리오를 사용할 수 있습니다.
- 단일 API Management 리소스를 사용 하 여 내부 소비자와 외부 소비자 모두에 게 모든 Api를 노출 합니다.
- 단일 API Management 리소스를 사용 하 여 Api의 하위 집합을 외부 소비자에 게 노출 합니다.
- 공용 인터넷에서 API Management에 대 한 액세스를 전환 하는 방법을 제공 합니다.

참고:이 기능은 API Management 프리미엄 및 개발자 계층에서 사용할 수 있습니다.

* [외부 소비자에 게 개인 Api를 노출 하는 방법](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Vnet 내에서 API Management를 사용 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Azure Application Gateway의 Azure 웹 애플리케이션 방화벽](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Azure 애플리케이션 게이트웨이 이해](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버 헤드를 최소화 합니다.

**지침**: Vnet (Virtual Network) 서비스 태그를 사용 하 여 API Management 서브넷에 사용 되는 Nsgs (네트워크 보안 그룹)에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: Microsoft.apimanagement)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그가 들어 있는 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다.

주의: API Management 서브넷에 NSG를 구성 하는 경우 열려 있어야 하는 포트 집합이 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다.

* [서비스 태그 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [API Management에 필요한 포트](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대 한 표준 보안 구성 유지

**지침**: Azure API Management 배포와 관련 된 네트워크 설정에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.apimanagement" 및 "" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure API Management 배포 및 관련 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. Azure Virtual Network에 대해 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.
- DDoS Protection 표준을 사용하도록 설정해야 합니다.

또한 Azure 청사진을 사용 하 여 Azure Resource Manager 템플릿, RBAC (역할 기반 액세스 제어) 및 단일 청사진 정의의 정책과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 버전 관리를 통해 새 구독, 환경 및 미세 조정 제어 및 관리에 청사진을 쉽게 적용할 수 있습니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 네트워크 보안 및 트래픽 흐름과 관련 된 Nsgs (네트워크 보안 그룹) 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 모든 규칙에 대 한 비즈니스 필요 및/또는 기간 (등)을 지정할 수 있습니다.

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 검색 합니다.

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 azure API Management 배포와 연결 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 네트워크 리소스에 대 한 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인 하 고 검색 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인 된 시간 동기화 원본을 사용 하십시오.

**지침**: Microsoft는 Azure API Management에 대 한 시간 소스를 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하거나, 장기/보관 저장소 또는 오프 라인 분석을 위해 Azure Storage에 로그를 보내거나, azure Event Hubs를 사용 하 여 다른 곳에서 azure의 다른 분석 솔루션으로 로그를 내보냅니다. Azure API Management는 기본적으로 Azure Monitor 로그 및 메트릭을 출력 합니다. 로깅의 자세한 정도는 서비스 전체 및 API 별로 구성할 수 있습니다.

Azure Monitor 외에도 Azure API Management를 하나 이상의 Azure 애플리케이션 Insights 서비스와 통합할 수 있습니다. Application Insights에 대 한 로깅 설정은 서비스별 또는 API 별로 구성할 수 있습니다.

필요에 따라 Azure 센티널 또는 타사 SIEM (보안 인시던트 및 이벤트 관리)에 대 한 온-보드 데이터를 설정 합니다.

* [진단 설정을 구성 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor 및 타사 SIEM 통합을 시작 하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [사용자 지정 로깅 및 분석 파이프라인을 만드는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [Azure 애플리케이션 Insights와 통합 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대 한 감사 로깅 사용

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고, 보고 및 분석을 위해 활동 로그를 Log Analytics 작업 영역으로 전송 하 고, 장기 보관을 위해 azure 및 기타의 다른 분석 솔루션에서의 azure Event Hubs 내보내기에 대 한 Azure Storage 합니다. Azure 활동 로그 데이터를 사용 하 여 Azure API Management 서비스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

데이터 평면 감사 로깅의 경우 진단 로그는 감사 뿐만 아니라 문제 해결을 위해 중요 한 작업 및 오류에 대 한 다양 한 정보를 제공 합니다. 진단 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 정보를 제공합니다. 진단 로그는 리소스에서 수행하는 작업에 대한 정보를 제공합니다.

* [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure API Management에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기/보관 저장소에 Azure Storage 계정을 사용 합니다.

* [Log Analytics 작업 영역에 대 한 로그 보존 매개 변수를 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Azure Storage 계정에 로그를 보관 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: Azure API Management는 로그 및 메트릭을 지속적으로 Azure Monitor 하 여 api의 상태를 거의 실시간으로 볼 수 있도록 합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용 하 여 경고를 검토, 쿼리, 시각화, 라우팅, 보관 및 구성 하 고 API Management 및 관련 리소스에서 제공 되는 메트릭 및 로그에 대 한 작업을 수행할 수 있습니다. 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다.

필요에 따라 API Management Azure 애플리케이션 Insights와 통합 하 고이를 기본 또는 보조 모니터링, 추적, 보고 및 경고 도구로 사용 합니다.

* [Azure API Management에 대 한 로그를 모니터링 하 고 검토 하는 방법](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [Azure Monitor에서 사용자 지정 쿼리를 수행 하는 방법](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Log Analytics 작업 영역 이해](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Azure 애플리케이션 Insights와 통합 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure 활동 로그 진단 설정 및 azure API Management 인스턴스에 대 한 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, 수집 된 데이터를 기반으로 다양 한 통찰력을 제공 합니다 Log Analytics 작업 영역 쿼리에 기반 하 여 경고를 만들 수 있습니다.

예기치 않은 상황이 발생 하는 경우를 알려 주는 메트릭 경고를 만듭니다. 예를 들어 Azure API Management 인스턴스가 특정 기간 동안 예상 되는 최대 용량을 초과 했거나 미리 정의 된 기간 동안 권한이 없는 게이트웨이 요청이 나 오류가 발생 한 경우 알림을 받습니다.

필요에 따라 API Management Azure 애플리케이션 Insights와 통합 하 고이를 기본 또는 보조 모니터링, 추적, 보고 및 경고 도구로 사용 합니다.

필요에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정할 수 있습니다.

* [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure API Management에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [권한 없는 요청에 대 한 경고 규칙을 구성 하는 방법](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [Azure API management 인스턴스의 용량 메트릭을 보는 방법](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [Azure 애플리케이션 Insights와 통합 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅을 중앙 집중화

**지침**: 해당 사항 없음 Azure API Management는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅을 사용 하도록 설정

**지침**: 해당 사항 없음 Azure API Management는 사용자가 액세스할 수 있는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅을 사용 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: id 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure API Management 제어 평면 (Azure Portal)에 대 한 관리 권한이 있는 계정의 인벤토리를 유지 관리 합니다.

AD (Azure Active Directory)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. API Management는 이러한 역할 및 역할 기반 Access Control를 사용 하 여 API Management 서비스 및 엔터티에 대 한 세분화 액세스 관리를 지원 합니다.

또한 API Management에는 API Management의 사용자 시스템에 기본 제공 관리자 그룹이 포함 되어 있습니다. API Management 그룹은 개발자 포털에서 Api의 표시 여부를 제어 하 고 관리자 그룹의 멤버는 모든 Api를 볼 수 있습니다.

관리 계정의 관리 및 유지 관리에 대 한 Azure Security Center의 권장 사항을 따릅니다.

* [Azure API Management에서 역할 기반 Access Control를 사용 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [Azure API Management 인스턴스에서 사용자 목록을 가져오는 방법](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할에 할당 된 사용자 목록을 가져오는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할 정의를 가져오는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Azure Security Center에서 id 및 액세스 권장 사항 이해](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당 하는 경우 기본 암호를 변경 합니다.

**지침**: Azure API Management에는 기본 암호/키 개념이 없습니다.

Api에 대 한 액세스를 보호 하는 한 가지 방법으로 Azure API Management 구독은 생성 된 구독 키 쌍으로 제공 됩니다. 고객은 언제 든 지 이러한 구독 키를 다시 생성할 수 있습니다.

* [Azure API Management 구독 이해](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용과 관련 하 여 표준 운영 절차를 만듭니다. Azure Security Center Id 및 액세스 관리를 사용 하 여 관리 계정 수를 모니터링 합니다.

또한 전용 관리 계정을 추적 하는 데 도움이 되도록 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 권장 사항을 사용할 수 있습니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

* [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법 (미리 보기)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy 사용 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에 Single Sign-On (SSO)를 사용 하십시오.

**지침**: azure AD에서 제공 하는 SSO 기능을 활용 하기 위해 개발자 포털에서 사용자를 인증 하기 위한 id 공급자로 Azure Active Directory를 활용 하도록 azure API Management를 구성할 수 있습니다. 구성 된 새 개발자 포털 사용자는 먼저 Azure AD를 통해 인증 한 다음 인증 된 후 포털에서 등록 프로세스를 완료 하 여 기본 등록 프로세스를 따르도록 선택할 수 있습니다.

* [Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한 부여](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

또는 위임을 통해 로그인/등록 프로세스를 추가로 사용자 지정할 수 있습니다. 위임을 사용 하면 개발자 포털의 기본 제공 기능을 사용 하는 것과는 반대로, 기존 웹 사이트를 사용 하 여 개발자 로그인/등록 및 제품 구독을 처리할 수 있습니다. 웹 사이트에서 사용자 데이터를 소유 하 고 사용자 지정 방식으로 이러한 단계의 유효성 검사를 수행할 수 있습니다.

* [사용자 등록 및 제품 구독을 위임하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: AZURE ACTIVE DIRECTORY (AD) MULTI-FACTOR AUTHENTICATION (MFA)를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 id 및 액세스를 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터 (권한 있는 액세스 워크스테이션)를 사용 합니다.

**지침**: MFA (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 하도록 구성 된 PAW (권한 Multi-Factor Authentication 있는 액세스 워크스테이션)를 사용 합니다.

* [권한 있는 액세스 워크스테이션에 대 한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 AD (Azure Active Directory) PRIVILEGED IDENTITY MANAGEMENT (PIM)를 사용 합니다.

또한 Azure AD 위험 감지를 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 볼 수 있습니다.

* [Privileged Identity Management 배포 하는 방법 (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Azure AD 위험 검색 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 Azure Portal에 대 한 액세스를 허용 합니다.

* [Azure에서 명명 된 위치를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: 가능한 경우 중앙 인증 및 권한 부여 시스템으로 Azure AD를 사용 합니다. AAD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 합니다. 또한 Azure AD는 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

Azure Active Directory를 사용 하 여 개발자 계정을 인증 하도록 Azure API Management 개발자 포털을 구성 합니다.

Azure Active Directory (AD)와 함께 OAuth 2.0 프로토콜을 사용 하 여 Api를 보호 하도록 Azure API Management 인스턴스를 구성 합니다.

* [Azure API Management에서 Azure Active Directory를 사용 하 여 개발자 계정에 권한을 부여 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Azure Active Directory 및 API Management에서 OAuth 2.0를 사용 하 여 API를 보호 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [AAD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스를 검토 하 고 조정 합니다.

**지침**: Azure Active Directory는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 고객은 Azure Id 액세스 검토를 활용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토 하 여 올바른 사용자만 적절 한 액세스 권한을 갖도록 할 수 있습니다.

고객은 API Management 사용자 계정에 대 한 인벤토리를 유지 관리 하 고 필요에 따라 액세스를 조정할 수 있습니다. API Management 개발자는 API Management와 함께 제공 되는 Api의 소비자입니다. 기본적으로 새로 만든 개발자 계정은 활성이며, 개발자 그룹과 연결됩니다. 활성 상태의 개발자 계정은 구독하는 모든 API에 액세스하는 데 사용할 수 있습니다.

관리자는 사용자 지정 그룹을 만들거나 연결 된 Azure Active Directory 테 넌 트에서 외부 그룹을 활용할 수 있습니다. 사용자 지정 및 외부 그룹은 시스템 그룹과 함께 사용되어 개발자에게 API 제품에 대한 표시 여부 및 액세스를 제공합니다.

* [Azure API Management에서 사용자 계정을 관리하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [API Management 사용자 목록을 가져오는 방법](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Azure API Management에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [Azure Id 액세스 검토를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화 되는 계정에 대 한 액세스 시도를 모니터링 합니다.

**지침**: azure API Management에서 Azure Active Directory을 id 공급자로 사용 하 여 개발자 계정을 인증 하도록 azure API Management 인스턴스를 구성 합니다.

Azure Active Directory (AD)와 함께 OAuth 2.0 프로토콜을 사용 하 여 Api를 보호 하도록 Azure API Management 인스턴스를 구성 합니다.

들어오는 API 요청에 대 한 JWT 유효성 검사 정책을 구성 하 여 유효한 토큰의 존재 및 유효성을 적용 합니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고를 구성 합니다. 또한 Log Analytics 작업 영역을 Azure 센티널 또는 타사 SIEM에 등록할 수 있습니다.

`log-to-eventhub` 정책을 사용 하 여 API Management로 고급 모니터링을 구성 하 고, 보안 분석에 필요한 추가 컨텍스트 정보를 캡처하고, Azure 센티널 또는 타사 siem으로 보냅니다.

* [Azure API Management에서 Azure Active Directory를 사용 하 여 개발자 계정에 권한을 부여 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Azure Active Directory 및 API Management에서 OAuth 2.0를 사용 하 여 API를 보호 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [API Management 액세스 제한 정책](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [Azure AD 로그를 Azure Monitor에 통합 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Azure 센티널을 온보드 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Api 고급 모니터링](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: 제어 평면 (Azure Portal)의 계정 로그인 동작 편차에 대해 AD (Azure Active Directory) id 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 Azure 센티널로 데이터를 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Id 보호 위험 정책을 구성 하 고 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 Microsoft에 관련 고객 데이터에 대 한 액세스 권한을 제공 합니다.

**지침**: 현재 사용할 수 없음 고객 Lockbox는 현재 Azure API Management에 대해 지원 되지 않습니다.

* [고객 Lockbox 지원 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요 한 정보 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스를 추적 하는 데 도움을 줍니다.

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요 한 정보를 저장 하거나 처리 하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현 합니다. Azure API Management 인스턴스는 VNet (가상 네트워크)/서브넷으로 구분 하 고 적절 하 게 태그를 지정 해야 합니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹를 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [가상 네트워크에서 Azure API Management를 사용하는 방법](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요 한 정보에 대 한 무단 전송을 모니터링 하 고 차단 합니다.

**지침**: 현재 사용할 수 없음 현재 Azure API Management에서 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다.

Microsoft는 Azure API Management에 대 한 기본 인프라를 관리 하 고 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요 한 정보를 암호화 합니다.

**지침**: 관리 평면 호출은 TLS를 통해 Azure Resource Manager를 통해 수행 됩니다. 유효한 JSON 웹 토큰(JWT)이 필요합니다. 데이터 평면 호출은 TLS를 사용 하 고 지원 되는 인증 메커니즘 (예: 클라이언트 인증서 또는 JWT) 중 하나를 사용 하 여 보안을 설정할 수 있습니다.

* [Azure API Management의 데이터 보호 이해](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [Azure API Management에서 TLS 설정 관리](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [Azure Active Directory를 사용 하 여 Azure API Management에서 Api 보호](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Azure Active Directory B2C를 사용 하 여 Azure API Management에서 Api 보호](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Azure Security Center 모니터링**: 예

**책임**: 공유

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용 하 여 중요 한 데이터 식별

**지침**: 현재 사용할 수 없음 현재 Azure API Management에서 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다. 중요 한 정보를 처리할 수 있는 Azure API Management 서비스에 태그를 적용 하 고 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 제어

**지침**: Azure API Management에 대 한 액세스를 제어 하기 위해 역할 기반 액세스 제어를 사용 합니다. Azure API Management는 Azure 역할 기반 액세스 제어(RBAC)를 사용하여 API Management 서비스 및 엔터티(예: API 및 정책)에 대한 세분화된 액세스 관리를 가능하게 합니다.

* [Azure API Management에서 역할 기반 Access Control를 사용 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용 하 여 액세스 제어를 적용 하십시오.

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft는 Azure API Management에 대 한 기본 인프라를 관리 하 고 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 휴지 상태의 중요 정보 암호화

**지침**: 인증서, 키 및 비밀 이라는 비밀 값과 같은 중요 한 데이터는 서비스 인스턴스 키 당 서비스 관리로 암호화 됩니다. 모든 암호화 키는 서비스 인스턴스당 서비스 관리 됩니다.

* [Azure API Management를 사용 하 여 미사용 데이터 보호/미사용 데이터 암호화 이해](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요 한 Azure 리소스에 대 한 변경 내용을 기록 하 고 경고 합니다.

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 프로덕션 Azure Functions 앱 및 기타 중요 한 리소스 및 관련 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure API Management에서 Azure Monitor 및 Azure 활동 로그를 사용 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화 된 취약점 검사 도구 실행

**지침**: 현재 사용할 수 없음 Azure Security Center의 취약성 평가는 현재 Azure API Management에서 사용할 수 없습니다.

Microsoft에서 검사 하 고 패치 하는 기본 플랫폼입니다. 서비스 구성 관련 취약성을 줄이기 위해 사용할 수 있는 보안 제어를 검토 합니다.

* [Azure API Management에서 사용할 수 있는 보안 컨트롤 이해](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화 된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화 된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백엔드 취약성 검색 비교

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용 하 여 검색 된 취약성에 대 한 수정의 우선 순위를 지정 합니다.

**지침**: 현재 사용할 수 없음 Azure Security Center의 취약성 평가는 현재 Azure API Management에서 사용할 수 없습니다.

Microsoft에서 검사 하 고 패치 하는 기본 플랫폼입니다. 고객은 서비스 구성 관련 취약성을 줄이기 위해 사용할 수 있는 보안 컨트롤을 검토할 수 있습니다.

* [Azure API Management에서 사용할 수 있는 보안 컨트롤 이해](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

* [Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타 데이터 유지 관리

**지침**: Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 하는 메타 데이터를 제공 합니다.

* [태그를 만들고 활용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한이 없는 Azure 리소스를 삭제 합니다.

**지침**: 적절 한 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정 하 고 권한이 없는 리소스가 적시에 구독에서 삭제 되도록 합니다.

또한 Azure policy를 사용 하 여 다음과 같은 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹를 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인 된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인 되지 않은 Azure 리소스에 대 한 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 계산 리소스 내에서 승인 되지 않은 소프트웨어 응용 프로그램 모니터링

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인 되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="68-use-only-approved-applications"></a>6.8: 승인 된 응용 프로그램만 사용 하십시오.

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인 된 Azure 서비스만 사용 합니다.

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용 하 여 특정 리소스 종류를 거부 하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인 된 응용 프로그램 목록 구현

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 Azure 리소스 관리자와 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 하 여 사용자가 Azure Resource Manager 상호 작용 하는 기능을 제한 하도록 Azure 조건부 액세스를 구성 합니다.

* [Azure Resource Manager에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Azure API Management의 역할 기반 액세스 제어](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 계산 리소스 내에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리

**지침**: 해당 사항 없음 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대 한 보안 구성을 설정 합니다.

**지침**: Azure Policy를 사용 하 여 Azure API Management 서비스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.apimanagement" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure API Management 서비스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성을 설정 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지

**지침**: Azure Policy를 사용 하 여 Azure API Management 서비스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.apimanagement" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure API Management 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. Azure 정책 [거부] 및 [없는 경우 배포]를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전 하 게 저장

**지침**: 사용자 지정 azure 정책 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 azure API Management 서비스 구성을 안전 하 게 저장 하 고 관리 합니다.

* [Azure DevOps에 파일을 저장 하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [Azure API Management DevOps Resource Kit 이해](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전 하 게 저장

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy를 사용 하 여 Azure API Management 서비스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.apimanagement" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure API Management 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. Azure 정책 [거부] 및 [없는 경우 배포]를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure API Management Devops Resource Kit를 사용 하 여 azure API Management에 대 한 구성 관리를 수행 합니다.

또한 Azure Policy를 사용 하 여 Azure API Management 서비스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.apimanagement" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure API Management 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. Azure 정책 [거부] 및 [없는 경우 배포]를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.

* [Azure API Management DevOps Resource Kit 이해](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대 한 자동화 된 구성 모니터링 구현

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 암호를 안전 하 게 관리

**지침**: Key Vault을 사용 하 여 인증서를 관리 하 고이를 autorotate로 설정 합니다. Azure Key Vault를 사용 하 여 사용자 지정 도메인 SSL 인증서를 관리 하는 경우 인증서가 비밀이 아니라 인증서로 Key Vault에 삽입 되어 있는지 확인 합니다.

* [Key Vault 키 회전에 대 한 지침을 사용 하 여 사용자 지정 도메인 이름을 설정 하는 방법](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Azure Security Center 모니터링**: 예

**책임**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: id를 안전 하 게 자동으로 관리

**지침**: AZURE ACTIVE DIRECTORY (AD)에서 생성 된 관리 서비스 ID를 사용 하 여 API Management 인스턴스가 Azure Key Vault 같은 다른 Azure AD 보호 리소스에 쉽고 안전 하 게 액세스할 수 있도록 합니다.

* [API Management 인스턴스에 대 한 관리 되는 id를 만드는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [관리 id를 사용 하 여 인증 하는 정책](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도 하지 않은 자격 증명 노출을 제거 합니다.

**지침**: 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색 된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동 하는 것을 권장 합니다.

* [자격 증명 스캐너를 설정 하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조 하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: 해당 사항 없음 이 권장 사항은 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 azure 서비스 (예: Azure API Management)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 계산 되지 않는 Azure 리소스에 업로드할 파일 사전 검사

**지침**: 해당 사항 없음 이 권장 사항은 데이터를 저장 하도록 디자인 된 비 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 azure 서비스 (예: Azure API Management)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 데이터를 저장 하도록 디자인 된 비 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 azure 서비스 (예: Azure API Management)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Azure API Management를 통해 api를 게시 하 고 관리 하 여 수동으로 설계, 구현 및 관리 하는 내결함성 및 인프라 기능을 활용 하 고 있습니다. API Management는 운영 오버 헤드를 추가 하지 않고 데이터 평면을 지역 장애에 impervious 하는 다중 지역 배포를 지원 합니다.

API Management의 서비스 백업 및 복원 기능은 재해 복구 전략을 구현 하는 데 필요한 구성 요소를 제공 합니다. 수동 또는 자동화 된 백업 및 복원 작업을 수행할 수 있습니다.

* [여러 지역에 API Management 데이터 평면을 배포 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [API Management 백업 작업을 호출 하는 방법](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

* [API Management 복원 작업을 호출 하는 방법](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure API Management에서 제공 하는 백업 및 복원 작업은 전체 시스템 백업 및 복원을 수행 합니다.

관리 id를 사용 하 여 API Management 사용자 지정 도메인 이름에 대 한 Azure Key Vault에서 인증서를 가져올 수 있습니다. Azure Key Vault 내에 저장 되는 인증서를 백업 합니다.

* [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Azure Key Vault 인증서를 백업 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함 한 모든 백업의 유효성을 검사 합니다.

**지침**: 백업에서 서비스 및 인증서의 테스트 복원을 수행 하 여 백업 유효성을 검사 합니다.

* [API Management 복원 작업을 호출 하는 방법](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

* [Azure Key Vault 인증서를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키의 보호 보장

**지침**: Azure API Management 고객 소유 Azure Storage 계정에 백업을 기록 합니다. Azure Storage 보안 권장 사항을 따라 백업 보호

* [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Blob 저장소에 대 한 보안 권장 사항](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호 합니다.

* [Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 응답](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 응답 가이드 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 작성 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 인시던트 처리/관리 단계를 정의 합니다.

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축 하는 방법에 대 한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 검색 또는 분석에 사용 되는 Security Center의 신뢰도와 경고를 발생 시킨 활동의 악의적인 의도를 가진 신뢰도 수준을 기준으로 합니다.

또한 구독을 명확 하 게 표시 합니다 (예: 프로덕션, 비 prod) 태그를 사용 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위해 명명 시스템을 만듭니다. 사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다.

* [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 필요에 따라 weak 점수와 격차 및 수정 계획을 식별 합니다.

* [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램에 대 한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트에 대 한 세부 정보를 제공 하 고 보안 인시던트에 대 한 경고 알림을 구성 하십시오.

**지침**: Microsoft 보안 대응 센터 (MSRC)가 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 microsoft에서 사용자에 게 연락 하는 데 사용 됩니다. 문제가 해결 되었는지 확인 하기 위해 팩트 이후에 인시던트를 검토 합니다.

* [Azure Security Center 보안 연락처를 설정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 응답 시스템에 통합 하세요.

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다.

* [연속 내보내기를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure 센티널로 경고를 스트리밍하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대 한 응답을 자동화 합니다.

**지침**: Azure Security Center의 워크플로 자동화 기능을 활용 하 여 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화를 구성 하 고 Logic Apps 하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 60 일 내에 모든 중요 한 보안 결과를 재구성 해야 합니다.

**지침**: * Microsoft [Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 하세요](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.) .

* [Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 Microsoft의 전략과 실행에 대 한 자세한 내용은 여기에서 찾을 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치 마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 를 참조 하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 에 대 한 자세한 정보
