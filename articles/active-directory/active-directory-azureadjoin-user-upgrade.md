<properties 
	pageTitle="설정에서 Azure AD으로 Windows 10 장치 설정| Microsoft Azure" 
	description="Windows 10에서 설정 메뉴를 통해 Azure AD에 조인시킬 수 있는 방법에 대해 설명합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# 설정에서 Azure AD으로 Windows 10 장치 설정
이미 Windows 7 또는 8을 사용하고 있고 사용하는 컴퓨터를 Windows 10으로 업그레이드한 경우, 설정 메뉴를 통해 Azure AD를 조인시킬 수 있습니다.

설정 메뉴에서 Azure AD에 조인시키려면
-----------------------------------------------------------------------------------------------

1. 시작 메뉴에서 설정 참을 클릭합니다.
2. 설정->**시스템**->**정보**->**Azure AD 조인**
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Azure AD 가입 메시지 창에서 **계속**을 클릭합니다.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. 로그인 자격 증명을 제공합니다. 이 로그인 환경은 완전한 인증에 필요한 모든 단계가 포함됩니다. 페더레이션된 테넌트의 일부인 경우 관리자가 조직이 호스팅하는 페더레이션 환경을 제공합니다.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. 조직에서 Azure AD를 조인시키기 위해 다중요소 인증을 구성한 경우 계속 진행하려면 두 번째 요소를 제공해야 합니다.
6. **이 장치가 관리되도록 허용** 화면에서 **동의**를 클릭합니다.
7. "이제 장치가 Azure AD의 조직에 조인되었습니다"라는 메시지가 표시됩니다.


## 추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->