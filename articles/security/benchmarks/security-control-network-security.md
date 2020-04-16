---
title: Azure 보안 제어 - 네트워크 보안
description: Azure 보안 제어 네트워크 보안
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408310"
---
# <a name="security-control-network-security"></a>보안 제어: 네트워크 보안

네트워크 보안 권장 사항은 Azure 서비스에 대한 액세스가 허용되거나 거부되는 네트워크 프로토콜, TCP/UDP 포트 및 네트워크 연결 서비스를 지정하는 데 중점을 둡니다.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Customer |

모든 가상 네트워크 서브넷 배포에 응용 프로그램의 신뢰할 수 있는 포트 및 원본과 관련된 네트워크 액세스 컨트롤이 적용되는 네트워크 보안 그룹이 있는지 확인합니다. 사용 가능한 경우 개인 링크가 있는 개인 끝점을 사용하여 VNet ID를 서비스로 확장하여 Azure 서비스 리소스를 가상 네트워크에 보호합니다. 개인 끝점 및 비공개 링크를 사용할 수 없는 경우 서비스 끝점을 사용합니다. 서비스 별 요구 사항은 해당 특정 서비스에 대한 보안 권장 사항을 참조하십시오. 

또는 특정 사용 사례가 있는 경우 Azure 방화벽을 구현하여 요구 사항을 충족할 수 있습니다.

- [가상 네트워크 서비스 엔드포인트 이해](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Azure 개인 링크 이해](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [가상 네트워크를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [보안 구성으로 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Azure 방화벽 배포 및 구성 방법](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Customer |

Azure 보안 센터를 사용하고 네트워크 보호 권장 사항을 따라 Azure에서 네트워크 리소스를 보호합니다. NSG 흐름 로그를 활성화하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다. 또한 NSG 흐름 로그를 로그 분석 작업 공간으로 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 통찰력을 제공할 수도 있습니다. 트래픽 분석의 몇 가지 장점은 네트워크 활동을 시각화하고 핫스팟을 식별하고, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하고, 네트워크 잘못된 구성을 정확히 찾아낼 수 있다는 점입니다.

- [NSG 흐름 로그를 활성화하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [트래픽 분석을 활성화하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Azure 보안 센터에서 제공하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.3 | 9.5 | Customer |

들어오는 트래픽을 추가로 검사하기 위해 중요한 웹 응용 프로그램 앞에 Azure 웹 응용 프로그램 방화벽(WAF)을 배포합니다. WAF에 대한 진단 설정을 활성화하고 스토리지 계정, 이벤트 허브 또는 로그 분석 작업 영역에 로그를 수집합니다.

- [Azure WAF 배포 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.4 | 12.3 | Customer |

Azure 가상 네트워크에서 DDoS 표준 보호를 사용하여 DDoS 공격을 방지합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 IP 주소와의 통신을 거부합니다.

위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성된 조직의 각 네트워크 경계에 Azure 방화벽을 배포합니다.

Azure 보안 센터 Just In Time 네트워크 액세스를 사용하여 제한된 기간 동안 승인된 IP 주소로 끝점 의 노출을 제한하도록 NSG를 구성합니다.

Azure 보안 센터 적응형 네트워크 강화를 사용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트 및 소스 IP를 제한하는 NSG 구성을 권장합니다.

- [DDoS 보호를 구성하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Azure 방화벽을 배포하는 방법](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Azure 보안 센터 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Azure 보안 센터 적응형 네트워크 강화 이해](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Azure 보안 센터(시간 네트워크 액세스 제어)에 대한 이해](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 레코드

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.5 | 12.5 | Customer |

네트워크 감시자 패킷 캡처를 사용하여 비정상적인 활동을 조사합니다.

- [네트워크 감시자 사용 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

페이로드 검사 기능을 갖춘 IDS/IPS 기능을 지원하는 Azure 마켓플레이스에서 오퍼를 선택합니다.  페이로드 검사를 기반으로 하는 침입 감지 및/또는 방지가 요구 사항이 아닌 경우 위협 인텔리전스가 있는 Azure 방화벽을 사용할 수 있습니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악의적인 IP 주소 및 도메인에 대한 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

조직의 각 네트워크 경계에 선택한 방화벽 솔루션을 배포하여 악의적인 트래픽을 탐지 및/또는 거부합니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure 방화벽을 배포하는 방법](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Azure 방화벽으로 경고를 구성하는 방법](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

신뢰할 수 있는 인증서에 대해 HTTPS/SSL을 사용하도록 설정된 웹 응용 프로그램에 Azure 응용 프로그램 게이트웨이를 배포합니다.

- [응용 프로그램 게이트웨이 배포 방법](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [HTTPS를 사용하도록 응용 프로그램 게이트웨이를 구성하는 방법](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Azure 웹 응용 프로그램 게이트웨이를 통한 계층 7 부하 분산 이해](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.8 | 1.5 | Customer |

가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

복잡한 보안 구성을 단순화하는 데 도움이 되는 응용 프로그램 보안 그룹을 사용할 수도 있습니다. 애플리케이션 보안 그룹을 사용하면 네트워크 보안을 애플리케이션 구조의 자연 확장으로 구성하여 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다.

- [서비스 태그 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [응용 프로그램 보안 그룹 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.9 | 11.1 | Customer |

Azure 정책을 사용하면 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.

또한 Azure Blueprint를 사용하여 단일 Blueprint 정의에서 Azure Resources Manager 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키징하여 대규모 Azure 배포를 단순화할 수도 있습니다. 새 구독에 Blueprint를 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

- [Azure 정책을 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [네트워킹을 위한 Azure 정책 샘플](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.10 | 11.2 | Customer |

NSG 및 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크로 의 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그 지정과 관련된 기본 제공 Azure 정책 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스가 태그로 만들어지도록 하고 기존 태그가 지정되지 않은 리소스를 알려줍니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [가상 네트워크를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [보안 구성을 사용하여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.11 | 11.3 | Customer |

Azure 활동 로그를 사용하여 리소스 구성을 모니터링하고 Azure 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 리소스에 대한 변경이 발생할 때 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 보고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Azure 모니터에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [로깅 및 모니터링](security-control-logging-monitoring.md) 보기
