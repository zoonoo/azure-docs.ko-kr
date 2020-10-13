---
title: 조건부 액세스 정책-Azure Active Directory의 조건
description: Azure AD 조건부 액세스 정책의 조건
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dddad5692ab2eebb6ef9b427c091be449791d7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818350"
---
# <a name="conditional-access-conditions"></a>조건부 액세스: 조건

관리자는 조건부 액세스 정책 내에서 위험, 장치 플랫폼 또는 위치와 같은 조건의 신호를 활용 하 여 정책 결정을 강화할 수 있습니다. 

[![조건부 액세스 정책 정의 및 조건 지정](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

여러 조건을 결합 하 여 세분화 된 특정 조건부 액세스 정책을 만들 수 있습니다.

예를 들어 중요 한 응용 프로그램에 액세스 하는 경우 관리자는 multi-factor authentication과 같은 다른 컨트롤 외에도 Id 보호 및 위치에서의 로그인 위험 정보를 액세스 결정으로 나눌 수 있습니다.

## <a name="sign-in-risk"></a>로그인 위험

[Id 보호](../identity-protection/overview-identity-protection.md)에 대 한 액세스 권한이 있는 고객은 조건부 액세스 정책의 일부로 로그인 위험을 평가할 수 있습니다. 로그인 위험은 id 소유자가 지정 된 인증 요청에 권한이 부여 되지 않은 확률을 나타냅니다. 로그인 위험에 대 한 자세한 내용은 문서, [위험 요소](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) 및 [방법: 위험 정책 구성 및 사용](../identity-protection/howto-identity-protection-configure-risk-policies.md)에 나와 있습니다.

## <a name="user-risk"></a>사용자 위험 

[Id 보호](../identity-protection/overview-identity-protection.md)에 대 한 액세스 권한이 있는 고객의 경우 조건부 액세스 정책의 일부로 사용자 위험을 평가할 수 있습니다. 사용자 위험은 지정 된 id 또는 계정이 손상 될 확률을 나타냅니다. 사용자 위험에 대 한 자세한 내용은 문서, [위험 요소](../identity-protection/concept-identity-protection-risks.md#user-risk) 및 [방법: 위험 정책 구성 및 사용](../identity-protection/howto-identity-protection-configure-risk-policies.md)에 나와 있습니다.

## <a name="device-platforms"></a>디바이스 플랫폼

장치 플랫폼은 장치에서 실행 되는 운영 체제를 특징으로 합니다. Azure AD는 장치에서 제공 하는 정보 (예: 사용자 에이전트 문자열)를 사용 하 여 플랫폼을 식별 합니다. 사용자 에이전트 문자열을 수정할 수 있으므로이 정보는 확인 되지 않습니다. 장치 플랫폼은 Microsoft Intune 장치 준수 정책과 함께 사용 하거나 block 문의 일부로 사용 해야 합니다. 기본값은 모든 장치 플랫폼에 적용 하는 것입니다.

Azure AD 조건부 액세스는 다음과 같은 장치 플랫폼을 지원 합니다.

- Android
- iOS
- Windows Phone
- Windows
- macOS

**다른 클라이언트** 상태를 사용 하 여 레거시 인증을 차단 하는 경우 장치 플랫폼 조건을 설정할 수도 있습니다.

> [!IMPORTANT]
> 지원 되지 않는 장치 플랫폼에 대 한 조건부 액세스 정책을 사용 하는 것이 좋습니다. 예를 들어 Linux 또는 기타 지원 되지 않는 클라이언트에서 회사 리소스에 대 한 액세스를 차단 하려면 모든 장치를 포함 하는 장치 플랫폼 조건으로 정책을 구성 하 고 지원 되는 장치 플랫폼을 제외 하 고 액세스를 차단 하도록 컨트롤 집합을 부여 해야 합니다.

## <a name="locations"></a>위치

위치를 조건으로 구성 하는 경우 조직에서 위치를 포함 하거나 제외 하도록 선택할 수 있습니다. 이러한 명명 된 위치에는 공용 IPv4 네트워크 정보, 국가 또는 지역 또는 특정 국가나 지역에 매핑되지 않는 알 수 없는 영역도 포함 될 수 있습니다. IP 범위만 신뢰할 수 있는 위치로 표시할 수 있습니다.

**모든 위치**를 포함 하는 경우이 옵션에는 명명 된 위치를 구성 하는 것이 아니라 인터넷의 모든 IP 주소가 포함 됩니다. 모든 **위치**를 선택 하는 경우 관리자는 **신뢰할 수 있는** 위치나 **선택한 위치**를 모두 제외 하도록 선택할 수 있습니다.

예를 들어, 일부 조직에서는 사용자가 실제 본사와 같은 신뢰할 수 있는 위치에서 네트워크에 연결 된 경우 multi-factor authentication을 요구 하지 않도록 선택할 수 있습니다. 관리자는 모든 위치를 포함 하지만 본사 네트워크에 대해 선택한 위치를 제외 하는 정책을 만들 수 있습니다.

위치에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건](location-condition.md)문서에서 찾을 수 있습니다.

## <a name="client-apps"></a>클라이언트 앱

기본적으로 새로 만든 모든 조건부 액세스 정책은 클라이언트 앱 조건이 구성 되지 않은 경우에도 모든 클라이언트 앱 유형에 적용 됩니다. 

> [!NOTE]
> 클라이언트 앱 조건의 동작은 8 월 2020에 업데이트 되었습니다. 기존 조건부 액세스 정책이 있는 경우 변경 되지 않은 상태로 유지 됩니다. 그러나 기존 정책을 클릭 하면 구성 토글이 제거 되 고 정책이 적용 되는 클라이언트 앱이 선택 됩니다.

> [!IMPORTANT]
> 레거시 인증 클라이언트의 로그인은 MFA를 지원 하지 않으며, 장치 상태 정보를 Azure AD에 전달 하지 않으므로 MFA 또는 규격 장치 요구와 같은 조건부 액세스 권한 부여 컨트롤에 의해 차단 됩니다. 레거시 인증을 사용 해야 하는 계정이 있는 경우 정책에서 해당 계정을 제외 하거나 최신 인증 클라이언트에만 적용 되도록 정책을 구성 해야 합니다.

**예** 로 설정 된 경우 토글 **구성** 은 선택 된 항목에 적용 되 고, **아니요** 로 설정 하면 최신 및 레거시 인증 클라이언트를 비롯 한 모든 클라이언트 앱에 적용 됩니다. 이 토글은 8 월 2020 일 이전에 만든 정책에 표시 되지 않습니다.

- 최신 인증 클라이언트
   - 브라우저
      - 여기에는 SAML, WS-FEDERATION, Openid connect Connect 또는 OAuth 기밀 클라이언트로 등록 된 서비스와 같은 프로토콜을 사용 하는 웹 기반 응용 프로그램이 포함 됩니다.
   - 모바일 앱 및 데스크톱 클라이언트
      -  이 옵션에는 Office 데스크톱 및 전화 응용 프로그램과 같은 응용 프로그램이 포함 됩니다.
- 레거시 인증 클라이언트
   - Exchange ActiveSync 클라이언트
      - 여기에는 EAS (Exchange ActiveSync) 프로토콜의 모든 사용이 포함 됩니다.
      - 정책에서 Exchange ActiveSync 사용을 차단 하는 경우 영향을 받는 사용자는 단일 격리 전자 메일을 받게 됩니다. 이 전자 메일은 차단 된 이유에 대 한 정보를 제공 하 고 가능 하면 재구성 지침을 포함 합니다.
      - 관리자는 조건부 액세스 MS Graph API을 통해 지원 되는 플랫폼 (예: iOS, Android 및 Windows)에만 정책을 적용할 수 있습니다.
   - 기타 클라이언트
      - 이 옵션은 최신 인증을 지원 하지 않는 기본/레거시 인증 프로토콜을 사용 하는 클라이언트를 포함 합니다.
         - 인증된 SMTP - POP 및 IMAP 클라이언트에서 이메일 메시지를 보낼 때 사용합니다.
         - 자동 검색 - Outlook 및 EAS 클라이언트에서 Exchange Online의 사서함을 찾아 연결할 때 사용합니다.
         - Exchange Online PowerShell - 원격 PowerShell을 사용하여 Exchange Online에 연결하는 데 사용됩니다. Exchange Online PowerShell에 대한 기본 인증을 차단하는 경우 Exchange Online PowerShell 모듈을 사용하여 연결해야 합니다. 자세한 내용은 [다단계 인증을 사용하여 Exchange Online PowerShell에 연결](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)을 참조하세요.
         - EWS(Exchange 웹 서비스) - Outlook, Outlook for Mac 및 타사 앱에서 사용하는 프로그래밍 인터페이스입니다.
         - IMAP4 - IMAP 이메일 클라이언트에서 사용합니다.
         - MAPI over HTTP(MAPI/HTTP) - Outlook 2010 이상에서 사용합니다.
         - OAB(오프라인 주소록) - Outlook에서 다운로드하여 사용하는 주소 목록 컬렉션의 복사본입니다.
         - 외부에서 Outlook 사용(RPC over HTTP) - Outlook 2016 이하 버전에서 사용합니다.
         - Outlook 서비스 - Windows 10용 메일 및 일정 앱에서 사용합니다.
         - POP3 - POP 이메일 클라이언트에서 사용합니다.
         - 보고 웹 서비스 - Exchange Online에서 보고서 데이터를 검색할 때 사용합니다.

이러한 조건은 관리 되는 장치를 요구 하 고, 레거시 인증을 차단 하 고, 웹 응용 프로그램을 차단 하지만 모바일 또는 데스크톱 앱을 허용할 때 주로 사용 됩니다.

### <a name="supported-browsers"></a>지원되는 브라우저

이 설정은 모든 브라우저에서 작동합니다. 하지만 호환 디바이스 요구 사항과 같은 디바이스 정책을 충족하기 위해 다음 운영 체제와 브라우저가 지원됩니다.

| OS | 브라우저 |
| :-- | :-- |
| 윈도우 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, 크롬 |
| Windows 7 | Internet Explorer, 크롬 |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

> [!NOTE]
> Edge 85 이상에서는 장치 id를 올바르게 전달 하기 위해 사용자에 게 브라우저에 로그인 해야 합니다. 그렇지 않으면 계정 확장이 없는 Chrome 처럼 동작 합니다. 이 로그인은 하이브리드 Azure AD 조인 시나리오에서 자동으로 발생 하지 않을 수 있습니다. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>브라우저에 인증서 프롬프트가 표시 되는 이유는 무엇 인가요?

Windows 7, iOS, Android 및 macOS Azure AD는 장치가 Azure AD에 등록 될 때 프로 비전 되는 클라이언트 인증서를 사용 하 여 장치를 식별 합니다.  사용자가 브라우저를 통해 처음으로 로그인 하면 사용자에 게 인증서를 선택 하 라는 메시지가 표시 됩니다. 사용자는 브라우저를 사용 하기 전에이 인증서를 선택 해야 합니다.

#### <a name="chrome-support"></a>Chrome 지원

**Windows 10 크리에이터 업데이트 (버전 1703)** 이상에서 Chrome을 지원 하려면 [windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치 합니다. 이 확장은 조건부 액세스 정책에서 장치 관련 세부 정보를 요구 하는 경우에 필요 합니다.

이 확장을 Chrome 브라우저에 자동으로 배포하려면 다음 레지스트리 키를 만듭니다.

- 경로 HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- 이름 1
- 형식 REG_SZ (문자열)
- 데이터 ppnbnpeolgkicgegkbkbjmhlideopiji; https \: //clients2.google.com/service/update2/crx

**Windows 8.1 및 7**에서 Chrome을 지원하려면 다음 레지스트리 키를 만듭니다.

- 경로 HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- 이름 1
- 형식 REG_SZ (문자열)
- Data {"pattern": " https://device.login.microsoftonline.com ", "filter": {"ISSUER": {"CN": "MS-조직 액세스"}}}

이러한 브라우저는 디바이스 인증을 지원하므로 정책에 대해 디바이스 유효성을 검사하고 식별하는 것이 가능합니다. 브라우저가 개인 모드로 실행 중이면 디바이스 검사가 실패합니다.

### <a name="supported-mobile-applications-and-desktop-clients"></a>지원되는 모바일 애플리케이션 및 데스크톱 클라이언트

조직에서는 **모바일 앱 및 데스크톱 클라이언트** 를 클라이언트 앱으로 선택할 수 있습니다.

이 설정은 다음 모바일 앱 및 데스크톱 클라이언트에서 수행하는 액세스 시도에 영향을 미칩니다.

| 클라이언트 앱 | 대상 서비스 | 플랫폼 |
| --- | --- | --- |
| Dynamics CRM 앱 | Dynamics CRM | Windows 10, Windows 8.1, iOS 및 Android |
| 메일/달력/사람 앱, Outlook 2016, Outlook 2013(최신 인증 포함)| Exchange Online | 윈도우 10 |
| 앱에 대한 MFA 및 위치 정책입니다. 장치 기반 정책은 지원 되지 않습니다.| 모든 My Apps 앱 서비스 | Android 및 iOS |
| Microsoft Teams Services - Microsoft Teams 및 모든 클라이언트 앱(Windows 데스크톱, iOS, Android, WP 및 웹 클라이언트)을 지원하는 서비스를 모두 제어합니다. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android 및 macOS |
| Office 2016 앱, Office 2013 (최신 인증 사용), [OneDrive 동기화 클라이언트](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1, Windows 7 |
| Office 2016 앱, Universal Office 앱, Office 2013 (최신 인증 사용), [OneDrive 동기화 클라이언트](/onedrive/enable-conditional-access) | SharePoint Online | 윈도우 10 |
| Office 2016(Word, Excel, PowerPoint, OneNote만 해당) | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10, macOS |
| Office 모바일 앱 | SharePoint | Android, iOS |
| Office Yammer 앱 | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016(macOS용 Office) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용) | Exchange Online | Windows 8.1, Windows 7 |
| Outlook 모바일 앱 | Exchange Online | Android, iOS |
| Power BI 앱 | Power BI 서비스 | Windows 10, Windows 8.1, Windows 7, Android 및 iOS |
| 비즈니스용 Skype | Exchange Online| Android, iOS |
| Visual Studio Team Services 앱 | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS 및 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트

- 조직에서는 사용자 또는 그룹에 정책을 할당 하는 경우에만 Exchange ActiveSync 클라이언트를 선택할 수 있습니다. 모든 **사용자**, 모든 **게스트 및 외부 사용자**또는 **디렉터리 역할** 을 선택 하면 모든 사용자가 차단 됩니다.
- Exchange ActiveSync 클라이언트에 할당 된 정책을 만들 때 **Exchange Online** 이 정책에 할당 된 유일한 클라우드 응용 프로그램 이어야 합니다. 
- 조직에서는 **장치 플랫폼** 상태를 사용 하 여 특정 플랫폼에 대해이 정책의 범위를 좁힐 수 있습니다.

정책에 할당 된 액세스 제어에서 승인 된 **클라이언트 앱 필요**를 사용 하는 경우 사용자는 Outlook 모바일 클라이언트를 설치 하 고 사용 하도록 지시 됩니다. **Multi-factor authentication** 이 필요한 경우 기본 인증에서 multi-factor authentication을 지원 하지 않기 때문에 영향을 받는 사용자는 차단 됩니다.

자세한 내용은 다음 아티클을 참조하세요.

- [조건부 액세스를 사용 하 여 레거시 인증 차단](block-legacy-authentication.md)
- [조건부 액세스를 사용 하 여 승인 된 클라이언트 앱 요구](app-based-conditional-access.md)

### <a name="other-clients"></a>기타 클라이언트

**다른 클라이언트**를 선택하여 IMAP, MAPI, POP, SMTP 및 최신 인증을 사용하지 않는 이전 Office 앱과 같은 메일 프로토콜과 함께 기본 인증을 사용하는 앱에 영향을 미치는 조건을 지정할 수 있습니다.

## <a name="device-state-preview"></a>디바이스 상태(미리 보기)

장치 상태 조건은 하이브리드 Azure AD에 가입 된 장치와 조직의 조건부 액세스 정책에서 Microsoft Intune 준수 정책을 준수 하는 것으로 표시 된 장치를 제외 하는 데 사용할 수 있습니다.

예를 들어 모든 장치 상태를 포함 하는 *Microsoft Azure 관리* 클라우드 앱에 액세스 하는 *모든 사용자* 는 장치 **하이브리드 Azure AD 조인** 된 장치를 제외한 **모든 장치 상태** 와 **규격으로 표시 된 장치** 및 *액세스 제어*를 **차단**합니다. 
   - 이 예제에서는 하이브리드 Azure AD에 가입 된 장치 에서만 Microsoft Azure 관리에 대 한 액세스를 허용 하는 정책 및/또는 호환 되는 것으로 표시 된 장치를 만듭니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: Grant](concept-conditional-access-grant.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
