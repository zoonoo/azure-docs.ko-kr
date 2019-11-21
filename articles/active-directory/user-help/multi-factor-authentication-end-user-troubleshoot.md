---
title: Common problems & solutions with account authentication - Azure AD
description: Learn about potential problems and solutions for some of the more common two-factor verification problems and your work or school account.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d998fafbf6b3ba0547991de6c3a8e71b66d91f7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231887"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>Common problems and solutions with two-factor verification and your work or school account

Your organization has turned on two-factor verification, meaning that your work or school account sign-in now requires a combination of your user name, your password, and a mobile device or phone. 암호 자체보다 더 안전하므로 조직에서 이 추가 확인을 설정했습니다. 이에 따라 두 가지 인증 형식, 즉 사용자가 알고 있는 인증 형식과 사용자 전용의 인증 형식을 사용합니다. 2단계 인증은 악의적인 해커가 사용자를 가장하지 못하게 차단하는 효과가 있습니다. 해커가 사용자의 암호를 알아내더라도 사용자의 디바이스까지 손에 넣을 가능성은 별로 높지 않기 때문입니다.

There are some common two-factor verification problems that seem to happen more frequently than any of us would like. We've put together this article hoping to address the most common problems and some possible fixes.

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자의 경우 [Azure Active Directory 문서](https://docs.microsoft.com/azure/active-directory)에서 Azure AD(Azure Active Directory) 환경을 설정하고 관리하는 방법에 대한 자세한 정보를 찾을 수 있습니다.
>
>이 콘텐츠는 회사 또는 학교 계정, 조직에서 귀하에게 제공한 계정(예: alain@contoso.com)에만 사용할 수 있습니다. 2단계 인증과 개인 Microsoft 계정, 직접 설정한 계정(예: danielle@outlook.com)에 문제가 있으면 [Microsoft 계정에 대한 2단계 인증 설정 또는 해제](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)를 참조하세요.

## <a name="i-forgot-my-mobile-device-at-home"></a>I forgot my mobile device at home

It happens. You left your mobile device at home and now you can't use your phone to verify you are who you say you are. If you previously added another method to sign in to your account, such as your office phone, you should be able to use that method now. If you never added an additional verification method, you'll have to contact your Help desk and have them help you get back into your account.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>To sign in to your work or school account using another verification method

1. Sign in to your account normally and choose the **Sign in another way** link on the **Two-factor verification** page.

    ![로그인 인증 방법 변경](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**다른 방법으로 로그인** 링크가 표시되지 않으면 다른 인증 방법을 설정하지 않은 것입니다. 계정에 로그인하는 데 도움이 필요하면 관리자에게 문의해야 합니다.

2. Choose your alternative verification method, and continue with the two-factor verification process.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>I lost my mobile device or it was stolen

If you've lost or had your mobile device stolen, you can either sign in using a different method or you can ask your Help desk to clear your settings. We strongly recommend letting your Help desk know if your phone was lost or stolen, so the appropriate updates can be made to your account. After your settings are cleared, you'll be prompted to [register for two-factor verification](multi-factor-authentication-end-user-first-time.md) the next time you sign in.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>I'm not getting the verification code sent to my mobile device

Not getting your verification code is a common problem and it's typically related to your mobile device and its settings. Some possible things to try:

- **Restart your mobile device.** Sometimes your device just needs a refresh. Restarting your device ends any background processes or services that are currently running and could cause problems, along with refreshing your device's core components, restarting them in case they crashed at some point.

- **Verify your security information is correct.** Make sure your security verification method information is accurate, especially your phone numbers. If you put in the wrong phone number, all of your alerts will go to that incorrect number. Fortunately, that user won't be able to do anything with the alerts, but it also won't help you sign in to your account. To make sure your information is correct, see the instructions in the [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md) article.

- **Verify your notifications are turned on.** Make sure your mobile device has notifications turned on and that you've selected a notification method that allows phone calls, your authentication app, and your messaging app (for text messages) to send visible alert notifications to your mobile device.

- **Make sure you have a device signal and Internet connection.** Make sure your phone calls and text messages are getting through to your mobile device. Have a friend call you and send you a text message to make sure you receive both. If you don't, first check to make sure your mobile device is turned on. If your device is turned on, but you're still not getting the call or text, it's most likely a problem with your network and you'll need to talk to your provider. If you often have signal-related problems, we recommend you install and use the [Microsoft Authenticator app](user-help-auth-app-download-install.md) on your mobile device. The authenticator app can generate random security codes for sign-in, without requiring any cell signal or Internet connection.

- **Turn off Do not disturb.** Make sure you haven't turned on the **Do not disturb** feature for your mobile device. When this feature is turned on, notifications aren't allowed to alert you on your mobile device. Refer to your mobile device's manual for instructions about how to turn off this feature.

- **Unblock phone numbers** In the United States, voice calls from Microsoft come from the following numbers: +1 (866) 539 4191, +1 (855) 330 8653, and +1 (877) 668 6536.

- **Check your battery-related settings.** This one seems a bit odd on the surface, but if you've set up your battery optimization to stop lesser-used apps from remaining active in the background, your notification system has most-likely been affected. To try to fix this problem, turn off battery optimization for your authentication app and your messaging app, and then try signing in to your account again.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>I'm not getting prompted for my second verification information

If you've signed in to your work or school account using your user name and password, but haven't been prompted about your additional security verification information, it might be that you haven't set up your device yet. Your mobile device must specifically be set up to work with your additional security verification method. To make sure you've turned on your mobile device and that it's available to use with your verification method, see the [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md) article. If you know you haven't set up your device or your account, you can do it now by following the steps in the [Set up my account for two-step verification](multi-factor-authentication-end-user-first-time.md) article.

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>I got a new phone number, how do I change it for two-factor verification?

If you've gotten a new phone number, you'll need to update your security verification method details so your verification prompts go to the right location. To do update your verification method, follow the steps in the **Add or change your phone number** section of the [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) article.

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>I got a new mobile device, how do I add it?

If you've gotten a new mobile device, you'll need to set it up to work with two-factor verification. This is a multi-step solution:

1. Set up your device to work with your work or school account by following the steps in the [Set up my account for two-step verification](multi-factor-authentication-end-user-first-time.md) article.

2. Update your account and device information in the **Additional security verification** page, deleting your old device and adding your new one. For more information, see the [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md) article.

3. 선택 사항입니다. Download, install, and set up the Microsoft Authenticator app on your mobile device by following the steps in the [Download and install the Microsoft Authenticator app](user-help-auth-app-download-install.md) article.

4. 선택 사항입니다. Turn on two-factor verification for your trusted devices by following the steps in the **Turn on two-factor verification prompts on a trusted device** section of the [Manage your two-factor verification method settings](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) article.

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>I'm having problems signing in on my mobile device while traveling

You might find it more difficult to use a mobile device-related verification method, like a text messaging, while you're in an international location. It's also possible that your mobile device can cause you to incur roaming charges. For this situation, we recommend you use the Microsoft Authenticator app, with the option to connect to a Wi-Fi hotspot. For more information about how to download, install, and set up the Microsoft Authenticator app on your mobile device, see the [Download and install the Microsoft Authenticator app](user-help-auth-app-download-install.md) article.

## <a name="i-cant-get-my-app-passwords-to-work"></a>I can't get my app passwords to work

App passwords replace your normal password for older desktop applications that don't support two-factor verification. 먼저 암호를 올바르게 입력했는지 확인합니다. If that doesn't fix it, try creating a new app password for the app by following the steps in the **Create and delete app passwords using the My Apps portal** section of the [Manage app passwords for two-step verification](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) article.

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Why can't I turn two-factor verification off?

If you're using two-factor verification with your work or school account (for example, alain@contoso.com), it most likely means that your organization has decided you must use this added security feature. 조직에서 이 기능을 사용해야 한다고 결정했으므로 개별적으로 해제할 수 있는 방법이 없습니다. If, however, you're using two-factor verification with a personal account, like alain@outlook.com, you have the ability to turn the feature on and off. For instructions about how to control two-factor verification for your personal Microsoft accounts, see [Turning two-factor verification on or off for your Microsoft account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>문제에 대한 답변을 찾을 수 없는 경우

If you've tried these steps but are still running into problems, contact your Help desk for assistance.

## <a name="related-articles"></a>관련 문서

- [2단계 확인 방법 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [Set up my account for two-step verification](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md)
