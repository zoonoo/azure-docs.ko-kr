<properties 
	pageTitle="Azure AD Connect를 사용하여 디렉터리 연결" 
	description="Azure AD Connect 디렉터리의 사용자 지정 설정을 설명합니다." 
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



# Azure AD Connect를 사용하여 디렉터리 연결

사용자 지정 설정의 경우, 엔터프라이즈 관리자 계정은 Active Directory 포리스트를 연결하는 데 필요하지 않습니다. 마법사는 도메인 또는 로컬 사용자 계정을 수락합니다. 그러나 계정은 로컬 AD 커넥터 계정으로 사용됩니다. 즉, 동기화에 대한 디렉터리 정보를 읽고 쓰는 계정입니다.

다음 시나리오를 사용하도록 추가 사용 권한을 할당해야 됨을 의미합니다.

시나리오 |사용 권한
------------- | ------------- |
암호 동기화| <li>디렉터리 변경 내용을 복제합니다.</li> <li>디렉터리 변경 내용을 모두 복제합니다.</li>
Exchange 하이브리드 배포|[Office 365 Exchange 하이브리드 AAD 동기화 다시 쓰기 특성 및 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)을 참조하세요.
암호 쓰기 저장 | <li>암호 변경</li><li>암호 재설정</li>
사용자, 그룹 및 장치 쓰기 저장|'다시 쓰기'를 수행하려는 디렉터리 개체 및 특성에 대한 쓰기 권한
SSO(Single Sign-on) 및 AD FS| 페더레이션된 서버에 있는 도메인에 도메인 관리자 권한이 있습니다.





**추가 리소스**

* [Azure AD Connect 계정 및 사용 권한에 대한 추가 정보](active-directory-aadconnect-account-summary.md)
* [암호 동기화에 대한 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Exchange 하이브리드에 대한 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [암호 쓰기 저장에 대한 사용 권한](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
* [MSDN의 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=July15_HO4-->