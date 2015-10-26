<properties
	pageTitle="엔터프라이즈 관리자 계정이 필요한 이유 | Microsoft Azure"
	description="사용자 지정 설정 설명."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Azure AD 연결을 설정할 때 AD DS에 연결하는 데 엔터프라이즈 관리자 계정이 필요한 이유

다음 테이블에서 엔터프라이즈 관리자 계정이 Azure AD Connect 설정에 필요한 이유를 보여줍니다.

다음 조건 | 설명
------------- | ------------- |
Express 설정 및 DirSync 업그레이드 | <li>Express 설정의 경우, 동기화(AD Connector 계정이라고도 함)에 사용되는 로컬 Active Directory 계정을 만들고 동기화 및 암호 동기화를 위한 올바른 사용 권한을 할당합니다.</li> <li>Dirsync 업그레이드의 경우, 현재 구성된 AD Connector 계정 암호를 재설정하고 이 계정을 사용하도록 새 Azure AD Connect 동기화 서비스를 구성합니다. </li>



**추가 리소스**


* [Azure AD Connect 계정 및 사용 권한에 대한 추가 정보](active-directory-aadconnect-account-summary.md)
* [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)
* [MSDN의 Azure AD Connect](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->