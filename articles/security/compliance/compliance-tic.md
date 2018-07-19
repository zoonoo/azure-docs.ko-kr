---
title: Azure에 대한 TIC(Trusted Internet Connections) 지침
description: Azure 및 SaaS 서비스에 대한 TIC(Trusted Internet Connections) 지침
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: cf24810c0aa414e751e55df163563f013c1a0081
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969948"
---
# <a name="trusted-internet-connection-guidance"></a>TIC(Trusted Internet Connections) 지침

## <a name="background"></a>백그라운드

TIC(Trusted Internet Connections) 이니셔티브의 목적은 현재 연방 기관에서 사용 중인 개별 외부 네트워크 연결의 보안을 최적화하고 표준화하는 것입니다. 정책은 OMB(Office of Management and Budget) [각서 M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf)에 요약되어 있습니다.

2007년 11월에 OMB는 연방 네트워크 경계 보안 및 인시던트 응답 기능을 개선하기 위해 TIC 프로그램을 수립했습니다. TIC는 특정 서명 및 패턴 기반 데이터를 식별하고 봇네트 활동과 같은 행동 이상을 발견하기 위해 모든 인바운드 및 아웃바운드 트래픽의 네트워크 분석을 수행하도록 설계되었습니다. 기관은 외부 네트워크 연결을 통합하고, 제한된 개수의 네트워크 끝점(Trusted Internet Connections라고 함)에서 호스트된 침입 검색 및 방지 장치(EINSTEIN이라고 함)를 통해 모든 트래픽을 라우트하도록 요구되었습니다.

간단히 말해, TIC의 목적은 기관에 다음을 알리기 위한 것입니다.
- 내 네트워크에 있는 사람(권한 있음 또는 권한 없음)
- 내 네트워크에 액세스하는 시간 및 이유
- 액세스하는 리소스

이제 모든 기관 외부 연결은 OMB 승인 TIC를 통해 라우트되어야 합니다. 연방 기관은 TICAP(TIC 액세스 공급자)로 또는 MTIPS(Managed Trusted Internet Protocol Service)라는 주요 계층 1 인터넷 서비스 공급자 중 하나와 서비스 계약을 체결하여 TIC 프로그램에 참여해야 합니다.  TIC에는 기관 및 MTIPS 공급자가 현재 수행하는 필수 중요 기능이 포함되어 있습니다. 현재 버전의 TIC에서는 EINSTEIN 버전 2 침입 검색 및 EINSTEIN 버전 3 가속(3A) 침입 방지 장치가 각 TICAP 및 MTIPS에 배포되고, 기관이 DHS(Department of Homeland Security)와 양해 각서를 체결하여 연방 시스템에 EINSTEIN 기능을 배포합니다.

.gov 네트워크를 보호하는 책임의 일환으로, DHS는 기관 Netflow 데이터의 원시 데이터 피드를 통해 전체 연방 엔터프라이즈에서 인시던트의 상관 관계를 지정하고 특수 도구를 사용하여 분석을 수행하도록 요구합니다. DHS 라우터는 인터페이스를 들어오거나 나갈 때 IP 네트워크 트래픽을 수집하는 기능을 제공합니다. 네트워크 관리자는 Netflow 데이터를 분석하여 트래픽의 소스 및 대상, 서비스 클래스 등의 사항을 파악할 수 있습니다. Netflow 데이터는 “비콘텐츠 데이터”(예: 헤더, 소스 IP, 대상 IP 등)로 간주되며 DHS에서 콘텐츠와 관련된 정보, 즉 누가 어떤 작업을 얼마 동안 수행했는지를 알 수 있도록 합니다.

또한 이니셔티브에는 온-프레미스 인프라를 가정하는 보안 정책, 지침 및 프레임워크가 포함되어 있습니다. 정부 기관이 비용 절감, 운영 효율성 및 혁신을 위해 클라우드로 이동할 때 TIC의 구현 요구 사항으로 인해 네트워크 트래픽 속도가 느려지고 정부 사용자가 클라우드 기반 데이터에 액세스할 수 있는 속도와 민첩성이 제한되는 경우도 있습니다.

이 문서에서는 정부 기관이 Microsoft Azure Government를 사용하여 IaaS 및 PaaS 서비스 둘 다에서 TIC 정책을 준수하도록 지원할 수 있는 방법을 설명합니다.

## <a name="summary-of-azure-networking-options"></a>Azure 네트워킹 옵션 요약

Azure 서비스에 연결할 때 다음 세 가지 기본 옵션이 있습니다.

1. 직접 인터넷 연결: 개방형 인터넷 연결을 통해 Azure 서비스에 직접 연결합니다. 매체 및 연결은 공용입니다. 응용 프로그램 및 전송 수준 암호화를 통해 개인 정보를 보호합니다. 대역폭은 사이트의 인터넷 연결에 의해 제한되며, 여러 활성 공급자를 사용하여 복원력을 유지할 수 있습니다.
1. 가상 사설망: VPN Gateway를 사용하여 Azure Virtual Network에 비공개로 연결합니다.
매체는 사이트의 표준 인터넷 연결을 트래버스하지만 연결이 터널에서 암호화되어 개인 정보를 보호하기 때문에 공용입니다. 대역폭은 선택한 구성 및 VPN 장치에 따라 제한됩니다. Azure 지점 및 사이트 간 연결은 일반적으로 100Mbps로 제한되고, 사이트 간 연결은 1.25Gbps로 제한됩니다.
1. Microsoft ExpressRoute: ExpressRoute는 Microsoft 서비스에 대한 직접 연결입니다. 연결이 격리된 파이버 채널을 통해 이루어지므로 사용하는 구성에 따라 연결이 공개 또는 비공개일 수 있습니다. 대역폭은 일반적으로 최대 10Gbps로 제한됩니다.

Department of Homeland Security의 “TIC(Trusted Internet Connections) 참조 아키텍처 문서 버전 2.0”에 있는 TIC(Trusted Internet Connections) 부록 H(클라우드 고려 사항) 요구 사항을 충족하는 몇 가지 방법이 있습니다. 이 문서 전체에서 DHS TIC 지침을 TIC 2.0이라고 합니다.

D/A TIC를 통해 트래픽을 라우트하지 않고 D/A(부처 또는 기관)에서 Azure 또는 Office 365로 연결할 수 있으려면 D/A에서 CSP(클라우드 서비스 공급자)에 대한 전용 연결 및/또는 암호화된 터널을 사용해야 합니다. CSP 서비스는 D/A 클라우드 자산에 대한 연결이 정부 담당자의 직접 액세스를 위해 공용 인터넷에 제공되지 않도록 유지할 수 있습니다.

O365는 [Microsoft 피어링](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)이 설정된 ExpressRoute 또는 TLS 1.2를 통해 모든 트래픽을 암호화하는 인터넷 연결을 사용하여 TIC 2.0 부록 H를 준수합니다.  D/A 네트워크의 D/A 최종 사용자는 인터넷을 통해 기관 네트워크 및 TIC 인프라에 연결할 수 있습니다. O365에 대한 모든 원격 인터넷 액세스는 차단되며 기관을 통해 라우트됩니다. D/A는 일종의 공용 피어링인 Microsoft 피어링이 설정된 ExpressRoute를 통해 O365에 연결할 수도 있습니다.  

Azure의 경우에만, 인터넷 액세스를 제한하는 서비스와 함께 사용될 때 옵션 2(VPN) 및 3(ExpressRoute)이 이러한 요구 사항을 충족할 수 있습니다.

![Microsoft TIC(Trusted Internet Connection) 다이어그램](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure 서비스 제공 인프라가 TIC 준수에 도움이 되는 방법

IaaS를 사용하여 TIC 정책을 준수하는 것은 Azure 고객이 고유한 가상 네트워크 라우팅을 관리하기 때문에 비교적 간단합니다.

TIC 참조 아키텍처 준수에 도움이 되는 중요 요구 사항은 VNet(가상 네트워크)가 부처 또는 기관 네트워크의 비공개 확장이 되어야 한다는 것입니다. _비공개_ 확장 프로그램이 되려면 정책에 따라 온-프레미스 TIC 네트워크 연결을 통하지 않고 네트워크를 나가는 트래픽이 없어야 합니다. 이 프로세스를 “강제 터널링”이라고 하며, TIC 준수에 사용되는 경우, CSP 환경에 있는 시스템의 모든 트래픽이 조직 네트워크의 온-프레미스 게이트웨이를 통과하여 TIC를 통해 인터넷으로 이동하도록 라우트하는 프로세스입니다.

Azure IaaS TIC 준수는 다음 두 가지 주요 단계로 나눌 수 있습니다.

1. 구성
1. 감사

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC 준수 구성

Azure를 사용하여 TIC 규격 아키텍처를 구성하려면 먼저 가상 네트워크에 대한 직접 인터넷 연결을 방지한 다음, 강제로 인터넷 트래픽이 온-프레미스 네트워크를 통과하도록 해야 합니다.

#### <a name="prevent-direct-internet-access"></a>직접 인터넷 액세스 방지

Azure IaaS 네트워킹은 Virtual Machines의 NIC(네트워크 인터페이스 컨트롤러)가 연결된 서브넷으로 구성된 가상 네트워크를 통해 수행됩니다.

TIC 준수를 지원하는 가장 간단한 시나리오는 가상 머신 또는 그 컬렉션이 외부 리소스에 연결할 수 없도록 하는 것입니다. 가상 네트워크에서 하나 이상의 NIC 또는 서브넷에 대한 트래픽을 제어하는 데 사용할 수 있는 NSG(네트워크 보안 그룹)를 통해 외부 네트워크에서 연결을 끊을 수 있습니다. NSG에는 트래픽 방향, 프로토콜, 원본 주소 및 포트, 대상 주소 및 포트에 따라 트래픽을 허용하거나 거부하는 액세스 제어 규칙이 포함되어 있습니다. NSG의 규칙은 언제든지 변경할 수 있으며, 변경 내용은 연결된 모든 인스턴스에 적용됩니다.  NSG를 만드는 방법에 대한 자세한 내용은 [NSG 만들기](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal) 문서를 참조하세요.

#### <a name="force-internet-traffic-through-on-premises-network"></a>강제로 인터넷 트래픽이 온-프레미스 네트워크를 통과하도록 구현

Azure는 시스템 경로를 자동으로 만들고 가상 네트워크의 각 서브넷에 경로를 할당합니다. 시스템 경로를 만들거나 시스템 경로를 제거할 수는 없지만, 사용자 지정 경로를 사용하여 일부 시스템 경로를 재정의할 수 있습니다. Azure는 각 서브넷에 대한 기본 시스템 경로를 만들고, 특정 Azure 기능을 사용하는 경우, 특정 서브넷 또는 모든 서브넷에 대한 선택적 기본 경로를 추가합니다. 이 라우팅은 가상 네트워크 내의 대상에 전송된 트래픽이 가상 네트워크 내에 있고, IANA 지정 개인 주소 공간(예: 10.0.0.0/8)이 삭제되고(가상 네트워크의 주소 공간에 포함된 경우 제외), 최후의 수단으로 0.0.0.0/0이 가상 네트워크의 인터넷 끝점에 라우트되도록 합니다.

![TIC 강제 터널링](media/tic-diagram-c.png)

모든 트래픽이 D/A TIC를 트래버스하도록 하려면 가상 네트워크를 나가는 모든 트래픽이 온-프레미스 연결을 통해 라우트되어야 합니다.  사용자 정의 경로를 생성하거나 온-프레미스 네트워크 게이트웨이와 Azure 가상 네트워크 게이트웨이 간에 BGP(Border Gateway Protocol) 경로를 교환하여 사용자 지정 경로를 만듭니다. 사용자 정의 경로에 대한 자세한 내용은 https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined에서 확인할 수 있습니다. Border Gateway Protocol에 대한 자세한 내용은 https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol에서 확인할 수도 있습니다.

#### <a name="user-defined-routes"></a>사용자 정의 경로

경로 기반 가상 네트워크 게이트웨이를 사용하는 경우, 0.0.0.0/0 트래픽이 가상 네트워크 게이트웨이의 “다음 홉”으로 라우트되도록 하는 UDR(사용자 정의 경로) 설정을 추가하여 Azure 내에서 강제 터널링을 수행할 수 있습니다. Azure는 시스템 정의 경로보다 사용자 정의 경로에 우선 순위를 지정하므로, 모든 비 VNet 트래픽이 가상 네트워크 게이트웨이로 전송된 다음, 온-프레미스로 라우트됩니다. 이 사용자 정의 경로는 정의된 후 Azure 환경의 모든 가상 네트워크에서 새로 생성되었거나 기존에 있던 모든 서브넷과 연결되어야 합니다.

![사용자 정의 경로 및 TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Border Gateway Protocol

ExpressRoute 또는 BGP(Border Gateway Protocol) 사용이 가능한 가상 네트워크 게이트웨이를 사용하는 경우, BGP가 경로 알림 방법으로 권장됩니다. BGP에서 경로 0.0.0.0/0을 알리면, ExpressRoute 및 BGP 인식 가상 네트워크 게이트웨이는 이 기본 경로가 가상 네트워크 내의 모든 서브넷에 적용되도록 합니다.

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC 준수 감사

Azure는 TIC 준수를 감사하는 여러 가지 방법을 제공합니다.

#### <a name="effective-routes"></a>유효한 경로

기본 경로가 전파되었는지 확인하려면 특정 VM, 특정 NIC 또는 사용자 정의 경로 테이블의 “유효한 경로”를 살펴봅니다. 이 작업은 https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal에 설명된 대로 Azure Portal을 통해 또는 https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell에 설명된 PowerShell을 통해 수행할 수 있습니다. 유효한 경로 블레이드를 사용하면 아래와 같이 관련 엔터티에 적용되는 관련 사용자 정의 경로, BGP 알림 경로 및 시스템 경로를 확인할 수 있습니다.

![경로 스크린샷](media/tic-screen-1.png)

**참고**: 실행 중인 VM과 연결되어 있지 않으면 NIC에 대한 유효한 경로를 볼 수 없습니다.

#### <a name="network-watcher"></a>Network Watcher

Azure Network Watcher는 TIC 준수를 감사하는 데 사용할 수 있는 여러 도구를 제공합니다.  https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview에서 Network Watcher에 대해 자세히 알아보세요.

##### <a name="network-security-groups-flow-logs"></a>네트워크 보안 그룹 흐름 로그 

Azure Network Watcher는 IP 트래픽과 관련된 메타데이터를 나타내는 네트워크 흐름 로그를 캡처하는 기능을 제공합니다. 네트워크 흐름 로그에는 다른 데이터 외에도 대상의 소스 및 대상 주소가 포함되어 있습니다. 이 로그와 가상 네트워크 게이트웨이, 온-프레미스 에지 장치 및/또는 TIC의 로그가 결합되어 모든 트래픽이 실제로 온-프레미스로 라우트되는지 모니터링할 수 있습니다. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure Platform as a Service 제품이 TIC 준수에 도움이 되는 방법

Azure Storage 등의 Azure PaaS 서비스는 인터넷 연결 가능 URL을 통해 액세스할 수 있습니다. 승인된 자격 증명만 있으면 누구든지 TIC을 트래버스하지 않고 임의 위치에서 저장소 계정 등의 리소스에 액세스할 수 있습니다. 이러한 이유로, 많은 정부 고객은 Azure PaaS 서비스가 TIC 정책을 준수하지 않는다는 잘못된 결론을 내립니다. 실제로 Azure PaaS 서비스는 가상 네트워크(VNet)에 연결할 수 있는 경우, 위에서 설명한 TIC 규격 IaaS 환경과 동일한 아키텍처를 사용하여 TIC 정책을 준수할 수 있습니다. Azure PaaS 서비스와 Azure VNet를 통합하면 해당 VNet에서 서비스를 비공개로 액세스할 수 있으며, 사용자 정의 경로 또는 BGP를 통해 0.0.0.0/0에 대한 사용자 지정 라우팅을 적용하여 모든 인터넷 바인딩된 트래픽이 온-프레미스로 라우트되어 TIC를 트래버스하도록 할 수 있습니다.  일부 Azure 서비스는 다음 패턴을 사용하여 Vnet에 통합할 수 있습니다.

- **서비스 전용 인스턴스 배포**: VNet에 연결된 끝점이 있는 전용 인스턴스로 배포할 수 있는 PaaS 서비스 수가 점점 증가하고 있습니다. 예를 들어, ASE(App Service Environment)를 “격리” 모드로 배포하여 해당 네트워크 끝점을 VNet로 제한할 수 있습니다. 그런 다음, 이 ASE에서 Web Apps, APIs, Functions 등 많은 Azure PaaS 서비스를 호스트할 수 있습니다.
- **VNet 서비스 끝점**: 끝점을 공용 주소 대신 VNet 전용 IP로 이동하는 옵션을 허용하는 PaaS 서비스 수가 점점 증가하고 있습니다.

2018년 5월 기준, VNet 또는 서비스 끝점에 전용 인스턴스 배포를 지원하는 서비스는 아래와 같습니다. *(가용성은 Azure 상용을 나타냄, Azure Government 가용성은 보류 중임)

### <a name="service-endpoints"></a>서비스 엔드포인트

|서비스                   |상태            |
|--------------------------|------------------|
|Azure KeyVault            | 비공개 미리 보기  |
|Cosmos DB                 | 비공개 미리 보기  |
|ID                  | 비공개 미리 보기  |
|Azure Data Lake           | 비공개 미리 보기  |
|Sql Postgress/Mysql       | 비공개 미리 보기  |
|Azure SQL Data Warehouse  | 공개 미리 보기   |
|Azure SQL                 | GA               |
|Storage                   | GA               |

### <a name="vnet-injection"></a>VNet 삽입

|서비스                            |상태            |
|-----------------------------------|------------------|
|SQL 관리되는 인스턴스               | 공개 미리 보기   |
|Azure Container Service(AKS)       | 공개 미리 보기   |
|Service Fabric                     | GA               |
|API Management                     | GA               |
|Azure Active Directory             | GA               |
|Azure Batch                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|계산 가상 머신 확장 집합  | GA               |
|계산 클라우드 서비스              | GA               |

### <a name="vnet-integration-details"></a>VNet 통합 세부 정보

아래 다이어그램은 VNet 삽입 및 VNet 서비스 터널링 둘 다를 사용하여 PaaS 서비스에 액세스하기 위한 일반적인 네트워크 흐름을 보여 줍니다.  네트워크 서비스 게이트웨이, VNet 및 서비스 태그에 대한 추가 정보는 https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags에서 확인할 수 있습니다.

![TIC에 대한 PaaS 연결 옵션](media/tic-diagram-e.png)

1. ExpressRoute를 사용하는 Azure에 대한 비공개 연결 강제 터널링을 포함하는 ExpressRoute 개인 피어링은 ExpressRoute를 통한 모든 고객 VNet 트래픽을 강제로 온-프레미스로 되돌리는 데 사용됩니다. Microsoft 피어링은 필요하지 않습니다.
2. ExpressRoute Microsoft 피어링과 함께 Azure VPN Gateway를 사용하면 고객 VNet와 온-프레미스 에지 간의 종단 간 IPSec 암호화를 오버레이할 수 있습니다. 
3. 고객 VNet에 대한 네트워크 연결은 NSG(네트워크 보안 그룹)를 사용하여 제어되므로 고객이 IP, 포트 및 프로토콜을 기준으로 허용/거부할 수 있습니다.
4. 고객 서비스에 대한 서비스 끝점을 만들면 고객 VNet가 PaaS 서비스로 확장됩니다.
5. PaaS 서비스 끝점은 모든 액세스를 기본 거부하고 고객 VNet에서 지정된 서브넷의 액세스만 허용하도록 보안이 설정됩니다.  기본 거부에는 인터넷에서 시작된 연결도 포함됩니다.
6. 고객 VNet 내의 리소스에 액세스해야 하는 다른 모든 Azure 서비스는 다음 조건 중 하나를 충족해야 합니다.  
  a. VNet에 직접 배포되어야 합니다.  
  나. 해당 Azure 서비스의 지침에 따라 선택적으로 허용되어야 합니다.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>옵션 1: 전용 인스턴스 “VNet 삽입”

VNet 삽입을 사용하면 고객이 HDInsight와 같은 지정된 Azure 서비스의 전용 인스턴스를 고유한 VNet에 선택적으로 배포할 수 있습니다. 서비스 인스턴스는 고객 VNet의 전용 서브넷에 배포됩니다. VNet 삽입을 사용하면 비인터넷 라우팅 가능 주소를 통해 서비스 리소스에 액세스할 수 있습니다.  온-프레미스 인스턴스는 공용 인터넷 주소 공간에 방화벽을 여는 대신, ExpressRoute 또는 사이트 간 VPN을 통해 직접 VNet 주소 공간을 경유하여 이러한 서비스 인스턴스에 액세스할 수 있습니다. 전용 인스턴스를 끝점에 연결하면 IaaS TIC 준수에 사용된 것과 동일한 전략을 활용하여, 기본 라우팅을 통해 인터넷 바인딩된 트래픽을 온-프레미스 바인딩된 가상 네트워크 게이트웨이로 리디렉션할 수 있습니다. 지정된 서브넷에 대한 NSG(네트워크 보안 그룹)를 통해 인바운드 및 아웃바운드 액세스를 추가로 제어할 수 있습니다.

![VNet 삽입 개요 다이어그램](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>옵션 2: VNet 서비스 끝점 

Azure VNet에 통합하는 대체 방법인 “서비스 끝점” 기능을 제공하는 Azure 다중 테넌트 서비스 수가 점점 증가하고 있습니다. VNet 서비스 끝점은 직접 연결을 통해 VNet의 ID 및 VNet IP 주소 공간을 서비스로 확장합니다.  VNet에서 Azure 서비스로의 트래픽은 항상 Azure 백본 네트워크에 유지됩니다. 서비스에 대해 서비스 끝점을 사용할 수 있게 되면 서비스에서 노출된 정책을 통해 서비스 연결을 해당 VNet로 제한할 수 있습니다. 액세스 검사는 Azure 서비스에 의해 플랫폼에 적용되며, 잠긴 리소스에 대한 액세스는 요청이 허용된 VNet/서브넷 및/또는 ExpressRoute를 사용하는 경우, 온-프레미스 트래픽을 식별하는 데 사용되는 두 개의 IP에서 시작된 경우에만 부여됩니다. 이 기능을 사용하여 인바운드/아웃바운드 트래픽이 PaaS 서비스에서 직접 나가지 않도록 효과적으로 방지할 수 있습니다.

![서비스 끝점 개요 다이어그램](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>네트워크 상황 인식을 위해 클라우드 네이티브 도구 사용

Azure는 네트워크 트래픽 흐름을 파악하는 데 필요한 상황 인식에 도움이 되는 클라우드 네이티브 도구를 제공합니다. 클라우드 네이티브 도구는 TIC 정책을 준수하는 데 필요하지는 않지만 보안 기능을 강화할 수 있습니다.

### <a name="azure-policy"></a>Azure Policy

Azure Policy(https://azure.microsoft.com/services/azure-policy/)는 준수 이니셔티브를 감사하고 적용하는 기능을 조직에 제공하는 Azure 서비스입니다.  현재 Azure 상용 클라우드에서 공개 미리 보기로 제공되고 있으며 Microsoft Azure for Government에서는 아직 사용할 수 없습니다. TIC에 민감한 고객은 향후 준수 보증을 위해 Policy 규칙의 계획 및 테스트를 시작할 수 있습니다. Azure Policy는 구독 수준을 대상으로 하며 이니셔티브, 정책 정의, 감사 및 적용 결과, 예외 관리를 위한 중앙 집중식 인터페이스를 제공합니다. 관리자는 다양한 기본 제공 Azure Policy 정의 외에도 간단한 JSON 템플릿을 통해 고유한 사용자 지정 정의를 만들 수 있습니다. Microsoft에서는 대부분의 고객이 가능한 한, 적용보다 감사에 우선 순위를 두는 것을 권장합니다.

다음 샘플 정책은 TIC 준수 시나리오에 유용할 수 있습니다.

|정책  |샘플 시나리오  |템플릿 시작  |
|---------|---------|---------|
|사용자 정의 경로 테이블 적용 |     모든 가상 네트워크 지점의 기본 경로가 온-프레미스로 라우팅이 승인된 가상 네트워크 게이트웨이를 가리키는지 확인 | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|지역에서 Network Watcher를 사용할 수 없는 경우 감사  | 사용된 모든 지역에서 Network Watcher를 사용할 수 있는지 확인  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|모든 서브넷에서 NSG X  | 인터넷 트래픽이 차단된 NSG(또는 승인된 NSG 집합)가 모든 VNet의 모든 서브넷에 적용되었는지 확인 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|모든 NIC에서 NSG X | 인터넷 트래픽이 차단된 NSG가 모든 VM의 모든 NIC에 적용되었는지 확인 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|VM 네트워크 인터페이스에 대해 승인된 VNet 사용  | 모든 NIC가 승인된 VNet에 있는지 확인 | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|허용되는 위치 | 모든 리소스가 규격 VNet 및 Network Watcher 구성이 있는 지역에 배포되었는지 확인  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|PublicIP와 같은 허용되지 않는 리소스 종류 없음  | 준수 플랜이 없는 리소스 종류의 배포 금지. 예를 들어, 이 정책을 사용하여 공용 IP 주소 리소스의 배포를 금지할 수 있습니다. NSG 규칙을 사용하여 인바운드 인터넷 트래픽을 효과적으로 차단할 수 있지만, 공용 IP 사용을 금지하면 공격 표면이 더욱 축소됩니다.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [트래픽 분석](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Azure Network Watcher의 트래픽 분석은 흐름 로그 데이터 및 기타 로그를 사용하여 네트워크 트래픽에 대한 전반적인 개요를 제공합니다. 이 데이터는 TIC 준수를 감사하고 문제 지점을 식별하는 데 유용할 수 있습니다. 개요 대시보드를 사용하여 인터넷과 통신한 다음, TIC 라우팅을 위한 집중 목록을 제공하는 VM을 신속하게 차단할 수 있습니다.

![트래픽 분석 스크린샷](media/tic-traffic-analytics-1.png)

“지역 지도”를 사용하여 VM에 대한 인터넷 트래픽의 물리적 대상을 신속하게 식별하여 의심스러운 위치나 패턴 변경을 파악하고 분류할 수 있습니다.

![트래픽 분석 스크린샷](media/tic-traffic-analytics-2.png)

네트워크 토폴로지 맵을 사용하여 기존 가상 네트워크를 신속하게 조사할 수 있습니다.

![트래픽 분석 스크린샷](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Azure Network Watcher 다음 홉

Network Watcher가 모니터링하는 지역의 네트워크는 “다음 홉” 테스트를 수행하여, Network Watcher에서 “다음 홉” 대상을 확인할 샘플 네트워크 흐름에 대한 소스 및 대상의 유형을 포털에서 쉽게 액세스하도록 할 수 있습니다. VM 및 예제 인터넷 주소에 대해 이 테스트를 사용하여 “다음 홉”이 실제로 네트워크 가상 게이트웨이인지 확인할 수 있습니다.

![Network Watcher 다음 홉](media/tic-network-watcher.png)

## <a name="conclusions"></a>결론

2018년 5월에 작성 및 정의된 TIC 2.0 부록 H 지침을 준수하도록 Microsoft Azure, Office 365 및 Dynamics 365 액세스를 쉽게 구성할 수 있습니다.  Microsoft는 이 지침이 변경될 수 있음을 알고 있으며, 새 지침이 릴리스될 때 고객이 적시에 지침을 준수할 수 있도록 지원할 것입니다.

## <a name="appendix-tic-patterns-for-common-workloads"></a>부록: 공통 워크로드에 대한 TIC 패턴

| Category | 워크로드 | IaaS | 전용 PaaS/VNet 삽입  | 서비스 엔드포인트  |
|---------|---------|---------|---------|--------|
| 컴퓨팅 | Linux 가상 머신 | 예 | | |
| 컴퓨팅 | Windows 가상 머신 | 예 | | |
| 컴퓨팅 | Virtual Machine Scale Sets | 예 | | |
| 컴퓨팅 | Azure 기능 | | ASE(App Service Environment)를 통해 | |
| 웹 및 모바일 | 내부 웹 응용 프로그램 | | ASE(App Service Environment)를 통해 | |
| 웹 및 모바일 | 내부 모바일 응용 프로그램 | | ASE(App Service Environment)를 통해 | |
| 웹 및 모바일 | API Apps | | ASE(App Service Environment)를 통해 | |
| 컨테이너 | ACS(Azure Container Service) | | | 예 |
| 컨테이너 | Azure Container Service (AKS)* | | | 예 |
| 데이터베이스 | SQL Database | | Azure SQL Database Managed Instance* | Azure SQL |
| 데이터베이스 | Azure Database for MySQL | | | 예 |
| 데이터베이스 | Azure Database for PostgreSQL | | | 예 |
| 데이터베이스 | SQL Data Warehouse | | | 예 |
| 데이터베이스 | Azure Cosmos DB | | | 예 |
| 데이터베이스 | Redis Cache | | 예 | |
| Storage | Blob | 예 | | |
| Storage | 파일 | 예 | | |
| Storage | 큐 | 예 | | |
| Storage | 테이블 | 예 | | |
| Storage | 디스크 | 예 | | |

*: 2018년 5월 기준 Azure Government의 공개 미리 보기  
**: 2018년 5월 기준 Azure Government의 비공개 미리 보기

