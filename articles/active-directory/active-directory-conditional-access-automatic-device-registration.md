<properties
	pageTitle="도메인 가입 Windows 장치의 Azure Active Directory 자동 장치 등록| Microsoft Azure"
	description="IT 관리자는 도메인 가입 Windows 장치를 Azure AD(Azure Active Directory)에 자동으로 등록하도록 선택할 수 있습니다."
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
	ms.date="03/07/2016"
	ms.author="femila"/>

# Windows 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록

IT 관리자는 도메인 가입 Windows 장치를 Azure AD(Azure Active Directory)에 자동으로 등록하도록 선택할 수 있습니다. 이 기능은 AD FS에서 온-프레미스로 관리하는 Office365 응용 프로그램에 대한 장치 기반 조건부 액세스 정책을 구성한 경우에 유용할 수 있습니다. [Azure Active Directory 장치 등록 개요](active-directory-conditional-access-device-registration-overview.md)를 읽으면 장치 등록 시나리오에 대해 자세히 알아볼 수 있습니다.

Azure Active Directory 자동 장치 등록은 Active Directory 도메인에 가입된 Windows 7 및 Windows 8.1 컴퓨터에 사용할 수 있습니다. 일반적으로 정보 근로자에게 제공된 회사 소유 컴퓨터가 이에 해당합니다.

도메인 가입 Windows 장치를 Azure AD에 등록하는 작업을 시작하려면 아래 필수 조건을 따르세요. 필수 조건을 완료한 후 도메인 가입 Windows 장치에 대해 자동 장치 등록을 구성합니다.

## Windows 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록을 위한 필수 조건

Azure Active Directory Connect를 사용하여 AD FS 배포 및 Azure Active Directory에 연결
----------------------------------------------------------------------------------------------
1. Azure Active Directory Connect를 사용하여 Windows Server 2012 R2와 함께 AD FS(Active Directory Federation Services)를 배포하고 Azure Active Directory와의 페더레이션 관계를 설정합니다.
2. Azure Active Directory 신뢰 당사자 트러스트 클레임 규칙을 추가로 구성합니다.
3. AD FS 관리 콘솔을 열고 **AD FS**>**트러스트 관계>신뢰 당사자 트러스트**로 이동합니다. Microsoft Office 365 ID 플랫폼 신뢰 당사자 트러스트 개체를 마우스 오른쪽 단추로 클릭하고 **클레임 규칙 편집...**을 선택합니다.
4. **발급 변환 규칙** 탭에서 **규칙 추가**를 선택합니다.
5. **클레임 규칙** 템플릿 드롭다운 상자에서 **사용자 지정 규칙을 사용하여 클레임 보내기**를 선택합니다. **다음**을 선택합니다.
6. **클레임 규칙 이름:** 텍스트 상자에 *인증 방법 클레임 규칙* 을 입력합니다.
7. **클레임 규칙:** 텍스트 상자에 다음 클레임 규칙을 입력합니다.

        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
    
8. **확인**을 두 번 클릭하여 대화 상자를 완료합니다.

Azure Active Directory 신뢰 당사자 트러스트 인증 클래스 참조 추가 구성
-----------------------------------------------------------------------------------------------------
페더레이션 서버에서 Windows PowerShell 명령 창을 열고 다음을 입력합니다.


  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`
   
여기서 <RPObjectName>은 Azure Active Directory 신뢰 당사자 트러스트 개체에 대한 신뢰 당사자 개체 이름입니다. 일반적으로 이 개체의 이름은 Microsoft Office 365 ID 플랫폼입니다.

AD FS 전역 인증 정책
-----------------------------------------------------------------------------
인트라넷에 대해 Windows 통합 인증을 허용하도록 AD FS 전역 기본 인증 정책을 구성합니다(기본값임).


Internet Explorer 구성
------------------------------------------------------------------------------
Windows 장치의 Internet Explorer에서 로컬 인트라넷 보안 영역에 대해 다음 설정을 구성합니다.

- 인증서가 하나만 있는 경우 클라이언트 인증서 선택 안 함: **사용**
- 스크립팅 허용: **사용**
- 인트라넷 영역에서만 자동으로 로그온: **선택**

이러한 설정은 Internet Explorer 로컬 인트라넷 보안 영역에 대한 기본 설정입니다. **인터넷 옵션** > **보안** > 로컬 인트라넷 > 사용자 지정 수준으로 이동하여 Internet Explorer에서 설정을 보거나 관리할 수 있습니다. Active Directory 그룹 정책을 사용하여 설정을 구성할 수도 있습니다.

네트워크 연결
-------------------------------------------------------------
도메인 가입 Windows 장치가 Azure AD에 자동으로 등록되려면 AD FS 및 Active Directory 도메인 컨트롤러에 연결되어 있어야 합니다. 일반적으로 이는 컴퓨터가 회사 네트워크에 연결되어 있어야 함을 의미합니다. 여기에는 유선 연결, Wi-Fi 연결, DirectAccess 또는 VPN이 포함될 수 있습니다.

## Azure Active Directory Device Registration 검색 구성
Windows 7 및 Windows 8.1 장치는 사용자 계정 이름을 잘 알려진 장치 등록 서버 이름과 결합하여 장치 등록 서버를 검색합니다. Azure Active Directory Device Registration 서비스와 연결된 A 레코드를 가리키는 DNS CNAME 레코드를 만들어야 합니다. CNAME 레코드는 잘 알려진 접두사 **enterpriseregistration**과 조직에서 사용자 계정에 사용되는 UPN 접미사를 순서대로 사용해야 합니다. 조직에서 여러 UPN 접미사를 사용하는 경우 DNS에 여러 CNAME 레코드를 만들어야 합니다.

예를 들어 조직에서 @contoso.com 및 @region.contoso.com이라는 두 개의 UPN 접미사를 사용하는 경우 다음과 같은 DNS 레코드를 만듭니다.

| 항목 | 형식 | 주소 |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

##Windows 7 및 Windows 8.1 도메인 가입 장치에 대해 자동 장치 등록 구성

아래 링크를 사용하여 Windows 7 및 Windows 8.1 도메인 가입 장치에 대해 자동 장치 등록을 구성합니다. 계속하기 전에 위의 필수 조건을 완료했는지 확인합니다.

* [Windows 8.1 도메인 가입 장치에 대해 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)

* [Windows 7 도메인 가입 장치에 대해 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows7.md)

* [Windows 10 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록](active-directory-azureadjoin-devices-group-policy.md)

추가 참고 사항
--------------------------------------------------------------------

Azure AD 장치 등록은 가장 광범위한 장치 기능을 제공합니다. Azure AD 장치 등록을 사용하여 개인(BYOD) 모바일 장치 및 회사 소유 도메인 가입 장치를 둘 다 등록할 수 있습니다. Office 365와 같은 호스티드 서비스와 AD FS를 사용하여 온-프레미스에서 관리되는 서비스 둘 다에서 장치를 사용할 수 있습니다.

모바일 및 기존 장치를 둘 다 사용하거나 Office 365, Azure AD 또는 다른 Microsoft 서비스를 사용하는 회사는 Azure AD 장치 등록 서비스를 사용하여 Azure AD에 장치를 등록해야 합니다. 회사에서 모바일 장치를 사용하지 않으며 office 365 또는 Microsoft Intune과 같은 Microsoft 서비스를 사용하지 않고 대신 온-프레미스 응용 프로그램만 호스트하는 경우 AD FS를 사용하여 Active Directory에 장치를 등록할 수 있습니다.

[여기](https://technet.microsoft.com/library/dn486831.aspx)서 AD FS를 사용하여 장치 등록을 배포하는 방법에 대해 자세히 알아볼 수 있습니다.

## 추가 항목

- [Azure Active Directory Device Registration 개요](active-directory-conditional-access-device-registration-overview.md)
- [Windows 7 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Windows 8.1 도메인 가입 장치에 대한 자동 장치 등록 구성](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Windows 10 도메인에 가입된 장치의 Azure Active Directory 자동 장치 등록](active-directory-azureadjoin-devices-group-policy.md)

<!---HONumber=AcomDC_0413_2016-->