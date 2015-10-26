<properties 
	pageTitle="사용자의 Azure AD 조인 설정 | Microsoft Azure" 
	description="관리자가 조직에서 최종 사용자(직원, 학생, 다른 사용자)를 위해 Azure AD 조인을 설정하는 방법을 설명" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>

# 조직에서 Azure AD 조인 설정

Azure AD 조인을 설정하기 전에 사용자의 온-프레미스 디렉터리를 클라우드와 동기화하거나 Azure AD에서 관리되는 계정을 수동으로 만들어야 합니다.

온-프레미스 사용자와 Azure AD의 동기화에 대한 자세한 지침을 보려면 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.


Azure AD에서 사용자를 수동으로 만들고 관리하려면 [Azure AD에서 사용자 관리](https://msdn.microsoft.com/library/azure/hh967609.aspx)를 참조하세요.

## 장치 등록 설정 
1. 관리자 권한으로 Azure 포털에 로그인합니다.
2. 왼쪽 창에서 Active Directory를 선택합니다.
3. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.
4. **구성** 탭을 선택합니다.
5. **장치** 섹션으로 스크롤합니다.
6. **장치** 탭에서 다음을 설정합니다.  
   * **사용자당 최대 장치 수**: Azure AD에서 사용자가 보유할 수 있는 장치의 최대 수를 선택합니다. 사용자가 이 할당량에 도달하는 경우 기존 장치 중 하나 이상이 제거될 때까지 장치를 더 추가할 수 없습니다.
   * **장치에 연결하기 위해 다단계 인증 필요**: 사용자가 Azure AD에 장치를 연결하기 위해 또 다른 인증 수단을 제공해야 하는 경우 이 옵션을 사용하도록 설정합니다. 다단계 인증에 대한 자세한 내용은 [클라우드에서 Azure Multi-Factor Authentication 시작](multi-factor-authentication-get-started-cloud/)을 참조하세요.
   *  **사용자가 Azure AD에 장치를 조인할 수 있음**: Azure AD에 장치를 조인하도록 허용된 사용자 및 그룹을 선택합니다.   
    * **Azure AD 조인 장치의 추가 관리자**: Azure AD Premium 또는 EMS(Enterprise Mobility Suite)를 사용하여 장치에 대한 로컬 관리자 권한을 부여 받은 사용자를 선택할 수 있습니다. 전역 관리자 및 장치 소유자에게는 기본적으로 로컬 관리자 권한이 부여됩니다.
   
>[AZURE.NOTE]오류 “**장치 등록 제한에 도달**"(오류 코드: **0x801C000E-DSREG\_ E\_ DEVICE\_ REGISTRATION\_ QUOTA\_EXCCEEDED**)이 발생한 경우 사용자별로 허용된 최대 장치를 변경해야 합니다. **사용자가 Azure AD에 장치를 조인할 수 있음** 섹션에서 **추가**를 선택하고 사용자당 허용하려는 장치 수를 설정합니다.
      

    
<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
사용자를 위해 Azure AD 조인을 설정하면 해당 회사 또는 개인 장치를 통해 Azure AD에 연결할 수 있습니다.

다음은 사용자가 Azure AD 조인을 설정할 수 있는 방법에 대한 세 가지 시나리오입니다.

- 사용자가 Azure AD에 직접 회사 소유의 장치 연결
- 사용자가 온-프레미스 Active Directory에 회사 소유의 장치를 연결하고 Azure AD로 확장
- 사용자가 개인 장치의 Windows에 회사 계정 추가 

## 추가 정보
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=Oct15_HO3-->