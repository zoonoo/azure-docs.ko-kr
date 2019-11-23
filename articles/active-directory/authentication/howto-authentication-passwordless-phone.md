---
title: Passwordless sign-in with the Microsoft Authenticator app - Azure Active Directory
description: Enable passwordless sign-in to Azure AD using the Microsoft Authenticator app (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a76aa0ca7cbda3f2db564c220ba12fec60f60509
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381872"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Enable passwordless sign-in with the Microsoft Authenticator app (preview)

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 기술과 마찬가지로 Microsoft Authenticator는 키 기반 인증을 사용하여 디바이스에 연결되고 생체 인식 또는 PIN을 사용하는 사용자 자격 증명을 사용하도록 설정합니다. This authentication method can be used on any device platform, including mobile, and with any app or website that integrates with Microsoft authentication libraries. 

![Example of a browser sign-in asking for user to approve the sign-in](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Instead of seeing a prompt for a password after entering a username, a person who has enabled phone sign-in from the Microsoft Authenticator app will see a message telling them to tap a number in their app. 앱에서 사용자는 번호가 일치해야 하고 [승인]을 선택한 다음, PIN 또는 생체 인식을 제공해야 인증이 완료됩니다.

> [!NOTE]
> This capability has been in the Microsoft Authenticator app since March of 2017, so there is a possibility that when the policy is enabled for a directory, users may encounter this flow immediately, and see an error message if they have not been enabled by policy. 이러한 변화를 인지하고 사용자가 대비하도록 하세요.

## <a name="prerequisites"></a>전제 조건

- Azure Multi-Factor Authentication, with push notifications allowed as a verification method 
- iOS 8.0 이상 또는 Android 6.0 이상을 실행하는 디바이스에 설치된 최신 버전의 Microsoft Authenticator

> [!NOTE]
> If you enabled the previous Microsoft Authenticator app passwordless sign-in preview using Azure AD PowerShell, it was enabled for your entire directory. If you enable using this new method, it will supercede the PowerShell policy. We recommend enabling for all users in your tenant via the new Authentication Methods, otherwise users not in the new policy will no longer be able to log in passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Enable passwordless authentication methods

### <a name="enable-the-combined-registration-experience"></a>Enable the combined registration experience

Registration features for passwordless authentication methods rely on the combined registration preview. Follow the steps in the article [Enable combined security information registration (preview)](howto-registration-mfa-sspr-combined.md), to enable the combined registration preview.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Enable passwordless phone sign-in authentication methods

1. [Azure 포털](https://portal.azure.com)
1. *Azure Active Directory*를 검색하고 선택합니다. Select **Security** > **Authentication methods** > **Authentication method policy (Preview)**
1. Under **Passwordless phone sign-in**, choose the following options
   1. **Enable** - Yes or No
   1. **Target** - All users or Select users
1. **Save** to set the new policy

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>User registration and management of Microsoft Authenticator app

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)으로 이동합니다.
1. Sign in if not already
1. Add an authenticator app by clicking **Add method**, choosing **Authenticator app**, and clicking **Add**
1. Follow the instructions to install and configure the Microsoft Authenticator app on your device
1. Click **Done** to complete Authenticator MFA app setup flow. 
1. In **Microsoft Authenticator**, choose **Enable phone sign-in** from the account drop-down menu
1. Follow the instructions in the app to finish registering for passwordless phone sign-in. 

Organizations can point their users to the article [Sign in with your phone, not your password](../user-help/microsoft-authenticator-app-phone-signin-faq.md) for further assistance setting up in the Microsoft Authenticator app and enabling phone sign-in.

## <a name="sign-in-with-passwordless-credential"></a>Sign in with passwordless credential

공개 미리 보기의 경우 사용자가 이 새 자격 증명을 만들거나 사용할 수 있도록 하는 방법은 없습니다. A user will only encounter passwordless sign-in once an admin has enabled their tenant **and** the user has updated their Microsoft Authenticator app to enable phone sign-in.

After typing your username on the web and selecting **Next**, users are presented with a number and are prompted in their Microsoft Authenticator app to select the appropriate number to authenticate instead of using their password. 

![Example of a browser sign-in using the Microsoft Authenticator app](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>알려진 문제

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>User is not enabled by policy but still has passwordless phone sign-in method in Microsoft Authenticator

It is possible that a user has at some point created a passwordless phone sign-in credential in their current Microsoft Authenticator app, or on an earlier device. Once an admin enables the authentication method policy for passwordless phone sign-in, any user with a credential registered, will start to experience the new sign-in prompt, regardless of whether they have been enabled to use the policy or not. If the user has not been allowed to use the credential by policy, they will see an error after completing the authentication flow. 

The admin can choose to enable the user to use passwordless phone sign-in, or the user must remove the method. If the user no longer has the registered device, they can go to [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) and remove it. If they are still using the Authenticator for MFA, they can choose **Disable phone sign-in** from within the Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>AD FS integration

사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용하도록 설정하면 해당 사용자의 인증은 항상 승인을 위해 알림을 보내는 것으로 기본 설정됩니다. 이 논리는 하이브리드 테넌트의 사용자가 [암호를 대신 사용]을 클릭하는 추가 단계를 수행하지 않고 로그인 확인을 위해 ADFS로 이동하는 것을 방지합니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 무시합니다. 

If a user has an unanswered passwordless phone sign-in verification pending and attempts to sign in again, the user may be taken to ADFS to enter a password instead.  

### <a name="azure-mfa-server"></a>Azure MFA 서버

End users who are enabled for MFA through an organization’s on-premises Azure MFA server can still create and use a single passwordless phone sign in credential. 사용자가 자격 증명으로 Microsoft Authenticator의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.  

### <a name="device-registration"></a>디바이스 등록

One of the prerequisites to create this new strong credential, is that the device, where the Microsoft Authenticator app is installed, must also be registered within the Azure AD tenant to an individual user. Due to current device registration restrictions, a device can only be registered in a single tenant. 이 제한은 Microsoft Authenticator 앱에서 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 설정할 수 있음을 의미합니다.

> [!NOTE]
> Device registration is not the same as device management or "MDM." It only associates a device ID and a user ID together in the Azure AD directory.  

## <a name="next-steps"></a>다음 단계

[What is passwordless?](concept-authentication-passwordless.md)

[디바이스 등록에 대한 자세한 정보](../devices/overview.md#getting-devices-in-azure-ad)

[Azure Multi-Factor Authentication에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)
