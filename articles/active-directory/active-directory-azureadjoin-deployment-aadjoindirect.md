<properties 
	pageTitle="Azure AD 조인에 대한 사용 시나리오와 배포 고려 사항 | Microsoft Azure" 
	description="관리자가 최종 사용자(직원, 학생, 다른 사용자)를 위해 Azure AD 조인을 설정하는 방법을 설명하는 항목입니다." 
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
	ms.date="08/02/2015" 
	ms.author="femila"/>

# Azure AD 연결에 대한 사용 시나리오와 배포 고려 사항 

## Azure AD 연결에 대한 사용 시나리오
시나리오 1: 클라우드에서 주로 업무 처리
--------------------------------------------------------
Azure AD 연결은 현재 클라우드에서 업무용 ID를 작동 및 관리하거나 곧 클라우드로 전환하려는 경우에 유용할 수 있습니다. Azure AD에서 만든 계정을 사용하여 Windows 10에 로그인할 수 있습니다. 사용자는 [FRX(첫 실행 경험) 프로세스](active-directory-azureadjoin-user-frx.md)를 사용하거나 [설정 환경](active-directory-azureadjoin-user-upgrade.md)을 통해 Azure AD에 연결하여 Azure AD에 컴퓨터를 연결할 수 있습니다. 이제 사용자는 브라우저나 Office 응용 프로그램에서 SSO를 사용하여 Office 365와 같은 클라우드 리소스에 편리하게 액세스할 수 있습니다.

시나리오 2: 교육 기관 ---------------------------------------------------------------------------------- 교육 기관에는 일반적으로 두 가지 사용자 유형인 교직원 및 학생이 있습니다. 교직원은 조직에서 장기 멤버로 간주되므로 온-프레미스 계정을 만드는 것이 적절합니다. 하지만 학생들은 조직의 단기 멤버이므로 Azure AD에서 관리할 수 있습니다. 따라서 온-프레미스 대신 클라우드에 디렉터리 확장을 적용할 수 있습니다. 이제 이러한 학생들은 자신의 Azure AD 계정으로 Windows에 로그인하고 Office 응용 프로그램에서 Office 365 리소스에 액세스할 수 있습니다.

시나리오 3: 소매 기업
---------------------------------------------------------------------------------------
소매 기업에는 비정규직 근로자가 있고 장기 직원이 있습니다. 장기적인 정규직 직원은 온-프레미스 계정으로 만든 후 도메인에 연결된 컴퓨터를 사용하도록 할 수 있습니다. 하지만 비정규직 근로자는 조직의 단기 멤버이므로 사용자 라이선스를 보다 쉽게 이동할 수 있도록 관리하는 것이 좋습니다. Office 365 라이선스를 사용하여 클라우드에 이러한 사용자를 만들면 이러한 사용자는 Azure AD 계정을 사용하여 Windows 및 Office 응용 프로그램에 로그인할 수 있으며 사용자가 조직을 떠난 후에 해당 라이선스의 이동을 보다 쉽게 관리할 수 있습니다.

시나리오 4: 시나리오
------------------------------------------------------------------------------------------
위에서 설명한 시나리오 외에도, 사용자의 장치를 Azure AD에 연결하여 간소화된 연결 환경, Azure AD의 장치 관리, 자동 MDM 등록 및 Azure AD/온-프레미스 리소스에 대한 Single Sign-On 등에 따른 많은 이점을 얻을 수 있습니다.


##Azure AD 연결에 대한 배포 고려 사항

사용자가 Azure AD에 직접 회사 소유의 장치를 연결하도록 허용
-----------------------------------------------------------------------------------------

기업에서는 파트너 회사 및 조직에 클라우드 전용 계정을 제공할 수 있습니다. 그러면 이러한 파트너는 회사 앱 및 리소스에 쉽게 액세스하고 Single-Sign On을 사용할 수 있습니다. 이 시나리오는 장치를 사용해서 주로 인증을 위해 Azure AD에 의존하는 Office 365 또는 SaaS 앱과 같은 클라우드 리소스에 액세스하는 사용자에게 해당됩니다.

### 필수 조건
**엔터프라이즈 수준(관리자)**

*	Azure Active Directory를 사용하는 Azure 구독  

**사용자 수준**

*	Windows 10(Professional 및 Enterprise SKU)

### 관리자 작업
* [장치 등록 및 MFA 설정](active-directory-azureadjoin-setup.md)

### 사용자 작업
* [설치하는 동안 Azure AD로 새 Windows 10 장치 설정](active-directory-azureadjoin-user-frx.md)
* [설정에서 Azure AD으로 Windows 10 장치 설정](active-directory-azureadjoin-user-upgrade.md)
* [개인 Windows 10 장치를 조직에 연결](active-directory-azureadjoin-personal-device.md)
  


## 조직의 BYOD를 Windows 10에서 사용하도록 설정
사용자 및 직원이 개인 Windows 장치를 사용하여 회사 앱 및 리소스에 액세스하도록 설정할 수 있습니다. 사용자는 안전하고 호환되는 방식으로 작업 리소스에 액세스하기 위해 개인 Windows 장치에 자신의 Azure AD 계정(회사 계정)을 추가할 수 있습니다.

### 필수 조건
**엔터프라이즈 수준(관리자)**

*	Azure AD 구독

**사용자 수준**

*	Windows 10(Professional 및 Enterprise SKU)


### 관리자 작업

* [장치 등록 및 MFA 설정](active-directory-azureadjoin-setup.md)

### 사용자 작업
* [개인 Windows 10 장치를 조직에 연결](active-directory-azureadjoin-personal-device.md)


## 추가 정보
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

<!---HONumber=August15_HO6-->