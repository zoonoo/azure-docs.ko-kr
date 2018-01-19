---
title: "시스템 통합 계획 고려 사항에 대 한 Azure 스택 | Microsoft Docs"
description: "이제 계획 하 고 다중 노드 Azure 스택에 대 한 준비를 수행할 수 있는 작업에 대해 알아봅니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: cd14f0e5259e5c0b6cbf11790bbdf08164267ffa
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>시스템 통합 계획 고려 사항에 대 한 Azure 스택
Azure 스택 통합 시스템에 관심이 있다면 배포 및 시스템 데이터 센터에 얼마나 적합 한지 주요 계획 고려 사항 중 일부를 이해 해야 합니다. 이 문서에서는 Azure 스택 multi-node 시스템에 대 한 중요 한 인프라 결정을 내릴 수 있도록 이러한 고려 사항에 대 한 고급 개요를 제공 합니다. 이러한 고려 사항에 대 한 이해가는 Azure 스택 데이터 센터에 배포할 때 OEM 하드웨어 공급 업체를 작업할 때 도움이 됩니다.  

> [!NOTE]
> Azure 스택 multi-node 시스템 인증된 한 하드웨어 공급 업체에서 구입할 수만 있습니다. 

Azure 스택 집합을 제대로 Azure 스택 환경으로 통합 해야 하는 의사 결정의를 배포 합니다. 배포를 신속 하 고 원활 하 게 이동 하는 프로세스를 시작 하기 전에 하드웨어 공급 업체에 대 한 준비 될 및 계획 과정 동안 솔루션 공급자에 게이 정보를 제공 해야 합니다.

정보는 네트워킹, 보안 및 기술에서 많은 다양 한 영역과 의사 결정권자 필요할 수 있는 많은 중요 한 결정으로 id 정보에서 범위를 필요 합니다. 따라서 배포를 시작 하기 전에 필요한 모든 정보를 준비를 갖출 있도록 조직에 여러 팀에서 사용자 가져오기 해야 합니다. 관련 결정 사항을 제공 하기 위해 유용한 권장 사항을 포함 될 수 하는 대로이 정보를 수집 하는 동안 하드웨어 공급 업체에 게 문의 하는 것이 유용 합니다.

연구 하 고 필요한 정보를 수집 하는 동안 네트워크 환경에 배포 전 구성을 변경 해야 합니다. 여기 Azure 스택 솔루션에 대 한 IP 주소 공간을 예약 하 고 라우터, 스위치 및 새 Azure 스택 솔루션 스위치에 대 한 연결을 준비 하는 방화벽 구성에 포함 될 수 있습니다. 계획 된 주제 영역 전문가 줄 맞춤 및 배포 프로젝트 전반에 걸쳐 관련가 있는지 확인 합니다.

## <a name="management-considerations"></a>관리 고려 사항
여기서는 인프라는 잠겨 있으며는 사용 권한에서 둘 다 azure 스택은 봉인 된 시스템 및 네트워크 관점입니다. 네트워크 액세스 제어 목록 (Acl) 허가 되지 않은 들어오는 모든 트래픽 및 인프라 구성 요소 간의 불필요 한 모든 통신을 차단에 적용 됩니다. 이렇게 하면 시스템에 액세스할 수 있는 권한이 없는 사용자가 어려워집니다.

일별 관리 및 작업에 대 한 인프라에 제한 되지 않은 관리자 액세스 권한 없음 있습니다. Azure 스택 연산자에는 시스템 관리자 포털을 통해 또는 Azure 리소스 관리자를 통해 Azure CLI, PowerShell 또는 REST API) (통해 관리 해야 합니다. Hyper-v 관리자 또는 장애 조치 클러스터 관리자와 같은 다른 관리 도구에서 시스템에 액세스할 수 없습니다 있습니다. 시스템을 보호 하기 위해 Azure 스택 인프라의 구성 요소 내부 제 3 자 소프트웨어 (예를 들어, 에이전트)를 설치할 수 없습니다. 외부 관리 및 보안 소프트웨어와의 상호 운용성 Azure CLI, PowerShell 또는 REST API를 통해 발생합니다.

경고 중재 단계를 통해 확인 되지 문제 해결을 위한 높은 수준의 액세스 필요 하면 지원을 사용 해야 합니다. 지원을 통해 된 고급 작업을 수행 하는 시스템에 대 한 임시 전체 관리자 액세스를 제공 하는 방법은 없습니다. 

## <a name="identity-considerations"></a>Identity 고려 사항

### <a name="choose-identity-provider"></a>Id 공급자를 선택 합니다.
Azure 스택 배포의 경우 Azure AD 또는 AD FS 사용 하려면 id 공급자를 고려해 야 합니다. 전체 시스템 재배포 하지 않고 배포 후 id 공급자를 전환할 수 없습니다.

Identity provider 선택한 테 넌 트 가상 컴퓨터, id 시스템 및 사용 하는 계정에는 영향을 주지 않습니다는 등 Active Directory 도메인에 참가 합니다. 이 별개입니다.

에 id 공급자를 선택 하는 방법에 대 한 자세히 알아볼 수 있습니다는 [Azure 스택 시스템 통합된 문서에 대 한 배포 결정 사항](.\azure-stack-deployment-decisions.md)합니다.

### <a name="ad-fs-and-graph-integration"></a>AD FS 및 그래프 통합
AD FS를 사용 하 여 id 공급자로 Azure 스택 배포 하려는 경우 Azure 스택에 AD FS 인스턴스 페더레이션 트러스트를 통해 기존 AD FS 인스턴스와 통합 해야 합니다. 따라서 기존 Active Directory 포리스트를 스택에서 Azure 리소스를 사용 하 여 인증에서 id 수 있습니다.

기존 Active Directory와 Azure 스택의 그래프 서비스를 통합할 수도 있습니다. 이렇게 하면 역할 기반 액세스 제어 (RBAC) Azure 스택의 관리할 수 있습니다. 리소스에 대 한 액세스 위임 되는 그래프 구성 요소 LDAP 프로토콜을 사용 하 여 기존 Active Directory 포리스트의 사용자 계정을 조회 합니다.

다음 다이어그램에서는 통합 된 AD FS 및 그래프 트래픽 흐름을 보여 줍니다.
![AD FS 및 그래프 트래픽 흐름을 보여 주는 다이어그램](media/azure-stack-deployment-planning/ADFSIntegration.PNG)

## <a name="licensing-model"></a>라이선스 모델

사용할 라이선스 모델을 결정 해야 합니다. 연결 된 배포에 대 한 사용량 기준 과금으로-있습니다-사용 또는 용량 기반 라이선스를 선택할 수 있습니다. 사용량 기준 과금-으로-있습니다-사용 필요 사용량을 보고, Azure 상거래를 통해 다음 청구는 Azure에 연결 합니다. 배포 하는 경우 지원 되는 용량 기반 라이선스 인터넷에서 연결이 끊어졌습니다. 라이선스 모델에 대 한 자세한 내용은 참조 [패키징 및 가격에 Microsoft Azure 스택](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다.

## <a name="naming-decisions"></a>명명 결정

Azure 스택 네임 스페이스, 특히 지역 이름 및 외부 도메인 이름 계획 하고자 하는 방법에 대해 생각 하는 것이 해야 합니다. 공용 끝점에 대 한 Azure 스택 배포의 정규화 된 도메인 이름 (FQDN)은 이러한 두 이름의 조합 &lt; *지역*&gt;&lt;*external_FQDN*  &gt;, 예를 들어 *east.cloud.fabrikam.com*합니다. 이 예제에서는 Azure 스택 포털 다음 Url에서 사용할 수 있는 합니다.

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

다음 표에서 이러한 도메인 명명 결정을 요약 합니다.

| 이름 | 설명 | 
| -------- | ------------- | 
|지역 이름 | 첫 번째 Azure 스택 영역의 이름입니다. 이 이름은 공용 가상 IP 주소 (Vip)를 Azure 스택에서 관리에 대 한 FQDN의 일부로 사용 됩니다. 일반적으로 지역 이름 데이터 센터 위치와 같은 물리적 위치 식별자 것입니다. | 
| 외부 도메인 이름 | 외부 연결 Vip 사용 하 여 끝점에 대 한 DNS 도메인 이름 () 영역의 이름입니다. 이러한 공용 Vip FQDN에 사용 됩니다. | 
| 개인 (내부) 도메인 이름 | 인프라 관리에 대 한 Azure 스택에 만든 도메인 (및 내부 DNS 영역)의 이름입니다. 
| | |

## <a name="certificate-requirements"></a>인증서 요구 사항

배포에 대 한 공용 끝점에 대 한 Secure Sockets Layer (SSL) 인증서를 제공 해야 합니다. 상위 수준에서 인증서에는 다음과 같은 요구 사항이 있습니다.

- 단일 와일드 카드 인증서를 사용할 수 있습니다 또는 전용된 인증서 집합을 사용 하 고 저장소 및 키 자격 증명 모음 등 끝점에 대 한 와일드 카드를 사용할 수 있습니다.
- 신뢰할 수 있는 공용 인증 기관 (CA)에서 인증서를 발급할 수 또는 고객이 관리 하는 CA입니다.

어떤 PKI에 대 한 자세한 내용은 얻을, 참조, 하는 방법과 Azure 스택 배포 위해 인증서가 필요한 [Azure 스택 공개 키 인프라 인증서 요구 사항](azure-stack-pki-certs.md)합니다.  


> [!IMPORTANT]
> 일반적인 지침으로 제공된 된 PKI 인증서 정보를 사용 해야 합니다. Azure 스택에 대 한 모든 PKI 인증서를 구입 하기 전에 OEM 하드웨어 파트너와 작동 합니다. 더 자세한 인증서 지침 및 요구 사항을 제공 됩니다.



## <a name="time-synchronization"></a>시간 동기화
서버를 사용 하 여 Azure 스택을 동기화 하는 특정 시간을 선택 해야 합니다.  시간 동기화는 서로 사용 하 여 내부 서비스를 인증 하는 데 사용 되는 Kerberos 티켓을 생성 하는 데 사용 된 인프라 역할 및 Azure 스택에 중요 한입니다.

IP 주소 인프라의 구성 요소 대부분에는 URL을 해결할 수, 있지만 지원할 수 시간 동기화 서버에 대 한 IP 지정 해야 합니다. 인 경우는 연결이 끊어진된 배포 옵션을 사용할 지정 해야 Azure 스택에 인프라 네트워크에서 연결할 수 있는지에 있는 회사 네트워크에서 시간 서버. 이 네트워크 통합 부분 배포 프로젝트를 계획 하는 동안 고려해 야가 필요할 수 있습니다.


## <a name="network-connectivity"></a>네트워크 연결
이 섹션에서는 중요 한 결정 하는 방법에 대 한 기존 네트워킹 환경에 Azure 스택을 통합 하는 데 도움이 되는 Azure 스택 네트워크 인프라 정보를 제공 합니다. 

> [!NOTE]
> Azure 스택 (예를 들어 www.bing.com)에서 외부 DNS 이름을 해결 하려면 Azure 스택 Azure 스택 권한을 보유 하지 않은 DNS 요청을 전달 하는 데 사용할 수 있는 DNS 서버를 제공 해야 합니다. Azure 스택 DNS에 대 한 자세한 내용은 요구 사항 참조, [스택 Azure 데이터 센터 통합 DNS](azure-stack-integrate-dns.md)합니다.

### <a name="physical-network-design"></a>실제 네트워크 디자인
Azure 스택 솔루션에는 해당 작업 및 서비스를 지원 하기 위해 탄력적이 고 항상 사용 가능한 실제 인프라가 필요 합니다. 다음은 권장된 설계에 대 한 다이어그램이입니다.

![권장 되는 스택 Azure 네트워크 디자인](media/azure-stack-deployment-planning/recommended-design.png)


### <a name="logical-networks"></a>논리 네트워크
논리 네트워크는 기본 실제 네트워크 인프라의 추상화를 나타냅니다. 구성 하 고 호스트, 가상 컴퓨터 및 서비스에 대 한 네트워크 할당을 간소화 하는 데 사용 됩니다. 논리 네트워크 만들기의 일부로 네트워크 사이트 가상 local area network (Vlan), IP 서브넷 및 각 실제 위치의 논리 네트워크와 연결 된 IP 서브넷/VLAN 쌍을 정의 하기 위해 만듭니다.
Azure 스택에 대 한 네트워크 인프라는 스위치에 구성 되어 있는 다음 논리 네트워크를 사용 합니다.

### <a name="network-infrastructure"></a>네트워크 인프라
Azure 스택에 대 한 네트워크 인프라는 스위치에 구성 된 여러 논리 네트워크 구성 됩니다. 다음 다이어그램에서는 이러한 논리 네트워크 및 베이스 보드 관리 컨트롤러 (BMC)는의-tor ()와 통합 하는 방법 (고객 네트워크) 스위치 테두리를 보여 줍니다.

![논리 네트워크 다이어그램 및 스위치 연결](media/azure-stack-deployment-planning/NetworkDiagram.png)

#### <a name="bmc-network"></a>BMC 네트워크
이 네트워크 모든 베이스 보드 관리 컨트롤러 (라고도 서비스 프로세서, 예를 들어 iDRAC, iLO, iBMC 등)를 연결 하는 전용 관리 네트워크입니다. 있는 경우에 하드웨어 수명 주기 호스트 (HLH)이이 네트워크에 배치 됩니다와 하드웨어 유지 관리 및/또는 모니터링에 대 한 OEM 특정 소프트웨어를 제공할 수 있습니다. 

#### <a name="private-network"></a>개인 네트워크
/이 24 (254 호스트 Ip)이 네트워크는 Azure 스택 영역 (Azure 스택 영역의 테두리 스위치 장치 보다 더 확장지 않습니다)에 전용 포트 이며 두 개의 서브넷으로 구분 됩니다.

- **저장소 네트워크**합니다. /25 (126 호스트 Ip) 저장소 공간 다이렉트 (S2D) 및 서버 메시지 블록 (SMB) 저장소 트래픽 및 가상 컴퓨터 실시간 마이그레이션을 사용 하는 데 사용 되는 네트워크입니다. 
- **내부 가상 IP 네트워크**합니다. A/25 소프트웨어 부하 분산 장치 (SLB)에 대 한 Vip를 내부 전용으로 전용된 네트워크입니다.

#### <a name="azure-stack-infrastructure-network"></a>Azure 스택 인프라 네트워크
이 통신 하 고 자체 데이터를 교환할 수 있도록 24 네트워크 내부 Azure 스택 구성 요소에 전용/입니다. 라우팅 가능한 IP 주소가 이어야 하는데는 비공개로 유지 솔루션에 대 한 액세스 제어 목록 (Acl)를 사용 하 여이 서브넷입니다.  테두리 스위치와 동일한 크기는 / 27 매우 작은 범위를 제외 하 고 다음 라우팅 것으로 예상 되지 않습니다는 외부 리소스 및/또는 인터넷에 대 한 액세스를 필요로 할 때 이러한 서비스 중에서 사용 된 네트워크입니다. 

#### <a name="public-infrastructure-network"></a>공용 인프라 네트워크
이/27 네트워크는 앞에서 언급 한 Azure 스택 인프라 서브넷에서 매우 작은 범위, 공용 IP 주소 필요 하지 않습니다 하지만 NAT 또는 투명 프록시를 통한 인터넷 액세스 필요지 않습니다. 이 네트워크는 Emergency 복구 콘솔 시스템 (ERCS)에 대 한 할당 됩니다. ERCS VM Azure에 등록 하는 동안 인터넷 액세스 필요 하 고 문제 해결을 위해 관리 네트워크에 라우팅할 수 있어야 합니다.

#### <a name="public-vip-network"></a>공용 VIP 네트워크
공용 VIP 네트워크 스택에서 Azure 네트워크 컨트롤러에 할당 됩니다. 스위치는 논리 네트워크는 없습니다. SLB 주소 풀을 사용 하 고 할당/32 테 넌 트 작업에 대 한 네트워크. 스위치 라우팅 테이블에서 이러한 32 Ip는 BGP 통해 사용할 수 있는 경로를 보급 합니다. 이 네트워크 외부에서 액세스할 수 있는 또는 공용 IP 주소를 포함합니다. Azure 스택 인프라는 나머지 테 넌 트 Vm에서 사용 하는 동안이 공용 VIP 네트워크에서 최소한 8 주소를 사용 합니다. 이 서브넷의 네트워크 크기의 /26 (64 호스트)의 최소/22 (1022 호스트)의 최대 범위, / 24를 계획 하는 것이 좋습니다 네트워크입니다.

#### <a name="switch-infrastructure-network"></a>인프라 네트워크 스위치
이/26 네트워크 라우팅할 수 있는 지점 간 IP/30 (2 호스트 IP의) 서브넷 및 전용는 루프백이 포함 된 서브넷은 / 대역에 대 한 32 서브넷 전환 관리 및 BGP 라우터 id입니다. 이 IP 주소 범위, 데이터 센터에 Azure 스택 솔루션의 외부에서 라우팅할 수 있어야 하 고 개인 또는 공용 Ip 수 있습니다. 예를 들어 시나리오 내에서 다중 테 넌 트 서비스 공급자 공용 Ip는 밀접 하 게 제어 되는 엔터프라이즈 배포 내에서 하는 동안 필요할 수 있습니다, 개인 Ip 적합할 수 있습니다.

#### <a name="switch-management-network"></a>관리 네트워크 스위치
이/29 (6 호스트 Ip) 네트워크 스위치 관리 포트를 연결 하는 전용입니다. 배포, 관리 및 문제 해결에 대 한 대역폭을 벗어난 액세스 수 있습니다. 위에서 언급 한 스위치 인프라 네트워크에서 계산 됩니다.

### <a name="transparent-proxy"></a>투명 프록시
Azure 스택 솔루션 일반 웹 프록시를 지원 하지 않습니다. 데이터 센터 모든 트래픽이 프록시를 사용 하도록이 필요한 경우 구성 해야 정책에 따라 처리 하는 랙에서 모든 트래픽을 처리 하는 투명 프록시 네트워크의 영역 간에 트래픽을 분리 합니다. 투명 프록시 (라고도 가로채기, 인라인 또는 강제 프록시) 특수 한 클라이언트 구성 없이 네트워크 계층에서 일반적인 통신을 차단 합니다. 클라이언트가 필요한 프록시 있음을 알 수 없습니다.

### <a name="publish-azure-stack-services"></a>Azure 스택 서비스 게시

Azure 스택 서비스 사용할 수 있도록 사용자에 게 외부 Azure 스택에서 해야 합니다. Azure 스택 인프라 역할에 대 한 다양 한 끝점을 설정합니다. 이러한 끝점 공용 IP 주소 풀에서 Vip가 할당 됩니다. 외부 DNS 영역 배포 시에 지정 된 각 끝점에 대 한 DNS 항목이 생성 됩니다. 예를 들어 사용자 포털 포털의 DNS 호스트 항목을 할당 됩니다.  *&lt;지역 >.&lt; fqdn >*합니다.

#### <a name="ports-and-urls"></a>포트 및 Url

Azure 스택 서비스를 확인 하려면 (포털, Azure 리소스 관리자, DNS, 같은 등) 외부 네트워크에 사용할 수 있는 허용 해야 이러한 끝점에 인바운드 트래픽을 특정 Url, 포트 및 프로토콜에 대 한 합니다.
 
배포에 있는 기존 프록시 서버에 투명 프록시 업링크 허용 해야 특정 포트 및 Url 아웃 바운드 통신에 대 한 합니다. 여기 identity, 마켓플레이스 신디케이션, 패치 및 업데이트, 등록 및 사용 현황 데이터에 대 한 포트 및 Url이 포함 됩니다.

자세한 내용은 다음을 참조하세요.
- [인바운드 포트 및 프로토콜](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [아웃 바운드 포트 및 Url](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Azure 스택 Azure에 연결

하이브리드 클라우드 시나리오에 대 한 Azure 스택 Azure에 연결 하려는 방법을 계획 해야 합니다. Azure에서 가상 네트워크를 Azure 스택에서 가상 네트워크를 연결 하는 데 지원 되는 두 방법에는 
- **사이트 간**합니다. IPsec (IKE v1 및 IKE v2)을 통한 가상 사설망 (VPN) 연결 합니다. 이러한 종류의 연결에는 VPN 장치 또는 라우팅 및 원격 액세스 서비스 (RRAS) 필요합니다. Azure에서 VPN 게이트웨이에 대 한 자세한 내용은 참조 하십시오. [에 대 한 VPN 게이트웨이](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)합니다. 이 터널을 통해 통신 암호화 및 안전 합니다. 그러나 대역폭 (100-200 Mbps) 터널의 최대 처리량으로 제한 됩니다.
- **아웃 바운드 NAT**합니다. 기본적으로 모든 가상 컴퓨터가 Azure 스택 갖습니다 아웃 바운드 NAT. 통해 외부 네트워크에 연결 Azure 스택에서 생성 된 각 가상 네트워크에 할당 된 공용 IP 주소를 가져옵니다. 가상 컴퓨터에 직접 할당 된 공용 IP 주소 또는 공용 IP 주소와 부하 분산 장치 뒤를 통해 아웃 바운드 NAT 가상 네트워크의 VIP를 사용 하 여 아웃 바운드 액세스를 갖습니다. 이 가상 컴퓨터에 의해 시작 되 고 외부 네트워크 (인터넷 또는 인트라넷)에 대해 대상이 지정 되었으며 통신에만 작동 합니다. 외부에서 가상 컴퓨터와 통신 하는 사용할 수 없습니다.

#### <a name="hybrid-connectivity-options"></a>하이브리드 연결 옵션

하이브리드 연결을 제공 하려는 어떤 종류의 배포와 배포 될 고려 하는 합니다. 테 넌 트 당 네트워크 트래픽을 격리 해야 하는지 여부 및 인트라넷 또는 인터넷 배포 해야 하는지 여부를 고려해 야 할 필요 합니다.

- **단일 테 넌 트 Azure 스택**합니다. Azure 스택 배포 보이는, 적어도 네트워킹 측면에서 단일 테 넌 트 말 이죠. 구독, 테 넌 트 많은 될 수 있지만 인트라넷 서비스와 마찬가지로 모든 트래픽이 동일한 네트워크를 통해 이동 합니다. 구독 간에 네트워크 트래픽을 다른 구독으로 동일한 네트워크 연결을 통해 이동 하 고 암호화 된 터널을 통해 격리 될 필요가 없습니다.

- **다중 테 넌 트 Azure 스택**합니다. Azure 스택 배포는 Azure 스택 외부에 있는 네트워크에 대 한 바인딩된 각 테 넌 트 구독 트래픽이 다른 테 넌 트의 네트워크 트래픽으로부터 격리 되어야 합니다.
 
- **인트라넷 배포**합니다. 하나 이상의 방화벽으로 보호 하 고 일반적으로 개인 IP 주소 공간에 회사 인트라넷에 있는 Azure 스택 배포 합니다. 공용 IP 주소 진정으로 공개 하지은 공용 인터넷을 통해 직접 라우팅할 수 없기 때문입니다.

- **인터넷 배포**합니다. 공용 VIP 범위에 대 한 공용에 연결 된 Azure 스택 배포 인터넷 라우팅 가능한 공용 IP를 사용 하 여 인터넷을 해결 합니다. 배포 방화벽 뒤에 계속 배치 될 수 있습니다 하지만 공용 VIP 범위는 공용 인터넷 및 Azure에서 직접 연결할 수 있습니다.
 
다음 표에서 장점과 단점, 사용 사례와 하이브리드 연결 시나리오를 요약 합니다.

| 시나리오 | 연결 방법 | 장점 | 단점 | 에 적합 합니다. |
| -- | -- | --| -- | --|
| 단일 테 넌 트 Azure 스택, 인트라넷 배포 | 아웃 바운드 NAT | 더 빠른 전송에 대 한 향상 된 대역폭입니다. 간편한 구현 합니다. 게이트웨이가 필요 없습니다. | 트래픽 암호화 되지 않습니다. 격리 없음 또는 TOR 다음 암호화 합니다. | 엔터프라이즈 배포의 모든 테 넌 트에 동일 하 게 신뢰할 수 있는 위치입니다.<br><br>Azure에 Azure ExpressRoute 회로 있는 기업을 합니다. |
| 인트라넷 배포의 다중 테 넌 트 Azure 스택 | 사이트 간 VPN | 대상 테 넌 트 VNet의에서 트래픽은 보안입니다. | 사이트 간 VPN 터널 대역폭 제한 됩니다.<br><br>가상 네트워크와 대상 네트워크 VPN 장치에 게이트웨이가 필요합니다. | 여기서 일부 테 넌 트 트래픽 엔터프라이즈 배포는 다른 테 넌 트 로부터 보호 되어야 합니다. |
| 단일 테 넌 트 Azure 스택, 인터넷 배포 | 아웃 바운드 NAT | 더 빠른 전송에 대 한 향상 된 대역폭입니다. | 트래픽 암호화 되지 않습니다. 격리 없음 또는 TOR 다음 암호화 합니다. | 호스팅 시나리오 테 넌 트 자신의 Azure 스택 배포 및 Azure 스택 환경에 전용된 회로 가져오는 위치입니다. 예를 들어 ExpressRoute 및 다중 프로토콜 레이블 스위칭 (MPLS).
| 다중 테 넌 트 Azure 스택, 인터넷 배포 | 사이트 간 VPN | 대상 테 넌 트 VNet의에서 트래픽은 보안입니다. | 사이트 간 VPN 터널 대역폭 제한 됩니다.<br><br>가상 네트워크와 대상 네트워크 VPN 장치에 게이트웨이가 필요합니다. | 하려는 시나리오에서 공급자는 다중 테 넌 트 클라우드 제공할 호스팅, 서로 캡슐화 된 트래픽과 테 넌 트를 신뢰 하지 않는 암호화 되어야 합니다.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>ExpressRoute 사용

Azure 스택을 통해 Azure에 연결할 수 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 인트라넷 단일 테 넌 트 및 다중 테 넌 트 시나리오 둘 다에 대 한 합니다. 통해 프로 비전 된 ExpressRoute 회로 해야 [연결 공급자](https://docs.microsoft.com/azure/expressroute/expressroute-locations)합니다.

다음 다이어그램에서는 단일 테 넌 트 시나리오에 대 한 express 경로 보여 줍니다. (여기서 "고객의 연결을" express 경로 회로).

![표시 된 단일 테 넌 트 ExpressRoute 시나리오를 다이어그램합니다](media/azure-stack-deployment-planning/ExpressRouteSingleTenant.PNG)

다음 다이어그램에서는 다중 테 넌 트 시나리오에 대 한 express 경로 보여 줍니다.

![다중 테 넌 트 ExpressRoute 시나리오를 보여 주는 다이어그램](media/azure-stack-deployment-planning/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>외부 모니터링
Azure 스택 배포 및 장치에서 모든 경고의 단일 뷰에서 하 고 티켓에 대 한 기존 IT 서비스 관리 워크플로에 경고를 통합 솔루션을 모니터링 하는 외부 데이터 센터와 Azure 스택을 통합할 수 있습니다.

Azure 스택 솔루션에 포함 된, 하드웨어 수명 주기 호스트 (HLH)는 하드웨어에 대 한 OEM 공급 업체에서 제공 하는 관리 도구를 실행 하는 Azure 스택 외부 컴퓨터. 이러한 도구 또는 데이터 센터에서 기존 모니터링 솔루션 직접 통합 하는 다른 솔루션을 사용할 수 있습니다.

다음 표에서 현재 사용할 수 있는 옵션 목록이 요약 되어 있습니다.

| 영역 | 외부 모니터링 솔루션 |
| -- | -- |
| Azure 스택 소프트웨어 | - [Operations Manager 용 관리 팩 azure 스택](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios 플러그 인](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-REST 기반 API 호출 | 
| 물리적 서버 (IPMI 통해 Bmc) | -Operations Manager 공급 업체 관리 팩<br>OEM 하드웨어 공급 업체에서 제공한 솔루션<br>-하드웨어 공급 업체 Nagios 플러그 인 | OEM 파트너 지원 모니터링 솔루션 (포함) | 
| 네트워크 장치에서는 SNMP) | 과 operations Manager 네트워크 장치 검색<br>OEM 하드웨어 공급 업체에서 제공한 솔루션<br>-플러그 인 Nagios 스위치 |
| 테 넌 트 구독 상태 모니터링 | - [Windows Azure 용 system Center 관리 팩](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

다음 요구 사항에 유의하세요.
- 사용 하면 솔루션은 에이전트 없는 이어야 합니다. Azure 스택 구성 요소 안의 제 3 자 에이전트를 설치할 수 없습니다. 
- System Center Operations Manager를 사용 하려는 경우이 Operations Manager 2012 R2 또는 Operations Manager 2016 필요 합니다.

## <a name="backup-and-disaster-recovery"></a>Backup 및 재해 복구

계획 인프라에 대해 모두 기본 스택 Azure IaaS 가상 컴퓨터 및 PaaS 서비스를 호스팅하는 및 테 넌 트 응용 프로그램 및 데이터에 대 한 백업 및 재해 복구 계획 작업이 포함 됩니다. 계획 해야 이러한 별도로.

### <a name="protect-infrastructure-components"></a>인프라 구성 요소를 보호 합니다.

Azure 스택 인프라 구성 요소를 지정 하는 공유에 백업 합니다.

- 외부 SMB 파일 공유 기존의 Windows 기반 파일 서버 또는 제 3 자 장치에 필요 합니다.
- 네트워크 스위치와 하드웨어 수명 주기 호스트의 백업에 대 한이 동일한 공유를 사용 해야 합니다. OEM 하드웨어 공급 업체는 이러한 방화벽은 Azure 스택에 외부 백업 및 복원 구성 요소에 대 한 지침을 제공 하는 데 도움이 됩니다. 넌 OEM 공급 업체의 권장 사항을 기반으로 하는 백업 워크플로 실행 합니다.

예: 배포 입력과 식별자, 서비스 계정, CA 루트 인증서, 페더레이션된 리소스 (연결이 끊어진된 배포의 경우), 계획, 제안, reseed 배포 데이터에 대 한 인프라 백업 치명적인 데이터 손실을 발생 하는 경우 사용할 수 있습니다. 구독, 할당량, RBAC 정책 및 역할 할당 및 주요 자격 증명 모음 암호입니다.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>IaaS 가상 컴퓨터에서 테 넌 트 응용 프로그램을 보호

Azure 스택 백업 되지 않습니다 테 넌 트 응용 프로그램 및 데이터를 합니다. 에 대 한 백업 및 재해 복구 보호는 대상 외부 Azure 스택에 계획 해야 합니다. 테 넌 트 보호는 테 넌 트 기반 작업입니다. 테 넌 트 IaaS 가상 컴퓨터에 대 한 파일 폴더, 응용 프로그램 데이터 및 시스템 상태를 보호 하기 위해 게스트 기술을 사용할 수 있습니다. 그러나 엔터프라이즈 또는 서비스 공급 업체는 동일한 데이터 센터 내부 또는 외부 클라우드에 백업 및 복구 솔루션을 제공 수 있습니다.

Linux 또는 Windows IaaS 가상 컴퓨터를 백업 하려면 파일, 폴더, 운영 체제 상태 및 응용 프로그램 데이터를 보호 하기 위해 게스트 운영 체제에 액세스할 수 있는 백업 제품을 사용 해야 있습니다. 지원 되는 타사 제품 또는 Azure 백업으로 System Center 데이터 센터 Protection Manager를 사용할 수 있습니다.

데이터를 보조 위치에 복제 하 고 재해가 발생할 경우 응용 프로그램 장애 조치를 조정를 Azure Site Recovery 또는 지원 되는 타사 제품을 사용할 수 있습니다. (통합형된 시스템의 초기 릴리스에서 Azure Site Recovery을 지원 하지 않습니다 장애 복구 합니다. 그러나를 얻을 수 있습니다는 수동 프로세스를 통해 장애 복구 합니다.) 또한 네이티브 복제 (예: Microsoft SQL Server)를 지 원하는 응용 프로그램은 응용 프로그램이 실행 되 고 있는 다른 위치로 데이터를 복제할 수 있습니다.

> [!IMPORTANT]
> 통합된 시스템의 초기 릴리스에서 IaaS 가상 컴퓨터의 게스트 수준에서 작동 하는 보호 기술 지원 합니다. 기본 인프라 서버에 에이전트를 설치할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 정보를 참조 하십시오.는 [Azure 스택](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지.
- Azure 스택에 대 한 로드맵 및 지리적 가용성에 대 한 정보에 대 한 통합된 시스템 백서를 참조: [Azure 스택: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 
