---
title: Azure Active Directory 응용 프로그램 프록시 배포 계획
description: 조직 내에서 응용 프로그램 프록시 배포를 계획 하는 종단 간 가이드
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 04a2e9968e8716818637a34adea86de88e1f848c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388317"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD 응용 프로그램 프록시 배포 계획

Azure Active Directory (Azure AD) 응용 프로그램 프록시는 온-프레미스 응용 프로그램에 대 한 원격 액세스를 안전 하 고 비용 효율적인 솔루션입니다. 레거시에 대 한 액세스를 관리 하는 "클라우드 첫 번째" 조직 위한 즉시 전환 경로 온-프레미스 최신 프로토콜을 사용할 수 없는 아직 되지 않은 응용 프로그램을 제공 합니다. 자세한 소개를 참조 하세요 [응용 프로그램 프록시 란](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)합니다.

내부 리소스에 원격 사용자가 액세스할 수 있도록 하는 것에 대 한 응용 프로그램 프록시를 사용 하는 것이 좋습니다. 응용 프로그램 프록시는 VPN이 필요 하거나 이러한 원격 액세스 사용 사례에 대 한 역방향 프록시를 대체합니다. 회사 네트워크에 있는 사용자는 것이 아닙니다. 인트라넷 액세스에 대 한 응용 프로그램 프록시를 사용 하는 이러한 사용자는 바람직하지 않은 성능 문제가 있을 수 있습니다.

이 문서에서는 계획, 운영 및 Azure AD 응용 프로그램 프록시를 관리 하는 데 필요한 리소스를 포함 합니다. 

## <a name="plan-your-implementation"></a>구현 계획

다음 섹션을 효율적으로 배포 환경에 대해 설정 하는 요소를 계획 하는 키의 광범위 한 볼 수 있습니다. 

### <a name="prerequisites"></a>필수 조건

구현 시작 하기 전에 다음 필수 조건을 충족 해야 합니다. 이러한 필수 구성이 요소를 포함 하 여이 환경을 설정에서 자세한 정보를 볼 수 있습니다 [자습서](application-proxy-add-on-premises-application.md)합니다.

* **커넥터**: 커넥터를 배포할 수 있는 경량 에이전트 같습니다.
   * 물리적 하드웨어를 온-프레미스
   * 모든 하이퍼바이저 솔루션 내에서 호스트 되는 VM
   * 응용 프로그램 프록시 서비스로 아웃 바운드 연결을 사용 하도록 설정 하려면 Azure에서 호스트 되는 VM.

* 참조 [이해 Azure AD 앱 프록시 커넥터](application-proxy-connectors.md) 에 대 한 자세한 개요입니다.

     * 커넥터 컴퓨터 해야 [TLS 1.2에 대해 사용할](application-proxy-add-on-premises-application.md) 커넥터를 설치 하기 전에 합니다.

     * 가능한 경우에 커넥터를 배포 합니다 [동일한 네트워크](application-proxy-network-topology.md) 및 백 엔드 웹 응용 프로그램 서버를 세그먼트입니다. 응용 프로그램의 검색을 완료 한 후 커넥터를 배포 하는 것이 좋습니다.
     * 각 커넥터 그룹에 두 개 이상의 커넥터 고가용성 및 확장성을 제공 하는 것이 좋습니다. 세 명의 커넥터 언제 든 지 컴퓨터를 서비스 해야 하는 경우 적합 합니다. 검토 합니다 [커넥터 용량 테이블](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) 에서 커넥터를 설치 하려면 컴퓨터의 유형을 결정에 도움이 되도록 합니다. 클수록 더 많은 버퍼 컴퓨터 및 고성능 커넥터가 됩니다.

* **네트워크 액세스 설정**: Azure AD 응용 프로그램 프록시 커넥터 [HTTPS (TCP 포트 443) 및 HTTP (TCP 포트 80)를 통해 Azure에 연결](application-proxy-add-on-premises-application.md)합니다. 

   * 종료 커넥터 TLS 트래픽 지원 되지 않으며 커넥터는 각 Azure 앱 프록시 끝점을 사용 하 여 보안 채널 설정에서 수 없게 됩니다.

   * 모든 형태의 커넥터와 Azure 간의 아웃 바운드 TLS 통신에서 인라인 검사 하지 않습니다. 내부 검사 커넥터와 백 엔드 응용 프로그램 간의 사용자 환경이 저하 될 수 있고 따라서 권장 되지 않습니다 가능 합니다.

   * 자체 커넥터의 부하 분산도 지원 되지 않습니다에 필요 합니다.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 구성 하기 전에 중요 한 고려 사항

다음과 같은 핵심 요구 사항은 구성 하 고 Azure AD 응용 프로그램 프록시를 구현 하기 위해 충족 되어야 합니다.

*  **Azure 온 보 딩**: 응용 프로그램 프록시를 배포 하기 전에 사용자 id는 온-프레미스 디렉터리에서 동기화 하거나 Azure AD 테 넌 트 내에서 직접 만들 수 해야 합니다. Id 동기화는 Azure AD를 미리 게시 된 응용 프로그램을 앱 프록시에 대 한 액세스 권한을 부여 하기 전에 사용자를 인증 하 고에서 single sign-on (SSO) 하는 데 필요한 사용자 식별자 정보를가지고 있습니다.

* **조건부 액세스 요구 사항**: 이 사용자에 영향을 주는 대기 시간을 추가 하기 때문에 인트라넷 액세스에 대 한 응용 프로그램 프록시를 사용 하는 것은 좋지 않습니다. 인터넷에서 원격 액세스를 위한 사전 인증 및 조건부 액세스 정책을 사용 하 여 응용 프로그램 프록시를 사용 하는 것이 좋습니다.  인트라넷에서 사용에 대 한 조건부 액세스 diretly 할 수 있는 응용 프로그램을 현대화 하는 것을 제공 하는 방법은 AAD로 인증 합니다. 가리킵니다 [마이그레이션에 대 한 리소스 AAD에 응용 프로그램](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 자세한 내용은 합니다. 

* **서비스 제한 사항**: 으로부터 보호 하기 위해 믿지 있습니다 개별 테 넌 트에서 리소스 제한은 응용 프로그램 및 테 넌 트 별로 설정 됩니다. 참조 하는 이러한 한도 보려는 [Azure AD 서비스 제한 및 제한 사항](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)합니다. 이러한 제한 한도 일반적인 사용량 맨 위에 벤치 마크를 기반으로 하 고는 대부분의 배포에 대 한 충분 한 버퍼를 제공 합니다.

* **공용 인증서**: 사용자 지정 도메인 이름을 사용 하는 경우 신뢰할 수 있는 타사 인증 기관에서 발급 된 공용 인증서를 확보 해야 합니다. 조직 요구 사항에 따라 인증서를 가져오는 시간이 걸릴 수 있으며 최대한 빨리 프로세스를 시작 하는 것이 좋습니다. Azure 응용 프로그램 프록시는 표준 지원 [와일드 카드](application-proxy-wildcard.md), 또는 인증서 SAN 기반 합니다.

* **도메인 요구 사항**: Kerberos 제한 위임 (KCD)을 사용 하 여 게시 된 응용 프로그램에서 single sign-on 커넥터 호스트는 게시 중인 응용 프로그램과 동일한 AD 도메인에 가입 해야 합니다. 항목에 대 한 자세한 내용은 참조 하세요. [single sign on에 대 한 KCD](application-proxy-configure-single-sign-on-with-kcd.md) 응용 프로그램 프록시를 사용 합니다. 커넥터 서비스 로컬 시스템 컨텍스트에서 실행 하 고 사용자 지정 id를 사용 하도록 구성 되지 해야 합니다.

* **Url에 대 한 DNS 레코드**

   * 응용 프로그램 프록시에서 사용자 지정 도메인을 사용 하기 전에 클라이언트가 미리 정의 된 응용 프로그램 프록시 주소로 사용자 지정 정의 된 외부 URL을 확인 하도록 허용 하는 공용 DNS에서 CNAME 레코드를 만들어야 합니다. 사용자 지정 도메인을 사용 하는 응용 프로그램에 대 한 CNAME 레코드를 만들 수 없는 원격 사용자가 응용 프로그램에 연결할 수 없게 됩니다. DNS 공급자에에서 다를 수 있습니다 CNAME 레코드를 추가 하는 데 필요한 단계에 알아봅니다 따라서 하는 방법 [Azure portal을 사용 하 여 DNS 레코드 및 레코드 집합 관리](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)합니다.

   * 마찬가지로, 커넥터 호스트에 게시 되는 응용 프로그램의 내부 URL을 확인할 수 있어야 합니다.

* **관리 권한 및 역할**

   * **커넥터 설치** 에 설치 된 Windows 서버에 로컬 관리자 권한이 필요 합니다. 또한 최소 필요는 *응용 프로그램 관리자* 역할의 인증 및 Azure AD 테 넌 트 커넥터 인스턴스를 등록 합니다. 

   * **응용 프로그램 게시 및 관리** 필요 합니다 *응용 프로그램 관리자* 역할입니다. 응용 프로그램 관리자는 등록, SSO 설정, 사용자 및 그룹 할당 및 라이선스, 응용 프로그램 프록시 설정 및 동의 포함 하는 디렉터리의 모든 응용 프로그램을 관리할 수 있습니다. 조건부 액세스를 관리 하는 기능을 부여 하지 것입니다. 합니다 *클라우드 응용 프로그램 관리자* 역할에서는 모든 기능이 응용 프로그램 관리자의 점을 제외 하 고 응용 프로그램 프록시 설정 관리할 수는 없습니다.

* **라이선스**: 응용 프로그램 프록시는 Azure AD Basic 정기 가입을 통해 사용할 수 있습니다. 참조 된 [Azure Active Directory 가격 페이지](https://azure.microsoft.com/pricing/details/active-directory/) 옵션 및 기능 라이선스의 전체 목록은 합니다.  

### <a name="application-discovery"></a>응용 프로그램 검색

다음 정보를 수집 하 여 응용 프로그램 프록시를 통해 게시 되는 모든 범위에서 응용 프로그램의 인벤토리를 컴파일하십시오.

| 정보 유형| 수집할 정보 |
|---|---|
| 서비스 유형| 예를 들면 다음과 같습니다. SharePoint, SAP, CRM, 사용자 지정 웹 응용 프로그램, API |
| 애플리케이션 플랫폼 | 예를 들면 다음과 같습니다. Windows IIS, Apache Tomcat, Linux, NGINX를 |
| 도메인 멤버 자격| 웹 서버의 정규화 된 도메인 이름 (FQDN) |
| 응용 프로그램 위치 | 인프라에서 웹 서버 또는 팜에 있는 위치 |
| 내부 액세스 | 내부적으로 응용 프로그램에 액세스할 때 사용 되는 정확한 URL입니다. <br> 팜에 어떤 유형의 부하 분산 사용 중인지 여부? <br> 여부는 응용 프로그램 자체 이외의 원본에서 콘텐츠를 그립니다.<br> Websocket을 통해 응용 프로그램이 작동 하는 경우를 결정 합니다. |
| 외부 액세스 | 응용 프로그램이 이미에 노출 되는 외부 공급 업체 솔루션입니다. <br> 외부 액세스에 대 한 사용 하려는 URL입니다. 하는 경우 SharePoint 대체 액세스 매핑 당 구성 되었는지 확인 합니다 [이 지침](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)합니다. 그렇지 않은 경우에 외부 Url을 정의 해야 합니다. |
| 공용 인증서 | 사용자 지정 도메인을 사용 하는 경우에 해당 주체 이름 가진 인증서를 확보 합니다. 인증서가 있으면 note 일련 번호 및 위치를 가져올 수 있습니다. |
| 인증 유형| Basic, Windows 통합 인증, 폼 기반, 헤더를 기반으로 클레임 등 응용 프로그램 지원에서 지원 되는 인증 형식입니다. <br>응용 프로그램은 특정 도메인 계정으로 실행 되도록 구성 하는 경우는 완전히 정규화 된 도메인 이름 (FQDN)의 서비스 계정 note 합니다.<br> SAML 기반 일 경우, 식별자 및 회신 Url입니다. <br> 헤더를 기반으로 하는 경우에 공급 업체 솔루션 및 인증을 처리 하는 것에 대 한 특정 요구를 입력 합니다. |
| 커넥터 그룹 이름 | 이 백 엔드 응용 프로그램에 SSO 고 통로 제공 하도록 지정 된 수는 커넥터 그룹에 대 한 논리적 이름입니다. |
| 사용자/그룹 액세스 | 사용자 또는 응용 프로그램에 대 한 외부 액세스를 부여 되는 사용자 그룹입니다. |
| 추가 요구 사항 | 모든 추가 원격 액세스 또는 응용 프로그램을 게시에 포함 시켜야 하는 보안 요구 사항을 note 합니다. |

다운로드할 수 있습니다 [응용 프로그램 인벤토리 스프레드시트](https://aka.ms/appdiscovery) 앱 인벤토리를 합니다.

### <a name="define-organizational-requirements"></a>조직의 요구 사항 정의

다음은 영역은 조직의 비즈니스 요구 사항을 정의 해야 합니다. 각 영역에는 요구 사항 예가 포함 되어 있습니다.

 **액세스 권한**

* 도메인 가입을 사용 하 여 원격 사용자 또는 Azure AD 가입 장치 사용자는 안전 하 게 사용 하 여 원활 하 게에서 single sign-on (SSO) 게시 된 응용 프로그램을 액세스할 수 있습니다.

* 안전 하 게 승인 된 개인 장치가 있는 원격 사용자는 MFA에 등록 된 인증 방법으로 휴대폰에 Microsoft Authenticator 앱을 등록 한 제공 게시 된 응용 프로그램을 액세스할 수 있습니다.

**거 버 넌 스** 

* 관리자가 정의 하 고 응용 프로그램 프록시를 통해 게시 된 응용 프로그램에 사용자 할당의 수명 주기를 모니터링할 수 있습니다.

**보안**

* 사용자만 그룹 멤버 자격을 통해 응용 프로그램에 할당 하거나 개별적으로 해당 응용 프로그램에 액세스할 수 있습니다.

**성능**

* 내부 네트워크에서 응용 프로그램 액세스에 비해 응용 프로그램 성능 저하 없이 있습니다.

**사용자 환경**

* 사용자가 모든 장치 플랫폼의 친숙 한 회사 Url을 사용 하 여 응용 프로그램에 액세스 하는 방법을 알아야 합니다.

**감사**
* 관리자는 사용자 액세스 활동 감사 수 있습니다.


### <a name="best-practices-for-a-pilot"></a>파일럿에 대 한 모범 사례

단일 응용 프로그램에서 Single sign-on (SSO)을 사용 하 여 원격 액세스를 위한 완전히 수수료 하는 데 필요한 시간과 양을 결정 합니다. 해당 초기 검색, 게시 및 일반 테스트를 고려 하는 파일럿을 실행 하 여 수행 합니다. 이미 통합 Windows 인증 (IWA)에 대 한 미리 구성 된 간단한 IIS 기반 웹 응용 프로그램을 사용 하는 데 도움이 됩니다 기준선을이 설치 하려면 성공적으로 파일럿 원격 액세스 및 SSO에 최소한의 노력으로 합니다.

다음과 같은 디자인 요소는 프로덕션 테 넌 트에 직접 구현 파일럿의 성공 여부를 증가 해야 합니다.  

**커넥터 관리**:  

* 커넥터 중요 한 역할 응용 프로그램에 온-프레미스 통로 제공 합니다. 사용 하는 **기본** 커넥터 그룹은 초기 파일럿 테스트 게시 된 응용 프로그램을 프로덕션 환경에 위임 하기 전에 적합 합니다. 그런 다음 프로덕션 커넥터 그룹에 성공적으로 테스트 대상된 응용 프로그램을 이동할 수 있습니다.

**응용 프로그램 관리**:

* 직원에 게 친숙 하 고 관련 된 외부 URL은 기억 하기 가장 가능성이 높습니다. 미리 정의 된 msappproxy.net 또는 onmicrosoft.com 접미사를 사용 하 여 응용 프로그램을 게시 하지 마십시오. 대신 제공 하는 친숙 한 최상위 확인된 된 도메인 논리 호스트 이름에 접두사가 *인트라넷. < customers_domain > c o m*합니다.

* 시작 아이콘 형태로 Azure MyApps 포털을 숨겨 파일럿 그룹으로 파일럿 응용 프로그램의 아이콘의 표시 여부를 제한 합니다. 프로덕션에 대 한 준비가 되 면 해당 각 대상된 고객에 게 동일한 사전 프로덕션 테 넌 트에서 또는 프로덕션 테 넌 트에서 응용 프로그램을 게시 하 여 앱을 범위 지정할 수 있습니다.

**Single sign-on이 설정**: 일부 SSO 설정에 특정 종속성이 있으므로 종속성 함으로써 지연 미리 사항은 변경 제어 하지 않도록 설정 하려면 시간이 걸릴 수 있습니다. 도메인 가입 SSO Kerberos 제한 위임 (KCD)을 사용 하 여 및 기타 시간이 오래 걸리는 작업을 처리 하는 데 커넥터 호스트 포함 됩니다. 예를 들어 헤더 기반 SSO를 해야 하는 경우 Pingaccess 인스턴스를 설정 합니다.

**커넥터 호스트와 대상 응용 프로그램 간에 SSL**: 보안 가장 중요 하며 이므로 항상 커넥터 호스트와 대상 응용 프로그램 간에 TLS를 사용 해야 합니다. 폼 기반 인증, FBA ()에 대 한 웹 응용 프로그램은 구성 하는 경우에 특히 사용자 자격 증명은 다음 효과적으로 일반 텍스트로 전송 합니다.

**증분 방식으로 구현 하 고 각 단계를 테스트**합니다. 아래 지침에 따라 모든 사용자 및 비즈니스 요구 사항을 충족 하도록 응용 프로그램을 게시 한 후 기본 기능 테스트를 수행 합니다.

1. 테스트 하 고 사용 하지 않도록 설정 하는 사전 인증을 사용 하 여 웹 응용 프로그램에 대 한 일반 액세스를 확인 합니다.
2. 성공 하는 경우 사전 인증 및 할당 사용자 및 그룹을 사용 하도록 설정 합니다. 테스트 하 고 액세스의 유효성을 검사 합니다.
3. 그런 다음 응용 프로그램에 대 한 SSO 메서드를 추가 하 고 액세스 유효성 검사를 다시 테스트.
4. 조건부 액세스 및 필요에 따라 MFA 정책을 적용 합니다. 테스트 하 고 액세스의 유효성을 검사 합니다.

**문제 해결 도구**: 이 문제를 해결 하는 경우 항상 커넥터 호스트에서 브라우저에서 게시 된 응용 프로그램에 대 한 액세스를 검사 하 여 시작 하 고 응용 프로그램이 예상 대로 작동 하는지 확인 합니다. 단순할수록 근본 원인을 확인 하는 쉽게 설치, 따라서 고려 같은 하나의 커넥터만 및 없습니다 SSO를 사용 하 여 최소 구성으로 문제를 재현 하 합니다. 경우에 따라 Telerik의 Fiddler와 같은 도구를 디버깅 하는 웹 프록시를 통해 액세스 하는 응용 프로그램에 대 한 액세스 또는 콘텐츠 문제를 해결 하는 필수적인 입증할 수 있습니다. Fiddler 추적 하는 데 도움이 하 여 iOS 및 Android와 같은 모바일 플랫폼에 대 한 트래픽을 디버그 프록시 역할도 할 수 있습니다 및 거의 아무 것도 구성할 수 있는 프록시를 통해 경로에 있습니다. 참조 된 [문제 해결 가이드](application-proxy-troubleshoot.md) 자세한 내용은 합니다.

## <a name="implement-your-solution"></a>솔루션 구현

### <a name="deploy-application-proxy"></a>응용 프로그램 프록시 배포

이 응용 프로그램 프록시를 배포 하는 단계를 나와 [원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 하기 위한 자습서](application-proxy-add-on-premises-application.md)합니다. 설치 성공 아니면 선택 **응용 프로그램 프록시 문제 해결** 포털 또는 문제 해결 가이드를 사용 하 여 [응용 프로그램 프록시 에이전트 커넥터를 설치 하는 문제에 대 한](application-proxy-connector-installation-problem.md)합니다.

### <a name="publish-applications-via-application-proxy"></a>응용 프로그램 프록시를 통해 응용 프로그램 게시

모든 필수 조건을 충족 하 고 응용 프로그램 프록시 페이지에 여러 커넥터를 등록 하는 것으로 표시 및 활성 있는지 가정 응용 프로그램을 게시 합니다.

사용 하 여 응용 프로그램을 게시할 수도 있습니다 [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)합니다.

응용 프로그램을 게시할 때 따라야 할 몇 가지 모범 사례는 다음과 같습니다.

* **커넥터 그룹을 사용 하 여**: 각각의 응용 프로그램을 게시 하는 것에 대 한 지정 된 커넥터 그룹을 할당 합니다. 각 커넥터 그룹에 두 개 이상의 커넥터 고가용성 및 확장성을 제공 하는 것이 좋습니다. 세 명의 커넥터 언제 든 지 컴퓨터를 서비스 해야 하는 경우 적합 합니다. 또한 볼 [별도 네트워크 및 커넥터 그룹을 사용 하 여 위치에서 응용 프로그램 게시](application-proxy-connector-groups.md) 방법을 사용할 수도 있습니다 커넥터 그룹 네트워크 또는 위치에서 커넥터 세그먼트를 볼 수 있습니다.

* **백 엔드 응용 프로그램 시간 제한 설정**: 이 설정은 응용 프로그램 클라이언트 트랜잭션을 처리 하는 데 75 초 이상 필요할 수 있는 시나리오에서 유용 합니다. 예를 들어 클라이언트에 보낼 때 쿼리 웹 응용 프로그램에는 데이터베이스에 대 한 프런트 엔드로 작동 합니다. 프런트 엔드는 백 엔드 데이터베이스 서버에이 쿼리를 보내고 응답을 기다리는 했지만 응답을 받을 시간, 클라이언트 쪽의 대화 시간이 초과 합니다. 긴 트랜잭션이 완료 180 초 동안 제공 하는 제한 시간을 설정 합니다.

* **적절 한 쿠키 형식 사용**

   * **HTTP 전용 쿠키**: Set-cookie HTTP 응답 헤더에 HTTPOnly 플래그를 포함 하는 응용 프로그램 프록시를 배치 하 여 추가 보안을 제공 합니다. 사이트 간 스크립팅 (XSS) 같은 악용을 완화 하기 위해이 설정을 사용 하면 됩니다. 세션 쿠키에 액세스할 필요가 있는 클라이언트/사용자 에이전트에는 아니요를이 설정 된 채로 있습니다. 예를 들어 원격 데스크톱 게이트웨이를 연결 하는 RDP/MTSC 클라이언트 앱 프록시를 통해 게시 합니다.

   * **쿠키 보안**: 쿠키가 보안 특성으로 설정 되 면 사용자 에이전트 (클라이언트 쪽 앱)만 포함 됩니다 쿠키가 HTTP 요청에서 요청 TLS 보안된 채널을 통해 전송 되는 경우. 이렇게 해야 사용할 수 있도록 일반 텍스트로 채널을 통해 손상 되지 않도록 하는 쿠키의 위험을 완화 합니다.

   * **영구 쿠키**: 응용 프로그램 프록시 세션 쿠키를 만료 되거나 삭제 될 때까지 유효한 유지 함으로써 브라우저 클로저 사이 지속할 수 있습니다. 사용 시나리오에 대 한 office와 같은 다양 한 응용 프로그램을 다시 인증 하 라는 메시지가 표시 되는 사용자 없이 게시 된 웹 응용 프로그램, 내 문서에 액세스 하는 위치입니다. 그러나 주의 해 서 사용 영구 쿠키로 궁극적으로 그대로 둘 수는 서비스에 무단된 액세스 위험을 보정 다른 컨트롤과 함께에서 사용 되지 않는 경우. 이 설정은 쿠키 프로세스 간에 공유할 수 없습니다는 이전 응용 프로그램에 사용할 해야 합니다. 이 설정을 사용 하는 대신 프로세스 간에 공유 쿠키를 처리 하도록 응용 프로그램을 업데이트 하는 것이 좋습니다.

* **헤더의 Url 변환**: 이렇게 하려면 시나리오는 조직의 공용 네임 스페이스 (또는 분할 DNS)에 맞게 내부 DNS를 구성할 수 없습니다. 응용 프로그램에서 클라이언트 요청에서 원래 호스트 헤더에 필요한 경우가 아니면 Yes로 설정 하는이 값을 그대로 둡니다. 대안은 내부 URL의 FQDN을 사용 하 여 실제 트래픽을 호스트 헤더로 외부 URL에 FQDN의 라우팅에 대 한 커넥터가 있어야 합니다. 대부분의 경우에서이 대안을 허용 해야 응용 프로그램 작동을 정상적으로 원격으로 액세스할 때 하지만 사용자 손실 일치 하는 내부 및 외부 URL의 이점입니다.

* **애플리케이션 본문의 URL 변환**: 클라이언트에 다시 응답 번역할 수 있도록 해당 앱에서 링크 하려는 경우 앱에 대 한 응용 프로그램 본문 링크 변환을 설정 합니다. 사용 하도록 설정 하는 경우이 함수는 앱 프록시 클라이언트로 반환 되는 HTML 및 CSS 응답에서 발견 된 모든 내부 링크 변환에서 시도 하는 최상의 노력을 제공 합니다. 이 경우 하드 코드 된 절대 또는 콘텐츠 전체에서 NetBIOS 짧은 이름 링크를 포함 하는 앱 또는 다른 연결 하는 콘텐츠를 사용 하 여 앱 게시 온-프레미스 응용 프로그램에 유용 합니다.

시나리오는 게시 된 앱 링크를 다른 앱을 게시 하는 위치에 대 한 앱 별 수준에 게 사용자 환경 제어할 수 있도록 각 응용 프로그램에 대해 링크 변환을 사용 하도록 설정 합니다.

예를 들어 애플리케이션 프록시를 통해 게시된 다음과 같은 세 개의 애플리케이션이 있고 모두 서로 연결되어 있다고 가정합니다. 혜택, 비용, 이동, 및 피드백을 응용 프로그램 프록시를 통해 게시 되지 않은 네 번째 앱을 합니다.

![그림 1](media/App-proxy-deployment-plan/link-translation.png)

Benefits 앱에 대해 링크 변환을 사용 하도록 설정 하면 회사 네트워크 외부에서 응용 프로그램에 액세스 하는 사용자가 액세스할 수 있도록 Expenses 및 Travel에 대 한 링크를 해당 앱에 대 한 외부 Url로 리디렉션됩니다. Expenses 및 Travel에서 Benefits로 다시 링크는 이러한 두 앱에 대 한 링크 변환을 사용할 수 있으므로 작동 하지 않습니다. Benefits 앱을 사용 하 여 사용자가 회사 네트워크 외부에서 피드백 앱에 액세스할 수 없습니다 있도록 없습니다 외부 URL을 되어 피드백 링크를 리디렉션할 되지 않습니다. 에 대 한 자세한 내용은 [번역 및 기타 리디렉션 옵션 연결](application-proxy-configure-hard-coded-link-translation.md)합니다.

### <a name="access-your-application"></a>응용 프로그램에 액세스

액세스를 관리 하기 위한 여러 옵션이 있는 앱 프록시로 게시 된 리소스 선택 가장 적합 한 시나리오 및 확장성 요구 사항이 지정된 합니다. 일반적인 접근 방식을 포함 합니다: Azure AD Connect를 통해 동기화 되는 온-프레미스 그룹을 사용 하 여 Azure AD 동적 그룹을 만든 사용자 특성을 기반으로 리소스 소유자 또는이 모두의 조합에서 관리 하는 셀프 서비스 그룹을 사용 하 여 합니다. 각각의 이점에 대 한 연결된 된 리소스를 참조 하세요.

에 가장 직접적인 방법은 응용 프로그램에 사용자 액세스를 할당 하는 것은 **사용자 및 그룹** 게시 된 응용 프로그램 및 그룹이 나 개인을 직접 할당의 왼쪽 창에서 옵션입니다.

![그림 24](media/App-proxy-deployment-plan/add-user.png)

또한 그룹의 멤버가 아닌 현재에 할당 하 고 셀프 서비스 옵션을 구성 하 여 응용 프로그램에 사용자가 셀프 서비스 액세스를 허용할 수 있습니다.

![그림 25](media/App-proxy-deployment-plan/allow-access.png)

사용 하도록 설정 하는 경우 사용자는 다음 MyApps 포털 및 요청 액세스에 로그인 할 수 있고 하나 되어야 자동으로 승인 하 고 지정 된 승인자 로부터 승인 필요를 이미 허용 된 셀프 서비스 그룹에 추가 합니다.

게스트 사용자 일 수도 있습니다 [Azure AD B2B를 통해 응용 프로그램 프록시를 통해 게시 된 내부 응용 프로그램에 액세스 하도록 초대](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)합니다.

익명으로 일반적으로 액세스할 수 있는 프레미스 응용 프로그램에 없는 인증을 요구 좋습니다 응용 프로그램에 있는 옵션을 사용 하지 않도록 설정에 대 한 **속성**합니다.

![그림 26](media/App-proxy-deployment-plan/assignment-required.png)


이 옵션을 아니요로 설정 유지를 사용 하면 권한이 없는 Azure AD 앱 프록시를 통해 온-프레미스 응용 프로그램에 액세스, 따라서 주의 해 서 사용 하 여 수 있습니다.

응용 프로그램 게시 되 면이 액세스할 수 있어야에서 해당 아이콘 또는 브라우저에서 외부 URL을 입력 하 여 [ https://myapps.microsoft.com ](https://myapps.microsoft.com/)합니다.

### <a name="enable-pre-authentication"></a>사전 인증을 사용 하도록 설정

응용 프로그램에 외부 URL을 통해 액세스 하는 응용 프로그램 프록시를 통해 액세스할 수 있는지 확인 합니다. 

1. **Azure Active Directory** > **Enterprise 애플리케이션** > **모든 애플리케이션**으로 이동하고 관리하려는 앱을 선택합니다.

2. **애플리케이션 프록시**를 선택합니다.

3. 에 **사전 인증** 필드에서 드롭다운 목록을 사용 하 여 선택 **Azure Active Directory**를 선택 하 고 **저장**합니다.

사전 인증 사용, Azure AD는 사용자 먼저 인증에 대 한 하며 이면 single sign on configued 다음 백 엔드 응용 프로그램은 또한 확인 사용자 응용 프로그램에 액세스 하기. 처음에 HTTP 용으로 구성 하는 모든 응용 프로그램은 이제 HTTPS를 사용 하 여 보안 됩니다 하도록 외부 URL을 HTTPS를 사용 하 여 구성도 Azure ad에서 통과 사전 인증 모드를 변경 합니다.

### <a name="enable-single-sign-on"></a>Single Sign-On 사용

SSO는 사용자만 Azure AD에 액세스할 때 한 번에 로그인 해야 하기 때문에 최상의 사용자 환경 및 보안을 제공 합니다. 사용자는 사전 인증 되 면 SSO는 응용 프로그램 프록시 커넥터 인증 사용자를 대신 하 여 온-프레미스 응용 프로그램에 의해 수행 됩니다. 백 엔드 응용 프로그램 사용자 자체 것 처럼 로그인을 처리 합니다. 

선택 된 **통과** 옵션을 사용 하면 Azure AD에 인증 필요 없이 게시 된 응용 프로그램에 액세스할 수 있습니다.

SSO를 수행 하는 Azure AD 응용 프로그램 미리 함수 SSO에 대 한 액세스 시 Azure AD 사용 하 여 사용자를 인증 하도록 구성 해야 하므로 리소스에 대 한 액세스를 요청 하는 사용자를 식별할 수, 그렇지 않으면 SSO 옵션을 사용할 수 없습니다 수만 있습니다.

읽기 [Azure AD에서 응용 프로그램에서 Single sign-on](what-is-single-sign-on.md) 응용 프로그램을 구성 하는 경우 가장 적절 한 SSO 메서드를 선택할 수 있도록 합니다.

###  <a name="working-with-other-types-of-applications"></a>다른 유형의 응용 프로그램 사용

Azure AD 응용 프로그램 프록시는 Azure AD 인증 라이브러리를 사용 하도록 개발 된 응용 프로그램을 지원할 수도 있습니다 ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) 또는 Microsoft 인증 라이브러리 ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). 사용자를 대신 하 여 사전 인증을 수행 하는 클라이언트 요청의 헤더 정보에서 받은 토큰을 발급 하는 Azure AD를 사용 하 여 네이티브 클라이언트 앱을 지원 합니다.

읽기 [네이티브 및 모바일 클라이언트 앱을 게시](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) 하 고 [클레임 기반 응용 프로그램](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) 응용 프로그램 프록시의 사용 가능한 구성에 대해 자세히 알아보려면 합니다.

### <a name="use-conditional-access-to-strengthen-security"></a>조건부 액세스를 사용 하 여 보안을 강화

응용 프로그램 보안에는 고급 보안 기능 집합에서 보호 하 고 응답할 수 있는 복잡 한 위협 온-프레미스 및 클라우드에서 필요 합니다. 공격자는 대부분의 약한, default 또는 도난 당한 사용자 자격 증명을 통해 회사 네트워크 액세스할을 수 있습니다.  Microsoft id 기반의 보안 관리 및 권한 있는 및 권한 없는 id를 보호 하 여 도난된 된 자격 증명의 사용을 줄입니다.

Azure AD 응용 프로그램 프록시를 지원 하기 위해 다음과 같은 기능을 사용할 수 있습니다.

* 사용자 및 위치 기반 조건부 액세스: 지리적 위치에서 사용 하 여 IP 주소에 대 한 사용자 액세스를 제한 하 여 보호 된 중요 한 데이터 유지 [위치 기반 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)합니다.

* 장치 기반 조건부 액세스: 등록, 승인 및 규격 장치만 사용 하 여 회사 데이터에 액세스할 수 있게 [장치 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)합니다.

* 응용 프로그램 기반 조건부 액세스: 작업은 사용자는 회사 네트워크에 없는 경우 중지 필요가 없습니다. [회사의 클라우드 및 온-프레미스 앱에 대 한 액세스 보호](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) 및 조건부 액세스를 사용 하 여 컨트롤을 유지 관리 합니다.

* 위험 기반 조건부 액세스: 사용 하 여 악의적인 해커 로부터 데이터 보호를 [위험 기반 조건부 액세스 정책](https://www.microsoft.com/cloud-platform/conditional-access) 적용할 수 있는 모든 앱 및 모든 사용자에 게 여부 온-프레미스 또는 클라우드에서 합니다.

* Azure AD 액세스 패널: 배포 응용 프로그램 프록시 서비스와 안전 하 게 게시 하는 응용 프로그램을 사용 하 여 사용자가 검색 하 고 모든 응용 프로그램에 액세스 하려면 간단한 허브를 제공 합니다. 새 앱 및 그룹에 대 한 액세스를 요청 하거나 다른 사용자를 대신 하 여 이러한 리소스에 대 한 액세스를 통해 관리 하는 기능과 같은 셀프 서비스 기능으로 생산성 증가 합니다 [액세스 패널](https://aka.ms/AccessPanelDPDownload)합니다.

## <a name="manage-your-implementation"></a>구현을 관리 하기

### <a name="required-roles"></a>필요한 역할

Microsoft Azure AD 사용 하 여 필요한 작업을 수행할 가능한 최소 권한을 부여 하는 원칙을 대표 합니다. [사용할 수 있는 다양 한 Azure 역할 검토](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 적합 한 각 가상 사용자의 요구를 충족 하도록 선택 합니다. 일부 역할을 일시적으로 적용 되어 배포가 완료 되 면 제거 해야 합니다.

| 비즈니스 역할| 비즈니스 작업| Azure AD 역할 |
|---|---|---|
| 기술 지원팀 관리자 도움말 | 일반적으로 한 정하는 제한 보고 최종 사용자 문제, 사용자의 암호를 변경 하 고, 새로 고침 토큰을 무효화 하 고, 서비스 상태 모니터링 등 제한 된 작업을 수행 합니다. | 기술 지원팀 관리자 |
| Identity 관리자| 앱 프록시를 디버깅 하려면 읽기 Azure AD 로그인 보고서 및 감사 로그에서 관련 문제입니다.| 보안 판독기 |
| 애플리케이션 소유자| 엔터프라이즈 응용 프로그램, 응용 프로그램 등록 및 응용 프로그램 프록시 설정의 모든 측면을 만들고 설정 합니다.| 응용 프로그램 관리자 |
| 인프라 관리 | 인증서 롤오버 소유자 | 응용 프로그램 관리자 |

보안 정보 또는 리소스에 대 한 액세스 권한이 있는 사용자 수를 최소화 무단된 액세스 또는 실수로 중요 한 리소스에 영향을 주는 권한 있는 사용자를 가져오는 악의적인 행위자의 가능성을 줄이는 데 도움이 됩니다. 
 
그러나 사용자는 여전히 필요 매일 권한 있는 작업을 수행할 수 있으므로-just-in-time (JIT) 기반 적용 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 주문형으로 제공 하는 정책을 Azure 리소스에 대 한 액세스 권한 및 Azure AD는이 효과적으로 관리 액세스를 관리 하 고 감사 중심 접근 방식을으로 것이 좋습니다.

### <a name="reporting-and-monitoring"></a>보고 및 모니터링

Azure AD 조직의 사용자 사용 현황 및 감사 로그 및 보고서를 통해 작업 상태를 프로 비전에 대 한 추가 정보를 제공할 수 있습니다. 

#### <a name="application-audit-logs"></a>애플리케이션 감사 로그

이러한 로그는 응용 프로그램 프록시 및 장치 및 응용 프로그램을 액세스 하는 사용자를 사용 하 여 구성 하는 응용 프로그램에 로그인 하는 방법에 대 한 자세한 정보를 제공 합니다. 감사 로그 있는 감사 API 및 Azure portal에서 내보내기에 대 한 합니다.

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 이벤트 로그 및 성능 카운터

커넥터 관리 및 세션에 로그 합니다. 관리 로그에는 주요 이벤트와 해당 오류가 포함됩니다. 세션 로그에는 모든 트랜잭션 및 처리 세부 정보가 포함됩니다. 로그 및 카운터는 Windows 이벤트 로그에 있으며이 따라 [Azure Monitor에서 이벤트 로그 데이터 원본을 구성 하는 자습서](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)합니다.

### <a name="troubleshooting-guide-and-steps"></a>문제 해결 가이드 및 단계

일반적인 문제 및 가이드를 사용 하 여 해결 하는 방법에 자세히 알아보려면 [문제 해결](application-proxy-troubleshoot.md) 오류 메시지입니다. 

다음 문서를 만드는 지원 조직에 대 한 문제 해결 가이드도 사용할 수 있는 일반적인 시나리오를 다룹니다. 

* [앱 페이지를 표시할 때 문제](application-proxy-page-appearance-broken-problem.md)
* [너무 긴 애플리케이션 로드](application-proxy-page-load-speed-problem.md)
* [애플리케이션 페이지에서 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)
* [내 앱에 대한 포트 열기 작업](application-proxy-connectivity-ports-how-to.md)
* [내 앱에 대한 커넥터 그룹에서 작동하지 않는 커넥터](application-proxy-connectivity-no-working-connector.md)
* [관리자 포털에서 구성](application-proxy-config-how-to.md)
* [내 앱에 Single Sign-On 구성](application-proxy-config-sso-how-to.md)
* [관리 포털에서 앱을 만들 때 문제](application-proxy-config-problem.md)
* [Kerberos 제한 위임 구성](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [PingAccess로 구성](application-proxy-back-end-ping-access-how-to.md)
* [이 회사 응용 프로그램 오류에 액세스할 수 없습니다.](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [애플리케이션 프록시 에이전트 커넥터 설치 시 문제](application-proxy-connector-installation-problem.md)
* [로그인 문제](application-sign-in-problem-on-premises-application-proxy.md)
