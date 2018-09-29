---
title: Microsoft Authenticator 앱 시작 - Azure Active Directory | Microsoft Docs
description: 최신 버전의 Microsoft Authenticator로 업그레이드하는 방법을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 590f9e2a9cf531a1124f9cb132791f2956227d9c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046582"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 시작

Microsoft Authenticator 앱은 계정에 대한 권한이 없는 액세스를 방지하고 사기성이 있는 트랜잭션을 중지하는 데 도움이 되어 회사 또는 학교 계정(예: alain@contoso.com) 또는 개인적인 Microsoft 계정(예: alain@outlook.com)에 대한 추가적인 보안 수준을 제공합니다.

2단계 인증에 앱을 사용할 때 다음 두 가지 방법 중 하나로 작동할 수 있습니다.

- **알림.** 앱에서 사용자의 장치에 알림을 보냅니다. 알림이 올바른지 확인한 다음, **확인**을 선택합니다. 모르는 알림이라면 **거부**를 선택합니다.

- **확인 코드.** 사용자 이름 및 암호를 입력한 후 앱을 열고 **계정** 화면에 제공한 확인 코드를 로그인 화면에 복사합니다. 확인 코드는 인증의 두 번째 형식으로 작동합니다.

## <a name="opt-in-for-two-step-verification"></a>2단계 인증에 옵트인

조직은 회사 또는 학교 계정에 2단계 인증을 사용하는지 여부를 결정합니다. 관리자가 어떤 인증 방법을 설정하고 사용해야 하는지를 사용자에게 알려줍니다. 자세한 내용은 [Azure Multi-Factor Authentication은 무엇을 의미하나요?](multi-factor-authentication-end-user.md)를 참조하세요.

개인 Microsoft 계정의 경우 2단계 인증을 설정할 수 있습니다. 자세한 내용 및 지침은 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)를 참조하세요.

또한 Microsoft가 아닌 계정에 Microsoft Authenticator 앱을 사용할 수 있습니다. 그러한 계정은 2단계 인증 이외의 기능을 호출할 수도 있지만, 보안 또는 로그인 설정 내에서 찾을 수 있어야 합니다. 이러한 Microsoft 이외의 계정을 설정하는 방법에 대한 자세한 내용은 [Microsoft 고객 지원 비디오](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX)를 참조하세요.

## <a name="install-the-app"></a>앱 설치

Microsoft Authenticator 앱은 [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) 및 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)에서 사용할 수 있습니다. 최상의 경험을 얻으려면 메시지가 표시될 때 앱이 알림을 받도록 해야 합니다. 

## <a name="add-accounts-to-the-app"></a>앱에 계정 추가

Microsoft Authenticator 앱에 회사 또는 학교 계정이나 개인 계정을 추가할 수 있습니다. 

### <a name="add-a-personal-microsoft-account"></a>개인 Microsoft 계정 추가

개인 Microsoft 계정(Outlook.com, Xbox, Skype 등에 로그인하는 데 사용하는 계정)의 경우, Microsoft Authenticator 앱의 사용자 계정에 로그인하기만 하면 됩니다.

### <a name="add-a-work-or-school-account"></a>회사 또는 학교 계정 추가

1. 가능한 경우 다른 PC 또는 장치에서 [추가 보안 인증](http://aka.ms/mfasetup) 화면으로 이동합니다. 이 화면으로 이동하는 방법에 대한 정보는 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)을 참조하거나 관리자에게 문의하세요.

    >[!Note]
    >관리자가 보안 정보 미리 보기 환경을 켠 경우 [인증자 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md) 섹션의 지침을 따를 수 있습니다.

2. **인증자 앱** 옆에 있는 확인란을 선택한 다음, **구성**을 선택합니다.

    ![보안 확인 설정 화면의 구성 단추](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    **모바일 앱 구성** 화면이 인증자 앱을 사용하여 스캔하도록 QR 코드와 함께 나타납니다.

    ![QR 코드를 제공하는 화면](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Microsoft Authenticator 앱을 엽니다. **계정** 화면에서 **계정 추가**를 선택한 다음, **회사 또는 학교 계정**을 선택합니다.

4. 장치의 카메라를 사용하여 QR 코드를 스캔한 다음, **완료**를 선택하여 QR 코드 화면을 닫습니다.

    >[!Note]
    >카메라가 올바르게 작동하지 않으면 [QR 코드 및 URL을 수동으로 입력](#add-an-account-to-the-app-manually)할 수 있습니다.

    앱의 **계정** 화면에 사용자 계정 이름 및 6자리 확인 코드가 표시됩니다. 추가 보안을 위해 확인 코드는 동일한 코드를 두 번 사용하지 않도록 30초마다 변경됩니다.  

    ![계정 화면](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>앱에 수동으로 계정 추가

1. **추가 보안 인증** 화면으로 이동합니다. 이 화면으로 이동하는 방법은 [보안 설정 변경](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)을 참조하세요.

2. **인증자 앱** 옆에 있는 확인란을 선택한 다음, **구성**을 선택합니다.

    **모바일 앱 구성** 화면이 나타납니다.

3. QR 스캐너에 수동으로 입력할 수 있도록 **모바일 앱 구성** 화면에서 코드 및 URL 정보를 복사합니다.

4. Microsoft Authenticator 앱을 엽니다. **계정** 화면에서 **계정 추가**를 선택한 다음, **회사 또는 학교 계정**을 선택합니다.

5. QR 스캐너 화면에서 **수동으로 코드 입력**을 선택합니다.

    ![QR 코드를 스캔하는 화면](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. QR 코드를 사용하여 화면에서 코드와 URL을 **계정 추가** 화면에 입력한 다음, **마침**을 선택합니다.

    ![코드와 URL을 입력하기 위한 화면](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    앱의 **계정** 화면에 사용자 계정 이름 및 6자리 확인 코드가 표시됩니다. 추가 보안을 위해 확인 코드는 동일한 코드를 두 번 사용하지 않도록 30초마다 변경됩니다.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>장치의 지문 또는 안면 인식 기능 사용

조직에서는 본인 확인을 완료하기 위해 PIN을 요구할 수 있습니다. PIN 대신 장치의 지문이나 안면 인식 기능을 사용하도록 Microsoft Authenticator 앱을 설정할 수 있습니다. 처음으로 인증자 앱을 사용하여 계정을 확인하는 경우 PIN 대신 사용자 장치 생체 인식 기능을 신원 확인으로 사용하는 옵션을 선택하여 이를 설정할 수 있습니다.

## <a name="use-the-app-when-you-sign-in"></a>로그인할 때 앱 사용

앱에 계정을 추가한 후 앱을 사용하여 계정에 로그인할 수 있습니다.

앱에서 확인 코드를 사용하도록 선택한 경우 **계정** 페이지에 표시되기 시작합니다. 필요한 경우 항상 새 코드를 갖도록 코드가 30초마다 변경됩니다. 하지만 로그인하고 확인 코드를 입력하라는 메시지가 나타날 때까지 아무것도 할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- 앱에 대한 일반적인 질문이 더 있는 경우 [Microsoft Authenticator FAQ](microsoft-authenticator-app-faq.md)를 참조하세요.

- 2단계 인증에 대해 더 자세히 알아보려면 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)을 참조하세요.

- 보안 정보에 대해 더 자세히 알아보려면 [보안 정보 관리](security-info-manage-settings.md)를 참조하세요.
