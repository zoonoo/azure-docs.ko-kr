---
title: Active Directory와 Azure Active Directory 연결 | Microsoft Docs
description: Azure AD Connect는 온-프레미스 디렉터리와 Azure Active Directory를 통합니다. 이렇게 하면 Azure AD와 통합된 Office 365, Azure 및 SaaS 응용 프로그램에 대한 공통 ID를 제공할 수 있습니다.
keywords: Azure AD Connect 소개, Azure AD Connect 개요, Azure AD Connect 정의, active directory 설치
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c8ab6b6e6bab7451de7d975dde644386fd4cb84e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311537"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>하이브리드 ID 및 Microsoft ID 솔루션
[Microsoft Azure AD(Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md) 하이브리드 ID 솔루션을 사용하면 온-프레미스의 사용자를 계속 관리하면서 온-프레미스 디렉터리 개체를 Azure AD와 동기화할 수 있습니다. 온-프레미스 Windows Server Active Directory와 Azure AD와의 동기화를 계획할 때는 맨 먼저 관리된 ID를 사용할지 또는 페더레이션 ID를 사용할지를 결정해야 합니다. 

- **ID 관리** - 온-프레미스 Active Directory에서 동기화되는 사용자 계정 및 그룹과 사용자 인증은 Azure에서 관리됩니다.   
- **페더레이션 ID**를 사용하면 Azure에서 사용자 인증을 분리하고 신뢰할 수 있는 온-프레미스 ID 공급자에게 인증을 위임하여 사용자를 보다 철저하게 제어할 수 있습니다. 

하이브리드 ID를 구성하는 데 사용할 수 있는 옵션은 몇 가지가 있습니다. 조직의 요구 사항에 가장 잘 맞는 ID 모델을 고려할 때는 시간, 기존 인프라, 복잡성 및 비용에 대해서도 생각해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 그러나 요구 사항이 변경되면 다른 ID 모델로 전환할 유연성도 있습니다.

## <a name="managed-identity"></a>관리 ID 

관리 ID는 온-프레미스 디렉터리 개체(사용자 및 그룹)를 Azure AD와 통합하는 가장 간단한 방법입니다. 

![동기화된 하이브리드 ID](./media/whatis-hybrid-identity/managed.png)

관리 ID는 가장 쉽고 빠른 방법이지만, 사용자가 여전히 클라우드 기반 리소스에 대한 별도의 암호를 유지 관리해야 합니다. 이를 방지하려면 선택적으로 Azure AD 디렉터리에 대한 [사용자 암호 해시를 동기화](how-to-connect-password-hash-synchronization.md)할 수도 있습니다. 암호 해시를 동기화하면 사용자가 온-프레미스에서 사용하는 것과 같은 사용자 이름 및 암호로 클라우드 기반 조직 리소스에 로그인할 수 있습니다. Azure AD Connect는 온-프레미스 디렉터리에 변경 내용이 있는지 주기적으로 확인하고 Azure AD 디렉터리를 동기화된 상태로 유지합니다. 온-프레미스 Active Directory에서 사용자 특성 또는 암호가 변경되면 Azure AD에서 자동으로 업데이트됩니다. 

Office 365, SaaS 응용 프로그램 및 기타 Azure AD 기반 리소스에 사용자가 로그인할 수 있도록 하기만 하면 되는 대부분 조직의 경우 기본 암호 해시 동기화 옵션을 사용하는 것이 좋습니다. 이 옵션이 적합하지 않은 경우 통과 인증과 AD FS 중에 결정해야 합니다.

> [!TIP]
> 사용자 암호는 실제 사용자 암호를 나타내는 해시 값의 형태로 온-프레미스 Windows Server Active Directory에 저장됩니다. 해시 값은 단방향 수학 함수(해시 알고리즘)의 결과입니다. 암호의 일반 텍스트 버전에 대한 함수의 결과를 되돌릴 수 있는 방법이 없습니다. 암호 해시를 사용하여 온-프레미스 네트워크에 로그인할 수 없습니다. 암호 해시를 동기화하도록 선택하면 Azure AD Connect가 온-프레미스 Active Directory에서 암호 해시를 추출하고 암호 해시에 추가 보안 처리를 적용한 후 암호 해시가 Azure AD로 동기화됩니다. 암호 해시 동기화를 암호 쓰기 저장과 함께 사용하여 Azure AD에서 셀프 서비스 암호 재설정을 사용하도록 설정할 수 있습니다. 또한 회사 네트워크에 연결된 도메인에 연결된 컴퓨터의 사용자에 대해 SSO(Single Sign-On)를 사용하도록 설정할 수 있습니다. Single Sign-On으로 활성화된 사용자는 클라우드 리소스에 안전하게 액세스하기 위해 사용자 이름을 입력하기만 하면 됩니다. 
>

## <a name="pass-through-authentication"></a>통과 인증

[Azure AD 통과 인증](how-to-connect-pta.md)은 온-프레미스 Active Directory를 사용하여 Azure AD 기반 서비스에 대한 간단한 암호 유효성 검사 솔루션을 제공합니다. 조직의 보안 및 규정 준수 정책에서 사용자의 암호(해시된 형태로도) 전송을 허용하지 않고 도메인에 연결된 장치에 대해 데스크톱 SSO를 지원하기만 하면 되는 경우 통과 인증 사용을 평가하는 것이 좋습니다. 통과 인증은 DMZ에서 어떠한 배포도 요구하지 않으므로, AD FS와 비교할 경우 배포 인프라가 간소화됩니다. 사용자가 Azure AD를 사용하여 로그인할 때 이 인증 방법은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다.

![통과 인증](./media/whatis-hybrid-identity/pass-through-authentication.png)

통과 인증을 사용하면 복잡한 네트워크 인프라가 필요하지 않으며 클라우드에 온-프레미스 암호를 저장할 필요가 없습니다. 통과 인증이 Single Sign-On과 결합되면 Azure AD 또는 다른 클라우드 서비스에 로그인할 때 완전히 통합된 환경이 제공됩니다.

통과 인증은 암호 유효성 검사 요청을 수신 대기하는 간단한 온-프레미스 에이전트를 사용하는 Azure AD Connect를 통해 구성됩니다. 에이전트는 고가용성 및 부하 분산을 제공하도록 여러 컴퓨터에 손쉽게 배포될 수 있습니다. 모든 통신이 아웃바운드이므로 커넥터를 DMZ에 설치하기 위한 요구 사항이 없습니다. 커넥터에 대한 서버 컴퓨터 요구 사항은 다음과 같습니다.

- Windows Server 2012 R2 이상
- 사용자의 유효성이 검사되는 포리스트의 도메인에 연결

## <a name="federated-identity-ad-fs"></a>페더레이션 ID(AD FS)

사용자가 Office 365 및 다른 클라우드 서비스에 액세스하는 방식을 더 세밀하게 제어하려면 [AD FS(Active Directory Federation Services)](how-to-connect-fed-whatis.md)를 사용하여 SSO(Single Sign-On)와의 디렉터리 동기화를 설정하면 됩니다. 사용자의 로그인을 AD FS와 페더레이션하면 사용자 자격 증명의 유효성을 검사하는 인증이 온-프레미스 서버에 위임됩니다. 이 모델에서는 온-프레미스 Active Directory 자격 증명이 Azure AD에 전달되지 않습니다.

![페더레이션 ID](./media/whatis-hybrid-identity/federated-identity.png)

ID 페더레이션이라고도 하는 이 로그인 방법은 모든 사용자 인증이 온-프레미스에서 제어되도록 하며 관리자가 더 엄격한 수준의 액세스 제어를 구현할 수 있게 해줍니다. AD FS와의 ID 페더레이션은 가장 복잡한 옵션이며 온-프레미스 환경에 추가 서버를 배포해야 합니다. ID 페더레이션은 Active Directory 및 AD FS 인프라에 대한 연중무휴 지원 제공도 약속합니다. 온-프레미스 인터넷 액세스, 도메인 컨트롤러 또는 AD FS 서버를 사용할 수 없는 경우 사용자가 클라우드 서비스에 로그인할 수 없으므로 이러한 높은 수준의 지원이 필요합니다.

> [!TIP]
> AD FS(Active Directory Federation Services)와 페더레이션을 사용하도록 선택하는 경우, 필요에 따라 AD FS 인프라에 장애가 발생할 경우 백업으로 암호 해시 동기화를 설정할 수 있습니다.
>

## <a name="common-scenarios-and-recommendations"></a>일반적인 시나리오 및 권장 사항

다음은 몇 가지 일반적인 하이브리드 ID 및 액세스 관리 시나리오와 각 시나리오에 적합한 하이브리드 ID 옵션(하나 또는 여러 개)에 대한 권장 사항입니다.

|수행 작업:|PHS 및 SSO<sup>1</sup>| PTA 및 SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|온-프레미스 Active Directory에서 만든 새 사용자, 연락처 및 그룹 계정을 클라우드에 자동으로 동기화|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)|
|Office 365 하이브리드 시나리오에 대한 테넌트 설정|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)|
|사용자가 온-프레미스 암호를 사용하여 로그인하고 Cloud Services에 액세스할 수 있도록 함|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)|
|회사 자격 증명을 사용하여 Single Sign-On 구현|![권장](./media/whatis-hybrid-identity/ic195031.png)| ![권장](./media/whatis-hybrid-identity/ic195031.png) |![권장](./media/whatis-hybrid-identity/ic195031.png)|
|클라우드에 암호 해시가 저장되지 않도록 함| |![권장](./media/whatis-hybrid-identity/ic195031.png)|![권장](./media/whatis-hybrid-identity/ic195031.png)|
|클라우드 다단계 인증 솔루션 사용| |![권장](./media/whatis-hybrid-identity/ic195031.png)|![권장](./media/whatis-hybrid-identity/ic195031.png)|
|온-프레미스 다단계 인증 솔루션 사용| | |![권장](./media/whatis-hybrid-identity/ic195031.png)|
|사용자에 대한 스마트 카드 인증 지원<sup>4</sup>| | |![권장](./media/whatis-hybrid-identity/ic195031.png)|
|Office 포털 및 Windows 10 Desktop에 암호 만료 알림 표시| | |![권장](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Single Sign-On을 사용하여 암호 해시 동기화
>
> <sup>2</sup> 통과 인증 및 Single Sign-On 
>
> <sup>3</sup> AD FS를 통해 페더레이션된 Single Sign-On
>
> <sup>4</sup> AD FS는 엔터프라이즈 PKI와 통합되어 인증서를 사용한 로그인을 허용할 수 있습니다. 이러한 인증서는 MDM 또는 GPO와 같은 신뢰할 수 있는 프로비전 채널이나 스마트 카드 인증서(PIV/CAC 카드 포함) 또는 비즈니스용 Hello(cert-trust)를 통해 배포되는 소프트 인증서일 수 있습니다. 스마트 카드 인증 지원에 대한 자세한 내용은 [이 블로그](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)를 참조하세요.
>

## <a name="what-is-azure-ad-connect"></a>Azure AD Connect란?

Azure AD Connect는 하이브리드 ID 목표를 충족하고 달성하도록 설계된 Microsoft 도구입니다.  이렇게 하면 Azure AD와 통합된 Office 365, Azure 및 SaaS 응용 프로그램 사용자를 위한 공통 ID를 제공할 수 있습니다.  다음과 같은 기능을 제공합니다.
    
- [동기화](how-to-connect-sync-whatis.md) - 이 구성 요소는 사용자, 그룹 및 기타 개체 생성을 담당합니다. 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 만드는 것도 담당합니다.  Azure AD를 사용하여 암호 해시 동기화를 담당합니다.
- [암호 해시 동기화](how-to-connect-password-hash-synchronization.md) - 사용자 암호 해시를 Azure AD와 동기화하여 사용자가 온-프레미스와 클라우드에서 동일한 암호를 사용할 수 있게 해주는 선택적 구성 요소입니다.
-   [AD FS 및 페더레이션 통합](how-to-connect-fed-whatis.md) - 페더레이션은 Azure AD Connect의 선택적 부분이며 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 구성하는 데 사용할 수 있습니다. 또한 인증서 갱신 및 추가 AD FS 서버 배포와 같은 AD FS 관리 기능을 제공합니다.
-   [통과 인증](how-to-connect-pta.md) - 사용자가 동일한 온-프레미스 및 클라우드에서 암호를 사용할 수 있지만 페더레이션 환경의 추가 인프라가 필요하지 않은 다른 선택적 구성 요소입니다
-   [PingFederate 및 페더레이션 통합](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) - PingFederate를 ID 공급자로 사용할 수 있게 해주는 또 다른 페더레이션 옵션입니다.
-   [상태 모니터링](whatis-hybrid-identity-health.md) - Azure AD Connect Health는 강력한 모니터링을 제공하고 Azure Portal에서 중앙 위치를 제공하여 이 작업을 볼 수 있습니다. 


![Azure AD Connect의 정의](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Azure AD Connect Health란?

Azure AD(Azure Active Directory) Connect Health를 사용하면 온-프레미스 ID 인프라 및 동기화 서비스를 모니터링하고 파악할 수 있습니다. AD FS(Active Directory Federation Services) 서버, Azure AD Connect 서버(즉, 동기화 엔진), Active Directory 도메인 컨트롤러 등과 같은 핵심 ID 구성 요소를 위한 모니터링 기능을 제공하여 Office 365 및 Microsoft Online Services에 대해 신뢰할 수 있는 연결을 유지 관리할 수 있습니다. 또한 사용자가 사용 현황 및 기타 중요한 정보를 보고 올바른 의사 결정을 내릴 수 있도록 이러한 구성 요소의 핵심 데이터 요소에 쉽게 액세스할 수 있게 만들어 줍니다.

정보는 [Azure AD Connect Health 포털](https://aka.ms/aadconnecthealth)에 표시됩니다. Azure AD Connect Health 포털에서 경고, 성능 모니터링, 사용 현황 분석, 기타 정보를 볼 수 있습니다. Azure AD Connect Health는 핵심 ID 구성 요소의 상태를 한 곳에서 볼 수 있게 해주는 단일 렌즈의 역할을 합니다.

![Azure AD Connect Health 정의](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Azure AD Connect Health의 기능이 점점 많아지면서, 포털에서는 ID 렌즈를 통해 단일 대시보드를 제공합니다. 사용자의 업무 수행 능력이 향상되는 훨씬 강력하고 정상적이고 통합된 환경을 제공합니다.


## <a name="why-use-azure-ad-connect"></a>Azure AD Connect를 사용하는 이유
Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 사용자와 조직은 다음과 같은 이점을 얻을 수 있습니다.

* 사용자는 단일 ID를 사용하여 온-프레미스 응용 프로그램 및 Office 365와 같은 클라우드 서비스에 액세스할 수 있습니다.
* 동기화 및 로그인에 대해 간편한 배포 환경을 제공하는 단일 도구입니다.
* 시나리오에 대한 최신 기능을 제공합니다. Azure AD Connect는 DirSync 및 Azure AD Sync와 같은 이전 버전의 ID 통합 도구를 대체합니다. 자세한 내용은 [하이브리드 ID 디렉터리 통합 도구 비교](plan-hybrid-identity-design-considerations-tools-comparison.md)를 참조하세요.

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하는 이유
온-프레미스 디렉터리와 Azure AD를 통합하면 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있는 공통 ID가 제공되므로 사용자의 생산성이 향상됩니다. 그러나 이 통합에는 사용자가 아무 장치에서 온-프레미스와 클라우드의 리소스에 안정적으로 액세스할 수 있도록 환경을 정상적으로 유지해야 하는 과제가 발생합니다. Azure AD Connect Health를 사용하면 Office 365 또는 다른 Azure AD 응용 프로그램에 액세스하는 데 사용되는 온-프레미스 ID 인프라를 모니터링하고 통찰력을 얻을 수 있습니다. 각 온-프레미스 ID 서버에 에이전트를 설치하는 것만큼 간단합니다.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[AD FS에 대한 Azure AD Connect Health](how-to-connect-health-adfs.md)
AD FS용 Azure AD Connect Health는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에서 AD FS 2.0을 지원합니다. 또한 엑스트라넷 액세스에 대한 인증 지원을 제공하는 AD FS 프록시 또는 웹 응용 프로그램 프록시 서버의 모니터링을 지원합니다. AD FS에 대한 Azure AD Connect Health는 Health 에이전트를 쉽고 빠르게 설치하며 주요 기능 집합을 제공합니다.

#### <a name="key-benefits-and-best-practices"></a>주요 이점 및 모범 사례

- *향상된 보안*
  - [엑스트라넷 잠금 추세](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [실패한 로그인 보고서](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [개인 정보 보호 준수](reference-connect-health-user-privacy.md)에서    
- *모든 [심각한 ADFS 시스템 문제](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)에 대한 알림 받기*
    - 서버 구성 및 가용성 
    - [성능 및 연결](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - 정기적인 유지 관리    
- *쉬운 배포 및 관리*
  - 빠른 [에이전트 설치](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - 최신 에이전트 자동 업그레이드 
  - 몇 분 안에 포털에서 사용 가능한 데이터    
- *다양한 [사용량 메트릭](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - 상위 응용 프로그램 사용량
  - 네트워크 위치 및 TCP 연결
  - 서버당 토큰 요청    
- *멋진 사용자 환경* 
  - Azure Portal에서 대시보드 방식
  - [이메일 경고](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>기능 강조 표시

*   AD FS 및 AD FS 프록시 서버가 정상이 아닐 때 알아야 하는 경고 모니터링
*   중요한 경고에 대한 전자 메일 알림
*   AD FS의 용량 계획에 유용한 성능 데이터의 동향
*   피벗(앱, 사용자, 네트워크 위치 등)을 사용하여 AD FS 로그인의 사용량 분석
*   잘못된 사용자 이름/암호를 시도한 상위 50명의 사용자와 그들의 최신 IP 주소와 같은 AD FS에 대한 보고서
*   실패한 AD FS 로그인에 대한 위험 IP 보고서

[AD FS와 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)에 대해 자세히 알아보세요.

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[동기화에 대한 Azure AD Connect Health](how-to-connect-health-sync.md)
동기화에 대한 Azure AD Connect Health는 온-프레미스 Active Directory와 Azure AD 간에 발생하는 동기화에 대한 정보를 모니터링하고 제공합니다. 동기화에 대한 Azure AD Connect Health는 다음과 같은 주요 기능 집합을 제공합니다.

* Azure AD Connect 서버, 즉 동기화 엔진이 정상이 아닐 때 알아야 하는 경고 모니터링
* 중요한 경고에 대한 전자 메일 알림
* 다른 작업에 대한 대기 시간 차트, 동기화 작업(추가, 업데이트, 삭제 등)의 동향을 비롯한 동기화 작업 통찰력
* 동기화 속성, 최근에 성공한 Azure AD로 내보내기에 대한 정보 빠른 보기
* 개체 수준 동기화 오류에 대한 보고서는 \(Azure AD Premium이 필요 없음\)

[동기화할 Azure AD Connect Health 사용](how-to-connect-health-sync.md)에 대해 자세히 알아보세요.

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[AD DS용 Azure AD Connect Health](how-to-connect-health-adds.md)
AD DS(Active Directory Domain Services)용 Azure AD Connect Health는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에 설치된 도메인 컨트롤러에 대한 모니터링을 제공합니다. Health 에이전트를 설치하면 클라우드에서 온-프레미스 AD DS 환경을 모니터링할 수 있습니다. AD DS용 Azure AD Connect Health는 다음과 같은 주요 기능 집합을 제공합니다.

* 도메인 컨트롤러가 비정상인 시기를 감지하는 경고 모니터링 및 중요한 경고에 대한 전자 메일 알림
* 도메인 컨트롤러의 상태 및 작동 상태에 대한 빠른 보기를 제공하는 도메인 컨트롤러 대시보드
* 오류가 감지되면 문제 해결 가이드에 대한 링크와 함께 최신 복제 정보를 제공하는 복제 상태 대시보드
* 문제 해결 및 모니터링을 위해 필요한 인기 있는 성능 카운터의 성능 데이터 그래프에 대한 빠른 액세스

[AD DS와 Azure AD Connect Health 사용](how-to-connect-health-adds.md)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계


- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)
- [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)|
- [통과 인증](how-to-connect-pta.md)
- [Azure AD Connect 및 페더레이션](how-to-connect-fed-whatis.md)
- [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md) 
- [Azure AD Connect 동기화](how-to-connect-sync-whatis.md)
- [버전 기록](reference-connect-version-history.md)
- [디렉터리 통합 도구 비교](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect FAQ](reference-connect-faq.md)









