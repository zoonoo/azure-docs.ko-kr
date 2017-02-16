---
title: "Microsoft Authenticator 앱 도움말 및 지원 | Microsoft Docs"
description: "Microsoft Authentication 앱 및 Azure Multi-Factor Authentication과 관련된 질문과 대답 목록을 제공합니다."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 45358e89561ef2265ca5d8ae522b823c4629b3fc
ms.openlocfilehash: f869e456ef40d04f7dd085d4ce7c3d654cdf2091


---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator 앱 FAQ
Microsoft Authenticator 앱은 Azure Authenticator 앱을 대신하며, Azure Multi-factor Authentication을 사용하는 경우 권장됩니다. 이 앱은 Windows Phone, Android 및 iOS에 사용할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="what-happened-to-the-azure-authenticator-multi-factor-auth-and-microsoft-account-apps"></a>Azure Authenticator, Multi-Factor Auth 및 Microsoft 계정 앱에 변경된 내용
Microsoft Authenticator 앱은 서로를 대체합니다. Azure Authenticator는 Microsoft Authenticator로 업그레이드되었습니다. Multi-Factor Auth 또는 Microsoft 계정 앱을 사용하는 경우 Microsoft Authenticator를 설치하고 다시 사용자 계정을 추가합니다. 이전 버전을 삭제하기 전에 새 앱에 계정 추가를 완료해야 합니다.

[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>인증 코드에 Microsoft Authenticator 응용 프로그램을 이미 사용하고 있습니다. 원 클릭 푸시 알림으로 전환하려면 어떻게 해야 하나요?
푸시 알림을 통한 로그인 승인은 Google 또는 Facebook 같은 타사 계정이 아닌 개인 Microsoft 계정이나 회사 및 학교 Microsoft 계정에만 사용할 수 있습니다. 회사 또는 학교 Microsoft 계정을 갖고 있는 경우 조직에서 이 옵션을 사용하지 않도록 설정할 수 있습니다.

개인 계정으로 Microsoft 계정을 사용하고 있으며 푸시 알림으로 전환하려는 경우 계정을 다시 추가해야 합니다. 장치를 계정에 다시 등록하고 푸시 알림을 설정합니다.  

조직에서는 회사 또는 학교 계정에 Microsoft Authenticator를 사용하는 경우 원 클릭 알림 허용 여부를 결정합니다.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Microsoft 이외의 계정에서도 원 클릭 푸시 알림이 작동하나요?
아니요. 푸시 알림은 Microsoft 계정 및 Azure Active Directory 계정에만 작동합니다. 회사 또는 학교에서 Azure AD 계정을 사용하는 경우 이 기능을 사용하지 않도록 설정할 수 있습니다.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>백업에서 장치를 복원했으며 내 계정 코드가 누락되었거나 작동하지 않습니다. 어떻게 된 건가요?
보안을 위해 앱 백업에서 계정을 복원하지 않습니다. 백업에서 iOS 앱을 복원하는 경우 계정이 계속 표시되지만 로그인 확인을 수신하기 위해 작동되거나 보안 코드를 생성하지 않습니다. 앱을 복원한 후에 계정을 삭제한 후 다시 추가합니다.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>새 장치가 있습니다. 이전 장치에서 Microsoft Authenticator를 제거하고 새 장치로 이동시키려면 어떻게 해야 하나요?
새 장치에 Microsoft Authenticator 앱을 추가해도 다른 장치에서 자동으로 제거하지 않습니다. 계정에 대해 구성된 장치를 관리하도록&2;단계 인증을 관리하는 데 사용한 동일한 웹 사이트를 방문하여 이전 앱을 제거합니다.

개인 Microsoft 계정의 경우 이 웹 사이트는 [계정 보안](https://account.microsoft.com/security) 페이지입니다. 회사 또는 학교 Microsoft 계정의 경우 이 웹 사이트는 [MyApps](https://myapps.microsoft.com) 또는 조직에서 설정한 사용자 지정 포털 중 하나일 수 있습니다.

### <a name="how-do-i-remove-an-account-from-the-app"></a>앱에서 계정을 제거하려면 어떻게 해야 하나요?
* iOS: 메인 화면에서 계정 타일을 지웁니다. **삭제**를 선택합니다.
* Windows Phone: 메인 화면에서 메뉴 단추를 선택한 다음 **계정을 편집**합니다. 계정 이름 옆에 있는 **X**를 누릅니다.
* Android: 메인 화면에서 메뉴 단추를 선택한 다음 **계정을 편집**합니다. 계정 이름 옆에 있는 **X**를 누릅니다.

조직에 등록된 Android 장치가 있는 경우 계정을 제거하도록 추가 단계를 완료해야 합니다. 이러한 장치에서 Microsoft Authenticator 앱은 장치 관리자는 자동으로 등록됩니다. 앱을 완전히 제거하려면 먼저 앱 설정에서 앱 등록을 취소해야 합니다.

### <a name="why-does-the-app-request-so-many-permissions"></a>앱이 너무 많은 권한을 요청하는 이유는 무엇인가요?
요청한 사용 권한의 전체 목록은 다음과 같으며 앱에서 사용하는 방법은 다음과 같습니다.

* **카메라**: 카메라를 사용하여 회사, 학교 또는 Microsoft가 아닌 계정을 추가할 때 QR 코드를 검색합니다.
* **연락처 및 휴대폰**: 개인 Microsoft 계정으로 로그인하는 경우 휴대폰에서 사용하는 기존 계정을 검색하여 프로세스를 단순화하려고 합니다.
* **SMS**: 개인 Microsoft 계정으로 처음 로그인하는 경우 기록된 전화 번호와 일치하는지 확인해야 합니다. 앱을 다운로드한 휴대폰으로 메시지를 보냅니다. 메시지에는 6~8자리 인증 코드가 포함되어 있습니다. 이 코드를 찾아 앱에 입력을 요청하는 대신 문자 메시지에서 찾을 수 있습니다.
* **다른 앱을 통해 가져오기**: ID를 인증하는 알림을 수신할 때 해당 알림을 실행 중일 수 있는 다른 앱을 통해 표시합니다.
* **인터넷의 데이터 수신**: 알림을 보내기 위해 이 사용 권한이 필요합니다.
* **휴대폰 절전 모드 전환 방지**: 조직에서 장치를 등록하는 경우 휴대폰에서 이 정책을 변경할 수 있습니다.
* **진동 제어**: ID를 인증하는 알림을 수신할 때마다 진동할지 여부를 선택하는 옵션이 있습니다.
* **지문 하드웨어 사용**: 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 프로세스를 보다 쉽게 수행하려면 PIN을 입력하는 대신 지문을 사용할 수 있습니다.
* **네트워크 연결 보기**: Microsoft 계정을 추가할 때 앱에는 네트워크/인터넷 연결이 필요합니다.
* **저장소의 내용 읽기**: 앱 설정을 통해 기술 문제를 보고하는 경우에만 이 사용 권한을 사용하게 됩니다. 저장소의 일부 정보를 수집하여 문제를 진단합니다.
* **전체 네트워크 액세스**: ID를 인증하는 알림을 보내는 데 이 사용 권한이 필요합니다.
* **시작 시 실행**: 전화를 다시 시작하는 경우 이 사용 권한을 사용하면 ID를 인증하는 알림을 계속 수신할 수 있습니다.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Microsoft Authenticator 앱에서 사용자가 장치를 잠금 해제하지 않아도 요청을 승인할 수 있도록 허용하는 이유는 무엇인가요?

의도적인 작동입니다. 2단계 인증은 두 가지를 요구합니다. 하나는 사용자가 알고 있는 것이고, 다른 하나는 사용자가 갖고 있는 것입니다. 사용자가 알고 있는 것은 암호입니다. 사용자가 갖고 있는 것은 휴대폰(Microsoft Authenticator 앱을 통해 설정하고 MFA 증명으로 등록된)입니다.  따라서 휴대폰을 갖고 있고 요청을 승인하면 인증의 두 번째 요소에 대한 조건을 충족합니다. 

## <a name="next-steps"></a>다음 단계

### <a name="contact-us"></a>문의처
궁금한 내용에 대한 답변이 여기에 없으면 직접 질문을 남겨주세요. [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 질문을 게시하고, 커뮤니티의 도움을 받고, 이 페이지에 의견을 남기거나 [지원에 문의](https://support.microsoft.com/contactus)하세요. 최대한 신속하게 답변을 드리겠습니다.


### <a name="related-topics"></a>관련된 항목
* Microsoft 계정에 대한 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* 회사 또는 학교 계정에 [2단계 인증 문제](multi-factor-authentication-end-user-troubleshoot.md)가 있나요?




<!--HONumber=Jan17_HO3-->


