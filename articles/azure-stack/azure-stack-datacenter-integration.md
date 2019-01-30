---
title: 일반 데이터 센터 통합 고려 사항 Azure Stack 통합 시스템 | Microsoft Docs
description: 이제 계획 및 다중 노드 Azure Stack을 사용 하 여 데이터 센터 통합에 대 한 준비를 수행할 수 있는 작업에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: afb4e634b7e255ef8f2cfc84319029af7412372e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251880"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Azure Stack 통합 시스템의 데이터 센터 통합 고려 사항
Azure Stack 통합 시스템에서 관심이 있는 경우에 배포 및 시스템 데이터 센터에 적용 하는 방법의 주요 계획 고려 사항 중 일부를 이해 해야 합니다. 이 문서에서는 Azure Stack 다중 노드 시스템에 대 한 중요 한 인프라 결정을 내릴 수 있도록 이러한 고려 사항은의 대략적인 개요를 제공 합니다. 이러한 고려 사항을 이해에는 데이터 센터에 Azure Stack을 배포할 때 OEM 하드웨어 공급 업체를 작업할 때 도움이 됩니다.  

> [!NOTE]
> Azure Stack 다중 노드 시스템 인증된 한 하드웨어 공급 업체에서 구입할 수만 있습니다. 

Azure Stack을 배포 하려면 배포 빠르고 원활 하 게 이동 하는 프로세스를 시작 하기 전에 솔루션 공급자에 계획 정보를 제공 해야 합니다. 정보는 네트워킹, 보안 및 여러 다른 영역 및 의사 결정자의 기술 자료를 필요할 수 있는 많은 중요 한 결정을 사용 하 여 id 정보에서 범위를 필요 합니다. 따라서을 가져오는 하 게 조직 내에서 여러 팀에서 배포가 시작 되기 전에 필요한 모든 정보가 준비 있는지 확인 해야 합니다. 이야기를 나누어 보려고 하드웨어 공급 업체에이 정보를 수집 하는 중에 결정을 내리는 데 유용한 조언이 있을 수 있습니다 하는 것이 도움이 됩니다.

연구 하 고 필요한 정보를 수집 하는 동안 네트워크 환경에 몇 가지 사전 배포 구성을 변경 해야 합니다. 이 라우터, 스위치 및 새 Azure Stack 솔루션 스위치에 대 한 연결을 준비 하도록 방화벽을 구성 하는 Azure Stack 솔루션에 대 한 IP 주소 공간 예약을 포함할 수 있습니다. 계획에 도움이 최대 인라인 주제 영역 전문가 되었는지 확인 합니다.

## <a name="capacity-planning-considerations"></a>용량 계획 고려 사항
획득을 위해 Azure Stack 솔루션을 평가할 때에 Azure Stack 솔루션의 전체 용량에 직접적인 영향을 포함 하는 하드웨어 구성 선택 항목을 수행 되어야 합니다. 여기에 CPU, 메모리 밀도, 저장소 구성 및 전반적인 솔루션 확장 (예: 서버의 수)의 클래식 선택이 포함 됩니다. 기존의 가상화 솔루션과 달리 이러한 구성 요소가 사용 가능한 용량을 확인할 수의 간단한 산술 연산을 적용 되지 않습니다. 첫 번째 원인은 Azure Stack 솔루션 자체 내에서 인프라 또는 관리 구성 요소를 호스트 하도록 설계 되는 것입니다. 복원 력; 지원 하기 위해 예약 되어 솔루션의 용량 중 두 번째 이유는 테 넌 트 워크 로드의 중단을 최소화 하는 방식으로 솔루션의 소프트웨어 업데이트 합니다. 

합니다 [Azure Stack capacity planner 워크시트](https://aka.ms/azstackcapacityplanner) 하도록 정확한 정보를 두 가지 방법으로 용량 계획에 대해 결정할: 중 하나를 선택 하 여 하드웨어를 제공 하는 리소스 또는 조합에 맞게 시도 정의 하 여 Azure Stack 워크 로드를 사용할 수 있는 하드웨어에서 지 원하는 Sku를 보려면 실행 됩니다. 마지막으로, Azure Stack에서 계획 및 구성 관련 결정을 내리는 데 도움이 되는 가이드 스프레드시트 것입니다. 

사용자 고유의 조사와 분석의 대 안으로 맞도록 스프레드시트를 사용 하는 것이 없습니다.  Microsoft는 진술이 나 보증도 명시적 또는 묵시적으로 스프레드시트에서 제공 하는 정보는.



## <a name="management-considerations"></a>관리 고려 사항
Azure Stack은 봉인된 된 시스템 인프라 잠겨 둘 다에서 권한 있는 및 관점을 네트워크. 네트워크 액세스 제어 목록 (Acl) 허가 되지 않은 들어오는 모든 트래픽 및 인프라 구성 요소 간의 불필요 한 모든 통신을 차단에 적용 됩니다. 따라서 시스템에 액세스할 권한이 없는 사용자가 어렵게 됩니다.

일상적인 관리 및 작업에 대해 무제한 관리자 액세스 권한 없음 인프라에 있습니다. Azure Stack 운영자는 시스템 관리자 포털을 통해 또는 (PowerShell 또는 REST API)를 통해 Azure Resource Manager를 통해 관리 해야 합니다. Hyper-v 관리자 또는 장애 조치 클러스터 관리자와 같은 다른 관리 도구에서 시스템에는 액세스가 있습니다. 시스템을 보호 하기 위해, 타사 소프트웨어 (예: 에이전트)는 Azure Stack 인프라의 구성 요소 내에서 설치할 수 없습니다. 외부 관리 및 보안 소프트웨어와의 상호 운용성 PowerShell 또는 REST API를 통해 발생합니다.

경고 중재 단계를 통해 확인 되지 문제 해결을 위한 높은 수준의 액세스 필요 하면 Microsoft 기술 지원 서비스를 사용 하 여 작업 해야 합니다. 지원을 통해 방법이 고급 작업을 수행 하려면 시스템에 대 한 임시 전체 관리자 액세스를 제공 합니다. 

## <a name="identity-considerations"></a>Id 고려 사항

### <a name="choose-identity-provider"></a>Id 공급자를 선택 합니다.
Azure Stack 배포의 경우 Azure AD 또는 AD FS에 대 한 사용 하려는 id 공급자를 고려해 야 할 해야 합니다. 전체 시스템 재배포 하지 않고 배포 후 id 공급자를 전환할 수 없습니다. Azure AD 계정을 소유 하지 않은 하 고, 클라우드 서비스 공급자에서 제공 하는 계정을 사용 하는 공급자를 전환 하 고 다른 Azure AD를 사용 하려는 경우 계정, f 솔루션을 다시 배포 하려면 솔루션 공급자에 게 문의 해야 하는 시점에서 또는 비용에 있습니다.



Id 공급자 선택 하는 등 Active Directory 도메인에 참가 여부를 테 넌 트 가상 컴퓨터, id 시스템 및 계정에서 사용 하는 관련이 없습니다. 이 별도입니다.

에 id 공급자를 선택 하는 방법에 대 한 자세히 알아볼 수 있습니다 합니다 [Azure Stack 통합된 시스템 연결 모델 문서](./azure-stack-connection-models.md)합니다.

### <a name="ad-fs-and-graph-integration"></a>AD FS 및 그래프 통합
AD FS를 사용 하 여 id 공급자로 Azure Stack을 배포 하려는 경우에 페더레이션 트러스트를 통해 기존 AD FS 인스턴스를 사용 하 여 Azure Stack에서 AD FS 인스턴스를 통합 해야 합니다. 이 id를 기존 Active Directory 포리스트를 Azure Stack에서 리소스를 사용 하 여 인증할 수 있습니다.

기존 Active Directory를 사용 하 여 Azure Stack에서 그래프를 통합할 수 있습니다. 이 옵션을 사용 하면 역할 기반 Access Control (RBAC) Azure Stack에서 관리할 수 있습니다. 리소스에 대 한 액세스 위임 되는 그래프 구성 요소 LDAP 프로토콜을 사용 하 여 기존 Active Directory 포리스트의 사용자 계정을 조회 합니다.

다음 다이어그램은 통합 된 AD FS 및 그래프 트래픽 흐름을 보여줍니다.
![AD FS 및 그래프 트래픽 흐름을 보여 주는 다이어그램](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>라이선스 모델
사용 하려는 라이선스 모델을 결정 해야 합니다. 사용 가능한 옵션은 인터넷에 연결 하는 Azure Stack을 배포 하는 여부에 따라 달라 집니다.
- 에 [배포를 연결](azure-stack-connected-deployment.md),-수-종 또는 용량 기반 라이선스를 선택할 수 있습니다. 수-종 필요 사용량을 보고, 그런 다음 Azure 상거래를 통해 비용이 청구는 Azure에 연결 합니다. 
- 전용 용량 기반 라이선스는 지원 되는 경우 있습니다 [배포 연결이 끊긴](azure-stack-disconnected-deployment.md) 인터넷에서. 

라이선스 모델에 대 한 자세한 내용은 참조 하세요. [Microsoft Azure Stack 패키징 및 가격 책정](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다.


## <a name="naming-decisions"></a>명명 결정

Azure Stack 네임 스페이스, 영역 이름 및 외부 도메인 이름을 특히 계획 하고자 하는 방법을 생각해 해야 합니다. 공용 끝점에 대 한 Azure Stack 배포의 외부 정규화 된 도메인 이름 (FQDN)에 이러한 두 이름은의 조합입니다. &lt; *지역*&gt;.&lt; *fqdn*&gt;합니다. 예를 들어 *east.cloud.fabrikam.com*합니다. 이 예제에서는 Azure Stack 포털은 다음 Url에서 사용 가능한 것:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Azure Stack 배포에 대 한 선택 영역 이름이 고유 해야 하며 포털 주소에 표시 됩니다. 

다음 표에서 이러한 도메인 명명 결정 합니다.

| 이름 | 설명 | 
| -------- | ------------- | 
|지역 이름 | 첫 번째 Azure Stack 지역의 이름입니다. 이 이름은 공용 가상 IP 주소 (Vip)는 Azure Stack 관리에 대 한 FQDN의 일부로 사용 됩니다. 일반적으로 지역 이름 데이터 센터 위치와 같은 물리적 위치 식별자 여야 합니다.<br><br>지역 이름 문자 및 숫자 0-9 사이의 구성 되어야 합니다. 와 같은 특수 문자가 "-" 또는 "#" 등 허용 됩니다.| 
| 외부 도메인 이름 | 외부 연결 Vip 사용 하 여 끝점에 대 한 도메인 이름 시스템 (DNS) 영역의 이름입니다. 이러한 공용 Vip에 대 한 FQDN에 사용 합니다. | 
| 개인 (내부) 도메인 이름 | 인프라 관리에 대 한 Azure Stack에서 만든 도메인 (및 내부 DNS 영역)의 이름입니다. 
| | |

## <a name="certificate-requirements"></a>인증서 요구 사항

배포에 대 한 공용 끝점에 대 한 Secure Sockets Layer (SSL) 인증서를 제공 해야 합니다. 높은 수준에서는 인증서 요구 사항은:

- 단일 와일드 카드 인증서를 사용할 수 있고 전용된 인증서 집합을 사용 하 여 저장소 및 Key Vault와 같은 끝점에 대해서만 와일드 카드를 사용할 수 있습니다.
- 신뢰할 수 있는 공용 인증 기관 (CA)에서 인증서를 발행할 수 있습니다 또는 고객이 관리 하는 CA입니다.

어떤 PKI에 대 한 자세한 정보에 대 한 인증서가 얻을, 참조, 방법 및 Azure Stack 배포에 필요 [Azure Stack 공개 키 인프라 인증서 요구 사항](azure-stack-pki-certs.md)합니다.  


> [!IMPORTANT]
> 일반적인 지침으로 제공된 된 PKI 인증서 정보를 사용 해야 합니다. Azure Stack에 대 한 PKI 인증서를 구입 하기 전에 OEM 하드웨어 파트너와 작동 합니다. 자세한 인증서 지침 및 요구 사항 제공 됩니다.


## <a name="time-synchronization"></a>시간 동기화
사용 하 여 서버를 사용 하 여 Azure Stack을 동기화 하는 특정 시간을 선택 해야 합니다.  시간 symbolization Azure Stack 및 해당 인프라 역할을 하는 중요 한은 서로 사용 하 여 내부 서비스를 인증 하는 데 사용 되는 Kerberos 티켓을 생성 하는 것입니다.

시간 동기화 서버에 대 한 IP 인프라의 구성 요소는 대부분 URL을 확인할 수 있지만 일부 지원할 수 있습니다만 IP 주소를 지정 해야 합니다. 경우는 연결이 끊긴된 배포 옵션을 사용 하 여, 지정 해야 Azure Stack에 인프라 네트워크에서 액세스 수 있는지 확인 시간 서버에 있는 회사 네트워크에 있습니다.

## <a name="connect-azure-stack-to-azure"></a>Azure Stack을 Azure에 연결

하이브리드 클라우드 시나리오에 대 한 Azure Stack을 Azure에 연결 하는 방법에 대해 계획 해야 합니다. 두 가지가 있습니다 지원 되는 Azure에서 가상 네트워크에 Azure Stack에서 가상 네트워크를 연결 하려면: 
- **사이트 간**합니다. IPsec (IKE v1 및 IKE v2)을 통한 가상 사설망 (VPN) 연결입니다. 이 연결 유형은 VPN 장치 또는 라우팅 및 원격 액세스 서비스 (RRAS) 필요합니다. Azure에서 VPN gateway에 대 한 자세한 내용은 참조 하세요. [VPN Gateway에 대 한](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)합니다. 이 터널을 통해 통신 암호화 되 고 안전 합니다. 그러나 대역폭 (100-200 Mbps) 터널의 최대 처리량으로 제한 됩니다.
- **아웃 바운드 NAT**합니다. Azure Stack의 모든 가상 머신은 기본적으로 아웃 바운드 NAT. 통해 외부 네트워크에 연결할 수 있습니다. Azure Stack에서 만든 각 가상 네트워크에 할당 된 공용 IP 주소를 가져옵니다. 든 가상 머신에 공용 IP 주소를 직접 할당 된 공용 IP 주소를 사용 하 여 부하 분산 장치 뒤에 가상 네트워크의 VIP를 사용 하 여 아웃 바운드 NAT 통해 아웃 바운드 액세스를 갖게 됩니다. 이 가상 컴퓨터에서 시작 되며 외부 네트워크 (인터넷 또는 인트라넷)에 대 한 대상으로 하는 통신에 대해서만 작동 합니다. 외부에서 가상 머신과 통신 데 사용할 수 없습니다.

### <a name="hybrid-connectivity-options"></a>하이브리드 연결 옵션

하이브리드 연결을 제공 하려는 어떤 종류의 배포 및 배포 될 위치를 고려해 야 할 중요 한 것입니다. 테 넌 트 당 네트워크 트래픽을 격리 해야 하는지 여부와 인트라넷 또는 인터넷 배포 해야 하는지 여부를 고려 하는 것이 필요 합니다.

- **단일 테 넌 트 Azure Stack**합니다. 보이는, 적어도 네트워킹 측면에서 단일 테 넌 트 것 처럼 Azure Stack 배포 합니다. 구독을 테 넌 트 여러 될 수 있지만 인트라넷 서비스와 마찬가지로 모든 트래픽은 동일한 네트워크를 통해 이동 합니다. 구독 간에 네트워크 트래픽을 다른 구독으로 동일한 네트워크 연결을 통해 이동 하 고 암호화 된 터널을 통해 격리 시킬 필요가 없습니다.

- **다중 테 넌 트 Azure Stack**합니다. Azure Stack에 외부 네트워크에 대 한 바인딩되는 각 테 넌 트 구독의 트래픽은 다른 테 넌 트의 네트워크 트래픽으로부터 격리 되어야 합니다. 여기서는 Azure Stack 배포 합니다.
 
- **인트라넷 배포**합니다. 일반적으로 개인 IP 주소 공간에 하나 이상의 방화벽 뒤에 회사 인트라넷에 위치 하는 Azure Stack 배포 합니다. 공용 IP 주소를 공용 인터넷을 통해 직접 라우팅할 수 없기 때문으로 실제로 공용있지 않습니다.

- **인터넷 배포**합니다. 공용 VIP 범위에 대 한 공용에 연결 된 Azure Stack 배포를 인터넷 라우팅 가능한 공용 IP를 사용 하 여 인터넷을 해결 합니다. 배포 방화벽 뒤에 앉아 여전히 수 이지만 공용 VIP 범위 공용 인터넷과 Azure 로부터의 직접 연결할 수 있습니다.
 
다음 표에서 장점과 단점, 사용 사례를 사용 하 여 하이브리드 연결 시나리오를 보여 줍니다.

| 시나리오 | 연결 방법 | 장점 | 단점 | 에 적합 합니다. |
| -- | -- | --| -- | --|
| 단일 테 넌 트 Azure Stack, 인트라넷 배포 | 아웃 바운드 NAT | 더 나은 대역폭 전송 속도가 빨라집니다. 간단 하 게 구현할; 게이트웨이가 필요 없습니다. | 트래픽을 암호화 되지 않습니다. 격리 없음 또는 스택 외부 암호화 합니다. | 모든 테 넌 트에 동일 하 게 신뢰할 수 있는 엔터프라이즈 배포 합니다.<br><br>Azure에 Azure ExpressRoute 회로 보유 하는 기업입니다. |
| 다중 테 넌 트 Azure Stack, 인트라넷 배포 | 사이트 간 VPN | 대상 VNet 테 넌 트에서 트래픽이 보호 됩니다. | 사이트 간 VPN 터널 대역폭 제한 됩니다.<br><br>대상 네트워크에서 VPN 장치와 가상 네트워크에 게이트웨이가 필요합니다. | 여기서 일부 테 넌 트 트래픽 엔터프라이즈 배포는 다른 테 넌 트에서 보호 되어야 합니다. |
| 단일 테 넌 트 Azure Stack, 인터넷 배포 | 아웃 바운드 NAT | 더 나은 대역폭 전송 속도가 빨라집니다. | 트래픽을 암호화 되지 않습니다. 격리 없음 또는 스택 외부 암호화 합니다. | 호스팅 시나리오 테 넌 트 자체 Azure Stack 배포 및 Azure Stack 환경에 전용된 회로 가져오는 위치입니다. 예를 들어, ExpressRoute 및 다중 프로토콜 레이블 전환 (MPLS).
| 다중 테 넌 트 Azure Stack, 인터넷 배포 | 사이트 간 VPN | 대상 VNet 테 넌 트에서 트래픽이 보호 됩니다. | 사이트 간 VPN 터널 대역폭 제한 됩니다.<br><br>대상 네트워크에서 VPN 장치와 가상 네트워크에 게이트웨이가 필요합니다. | 호스팅 공급자는 다중 테 넌 트 클라우드를 제공 하려고 하는 시나리오, 서로 트래픽과 테 넌 트를 신뢰 하지 않는 암호화 되어야 합니다.
|  |  |  |  |  |

### <a name="using-expressroute"></a>ExpressRoute 사용

Azure Stack을 통해 Azure에 연결할 수 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 단일 테 넌 트 인트라넷 및 다중 테 넌 트 시나리오에 대 한 합니다. 프로 비전 된 ExpressRoute 회로 통해 필요 [연결 공급자](https://docs.microsoft.com/azure/expressroute/expressroute-locations)합니다.

다음 다이어그램은 단일 테 넌 트 시나리오에 대 한 ExpressRoute를 보여 줍니다 (여기서 "고객 연결" ExpressRoute 회로).

![단일 테 넌 트 ExpressRoute 시나리오를 보여주는 다이어그램](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

다음 다이어그램은 다중 테 넌 트 시나리오에 대 한 ExpressRoute를 보여줍니다.

![다중 테 넌 트 ExpressRoute 시나리오를 보여주는 다이어그램](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>외부 모니터링
Azure Stack 배포 및 장치에서 모든 경고의 단일 뷰를 얻으려면을 보완 하 고 경고 티켓에 대 한 기존 IT 서비스 관리 워크플로를 통합 하면 [솔루션을모니터링하는외부데이터센터를사용하여AzureStack통합](azure-stack-integrate-monitor.md).

Azure Stack 솔루션에 포함 된 하드웨어 수명 주기 호스트는 하드웨어에 대 한 OEM 공급 업체에서 제공한 관리 도구를 실행 하는 Azure Stack 외부 컴퓨터. 이러한 도구 또는 데이터 센터에서 기존 모니터링 솔루션을 사용 하 여 직접 통합 하는 다른 솔루션을 사용할 수 있습니다.

다음 표에서 현재 사용 가능한 옵션 목록을 보여 줍니다.

| 영역 | 외부 모니터링 솔루션 |
| -- | -- |
| Azure Stack 소프트웨어 | [Operations Manager에 대 한 azure Stack 관리 팩](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios 플러그 인](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST 기반 API 호출 | 
| 물리적 서버 (IPMI 통해 Bmc) | OEM 하드웨어-Operations Manager 공급 업체 관리 팩<br>OEM 하드웨어 공급 업체에서 제공한 솔루션<br>하드웨어 공급 업체 Nagios 플러그 인 | OEM 파트너 지원 솔루션 (포함)를 모니터링 합니다. | 
| 네트워크 장치에서는 SNMP) | Operations Manager 네트워크 장치 검색<br>OEM 하드웨어 공급 업체에서 제공한 솔루션<br>Nagios는 플러그 인 전환 |
| 테 넌 트 구독 상태 모니터링 | [Windows Azure 용 system Center 관리 팩](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

다음 요구 사항에 유의하세요.
- 사용할 솔루션은 에이전트 없는 이어야 합니다. Azure Stack 구성 요소 내에서 타사 에이전트를 설치할 수 없습니다. 
- System Center Operations Manager를 사용 하려는 경우 Operations Manager 2012 R2 또는 Operations Manager 2016가 필요 합니다.

## <a name="backup-and-disaster-recovery"></a>백업 및 재해 복구

백업 및 재해 복구 계획 모두 기본 Azure Stack 인프라 IaaS virtual machines와 PaaS 서비스를 호스트 하 고 테 넌 트 응용 프로그램 및 데이터에 대 한 계획 해야 합니다. 별도로 이러한 항목에 대해 계획 해야 합니다.

### <a name="protect-infrastructure-components"></a>인프라 구성 요소를 보호 합니다.

할 수 있습니다 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md) SMB 인프라 구성 요소를 지정 하는 공유:

- 기존 Windows 기반 파일 서버 또는 타사 장치에서 외부 SMB 파일 공유를 해야 합니다.
- 네트워크 스위치와 하드웨어 수명 주기 호스트의 백업에 대 한이 동일한 공유를 사용 해야 합니다. OEM 하드웨어 공급 업체는 Azure Stack에 외부의 이러한 구성 요소 백업 및 복원에 대 한 지침을 제공 하는 데 도움이 됩니다. 여러분이 OEM 공급 업체의 권장 사항을 기반으로 백업 워크플로 실행 하는 일을 담당 합니다.

치명적인 데이터 손실이 발생 하는 경우 인프라에 백업 하는 배포 입력 및 식별자를 서비스 계정에 CA 루트 인증서 (연결이 끊어진된 배포의 경우)에 연결된 된 리소스, 계획, 제품 등 reseed 배포 데이터를 사용할 수 있습니다. 구독, 할당량, RBAC 정책 및 역할 할당 및 Key Vault 암호입니다.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>IaaS virtual machines에서 테 넌 트 응용 프로그램 보호

Azure Stack 백업 하지 않습니다 테 넌 트 응용 프로그램 및 데이터를 설정 합니다. Azure Stack에 외부 대상에 백업 및 재해 복구 보호 계획 해야 합니다. 테 넌 트 보호는 테 넌 트 기반 작업입니다. 테 넌 트 IaaS virtual machines에 대 한 파일 폴더, 응용 프로그램 데이터 및 시스템 상태를 보호 하기 위해 게스트 내 기술을 사용할 수 있습니다. 그러나 엔터프라이즈 또는 서비스 공급자 하려는 동일한 데이터 센터 또는 클라우드의 외부에서 백업 및 복구 솔루션을 제공 합니다.

Linux 또는 Windows IaaS virtual machines를 백업 하려면 파일, 폴더, 운영 체제 상태 및 응용 프로그램 데이터를 보호 하기 위해 게스트 운영 체제에 대 한 액세스를 사용 하 여 백업 제품을 사용 해야 합니다. 지원 되는 타사 제품 또는 Azure Backup에서 System Center 데이터 센터 Protection Manager를 사용할 수 있습니다.

데이터를 보조 위치로 복제 재해가 발생할 경우 응용 프로그램 장애 조치를 오케스트레이션 하 고 지원 되는 타사 제품 또는 Azure Site Recovery를 사용할 수 있습니다. 또한 Microsoft SQL Server와 같은 네이티브 복제를 지 원하는 응용 프로그램은 응용 프로그램이 실행 되 고 있는 다른 위치로 데이터를 복제할 수 있습니다.

## <a name="learn-more"></a>자세한 정보

- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 내용은 참조는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지입니다.
- 통합된 시스템, Azure Stack에 대 한 로드맵 및 지역 가용성에 대 한 자세한 백서를 참조 합니다. [Azure Stack: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 

## <a name="next-steps"></a>다음 단계
[Azure Stack 배포 연결 모델](azure-stack-connection-models.md)
