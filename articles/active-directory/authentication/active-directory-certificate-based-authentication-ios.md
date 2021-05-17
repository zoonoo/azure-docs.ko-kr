---
title: iOS에서 인증서 기반 인증 - Azure Active Directory
description: iOS 디바이스에서 솔루션의 Azure Active Directory에 대한 인증서 기반 인증을 구성하는 데 지원되는 시나리오 및 요구 사항에 대한 자세한 정보
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861326"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS에서 Azure Active Directory 인증서 기반 인증

보안을 향상시키기 위해 iOS 디바이스는 다음 애플리케이션 또는 서비스에 연결할 때 CBA(인증서 기반 인증)를 사용하여 디바이스의 클라이언트 인증서를 사용하는 Azure AD(Azure Active Directory)에 인증할 수 있습니다.

* Microsoft Outlook 및 Microsoft Word와 같은 Office 모바일 애플리케이션
* EAS(Exchange ActiveSync) 클라이언트

인증서를 사용하면 모바일 디바이스의 특정 메일 및 Microsoft Office 애플리케이션에 사용자 이름 및 암호 조합을 입력해야 합니다.

이 문서에서는 iOS 디바이스에서 CBA를 구성하기 위한 요구 사항 및 지원되는 시나리오에 대해 자세히 설명합니다. iOS용 CBA는 Azure 공용 클라우드, Microsoft Government Cloud, Microsoft Cloud Germany 및 Microsoft Azure 중국 21Vianet에서 사용할 수 있습니다.

## <a name="microsoft-mobile-applications-support"></a>Microsoft 모바일 애플리케이션 지원

| 앱 | 지원 |
| --- | --- |
| Azure Information Protection 앱 |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Intune 회사 포털 |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Microsoft Teams |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Office(모바일) |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| OneNote |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| OneDrive |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Outlook |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Power BI |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| 비즈니스용 Skype |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Word / Excel / PowerPoint |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |
| Yammer |![이 애플리케이션에 대한 지원을 나타내는 확인 표시][1] |

## <a name="requirements"></a>요구 사항

iOS에서 CBA를 사용하기 위해서는 다음 요구 사항 및 고려 사항이 적용됩니다.

* 디바이스 OS 버전은 iOS 9 이상이어야 합니다.
* Microsoft Authenticator는 iOS의 Office 애플리케이션에 필요합니다.
* ADFS 서버의 인증 URL을 포함하는 macOS 키 집합에 ID 기본 설정을 만들어야 합니다. 자세한 내용은 [Mac에서 키 집합 액세스에 ID 기본 설정 만들기](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)를 참조하세요.

다음 ADFS(Active Directory Federation Services) 요구 사항 및 고려 사항이 적용됩니다.

* 인증서 인증을 위해 ADFS 서버를 사용하도록 설정하고 페더레이션 인증을 사용해야 합니다.
* 인증서에는 EKU(확장 된 키 사용)를 사용해야 하며 *주체 대체 이름(NT 주체 이름)* 에 사용자의 UPN이 포함되어야 합니다.

## <a name="configure-adfs"></a>ADFS 구성

Azure AD에서 클라이언트 인증서를 해지하려면 ADFS 토큰에 다음 클레임이 있어야 합니다. Azure AD는 이러한 클레임이 ADFS 토큰(또는 다른 SAML 토큰)에서 사용 가능한 경우 새로 고침 토큰에 이러한 클레임을 추가합니다. 새로 고침 토큰의 유효성을 검사해야 하는 경우 이 정보가 해지를 확인하는 데 사용됩니다.

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -클라이언트 인증서의 일련 번호 추가
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` - 클라이언트 인증서 발급자에 대한 문자열 추가

조직의 ADFS 오류 페이지를 다음 정보로 업데이트하는 것이 가장 좋습니다.

* iOS에서 Microsoft Authenticator를 설치하기 위한 요구 사항
* 사용자 인증서를 얻는 방법에 대한 지침

자세한 내용은 [AD FS 로그인 페이지 사용자 지정](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))을 참조하세요.

## <a name="use-modern-authentication-with-office-apps"></a>Office 앱에서 최신 인증 사용

일부 Office 앱(최신 인증 사용)은 요청 시 Azure AD에 `prompt=login`을 보냅니다. 기본적으로 Azure AD는 ADFS에 대한 요청의 `prompt=login`을 `wauth=usernamepassworduri`(U/P 인증을 수행하도록 ADFS에 요청) 및 `wfresh=0`(SSO 상태를 무시하고 새로운 인증을 수행하도록 ADFS에 요청)으로 변환합니다. 이러한 앱에 인증서 기반 인증을 사용하려면 기본 Azure AD 동작을 수정합니다.

기본 동작을 업데이트하려면 페더레이션된 도메인 설정의 '*PromptLoginBehavior*'을 *사용 안 함* 으로 설정합니다. 다음 예제와 같이 [get-msoldomainfederationsettings](/powershell/module/msonline/set-msoldomainfederationsettings) cmdlet을 사용하여 이 작업을 수행할 수 있습니다.

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트에 대한 지원

iOS 9 이상에서, 네이티브 iOS 메일 클라이언트가 지원됩니다. 이 기능이 다른 모든 Exchange ActiveSync 애플리케이션에 대해 지원되는지 확인하려면 애플리케이션 개발자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

사용자 환경에서 인증서 기반 인증을 구성하려면 지침의 [인증서 기반 인증 시작](active-directory-certificate-based-authentication-get-started.md)을 참조하세요.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
