---
title: Windows 용 셀프 서비스 암호 재설정-Azure Active Directory
description: Windows 로그인 화면에서 암호 찾기를 사용 하 여 셀프 서비스 암호 재설정을 사용 하도록 설정 하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: be1c0e93a51064870635d4f06bd5b365bbfe517a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847289"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>방법: Windows 로그인 화면에서 암호 재설정 사용

Windows 7, 8, 8.1 및 10을 실행 하는 컴퓨터의 경우 사용자가 Windows 로그인 화면에서 암호를 재설정할 수 있도록 설정할 수 있습니다. 사용자는 더 이상 [SSPR 포털](https://aka.ms/sspr)에 액세스할 수 있는 웹 브라우저가 포함 된 장치를 찾을 필요가 없습니다.

![SSPR 링크가 표시 된 Windows 7 및 10 로그인 화면 예제](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>일반적인 제한 사항

- 현재 원격 데스크톱 또는 Hyper-v 고급 세션에서 암호 재설정이 지원 되지 않습니다.
- 이 기능은 802.1x 네트워크 인증이 배포된 네트워크에 및 “사용자가 로그온하기 직전에 수행” 옵션에는 작동하지 않습니다. 802.1x 네트워크 인증이 배포된 네트워크의 경우 머신 인증을 사용하여 이 기능을 사용하는 것이 좋습니다.
- 새 암호를 사용 하 고 캐시 된 자격 증명을 업데이트 하려면 하이브리드 Azure AD 조인 컴퓨터에 도메인 컨트롤러에 대 한 네트워크 연결 회선이 있어야 합니다.
- 이미지를 사용 하는 경우 sysprep를 실행 하기 전에 CopyProfile 단계를 수행 하기 전에 기본 제공 관리자에 대 한 웹 캐시가 지워지지 않았는지 확인 합니다. 이 단계에 대 한 자세한 내용은 [사용자 지정 기본 사용자 프로필을 사용할 때 지원 문서 성능 저하](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)에서 찾을 수 있습니다.
- 다음 설정은 Windows 10 장치에서 암호를 사용 하 고 다시 설정 하는 기능을 방해 하는 것으로 알려져 있습니다.
    - Windows 10 1809 이전 버전에서 정책이 Ctrl+Alt+Del을 요구하는 경우 **암호 재설정**이 작동하지 않습니다.
    - 잠금 화면 알림이 꺼진 경우 **암호 재설정**이 작동하지 않습니다.
    - HideFastUserSwitching이 사용하도록 설정 또는 1로 설정됨
    - DontDisplayLastUserName이 사용하도록 설정 또는 1로 설정됨
    - NoLockScreen이 사용하도록 설정 또는 1로 설정됨
    - EnableLostMode가 디바이스에서 설정됨
    - Explorer.exe는 사용자 지정 셸로 바뀜
- 다음 세 가지 설정을 조합 하면이 기능이 작동 하지 않을 수 있습니다.
    - 대화형 로그온: CTRL + ALT + DEL = Disabled가 필요 하지 않습니다.
    - DisableLockScreenAppNotifications = 1 또는 사용
    - IsContentDeliveryPolicyEnforced = 1 또는 True

## <a name="windows-10-password-reset"></a>Windows 10 암호 재설정

### <a name="windows-10-prerequisites"></a>Windows 10 필수 조건

- 관리자는 Azure Portal에서 Azure AD 셀프 서비스 암호 재설정을 사용 하도록 설정 해야 합니다.
- **사용자는이 기능을 사용 하기 전에 SSPR에 등록 해야 합니다.**
- 네트워크 프록시 요구 사항
   - Windows 10 디바이스 
       - `passwordreset.microsoftonline.com` 및 `ajax.aspnetcdn.com`에 대 한 포트 443
       - Windows 10 장치는 컴퓨터 수준 프록시 구성만 지원 합니다.
- Windows 10, 버전 4 월 2018 업데이트 (v1803) 이상을 실행 하 고 장치는 다음 중 하나 여야 합니다.
    - Azure AD 가입
    - 하이브리드 Azure AD 가입

### <a name="enable-for-windows-10-using-intune"></a>Intune을 사용 하 여 Windows 10 사용

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
1. 이 정책은 특정 사용자, 장치 또는 그룹에 할당할 수 있습니다. 자세한 내용은 [Microsoft Intune에서 사용자 및 장치 프로필 할당](https://docs.microsoft.com/intune/device-profile-assign)문서에서 찾을 수 있습니다.

### <a name="enable-for-windows-10-using-the-registry"></a>레지스트리를 사용 하 여 Windows 10 사용

1. 관리 자격 증명을 사용하여 Windows PC에 로그인
1. 관리자 권한으로 **regedit** 실행
1. 다음 레지스트리 키를 설정
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 암호 재설정 문제 해결

Azure AD 감사 로그에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

사용자가 Windows 10 장치의 로그인 화면에서 암호를 재설정 하면 `defaultuser1` 라는 낮은 권한 임시 계정이 만들어집니다. 이 계정은 암호 재설정 프로세스를 안전하게 유지하는 데 사용됩니다. 계정 자체는 무작위로 생성된 암호를 가지고 있으며, 디바이스 로그인 시 표시되지 않으며, 사용자가 암호를 설정하면 자동으로 제거됩니다. `defaultuser` 프로필이 여러 개 있을 수 있지만 안전 하 게 무시할 수 있습니다.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 및 8.1 필수 구성 요소

- 관리자는 Azure Portal에서 Azure AD 셀프 서비스 암호 재설정을 사용 하도록 설정 해야 합니다.
- **사용자는이 기능을 사용 하기 전에 SSPR에 등록 해야 합니다.**
- 네트워크 프록시 요구 사항
   - Windows 7, 8 및 8.1 장치
       - `passwordreset.microsoftonline.com` 포트 443
- 패치가 적용된 Windows 7 또는 Windows 8.1 운영 체제
- [TLS(전송 계층 보안) 레지스트리 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)에 있는 지침을 사용하여 TLS 1.2 사용하도록 설정
- 컴퓨터에 두 개 이상의 타사 자격 증명 공급자를 사용 하도록 설정 하면 사용자는 로그인 화면에서 둘 이상의 사용자 프로필을 볼 수 있습니다.

> [!WARNING]
> 자동 negotiate로 설정 된 것이 아니라 TLS 1.2을 사용 하도록 설정 해야 합니다.

### <a name="install"></a>Install

1. 사용할 Windows 버전에 대해 적절한 설치 관리자를 다운로드합니다.
   - [https://aka.ms/sspraddin](https://aka.ms/sspraddin)의 Microsoft 다운로드 센터에서 소프트웨어를 지원합니다.
1. 설치하고 설치 관리자를 실행할 머신에 로그인합니다.
1. 설치 후에 다시 부팅하는 것이 좋습니다.
1. 다시 부팅 한 후 로그인 화면에서 사용자를 선택 하 고 "암호 잊음?"를 클릭 합니다. 암호 재설정 워크플로를 시작하려면
1. 암호를 재설정하는 화면 단계를 따라 워크플로를 완료합니다.

![예제 Windows 7 "암호 찾기" 클릭 SSPR flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>자동 설치

- 자동 설치의 경우 "msiexec /i SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.
- 자동 제거의 경우 "msiexec /x SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정 문제 해결

머신 및 Azure AD 모두에 이벤트가 기록됩니다. Azure AD 이벤트에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

추가 로깅이 필요한 경우 머신의 레지스트리 키를 변경하여 자세한 정보 표시 로깅을 사용할 수 있습니다. 문제 해결에 대해서만 자세한 정보 표시 로깅을 사용합니다.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- 자세한 정보 로깅을 사용 하도록 설정 하려면 `REG_DWORD: “EnableLogging”`을 만들고 1로 설정 합니다.
- 자세한 정보 로깅을 사용 하지 않도록 설정 하려면 `REG_DWORD: “EnableLogging”`를 0으로 변경 합니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

Windows 장치에 대 한 암호 재설정을 구성 했으므로 사용자에 대 한 변경 사항은 무엇 인가요? 로그인 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요?

![SSPR 링크가 표시 된 Windows 7 및 10 로그인 화면 예제](./media/howto-sspr-windows/windows-reset-password.png)

사용자가 로그인 하려고 하면 로그인 화면에서 셀프 서비스 암호 재설정 환경을 여는 **암호 다시 설정** 또는 **암호 찾기** 링크가 표시 됩니다. 사용자는 이 기능을 사용하면 다른 디바이스를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용하는 방법에 대한 지침은 [회사 또는 학교 암호 재설정](../user-help/active-directory-passwords-update-your-own-password.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[허용할 인증 방법 계획](concept-authentication-methods.md)

[Windows 10 구성](https://docs.microsoft.com/windows/configuration/)
