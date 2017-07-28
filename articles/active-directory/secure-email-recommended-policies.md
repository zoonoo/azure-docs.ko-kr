---
title: "Azure AD 보안 메일 권장 정책 | Microsoft Docs"
description: "메일 정책 및 구성을 적용하는 방법에 대한 Microsoft 권장 사항의 정책을 설명합니다."
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 1747ba5ff55780322eeaae5396099ec373289c98
ms.contentlocale: ko-kr
ms.lasthandoff: 06/08/2017


---

# <a name="recommended-policies"></a>권장된 정책
 
이 문서에서는 고객이 조직의 메일 및 메일 클라이언트를 보호하고 최신 인증 및 조건부 액세스를 지원하는 데 도움이 되는 권장 정책을 설명합니다. 또한 사용자에게 최상의 SSO 환경을 제공하기 위해 권장하는 기본 플랫폼 클라이언트 구성뿐만 아니라 조건부 액세스를 위한 기술적 사전 요구 사항에 대해서도 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 나머지 부분에서 설명하는 정책을 구현하기 전에 조직에서 충족해야 하는 몇 가지 필수 조건이 있습니다.
* [명명된 네트워크 구성](https://docs.microsoft.com/azure/active-directory/active-directory-known-networks-azure-portal). Azure AD ID 보호는 사용 가능한 모든 세션 데이터를 수집하고 분석하여 위험 점수를 생성합니다. Azure AD 명명된 네트워크 구성에서 네트워크에 대한 조직의 공용 IP 범위를 지정하는 것이 좋습니다. 이러한 범위에서 들어오는 트래픽은 위험 점수가 낮게 지정되므로 회사 환경 외부의 트래픽은 높은 위험 점수로 처리됩니다.
* [MFA(Multi-Factor Authentication)로 모든 사용자 등록](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-manage-users-and-devices). Azure AD ID 보호는 Azure MFA를 사용하여 추가 보안 확인을 수행합니다. 모든 사용자가 Azure MFA에 미리 등록하는 것이 좋습니다.
* [도메인 가입 Windows 컴퓨터의 자동 장치 등록 사용](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). 조건부 액세스는 서비스에 연결하는 장치가 도메인 가입 또는 규격 장치임을 보장할 수 있습니다. Windows 컴퓨터에서 이를 지원하려면 장치를 Azure AD에 등록해야 합니다.  이 문서는 자동 장치 등록을 구성하는 방법에 대해 설명합니다.  AD FS는 필수 요구 사항입니다.
* **지원 팀 준비**. MFA를 완료할 수 없는 사용자를 위한 계획을 세워야 합니다. 이러한 사용자를 정책 제외 그룹에 추가하거나 해당 사용자를 위해 새로운 MFA 정보를 등록할 수 있습니다. 이러한 중요 보안 사항을 변경하기 전에 실제 사용자가 요청하는지 확인해야 합니다. 사용자의 관리자가 승인을 받도록 요청하는 것이 효과적인 단계입니다.
* [온-프레미스 AD에 대한 암호 쓰기 저장 구성](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). 암호 쓰기 저장을 사용하면 Azure AD는 계정 손상의 위험 수준이 높게 감지된 경우 사용자에게 온-프레미스 암호를 변경하도록 요구할 수 있습니다. 두 가지 방법 중 하나로 Azure AD Connect를 사용하여 이 기능을 사용하도록 설정할 수 있습니다. Azure AD Connect 설치 마법사의 선택적 기능 화면에서 암호 쓰기 저장을 사용하거나 Windows PowerShell을 통해 설정할 수 있습니다.  
* [최신 인증 사용](https://support.office.com/en-us/article/Enable-Exchange-Online-for-modern-authentication-58018196-f918-49cd-8238-56f57f38d662) 및 [레거시 끝점 보호](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-supported-apps).  조건부 액세스는 최신 인증을 사용하는 모바일과 데스크톱 응용 프로그램 모두에서 작동합니다. 응용 프로그램이 레거시 인증 프로토콜을 사용하는 경우 조건이 적용되더라도 액세스할 수 있습니다. 조건부 액세스 규칙을 사용할 수 있는 응용 프로그램과 다른 응용 프로그램 진입점을 보호하기 위해 수행해야 하는 조치를 이해하는 것이 중요합니다.
* Rights Management를 활성화하여 [Azure Information Protection 사용](https://docs.microsoft.com/en-us/information-protection/get-started/infoprotect-tutorial-step1). Azure Information Protection을 메일과 함께 사용하면 메일 분류로 시작할 수 있습니다. 빠른 시작 자습서에 따라 정책을 사용자 지정하고 게시합니다.  

### <a name="recommended-email-clients"></a>권장되는 메일 클라이언트
다음 메일 클라이언트는 최신 인증 및 조건부 액세스를 지원합니다. Azure Information Protection은 아직 모든 클라이언트에 대해 사용할 수 없습니다.

|플랫폼|클라이언트|버전/노트|Azure Information Protection|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2016, 2013([최신 인증 사용]((https://support.office.com/en-us/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910)))|예|
|**iOS**|Outlook|[최신](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|아니요|
|**Android**|Outlook|[최신](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|아니요|
|**macOS**|지원 출시 예정||아니요|
|**Linux**|지원되지 않음||아니요|

#### <a name="additional-client-software"></a>추가 클라이언트 소프트웨어
Azure Information Protection으로 보호된 문서에 액세스하려면 추가 소프트웨어가 필요할 수 있습니다. Azure Information Protection으로 보호된 문서를 만들고 보려면 [지원되는 소프트웨어 및 문서 형식](https://docs.microsoft.com/information-protection/get-started/requirements-applications)을 사용하고 있는지 확인합니다.

## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>SSO 및 조건부 액세스에 권장되는 클라이언트 구성
이 섹션에서는 사용자에게 최상의 SSO 환경을 제공하기 위해 권장하는 기본 플랫폼 클라이언트 구성뿐만 아니라 조건부 액세스를 위한 기술적 필수 조건에 대해 설명합니다.

### <a name="windows-devices"></a>Windows 장치
Azure는 온-프레미스와 Azure AD 모두에서 가능한 가장 매끄러운 SSO 환경을 제공하도록 설계되어 있으므로 Windows 10(버전 1703 이상)을 권장합니다. 회사 또는 학교에서 실행하는 장치는 Azure AD에 직접 가입하도록 구성해야 하며, 조직에서 온-프레미스 AD 도메인 가입을 사용하는 경우 해당 장치는 [Azure AD에 자동으로 등록되도록 구성](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)되어야 합니다. 

BYOD Windows 장치의 경우 사용자는 "회사 또는 학교 계정 추가"를 사용할 수 있습니다. BYOD Windows 장치의 경우 사용자는 "회사 또는 학교 계정 추가"를 사용할 수 있습니다. Windows 10에서 Chrome 브라우저를 사용하는 사용자는 [확장을 설치](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog)해야 Edge/IE와 동일한 매끄러운 로그인 환경을 사용할 수 있습니다. 또한 조직에 도메인 가입 Windows7 장치가 있으면 비 Windows 10 컴퓨터에 대한 Microsoft Workplace Join [패키지를 설치하여 Azure AD에 장치를 등록](https://www.microsoft.com/en-us/download/details.aspx?id=53554)할 수 있습니다.

### <a name="ios-devices"></a>iOS 장치

조건부 액세스 또는 MFA 정책을 배포하기 전에 [Microsoft Authenticator 앱](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)을 사용자 장치에 설치하는 것이 좋습니다. 최소한 사용자가 회사 또는 학교 계정을 추가하여 Azure AD에 [장치를 등록하라는 요청을 받았거나](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time) Intune 회사 포털 앱을 설치하여 해당 장치를 관리에 등록하는 경우 이 앱을 설치해야 합니다. 이는 구성된 조건부 액세스 정책에 따라 다릅니다.

### <a name="android-devices"></a>Android 장치

사용자는 조건부 액세스 정책이 배포되기 전 또는 특정 인증 시도 중 필요할 때 [Intune 회사 포털 앱](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
) 및 [Microsoft Authenticator 앱](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)을 설치하는 것이 좋습니다. 앱을 설치한 후 Azure AD에 등록하거나 Intune에 장치를 등록하라는 요청을 받을 수 있습니다. 이는 구성된 조건부 액세스 정책에 따라 다릅니다. 

또한 Intune MDM 정책에 따라 메일 계정을 관리하고 보호할 수 있도록 Android for Work 또는 Samsung Knox를 지원하는 OEM 및 버전에서 COD(회사 소유 장치)를 표준화하는 것이 좋습니다.

## <a name="tiers-of-security-and-protection"></a>보안 및 보호 계층

대부분의 조직에는 보안 및 데이터 보호와 관련된 특정 요구 사항이 있습니다. 이러한 요구 사항은 산업 세그먼트 및 조직 내 작업 기능에 따라 다릅니다. 예를 들어 법률 부서와 Office 365 관리자는 메일 서신에 대해 다른 비즈니스 단위 사용자에게 필요하지 않은 추가 보안 및 정보 보호 제어를 요구할 수 있습니다. 

각 산업에는 고유한 일련의 특수한 규정이 있습니다. 이 문서는 가능한 모든 보안 옵션 목록이나 산업 세그먼트 또는 작업 기능별로 권장 사항을 제공하는 것이 아니라 요구 사항의 세분성에 따라 적용될 수 있는 메일에 대한 보안 및 보호의 3가지 계층([기본, 중요 및 매우 규제](https://go.microsoft.com/fwlink/p/?linkid=841656))에 따른 권장 사항을 제공합니다.  

**기본** 데이터에 액세스하는 ID 및 장치뿐만 아니라 데이터를 보호하기 위한 최소 표준을 설정하는 것이 좋습니다. 많은 조직의 요구 사항을 충족시키는 강력한 기본 보호를 제공하기 위해 기본 권장 사항을 따를 수 있습니다. 

**중요** 일부 고객은 데이터 하위 집합을 상위 레벨에서 보호해야 하거나 모든 데이터를 상위 레벨에서 보호해야 할 수 있습니다. Office 365 환경에서 모든 데이터 집합 또는 특정 데이터 집합에 향상된 보호를 적용할 수 있습니다. 중요한 데이터에 액세스하는 ID 및 장치를 비교 가능한 보안 수준으로 보호하는 것이 좋습니다. 

**매우 규제** 일부 조직에는 매우 적은 양의 기밀 데이터, 영업 비밀 또는 규제된 데이터가 있을 수 있습니다. Microsoft는 ID 및 장치에 대한 추가 보호를 포함하여 조직이 이러한 요구 사항을 충족할 수 있도록 하는 기능을 제공합니다. 

### <a name="default-protection-mechanism-recommendations"></a>기본 보호 메커니즘 권장 사항


다음 표에는 이전에 정의한 각각의 보안 및 보호 계층에 대한 기본 보호 메커니즘 권장 사항이 나와 있습니다.

|보호 메커니즘|기본|중요|매우 규제|
|:-------------------|:-------|:--------|:---------------|
|**MFA 적용**|중간 이상의 로그인 위험에서|낮음 이상의 로그인 위험에서|모든 새 세션에서|
|**암호 변경 적용**|위험 수준이 높은 사용자에 대해|위험 수준이 높은 사용자에 대해|위험 수준이 높은 사용자에 대해|
|**Intune 응용 프로그램 보호 적용**|예|예|예|
|**Intune 등록 적용(COD)**|규격 장치 또는 도메인 가입 장치 필요|규격 장치 또는 도메인 가입 장치 필요|규격 장치 또는 도메인 가입 장치 필요|

### <a name="device-ownership"></a>장치 소유권
위의 표는 많은 조직에서 COD(회사 소유 장치)뿐만 아니라 개인 장치 또는 BYOD(bring-your-own device)를 지원하여 해당 리소스에서 모바일 생산성을 지원하는 추세를 반영합니다. Intune 앱 보호 정책은 메일이 COD 및 BYOD에서 Outlook 모바일 앱 및 다른 Office 모바일 앱을 벗어나지 않도록 보호합니다.  

회사 소유 장치는 추가 보호 및 제어를 적용하기 위해 Intune에서 관리하거나 도메인에 가입되어 있어야 합니다.  데이터 중요도에 따라 조직에서 특정 사용자 집단 또는 특정 앱에 BYOD를 허용하지 않도록 선택할 수 있습니다.

## <a name="baseline"></a>기초 
이 섹션에서는 데이터, ID 및 장치 보호의 기본 계층에 대한 보안 메일 권장 사항에 대해 설명합니다. 이러한 권장 사항은 많은 조직의 기본 보호 요구 사항을 충족해야 합니다.
>[!NOTE]
>아래 정책은 누적될 수 있으며 서로를 기반으로 작성됩니다. 각 섹션은 각 계층에 적용된 추가 사항만 설명합니다.
>

### <a name="conditional-access-policy-settings"></a>조건부 액세스 정책 설정

#### <a name="identity-protection"></a>ID 보호 
이전 섹션에서 설명한대로 사용자에게 SSO(Single Sign-On) 환경을 제공할 수 있습니다. [위험 이벤트](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)를 기반으로 필요한 경우에만 개입해야 합니다.  

* 중간 이상의 로그인 위험을 기반으로 MFA 필요
* 위험 수준이 높은 사용자의 경우 안전한 암호 변경 필요

>[!IMPORTANT]
>이 정책 권장 사항에는 [암호 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) 및 [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)이 필요합니다.
>

#### <a name="data-loss-prevention"></a>데이터 손실 방지 
장치 및 앱 관리 정책의 목표는 장치 분실 또는 도난 시 데이터 손실을 보호하는 것입니다. PIN으로 데이터에 대한 액세스를 보호하고 장치에서 데이터를 암호화하며 장치가 손상되지 않도록 하여 이를 수행할 수 있습니다.

|정책 권장 사항|설명|
|:--------------------|:----------|
|**사용자 PC 관리 필요**|사용자가 PC를 Active Directory 도메인에 가입하거나 Intune 또는 Configuration Manager를 사용한 관리에 등록해야 합니다.|
|**도메인 가입 PC에 대해 Configuration Manager 정책 또는 GPO(그룹 정책 개체)를 통해 보안 설정 적용**|BitLocker를 사용하고 바이러스 백신을 사용하며 방화벽을 사용하도록 관리되는 PC를 구성하는 정책 배포|
|**사용자 모바일 장치 관리 필요**|메일에 액세스하는 데 사용되는 사용자 장치는 Intune에서 관리해야 하거나 회사 메일은 Outlook Mobile과 같은 Intune 앱 보호 정책에 의해 보호되는 모바일 메일 앱을 통해서만 액세스해야 합니다.|
|**관리 장치에 Intune 장치 준수 정책 적용**|관리되는 회사 모바일 장치 및 Intune 관리 PC에 대해 최소 길이가 6인 PIN, 장치 암호화, 정상 상태의 장치(무단 해제 및 루팅되지 않음, 상태 증명 통과) 및 가능한 경우 Lookout 또는 SkyCure와 같은 타사 MTP에서 결정한 위험이 낮은 장치를 요구하는 Intune 장치 준수 정책을 적용합니다.|
|**관리되지 않는 장치에서 실행되는 관리되는 앱에 Intune 앱 보호 정책 적용**|관리되지 않는 개인 모바일 장치에서 실행 중인 관리 앱에 대해 최소 길이가 6인 PIN, 장치 암호화, 정상 상태의 장치(무단 해제 및 루팅되지 않음, 상태 증명 통과)를 요구하는 Intune 앱 보호 정책을 적용합니다.|

### <a name="user-impact"></a>사용자 영향

대부분의 조직에서는 메일에 액세스하기 위해 Office 365에 로그인해야 하는 시기와 조건에 대해 사용자의 기대치를 설정할 수 있어야 합니다.  

사용자는 일반적으로 다음 상황을 제외하고는 SSO(Single Sign-On)를 활용합니다. 
* Exchange Online에 대한 인증 토큰을 요청할 때:
  * 중간 이상의 로그인 위험이 감지되고 사용자가 현재 세션에서 아직 MFA를 수행하지 않은 경우 사용자에게 MFA를 요청할 수 있습니다.  
  * 사용자는 Intune 앱 보호 SDK를 지원하는 메일 앱을 사용하거나 Intune 규격 또는 AD 도메인 가입 장치에서 메일에 액세스해야 합니다. 
* 사용자가 로그인 위험에 있을 때 MFA를 성공적으로 완료하면 암호를 변경하라는 요청을 받습니다.

## <a name="sensitive"></a>중요

이 섹션에서는 데이터, ID 및 장치 보호의 중요 계층에 대한 보안 메일 권장 사항을 설명합니다. 이러한 권장 사항은 데이터 하위 집합을 상위 레벨에서 보호해야 하거나 모든 데이터를 상위 레벨에서 보호해야 하는 고객을 위한 것입니다. 

Office 365 환경에서 모든 데이터 집합 또는 특정 데이터 집합에 향상된 보호를 적용할 수 있습니다. 예를 들어 정책을 적용하여 보호된 앱 간에만 중요한 데이터를 공유함으로써 데이터 손실을 방지할 수 있습니다. 중요한 데이터에 액세스하는 ID 및 장치를 비교 가능한 보안 수준으로 보호하는 것이 좋습니다. 

### <a name="conditional-access-policy-settings"></a>조건부 액세스 정책 설정
#### <a name="identity-protection"></a>ID 보호 

이전 섹션에서 설명한대로 사용자에게 SSO(Single Sign-On) 환경을 제공할 수 있습니다. [위험 이벤트](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)를 기반으로 필요한 경우에만 개입해야 합니다.   

* 낮음 이상의 세션에서 MFA 필요
 * 위험 수준이 높은 사용자의 경우 안전한 암호 변경 필요

>[!IMPORTANT]
>이 정책 권장 사항에는 [암호 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) 및 [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)이 필요합니다.
>

#### <a name="data-loss-prevention"></a>데이터 손실 방지 

이러한 장치 및 앱 관리 정책의 목표는 장치 분실 또는 도난 시 데이터 손실을 보호하는 것입니다. PIN으로 데이터에 대한 액세스를 보호하고 장치에서 데이터를 암호화하며 장치가 손상되지 않도록 하여 이를 수행할 수 있습니다.

|정책 권장 사항|설명|
|:--------------------|:----------|
|**사용자 PC 관리 필요**|메일 액세스를 허용하기 전에 사용자가 PC를 Active Directory 도메인에 가입하거나 Intune 또는 Configuration Manager를 사용한 관리에 등록하도록 하고 해당 장치가 정책을 준수하는지 확인해야 합니다.|
|**도메인 가입 PC에 대해 Configuration Manager 정책 또는 GPO(그룹 정책 개체)를 통해 보안 설정 적용**|BitLocker를 사용하고 바이러스 백신을 사용하며 방화벽을 사용하도록 관리되는 PC를 구성하는 정책 배포|
|**사용자 모바일 장치 관리 필요**|메일에 액세스하는 데 사용되는 사용자 장치는 Intune에서 관리해야 하거나 회사 메일은 Outlook Mobile과 같은 Intune 앱 보호 정책에 의해 보호되는 모바일 메일 앱을 통해서만 액세스해야 합니다.|
|**관리 장치에 Intune 장치 준수 정책 적용**|관리되는 회사 모바일 장치 및 Intune 관리 PC에 대해 최소 길이가 6인 PIN, 장치 암호화, 정상 상태의 장치(무단 해제 및 루팅되지 않음, 상태 증명 통과) 및 가능한 경우 Lookout 또는 SkyCure와 같은 타사 MTP에서 결정한 위험이 낮은 장치를 요구하는 Intune 장치 준수 정책을 적용합니다.|
|**관리되지 않는 장치에서 실행되는 관리되는 앱에 Intune 앱 보호 정책 적용**|관리되지 않는 개인 모바일 장치에서 실행 중인 관리 앱에 대해 최소 길이가 6인 PIN, 장치 암호화, 정상 상태의 장치(무단 해제 및 루팅되지 않음, 상태 증명 통과)를 요구하는 Intune 앱 보호 정책을 적용합니다.|

### <a name="user-impact"></a>사용자 영향

대부분의 조직에서는 Office 365 메일에 로그인해야 하는 시기와 조건에 관련된 사용자의 기대치를 설정할 수 있어야 합니다. 

사용자는 일반적으로 다음 상황을 제외하고는 SSO(Single Sign-On)를 활용합니다. 
* Exchange Online에 대한 인증 토큰을 요청할 때:
  * 낮음 이상의 로그인 위험이 감지되고 사용자가 현재 세션에서 아직 MFA를 수행하지 않은 경우 사용자에게 MFA를 요청합니다.  
  * 사용자는 Intune 앱 보호 SDK를 지원하는 메일 앱을 사용하거나 Intune 규격 또는 AD 도메인 가입 장치에서 메일에 액세스해야 합니다. 
* 사용자가 로그인 위험에 있을 때 MFA를 성공적으로 완료하면 암호를 변경하라는 요청을 받습니다.

## <a name="highly-regulated"></a>매우 규제
이 섹션에서는 데이터, ID 및 장치 보호의 매우 규제 계층에 대한 보안 메일 권장 사항을 설명합니다. 이러한 권장 사항은 매우 적은 양의 기밀 데이터, 영업 비밀 또는 규제된 데이터가 있을 수 있는 고객을 위한 것입니다. Microsoft는 ID 및 장치에 대한 추가 보호를 포함하여 조직이 이러한 요구 사항을 충족할 수 있도록 하는 기능을 제공합니다. 

### <a name="conditional-access-policy-settings"></a>조건부 액세스 정책 설정
#### <a name="identity-protection"></a>ID 보호 

Microsoft는 매우 규제 계층의 경우 모든 새 세션에 대해 MFA를 적용할 것을 권장합니다.
* 모든 새 세션에 대해 MFA 필요
* 위험 수준이 높은 사용자의 경우 안전한 암호 변경 필요

>[!IMPORTANT]
>이 정책 권장 사항에는 [암호 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) 및 [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)이 필요합니다.
>

#### <a name="data-loss-prevention"></a>데이터 손실 방지
이러한 장치 및 앱 관리 정책의 목표는 장치 분실 또는 도난 시 데이터 손실을 보호하는 것입니다. PIN으로 데이터에 대한 액세스를 보호하고 장치에서 데이터를 암호화하며 장치가 손상되지 않도록 하여 이를 수행합니다.

매우 규제 계층의 경우 Intune 규격 또는 도메인 가입 장치에서만 실행되는 Intune 앱 보호 정책을 지원하는 앱을 요청하는 것이 좋습니다.

|정책 권장 사항|설명|
|:--------------------|:----------|
|**사용자 PC 관리 필요**|메일 액세스를 허용하기 전에 사용자가 PC를 Active Directory 도메인에 가입하거나 Intune 또는 Configuration Manager를 사용한 관리에 등록하도록 하고 해당 장치가 정책을 준수하는지 확인해야 합니다.|
|**도메인 가입 PC에 대해 Configuration Manager 정책 또는 GPO(그룹 정책 개체)를 통해 보안 설정 적용**|BitLocker를 사용하고 바이러스 백신을 사용하며 방화벽을 사용하도록 관리되는 PC를 구성하는 정책 배포|
|**사용자 모바일 장치 관리 필요**|메일에 액세스하는 데 사용되는 사용자 장치는 Intune에서 관리해야 하거나 회사 메일은 Outlook Mobile과 같은 Intune 앱 보호 정책에 의해 보호되는 모바일 메일 앱을 통해서만 액세스해야 합니다.|
|**관리 장치에 Intune 장치 준수 정책 적용**|관리되는 회사 모바일 장치 및 Intune 관리 PC에 대해 최소 길이가 6인 PIN, 장치 암호화, 정상 상태의 장치(무단 해제 및 루팅되지 않음, 상태 증명 통과) 및 가능한 경우 Lookout 또는 SkyCure와 같은 타사 MTP에서 결정한 위험이 낮은 장치를 요구하는 Intune 장치 준수 정책을 적용합니다.|

### <a name="user-impact"></a>사용자 영향
대부분의 조직에서는 Office 365 메일에 로그인해야 하는 시기와 조건에 관련된 사용자의 기대치를 설정할 수 있어야 합니다. 

* Single Sign-On 세션의 최대 수명은 1일입니다. 세션이 만료되면 사용자는 MFA를 사용하여 다시 인증해야 합니다.
* 사용자가 로그인 위험에 있을 때 MFA를 완료하면 암호를 변경하라는 요청을 받습니다.
* Exchange Online에 대한 인증 토큰을 요청할 때:
  * 사용자는 새 세션을 시작할 때마다 MFA를 수행하라는 요청을 받습니다.  
  * 사용자는 Intune 앱 보호 SDK를 지원하는 메일 앱을 사용해야 합니다.
  * 사용자는 Intune 규격 또는 AD 도메인 가입 장치에서 메일에 액세스해야 합니다. 
 
 ## <a name="next-steps"></a>다음 단계
 [권장된 정책 배포](secure-email-deploy-recommended-policies.md)

