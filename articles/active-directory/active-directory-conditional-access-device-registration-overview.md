<properties
	pageTitle="Azure Active Directory Device Registration 개요| Microsoft Azure"
	description="장치 기반 조건부 액세스 시나리오의 기초입니다. 장치가 등록될 경우 Azure Active Directory Device Registration은 사용자가 로그인할 때 장치를 인증하는 데 사용되는 ID로 장치를 프로비전합니다."
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
	ms.date="11/24/2015"
	ms.author="femila"/>

# Azure Active Directory Device Registration 개요

Azure Active Directory Device Registration은 장치 기반 조건부 액세스 시나리오의 기초입니다. 장치가 등록될 경우 Azure Active Directory Device Registration은 사용자가 로그인할 때 장치를 인증하는 데 사용되는 ID로 장치를 프로비전합니다. 그런 다음 인증된 장치 및 그 장치의 특성을 사용하여 클라우드 및 온-프레미스에 호스트되는 응용 프로그램에 조건부 액세스 정책을 적용할 수 있습니다. Intune과 같은 모바일 장치 관리 솔루션과 함께 사용할 경우 Azure Active Directory의 장치 특성이 장치에 대한 추가 정보로 업데이트됩니다. 이렇게 하면 장치의 액세스가 보안 및 규정 준수에 대한 표준을 충족하도록 적용하는 조건부 액세스 규칙을 만들 수 있습니다. Azure Active Directory Device Registration은 Azure Active Directory에서 사용할 수 있습니다. 서비스에는 iOS, Android 및 Windows 장치 지원이 포함됩니다. Azure Active Directory Device Registration을 활용하는 개별 시나리오에는 보다 구체적인 요구 사항 및 플랫폼 지원이 있을 수도 있습니다. 현재 사용할 수 있는 특정 시나리오에 대해 자세히 알아보려면 계속 읽어보세요.

## Azure Active Directory Device Registration으로 사용할 수 있는 시나리오

Azure AD 장치 등록을 다양한 시나리오의 기초로 간주할 수 있습니다. 일반적으로 서비스에는 iOS, Android 및 Windows 장치 지원이 포함됩니다. Azure AD 장치 등록을 활용하는 개별 시나리오에는 보다 구체적인 요구 사항 및 플랫폼 지원이 있을 수도 있습니다. 이러한 시나리오는 다음과 같습니다. 온-프레미스에서 호스트되는 응용 프로그램에 대한 조건부 액세스: Windows Server 2012 R2에서 AD FS를 사용하도록 구성된 응용 프로그램에 대한 액세스 정책이 있는 등록된 장치를 사용할 수 있습니다. 온-프레미스에 대해 조건부 액세스를 설정하는 방법에 대한 자세한 내용은 Azure Active Directory Device Registration을 사용하여 온-프레미스 조건부 액세스 설정을 참조하세요.

Microsoft Intune을 사용한 Office 365 응용 프로그램에 대한 조건부 액세스: IT 관리자는 조건부 액세스 장치 정책을 프로비전하여 규격 장치를 사용하는 정보 작업자가 서비스에 액세스할 수 있도록 하는 동시에 회사 리소스를 보호할 수 있습니다. 자세한 내용은 Office 365 서비스에 대한 조건부 액세스 장치 정책을 참조하세요.

##Azure Active Directory Device Registration 설정

Azure Active Directory Device Registration 서비스에 사용할 수 있는 설정은 다음과 같습니다. Azure 포털에서 Azure AD 장치 등록 사용.

Windows 장치는 잘 알려진 DNS 레코드를 찾아 서비스를 검색합니다. Windows 7 및 Windows 8.1 장치가 서비스를 검색 및 사용할 수 있도록 회사 DNS를 구성해야 합니다.

Azure Active Directory에서 관리자 포털을 사용하여 등록된 장치를 보고 사용/사용하지 않도록 설정할 수 있습니다.

## Azure Active Directory Device Registration 사용
다음 섹션에서는 디렉터리에 Azure Active Directory Device Registration 서비스를 사용하도록 설정하는 방법을 설명합니다.
Azure Active Directory Device Registration 서비스를 사용하도록 설정하려면
-------------------------------------------------------------
1. 관리자 권한으로 Azure 포털에 로그온합니다.
1. 왼쪽 창에서 **Active Directory**를 선택합니다.
1. **디렉터리** 탭에서 해당 디렉터리를 선택합니다.
1. **구성** 탭을 선택합니다.
1. **장치** 섹션으로 스크롤합니다.
1. **사용자가 장치에 작업 공간을 연결할 수 있습니다.**에 대해 **모두**를 선택합니다.
1. 사용자당 권한을 부여하려는 최대 장치 수를 선택합니다.

>[AZURE.NOTE]Office 365에 대한 모바일 장치 관리 및 Microsoft Intune에 등록하려면 작업 공간 연결이 필요합니다. 이러한 서비스 중 하나를 구성한 경우 모두가 선택되고 없음 단추는 사용할 수 없습니다.


기본적으로 2단계 인증은 서비스에 대해 사용되지 않습니다. 그러나 장치를 등록하는 경우 2단계 인증을 사용하는 것이 좋습니다.

* 이 서비스에 대해 2단계 인증을 요구하기 전에 Azure Active Directory에서 2단계 인증 공급자를 구성하고 Multi-Factor Authentication에 대해 사용자 계정을 구성해야 합니다. Azure Active Directory에 Multi-Factor Authentication 추가를 참조하세요.

* Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS에서 2단계 인증 모듈을 구성해야 합니다. Active Directory Federation Services에 Multi-Factor Authentication 사용을 참조하세요.

## Azure Active Directory Device Registration 검색 구성
Windows 7 및 Windows 8.1 장치는 사용자 계정 이름을 잘 알려진 장치 등록 서버 이름과 결합하여 장치 등록 서버를 검색합니다. Azure Active Directory Device Registration 서비스와 연결된 A 레코드를 가리키는 DNS CNAME 레코드를 만들어야 합니다. CNAME 레코드는 잘 알려진 접두사 enterpriseregistration과 조직에서 사용자 계정에 사용되는 UPN 접미사를 순서대로 사용해야 합니다. 조직에서 여러 UPN 접미사를 사용하는 경우 DNS에 여러 CNAME 레코드를 만들어야 합니다.

예를 들어 조직에서 @contoso.com 및 @region.contoso.com이라는 두 개의 UPN 접미사를 사용하는 경우 다음과 같은 DNS 레코드를 만듭니다.
 
| 항목 | 형식 | 주소 |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Azure Active Directory에서 장치 개체 보기 및 관리
1. Azure 관리자 포털에서 장치를 보고 차단 및 차단 해제할 수 있습니다. 차단된 장치는 등록된 장치만 허용하도록 구성된 응용 프로그램에 더 이상 액세스할 수 없습니다.
1. 관리자 권한으로 Microsoft Azure 포털에 로그온합니다.
1. 왼쪽 창에서 **Active Directory**를 선택합니다.
1. 디렉터리를 선택합니다.
1. **사용자** 탭을 선택합니다. 그런 다음 사용자를 선택하여 해당 장치를 표시합니다.
1. **장치** 탭을 선택합니다.
1. 드롭다운 메뉴에서 **등록된 장치**를 선택합니다.
1. 여기서 사용자가 등록한 장치를 보거나 차단 또는 차단 해제할 수 있습니다. 

## 추가 항목

Azure AD 장치 등록을 사용하여 Windows 7 및 Windows 8.1 도메인 가입 장치를 등록할 수 있습니다. 다음 항목에서는 Windows 7 및 Windows 8.1 장치에서 장치 등록을 구성하는 데 필요한 단계 및 필수 조건에 대한 자세한 정보를 제공합니다.

- [도메인 가입 Windows 장치의 Azure Active Directory 자동 장치 등록](active-directory-conditional-access-automatic-device-registration.md) 
- [Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Windows 8.1 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

<!---HONumber=AcomDC_1125_2015-->