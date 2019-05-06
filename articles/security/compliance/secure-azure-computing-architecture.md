---
title: Azure 컴퓨팅 아키텍처 보안
description: 이 네트워크 가상 어플라이언스 및 기타 도구를 활용 하는 엔터프라이즈 수준 DMZ 아키텍처에 대 한 참조 아키텍처입니다. 이 아키텍처는 Department of Defense의 보안 클라우드 컴퓨팅 아키텍처 기능 요구 사항을 충족 하도록 설계 되었습니다. 그러나 모든 조직에 활용할 수 있습니다. 이 참조는 Citrix 또는 F5 어플라이언스를 사용 하 여 두 자동화 된 옵션을 포함 합니다.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917597"
---
# <a name="secure-azure-computing-architecture"></a>Azure 컴퓨팅 아키텍처 보안

보안 가상 네트워크를 설정 및 보안 도구와 DoD standards and 연습에서 규정 된 서비스를 구성 하는 지침에 대 한 요청한 신속 하 게 증가 DoD 고객에 게 Azure로 워크 로드를 배포 합니다. 게시 사용 안 함 합니다 [보안 클라우드 컴퓨팅 아키텍처 (SCCA) 기능 요구 사항 문서](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017에서. SCCA Defense 정보 시스템 네트워크 보안의 (DISN) 기능 목표를 설명 하 고 상용 클라우드 공급자 연결 지점 및 소유자 보안 클라우드 응용 프로그램 연결 경계에서 업무에 어떻게. 상용 클라우드를 연결 하는 모든 DoD 엔터티에 SCCA FRD에 명시 된 지침을 따릅니다는 일부 터 전환 합니다.
 
SCCA의 네 가지 구성 요소가 있습니다. 경계 클라우드 액세스 지점 (BCAP), 가상 데이터 센터 보안 스택 (VDSS), 가상 데이터 센터 관리 서비스인 (VDM) 및 클라우드 자격 증명 관리자 (TCCM)를 신뢰할 수 있는 합니다. Microsoft은 Azure에서 실행 되는 작업을 IL4와 IL5 SCCA 요구 사항을 충족 하는 솔루션을 개발 했습니다. 이 Azure 특정 솔루션에 보안 Azure 컴퓨팅 아키텍처 (SACA) 라고 합니다. SACA를 배포 하는 고객 SCCA FRD 준수 되 고 연결 된 후 Azure로 워크 로드를 이동할 DoD 고객에 게 사용 하도록 설정 됩니다. 

신뢰할 수 있는 인터넷 연결 (티켓) 지침을 준수 하는 대 민 고객 뿐 아니라 보안 DMZ를 구현 하려는 상용 고객과 SACA 최신 수정 버전도 도움이 됩니다 SCCA 지침 및 아키텍처는 DoD 고객에 게 특정, 자신의 azure 환경을 보호 합니다.


## <a name="secure-cloud-computing-architecture-components"></a>보안 클라우드 컴퓨팅 아키텍처 구성 요소

**BCAP**

DISN 클라우드 환경에서 발생 하는 공격 으로부터 보호 하는 BCAP의 목적은입니다. BCAP는 침입 탐지 및 방지 수행할 뿐만 아니라 허가 되지 않은 트래픽을 필터링 합니다. 이 구성 요소는 SCCA의 다른 구성 요소와 함께 있을 수 있습니다. 이 구성 요소가 실제 하드웨어를 사용 하 여 배포 되는 것이 좋습니다. 아래 BCAP 보안 요구 사항 목록을 찾을 있습니다.

***BCAP 보안 요구 사항***

![BCAP 요구 사항 행렬](media/bcapreqs.png)


**VDSS**

VDSS의 목적은 Azure에서 호스트 되는 DoD 업무 소유자 응용 프로그램을 보호 하는 것입니다. VDSS는 SCCA에서 보안 작업의 대부분을 수행합니다. Azure에서 실행 중인 응용 프로그램을 보호 하기 위해 트래픽 검사를 수행 하 합니다. Azure 환경 내에서이 구성 요소를 제공할 수 있습니다.

***VDSS 보안 요구 사항***

![VDSS 요구 사항 행렬](media/vdssreqs.png)

**VDMS**

VDM의 목적은 호스트 보안 제공 하는 것 뿐만 아니라 데이터 센터 서비스를 공유 합니다. VDM의 기능에 SCCA의 허브에서 실행 하거나 업무 소유자는 자신의 특정 Azure 구독에서의 부분을 배포할 수 있습니다. Azure 환경 내에서이 구성 요소를 제공할 수 있습니다.

***VDM 보안 요구 사항***

![VDM 요구 사항 행렬](media/vdmsreqs.png)


**TCCM**

TCCM은 비즈니스 역할입니다. 이 SCCA 관리를 담당 수도 있습니다. 업무 계획 및 identity 보장 클라우드 환경에 대 한 계정 액세스에 대 한 정책을 설정 포함 및 액세스 관리 제대로 작동 하 고 클라우드 자격 증명 관리 계획을 유지 관리 합니다. 이 개별 권한 부여 공식에 의해 지명 됩니다. BCAP, VDSS, 및 VDM 해당 직무를 수행 하려면 TCCM에 필요한 기능을 제공 합니다.

***TCCM 보안 요구 사항***

![TCCM 요구 사항 행렬](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 구성 요소 및 계획 고려 사항 

SACA 참조 아키텍처는 TCCM 사용 뿐만 아니라 azure에서 VDSS 및 VDM 구성 요소를 배포 하도록 설계 되었습니다. 이 아키텍처는 모듈식 즉는 중앙 집중식된 허브에 있을 수 있는 모든 VDSS 및 VDM 부분이 또는 업무 소유자 공간 또는 온-프레미스에서 충족할 수 있는 컨트롤의 일부입니다. Microsoft 팀의 권장에는 중앙 가상 네트워크를 통해 모든 업무 소유자 연결할 수 있는 VDSS 및 VDM 구성 요소에 배치 합니다. 아래 다이어그램에서는 권장된 아키텍처를 보여 줍니다. 


![SACA 참조 아키텍처 다이어그램](media/sacav2generic.png)

기술 아키텍처 및 SCCA 규정 준수 전략을 계획할 때 고려해 야 할 여러 가지 있습니다. 모든 고객을 설명 해야 할 것부터 고려 항목에서는 다음 수행 되는 중요 합니다. 아래 항목에는 실제 DoD 고객에 게 개발 및 느려지고 계획 및 실행 하는 문제가 되었습니다. 

- 조직에 사용할 수 있는 BCAP는?
    - DISA BCAP
        - DISA에 두 운영 BCAPs 캠프 Roberts CA 고 오각형, 세 번째 곧 온라인으로 합니다. 
        - DISA BCAPs 모든 ExpressRoute 회로를 Azure DoD 고객에 게 연결 하 여 활용할 수 있는 경우 
        - 사용 안 함, Office 365와 같은 Microsoft SaaS 도구 구독할 DoD 고객에 대 한 엔터프라이즈 수준의 Microsoft 피어 링 세션을 이미 있습니다. 사용 안 함 BCAP를 사용 하 여 연결 및 SACA에 피어 링을 설정할 수 있습니다. 
    - 사용자 고유의 BCAP 빌드
        - 이 공간 공동 배치 된 데이터 센터에서 임대를 Azure에 ExpressRoute 회로 설정 해야 합니다. 
        - 이 옵션에 추가 승인을 해야 합니다. 
        - 추가 승인 및 실제 작성으로 인해이 옵션에는 가장 많은 시간이 걸립니다. 
    - Microsoft의 권장 DISA BCAP를 활용 하는 것입니다. 이 옵션에 중복성을 기본 제공 하며 현재 프로덕션에서 작동 하는 고객은 이미를 바로 사용할 수 합니다.
- DoD 라우팅할 수 있는 IP 공간
    - 에 지에서 DoD 라우팅할 수 있는 IP 공간을 사용 하도록 해야 합니다. Nat 옵션을 Azure에서 개인 IP 공간에 제품은입니다.  
    - DoD NIC IP 공간을 알 수 문의 DISA 사용 하 여 스냅인 제출물의 일부로 필요 합니다. 
    - / 24 개 수행 해야 Azure에서 개인 주소 공간에 NAT 하려는 경우 SACA 배포 하려는 각 지역에 대 한 NIC에서 할당 된 주소 공간의 서브넷입니다. 
- 중복 
    - SACA 인스턴스 두 개 이상의 지역에 배포 하는 것이 좋습니다. DoD 클라우드 둘 다 사용 가능한 DoD 지역에 배포 하기를 의미 합니다. 
    - 또한 별도 ExpressRoute 회로 통해 두 개 이상의 BCAPs에 연결 하는 것이 좋습니다. 모두 Express Routes는 다음 각 지역의 SACA 인스턴스에 연결할 수 있습니다. 
- DoD 구성 요소 관련 요구 사항
    - 조직 외부 SCCA 요구 사항 특정 요구 사항이 있습니까? (일부 조직에서는 특정 IP 요구 해야 하는 데 사용)
- SACA는 모듈식 아키텍처  
    - 환경에 필요한 구성 요소 에서만 사용 합니다. 
        - 단일 계층 또는 다층 계층에서 Nva를 배포 합니다.
        - IP를 통합 하거나 사용자 고유의 IP 가져오기
- 응용 프로그램 및 데이터의 DoD 영향 수준
    - IL5 지역에서 실행 중인 응용 프로그램의 가능성이 있으면 IL5 SACA 인스턴스에 작성 하는 것이 좋습니다. 인스턴스는 IL5 뿐만 아니라 IL4 응용 프로그램 앞에 사용할 수 있습니다. 하지만 IL5 응용 프로그램 앞에 IL4 SACA 인스턴스 가능성이 받지 인정 합니다. 
- VDSS에는 네트워크 가상 어플라이언스 공급 업체를 사용할 것인가?
    - 앞에서 설명한 대로 다양 한 어플라이언스 및 Azure 서비스를 사용 하 여이 SACA 참조를 빌드할 수 있습니다. 그러나 F5와 Citrix와 SACA 아키텍처를 배포 하는 자동화 된 솔루션 템플릿 했습니다지 않습니다. 이러한 솔루션 아래에서 자세히 설명 합니다. 
- Azure 서비스를 사용 하 시겠습니까?
    - Azure 서비스는 log analytics, 호스트 기반 보호 및 ID 기능 관련 요구 사항을 충족할 수 있습니다. 그러나 있기 일부 서비스 IL5 지역에서 일반적으로 사용할 수 없습니다. 이러한 Azure 서비스도 요구 사항을 충족할 수 없는 경우 몇 가지 타사 도구를 사용할 필요가 발생할 수 있습니다. 어떤 도구를 방법을 잘 알고 있고 Azure 네이티브 도구를 사용 하 여의 실행 가능성 확인 해야 합니다. 
    - Microsoft의 권장 사항은 많은 Azure 클라우드 보안을 염두 작성 하 고 Azure 플랫폼의 나머지 부분과 매끄럽게 통합 가능한 네이티브 도구를 사용 하는 경우 다음은 SCCA의 다양 한 요구 사항을 충족 하기 위해 활용할 수 있는 Azure 네이티브 도구 목록입니다. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
        - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- 크기 조정
    - 크기 조정 연습을 완료 해야 합니다. 네트워크 처리량 요구 사항 뿐만 아니라 SACA 인스턴스를 통해 할 수 있습니다 동시 연결 수를 확인 해야 합니다. 
    - 이 Vm 크기를 조정 하는 데 도움이 뿐만 아니라 SACA 인스턴스에 사용 되는 다양 한 공급 업체에서 필요한 라이선스를 식별 하는 데 도움이 되는 대로 중요 한 단계입니다. 
    - 좋은 비용 분석을 수행할 수 없습니다.이 크기 조정 연습 과정 없이 것도 모든 크기가 올바르게 최상의 성능을 위해 수 있도록 확인 해야 합니다. 


## <a name="most-common-deployment-scenario"></a>가장 일반적인 배포 시나리오

Microsoft는 여러 고객에 게 이미 전체 살펴보았다 배포 또는 적어도 SACA 환경 단계를 계획 합니다. 이 덕분에 수 가장 일반적인 배포 시나리오에 대 한 정보를 가져옵니다. 아래 다이어그램에서는 가장 일반적인 아키텍처를 보여 줍니다. 


![SACA 참조 아키텍처 다이어그램](media/sacav2commonscenario.png) 


다이어그램에서 보듯이 DoD 고객에 게는 일반적으로 이러한 삶에 서 부 해안의 및 동부 연안에 다른 위치 중 하나는 DISA BCAPs 중 두 가지를 구독 합니다. ExpressRoute 사설 피어를 각각의 DISA BCAP 위치에서 Azure에 사용 됩니다. 그런 다음 이러한 ExpressRoute 피어 국방부 동부 및 DoD 중부 Azure 지역에서 가상 네트워크 게이트웨이 연결 됩니다. SACA 인스턴스 국방부 동부 및 중부 Azure DoD 지역에 모든 수신 및 송신 트래픽 흐름 통해 DISA BCAP에 대 한 Expressroute 연결에서 배포 됩니다. 

중요 업무용 응용 프로그램은 다음에 응용 프로그램 배포는 Azure 지역을 선택할 소유자 및 가상 네트워크 피어 링 사용 하 여 해당 응용 프로그램에 연결할 SACA 가상 네트워크에 가상 네트워크의입니다. Force VDSS 인스턴스를 통해 모든 트래픽을 터널링 합니다. 

SCCA 요건을 충족할 항상 사용 가능한 쉽게 확장 가능 하 고, 배포 및 관리를 간소화 하는 것 처럼 Microsoft에서이 아키텍처 가장 좋습니다.

## <a name="automated-saca-deployment-options"></a>자동화 된 SACA 배포 옵션

 앞서 설명한 대로 Microsoft는 자동화 된 SACA 인프라 템플릿을 만들려면 두 공급 업체와 협력 했습니다. 두 템플릿 모두 다음 Azure 구성 요소를 배포 합니다. 

- SACA 가상 네트워크
    - 관리 서브넷
        - 관리 Vm 및 서비스 배포 되는 위치 (점프 상자)
        - VDM 서브넷
            - Vm 및 서비스 VDM에 사용 되는 배포 되는 위치
        - 신뢰할 수 없는 및 신뢰할 수 있는 서브넷 
            - 가상 어플라이언스 배포 되는 위치
        - 게이트웨이 서브넷
            - ExpressRoute 게이트웨이 배포할 위치
- 관리 점프 상자 가상 컴퓨터
    - 환경의 Out of Band Management에 사용 합니다.
- 네트워크 가상 어플라이언스
    - Citrix 또는 f5 키를 배포 하는 템플릿에 따라 합니다.
- 공용 IP
    - 프런트 엔드에 대 한 ExpressRoute 온라인 상태가 될 때까지 사용 합니다. 이러한 Ip는 백 엔드 Azure 개인 주소 공간에 변환
- 경로 테이블 
    - 적용 된 자동화, 이러한 경로 테이블 강제 터널링 하는 동안 모든 트래픽을 가상 어플라이언스를 통해
- Azure Load Balancer-표준 SKU
    - 이러한 기기에서 부하 분산 트래픽에 사용 됩니다.
- 네트워크 보안 그룹
    - 특정 끝점에 트래픽 종류를 통과할 수를 제어 하기 위한 사용


**Citrix SACA 배포**

Citrix는 항상 사용 가능한 Citrix ADC 어플라이언스의 두 계층을 배포 하는 배포 템플릿을 만들었습니다. 이 아키텍처는 VDSS의 요구 사항을 충족합니다. 

![Citrix SACA 다이어그램](media/citrixsaca.png)


Citrix 설명서 및 배포 스크립트를 찾을 수 있습니다 [여기 있습니다.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **F5 SACA 배포**

F5 키를 두 개의 서로 다른 아키텍처를 포함 하는 두 개의 별도 배포 템플릿을 만들었습니다. 첫 번째 활성-활성 고가용성 구성에서 F5 어플라이언스의 하나의 계층을 있습니다. 이 아키텍처는 VDSS에 대 한 요구 사항을 충족 합니다. 두 번째 활성-활성 고가용성 F5s 두 번째 계층을 추가합니다. 이 두 번째 계층의 목적은 고객 F5 계층 사이 F5에서 별도 자체 IP를 추가할 수 있도록 합니다. 일부 DoD 구성 요소 사용에 대 한 사전 설정 된 특정 IP를 갖습니다. 있는 경우 F5 어플라이언스의 단일 계층 작동에 대 한 가장 이후 아키텍처 F5 장치의 IP에 포함 합니다.  

![Citrix SACA 다이어그램](media/f5saca.png)

F5 설명서 및 배포 스크립트를 찾을 수 있습니다 [여기 있습니다.](https://github.com/f5devcentral/f5-azure-saca) 












