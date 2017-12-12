---
title: "시스템 통합 계획 고려 사항에 대 한 Azure 스택 | Microsoft Docs"
description: "이제 계획 하 고 다중 노드 Azure 스택에 대 한 준비를 수행할 수 있는 작업에 대해 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: b39b3b8f73f9deef48173c712d7966eb13c6ecc3
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>시스템 통합 계획 고려 사항에 대 한 Azure 스택

*적용 대상: Azure 스택 시스템 통합*

Azure 스택 통합 시스템에 관심이 있다면 배포 및 시스템 데이터 센터에 얼마나 적합 한지 주요 계획 고려 사항 중 일부를 이해 합니다. 이 항목에서는 이러한 고려 사항에 대 한 고급 개요를 제공 합니다.

통합된 시스템을 구입 하려는 경우 (oem) 하드웨어 공급 업체는 데 도움이 됩니다 대부분 자세히 계획 프로세스의 과정을 안내 합니다. 실제 배포도 수행 합니다.

## <a name="management-considerations"></a>관리 고려 사항

여기서는 인프라는 잠겨 있으며는 사용 권한에서 둘 다 azure 스택은 봉인 된 시스템 및 네트워크 관점입니다. 네트워크 액세스 제어 목록 (Acl) 허가 되지 않은 들어오는 모든 트래픽 및 인프라 구성 요소 간의 불필요 한 모든 통신을 차단에 적용 됩니다. 이렇게 하면 시스템에 액세스할 수 있는 권한이 없는 사용자가 어려워집니다.

일별 관리 및 작업에 대 한 인프라에 제한 되지 않은 관리자 액세스 권한 없음 있습니다. Azure 스택 연산자에는 시스템 관리자 포털을 통해 또는 Azure 리소스 관리자를 통해 PowerShell 또는 REST API) (통해 관리 해야 합니다. Hyper-v 관리자 또는 장애 조치 클러스터 관리자와 같은 다른 관리 도구에서 시스템에 액세스할 수 없습니다 있습니다. 시스템을 보호 하기 위해 Azure 스택 인프라의 구성 요소 내부 제 3 자 소프트웨어 (예를 들어, 에이전트)를 설치할 수 없습니다. 외부 관리 및 보안 소프트웨어와의 상호 운용성 PowerShell 또는 REST API를 통해 발생합니다.

경고 중재 단계를 통해 확인 되지 문제 해결을 위한 높은 수준의 액세스 필요 하면 지원을 사용 해야 합니다. 지원을 통해 된 고급 작업을 수행 하는 시스템에 대 한 임시 전체 관리자 액세스를 제공 하는 방법은 없습니다. 

## <a name="deploy-connected-or-disconnected"></a>연결 또는 연결 해제를 배포 합니다.
 
Azure 스택 인터넷 (및 Azure) 연결 또는 연결 끊김 배포 하도록 선택할 수 있습니다. Azure 스택 및 Azure 간의 하이브리드 시나리오를 포함 하 여 Azure 스택에서 가장 효과적으로 활용 하는 배포 하려는 Azure에 연결 합니다. 다음 표에서 배포 모드 간의 주요 차이점을 요약 하는 데 도움이 됩니다.

| 영역 | 연결 된 모드 | 연결이 끊어진된 모드 |
| -------- | ------------- | ----------|
| ID 공급자 | Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) | AD FS |
| 마켓플레이스에서 배포 | 항목을 스택에서 Azure 마켓플레이스에 Azure Marketplace에서 직접 다운로드 | Azure 스택에서 marketplace의 수동 관리 필요 |
| 라이선스 모델 | 사용량 기준 과금-으로--사용 또는 용량 기반 | 용량 기반만|
| 패치 및 업데이트  | Azure 스택에 직접 업데이트 패키지 다운로드 | 이동식 미디어 및 연결된 된 별도 장치 필요 |
| | | |

전체 시스템 재배포 하지 않고 나중에 배포 모드를 변경할 수 없습니다.

## <a name="identity-considerations"></a>Identity 고려 사항

### <a name="choose-identity-provider"></a>Id 공급자를 선택 합니다.

Azure 스택 배포의 경우 Azure AD 또는 AD FS 사용 하려면 id 공급자를 고려해 야 합니다. 전체 시스템 재배포 하지 않고 배포 후 id 공급자를 전환할 수 없습니다.

Identity provider 선택한 테 넌 트 가상 컴퓨터, id 시스템 및 사용 하는 계정에는 영향을 주지 않습니다는 등 Active Directory 도메인에 참가 합니다. 이 별개입니다.

**Azure AD를 사용 하 여 고려해 야 하는 이유**

- Azure AD (예: Azure 또는 Office 365)에 이미 기존 투자.
- Azure 및 Azure 스택 클라우드에서 동일한 id를 사용 하려고 합니다.
- 동일한 Azure 스택 인스턴스에 대해 서로 다른 조직을 호스트할 수 있는 다중 테 넌 트 시나리오를 지원 하려고 합니다.
- Azure AD 그래프를 통해 REST 기반 디렉터리 관리 Api 통해 사용자 프로 비전, 그룹 등를 사용 하려면.

> [!NOTE]
> 동시에 Azure AD 인스턴스와 기존 AD FS 인스턴스 모두에 Azure 스택 배포를 연결할 수 없습니다. 온-프레미스를 페더레이션 하려면 Azure ad를 배포 하는 경우 기존 AD FS 인스턴스를 사용 하려는 Azure AD 사용 하 여 AD FS 인스턴스.

**AD FS를 사용 하 여 고려해 야 하는 이유**

- 없음 또는 부분 인터넷 연결 합니다.
- Sovereignty/규정 요구 사항이 있습니다.
- 사용 하려는 (예: 회사 Active Directory) 사용자 id 시스템과 연산자 및 사용자 계정에 대 한 합니다. 이 수행 하려면 (Windows Server 2012, 2012 R2, 또는 2016)에서 Active Directory에서 지 원하는 기존 AD FS 인스턴스와 페더레이션 할 수 있습니다.
- 기존 Azure 투자 없는 있으며 Azure AD를 사용 하지 않음.

> [!NOTE]
> Active Directory에서 지 원하는 다른 AD FS 인스턴스와만 Azure 스택을 페더레이션 할 수 있습니다. 기타 id 공급자 지원 되지 않습니다. Azure 스택 사용 시 원하는 기타 id 공급자를 사용 하도록 설정한 경우 Azure AD 기반 배포 대신 사용 하는 것이 좋습니다.

### <a name="ad-fs-and-graph-integration"></a>AD FS 및 그래프 통합

AD FS를 사용 하 여 id 공급자로 Azure 스택 배포 하려는 경우 Azure 스택에 AD FS 인스턴스 페더레이션 트러스트를 통해 기존 AD FS 인스턴스와 통합 해야 합니다. 따라서 기존 Active Directory 포리스트를 스택에서 Azure 리소스를 사용 하 여 인증에서 id 수 있습니다.

기존 Active Directory와 Azure 스택의 그래프 서비스를 통합할 수도 있습니다. 이렇게 하면 역할 기반 액세스 제어 (RBAC) Azure 스택의 관리할 수 있습니다. 리소스에 대 한 액세스 위임 되는 그래프 구성 요소 LDAP 프로토콜을 사용 하 여 기존 Active Directory 포리스트의 사용자 계정을 조회 합니다.

다음 다이어그램에서는 통합 된 AD FS 및 그래프 트래픽 흐름을 보여 줍니다.
![AD FS 및 그래프 트래픽 흐름을 보여 주는 다이어그램](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

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

> [!IMPORTANT]
> 이 문서에서 인증서 정보는 일반적인 지침 으로만 제공 됩니다. Azure 스택에 대 한 모든 인증서를 구입 하기 전에 OEM 하드웨어 파트너와 작동 합니다. 더 자세한 인증서 지침 및 요구 사항을 제공 됩니다.

- 단일 와일드 카드 인증서를 사용할 수 있습니다 또는 전용된 인증서 집합을 사용 하 고 저장소 및 키 자격 증명 모음 등 끝점에 대 한 와일드 카드를 사용할 수 있습니다.
- 인증서를 신뢰할 수 있는 공용 인증 기관 (CA)에서 발급 되어야 합니다 또는 고객이 관리 하는 CA입니다.
 
다음 표에서 서비스와의 초기 Azure 스택 배포에 대 한 인증서를 필요로 하는 공용 끝점 수를 보여 줍니다. 

| 용도 | 끝점 
| -------- | ------------- | 
| Azure 리소스 관리자 (관리자) | adminmanagment 합니다. [region]입니다. [external_domain] |
| Azure 리소스 관리자 (사용자) | 관리 합니다. [region]입니다. [external_domain] |
| 포털 (관리자) | adminportal 합니다. [region]입니다. [external_domain] |
| 포털 (사용자) | 포털입니다. [region]입니다. [external_domain] |
| 주요 자격 증명 모음 (사용자) | &#42;. 자격 증명 모음입니다. [region]입니다. [external_domain] |
| 주요 자격 증명 모음 (관리자) | &#42;. adminvault 합니다. [region]입니다. [external_domain] |
| Storage | &#42;. blob입니다. [region]입니다. [external_domain]<br>&#42;. 테이블입니다. [region]입니다. [external_domain]<br>&#42;. 큐입니다. [region]입니다. [external_domain]  |
| 그래프 * * | 그래프입니다. [region]입니다. [external_domain] |
| AD FS * * | adfs 합니다. [region]입니다. [external_domain] |
| | |

* * 그래프 및 AD FS 끝점에 대 한 인증서는 AD FS 배포에만 필요 합니다.

단일 와일드 카드 인증서를 사용 하려면 6 개의 주체 대체 이름 (San) 초기 Azure 스택 배포에 대 한 총을 해야 합니다. 이러한 San은: 

- &#42;. [region]입니다. [external_domain]
- &#42;. 자격 증명 모음입니다. [region]입니다. [external_domain]
- &#42;. adminvault 합니다. [region]입니다. [external_domain]
- &#42;. blob입니다. [region]입니다. [external_domain]
- &#42;. 테이블입니다. [region]입니다. [external_domain] 
- &#42;. 큐입니다. [region]입니다. [external_domain]

## <a name="time-synchronization"></a>시간 동기화

Azure 스택 시간 서버 IP 주소를 통해 확인할 수 있는 외부 시간 서버와 동기화 해야 합니다. 회사 네트워크에 시간 서버가 연결 되지 않은 배포에 대 한 필요 합니다.

## <a name="network-connectivity"></a>네트워크 연결

### <a name="dns-integration"></a>DNS 통합

Azure 스택 (예를 들어 www.bing.com)에서 외부 DNS 이름을 해결 하려면 Azure 스택 Azure 스택 권한을 보유 하지 않은 DNS 요청을 전달 하는 데 사용할 수 있는 DNS 서버를 제공 해야 합니다. 배포 입력으로 내결함성에 대 한 DNS 전달자로 사용 하도록 두 개 이상의 서버를 제공 해야 합니다.

(예: 회사 포리스트)에서 외부 Azure 스택에서 Azure 스택 끝점의 DNS 이름을 확인 하는 데 사용할 부모 영역을 호스트 하는 DNS 서버와 함께 Azure 스택에 대 한 외부 DNS 영역을 호스팅하는 DNS 서버를 통합 해야 합니다. 이렇게 하려면 Azure 스택 및 데이터 센터에서 기존 DNS 영역 간 DNS 이름 확인 합니다. 이를 위해 조건부 전달 또는 영역 위임와 같은 메서드를 사용 합니다. Azure 스택 외부 DNS 네임 스페이스에 대 한 DNS 서버에 대해 직접적인 제어 부모 영역을 호스트 하는 경우 전달 조건부이 좋습니다. (외부 Azure 스택 DNS 영역이 회사 도메인 이름 (예를 들어 azurestack.contoso.com 및 contoso.com)의 자식 도메인으로 표시를 하는 경우 대신 사용 해야 영역 위임 합니다.

### <a name="network-infrastructure"></a>네트워크 인프라

Azure 스택에 대 한 네트워크 인프라는 스위치에 구성 된 여러 논리 네트워크 구성 됩니다. 다음 다이어그램에서는 이러한 논리 네트워크 및 베이스 보드 관리 컨트롤러 (BMC)는의-tor ()와 통합 하는 방법 (고객 네트워크) 스위치 테두리를 보여 줍니다.

![논리 네트워크 다이어그램 및 스위치 연결](media/azure-stack-planning-considerations/NetworkDiagram.png)

다음 표에서 논리 네트워크 및 계획 해야 하는 연결 된 IPv4 서브넷 범위를 보여 줍니다.

| 논리적 네트워크 | 설명 | 크기 | 
| -------- | ------------- | ------------ | 
| 공용 VIP | 소규모의 나머지 테 넌 트 가상 컴퓨터에서 사용 하는 Azure 스택을 서비스에 대 한 공용 IP 주소입니다. Azure 스택 인프라는이 네트워크에서 32 주소를 사용합니다. 앱 서비스 및 SQL 리소스 공급자를 사용 하려는 경우 더 많은 7을 사용 합니다. | / 26 (62 호스트) / 22 (1022 호스트)<br><br>권장 = / 24 (254 호스트) | 
| 스위치 인프라 | 전용된 라우팅 목적에 대 한 지점 간 IP 주소 관리 인터페이스 및 스위치에 할당 된 루프백 주소를 전환 합니다. | /26 | 
| 인프라 | Azure 스택 내부 구성 요소에 대 한 통신 하는 데 사용 합니다. | /24 |
| 개인 | 저장소 네트워크 및 개인 Vip에 사용 합니다. | /24 | 
| BMC | 실제 호스트에서 Bmc와 통신 하는 데 사용 합니다. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>테두리 스위치에 업링크

데이터 센터에서 테두리 스위치에 구성 하는 업링크가 필요 합니다. 다음 방법 중 하나를 사용 하는 Azure 스택 통합 시스템의 일부인의-tor () 스위치에서이 계층 3 트래픽을 라우팅할 수 있습니다.

- 경계 게이트웨이 프로토콜 (BGP) 
- 정적 라우팅

자동 업데이트는 소프트웨어 부하 분산 멀티플렉서 (SLB MUX) 외부 네트워크에 의해 게시 되어 경로 사용 하면 사용 BGP을 좋습니다. 배포 후 공용 IP 주소 범위를 추가 하는 경우에 유용 합니다. BGP 피어 링이에서 수행 하는 경우 TOR 스위치에 연결 된, 배포 후 수동 작업 없이 집계 스위치에 알려 졌는 자동으로 추가 하는 공용 IP 주소 범위 집계 스위치에 TOR 전환 합니다. 고정 라우팅을 사용 하는 경우 공용 IP 주소 블록 추가 될 때마다 새 범위에 대 한 경로 수동으로 업데이트 해야 합니다.

### <a name="proxy-server"></a>프록시 서버

Azure 스택 투명 프록시 서버만 지원합니다. 투명 프록시는 특수 한 클라이언트 구성 없이 네트워크 계층에서 요청을 차단 합니다.

### <a name="publish-azure-stack-services"></a>Azure 스택 서비스 게시

Azure 스택 서비스 사용할 수 있도록 사용자에 게 외부 Azure 스택에서 해야 합니다. Azure 스택 인프라 역할에 대 한 다양 한 끝점을 설정합니다. 이러한 끝점 공용 IP 주소 풀에서 Vip가 할당 됩니다. 외부 DNS 영역 배포 시에 지정 된 각 끝점에 대 한 DNS 항목이 생성 됩니다. 사용자 포털의 DNS 호스트 항목에 할당 된 예를 들어 "포털 <*지역*>. <*external_FQDN*>." 

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

![표시 된 단일 테 넌 트 ExpressRoute 시나리오를 다이어그램합니다](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

다음 다이어그램에서는 다중 테 넌 트 시나리오에 대 한 express 경로 보여 줍니다.

![다중 테 넌 트 ExpressRoute 시나리오를 보여 주는 다이어그램](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>외부 모니터링
Azure 스택 배포 및 장치에서 모든 경고의 단일 뷰에서 하 고 티켓에 대 한 기존 IT 서비스 관리 워크플로에 경고를 통합 솔루션을 모니터링 하는 외부 데이터 센터와 Azure 스택을 통합할 수 있습니다.

Azure 스택 솔루션에 포함 된, 하드웨어 수명 주기 호스트는 하드웨어에 대 한 OEM 공급 업체에서 제공 하는 관리 도구를 실행 하는 Azure 스택 외부 컴퓨터입니다. 이러한 도구 또는 데이터 센터에서 기존 모니터링 솔루션 직접 통합 하는 다른 솔루션을 사용할 수 있습니다.

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
