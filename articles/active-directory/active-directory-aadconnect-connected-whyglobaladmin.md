<properties 
	pageTitle="Azure AD Connect를 설정하기 위한 Azure AD 전역 관리자 계정이 필요한 이유" 
	description="전역 관리자 계정이 필요한 이유의 사용자 지정 설정의 설명입니다." 
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

# Azure AD Connect를 설정하기 위한 Azure AD 전역 관리자 계정이 필요한 이유

다음 테이블에서 Azure AD 전역 관리자 계정이 Azure AD Connect를 설정하기 위해 필요 한 이유를 보여줍니다.

다음 조건 | 설명 
------------- | ------------- |
Express 설정 및 DirSync 업그레이드 | Azure AD 디렉터리에서 동기화를 사용하고(필요한 경우) 진행 중인 동기화 작업 에 사용할 Azure AD 계정(Azure AD 커넥터 계정)을 만듭니다. 
사용자 지정 설정 | Azure AD 디렉터리에서 동기화를 사용하고 진행 중인 동기화 작업에 사용할 Azure AD 계정(Azure AD 커넥터 계정)을 만듭니다. AD FS 옵션이 있는 SSO(Single sign-on)의 경우, Azure AD에서 페더레이션 속성을 읽고 구성합니다.



**추가 리소스**


* [Azure AD Connect 계정 및 사용 권한에 대한 추가 정보](active-directory-aadconnect-account-summary.md)
* [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
* [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=August15_HO6-->