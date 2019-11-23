---
title: Self-service password reset for Windows - Azure Active Directory
description: How to enable self-service password reset using forgot password at the Windows login screen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e25efcb068fe51f05dbbde50e8a96da492a735
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381242"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>How to: Enable password reset from the Windows login screen

For machines running Windows 7, 8, 8.1, and 10 you can enable users to reset their password at the Windows login screen. Users no longer have to find a device with a web browser to access the [SSPR portal](https://aka.ms/sspr).

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>일반적인 제한 사항

- Password reset is not currently supported from a Remote Desktop or from Hyper-V enhanced sessions.
- 이 기능은 802.1x 네트워크 인증이 배포된 네트워크에 및 “사용자가 로그온하기 직전에 수행” 옵션에는 작동하지 않습니다. 802.1x 네트워크 인증이 배포된 네트워크의 경우 머신 인증을 사용하여 이 기능을 사용하는 것이 좋습니다.
- Hybrid Azure AD joined machines must have network connectivity line of sight to a domain controller to use the new password and update cached credentials.
- If using an image, prior to running sysprep ensure that the web cache is cleared for the built-in Administrator prior to performing the CopyProfile step. More information about this step can be found in the support article [Performance poor when using custom default user profile](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- The following settings are known to interfere with the ability to use and reset passwords on Windows 10 devices
    - Windows 10 1809 이전 버전에서 정책이 Ctrl+Alt+Del을 요구하는 경우 **암호 재설정**이 작동하지 않습니다.
    - 잠금 화면 알림이 꺼진 경우 **암호 재설정**이 작동하지 않습니다.
    - HideFastUserSwitching이 사용하도록 설정 또는 1로 설정됨
    - DontDisplayLastUserName이 사용하도록 설정 또는 1로 설정됨
    - NoLockScreen이 사용하도록 설정 또는 1로 설정됨
    - EnableLostMode가 디바이스에서 설정됨
    - Explorer.exe는 사용자 지정 셸로 바뀜
- The combination of the following specific three settings can cause this feature to not work.
    - Interactive logon: Do not require CTRL+ALT+DEL = Disabled
    - DisableLockScreenAppNotifications = 1 or Enabled
    - IsContentDeliveryPolicyEnforced = 1 or True

## <a name="windows-10-password-reset"></a>Windows 10 password reset

### <a name="windows-10-prerequisites"></a>Windows 10 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 10 devices 
       - Port 443 to `passwordreset.microsoftonline.com` and `ajax.aspnetcdn.com`
       - Windows 10 devices only support machine-level proxy configuration
- Run at least Windows 10, version April 2018 Update (v1803), and the devices must be either:
    - Azure AD 가입
    - 하이브리드 Azure AD 가입

### <a name="enable-for-windows-10-using-intune"></a>Enable for Windows 10 using Intune

Intune을 사용하여 로그인 화면에서 암호 재설정을 사용하도록 설정하는 가장 유연한 방법은 구성 변경을 배포하는 것입니다. Intune을 사용하면 여러분이 정의하는 특정 머신 그룹에 구성 변경 내용을 배포할 수 있습니다. 이 메서드는 디바이스의 Intune 등록이 필요합니다.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune에서 디바이스 구성 정책 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 클릭
1. **디바이스 구성** > **프로필** > **프로필 만들기**로 이동하여 새 디바이스 구성 프로필 만들기
   - 프로필의 의미 있는 이름 입력
   - 필요에 따라 프로필에 대한 의미 있는 설명 입력
   - 플랫폼 **Windows 10 이상**
   - 프로필 형식 **사용자 지정**
1. **설정** 구성
   - 다음 OMA URI 설정을 **추가**하여 암호 재설정 링크를 사용하도록 설정
      - 설정이 하는 일을 설명하는 의미 있는 이름 입력
      - 필요에 따라 설정에 대한 의미 있는 설명 입력
      - **OMA URI**을 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`으로 설정
      - **데이터 형식**을 **정수**로 설정
      - **값**을 **1**로 설정
      - **확인**
   - **확인**
1. **만들기**
1. This policy can be assigned to specific users, devices, or groups. More information can be found in the article [Assign user and device profiles in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Enable for Windows 10 using the Registry

1. 관리 자격 증명을 사용하여 Windows PC에 로그인
1. 관리자 권한으로 **regedit** 실행
1. 다음 레지스트리 키를 설정
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Troubleshooting Windows 10 password reset

Azure AD 감사 로그에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

When users reset their password from the login screen of a Windows 10 device, a low-privilege temporary account called `defaultuser1` is created. 이 계정은 암호 재설정 프로세스를 안전하게 유지하는 데 사용됩니다. 계정 자체는 무작위로 생성된 암호를 가지고 있으며, 디바이스 로그인 시 표시되지 않으며, 사용자가 암호를 설정하면 자동으로 제거됩니다. Multiple `defaultuser` profiles may exist but can be safely ignored.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8, and 8.1 password reset

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8, and 8.1 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 7, 8, and 8.1 devices
       - Port 443 to `passwordreset.microsoftonline.com`
- 패치가 적용된 Windows 7 또는 Windows 8.1 운영 체제
- [TLS(전송 계층 보안) 레지스트리 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)에 있는 지침을 사용하여 TLS 1.2 사용하도록 설정
- If more than one 3rd party credential provider is enabled on your machine, users will see more than one user profile on the login screen.

> [!WARNING]
> TLS 1.2 must be enabled, not just set to auto negotiate

### <a name="install"></a>Install

1. 사용할 Windows 버전에 대해 적절한 설치 관리자를 다운로드합니다.
   - [https://aka.ms/sspraddin](https://aka.ms/sspraddin)의 Microsoft 다운로드 센터에서 소프트웨어를 지원합니다.
1. 설치하고 설치 관리자를 실행할 머신에 로그인합니다.
1. 설치 후에 다시 부팅하는 것이 좋습니다.
1. After the reboot, at the login screen choose a user and click "Forgot password?" 암호 재설정 워크플로를 시작하려면
1. 암호를 재설정하는 화면 단계를 따라 워크플로를 완료합니다.

![예제 Windows 7 "암호 찾기" 클릭 SSPR flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>자동 설치

- 자동 설치의 경우 "msiexec /i SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.
- 자동 제거의 경우 "msiexec /x SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Troubleshooting Windows 7, 8, and 8.1 password reset

머신 및 Azure AD 모두에 이벤트가 기록됩니다. Azure AD 이벤트에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

추가 로깅이 필요한 경우 머신의 레지스트리 키를 변경하여 자세한 정보 표시 로깅을 사용할 수 있습니다. 문제 해결에 대해서만 자세한 정보 표시 로깅을 사용합니다.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- To enable verbose logging, create a `REG_DWORD: “EnableLogging”`, and set it to 1.
- To disable verbose logging, change the `REG_DWORD: “EnableLogging”` to 0.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

Now that you have configured password reset for your Windows devices, what changes for the user? 로그인 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요?

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

When users attempt to sign in, they now see a **Reset password** or **Forgot password** link that opens the self-service password reset experience at the login screen. 사용자는 이 기능을 사용하면 다른 디바이스를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용하는 방법에 대한 지침은 [회사 또는 학교 암호 재설정](../user-help/active-directory-passwords-update-your-own-password.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Plan authentication methods to allow](concept-authentication-methods.md)

[Configure Windows 10](https://docs.microsoft.com/windows/configuration/)
