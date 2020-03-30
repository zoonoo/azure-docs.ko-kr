---
title: 조건부 액세스 정책의 조건 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 조건은 무엇입니까?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295342"
---
# <a name="conditional-access-conditions"></a>조건부 액세스: 조건

조건부 액세스 정책 내에서 관리자는 위험, 장치 플랫폼 또는 위치와 같은 조건에서 신호를 사용하여 정책 결정을 개선할 수 있습니다. 

![조건부 액세스 정책 정의 및 조건 지정](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

여러 조건을 결합하여 세분화되고 특정 조건부 액세스 정책을 만들 수 있습니다.

예를 들어 중요한 응용 프로그램에 액세스할 때 관리자는 다단계 인증과 같은 다른 컨트롤 외에도 ID 보호 및 위치의 로그인 위험 정보를 액세스 결정에 반영할 수 있습니다.

## <a name="sign-in-risk"></a>로그인 위험

[ID 보호에](../identity-protection/overview-identity-protection.md)액세스할 수 있는 고객의 경우 로그인 위험을 조건부 액세스 정책의 일부로 평가할 수 있습니다. 로그인 위험은 지정된 인증 요청이 ID 소유자가 승인하지 않을 가능성을 나타냅니다. 로그인 위험에 대한 자세한 내용은 문서에서 찾을 수 [있습니다.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) [How To: Configure and enable risk policies](../identity-protection/howto-identity-protection-configure-risk-policies.md)

## <a name="device-platforms"></a>디바이스 플랫폼

장치 플랫폼은 장치에서 실행되는 운영 체제를 특징으로 합니다. Azure AD는 사용자 에이전트 문자열과 같은 장치에서 제공하는 정보를 사용하여 플랫폼을 식별합니다. 사용자 에이전트 문자열을 수정할 수 있기 때문에 이 정보는 확인되지 않습니다. 장치 플랫폼은 Microsoft Intune 장치 준수 정책과 함께 사용하거나 블록 문의 일부로 사용해야 합니다. 기본값은 모든 장치 플랫폼에 적용하는 것입니다.

Azure AD 조건부 액세스는 다음 장치 플랫폼을 지원합니다.

- Android
- iOS
- Windows Phone
- Windows
- macOS

**기타 클라이언트** 조건을 사용하여 레거시 인증을 차단하는 경우 장치 플랫폼 조건을 설정할 수도 있습니다.

## <a name="locations"></a>위치

위치를 조건으로 구성할 때 조직은 위치를 포함하거나 제외하도록 선택할 수 있습니다. 이러한 명명된 위치에는 공용 IPv4 네트워크 정보, 국가 또는 지역 또는 특정 국가 또는 지역에 매핑되지 않은 알 수 없는 지역이 포함될 수 있습니다. IP 범위만 신뢰할 수 있는 위치로 표시할 수 있습니다.

모든 **위치를**포함하는 경우 이 옵션에는 명명된 위치뿐만 아니라 인터넷의 모든 IP 주소가 포함됩니다. **위치를**선택할 때 관리자는 신뢰할 **수 있거나** 선택한 모든 **위치를**제외하도록 선택할 수 있습니다.

예를 들어 일부 조직에서는 사용자가 물리적 본사와 같은 신뢰할 수 있는 위치에서 네트워크에 연결되어 있을 때 다단계 인증을 요구하지 않도록 선택할 수 있습니다. 관리자는 모든 위치를 포함하지만 본사 네트워크에 대해 선택한 위치를 제외하는 정책을 만들 수 있습니다.

위치에 대한 자세한 내용은 문서에서 찾을 수 [있습니다.](location-condition.md)

## <a name="client-apps-preview"></a>클라이언트 앱(미리 보기)

조건부 액세스 정책은 기본적으로 최신 인증 프로토콜을 사용하는 브라우저 기반 응용 프로그램 및 응용 프로그램에 적용됩니다. 관리자는 이러한 응용 프로그램 외에도 Exchange ActiveSync 클라이언트 및 레거시 프로토콜을 사용하는 다른 클라이언트를 포함하도록 선택할 수 있습니다.

- 브라우저
   - 여기에는 SAML, WS-페더레이션, OpenID Connect 또는 OAuth 기밀 클라이언트로 등록된 서비스와 같은 프로토콜을 사용하는 웹 기반 응용 프로그램이 포함됩니다.
- 모바일 앱 및 데스크톱 클라이언트
   - 최신 인증 클라이언트
      - 이 옵션에는 Office 데스크톱 및 전화 응용 프로그램과 같은 응용 프로그램이 포함됩니다.
   - Exchange ActiveSync 클라이언트
      - 기본적으로 여기에는 EAS(Exchange ActiveSync) 프로토콜의 모든 사용이 포함됩니다. **지원되는 플랫폼에만 정책 적용을** 선택하면 iOS, Android 및 Windows와 같은 지원되는 플랫폼으로 제한됩니다.
      - 정책으로 Exchange ActiveSync 사용을 차단하면 영향을 받는 사용자는 단일 격리 전자 메일을 받게 됩니다. 이 이메일에는 차단된 이유에 대한 정보가 포함되어 있으며 가능한 경우 수정 지침이 포함되어 있습니다.
   - 기타 클라이언트
      - 이 옵션에는 최신 인증을 지원하지 않는 기본/레거시 인증 프로토콜을 사용하는 클라이언트가 포함됩니다.
         - 인증된 SMTP - POP 및 IMAP 클라이언트에서 전자 메일 메시지를 보내는 데 사용됩니다.
         - 자동 검색 - Outlook 및 EAS 클라이언트에서 Exchange Online에서 사서함을 찾고 연결하는 데 사용됩니다.
         - 교환 온라인 파워쉘 - 원격 PowerShell으로 교환 온라인에 연결하는 데 사용됩니다. Exchange 온라인 PowerShell에 대한 기본 인증을 차단하는 경우 Exchange 온라인 PowerShell 모듈을 사용하여 연결해야 합니다. 지침은 [다단계 인증을 사용하여 Exchange 온라인 PowerShell에 연결 을](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)참조하십시오.
         - 교환 웹 서비스 (EWS) - Outlook, Mac용 Outlook 및 타사 앱에서 사용하는 프로그래밍 인터페이스입니다.
         - IMAP4 - IMAP 이메일 클라이언트에서 사용됩니다.
         - MAPI HTTP (MAPI / HTTP) 이상 - Outlook 2010 이상에서 사용됩니다.
         - OAB(오프라인 주소록) - Outlook에서 다운로드하여 사용하는 주소 목록 모음의 복사본입니다.
         - Outlook 어디서나 (HTTP를 통해 RPC) - Outlook 2016 및 이전에서 사용됩니다.
         - Outlook 서비스 - Windows 10용 메일 및 캘린더 앱에서 사용됩니다.
         - POP3 - POP 이메일 클라이언트에서 사용됩니다.
         - 웹 서비스 보고 - Exchange Online에서 보고서 데이터를 검색하는 데 사용됩니다.

이러한 조건은 관리되는 장치를 요구하고, 레거시 인증을 차단하고, 웹 응용 프로그램을 차단하지만 모바일 또는 데스크톱 앱을 허용할 때 일반적으로 사용됩니다.

### <a name="supported-browsers"></a>지원되는 브라우저

이 설정은 모든 브라우저에서 작동합니다. 하지만 호환 디바이스 요구 사항과 같은 디바이스 정책을 충족하기 위해 다음 운영 체제와 브라우저가 지원됩니다.

| OS | 브라우저 |
| :-- | :-- |
| 윈도우 10 | 마이크로소프트 지, 인터넷 익스플로러, 크롬 |
| Windows 8 / 8.1 | Internet Explorer, 크롬 |
| Windows 7 | Internet Explorer, 크롬 |
| iOS | 마이크로소프트 지, 인튠 관리 브라우저, 사파리 |
| Android | 마이크로소프트 지, 부도 관리 브라우저, 크롬 |
| Windows Phone | 마이크로소프트 지, 인터넷 익스플로러 |
| Windows Server 2019 | 마이크로소프트 지, 인터넷 익스플로러, 크롬 |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>브라우저에 인증서 프롬프트가 표시되는 이유는 무엇입니까?

Windows 7에서 iOS, Android 및 macOS Azure AD는 장치가 Azure AD에 등록될 때 프로비전되는 클라이언트 인증서를 사용하여 장치를 식별합니다.  사용자가 브라우저를 통해 처음 에 서명하면 인증서를 선택하라는 메시지가 표시됩니다. 사용자는 브라우저를 사용하기 전에 이 인증서를 선택해야 합니다.

#### <a name="chrome-support"></a>Chrome 지원

윈도우에서 크롬 지원 **10 크리에이터 업데이트 (버전 1703)** 이상, 윈도우를 설치 [10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 이 확장은 조건부 액세스 정책에 장치별 세부 정보가 필요한 경우에 필요합니다.

이 확장을 Chrome 브라우저에 자동으로 배포하려면 다음 레지스트리 키를 만듭니다.

|    |    |
| --- | --- |
| 경로 | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 이름 | 1 |
| Type | REG_SZ (String) |
| 데이터 | ppnbnpelgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

**Windows 8.1 및 7**에서 Chrome을 지원하려면 다음 레지스트리 키를 만듭니다.

|    |    |
| --- | --- |
| 경로 | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 이름 | 1 |
| Type | REG_SZ (String) |
| 데이터 | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

이러한 브라우저는 디바이스 인증을 지원하므로 정책에 대해 디바이스 유효성을 검사하고 식별하는 것이 가능합니다. 브라우저가 개인 모드로 실행 중이면 디바이스 검사가 실패합니다.

### <a name="supported-mobile-applications-and-desktop-clients"></a>지원되는 모바일 애플리케이션 및 데스크톱 클라이언트

조직은 **모바일 앱 및 데스크톱 클라이언트를** 클라이언트 앱으로 선택할 수 있습니다.

이 설정은 다음 모바일 앱 및 데스크톱 클라이언트에서 수행하는 액세스 시도에 영향을 미칩니다.

| 클라이언트 앱 | 대상 서비스 | 플랫폼 |
| --- | --- | --- |
| Dynamics CRM 앱 | Dynamics CRM | Windows 10, Windows 8.1, iOS 및 Android |
| 메일/달력/사람 앱, Outlook 2016, Outlook 2013(최신 인증 포함)| Office 365 Exchange Online | 윈도우 10 |
| 앱에 대한 MFA 및 위치 정책입니다. 디바이스 기반 정책은 지원되지 않습니다.| 모든 My Apps 앱 서비스 | Android 및 iOS |
| Microsoft Teams Services - Microsoft Teams 및 모든 클라이언트 앱(Windows 데스크톱, iOS, Android, WP 및 웹 클라이언트)을 지원하는 서비스를 모두 제어합니다. | Microsoft 팀 | Windows 10, Windows 8.1, Windows 7, iOS, Android 및 macOS |
| 오피스 2016 애플 리 케이 션, 사무실 2013 (현대 인증), [OneDrive 동기화 클라이언트](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| 오피스 2016 애플 리 케이 션, 유니버설 오피스 애플 리 케이 션, 사무실 2013 (현대 인증), [OneDrive 동기화 클라이언트](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | 윈도우 10 |
| Office 2016(Word, Excel, PowerPoint, OneNote만 해당) | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office 모바일 앱 | Office 365 SharePoint Online | Android, iOS |
| Office Yammer 앱 | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016(macOS용 Office) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook 모바일 앱 | Office 365 Exchange Online | Android, iOS |
| Power BI 앱 | Power BI 서비스 | 윈도우 10, 윈도우 8.1, 윈도우 7, 안드로이드, 그리고 아이폰 OS |
| 비즈니스용 Skype | Office 365 Exchange Online| Android, iOS |
| Visual Studio Team Services 앱 | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS 및 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트

- 조직은 사용자 또는 그룹에 정책을 할당할 때만 Exchange ActiveSync 클라이언트를 선택할 수 있습니다. **모든 사용자,** **모든 게스트 및 외부 사용자**또는 **디렉터리 역할을** 선택하면 모든 사용자가 차단됩니다.
- Exchange ActiveSync 클라이언트에 할당된 정책을 만들 때 **Office 365 Exchange Online은** 정책에 할당된 유일한 클라우드 응용 프로그램이어야 합니다. 
- 조직은 **장치 플랫폼** 조건을 사용하여 이 정책의 범위를 특정 플랫폼으로 좁힐 수 있습니다.

정책에 할당된 액세스 제어가 **승인됨 클라이언트 앱 필요를**사용하는 경우 사용자는 Outlook 모바일 클라이언트를 설치하고 사용하도록 지시됩니다. **다단계 인증이** 필요한 경우 기본 인증은 다단계 인증을 지원하지 않으므로 영향을 받는 사용자가 차단됩니다.

자세한 내용은 다음 문서를 참조하세요.

- [조건부 액세스를 통해 레거시 인증 차단](block-legacy-authentication.md)
- [조건부 액세스권한이 있는 승인된 클라이언트 앱 필요](app-based-conditional-access.md)

### <a name="other-clients"></a>기타 클라이언트

**다른 클라이언트**를 선택하여 IMAP, MAPI, POP, SMTP 및 최신 인증을 사용하지 않는 이전 Office 앱과 같은 메일 프로토콜과 함께 기본 인증을 사용하는 앱에 영향을 미치는 조건을 지정할 수 있습니다.

## <a name="device-state-preview"></a>장치 상태(미리 보기)

장치 상태 조건은 조직의 조건부 액세스 정책에서 Microsoft Intune 준수 정책을 준수하는 것으로 표시된 하이브리드 Azure AD 조인 및/또는 장치인 장치를 제외하는 데 사용할 수 있습니다.

예를 들어 장치 **하이브리드 Azure AD를** 제외한 **모든 장치 상태를** 포함하여 Microsoft Azure *Management* 클라우드 앱에 액세스하는 *모든 사용자는* 조인 및 장치를 **준수로 표시하고** *액세스 컨트롤*, **차단**. 
   - 이 예제에서는 하이브리드 Azure AD 조인 및/또는 호환으로 표시된 장치에서만 Microsoft Azure Management에 대한 액세스를 허용하는 정책을 만듭니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 보조금](concept-conditional-access-grant.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)
