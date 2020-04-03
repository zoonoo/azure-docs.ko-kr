---
title: Azure 에지 영역 소개 - 미리 보기
description: Microsoft의 에지 컴퓨팅 제품에 대해 알아봅니다.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 04/02/2020
ms.author: ganesr
ms.openlocfilehash: 2f2311adc231eac4260e26f0d5d1f2115f43d2eb
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618708"
---
# <a name="about-azure-edge-zones---preview"></a>Azure 에지 영역 소개 - 미리 보기

Azure Edge 영역은 사용자에게 가까운 데이터 처리를 가능하게 하는 Microsoft Azure의 제품 제품군입니다. VM, 컨테이너 및 기타 선택된 Azure 서비스를 Edge Zones에 배포하여 응용 프로그램의 낮은 대기 시간 및 높은 처리량 요구 사항을 해결할 수 있습니다.

Edge 영역의 일반적인 사용 사례 시나리오는 다음과 같습니다.

- 로봇 공학의 실시간 명령 및 제어
- 인공 지능 및 머신 러닝으로 실시간 분석 및 추론
- 머신 비전
- 혼합 현실 및 VDI 시나리오를 위한 원격 렌더링
- 몰입형 멀티 플레이어 게이밍
- 미디어 스트리밍 및 콘텐츠 전송
- 감시 및 보안

Azure Edge 영역은 다음과 같은 세 가지 개별 오퍼링으로 제공됩니다.

- Azure 에지 영역
- 캐리어가 있는 Azure 에지 영역
- Azure 프라이빗 에지 영역

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure 에지 영역

![모서리 영역](./media/edge-zones-overview/edge-zones.png "모서리 영역")

Azure Edge 영역은 Azure 지역에서 멀리 떨어진 채우기 센터에 배치된 Azure의 작은 설치 공간 확장입니다. Azure Edge Zones는 VM, 컨테이너 및 최종 사용자와 가까운 대기 시간에 민감한 처리량 집약적인 응용 프로그램을 실행할 수 있는 선택된 Azure 서비스 집합을 지원합니다. Azure Edge Zone은 Microsoft 글로벌 네트워크의 일부이며 사용자와 가까운 Edge Zone에서 실행되는 응용 프로그램과 Azure 리전 내에서 실행되는 Azure 서비스의 전체 집합 간에 안전하고 신뢰할 수 있으며 높은 대역폭의 연결을 제공합니다. Azure Edge 영역은 Microsoft에서 소유 및 운영하며 동일한 Azure 도구 및 포털 집합을 사용하여 에지 영역에 서비스를 관리하고 배포할 수 있습니다.

일반적인 사용 사례는 다음과 같습니다.

- 게임 및 게임 스트리밍
- 미디어 스트리밍 및 콘텐츠 전송
- 인공 지능 및 기계 학습을 이용한 실시간 분석 및 추론
- 혼합 현실렌더링

Azure Edge 영역은 다음 대도시에서 사용할 수 있습니다.

- 뉴욕, NY
- 로스앤젤레스, CA
- 마이애미, FL

자세한 [내용은 Edge Zones 팀에 연락하십시오.](https://aka.ms/EdgeZones)

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>캐리어가 있는 Azure 에지 영역

![캐리어가 있는 모서리 영역](./media/edge-zones-overview/edge-carrier.png "캐리어가 있는 모서리 영역")

이동통신사가 있는 Azure Edge 영역은 인구 센터의 이동 통신사 데이터 센터에 배치된 Azure의 작은 설치 공간 확장입니다. 캐리어 인프라를 갖춘 Azure Edge Zones는 이동통신사업자의 5G 네트워크에서 한 홉 떨어진 곳에 배치되어 모바일 장치에서 애플리케이션에 10밀리초 이하의 대기 시간을 제공합니다. 이동통신사가 있는 Azure Edge 영역은 이동 통신사의 데이터 센터에 배포되며 Microsoft 글로벌 네트워크에 연결됩니다. 사용자에게 가까이 실행되는 응용 프로그램과 Azure 리전에서 실행되는 전체 Azure 서비스 집합 간에 안전하고 안정적이며 높은 대역폭의 연결을 제공합니다. 개발자는 동일한 친숙한 도구 집합을 사용하여 Edge Zones에 서비스를 빌드하고 배포할 수 있습니다.

일반적인 사용 사례는 다음과 같습니다.

- 게임 및 게임 스트리밍
- 미디어 스트리밍 및 콘텐츠 전송
- 인공 지능 및 기계 학습을 이용한 실시간 분석 및 추론
- 혼합 현실렌더링
- 커넥티드 카
- 원격 의료

에지 존은 다음 사업자와 협력하여 제공됩니다.

- AT&T (애틀랜타, 댈러스, 로스앤젤레스)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure 프라이빗 에지 영역

![프라이빗 에지 존](./media/edge-zones-overview/private-edge.png "프라이빗 에지 존")

Azure 프라이빗 에지 영역은 온-프레미스에 배치된 Azure의 작은 설치 공간 확장입니다. Azure 프라이빗 에지 존은 [Azure 스택 에지](https://azure.microsoft.com/products/azure-stack/edge/) 플랫폼을 기반으로 하며 온-프레미스에 배포된 컴퓨팅 및 저장소 서비스에 대한 짧은 대기 시간 액세스를 가능하게 합니다. 또한 프라이빗 에지 존을 사용하면 모바일 패킷 코어, 라우터, 방화벽 및 SD-WAN 어플라이언스와 같은 가상화된 네트워크 기능(VNF)과 ISVS의 응용 프로그램을 [Azure 관리 응용 프로그램으로](https://azure.microsoft.com/services/managed-applications/) 나란히 배포할 수 있습니다. Azure 프라이빗 에지 존에는 가상화된 네트워크 기능(VNF) 및 Azure 포털의 응용 프로그램의 수명 주기를 관리할 수 있는 클라우드 네이티브 오케스트레이션 솔루션이 함께 제공됩니다.

Azure 프라이빗 에지 존을 사용하면 Azure에서 응용 프로그램을 빌드하고 배포하는 데 사용되는 것과 동일한 친숙한 도구를 사용하여 온-프레미스에서 응용 프로그램을 개발하고 배포할 수 있습니다. 또한 개인 모바일 네트워크(개인 LTE, 개인 5G), 방화벽과 같은 보안 기능을 실행하고 동일한 프라이빗 에지 존 어플라이언스에서 SD-WAN 어플라이언스를 사용하여 여러 지점 및 Azure에서 온-프레미스 네트워크를 확장하고 Azure에서 관리할 수 있습니다.

일반적인 사용 사례는 다음과 같습니다.

- 로봇 공학의 실시간 명령 및 제어
- 인공 지능 및 머신 러닝으로 실시간 분석 및 추론
- 머신 비전
- 혼합 현실 및 VDI 시나리오를 위한 원격 렌더링
- 감시 및 보안

우리는 VNF 벤더, ISV 및 MSP 파트너의 풍부한 에코시스템을 통해 프라이빗 에지 존을 사용하여 엔드 투 엔드 솔루션을 구현합니다. 자세한 [내용은 프라이빗 에지 존 팀에 연락하십시오.](https://aka.ms/EdgeZonesPartner)

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>프라이빗 에지 존 - 파트너

![프라이빗 에지 존 파트너](./media/edge-zones-overview/partners.png "프라이빗 에지 존 파트너")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>가상화된 네트워크 기능(VNF)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>모바일 네트워크를 위한 가상화된 진화 패킷 코어(vEPC)

- [긍정 네트워크](https://www.affirmednetworks.com/)
- [드루이드 소프트웨어](https://www.druidsoftware.com/)
- [익스피토 ()에페토 ()에](https://www.expeto.io/)
- [메이니르 (주)](https://mavenir.com/)
- [메타 스위치](https://www.metaswitch.com/)
- [노키아 디지털 오토메이션 클라우드](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>모바일 라디오 파트너

- [컴스코프 루커스](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 벤더

- [넷파운드리](https://netfoundry.io/)
- [노키아의 누아네트웍스](https://www.nuagenetworks.net/)
- [벨로 클라우드에 의해 VMware SD-WAN](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>라우터 공급업체

- [Arista](https://www.arista.com/)

파트너가 되는 방법에 대한 자세한 내용은 [프라이빗 에지 존 팀에 연락하십시오.](https://aka.ms/EdgeZonesPartner)

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>방화벽 공급업체

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>관리형 솔루션 제공업체 - 이동 통신사업자 및 글로벌 시스템 통합업체

| 글로벌 SI 및 운영자 | 이동 통신사 |
| --- | --- |
| 암독스 (주)                       | 에티살라트 주             |
| 아메리칸 타워               | NTT 통신   |
| 센추리 링크                 | Proximus             |
| 익스피토 ()에페토 ()에                       | Rogers               |
| 페더레이션 무선           | SK텔레콤           |
| Infosys                      | Telefonica           |
| 테크 마히드라                | Telstra              |
|        *                     | Vodafone             |

파트너가 되는 방법에 대한 자세한 내용은 [프라이빗 에지 존 팀에 연락하십시오.](https://aka.ms/EdgeZonesPartner)

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>프라이빗 에지 존 - 솔루션

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>프라이빗 에지 존의 프라이빗 모바일 네트워크

![프라이빗 에지 존의 프라이빗 모바일 네트워크](./media/edge-zones-overview/mobile-networks.png "프라이빗 에지 존의 프라이빗 모바일 네트워크")

이제 프라이빗 에지 영역에 개인 모바일 네트워크를 배포할 수 있습니다. 프라이빗 모바일 네트워크는 업무상 중요한 애플리케이션에 필요한 초저지연, 대용량 및 안정적이고 안전한 무선 네트워크를 지원합니다. 프라이빗 모바일 네트워크는 창고에서 자동 유도 차량(AGV)의 명령 및 제어, 스마트 팩토리의 로봇과 증강 현실, 가상 현실 에지 애플리케이션 과 같은 시나리오를 가능하게 할 수 있습니다.

가상화된 진화된 패킷 코어(vEPC) 네트워크 기능은 개인 모바일 네트워크의 두뇌를 형성합니다. 이제 프라이빗 에지 영역에 vEPC를 배포할 수 있습니다. 프라이빗 에지 영역에서 사용할 수 있는 vEPC 파트너 목록은 [vEPC ISV를](#vEPC)참조하십시오.

프라이빗 에지 존에 개인 모바일 네트워크 솔루션을 배포하려면 모바일 액세스 포인트, SIM 카드 및 라우터와 같은 기타 VNF와 같은 다른 구성 요소가 필요합니다. 개인 모바일 네트워크를 설정하려면 라이선스 또는 라이선스가 부여되지 않은 스펙트럼에 대한 액세스가 매우 중요합니다. 또한 RF 계획, 물리적 레이아웃, 설치 및 지원에 대한 도움이 필요할 수 있습니다. 파트너 목록은 [모바일 라디오 파트너를](#mobile-radio)참조하십시오.

Microsoft는 네트워크 계획, 필요한 장치 구입, 하드웨어 설정, Azure에서 구성 관리에 이르기까지 이 프로세스의 모든 측면에 도움이 되는 파트너 에코시스템을 제공합니다. Microsoft와 긴밀하게 통합된 검증된 파트너 집합을 사용하면 솔루션이 안정적이고 사용하기 쉽다는 것을 확신할 수 있습니다. Microsoft와 그 파트너에 의존하여 나머지 를 지원하면서 핵심 시나리오에 집중할 수 있습니다.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>프라이빗 엣지 존의 SD-WAN

![프라이빗 엣지 존의 SD-WAN](./media/edge-zones-overview/sd-wan.png "프라이빗 엣지 존의 SD-WAN")
 
SD-WAN을 기술로 사용하면 대역폭 증가, 클라우드에 대한 고성능 액세스, 서비스 삽입, 안정성, 정책 관리 및 광범위한 네트워크 가시성을 갖춘 엔터프라이즈급 WAN(광역 네트워크)을 만들 수 있습니다. SD-WAN은 소유 비용을 낮추어 중복 중앙 컨트롤러에서 오케스트레이션된 원활한 지사 연결을 제공합니다.
프라이빗 에지 존의 SD-WAN을 사용하면 Capex 중심 모델에서 서비스형 소프트웨어(SaaS) 모델로 이동하여 IT 예산을 줄일 수 있습니다. 원하는 SD-WAN 파트너 오케스트레이터 또는 컨트롤러를 사용하여 새 서비스를 활성화하고 전체 네트워크에 즉시 전파할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 팀에 연락하십시오.

* [에지 존 팀](https://aka.ms/EdgeZones)
* [프라이빗 에지 존 팀 - 파트너가 되기 위해](https://aka.ms/EdgeZonesPartner)
