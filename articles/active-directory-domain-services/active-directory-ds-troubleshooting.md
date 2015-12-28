<properties
	pageTitle="Azure Active Directory 도메인 서비스 미리 보기: 문제 해결 가이드 | Microsoft Azure"
	description="Azure AD 도메인 서비스에 대한 문제 해결 가이드"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스*(미리 보기)* - 문제 해결 가이드
이 문서는 Azure AD(Active Directory) 도메인 서비스를 설치하거나 관리할 때 발생할 수 있는 문제에 대한 문제 해결 힌트를 제공합니다.


### 사용자는 Azure AD 도메인 서비스 관리된 도메인에 로그인할 수 없습니다.
Azure AD 테넌트에서 하나 이상의 사용자가 새로 만든 관리된 도메인에 로그인할 수 없는 상황이 발생한 경우 다음의 문제 해결 단계를 수행합니다.

- 시작 가이드에 설명된 단계에 따라 [암호 동기화를 사용하도록 설정](active-directory-ds-getting-started-password-sync.md)했는지 확인합니다.

- 영향을 받는 사용자 계정이 Azure AD 테넌트의 외부 계정인지 확인합니다. 외부 계정의 예는 Microsoft 계정(예: 'joe@live.com') 또는 외부 Azure AD 디렉터리에서 사용자 계정을 포함합니다. Azure AD 도메인 서비스에는 이러한 사용자 계정에 대한 자격 증명이 없으므로 이러한 사용자는 관리된 도메인에 로그인할 수 없습니다.

- Azure AD 테넌트에서 영향을 받는 사용자 계정의 UPN 접두사(즉, UPN의 첫 번째 부분) 길이가 20자보다 작은지 확인합니다. 예를 들어 'joereallylongnameuser@contoso.com' UPN의 경우 접두사('joereallylongnameuser')가 20자를 초과하여 이 계정은 Azure AD 도메인 서비스 관리된 도메인에서 사용할 수 없습니다.

- **동기화된 계정:** 영향을 받는 사용자 계정이 온-프레미스 디렉터리에서 동기화되는 경우 다음 단계를 따르도록 합니다.
    - Azure AD Connect의 최신 권장 사항으로 배포되거나 업데이트됩니다.
    - Azure AD에 레거시 자격 증명의 동기화를 사용하도록 설정하는 데 필요한 레지스트리 키를 만들었습니다.
    - 위에 언급한 Azure AD Connect를 실행하는 서버에서 레지스트리 키를 만든 후에 문서에서 설명한 대로 전체 동기화를 수행하려면 Azure AD를 수행하도록 해야 합니다.
    - 디렉터리의 크기에 따라 사용자 계정 및 해시 자격 증명이 Azure AD 도메인 서비스에서 사용할 수 있도록 하는 데 시간이 걸릴 수 있습니다. 인증을 다시 시도하기 전에 충분한 시간 동안 대기합니다(디렉터리 크기에 따라 몇 시간에서 큰 디렉터리는 하루나 이틀까지).

- **클라우드 전용 계정**: 영향을 받는 사용자 계정이 클라우드 전용 사용자 계정인 경우 사용자는 Azure AD 도메인 서비스를 사용하도록 설정한 후에 자신의 암호를 변경하도록 합니다. 이 단계를 수행하면 Azure AD 도메인 서비스가 생성되는 데 필요한 자격 증명 해시가 발생합니다.


### 문의처
관리된 도메인과 관련된 문제가 발생하는 경우 이 문제 해결 가이드에 설명된 단계가 문제를 해결하는지 확인해 주세요. 여전히 문제가 발생하는 경우 다음으로 마음껏 문의해 주세요.

- [Azure Active Directory 사용자 의견 채널](http://feedback.azure.com/forums/169401-azure-active-directory). 문의하시려면 **'AADDS'**라는 단어로 질문을 시작해 주세요.
- 또한 [Azure AD 도메인 서비스 피드백](mailto:aaddsfb@microsoft.com)에 메일을 보내실 수 있습니다.

<!---HONumber=AcomDC_1217_2015-->