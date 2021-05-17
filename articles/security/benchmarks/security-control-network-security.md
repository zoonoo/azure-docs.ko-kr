---
title: Azure 보안 제어 - 네트워크 보안
description: Azure 보안 제어 네트워크 보안
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cc52d1f6eec5f1aedb0db37b3945f7be6d9f62a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595637"
---
# <a name="security-control-network-security"></a>Azure 보안 제어: 네트워크 보안

네트워크 보안 권장 사항은 Azure 서비스에 대한 액세스가 허용되거나 거부되는 네트워크 프로토콜, TCP/UDP 포트 및 네트워크 연결 서비스를 지정하는 데 중점을 둡니다.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Customer |

모든 가상 네트워크 서브넷 배포에 애플리케이션의 신뢰할 수 있는 포트 및 원본에 특정한 네트워크 액세스 제어와 함께 네트워크 보안 그룹이 적용되었는지 확인합니다. 사용할 수 있는 경우 프라이빗 엔드포인트를 프라이빗 링크와 함께 사용하여 VNet ID를 서비스로 확장하면 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다. 프라이빗 엔드포인트와 프라이빗 링크를 사용할 수 없는 경우 서비스 엔드포인트를 사용합니다. 서비스별 요구 사항에 대해서는 해당 특정 서비스에 대한 보안 권장 사항을 참조하세요. 

또는 특정 사용 사례가 있는 경우 Azure Firewall을 구현하여 이 요구 사항을 충족할 수 있습니다.

- [가상 네트워크 서비스 엔드포인트 이해](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Azure Private Link 이해](../../private-link/private-link-overview.md)

- [Virtual Network를 만드는 방법](../../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall을 배포하고 구성하는 방법](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 NIC의 구성 및 트래픽 모니터링 및 기록

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Customer |

Azure Security Center를 사용하고 네트워크 보호 권장 사항에 따라 Azure에서 네트워크 리소스를 보호합니다. NSG 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점으로 네트워크 활동의 시각화 핫 스폿 식별, 보안 위협 식별, 트래픽 흐름 패턴 이해, 네트워크 구성 오류 파악 등의 기능이 있습니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.3 | 9.5 | Customer |

들어오는 트래픽의 추가 검사를 위해 중요한 웹 애플리케이션 앞에 Azure WAF(Web Application Firewall)를 배포합니다. WAF에 진단 설정을 사용하도록 설정하고 스토리지 계정, Event Hub 또는 Log Analytics 작업 영역에서 로그를 수집합니다.

- [Azure WAF를 배포하는 방법](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.4 | 12.3 | Customer |

DDoS 공격으로부터 보호하기 위해 Azure Virtual Networks에서 DDoS 표준 보호를 사용하도록 설정합니다. Azure Security Center에 통합된 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소와의 통신을 거부합니다.

위협 인텔리전스를 사용하도록 설정하고 악성 네트워크 트래픽에 대해 ‘경고 및 거부’하도록 구성하여 조직의 각 네트워크 경계에 Azure Firewall을 배포합니다.

Azure Security Center Just-in-time 네트워크 액세스를 사용하여 제한된 기간 동안 엔드포인트의 노출을 승인된 IP 주소로 제한하는 NSG를 구성할 수 있습니다.

Azure Security Center 적응형 네트워크 강화를 사용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트와 원본 IP를 제한하는 NSG 구성을 권장합니다.

- [DDoS Protection을 구성하는 방법](../../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall을 배포하는 방법](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../../security-center/azure-defender.md)

- [Azure Security Center 적응형 네트워크 강화 이해](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center Just-in-time 네트워크 액세스 제어 이해](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.5 | 12.5 | Customer |

Network Watcher 패킷 캡처를 사용하도록 설정하여 비정상적인 활동을 조사합니다.

- [Network Watcher를 사용하도록 설정하는 방법](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

페이로드 검사 기능으로 IDS/IPS 기능을 지원하는 제품을 Azure Marketplace에서 선택합니다.  페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

악의적인 트래픽을 감지하거나 거부하기 위해 각 조직의 네트워크 경계에 원하는 방화벽 솔루션을 배포합니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall을 배포하는 방법](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall을 사용하여 경고를 구성하는 방법](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

신뢰할 수 있는 인증서를 위해 HTTPS/TLS를 사용하도록 설정된 웹 애플리케이션용 Azure Application Gateway를 배포합니다.

- [Application Gateway 배포 방법](../../application-gateway/quick-create-portal.md)

- [HTTPS를 사용하도록 Application Gateway를 구성하는 방법](../../application-gateway/create-ssl-portal.md)

- [Azure Application Gateway를 사용한 계층 7 부하 분산 이해](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.8 | 1.5 | Customer |

가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

애플리케이션 보안 그룹을 사용하여 복잡한 보안 구성을 간소화할 수도 있습니다. 애플리케이션 보안 그룹을 사용하면 네트워크 보안을 애플리케이션 구조의 자연 확장으로 구성하여 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다.

- [서비스 태그 이해 및 사용](../../virtual-network/service-tags-overview.md)

- [애플리케이션 보안 그룹 이해 및 사용](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.9 | 11.1 | Customer |

Azure Policy를 사용하여 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.

또한 Azure Blueprints에서 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, Azure RBAC 제어 및 정책)를 단일 블루프린트 정의로 패키지하여 대규모 Azure 배포를 간소화할 수 있습니다. 블루프린트를 새로운 구독에 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](../../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint를 만드는 방법](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.10 | 11.2 | Customer |

NSG 및 네트워크 보안/트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: ‘태그 및 해당 값 필요’)를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

- [Virtual Network를 만드는 방법](../../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 1.11 | 11.3 | Customer |

Azure 활동 로그를 사용하여 리소스 구성을 모니터하고 Azure 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../../azure-monitor/alerts/alerts-activity-log.md)

## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [로깅 및 모니터링](security-control-logging-monitoring.md)을 참조하세요.