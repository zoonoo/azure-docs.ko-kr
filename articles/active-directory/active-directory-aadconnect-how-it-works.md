<properties 
	pageTitle="Azure AD Connect 작동 방법" 
	description="Azure AD Connect가 작동하는 방법을 알아봅니다." 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect 작동 방법

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="정의">정의</a> <a href="../active-directory-aadconnect-how-it-works/" title="작동 방식" class="current">작동 방식</a> <a href="../active-directory-aadconnect-get-started/" title="시작">시작</a> <a href="../active-directory-aadconnect-whats-next/" title="다음 단계">다음 단계</a> <a href="../active-directory-aadconnect-learn-more/" title="자세한 정보">자세한 정보</a>
</div>
Azure Active Directory Connect는 기본 세 부분으로 구성됩니다. 이러한 주요 부분은 동기화 서비스, 선택적 Active Directory Federation Services 부분, [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)를 사용하여 수행하는 모니터링 부분입니다.


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- 동기화 - 이 부분은 이전에 DirSync 및 AAD 동기화로 릴리스된 구성 요소와 기능으로 이루어집니다. 사용자 및 그룹 생성을 담당하는 부분입니다. 또한 온-프레미스 환경의 사용자 및 그룹에 대한 정보가 클라우드 내의 정보와 일치하도록 만들기도 합니다.
- AD FS - Azure AD Connect의 선택적 부분이며 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 설정하는 데 사용할 수 있습니다. 이 부분은 조직에서 도메인 가입 SSO, AD 로그인 정책 강화, 스마트 카드나 타사 MFA 등이 포함된 복잡한 배포를 처리하는 데 사용할 수 있습니다. SSO 구성에 대한 자세한 내용은 [Single Sign On을 사용한 DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx)를 참조하세요.
- 상태 모니터링 - AD FS를 사용하는 복잡한 배포의 경우 Azure AD Connect Health는 페더레이션 서버에 대한 강력한 모니터링을 제공하고Azure 포털에서 이 활동을 볼 수 있는 중앙 위치를 제공할 수 있습니다. 자세한 내용은 [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)를 참조하세요.


## Azure AD Connect 지원 구성 요소

다음은 Azure AD Connect를 설정한 서버에 Azure AD Connect가 설치되는 데 필요한 필수 조건 및 지원 구성 요소의 목록입니다. 이 목록은 기본 Express 설치에 해당합니다. 동기화 서비스 설치 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우, 다음 목록에 나온 SQL Server 2012 구성 요소는 설치되지 않습니다.

- Azure AD Connect Azure AD Connector
- Microsoft SQL Server 2012 명령줄 유틸리티
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell용 Azure Active Directory 모듈
- IT 전문가를 위한 Microsoft Online Services 로그인 도우미
- Microsoft Visual C++ 2013 재배포 패키지




 

<!---HONumber=58_postMigration-->