---
title: Azure Active 디렉터리 응용 프로그램 프록시 배포 계획
description: 조직 내에서 응용 프로그램 프록시 배포를 계획하기 위한 엔드 투 엔드 가이드
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330903"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD 응용 프로그램 프록시 배포 계획

Azure Active Directory(Azure AD) 응용 프로그램 프록시는 온-프레미스 응용 프로그램에 대한 안전하고 비용 효율적인 원격 액세스 솔루션입니다. "Cloud First" 조직이 아직 최신 프로토콜을 사용할 수 없는 레거시 온-프레미스 애플리케이션에 대한 액세스를 관리할 수 있는 즉각적인 전환 경로를 제공합니다. 추가 소개 정보는 [응용 프로그램 프록시입니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)

원격 사용자에게 내부 리소스에 대한 액세스 권한을 부여하는 데 응용 프로그램 프록시를 권장합니다. 응용 프로그램 프록시는 이러한 원격 액세스 사용 사례에 대한 VPN 또는 역방향 프록시의 필요성을 대체합니다. 회사 네트워크에 있는 사용자를 위한 것이 아닙니다. 인트라넷 액세스에 응용 프로그램 프록시를 사용하는 사용자는 바람직하지 않은 성능 문제가 발생할 수 있습니다.

이 문서에는 Azure AD 응용 프로그램 프록시를 계획, 운영 및 관리하는 데 필요한 리소스가 포함되어 있습니다. 

## <a name="plan-your-implementation"></a>구현 계획

다음 섹션에서는 효율적인 배포 환경을 위해 설정하는 주요 계획 요소에 대한 광범위한 보기를 제공합니다. 

### <a name="prerequisites"></a>사전 요구 사항

구현을 시작하기 전에 다음 필수 구성 조건을 충족해야 합니다. 이 [자습서에서는](application-proxy-add-on-premises-application.md)이러한 필수 구성 조건을 포함하여 환경 설정에 대한 자세한 정보를 볼 수 있습니다.

* **커넥터**: 커넥터는 다음에 배포할 수 있는 경량 에이전트입니다.
   * 온-프레미스의 물리적 하드웨어
   * 모든 하이퍼바이저 솔루션 내에서 호스팅되는 VM
   * 응용 프로그램 프록시 서비스에 대한 아웃바운드 연결을 사용하도록 Azure에서 호스팅되는 VM입니다.

* 자세한 개요는 [Azure AD 앱 프록시 커넥터 이해하기를](application-proxy-connectors.md) 참조하십시오.

     * 커넥터를 설치하기 전에 [TLS 1.2에 대해](application-proxy-add-on-premises-application.md) 커넥터 컴퓨터를 사용하도록 설정해야 합니다.

     * 가능하면 커넥터를 백 엔드 웹 응용 프로그램 서버와 [동일한 네트워크에](application-proxy-network-topology.md) 배포하고 세그먼트화합니다. 응용 프로그램 검색을 완료한 후 커넥터를 배포하는 것이 가장 좋습니다.
     * 각 커넥터 그룹에는 고가용성 및 확장을 제공하기 위해 두 개 이상의 커넥터가 있는 것이 좋습니다. 언제든지 컴퓨터를 서비스해야 하는 경우에 대비하여 3개의 커넥터를 갖는 것이 최적입니다. 커넥터 [용량 테이블을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) 검토하여 커넥터를 설치할 컴퓨터 유형을 결정하는 데 도움이 됩니다. 기계가 클수록 더 많은 버퍼와 수행자가 커넥터가 됩니다.

* **네트워크 액세스 설정**: Azure AD 응용 프로그램 프록시 커넥터는 [HTTPS(TCP 포트 443) 및 HTTP(TCP 포트 80)를 통해 Azure에 연결합니다.](application-proxy-add-on-premises-application.md) 

   * 커넥터 TLS 트래픽 종료는 지원되지 않으며 커넥터가 해당 Azure App 프록시 끝점을 사용하여 보안 채널을 설정하지 못하도록 합니다.

   * 커넥터와 Azure 간의 아웃바운드 TLS 통신에 대한 모든 형태의 인라인 검사를 피하십시오. 커넥터와 백 엔드 응용 프로그램 간의 내부 검사가 가능하지만 사용자 환경이 저하될 수 있으므로 권장되지 않습니다.

   * 커넥터 자체의 부하 분산도 지원되지 않거나 필요하지 않습니다.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 구성하기 전에 고려해야 할 사항

Azure AD 응용 프로그램 프록시를 구성하고 구현하려면 다음 핵심 요구 사항을 충족해야 합니다.

*  **Azure 온보딩**: 응용 프로그램 프록시를 배포하기 전에 사용자 ID는 온-프레미스 디렉터리에서 동기화되거나 Azure AD 테넌트 내에서 직접 만들어야 합니다. ID 동기화를 통해 Azure AD가 사용자에게 App Proxy 게시 애플리케이션에 대한 액세스 권한을 부여하기 전에 미리 인증하고 SSO(Single Sign-On)를 수행하는 데 필요한 사용자 식별자 정보를 가질 수 있습니다.

* **조건부 액세스 요구 사항**: 사용자에게 영향을 주는 대기 시간이 추가되므로 인트라넷 액세스에 응용 프로그램 프록시를 사용하지 않는 것이 좋습니다. 인터넷에서 원격 액세스를 위해 사전 인증 및 조건부 액세스 정책과 함께 응용 프로그램 프록시를 사용하는 것이 좋습니다.  인트라넷 사용을 위한 조건부 액세스를 제공하는 방법은 AAD로 직접 인증할 수 있도록 응용 프로그램을 현대화하는 것입니다. 자세한 내용은 [응용 프로그램을 AAD로 마이그레이션하기 위한 리소스를](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 참조하십시오. 

* **서비스 제한**: 개별 테넌트의 리소스 과다 사용으로부터 보호하기 위해 응용 프로그램 및 테넌트별로 설정된 제한 제한이 있습니다. 이러한 제한을 보려면 [Azure AD 서비스 제한 및 제한을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)참조하십시오. 이러한 제한 제한은 일반적인 사용량 볼륨보다 훨씬 높은 벤치마크를 기반으로 하며 대부분의 배포에 충분한 버퍼를 제공합니다.

* **공용 인증서**: 사용자 지정 도메인 이름을 사용하는 경우 TLS/SSL 인증서를 조달해야 합니다. 조직의 요구 사항에 따라 인증서를 얻는 데 다소 시간이 걸릴 수 있으므로 가능한 한 빨리 프로세스를 시작하는 것이 좋습니다. Azure 응용 프로그램 프록시는 표준, [와일드카드](application-proxy-wildcard.md)또는 SAN 기반 인증서를 지원합니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용 하 여 사용자 지정 도메인 구성을](application-proxy-configure-custom-domain.md)참조 합니다.

* **도메인 요구 사항**: Kerberos 제한 위임(KCD)을 사용하여 게시된 응용 프로그램에 대한 단일 로그온을 수행하려면 커넥터를 실행하는 서버와 앱을 실행하는 서버가 도메인에 가입되어 있고 동일한 도메인 또는 트러스트 도메인의 일부가 되어야 합니다.
이 항목에 대한 자세한 내용은 응용 프로그램 [프록시를 통해 단일 사인온에 대한 KCD를](application-proxy-configure-single-sign-on-with-kcd.md) 참조하십시오. 커넥터 서비스는 로컬 시스템의 컨텍스트에서 실행되며 사용자 지정 ID를 사용하도록 구성해서는 안 됩니다.

* **URL에 대한 DNS 레코드**

   * 응용 프로그램 프록시에서 사용자 지정 도메인을 사용하기 전에 공용 DNS에서 CNAME 레코드를 만들어 클라이언트가 미리 정의된 응용 프로그램 프록시 주소로 사용자 정의 외부 URL을 확인할 수 있도록 해야 합니다. 사용자 지정 도메인을 사용하는 응용 프로그램에 대한 CNAME 레코드를 만들지 않으면 원격 사용자가 응용 프로그램에 연결할 수 없습니다. CNAME 레코드를 추가하는 데 필요한 단계는 DNS 공급자마다 다를 수 있으므로 [Azure 포털을 사용하여 DNS 레코드 및 레코드 집합을 관리하는](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)방법을 알아봅니다.

   * 마찬가지로 커넥터 호스트는 게시되는 응용 프로그램의 내부 URL을 확인할 수 있어야 합니다.

* **관리 권한 및 역할**

   * **커넥터 설치에는** 설치 중인 Windows 서버에 대한 로컬 관리자 권한이 필요합니다. 또한 Azure AD 테넌트에 커넥터 인스턴스를 인증하고 등록하려면 최소한의 *응용 프로그램 관리자* 역할이 필요합니다. 

   * **응용 프로그램 게시 및 관리에는** *응용 프로그램 관리자* 역할이 필요합니다. 응용 프로그램 관리자는 등록, SSO 설정, 사용자 및 그룹 할당 및 라이선스, 응용 프로그램 프록시 설정 및 동의를 포함하여 디렉터리에서 모든 응용 프로그램을 관리할 수 있습니다. 조건부 액세스를 관리하는 기능은 부여하지 않습니다. *클라우드 응용 프로그램 관리자* 역할에는 응용 프로그램 프록시 설정 관리가 허용되지 않는다는 점을 제외하면 응용 프로그램 관리자의 모든 능력이 있습니다.

* **라이선스**: 응용 프로그램 프록시는 Azure AD 프리미엄 구독을 통해 사용할 수 있습니다. 라이선스 옵션 및 기능의 전체 목록은 [Azure Active Directory 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/active-directory/) 참조하십시오.  

### <a name="application-discovery"></a>응용 프로그램 검색

다음 정보를 수집하여 응용 프로그램 프록시를 통해 게시되는 모든 범위 내 응용 프로그램의 인벤토리를 컴파일합니다.

| Information Type| 수집할 정보 |
|---|---|
| 서비스 유형| 예: 공유점, SAP, CRM, 사용자 지정 웹 응용 프로그램, API |
| 애플리케이션 플랫폼 | 예: 윈도우 IIS, 리눅스의 아파치, 톰캣, NGINX |
| 도메인 멤버 자격| 웹 서버의 정규화된 도메인 이름(FQDN) |
| 신청 위치 | 인프라에 웹 서버 또는 팜이 있는 위치 |
| 내부 액세스 | 내부적으로 응용 프로그램에 액세스할 때 사용되는 정확한 URL입니다. <br> 팜의 경우 어떤 유형의 부하 분산이 사용 중입니까? <br> 응용 프로그램이 자체가 아닌 다른 소스에서 콘텐츠를 그리는지 여부입니다.<br> 응용 프로그램이 WebSocket을 통해 작동하는지 확인합니다. |
| 외부 액세스 | 응용 프로그램이 이미 외부에 노출된 공급업체 솔루션입니다. <br> 외부 액세스에 사용할 URL입니다. SharePoint의 경우 [이 지침에](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)따라 대체 액세스 매핑이 구성되었는지 확인합니다. 그렇지 않은 경우 외부 URL을 정의해야 합니다. |
| 공용 인증서 | 사용자 지정 도메인을 사용하는 경우 해당 주체 이름으로 인증서를 조달합니다. 인증서가 있는 경우 일련 번호와 인증서를 얻을 수 있는 위치를 기록합니다. |
| 인증 유형| 기본, Windows 통합 인증, 양식 기반, 헤더 기반 및 클레임과 같은 응용 프로그램 지원에서 지원하는 인증 유형입니다. <br>응용 프로그램이 특정 도메인 계정으로 실행되도록 구성된 경우 서비스 계정의 완전 인증된 도메인 이름(FQDN)을 기록합니다.<br> SAML 기반인 경우 식별자 및 회신 URL입니다. <br> 헤더 기반인 경우 공급업체 솔루션 및 인증 유형 처리에 대한 특정 요구 사항입니다. |
| 커넥터 그룹 이름 | 이 백 엔드 응용 프로그램에 도관 및 SSO를 제공하도록 지정될 커넥터 그룹의 논리적 이름입니다. |
| 사용자/그룹 액세스 | 응용 프로그램에 대한 외부 액세스 권한을 부여할 사용자 또는 사용자 그룹입니다. |
| 추가 요구 사항 | 응용 프로그램 게시에 고려해야 하는 추가 원격 액세스 또는 보안 요구 사항을 기록합니다. |

이 응용 [프로그램 인벤토리 스프레드시트를](https://aka.ms/appdiscovery) 다운로드하여 앱을 인벤토리할 수 있습니다.

### <a name="define-organizational-requirements"></a>조직 요구 사항 정의

다음은 조직의 비즈니스 요구 사항을 정의해야 하는 영역입니다. 각 영역에는 요구 사항의 예가 포함되어 있습니다.

 **액세스**

* 도메인이 가입된 원격 사용자 또는 Azure AD 조인 장치를 보유한 원격 사용자는 원활한 단일 사인온(SSO)을 사용하여 게시된 응용 프로그램에 안전하게 액세스할 수 있습니다.

* 승인된 개인 장치를 사용하는 원격 사용자는 MFA에 등록되어 있고 휴대폰에 Microsoft 인증자 앱을 인증 방법으로 등록한 경우 게시된 응용 프로그램에 안전하게 액세스할 수 있습니다.

**거 버 넌 스** 

* 관리자는 응용 프로그램 프록시를 통해 게시된 응용 프로그램에 대한 사용자 할당의 수명 주기를 정의하고 모니터링할 수 있습니다.

**보안**

* 그룹 구성원 자격을 통해 응용 프로그램에 할당된 사용자만 해당 응용 프로그램에 액세스할 수 있습니다.

**성능**

* 내부 네트워크에서 응용 프로그램에 액세스하는 것에 비해 응용 프로그램 성능이 저하되지 않습니다.

**사용자 경험**

* 사용자는 모든 장치 플랫폼에서 친숙한 회사 URL을 사용하여 응용 프로그램에 액세스하는 방법을 알고 있습니다.

**감사**
* 관리자는 사용자 액세스 활동을 감사할 수 있습니다.


### <a name="best-practices-for-a-pilot"></a>시험 운용을 위한 모범 사례

단일 사인온(SSO)을 사용하여 원격 액세스를 위해 단일 응용 프로그램을 완전히 커미셔널하는 데 필요한 시간과 노력을 결정합니다. 초기 검색, 게시 및 일반 테스트를 고려하는 파일럿을 실행하여 이렇게 합니다. IWA(통합 Windows 인증)에 대해 이미 미리 구성된 간단한 IIS 기반 웹 응용 프로그램을 사용하면 원격 액세스 및 SSO를 성공적으로 파일럿하기 위해 최소한의 노력이 필요하므로 기준을 설정하는 데 도움이 됩니다.

다음 디자인 요소는 프로덕션 테넌트에서 직접 파일럿 구현의 성공을 높여야 합니다.  

**커넥터 관리**:  

* 커넥터는 응용 프로그램에 온-프레미스 도관을 제공하는 데 핵심적인 역할을 합니다. **기본** 커넥터 그룹을 사용하면 프로덕션에 시운전하기 전에 게시된 응용 프로그램의 초기 파일럿 테스트에 적합합니다. 성공적으로 테스트된 응용 프로그램을 프로덕션 커넥터 그룹으로 이동할 수 있습니다.

**응용 프로그램 관리**:

* 직원들은 외부 URL이 친숙하고 관련성이 높다는 것을 기억할 가능성이 큽니까? 미리 정의된 msappproxy.net 또는 onmicrosoft.com 접미사를 사용하여 응용 프로그램을 게시하지 마십시오. 대신 *intranet.<customers_domain>.com과*같은 논리적 호스트 이름으로 접두사에 있는 친숙한 최상위 인증 도메인을 제공합니다.

* Azure MyApps 포털을 형성하는 시작 아이콘을 숨김으로써 파일럿 응용 프로그램 아이콘의 가시성을 파일럿 그룹으로 제한합니다. 프로덕션 준비가 되면 동일한 사전 프로덕션 테넌트에서 또는 프로덕션 테넌트에 응용 프로그램을 게시하여 해당 대상 대상에 맞게 앱을 범위를 지정할 수 있습니다.

**단일 사인온 설정**: 일부 SSO 설정에는 설정하는 데 시간이 걸릴 수 있는 특정 종속성이 있으므로 종속성이 미리 처리되도록 하여 변경 제어 지연을 방지합니다. 여기에는 Kerberos 제한 위임(KCD)을 사용하여 SSO를 수행하기 위해 커넥터 호스트를 도메인 조인하고 시간이 많이 소요되는 다른 작업을 처리해야 합니다. 예를 들어 헤더 기반 SSO가 필요한 경우 PING 액세스 인스턴스를 설정합니다.

**커넥터 호스트와 대상 응용 프로그램 간의 TLS**: 보안이 가장 중요하므로 커넥터 호스트와 대상 응용 프로그램 간의 TLS를 항상 사용해야 합니다. 특히 웹 응용 프로그램이 FBA(양식 기반 인증)를 위해 구성된 경우 사용자 자격 증명이 일반 텍스트로 효과적으로 전송됩니다.

**증분 구현 하고 각 단계를 테스트합니다.** 응용 프로그램을 게시한 후 기본 기능 테스트를 수행하여 아래 지침에 따라 모든 사용자 및 비즈니스 요구 사항을 충족하는지 확인합니다.

1. 사전 인증을 사용하지 않도록 설정한 웹 응용 프로그램에 대한 일반 액세스를 테스트하고 유효성을 검사합니다.
2. 성공하면 사전 인증을 활성화하고 사용자 및 그룹을 할당합니다. 액세스를 테스트하고 유효성을 검사합니다.
3. 그런 다음 응용 프로그램에 대한 SSO 메서드를 추가하고 다시 테스트하여 액세스의 유효성을 검사합니다.
4. 필요에 따라 조건부 액세스 및 MFA 정책을 적용합니다. 액세스를 테스트하고 유효성을 검사합니다.

**문제 해결 도구:** 문제 해결 시 항상 커넥터 호스트의 브라우저에서 게시된 응용 프로그램에 대한 액세스의 유효성을 검사하고 응용 프로그램이 예상대로 작동하는지 확인합니다. 설정이 간단할수록 근본 원인을 쉽게 파악할 수 있으므로 단일 커넥터만 사용하고 SSO가 없는 것과 같은 최소한의 구성으로 문제를 재현하는 것이 좋습니다. 경우에 따라 Telerik의 Fiddler와 같은 웹 디버깅 도구는 프록시를 통해 액세스하는 응용 프로그램의 액세스 또는 콘텐츠 문제를 해결하는 데 필수적임이 될 수 있습니다. Fiddler는 또한 iOS 및 Android와 같은 모바일 플랫폼과 프록시를 통해 라우팅하도록 구성할 수 있는 거의 모든 것에 대한 트래픽을 추적하고 디버깅하는 데 도움이 되는 프록시 역할을 할 수 있습니다. 자세한 내용은 [문제 해결 가이드를](application-proxy-troubleshoot.md) 참조하십시오.

## <a name="implement-your-solution"></a>솔루션 구현

### <a name="deploy-application-proxy"></a>응용 프로그램 프록시 배포

응용 프로그램 프록시를 배포하는 단계는 [원격 액세스를 위한 온-프레미스 응용 프로그램을 추가하기 위한](application-proxy-add-on-premises-application.md)이 자습서에서 다룹니다. 설치에 성공하지 못하면 포털에서 **응용 프로그램 프록시 문제를 해결하거나** 응용 프로그램 프록시 에이전트 커넥터 설치 문제에 대한 문제 해결 [가이드를](application-proxy-connector-installation-problem.md)사용합니다.

### <a name="publish-applications-via-application-proxy"></a>응용 프로그램 프록시를 통해 응용 프로그램 게시

응용 프로그램을 게시하면 모든 필수 구성 조건을 충족했으며 응용 프로그램 프록시 페이지에 등록및 활성으로 표시되는 커넥터가 여러 개 있다고 가정합니다.

[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)을 사용하여 응용 프로그램을 게시할 수도 있습니다.

다음은 응용 프로그램을 게시할 때 따라야 할 몇 가지 모범 사례입니다.

* **커넥터 그룹 사용**: 각 응용 프로그램을 게시하도록 지정된 커넥터 그룹을 할당합니다. 각 커넥터 그룹에는 고가용성 및 확장을 제공하기 위해 두 개 이상의 커넥터가 있는 것이 좋습니다. 언제든지 컴퓨터를 서비스해야 하는 경우에 대비하여 3개의 커넥터를 갖는 것이 최적입니다. 또한 [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에 응용 프로그램 게시를](application-proxy-connector-groups.md) 참조하여 커넥터 그룹을 사용하여 커넥터를 네트워크 또는 위치별로 분할하는 방법을 확인합니다.

* **백 엔드 응용 프로그램 시간 초과 설정**: 이 설정은 응용 프로그램이 클라이언트 트랜잭션을 처리하는 데 75초 이상 필요할 수 있는 시나리오에서 유용합니다. 예를 들어 클라이언트가 데이터베이스에 프런트 엔드 역할을 하는 웹 응용 프로그램에 쿼리를 보내는 경우입니다. 프런트 엔드는 이 쿼리를 백 엔드 데이터베이스 서버로 보내고 응답을 기다리지만 응답을 받을 때까지 는 클라이언트 측이 시간 시간을 버티게 됩니다. 시간 시간을 Long으로 설정하면 긴 트랜잭션이 완료될 때까지 180초가 됩니다.

* **적절한 쿠키 유형 사용**

   * **HTTP 전용 쿠키**: 응용 프로그램 프록시가 설정 쿠키 HTTP 응답 헤더에 HTTPOnly 플래그를 포함하도록 하여 추가 보안을 제공합니다. 이 설정은 XSS(교차 사이트 스크립팅)와 같은 악용을 완화하는 데 도움이 됩니다. 세션 쿠키에 액세스해야 하는 클라이언트/사용자 에이전트에 대해 이 설정을 아니요로 둡니다. 예를 들어 RDP/MTSC 클라이언트는 앱 프록시를 통해 게시된 원격 데스크톱 게이트웨이에 연결합니다.

   * **보안 쿠키**: 쿠키가 Secure 특성으로 설정된 경우 사용자 에이전트(클라이언트 측 앱)는 요청이 TLS 보안 채널을 통해 전송되는 경우에만 HTTP 요청에 쿠키를 포함합니다. 이렇게 하면 쿠키가 일반 텍스트 채널을 통해 손상될 위험을 줄일 수 있으므로 활성화해야 합니다.

   * **영구 쿠키**: 응용 프로그램 프록시 세션 쿠키가 만료되거나 삭제될 때까지 유효한 채로 브라우저 닫기 사이에 유지하도록 허용합니다. Office와 같은 리치 응용 프로그램이 사용자가 인증하라는 메시지를 다시 받지 않고 게시된 웹 응용 프로그램 내에서 문서에 액세스하는 시나리오에 사용됩니다. 그러나 영구 쿠키는 궁극적으로 다른 보상 컨트롤과 함께 사용하지 않을 경우 서비스가 무단 액세스의 위험에 처할 수 있기 때문에 주의해서 사용하도록 설정합니다. 이 설정은 프로세스 간에 쿠키를 공유할 수 없는 이전 응용 프로그램에만 사용해야 합니다. 이 설정을 사용하는 대신 프로세스 간에 쿠키를 공유하도록 응용 프로그램을 업데이트하는 것이 좋습니다.

* **헤더로 URL 번역**: 조직의 공용 네임스페이스(분할 DNS)와 일치하도록 내부 DNS를 구성할 수 없는 시나리오에서는 이 기능을 사용하도록 설정합니다. 응용 프로그램에 클라이언트 요청의 원래 호스트 헤더가 필요하지 않으면 이 값을 예로 설정합니다. 다른 방법은 커넥터가 실제 트래픽을 라우팅하기 위해 내부 URL에서 FQDN을 사용하고 외부 URL의 FQDN을 호스트 헤더로 사용하도록 하는 것입니다. 대부분의 경우 이 대안은 원격으로 액세스할 때 응용 프로그램이 정상적으로 작동하도록 허용해야 하지만 사용자는 URL 외부의 & 내부에서 일치하는 이점을 잃게 됩니다.

* **응용 프로그램 본문의 URL 번역**: 해당 앱의 링크를 클라이언트에 대한 응답으로 번역하려는 경우 앱의 응용 프로그램 본문 링크 번역을 켭니다. 이 기능을 사용하면 App Proxy가 클라이언트에 반환되는 HTML 및 CSS 응답에서 찾은 모든 내부 링크를 변환하는 데 최선의 노력을 기울입니다. 콘텐츠에 하드 코딩된 절대 또는 NetBIOS 단축 이름이 포함된 앱또는 다른 온-프레미스 응용 프로그램에 연결되는 콘텐츠가 있는 앱을 게시할 때 유용합니다.

게시된 앱이 게시된 다른 앱에 링크되는 시나리오의 경우 앱별 수준에서 사용자 환경을 제어할 수 있도록 각 응용 프로그램에 대한 링크 번역을 사용하도록 설정합니다.

예를 들어 응용 프로그램 프록시를 통해 모두 서로 연결되는 세 개의 응용 프로그램(혜택, 비용 및 여행) 및 응용 프로그램 프록시를 통해 게시되지 않은 네 번째 앱인 피드백이 있다고 가정합니다.

![그림 1](media/App-proxy-deployment-plan/link-translation.png)

혜택 앱에 대한 링크 번역을 사용하도록 설정하면 회사 네트워크 외부에서 응용 프로그램에 액세스하는 사용자가 액세스할 수 있도록 비용 및 여행에 대한 링크가 해당 앱의 외부 URL로 리디렉션됩니다. 비용 및 여행에서 혜택으로 돌아가는 링크는 해당 두 앱에 대해 링크 번역이 활성화되지 않았기 때문에 작동하지 않습니다. 외부 URL이 없기 때문에 피드백에 대한 링크가 리디렉션되지 않으므로 혜택 앱을 사용하는 사용자는 회사 네트워크 외부에서 피드백 앱에 액세스할 수 없습니다. 링크 번역 [및 기타 리디렉션 옵션에](application-proxy-configure-hard-coded-link-translation.md)대한 자세한 정보를 참조하십시오.

### <a name="access-your-application"></a>응용 프로그램에 액세스

App Proxy 게시된 리소스에 대한 액세스를 관리하기 위한 몇 가지 옵션이 있으므로 지정된 시나리오 및 확장성 요구 사항에 가장 적합한 옵션을 선택합니다. 일반적인 방법은 Azure AD Connect를 통해 동기화되는 온-프레미스 그룹을 사용하거나, 사용자 특성을 기반으로 Azure AD에서 동적 그룹을 만들거나, 리소스 소유자가 관리하는 셀프 서비스 그룹을 사용하거나, 이러한 모든 조합을 사용하는 것입니다. 각 리소스의 이점에 대한 링크된 리소스를 참조하십시오.

사용자에게 응용 프로그램에 대한 액세스 권한을 할당하는 가장 직접적인 방법은 게시된 응용 프로그램의 왼쪽 창에서 **사용자 및 그룹** 옵션으로 전환되고 그룹 또는 개인을 직접 할당하는 것입니다.

![그림 24](media/App-proxy-deployment-plan/add-user.png)

또한 사용자가 현재 구성원이 아닌 그룹을 할당하고 셀프 서비스 옵션을 구성하여 응용 프로그램에 대한 셀프 서비스 액세스를 허용할 수도 있습니다.

![그림 25](media/App-proxy-deployment-plan/allow-access.png)

활성화된 경우 사용자는 MyApps 포털에 로그인하여 액세스를 요청할 수 있으며, 이미 허용된 셀프 서비스 그룹에 자동 승인 및 추가되거나 지정된 승인자의 승인이 필요합니다.

게스트 사용자는 [Azure AD B2B를 통해 응용 프로그램 프록시를 통해 게시된 내부 응용 프로그램에 액세스하도록 초대받을](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)수도 있습니다.

일반적으로 익명으로 액세스할 수 있는 온-프레미스 응용 프로그램의 경우 인증이 필요하지 않은 경우 응용 프로그램의 **속성에**있는 옵션을 사용하지 않도록 설정하는 것이 좋습니다.

![그림 26](media/App-proxy-deployment-plan/assignment-required.png)


이 옵션을 아니요로 두면 사용자가 권한 없이 Azure AD 앱 프록시를 통해 온-프레미스 응용 프로그램에 액세스할 수 있으므로 주의해서 사용하십시오.

응용 프로그램이 게시되면 브라우저또는 [https://myapps.microsoft.com](https://myapps.microsoft.com/)의 아이콘으로 외부 URL을 입력하여 액세스할 수 있어야 합니다.

### <a name="enable-pre-authentication"></a>사전 인증 사용

외부 URL을 통해 액세스하는 응용 프로그램 프록시를 통해 응용 프로그램에 액세스할 수 있는지 확인합니다. 

1. Azure **Active Directory** > **Enterprise 응용** > 프로그램으로 이동**모든 응용 프로그램을** 관리하고 자하는 앱을 선택합니다.

2. **애플리케이션 프록시**를 선택합니다.

3. 사전 **인증** 필드에서 드롭다운 목록을 사용하여 **Azure Active Directory를**선택하고 **저장을**선택합니다.

사전 인증을 사용하도록 설정하면 Azure AD가 먼저 사용자에게 인증을 요청하며 단일 사인온이 구성된 경우 백 엔드 응용 프로그램은 응용 프로그램에 대한 액세스가 부여되기 전에 사용자를 확인합니다. 사전 인증 모드를 통과에서 Azure AD로 변경하면 HTTPS로 외부 URL도 구성되므로 HTTP로 처음 구성된 모든 응용 프로그램은 이제 HTTPS로 보호됩니다.

### <a name="enable-single-sign-on"></a>Single Sign-On 사용

SSO는 사용자가 Azure AD에 액세스할 때 한 번만 로그인하면 되므로 최상의 사용자 환경과 보안을 제공합니다. 사용자가 사전 인증되면 SSO는 사용자를 대신하여 온-프레미스 응용 프로그램에 인증되는 응용 프로그램 프록시 커넥터에 의해 수행됩니다. 백 엔드 응용 프로그램은 사용자 자신인 것처럼 로그인을 처리합니다. 

**통과** 옵션을 선택하면 사용자가 Azure AD를 인증할 필요 없이 게시된 응용 프로그램에 액세스할 수 있습니다.

Azure AD가 리소스에 대한 액세스를 요청하는 사용자를 식별할 수 있는 경우에만 SSO 수행이 가능하므로 SSO가 작동하기 위해 액세스시 Azure AD를 사용하여 사용자를 미리 인증하도록 응용 프로그램을 구성해야 하며 그렇지 않으면 SSO 옵션이 비활성화됩니다.

[Azure AD의 응용 프로그램에 대한 단일 사인온을](what-is-single-sign-on.md) 읽고 응용 프로그램을 구성할 때 가장 적합한 SSO 방법을 선택할 수 있습니다.

###  <a name="working-with-other-types-of-applications"></a>다른 유형의 응용 프로그램 작업

Azure AD 응용 프로그램 프록시는 Azure AD 인증[라이브러리(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)또는 Microsoft 인증[라이브러리(MSAL)를](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)사용하도록 개발된 응용 프로그램을 지원할 수도 있습니다. 사용자를 대신하여 사전 인증을 수행하기 위해 클라이언트 요청의 헤더 정보에서 수신된 Azure AD 발급 토큰을 사용하여 네이티브 클라이언트 앱을 지원합니다.

네이티브 및 모바일 클라이언트 앱 및 [클레임 기반 응용 프로그램 게시를](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) 읽고 응용 프로그램 프록시의 사용 가능한 구성에 대해 알아봅니다. [publishing native and mobile client apps](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)

### <a name="use-conditional-access-to-strengthen-security"></a>조건부 액세스를 사용하여 보안 강화

애플리케이션 보안에는 온프레미스 및 클라우드에서 복잡한 위협으로부터 보호하고 대응할 수 있는 고급 보안 기능이 필요합니다. 공격자는 대부분 약하거나 기본값 또는 도난당한 사용자 자격 증명을 통해 회사 네트워크에 액세스합니다.  Microsoft ID 기반 보안은 권한 있는 ID와 비권한 ID를 모두 관리하고 보호하여 도난당한 자격 증명의 사용을 줄입니다.

Azure AD 응용 프로그램 프록시를 지원하는 데 다음 기능을 사용할 수 있습니다.

* 사용자 및 위치 기반 조건부 액세스: [위치 기반 조건부](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)액세스 정책이 있는 지리적 위치 또는 IP 주소를 기반으로 사용자 액세스를 제한하여 중요한 데이터를 보호합니다.

* 장치 기반 조건부 액세스: 등록, 승인 및 호환 장치만 [장치 기반 조건부 액세스를](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)사용하여 회사 데이터에 액세스할 수 있는지 확인합니다.

* 응용 프로그램 기반 조건부 액세스: 사용자가 회사 네트워크에 없을 때 작업을 중지할 필요가 없습니다. [회사 클라우드 및 온-프레미스 앱에 대한 보안을 유지하고](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) 조건부 액세스를 통해 제어를 유지합니다.

* 위험 기반 조건부 액세스: 온-프레미스 또는 클라우드에서 모든 앱과 모든 사용자에게 적용할 수 있는 [위험 기반 조건부 액세스 정책을](https://www.microsoft.com/cloud-platform/conditional-access) 사용하여 악의적인 해커로부터 데이터를 보호합니다.

* Azure AD 액세스 패널: 응용 프로그램 프록시 서비스가 배포되고 응용 프로그램이 안전하게 게시되면 사용자에게 모든 응용 프로그램을 검색하고 액세스할 수 있는 간단한 허브를 제공합니다. 액세스 [패널을](https://aka.ms/AccessPanelDPDownload)통해 새 앱 및 그룹에 대한 액세스를 요청하거나 다른 사람을 대신하여 이러한 리소스에 대한 액세스를 관리하는 기능과 같은 셀프 서비스 기능을 사용하여 생산성을 향상시입니다.

## <a name="manage-your-implementation"></a>구현 관리

### <a name="required-roles"></a>필수 역할

Microsoft는 Azure AD를 사용하여 필요한 작업을 수행할 수 있는 최소한의 권한을 부여하는 원칙을 옹호합니다. [사용 가능한 다양한 Azure 역할을 검토하고](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 각 페르소내의 요구를 해결할 올바른 역할을 선택합니다. 배포가 완료된 후 일부 역할을 일시적으로 적용하고 제거해야 할 수 있습니다.

| 비즈니스 역할| 비즈니스 작업| Azure AD 역할 |
|---|---|---|
| 헬프 데스크 관리자 | 일반적으로 최종 사용자가 보고한 문제를 보고하고 사용자의 암호 변경, 새로 고침 토큰 무효화 및 서비스 상태 모니터링과 같은 제한된 작업을 수행하는 것으로 제한됩니다. | 기술 지원팀 관리자 |
| ID 관리자| 보고서에서 Azure AD 로그인을 읽고 로그를 감사하여 App 프록시 관련 문제를 디버깅합니다.| 보안 판독기 |
| 애플리케이션 소유자| 엔터프라이즈 응용 프로그램, 응용 프로그램 등록 및 응용 프로그램 프록시 설정의 모든 측면을 만들고 관리합니다.| 응용 프로그램 관리자 |
| 인프라 관리자 | 인증서 롤오버 소유자 | 응용 프로그램 관리자 |

보안 정보 나 리소스에 액세스할 수 있는 사용자의 수를 최소화하면 악의적인 행위자가 무단 액세스를 얻거나 권한이 있는 사용자가 실수로 중요한 리소스에 영향을 줄 수 있는 기회를 줄이는 데 도움이 됩니다. 
 
그러나 사용자는 여전히 일상적인 권한 있는 작업을 수행해야 하므로 JIT(적시) 기반 [권한 있는 ID 관리](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 정책을 적용하여 Azure 리소스 및 Azure AD에 대한 온디맨드 권한 있는 액세스를 제공하는 것이 관리 액세스 및 감사를 효과적으로 관리하기 위한 권장 방법입니다.

### <a name="reporting-and-monitoring"></a>보고 및 모니터링

Azure AD는 [감사 로그 및 보고서를](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)통해 조직의 응용 프로그램 사용 및 운영 상태와 에 대한 추가 통찰력을 제공합니다. 또한 응용 프로그램 프록시를 사용하면 Azure AD 포털 및 Windows 이벤트 로그에서 커넥터를 매우 쉽게 모니터링할 수 있습니다.

#### <a name="application-audit-logs"></a>애플리케이션 감사 로그

이러한 로그는 응용 프로그램 프록시로 구성된 응용 프로그램 및 장치 및 응용 프로그램에 액세스하는 사용자에 대한 로그인에 대한 자세한 정보를 제공합니다. [감사 로그는](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 내보내기를 위해 Azure 포털 및 [감사 API에](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) 있습니다. 또한 응용 프로그램에 [대한 사용 및 인사이트 보고서도](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 사용할 수 있습니다.

#### <a name="application-proxy-connector-monitoring"></a>애플리케이션 프록시 커넥터 모니터링

커넥터와 서비스는 모든 고가용성 작업을 처리합니다. Azure AD 포털의 응용 프로그램 프록시 페이지에서 커넥터의 상태를 모니터링할 수 있습니다. 커넥터 유지 관리에 대한 자세한 내용은 [Azure AD 응용 프로그램 프록시 커넥터 이해를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)참조하십시오.

![예: Azure AD 응용 프로그램 프록시 커넥터](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 이벤트 로그 및 성능 카운터

커넥터에는 관리자 로그와 세션 로그가 모두 있습니다. 관리 로그에는 주요 이벤트와 해당 오류가 포함됩니다. 세션 로그에는 모든 트랜잭션 및 처리 세부 정보가 포함됩니다. 로그와 카운터는 Windows 이벤트 로그에 있으며 자세한 내용은 [Azure AD 응용 프로그램 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)를 참조하십시오. 이 [자습서를 수행하여 Azure 모니터 에서 이벤트 로그 데이터 원본을 구성합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)

### <a name="troubleshooting-guide-and-steps"></a>문제 해결 가이드 및 단계

일반적인 문제와 오류 메시지 [문제 해결](application-proxy-troubleshoot.md) 가이드를 통해 문제를 해결하는 방법에 대해 자세히 알아보십시오. 

다음 문서에서는 지원 조직에 대한 문제 해결 가이드를 만드는 데 사용할 수 있는 일반적인 시나리오를 다룹니다. 

* [앱 페이지를 표시할 때 문제](application-proxy-page-appearance-broken-problem.md)
* [너무 긴 애플리케이션 로드](application-proxy-page-load-speed-problem.md)
* [애플리케이션 페이지에서 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)
* [내 앱에 대한 포트 열기 작업](application-proxy-connectivity-ports-how-to.md)
* [내 앱에 대한 커넥터 그룹에서 작동하지 않는 커넥터](application-proxy-connectivity-no-working-connector.md)
* [관리자 포털에서 구성](application-proxy-config-how-to.md)
* [내 앱에 Single Sign-On 구성](application-proxy-config-sso-how-to.md)
* [관리 포털에서 앱을 만들 때 문제](application-proxy-config-problem.md)
* [Kerberos 제한된 위임 구성](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [PingAccess로 구성](application-proxy-back-end-ping-access-how-to.md)
* [이 회사 응용 프로그램 오류에 액세스할 수 없습니다.](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [애플리케이션 프록시 에이전트 커넥터 설치 시 문제](application-proxy-connector-installation-problem.md)
* [로그인 문제](application-sign-in-problem-on-premises-application-proxy.md)
