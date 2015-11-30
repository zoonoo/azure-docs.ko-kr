<properties 
	pageTitle="설치하는 동안 Azure AD로 새 장치 설정| Microsoft Azure" 
	description="첫 실행 경험동안 사용자가 Azure AD 조인을 설정하는 방법에 대해 설명하는 항목입니다." 
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

# 설치하는 동안 Azure AD로 새 장치 설정

Windows 10에서 최종 사용자는 첫 실행 경험(FRX)에서 자신의 장치를 Azure AD에 조인시킬 수 있습니다. 이를 통해 조직은 압축 포장된 장치를 직원 또는 학생들에게 배포하거나 자신의 장치를 선택(CYOD)하게 할 수 있습니다. Windows 10에 Professional 또는 Enterprise SKU를 설치하는 경우 경험은 회사 소유 장치의 설정으로 초기화합니다.

Azure AD에 장치를 조인시키려면
-----------------------------------------------------------------------

1. **준비**단계가 끝나면 설치 마법사가 나타납니다.
2. 지역 및 언어를 지정하여 시작하고, EULA에 동의하고 온라인으로 연결합니다.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. 인터넷에 연결할 네트워크를 선택합니다.
4. 개인 장치인지 회사 소유 장치인지 선택합니다.
5. **이 장치는 내 조직에 속해 있습니다**를 클릭합니다. 이렇게 하면 Azure AD 조인 경험을 시작합니다. 다음은 Windows 10 Professional SKU에서 볼 수 있는 화면입니다. 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	조직에서 제공한 자격 증명을 입력 합니다.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	사용자 이름을 입력하면 일치하는 테넌트가 Azure AD에 나타납니다. 페더레이션된 도메인의 사용자인 경우 온-프레미스 보안 토큰 서비스가 (STS) 서버(예: AD FS)로 리디렉션됩니다. 페더레이션되지 않은 도메인의 사용자의 경우 Azure AD 호스팅 페이지에 직접 자격 증명을 입력해야 합니다. 또한 조직의 로고를 볼 수 있으며 회사 브랜딩이 구성된 경우 텍스트를 지원합니다.
8.	다중요소 인증 시도라는 메시지가 표시됩니다. 이는 IT에 의해 구성할 수 있습니다.
9.	그런 다음 Azure AD가 사용자/장치와 모바일 장치 관리(MDM) 등록에 필요한지 여부를 확인합니다. 
10.	그 후 Windows가 Azure AD에서 조직의 디렉터리에 장치를 등록하고 MDM에 등록합니다.
11.	이 작업이 완료되면 관리 사용자의 경우, Windows가 설치 프로세스를 마무리하고 자동 로그인을 통해 사용자를 데스크톱으로 이동시킵니다.
12.	페더레이션 사용자의 경우, Windows 로그인 화면으로 이동하고 자격 증명을 입력하여 로그인해야 합니다.

> [AZURE.NOTE]Windows 기본 환경에서 온-프레미스 Active Directory 도메인 조인은 지원되지 않습니다. 따라서 PC를 도메인에 조인시키려는 경우 "로컬 계정을 사용하여 Windows 설치" 링크를 선택해야 합니다. 그런 다음 이전에 수행한 대로 PC 설정에서 도메인에 조인시킬 수 있습니다.

## 추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->