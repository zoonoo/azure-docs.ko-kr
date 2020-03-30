---
title: Azure 보안 제어 - 네트워크 보안
description: 보안 제어 네트워크 보안
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251875"
---
# <a name="security-control-network-security"></a>보안 제어: 네트워크 보안

네트워크 보안 권장 사항은 Azure 서비스에 대한 액세스가 허용되거나 거부되는 네트워크 프로토콜, TCP/UDP 포트 및 네트워크 연결 서비스를 지정하는 데 중점을 둡니다.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Customer |

모든 가상 네트워크 서브넷 배포에 응용 프로그램의 신뢰할 수 있는 포트 및 원본과 관련된 네트워크 액세스 컨트롤이 적용되는 네트워크 보안 그룹이 있는지 확인합니다. 개인 링크가 활성화된 Azure 서비스를 사용하거나, Vnet 내부에 서비스를 배포하거나, 비공개 엔드포인트를 사용하여 개인적으로 연결합니다. 서비스 별 요구 사항은 해당 특정 서비스에 대한 보안 권장 사항을 참조하십시오.

또는 특정 사용 사례가 있는 경우 Azure 방화벽을 구현하여 요구 사항을 충족할 수 있습니다.

개인 링크에 대한 일반 정보:

https://docs.microsoft.com/azure/private-link/private-link-overview

가상 네트워크를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용하여 NSG를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure 방화벽을 배포하고 구성하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.2 | 9.3, 12.2 | Customer |

Azure 보안 센터를 사용하고 네트워크 보호 권장 사항을 따라 Azure에서 네트워크 리소스를 보호합니다. NSG 흐름 로그를 활성화하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다.

NSG 흐름 로그를 활성화하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure 보안 센터에서 제공하는 네트워크 보안 이해:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.3 | 9.5 | Customer |

들어오는 트래픽을 추가로 검사하기 위해 중요한 웹 응용 프로그램 앞에 Azure 웹 응용 프로그램 방화벽(WAF)을 배포합니다. WAF에 대한 진단 설정을 활성화하고 스토리지 계정, 이벤트 허브 또는 로그 분석 작업 영역에 로그를 수집합니다.

Azure WAF를 배포하는 방법:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.4 | 12.3 | Customer |

Azure 가상 네트워크에서 DDoS 표준 보호를 사용하여 DDoS 공격을 방지합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 IP 주소와의 통신을 거부합니다.

위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 경고 &quot;및 거부하도록&quot; 구성된 조직의 각 네트워크 경계에 Azure 방화벽을 배포합니다.

Azure 보안 센터 Just In Time 네트워크 액세스를 사용하여 제한된 기간 동안 승인된 IP 주소로 끝점 의 노출을 제한하도록 NSG를 구성합니다.

Azure 보안 센터 적응형 네트워크 강화를 사용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트 및 소스 IP를 제한하는 NSG 구성을 권장합니다.

DDoS 보호를 구성하는 방법:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 방화벽을 배포하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 보안 센터 통합 위협 인텔리전스 이해:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Azure 보안 센터 적응형 네트워크 강화 이해:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure 보안 센터(시간 네트워크 액세스 제어)를 이해합니다.

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.5 | 12.5, 15.8 | Customer |

NSG 흐름 로그를 저장소 계정에 기록하여 흐름 레코드를 생성합니다. 비정상적인 활동을 조사하는 데 필요한 경우 네트워크 감시자 패킷 캡처를 사용하도록 설정합니다.

NSG 흐름 로그를 활성화하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

네트워크 감시자 사용 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 경고 &quot;및 거부하도록&quot; 구성된 조직의 각 네트워크 경계에 Azure 방화벽을 배포합니다.

Azure 방화벽을 배포하는 방법:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽으로 경고를 구성하는 방법:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

신뢰할 수 있는 인증서에 대해 HTTPS/SSL을 사용하도록 설정된 웹 응용 프로그램에 Azure 응용 프로그램 게이트웨이를 배포합니다.

응용 프로그램 게이트웨이를 배포하는 방법:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

HTTPS를 사용하도록 응용 프로그램 게이트웨이를 구성하는 방법:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Azure 웹 응용 프로그램 게이트웨이를 통해 계층 7 부하 분산 이해:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.8 | 1.5 | Customer |

가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

서비스 태그 이해 및 사용:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.9 | 11.1 | Customer |

Azure 정책을 사용하면 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다.

또한 Azure Blueprint를 사용하여 단일 Blueprint 정의에서 Azure 리소스 관리자 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키징하여 대규모 Azure 배포를 단순화할 수도 있습니다. 새 구독에 Blueprint를 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

네트워킹을 위한 Azure 정책 샘플:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Azure Blueprint를 만드는 방법:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.1 | 11.2 | Customer |

NSG 및 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 &quot;경우&quot; 설명 필드를 사용하여 네트워크로 의 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 사항 및/또는 기간(등)을 지정합니다.

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

가상 네트워크를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용하여 NSG를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 1.11 | 11.3 | Customer |

Azure 정책을 사용하여 네트워크 리소스에 대한 구성의 유효성을 검사(및/또는 수정)합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

네트워킹을 위한 Azure 정책 샘플:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>다음 단계

- 다음 보안 제어 보기: [로깅 및 모니터링](security-control-logging-monitoring.md)
