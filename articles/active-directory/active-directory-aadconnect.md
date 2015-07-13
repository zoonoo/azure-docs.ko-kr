<properties 
	pageTitle="Azure AD Connect를 사용하여 하이브리드 관리에 디렉터리 사용합니다." 
	description="Azure AD Connect의 정의 및 사용해야 하는 이유를 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Azure AD Connect를 사용하여 하이브리드 관리에 디렉터리 사용

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="정의" class="current">정의</a> <a href="../active-directory-aadconnect-how-it-works/" title="작동 방식">작동 방식</a> <a href="../active-directory-aadconnect-get-started/" title="시작">시작</a> <a href="../active-directory-aadconnect-whats-next/" title="다음 단계">다음 단계</a> <a href="../active-directory-aadconnect-learn-more/" title="자세한 정보">자세한 정보</a>
</div>


오늘날 사용자는 랩톱, 스마트폰, 태블릿 등 어떤 장치에서든 온-프레미스와 클라우드 모두에서 응용 프로그램에 액세스할 수 있기를 원합니다. 이를 위해서는 관리자와 해당 조직이 사용자에게 이러한 앱에 액세스할 수 있는 방법을 제공할 수 있어야 하지만, 그렇다고 항상 클라우드로 완전히 전환해야 하는 것은 아닙니다.

<center>![Azure AD Connect 정의](./media/active-directory-aadconnect/arch.png)</center>

Azure Active Directory Connect의 도입으로 이러한 앱에 대한 액세스를 제공하고 클라우드로 전환하는 것이 어느 때보다 쉬워졌습니다. Azure AD Connect는 다음과 같은 이점을 제공합니다.

- 사용자가 클라우드와 온-프레미스 모두에서 공통 ID로 로그온할 수 있습니다. 여러 암호 또는 계정을 기억할 필요가 없으며, 관리자는 여러 계정으로 인해 발생할 수 있는 추가 오버 헤드를 걱정할 필요가 없습니다.
- 온-프레미스 디렉터리를 Azure Active Directory와 연결하는 데 사용되는 단일 도구 및 단계별 환경. 설치하면 마법사가 동기화 서비스, 암호 동기화 또는 AD FS 등의 디렉터리 통합을 실행하고 Azure AD PowerShell 모듈과 같은 필수 구성 요소를 얻는 데 필요한 모든 구성 요소를 배포 및 구성합니다.



<center>![Azure AD Connect 정의](./media/active-directory-aadconnect/azuread.png)</center>




## Azure AD Connect를 사용하는 이유 

Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 이러한 통합을 통해 사용자와 조직은 다음과 같은 이점을 얻을 수 있습니다.
	
* 조직에서는 사용자에게 Windows Server Active Directory를 활용하여 Azure Active Directory에 연결하는 클라우드 기반 서비스 또는 온-프레미스 간에 공통 하이브리드 ID를 제공할 수 있습니다. 
* 관리자는 응용 프로그램 리소스, 장치 및 사용자 ID, 네트워크 위치, 다단계 인증 등을 기반으로 조건부 액세스를 제공할 수 있습니다.
* 사용자는 Azure AD의 계정을 통해 공통 ID를 활용하여 Office 365, Intune, SaaS 앱 및 타사 응용 프로그램에 액세스할 수 있습니다.  
* 개발자는 온-프레미스 Active Directory 또는 클라우드 기반 응용 프로그램용 Azure에 응용 프로그램을 통합하여 공통 ID 모델을 활용하는 응용 프로그램을 빌드할 수 있습니다.

Azure AD Connect는 이러한 통합을 간편하게 해주며, 온-프레미스 및 클라우드 ID 인프라 관리를 간소화합니다.



----------------------------------------------------------------------------------------------------------
## Azure AD Connect 다운로드

Azure AD Connect를 사용하여 시작하려면 [Azure AD Connect 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771)를 사용하여 최신 버전을 다운로드하면 됩니다.

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->