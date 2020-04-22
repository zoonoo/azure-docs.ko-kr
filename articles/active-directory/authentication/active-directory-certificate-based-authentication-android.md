---
title: 안드로이드 인증서 기반 인증 - Azure Active Directory
description: Android 디바이스에서 솔루션의 인증서 기반 인증을 구성하는 데 지원되는 시나리오 및 요구 사항에 대한 자세한 정보
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9760624afec111a271ae5aa0ebbe5533d6ba8d6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680202"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Android에서 Azure Active Directory 인증서 기반 인증

Android 디바이스는 연결할 때 자신의 디바이스에서 클라이언트 인증서를 사용하여 Azure Active Directory에서 인증하는 데 CBA(인증서 기반 인증)을 사용할 수 있습니다.

* Microsoft Outlook 및 Microsoft Word와 같은 Office 모바일 애플리케이션
* EAS(Exchange ActiveSync) 클라이언트

이 기능을 구성하면 모바일 디바이스의 특정 메일 및 Microsoft Office 애플리케이션에 사용자 이름 및 암호 조합을 입력해야 합니다.

이 항목에서는 Office 365 엔터프라이즈, 비즈니스, 교육, 미국 정부, 중국 및 독일 계획의 테넌타 사용자를 위해 Android 장치에서 CBA를 구성하기 위한 요구 사항 및 지원되는 시나리오를 제공합니다.

이 기능은 Office 365 미국 국방부 및 연방 정부 계획에서 미리 보기 상태로 제공됩니다.

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

### <a name="implementation-requirements"></a>구현 요구 사항

디바이스 OS 버전은 Android 5.0(Lollipop) 이상이어야 합니다.

페더레이션 서버가 구성되어야 합니다.

Azure Active Directory에서 클라이언트 인증서를 해지하려면 ADFS 토큰에 다음 클레임이 있어야 합니다.

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(클라이언트 인증서의 일련 번호)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(클라이언트 인증서의 발급자에 대한 문자열)

Azure Active Directory는 이러한 클레임이 ADFS 토큰(또는 다른 SAML 토큰)에서 사용 가능한 경우 새로 고침 토큰에 이러한 클레임을 추가합니다. 새로 고침 토큰의 유효성을 검사해야 하는 경우 이 정보가 해지를 확인하는 데 사용됩니다.

조직의 ADFS 오류 페이지를 다음 정보로 업데이트하는 것이 가장 좋습니다.

* Android에서 Microsoft Authenticator를 설치하기 위한 요구 사항
* 사용자 인증서를 얻는 방법에 대한 지침

자세한 내용은 [AD FS 로그인 페이지 사용자 지정](https://technet.microsoft.com/library/dn280950.aspx)을 참조하세요.

일부 Office 앱(최신 인증이 활성화된 경우)은 요청시 Azure AD에 *'프롬프트=로그인'을*보냅니다. 기본적으로 Azure AD는 ADFS에 대한 요청에서 *'프롬프트=로그인'을**'wauth=userpassworduri'(ADFS에*U/P Auth를 수행하도록 요청함) 및 *'wfresh=0'(ADFS에*SSO 상태를 무시하고 새 인증을 수행하도록 요청함)으로 변환합니다. 이러한 앱에 인증서 기반 인증을 사용하려면 기본 Azure AD 동작을 수정해야 합니다. 페더레이션도메인 설정에서 *'PromptLoginBehavior'를**'사용 안 함'으로*설정합니다.
다음과 같은 [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet을 사용하면 이 작업을 수행할 수 있습니다.

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync 클라이언트 지원

Android 5.0(Lollipop) 이상의 특정 Exchange ActiveSync 애플리케이션은 지원됩니다. 이메일 애플리케이션에서 이 기능을 지원하는지 확인하려면 애플리케이션 개발자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

사용자 환경에서 인증서 기반 인증을 구성하는 방법에 대한 자세한 내용은 [Android에서 인증서 기반 인증 시작](active-directory-certificate-based-authentication-get-started.md)을 참조하세요.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
