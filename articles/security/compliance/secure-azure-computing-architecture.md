---
title: Azure 컴퓨팅 아키텍처 보안
description: 엔터프라이즈 수준 DMZ 아키텍처의이 참조 아키텍처는 네트워크 가상 어플라이언스 및 기타 도구를 사용 합니다. 이 아키텍처는 방어의 안전한 클라우드 컴퓨팅 아키텍처 기능 요구 사항을 충족 하도록 설계 되었습니다. 모든 조직에도 사용할 수 있습니다. 이 참조는 Citrix 또는 F5 어플라이언스를 사용 하는 두 가지 자동화 된 옵션을 포함 합니다.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 3a27eac3d4609f1054b0ef6a9417fe2f1ca53ae4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656636"
---
# <a name="secure-azure-computing-architecture"></a>Azure 컴퓨팅 아키텍처 보안

미국 Azure에 작업을 배포 하는 DoD (학과) 고객은 보안 가상 네트워크를 설정 하 고 DoD 표준 및 관행으로 규정 된 된 보안 도구 및 서비스를 구성 하는 방법에 대 한 지침을 요청 받았습니다. 

DISA (방어 정보 시스템 에이전시)는 2017의 [SCCA (Secure Cloud 컴퓨팅 아키텍처) FRD (기능 요구 사항 문서](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) )를 게시 했습니다. SCCA는 DISN (방어 정보 시스템 네트워크) 및 상업적 클라우드 공급자 연결 지점의 보안을 위한 기능 목표를 설명 합니다. 또한 SCCA는 업무 소유자가 연결 경계에서 클라우드 응용 프로그램을 보호 하는 방법을 설명 합니다. 상용 클라우드에 연결 되는 모든 DoD 엔터티는 SCCA FRD에 설정 된 지침을 따라야 합니다.
 
SCCA에는 다음 네 가지 구성 요소가 있습니다.
 
- 경계 클라우드 액세스 지점 (BCAP)
- VDSS (가상 데이터 센터 보안 스택)
- VDMS (Virtual Datacenter 관리 서비스)
- TCCM (신뢰할 수 있는 클라우드 자격 증명 관리자) 

Microsoft는 Azure에서 실행 되는 IL4 및 IL5 작업에 대 한 SCCA 요구 사항을 충족 하는 솔루션을 개발 했습니다. 이 Azure 특정 솔루션을 SACA (Secure Azure 컴퓨팅 아키텍처) 라고 합니다. SACA를 배포 하는 고객은 SCCA FRD를 준수 합니다. 이를 통해 DoD 고객이 연결 된 후에 워크 로드를 Azure로 이동할 수 있습니다.

SCCA 지침 및 아키텍처는 DoD 고객과 관련이 있지만 SACA help civilian 고객에 대 한 최신 수정 사항은 3 목 (신뢰할 수 있는 인터넷 연결) 지침을 준수 합니다. 최신 수정 버전은 Azure 환경을 보호 하기 위해 안전한 DMZ를 구현 하고자 하는 상용 고객 에게도 도움을 줍니다.


## <a name="secure-cloud-computing-architecture-components"></a>안전한 클라우드 컴퓨팅 아키텍처 구성 요소

### <a name="bcap"></a>BCAP

BCAP의 목적은 클라우드 환경에서 발생 하는 공격 으로부터 DISN을 보호 하는 것입니다. BCAP는 침입 감지 및 방지를 수행 합니다. 또한 권한이 없는 트래픽도 필터링 합니다. 이 구성 요소는 SCCA의 다른 구성 요소와 함께 배치할 수 있습니다. 실제 하드웨어를 사용 하 여이 구성 요소를 배포 하는 것이 좋습니다. 다음 표에는 BCAP 보안 요구 사항이 정리 되어 있습니다.

#### <a name="bcap-security-requirements"></a>BCAP 보안 요구 사항

![BCAP 요구 사항 매트릭스](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

VDSS의 목적은 Azure에서 호스트 되는 DoD 업무 소유자 응용 프로그램을 보호 하는 것입니다. VDSS는 SCCA에서 대부분의 보안 작업을 수행 합니다. Azure에서 실행 되는 응용 프로그램을 보호 하기 위해 트래픽 검사를 수행 합니다. 이 구성 요소는 Azure 환경 내에서 제공 될 수 있습니다.

#### <a name="vdss-security-requirements"></a>VDSS 보안 요구 사항

![VDSS 요구 사항 매트릭스](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDMS의 목적은 호스트 보안 및 공유 데이터 센터 서비스를 제공 하는 것입니다. VDMS의 함수는 SCCA의 허브에서 실행 하거나, 업무 소유자가 자신의 특정 Azure 구독에이를 배포할 수 있습니다. 이 구성 요소는 Azure 환경 내에서 제공 될 수 있습니다.

#### <a name="vdms-security-requirements"></a>VDMS 보안 요구 사항

![VDMS 요구 사항 매트릭스](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM은 비즈니스 역할입니다. 이 개인은 SCCA를 관리 하는 일을 담당 합니다. 해당 의무는 다음과 같습니다. 

- 클라우드 환경에 대 한 계정 액세스에 대 한 계획 및 정책을 설정 합니다. 
- Id 및 액세스 관리가 제대로 작동 하는지 확인 합니다. 
- 클라우드 자격 증명 관리 계획을 유지 관리 합니다. 

이 개인은 권한 부여 공식에 의해 부여 됩니다. BCAP, VDSS 및 VDMS는 TCCM에서 작업을 수행 하는 데 필요한 기능을 제공 합니다.

#### <a name="tccm-security-requirements"></a>TCCM 보안 요구 사항

![TCCM 요구 사항 매트릭스](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA 구성 요소 및 계획 고려 사항 

SACA 참조 아키텍처는 Azure에서 VDSS 및 VDMS 구성 요소를 배포 하 고 TCCM을 사용 하도록 설정 하기 위한 것입니다. 이 아키텍처는 모듈식입니다. VDSS 및 VDMS의 모든 부분은 중앙 허브에 존재할 수 있습니다. 일부 컨트롤은 임무 소유자 공간에서 또는 온-프레미스 에서도 충족 될 수 있습니다. 모든 임무 소유자가 연결할 수 있는 중앙 가상 네트워크에 VDSS 및 VDMS 구성 요소를 함께 배치 하는 것이 좋습니다. 다음 다이어그램은이 아키텍처를 보여 줍니다. 


![SACA 참조 아키텍처 다이어그램](media/sacav2generic.png)

SCCA 준수 전략 및 기술 아키텍처를 계획 하는 경우 처음부터 모든 고객에 게 영향을 주므로 다음 항목을 고려 하십시오. 다음 문제는 DoD 고객과 함께 제공 되며 계획 및 실행 속도를 저하 시키는 경향이 있습니다. 

#### <a name="which-bcap-will-your-organization-use"></a>조직에서 어떤 BCAP를 사용 하나요?
   - DISA BCAP:
        - DISA의 오각형과 캠프에는 두 가지 작동 BCAPs가 있습니다. 세 번째는 곧 온라인 상태로 전환 될 예정입니다. 
        - DISA의 BCAPs는 모두 azure에 대 한 Azure Express 경로 회로를 포함 하며,이는 DoD 고객이 연결에 사용할 수 있습니다. 
        - DISA에는 Office 365와 같은 Microsoft SaaS (software as a service) 도구를 구독 하려는 DoD 고객용 엔터프라이즈 수준 Microsoft 피어 링 세션이 있습니다. DISA BCAP를 사용 하 여 SACA 인스턴스로 연결 및 피어 링을 사용 하도록 설정할 수 있습니다. 
    - 사용자 고유의 BCAP 빌드:
        - 이 옵션을 사용 하려면 공동 배치 된 데이터 센터의 공간을 임대 하 고 Azure에 대 한 Express 경로 회로를 설정 해야 합니다. 
        - 이 옵션을 사용 하려면 추가 승인이 필요 합니다. 
        - 추가 승인 및 물리적 빌드 때문에이 옵션은 가장 많은 시간이 걸립니다. 
    - DISA BCAP를 사용 하는 것이 좋습니다. 이 옵션은 바로 사용할 수 있으며, 중복성이 기본 제공 되며, 현재 프로덕션 환경에서 작동 하는 고객을 포함 합니다.
- DoD 라우팅 가능한 IP 공간:
    - Edge에서 DoD 라우팅 가능한 IP 공간을 사용 해야 합니다. NAT를 사용 하 여 이러한 공간을 Azure의 개인 IP 공간에 연결 하는 옵션을 사용할 수 있습니다.
    - IP 공간을 얻으려면 DoD NIC (네트워크 정보 센터)에 문의 하세요. DISA를 사용 하 여 시스템/네트워크 승인 프로세스 (SNAP) 제출의 일부로 필요 합니다. 
    - NAT를 사용 하 여 Azure에서 개인 주소 공간을 연결 하려는 경우 SACA를 배포 하려는 각 지역에 대해 NIC에서 할당 된 주소 공간의 최소/24 서브넷이 필요 합니다.
- 중복성:
    - 최소 두 개 이상의 지역에 SACA 인스턴스를 배포 합니다. DoD 클라우드에서 사용 가능한 DoD 지역에 모두 배포 합니다.
    - 별도의 Express 경로 회로를 통해 둘 이상의 BCAPs에 연결 합니다. 그런 다음 두 Express 경로 연결을 각 지역의 SACA 인스턴스에 연결할 수 있습니다. 
- DoD 구성 요소별 요구 사항:
    - 조직에 SCCA 요구 사항 외에 특정 요구 사항이 있나요? 일부 조직에는 특정 IP 요구 사항이 있습니다.
- SACA는 모듈식 아키텍처입니다.
    - 사용자 환경에 필요한 구성 요소만 사용 합니다. 
        - 단일 계층 또는 다중 계층에 네트워크 가상 어플라이언스를 배포 합니다.
        - 통합 IP를 사용 하거나 사용자 고유의 IP를 가져옵니다.
- 응용 프로그램 및 데이터의 DoD 영향 수준:
    - Microsoft IL5 지역에서 실행 되는 응용 프로그램의 가능성이 있는 경우 IL5에서 SACA 인스턴스를 빌드합니다. IL4 응용 프로그램 및 IL5 앞에서 인스턴스를 사용할 수 있습니다. IL5 응용 프로그램 앞에 있는 IL4 SACA 인스턴스는 인정을 받지 못할 가능성이 높습니다.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>VDSS에 사용할 네트워크 가상 어플라이언스 공급 업체는 무엇 인가요?
앞서 설명한 것 처럼 다양 한 어플라이언스 및 Azure 서비스를 사용 하 여이 SACA 참조를 빌드할 수 있습니다. Microsoft는 F5와 Citrix를 모두 사용 하 여 SACA 아키텍처를 배포 하는 자동화 된 솔루션 템플릿을 보유 하 고 있습니다. 이러한 솔루션은 다음 섹션에서 설명 합니다.

#### <a name="which-azure-services-will-you-use"></a>어떤 Azure 서비스를 사용 하 시겠습니까?
- Log analytics, 호스트 기반 보호 및 ID 기능에 대 한 요구 사항을 충족할 수 있는 Azure 서비스가 있습니다. 일부 서비스는 Microsoft IL5 지역에서 일반적으로 제공 되지 않을 수 있습니다. 이 경우 이러한 Azure 서비스가 요구 사항을 충족할 수 없는 경우 타사 도구를 사용 해야 할 수 있습니다. 익숙한 도구와 Azure 네이티브 도구를 사용할 수 있는 가능성을 확인 하세요.
- 가능한 한 많은 Azure native tools를 사용 하는 것이 좋습니다. 클라우드 보안을 염두에 두고 구축 되었으며, 나머지 Azure 플랫폼과 원활 하 게 통합 됩니다. 다음 목록의 Azure native tools를 사용 하 여 SCCA의 다양 한 요구 사항을 충족 합니다.

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
    - 크기 조정 연습을 완료 해야 합니다. SACA 인스턴스 및 네트워크 처리량 요구 사항이 있을 수 있는 동시 연결 수를 확인 합니다. 
    - 이 단계는 중요 합니다. Vm의 크기를 조정 하 고 SACA 인스턴스에서 사용 하는 다양 한 공급 업체에서 필요한 라이선스를 식별 하는 데 도움이 됩니다. 
    - 이 크기 조정 연습 없이 좋은 비용 분석을 수행할 수 없습니다. 크기를 조정 하면 최상의 성능을 사용할 수도 있습니다. 


## <a name="most-common-deployment-scenario"></a>가장 일반적인 배포 시나리오

 여러 Microsoft 고객이 전체 배포를 진행 하거나 최소한 SACA 환경의 계획 단계를 완료 했습니다. 경험에 따르면 가장 일반적인 배포 시나리오에 대 한 통찰력을 얻을 것입니다. 다음 다이어그램은 가장 일반적인 아키텍처를 보여 줍니다. 


![SACA 참조 아키텍처 다이어그램](media/sacav2commonscenario.png) 


다이어그램에서 볼 수 있듯이 DoD 고객은 일반적으로 DISA BCAPs 중 두 개를 구독 합니다. 그 중 하나는 서 부 해안이 고 다른 하나는 동부 해안에 있습니다. Express 경로 개인 피어는 각 DISA BCAP 위치에서 Azure에 사용할 수 있습니다. 그런 다음 이러한 Express 경로 피어는 DoD 동부 및 DoD 중앙 Azure 지역의 가상 네트워크 게이트웨이에 연결 됩니다. SACA 인스턴스는 DoD 동부 및 DoD 중앙 Azure 지역에 배포 됩니다. 모든 수신 및 송신 트래픽은이를 통해 Express 경로 연결에서 DISA BCAP로 흐릅니다.

그런 다음, 업무 소유자 응용 프로그램은 응용 프로그램을 배포 하려는 Azure 지역을 선택 합니다. 가상 네트워크 피어 링을 사용 하 여 응용 프로그램의 가상 네트워크를 SACA 가상 네트워크에 연결 합니다. 그런 다음 VDSS 인스턴스를 통해 모든 트래픽을 강제로 터널링 합니다.

이 아키텍처는 SCCA 요구 사항을 충족 하기 때문에 권장 됩니다. 가용성이 뛰어나고 쉽게 확장할 수 있습니다. 또한 배포 및 관리를 간소화 합니다.

## <a name="automated-saca-deployment-options"></a>자동화 된 SACA 배포 옵션

 앞서 언급 했 듯이 Microsoft는 자동화 된 SACA 인프라 템플릿을 만들기 위해 두 공급 업체와 협력 하 고 있습니다. 두 템플릿 모두 다음 Azure 구성 요소를 배포 합니다. 

- SACA 가상 네트워크
    - 관리 서브넷
        - 이 서브넷은 관리 Vm 및 서비스가 배포 되는 위치입니다 (점프 상자 라고도 함).
        - VDMS 서브넷
            - 이 서브넷은 VDMS에 사용 되는 Vm과 서비스를 배포 하는 위치입니다.
        - 신뢰할 수 없는 서브넷 및 신뢰할 수 있는 서브넷
            - 이러한 서브넷에는 가상 어플라이언스를 배포 합니다.
        - 게이트웨이 서브넷
            - 이 서브넷은 Express 경로 게이트웨이가 배포 되는 위치입니다.
- 관리 점프 상자 가상 컴퓨터
    - 이는 환경의 대역 외 관리에 사용 됩니다.
- 네트워크 가상 어플라이언스
    - 배포 하는 템플릿에 따라 Citrix 또는 F5 키를 사용 합니다.
- 공용 IP
    - Express 경로를 온라인으로 전환할 때까지 프런트 엔드에 사용 됩니다. 이러한 Ip는 백 엔드 Azure 개인 주소 공간으로 변환 됩니다.
- 경로 테이블 
    - 자동화 하는 동안 적용 된 이러한 경로 테이블은 가상 어플라이언스를 통해 모든 트래픽을 강제로 터널링 합니다.
- Azure 부하 분산 장치-표준 SKU
    - 이러한 장치는 어플라이언스 간에 트래픽 부하를 분산 하는 데 사용 됩니다.
- 네트워크 보안 그룹
    - 특정 끝점으로 이동할 수 있는 트래픽 유형을 제어 하는 데 사용 됩니다.


### <a name="citrix-saca-deployment"></a>Citrix SACA 배포

Citrix 배포 템플릿은 항상 사용 가능한 Citrix ADC 어플라이언스의 두 계층을 배포 합니다. 이 아키텍처는 VDSS의 요구 사항을 충족 합니다. 

![Citrix SACA 다이어그램](media/citrixsaca.png)


Citrix 설명서 및 배포 스크립트는 [이 GitHub 링크](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)를 참조 하세요.


 ### <a name="f5-saca-deployment"></a>F5 SACA 배포

별도의 두 가지 F5 배포 템플릿에는 두 가지 아키텍처가 포함 됩니다. 첫 번째 템플릿에는 활성-활성 항상 사용 가능한 구성의 F5 어플라이언스 계층이 하나 뿐입니다. 이 아키텍처는 VDSS의 요구 사항을 충족 합니다. 두 번째 템플릿은 활성-활성 항상 사용 가능한 F5s의 두 번째 계층을 추가 합니다. 이 두 번째 계층을 사용 하면 사용자가 f5 계층 사이에서 f 5와 별도의 고유한 IP를 추가할 수 있습니다. 모든 DoD 구성 요소에서 사용 하도록 지정 된 특정 IP를 사용 하는 것은 아닙니다. 해당 하는 경우에는 f5 기기의 단일 계층이 대부분에 대해 작동 합니다. 해당 아키텍처는 F5 장치에 IP를 포함 하기 때문입니다.

![F5 SACA 다이어그램](media/f5saca.png)

F5 설명서 및 배포 스크립트는 [이 GitHub 링크](https://github.com/f5devcentral/f5-azure-saca)를 참조 하세요.












