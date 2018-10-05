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
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181783"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>하이브리드 ID 및 Microsoft의 ID 솔루션
오늘날 비즈니스 및 기업에서는 온-프레미스와 클라우드 응용 프로그램을 점점 더 많이 혼합하고 있습니다.  온-프레미스 및 클라우드에 응용 프로그램 및 해당 응용 프로그램에 액세스해야 하는 사용자가 있는 작업은 어려운 시나리오입니다.

Microsoft의 ID 솔루션은 온-프레미스 및 클라우드 기반 기능을 확장하며 이는 위치에 관계 없이 모든 리소스에 인증 및 권한 부여에 대한 단일 사용자 ID를 만듭니다. 하이브리드 ID라고 합니다.

## <a name="what-is-azure-ad-connect"></a>Azure AD Connect란?

Azure AD Connect는 하이브리드 ID 목표를 충족하고 달성하도록 설계된 Microsoft 도구입니다.  이렇게 하면 Azure AD와 통합된 Office 365, Azure 및 SaaS 응용 프로그램 사용자를 위한 공통 ID를 제공할 수 있습니다.  다음과 같은 기능을 제공합니다.
    
- [동기화](how-to-connect-sync-whatis.md) - 이 구성 요소는 사용자, 그룹 및 기타 개체 생성을 담당합니다. 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 만드는 것도 담당합니다.  Azure AD를 사용하여 암호 해시 동기화를 담당합니다.
-   [AD FS 및 페더레이션 통합](how-to-connect-fed-whatis.md) - 페더레이션은 Azure AD Connect의 선택적 부분이며 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 구성하는 데 사용할 수 있습니다. 또한 인증서 갱신 및 추가 AD FS 서버 배포와 같은 AD FS 관리 기능을 제공합니다.
-   [통과 인증](how-to-connect-pta.md) - 사용자가 동일한 온-프레미스 및 클라우드에서 암호를 사용할 수 있지만 페더레이션 환경의 추가 인프라가 필요하지 않은 다른 선택적 구성 요소입니다
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









