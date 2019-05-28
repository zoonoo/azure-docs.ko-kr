---
title: Azure 컴퓨팅 아키텍처 보안
description: 이 참조 아키텍처는 엔터프라이즈 수준 DMZ 아키텍처에 대 한 네트워크 가상 어플라이언스 및 기타 도구를 사용합니다. 이 아키텍처는 Department of Defense의 보안 클라우드 컴퓨팅 아키텍처 기능 요구 사항을 충족 하도록 설계 되었습니다. 또한 사용할 수 있는 조직에 대 한 합니다. 이 참조는 Citrix 또는 F5 어플라이언스를 사용 하는 두 가지 자동화 된 옵션을 포함 합니다.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963236"
---
# <a name="secure-azure-computing-architecture"></a>Azure 컴퓨팅 아키텍처 보안

미국 Azure로 워크 로드를 배포 하는 국방부 (DoD) 고객은 보안 가상 네트워크 설정 및 보안 도구 및 DoD standards and 연습에서 규정 된는 서비스를 구성 하는 지침에 대 한 요구 됩니다. 

Defense 정보 시스템 기관 (DISA) 게시 합니다 [보안 클라우드 컴퓨팅 아키텍처 (SCCA) 기능 요구 사항 문서 (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017에서. SCCA는 Defense 정보 시스템 네트워크의 (DISN) 및 상용 클라우드 공급자 연결 지점 보안 기능 목표를 설명 합니다. SCCA 업무 소유자 보안 연결 경계에서 응용 프로그램을 클라우드 하는 방법에 대해 설명 합니다. 상용 클라우드를 연결 하는 모든 DoD 엔터티에 SCCA FRD에 명시 된 지침을 따라야 합니다.
 
SCCA 네 가지 구성 요소에 있습니다.
 
- 경계 클라우드 액세스 지점 (BCAP)
- 가상 데이터 센터 보안 스택 (VDSS)
- 가상 데이터 센터 관리 서비스 (VDM)
- 신뢰할 수 있는 클라우드 자격 증명 관리자 (TCCM) 

Microsoft은 Azure에서 실행 되는 워크 로드를 IL4와 IL5 SCCA 요구 사항을 충족 하는 솔루션을 개발 했습니다. 이 Azure 전용 솔루션의 보안 Azure 컴퓨팅 아키텍처 (SACA) 라고 합니다. SACA를 배포 하는 고객 SCCA FRD 준수 됩니다. 연결 된 후 워크 로드를 Azure로 이동할 DoD 고객에 게 활성화할 수 있습니다.

SCCA 지침 및 아키텍처는 DoD 고객에 게 이지만 SACA 도움말 대 민 고객에 게 최신 수정 버전 신뢰할 수 있는 인터넷 연결 (티켓) 지침을 따라야 합니다. 최신 수정 버전 상용 고객 들이 Azure 환경 보호 하는 보안 DMZ를 구현 하고자 하는 데도 도움이 됩니다.


## <a name="secure-cloud-computing-architecture-components"></a>보안 클라우드 컴퓨팅 아키텍처 구성 요소

### <a name="bcap"></a>BCAP

BCAP의 목적은 DISN 클라우드 환경에서 발생 하는 공격 으로부터 보호 하기 위해입니다. BCAP 침입 탐지 및 방지를 수행합니다. 또한 허가 되지 않은 트래픽을 필터링 합니다. 이 구성 요소는 SCCA의 다른 구성 요소와 함께 있을 수 있습니다. 물리적 하드웨어를 사용 하 여이 구성 요소를 배포 하는 것이 좋습니다. BCAP 보안 요구 사항이 다음 표에 나열 됩니다.

#### <a name="bcap-security-requirements"></a>BCAP 보안 요구 사항

![BCAP 요구 사항 행렬](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS의 목적은 Azure에서 호스트 되는 DoD 소유자 중요 업무용 응용 프로그램을 보호 하는 것입니다. VDSS는 SCCA에서 보안 작업의 대부분을 수행합니다. 이 Azure에서 실행 되는 응용 프로그램 보안에 대 한 트래픽 검사를 수행 합니다. Azure 환경 내에서이 구성 요소를 제공할 수 있습니다.

#### <a name="vdss-security-requirements"></a>VDSS 보안 요구 사항

![VDSS 요구 사항 행렬](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDM의 목적은 호스트 보안 제공 하는 것 및 데이터 센터 서비스를 공유 합니다. VDM의 기능에 SCCA의 허브에서 실행 하거나 업무 소유자는 자신의 특정 Azure 구독에서의 부분을 배포할 수 있습니다. Azure 환경 내에서이 구성 요소를 제공할 수 있습니다.

#### <a name="vdms-security-requirements"></a>VDM 보안 요구 사항

![VDM 요구 사항 행렬](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM은 비즈니스 역할입니다. 이 개인의 SCCA 관리 담당 합니다. 업무 다음과 같습니다. 

- 계획 및 클라우드 환경에 대 한 계정 액세스에 대 한 정책을 설정 합니다. 
- Id 및 액세스 관리가 제대로 작동 하는지 확인 합니다. 
- 클라우드 자격 증명 관리 계획을 유지 합니다. 

이 개별 권한 부여 공식에 의해 지명 됩니다. BCAP, VDSS, 및 VDM는 TCCM 업무를 수행 해야 하는 기능을 제공 합니다.

#### <a name="tccm-security-requirements"></a>TCCM 보안 요구 사항

![TCCM 요구 사항 행렬](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 구성 요소 및 계획 고려 사항 

SACA 참조 아키텍처는 Azure의 VDSS 및 VDM 구성 요소를 배포 하 고는 TCCM 수 있도록 설계 되었습니다. 이 아키텍처는 모듈식입니다. VDSS 및 VDM 부분은 전혀 중앙 집중식된 허브에 존재할 수 있습니다. 컨트롤의 일부 업무 소유자 공간 또는 온-프레미스에서 충족할 수 있습니다. 모든 중요 업무용 소유자를 통해 연결할 수 있는 중앙 가상 네트워크에 VDSS 및 VDM 구성 요소를 공동 배치는 것이 좋습니다. 다음 다이어그램은이 아키텍처를 보여줍니다. 


![SACA 참조 아키텍처 다이어그램](media/sacav2generic.png)

기술 아키텍처 고 SCCA 규정 준수 전략을 계획할 때 모든 고객에 영향을 미치므로 시작 부분에서 다음 항목을 고려 합니다. 다음과 같은 문제가 DoD 고객에 게 개발 하 고 계획 및 실행을 저하 시키는 경향이 있습니다. 

#### <a name="which-bcap-will-your-organization-use"></a>조직에 사용할 수 있는 BCAP는?
   - DISA BCAP:
        - DISA에 두 운영 BCAPs는 오각형와 캠프 Roberts, CA 세 번째 곧 온라인 예정 되어 있습니다. 
        - 모든 DISA BCAPs Azure ExpressRoute 회로를 Azure에 연결에 대 한 DoD 고객에 게 사용할 수 있는 경우 
        - DISA에 엔터프라이즈 수준의 Microsoft 피어 링 세션을 DoD 고객에 게 Office 365와 같은 서비스 (SaaS) 도구로 Microsoft 소프트웨어에 가입. 사용 안 함 BCAP를 사용 하 여 연결 및 SACA에 피어 링을 설정할 수 있습니다. 
    - 사용자 고유의 BCAP를 빌드하십시오.
        - 이 옵션을 사용 하면 공동 배치 된 데이터 센터에서 공간 임대를 Azure에 ExpressRoute 회로 설정 해야 합니다. 
        - 이 옵션에는 추가 승인이 필요합니다. 
        - 추가 승인 및 실제 빌드 스케일 아웃으로 인해이 옵션에는 가장 많은 시간이 걸립니다. 
    - 사용 안 함 BCAP를 사용 하는 것이 좋습니다. 이 옵션을 바로 사용할 수에 기본 제공 중복성이 있고 프로덕션 환경에서 오늘에서 작동 하는 고객.
- DoD 라우팅할 수 있는 IP 공간:
    - 에 지에서 DoD 라우팅할 수 있는 IP 공간을 사용 해야 합니다. NAT를 사용 하 여 Azure에서 개인 IP 공간에 해당 공간을 연결할 하는 옵션은 사용할 수 있습니다.
    - DoD 정보 센터 (NIC (네트워크) IP 공간을 알 수에 게 문의 합니다. 사용 안 함을 사용 하 여 시스템/네트워크 승인 프로세스 (스냅인) 제출물의 일부로 필요합니다. 
    - NAT를 사용 하 여 Azure에서 개인 주소 공간을 연결 하려는 경우/24 개 SACA 배포 하려는 각 지역에 대 한 NIC에서 할당 된 주소 공간의 서브넷입니다.
- 중복성:
    - SACA 인스턴스 두 개 이상의 지역에 배포 합니다. DoD 클라우드에서 배포할 DoD 지역 모두 사용할 수 있습니다.
    - 별도 ExpressRoute 회로 통해 두 개 이상의 BCAPs에 연결 합니다. 그런 다음 각 지역의 SACA 인스턴스에 두 ExpressRoute 연결을 연결할 수 있습니다. 
- DoD 구성 요소 관련 요구 사항:
    - 조직 외부 SCCA 요구 사항 특정 요구 사항이 있습니까? 일부 조직에는 특정 IP 요구 사항이 있습니다.
- SACA는 모듈식 아키텍처:
    - 환경에 필요한 구성 요소만 사용 합니다. 
        - 단일 계층 또는 다중 계층 네트워크 가상 어플라이언스를 배포 합니다.
        - 통합 된 IP 또는 bring your own IP를 사용 합니다.
- 응용 프로그램 및 데이터의 DoD 영향 수준:
    - Microsoft IL5 지역에서 실행 중인 응용 프로그램의 가능성이 있으면 IL5 SACA 인스턴스에 빌드하십시오. 인스턴스는 IL4 응용 프로그램과 IL5 앞에 사용할 수 있습니다. 가장 가능성이 높은 IL5 응용 프로그램 앞에 IL4 SACA 인스턴스 인정을 받지 않습니다.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>VDSS에는 네트워크 가상 어플라이언스 공급 업체를 사용할 것인가?
앞에서 설명한 대로 다양 한 어플라이언스 및 Azure 서비스를 사용 하 여이 SACA 참조를 빌드할 수 있습니다. Microsoft는 F5와 Citrix와 SACA 아키텍처를 배포 하는 솔루션 템플릿 자동화 된 됩니다. 이러한 솔루션은 다음 섹션에서 다룹니다.

#### <a name="which-azure-services-will-you-use"></a>Azure 서비스를 사용 하 시겠습니까?
- Azure 서비스는 log analytics, 호스트 기반 보호 및 ID 기능에 대 한 요구 사항을 충족할 수 있습니다. 일부 서비스가 Microsoft IL5 지역의 일반적으로 사용할 수 없는 것 같습니다. 이 경우 이러한 Azure 서비스도 요구 사항을 충족할 수 없는 경우에 타사 도구를 사용 해야 합니다. 익숙한 도구와 Azure 네이티브 도구를 사용 하 여의 실행 가능성을 살펴봅니다.
- 최대한 많은 Azure 네이티브 도구를 사용 하는 것이 좋습니다. 클라우드 보안을 염두에 두고는 않으며 Azure 플랫폼의 나머지 부분과 매끄럽게 통합. 다음 목록의 SCCA의 다양 한 요구 사항에 맞게 Azure 네이티브 도구를 사용 합니다.

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 크기 조정
    - 크기 조정 연습을 완료 해야 합니다. SACA 인스턴스 및 네트워크 처리량 요구 사항을 통해 해야 하는 동시 연결 수를 확인 합니다. 
    - 이 단계는 중요 합니다. Vm 크기를 조정할 SACA 인스턴스에 사용 되는 다양 한 공급 업체에서 필요한 라이선스를 식별 하는 데 도움이 됩니다. 
    - 이 크기 조정 연습 과정 없이 적절 한 비용 분석을 수행할 수 없습니다. 최상의 성능을 위해 올바른 크기 조정 수도 있습니다. 


## <a name="most-common-deployment-scenario"></a>가장 일반적인 배포 시나리오

 또는 전체 배포를 통해 여러 Microsoft 고객에 게 완료 SACA 환경 계획 단계를 최소화 합니다. 해당 환경을 가장 일반적인 배포 시나리오에 대 한 정보를 표시 합니다. 다음 다이어그램은 가장 일반적인 아키텍처를 보여줍니다. 


![SACA 참조 아키텍처 다이어그램](media/sacav2commonscenario.png) 


다이어그램에서 보듯이 DoD 고객에 게는 일반적으로 두 DISA BCAPs에 구독 합니다. 다음 중 하나 동부 연안에 서 부 해안 및 다른 위치에 상주 합니다. ExpressRoute 사설 피어를 각각의 DISA BCAP 위치에서 Azure에 사용 됩니다. 그런 다음 이러한 ExpressRoute 피어 국방부 동부 및 중부 Azure DoD 지역에서 가상 네트워크 게이트웨이 연결 됩니다. SACA 인스턴스 국방부 동부 및 중부 Azure DoD 지역에 배포 됩니다. 모든 수신 및 송신 트래픽에 DISA BCAP에 대 한 ExpressRoute 연결을 통해 흐릅니다.

업무 소유자 응용 프로그램 계획 응용 프로그램을 배포 하는 Azure 지역을 선택 합니다. 가상 네트워크 피어 링 SACA 가상 네트워크에는 응용 프로그램의 가상 네트워크 연결을 사용 합니다. 그런 다음 이러한 force VDSS 인스턴스를 통해 모든 트래픽을 터널링 합니다.

SCCA 요구 사항을 만족 하기 때문에이 아키텍처를 좋습니다. 고가용성 및 쉽게 확장 가능한 됩니다. 또한 배포 및 관리를 간소화 합니다.

## <a name="automated-saca-deployment-options"></a>자동화 된 SACA 배포 옵션

 앞서 언급 했 듯이, Microsoft는 자동화 된 SACA 인프라 템플릿을 만들려면 두 공급 업체와 협력 했습니다. 다음 Azure 구성 요소를 배포 하는 두 템플릿 모두: 

- SACA 가상 네트워크
    - 관리 서브넷
        - 점프 상자 라고도 관리 Vm 및 서비스 배포는이 서브넷에서입니다.
        - VDM 서브넷
            - 이 서브넷은 Vm 및 서비스 VDM에 사용 되는 배포 되는 위치입니다.
        - 신뢰할 수 없는 및 신뢰할 수 있는 서브넷
            - 이러한 서브넷은 가상 어플라이언스 배포 되는 위치입니다.
        - 게이트웨이 서브넷 
            - 이 서브넷은 ExpressRoute 게이트웨이 배포 하는입니다.
- 관리 점프 상자 가상 컴퓨터
    - 환경-대역외 관리 용도로 사용 되는지 합니다.
- 네트워크 가상 어플라이언스
    - Citrix 중 하나를 사용 하 여 또는 F5 배포 템플릿을 기반으로 합니다.
- 공용 IP
    - ExpressRoute는 온라인 상태가 될 때까지 프런트 엔드 용도로 사용 되는지 합니다. 이러한 Ip는 백 엔드 Azure 개인 주소 공간을 변환 합니다.
- 경로 테이블 
    - 자동화 하는 동안 적용, 이러한 테이블 강제 터널 모든 트래픽을 라우팅할 가상 어플라이언스를 통해.
- Azure load balancer-표준 SKU
    - 트래픽 부하 분산할 기기 하는 데 사용 합니다.
- 네트워크 보안 그룹
    - 컨트롤 유형의 트래픽 특정 끝점 통과 하는 데 사용 합니다.


### <a name="citrix-saca-deployment"></a>Citrix SACA 배포

Citrix 배포 템플릿으로 항상 사용 가능한 Citrix ADC 어플라이언스의 두 계층을 배포합니다. 이 아키텍처는 VDSS의 요구 사항을 충족합니다. 

![Citrix SACA 다이어그램](media/citrixsaca.png)


Citrix 설명서 및 배포 스크립트를 참조 하세요 [이 GitHub 링크](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)합니다.


 ### <a name="f5-saca-deployment"></a>F5 SACA 배포

두 개의 개별 F5 배포 템플릿을 두 개의 서로 다른 아키텍처를 설명합니다. 첫 번째 템플릿 활성-활성 고가용성 구성에서 F5 어플라이언스의 하나의 계층을 있습니다. 이 아키텍처는 VDSS에 대 한 요구 사항을 충족 합니다. 두 번째 템플릿은 활성-활성 고가용성 F5s 두 번째 계층을 추가합니다. 이 두 번째 계층에는 고객이 F5 계층 사이 F5에서 별도 자체 IP를 추가할 수 있습니다. 일부 DoD 구성 요소 사용에 대 한 사전 설정 된 특정 IP를 갖습니다. 있는 경우 F5 어플라이언스의 단일 계층 아키텍처에 F5 장치의 IP를 포함 하기 때문에 대부분 작동 합니다.

![F5 SACA 다이어그램](media/f5saca.png)

F5 설명서 및 배포 스크립트를 참조 하세요 [이 GitHub 링크](https://github.com/f5devcentral/f5-azure-saca)합니다.












