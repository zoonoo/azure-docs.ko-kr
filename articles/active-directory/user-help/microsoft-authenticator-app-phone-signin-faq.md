---
title: Microsoft Authenticator 휴대폰 로그인 - Azure 및 Microsoft 계정 | Microsoft Docs
description: 암호를 입력하는 대신 휴대폰을 사용하여 Microsoft 계정에 로그인합니다. 이 문서에서는 이 기능에 대한 FAQ를 제공합니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: b2483d93fdc83970ca1568326f4bc728de16f77c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038870"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>암호가 아닌 휴대폰을 사용하여 로그인
Microsoft Authenticator 앱을 통해 암호를 입력한 후 2단계 인증을 수행하여 계정 보안을 유지할 수 있을 뿐 아니라 이제는 Microsoft 계정을 완전히 대신할 수 있습니다.

이 기능은 iOS 장치 및 Android 6.0 이상을 실행하는 Android 장치에서 사용할 수 있습니다.
 
## <a name="how-it-works"></a>작동 방법
사용자들은 대부분 Microsoft 계정에 로그인할 때 2단계 인증을 위해 Microsoft Authenticator 앱을 사용합니다. 암호를 입력한 후 앱으로 이동하여 알림을 승인하거나 확인 코드를 가져옵니다. 휴대폰 로그인을 사용하여 암호를 건너뛰고 휴대폰에서 ID 확인을 모두 수행할 수 있습니다. 휴대폰 로그인은 2단계 인증 유형이기 때문에 사용자의 신분 확인을 위해 사용자가 알고 있는 것과 보유하고 있는 것에 대한 정보를 제공해야 합니다. 휴대폰은 사용자가 보유하고 있는 것이지만, 휴대폰의 PIN 또는 생체 인식 키는 사용자가 알고 있는 것입니다.

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>회사 또는 학교 계정에 휴대폰 로그인 사용 
조직에서 이 새로운 환경을 설정한 경우 Microsoft Authenticator 앱에서 휴대폰 로그인을 설정하고 사용할 수 있습니다.

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>계정에 휴대폰 로그인을 사용하려면

- **휴대폰에 이 아이콘 ![설정을 보여 주는 아이콘](media/microsoft-authenticator-app-phone-signin-faq/icon.png)이 있는 경우**. 이미 휴대폰 로그인을 설정한 것입니다. 앱 외부에서 인증 요청에 대한 알림을 받을 수 있도록 푸시 알림을 추가하려면 계정을 업그레이드하라는 메시지가 표시될 수 있습니다. 

- **이미 2단계 인증에 Microsoft Authenticator 앱을 사용하는 경우.** 앱의 [계정] 화면으로 이동하여 회사 또는 학교 계정에 대한 드롭다운 화살표를 선택한 다음, **휴대폰 로그인 사용**을 선택합니다. 

- **개인 계정에는 Microsoft Authenticator 앱을 사용하지만 회사 또는 학교 계정에는 사용하지 않는 경우.** 조직의 2단계 인증 지침을 따르고, 휴대폰 로그인을 켭니다. 자세한 내용은 [Microsoft Authenticator 앱 시작](microsoft-authenticator-app-how-to.md)을 참조하세요.

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>휴대폰 로그인을 사용하여 회사 또는 학교 계정에 로그인하려면 

1. 평소와 같이 회사 또는 학교 계정에 로그인합니다. 

    사용자 이름을 입력하면 2자리 숫자가 보이는 페이지가 표시되고 Microsoft Authenticator 앱을 통해 로그인을 승인하라는 메시지가 표시됩니다. 이 로그인 방법을 사용하지 않으려면 **암호를 대신 사용**하도록 선택하고, 암호를 사용하여 로그인합니다.

    ![로그인 승인 상자](media\microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)

2. Microsoft 인증 앱에서 **로그인 승인**을 요청하는 알림을 받게 됩니다. 로그인 시도를 인식한 경우 **승인**을 선택합니다. 그렇지 않은 경우 **거부**를 선택합니다. 

3. **로그인 승인** 화면의 번호와 같은 번호를 탭합니다. 휴대폰의 PIN 또는 생체 인식 키를 사용하여 인증을 완료합니다.

    ![선택할 번호가 있는 로그인 승인 상자](media\microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>개인 계정에 휴대폰 로그인 사용 
Outlook.com, Xbox, Skype 등의 개인 Microsoft 계정에 휴대폰 로그인할 사용할 수 있습니다.

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>개인 계정에 휴대폰 로그인을 사용하려면

- **Microsoft Authenticator 앱을 사용하지 않는 경우.** Microsoft Authenticator 앱을 다운로드하여 설치하고, Microsoft Authenticator 앱의 [계정 추가] 화면에서 계정에 로그인하여 개인 Microsoft 계정을 추가합니다. 자세한 내용은 [Microsoft Authenticator 앱 시작](microsoft-authenticator-app-how-to.md)을 참조하세요.

    새 계정은 이 기능이 자동으로 설정되므로 즉시 휴대폰 로그인을 사용할 수 있습니다. 

- **이미 2단계 인증에 Microsoft Authenticator 앱을 사용하는 경우.** 앱의 **계정** 화면으로 이동하여 회사 또는 학교 계정에 대한 드롭다운 화살표를 선택한 다음, **휴대폰 로그인 사용**을 선택합니다.

    >[!NOTE]
    >계정을 보호하기 위해 앱에서 장치의 PIN 또는 생체 인식을 잠글 것을 요구합니다. 휴대폰을 잠금 해제 상태로 유지하면 휴대폰 로그인을 활성화하기 전에 보안 잠금을 설정하라고 앱에서 요구합니다.

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>휴대폰 로그인을 사용하여 개인 계정에 로그인하려면 

1. 개인 Microsoft 계정 로그인 페이지로 이동한 다음, 암호를 입력하는 대신 **Microsoft Authenticator 앱을 대신 사용** 링크를 선택합니다. 

    Microsoft는 사용자의 휴대폰에 알림을 보냅니다. 

2. 알림을 승인합니다. 

## <a name="phone-sign-in-faq"></a>휴대폰 로그인 FAQ
|**질문**|**해결 방법**|
|--------------|-------------|
|**휴대폰에 암호를 입력하는 것보다 안전하게 로그인할 수 있나요?**|오늘날 대부분의 사람들은 사용자 이름 및 암호를 사용하여 웹 사이트 또는 앱에 로그인합니다. 그러나 암호를 분실하거나, 도난당하거나, 해커가 추측할 가능성이 있습니다.<br><br>Microsoft Authenticator 앱을 설정하면 이 앱은 휴대폰의 PIN 또는 생체 인식 잠금으로 보호되는 계정을 잠금 해제하는 키를 휴대폰에 만듭니다. 이 키는 로그인하는 동안 신원을 증명하는 데 사용됩니다.<br><br>**중요**<br>데이터는 키를 로컬로 보호하는 용도로만 사용됩니다. 어떤 경우에도 전송되거나 클라우드에 저장되지 않습니다.|
|**이 기능이 2단계 인증을 대체하나요? 이 기능을 꺼야 하나요?**| 휴대폰 로그인의 범위를 확장하기 위해 최선을 다하고 있지만 지금은 Microsoft 에코시스템에서 아직 지원되지 않는 위치가 있습니다. 해당 위치에서 안전한 로그인을 위해 2단계 인증을 사용합니다. 이런 이유로 인해 아니요, 계정에 대해 2단계 인증을 해제해서는 안됩니다.|
|**내 계정에 대해 2단계 인증을 계속 설정하는 경우에 두 개의 알림을 승인해야 하나요?**|  아니요. 휴대폰을 사용하여 Microsoft 계정에 로그인해도 2단계 인증으로 집계되므로 두 번째 승인은 필요 없습니다.|
|**휴대폰을 분실하거나 휴대하지 않은 경우 계정에 액세스하려면 어떻게 해야 하나요?**| 언제든지 로그인 페이지에서 [암호를 대신 사용]을 선택하여 암호를 사용하도록 전환할 수 있습니다. 그러나 2단계 인증을 사용하더라도 여전히 두 번째 방법을 사용하여 신분을 증명해야 합니다.<br><br>**중요**<br>계정에 두 가지 이상의 최신 인증 방법을 연결하는 것이 좋습니다.<br><br>[보안 설정](https://account.live.com/proofs/manage) 페이지에서 개인 계정의 인증 방법을 관리할 수 있습니다. 회사 또는 학교 계정이면 관리자가 보안 정보를 설정한 경우 조직의 [추가 보안 인증](https://aka.ms/MFASetup) 페이지 또는 **계정 보안 유지** 페이지로 이동하면 됩니다. 보안 정보에 대한 자세한 내용은 [보안 정보 관리](security-info-manage-settings.md)를 참조하세요.<br><br>인증 방법을 관리할 수 없으면 관리자에게 문의해야 합니다.|
|**이 기능을 중지하고 다시 암호를 사용하려면 어떻게 해야 하나요?**|개인 계정의 경우 로그인하는 동안 **암호를 대신 사용** 링크를 선택합니다. 가장 최근에 사용한 방법이 저장되어 다음에 로그인할 때 기본값으로 제공됩니다. 다시 휴대폰 로그인을 사용하려면 로그인하는 동안 **앱을 대신 사용** 링크를 선택합니다.<br><br>회사 또는 학교 계정의 경우 Microsoft Authenticator 앱을 열고, **계정 편집** 메뉴를 선택하고, 계정을 삭제하는 방법으로 계정을 제거해야 합니다.|
|**휴대폰 로그인에 회사 또는 학교 계정을 둘 이상 사용할 수 없는 이유는 무엇인가요?**| 한 휴대폰은 하나의 회사 또는 학교 계정에 등록되어야 합니다. 다른 회사 또는 학교 계정에서 휴대폰 로그인을 사용하려면 먼저 **설정** 페이지를 통해 기존 디바이스의 등록을 취소해야 합니다.|
|**휴대폰을 사용하여 내 PC에 로그인할 수 있나요?**| PC의 경우 Windows 10에서 Windows Hello를 사용하여 로그인하는 것이 좋습니다. Windows Hello는 얼굴, 지문 또는 PIN을 사용하여 로그인할 수 있습니다.|
|**Windows Phone에서 휴대폰 로그인을 사용할 수 있나요?**|  아니요. 이 기능은 Windows Phone의 Microsoft Authenticator 앱에서 지원되지 않습니다.|

## <a name="next-steps"></a>다음 단계
- 앱에 대한 일반적인 질문이 더 있는 경우 [Microsoft Authenticator FAQ](microsoft-authenticator-app-faq.md)를 참조하세요.

- 2단계 인증에 대해 더 자세히 알아보려면 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)을 참조하세요.

- 보안 정보에 대해 더 자세히 알아보려면 [보안 정보 관리](security-info-manage-settings.md)를 참조하세요.
