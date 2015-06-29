<properties
	pageTitle="Azure AD는 어떻게 작동하나요?"
	description="Azure AD는 클라우드에서 사용자가 사용할 ID 환경을 만듭니다. 이 환경을 온-프레미스 ID 시스템에 연결할 수도 있고 독립적으로 사용할 수도 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="curtand"/>



# Azure Active Directory는 어떻게 작동하나요?


###이 항목에 대한 다른 문서
[Azure AD란?](active-directory-whatis.md)<br> [작동 원리](active-directory-works.md)<br> [시작](active-directory-get-started.md)<br> [다음 단계](active-directory-next-steps.md)<br> [자세한 정보](active-directory-learn-map.md)


Azure AD는 클라우드에서 사용자가 사용할 ID 환경을 만듭니다. 이 환경을 온-프레미스 ID 시스템에 연결할 수도 있고 독립적으로 사용할 수도 있습니다.

Azure AD 계정은 클라우드를 사용하기 위한 운전 면허증에 비유할 수 있습니다. 온라인 서비스에 액세스하는 데 필요한 고유의 ID입니다. 같은 맥락에서, Azure AD는 클라우드에서 운전 면허증을 관리하는 사설 등록 기관의 역할을 합니다. 클라우드 내 어디에서나 ID를 사용할 수 있게 해주고 온-프레미스 리소스에 액세스하는 사용자의 이동성을 개선합니다.

> [AZURE.NOTE]Azure Active Directory를 사용하려면 Azure 계정이 필요합니다. 계정이 없으면 [무료 Azure 계정을 등록](http://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.

## Azure AD는 Office 365, Microsoft Intune 및 다른 Azure 서비스를 어떻게 지원하나요?
Azure 포털, Office 365 관리 센터, Microsoft Intune 계정 포털 및 Azure AD PowerShell 모듈의 cmdlet은 모두 사용자의 디렉터리에 연결된 Azure AD의 단일 공유 인스턴스에서 쓰고 읽습니다. 포털(또는 cmdlet)은 디렉터리 정보를 가져오거나 변경하는 프런트 엔드 인터페이스로 작동합니다. [다른 서비스 지원에 대한 자세한 정보](active-directory-administer.md#what-is-an-azure-ad-tenant)

## Azure AD는 타사 응용 프로그램을 어떻게 지원하나요?
Azure AD는 코딩을 빠르게 시작하도록 돕기 위해 다양한 플랫폼용 오픈 소스 라이브러리를 지원하고 ID를 서비스로 제공하여 개발자에 대한 인증을 간소화합니다 [Azure AD의 인증 시나리오에 대해 자세히 알아보세요](active-directory-authentication-scenarios.md).


## Azure AD는 온-프레미스 디렉터리를 어떻게 확장하나요?
Azure AD는 가장 널리 사용되는 여러 가지 인증 및 권한 부여 프로토콜을 지원합니다. [Azure Active Directory 인증 프로토콜에 대해 자세히 알아보세요](active-directory-authentication-scenarios.md).

## Azure는 ID 관리를 어떻게 도와주나요?
Azure AD를 관리하는 방법, 디렉터리를 얻는 방법, 디렉터리를 삭제하는 방법, 디렉터리 데이터를 관리하는 방법이 궁금하세요? Azure AD 디렉터리를 관리하는 방법에 대해 자세히 알아보세요. [Azure AD를 관리하는 방법에 대해 자세히 알아보세요](active-directory-administer.md).

## 추가 리소스

* [조직으로 Azure 등록](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)
 

<!---HONumber=58_postMigration-->