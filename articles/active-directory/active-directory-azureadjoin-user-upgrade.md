<properties
	pageTitle="설정에서 Azure AD으로 Windows 10 장치 설정| Microsoft Azure"
	description="사용자가 설정 메뉴를 통해 Azure AD에 조인시킬 수 있는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# 설정에서 Azure AD으로 Windows 10 장치 설정
Windows 7 또는 Windows 8을 이미 사용 중이고 컴퓨터 또는 장치를 Windows 10으로 업그레이드한 경우 설정 메뉴에서 Azure Active Directory(Azure AD)에 연결할 수 있습니다.

## 설정 메뉴에서 Azure AD에 연결하려면


1. **시작** 메뉴에서 **설정** 참을 클릭합니다.
2. **설정**에서 **시스템**->**정보**->**Azure AD 조인**을 선택합니다.
<center> ![설정 메뉴에서 Azure AD 조인](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Azure AD 조인 메시지 창에서 **계속**을 클릭합니다.
<center> ![Azure AD 조인 메시지 창](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. 로그인 자격 증명을 제공합니다. 이 로그인 환경은 완전한 인증에 필요한 모든 단계를 포함합니다. 페더레이션된 테넌트의 일부인 경우 관리자가 조직이 호스팅하는 페더레이션 환경을 제공합니다.
<center> ![로그인 자격 증명 제공](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. 조직에서 Azure AD를 연결하기 위해 다중요소 인증을 구성한 경우 계속 진행하기 전에 두 번째 요소를 제공합니다.
6. **이 장치가 관리되도록 허용** 화면에서 **동의**를 클릭합니다.
7. "이제 장치가 Azure AD의 조직에 조인되었습니다"라는 메시지가 표시됩니다.


## 추가 정보
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)

<!---HONumber=AcomDC_0629_2016-->