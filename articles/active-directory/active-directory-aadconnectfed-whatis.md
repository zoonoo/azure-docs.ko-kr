<properties
	pageTitle="Azure AD Connect 및 페더레이션 | Microsoft Azure"
	description="이 페이지는 Azure AD Connect를 사용하는 AD FS 작업에 관한 모든 설명서의 중심 위치입니다."
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="anandy"/>


# Azure AD Connect 및 페더레이션

Azure AD Connect를 통해 온-프레미스 AD FS 및 Azure AD와 페더레이션을 구성할 수 있습니다. 페더레이션 로그온에서, 사용자가 자신의 온-프레미스 암호로 Azure AD 기반 서비스에 로그온 하고 자신의 암호를 다시 입력하지 않고도 회사 네트워크에 로그온하도록 설정할 수 있습니다. AD FS와 페더레이션 옵션을 사용하여 새로 배포하거나 Windows Server 2012 R2 팜에서 기존 AD FS를 지정할 수 있습니다.

이 항목은 Azure AD Connect의 페더레이션 관련 기능에 대한 정보를 포함하며 그와 관련된 다른 모든 항목에 대한 링크 목록을 포함합니다. Azure AD Connect에 대한 링크는 Azure Active Directory와 온-프레미스 ID 통합을 참조하세요.

## Azure AD Connect - 페더레이션 항목

| 항목 | 포함된 내용 및 적용 시기 |
|:------|:-----------|
| **Azure AD Connect 사용자 로그인 옵션** ||
| [사용자 로그인 옵션 이해](active-directory-aadconnect-user-signin.md) | 다양한 사용자 로그인 옵션 및 Azure 로그인 사용자 환경에 미치는 영향에 대한 설명 |
| **Azure AD Connect를 사용한 AD FS 설치**||
| [필수 구성 요소](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Azure AD Connect를 통한 성공적인 AD FS 설치의 필수 구성 요소|
| [AD FS 팜 구성](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Azure AD Connect를 사용하여 새 AD FS 팜을 설치하는 방법 |
| **AD FS 구성 수정** | |
| [트러스트 복구](active-directory-aadconnect-federation-management.md#reparing-the-trust) | 온-프레미스 AD FS와 Office 365/Azure 사이의 현재 트러스트를 복구하는 방법 |
| [새 AD FS 서버 추가](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | 초기 설치 후 추가적인 AD FS 서버를 통한 AD FS 팜 확장 |
| [새 AD FS WAP 서버 추가](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | 초기 설치 후 추가적인 WAP 서버를 통한 AD FS 팜 확장 |
| [새 페더레이션된 도메인 추가](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Azure AD를 통해 페더레이션될 또 다른 도메인 추가 |
|**설치 후 작업**||
| [사용자 지정 회사 로고/일러스트레이션 추가](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| AD FS 로그인 페이지에 표시될 사용자 지정 로고를 지정하여 로그인 환경 수정 |
| [로그인 설명 추가](active-directory-aadconnect-federation-management.md#add-sign-in-description) | AD FS 로그인 페이지의 로그인 설명 변경 | 
| [AD FS 클레임 규칙 수정](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Azure AD Connect 동기화 구성에 해당하는 AD FS의 클레임 규칙 수정/추가 |


## 추가 리소스

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
* [Azure에서 AD FS 배포](active-directory-aadconnect-azure-adfs.md)

<!---HONumber=AcomDC_0720_2016-->