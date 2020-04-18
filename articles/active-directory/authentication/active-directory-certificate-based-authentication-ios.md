---
title: iOS에서 인증서 기반 인증 - Azure Active Directory
description: iOS 장치를 사용하여 솔루션에서 Azure Active Directory에 대한 인증서 기반 인증을 구성하기 위한 지원되는 시나리오 및 요구 사항에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639622"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS에서 Azure Active Directory 인증서 기반 인증

보안을 향상시키기 위해 iOS 장치는 인증서 기반 인증(CBA)을 사용하여 다음 응용 프로그램 이나 서비스에 연결할 때 장치에서 클라이언트 인증서를 사용하여 Azure Active Directory(Azure AD)를 인증할 수 있습니다.

* Microsoft Outlook 및 Microsoft Word와 같은 Office 모바일 애플리케이션
* EAS(Exchange ActiveSync) 클라이언트

인증서를 사용하면 모바일 장치의 특정 메일 및 Microsoft Office 응용 프로그램에 사용자 이름과 암호 조합을 입력할 필요가 없습니다.

이 문서에서는 iOS 장치에서 CBA를 구성하기 위한 요구 사항 및 지원되는 시나리오에 대해 자세히 설명합니다. iOS용 CBA는 Azure 퍼블릭 클라우드, 마이크로소프트 정부 클라우드, 마이크로소프트 클라우드 독일, 마이크로소프트 저퍼 차이나 21Vianet에서 사용할 수 있습니다.

## <a name="microsoft-mobile-applications-support"></a>Microsoft 모바일 애플리케이션 지원

| 앱 | 지원 |
| --- | --- |
| Azure Information Protection 앱 |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| Intune 회사 포털 |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| Microsoft 팀 |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| OneNote |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| OneDrive |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| Outlook |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| Power BI |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| 비즈니스용 Skype |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| Word / Excel / PowerPoint |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |
| Yammer |![이 응용 프로그램에 대한 지원을 나타내는 표시를 확인하십시오.][1] |

## <a name="requirements"></a>요구 사항

iOS에서 CBA를 사용하려면 다음 요구 사항 및 고려 사항이 적용됩니다.

* 장치 OS 버전은 iOS 9 이상이어야 합니다.
* Microsoft Authenticator는 iOS의 Office 애플리케이션에 필요합니다.
* ADFS 서버의 인증 URL이 포함된 macOS 키체인에 ID 기본 설정을 만들어야 합니다. 자세한 내용은 [Mac의 키체인 액세스에서 ID 기본 설정 만들기를](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)참조하십시오.

다음 ADFS(활성 디렉터리 페더레이션 서비스) 요구 사항 및 고려 사항이 적용됩니다.

* ADFS 서버는 인증서 인증및 페더레이션 인증을 사용하도록 설정해야 합니다.
* 인증서는 EKU(고급 키 사용)를 사용하고 *주체 대체 이름(NT 주체 이름)에*사용자의 UPN을 포함해야 합니다.

## <a name="configure-adfs"></a>ADFS 구성

Azure AD가 클라이언트 인증서를 해지하려면 ADFS 토큰에 다음 클레임이 있어야 합니다. Azure AD는 ADFS 토큰(또는 다른 SAML 토큰)에서 사용할 수 있는 경우 이러한 클레임을 새로 고침 토큰에 추가합니다. 새로 고침 토큰의 유효성을 검사해야 하는 경우 이 정보는 해지를 확인하는 데 사용됩니다.

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- 클라이언트 인증서의 일련 번호를 추가
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- 클라이언트 인증서 발급자의 문자열을 추가합니다.

가장 좋은 방법은 조직의 ADFS 오류 페이지를 다음 정보로 업데이트해야 합니다.

* iOS에 Microsoft 인증기 설치를 위한 요구 사항입니다.
* 사용자 인증서를 얻는 방법에 대한 지침

자세한 내용은 [AD FS 로그인 사용자 지정 페이지를](https://technet.microsoft.com/library/dn280950.aspx)참조하십시오.

## <a name="use-modern-authentication-with-office-apps"></a>Office 앱에서 최신 인증 사용

최신 인증을 사용하는 일부 `prompt=login` Office 앱은 요청시 Azure AD로 전송됩니다. 기본적으로 Azure AD는 `prompt=login` 요청에서 ADFS로 `wauth=usernamepassworduri` 변환하고(ADFS에 U/P 인증 `wfresh=0` 작업을 수행하도록 요청) 및 (ADFS에 SSO 상태를 무시하고 새 인증을 수행하도록 요청)으로 변환합니다. 이러한 앱에 대해 인증서 기반 인증을 사용하도록 설정하려면 기본 Azure AD 동작을 수정합니다.

기본 동작을 업데이트하려면 페더레이션 도메인 설정에서 *'PromptLoginBehavior'를* *사용 안 함으로*설정합니다. 다음 예제와 같이 [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet을 사용하여 이 작업을 수행할 수 있습니다.

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트 지원

iOS 9 이상에서, 네이티브 iOS 메일 클라이언트가 지원됩니다. 이 기능이 다른 모든 Exchange ActiveSync 응용 프로그램에 대해 지원되는지 확인하려면 응용 프로그램 개발자에게 문의하십시오.

## <a name="next-steps"></a>다음 단계

사용자 환경에서 인증서 기반 인증을 구성하려면 [지침에](active-directory-certificate-based-authentication-get-started.md) 대한 인증서 기반 인증 시작 을 참조하십시오.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
