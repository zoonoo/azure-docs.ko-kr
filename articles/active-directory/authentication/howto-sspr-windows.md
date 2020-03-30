---
title: Windows용 셀프 서비스 암호 재설정 - Azure Active Directory
description: Windows 로그인 화면에서 잊어버린 암호를 사용하여 셀프 서비스 암호 재설정을 활성화하는 방법
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
ms.openlocfilehash: a1f0e5242d87bc68efd92a52619e8d48cff9ac87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370062"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>방법: Windows 로그인 화면에서 암호 재설정 사용

Windows 7, 8, 8.1 및 10을 실행하는 컴퓨터의 경우 사용자가 Windows 로그인 화면에서 암호를 재설정하도록 설정할 수 있습니다. 사용자는 더 이상 [SSPR 포털에](https://aka.ms/sspr)액세스하기 위해 웹 브라우저가 있는 장치를 찾을 필요가 없습니다.

![SSPR 링크가 표시된 예제 Windows 7 및 10 로그인 화면](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>일반적인 제한 사항

- 암호 재설정은 현재 원격 데스크톱 또는 Hyper-V 고급 세션에서 지원되지 않습니다.
- 일부 제 3 자 자격 증명 공급자는이 기능에 문제가 발생 하는 것으로 알려져 있습니다.
- [EnableLUA 레지스트리 키를](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) 수정하여 UAC를 사용하지 않도록 설정하면 문제가 발생하는 것으로 알려져 있습니다.
- 이 기능은 802.1x 네트워크 인증이 배포된 네트워크와 "사용자 로그온 직전에 수행"옵션이 있는 네트워크에서는 작동하지 않습니다. 802.1x 네트워크 인증이 배포된 네트워크의 경우 머신 인증을 사용하여 이 기능을 사용하는 것이 좋습니다.
- 하이브리드 Azure AD 조인 된 컴퓨터는 새 암호를 사용하고 캐시된 자격 증명을 업데이트하려면 도메인 컨트롤러에 대한 네트워크 연결 라인이 있어야 합니다.
- 이미지를 사용하는 경우 sysprep을 실행하기 전에 CopyProfile 단계를 수행하기 전에 기본 제공 관리자에 대한 웹 캐시가 지워졌는지 확인합니다. 이 단계에 대한 자세한 내용은 사용자 [지정 기본 사용자 프로필을 사용할 때](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)지원 문서 성능 저하에서 찾을 수 있습니다.
- 다음 설정은 Windows 10 장치에서 암호를 사용하고 재설정하는 기능을 방해하는 것으로 알려져 있습니다.
    - Windows 10 1809 이전 버전에서 정책이 Ctrl+Alt+Del을 요구하는 경우 **암호 재설정**이 작동하지 않습니다.
    - 잠금 화면 알림이 꺼진 경우 **암호 재설정**이 작동하지 않습니다.
    - HideFastUserSwitching이 사용하도록 설정 또는 1로 설정됨
    - DontDisplayLastUserName이 사용하도록 설정 또는 1로 설정됨
    - NoLockScreen이 사용하도록 설정 또는 1로 설정됨
    - EnableLostMode가 디바이스에서 설정됨
    - Explorer.exe는 사용자 지정 셸로 바뀜
- 다음 세 가지 설정을 조합하면 이 기능이 작동하지 않을 수 있습니다.
    - 대화형 로그온: CTRL+ALT+DEL = 사용 안 함이 필요하지 않음
    - 사용 안 함LockScreen앱 알림 = 1 또는 사용 가능
    - IsContent배달정책 적용 = 1 또는 True

## <a name="windows-10-password-reset"></a>윈도우 10 암호 재설정

### <a name="windows-10-prerequisites"></a>Windows 10 필수 조건

- 관리자는 Azure 포털에서 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정해야 합니다.
- **이 기능을 사용하기 전에 SSPR에 등록해야 합니다.**
- 네트워크 프록시 요구 사항
   - Windows 10 디바이스 
       - 포트 443~ `passwordreset.microsoftonline.com``ajax.aspnetcdn.com`
       - Windows 10 장치는 컴퓨터 수준 프록시 구성만 지원합니다.
- 적어도 실행 윈도우 10, 버전 4 월 2018 업데이트 (v1803), 장치는 중 하나여야합니다 :
    - Azure AD 가입
    - 하이브리드 Azure AD 가입

### <a name="enable-for-windows-10-using-intune"></a>윈도 즈에 대 한 사용 10 인튠을 사용 하 여

Intune을 사용하여 로그인 화면에서 암호 재설정을 사용하도록 설정하는 가장 유연한 방법은 구성 변경을 배포하는 것입니다. Intune을 사용하면 여러분이 정의하는 특정 머신 그룹에 구성 변경 내용을 배포할 수 있습니다. 이 메서드는 디바이스의 Intune 등록이 필요합니다.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune에서 디바이스 구성 정책 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 클릭
1. **Device configuration** > **Profiles**장치 > 구성 프로필 프로필**만들기로** 이동하여 새 장치 구성 프로필 만들기
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
      - **확인**을 클릭합니다.
   - **확인**을 클릭합니다.
1. **만들기를 클릭합니다.**
1. 이 정책은 특정 사용자, 장치 또는 그룹에 할당할 수 있습니다. 자세한 내용은 [Microsoft Intune의 사용자 및 장치 프로필 할당](https://docs.microsoft.com/intune/device-profile-assign)문서에서 찾을 수 있습니다.

### <a name="enable-for-windows-10-using-the-registry"></a>레지스트리를 사용하여 Windows 10용 으로 사용

1. 관리 자격 증명을 사용하여 Windows PC에 로그인
1. 관리자 권한으로 **regedit** 실행
1. 다음 레지스트리 키를 설정
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>윈도우 문제 해결 10 암호 재설정

Azure AD 감사 로그에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

사용자가 Windows 10 장치의 로그인 화면에서 암호를 재설정하면 권한이 낮은 임시 계정이라고 하는 `defaultuser1` 임시 계정이 만들어집니다. 이 계정은 암호 재설정 프로세스를 안전하게 유지하는 데 사용됩니다. 계정 자체는 무작위로 생성된 암호를 가지고 있으며, 디바이스 로그인 시 표시되지 않으며, 사용자가 암호를 설정하면 자동으로 제거됩니다. 여러 `defaultuser` 프로필이 존재할 수 있지만 무시해도 됩니다.

## <a name="windows-7-8-and-81-password-reset"></a>윈도우 7, 8, 8.1 암호 재설정

### <a name="windows-7-8-and-81-prerequisites"></a>윈도우 7, 8, 8.1 필수 구성 조건

- 관리자는 Azure 포털에서 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정해야 합니다.
- **이 기능을 사용하기 전에 SSPR에 등록해야 합니다.**
- 네트워크 프록시 요구 사항
   - 윈도우 7, 8 및 8.1 장치
       - 포트 443 -`passwordreset.microsoftonline.com`
- 패치가 적용된 Windows 7 또는 Windows 8.1 운영 체제
- [TLS(전송 계층 보안) 레지스트리 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)에 있는 지침을 사용하여 TLS 1.2 사용하도록 설정
- 컴퓨터에서 두 개 이상의 제3자 자격 증명 공급자를 사용하도록 설정하면 사용자는 로그인 화면에 두 개 이상의 사용자 프로필을 볼 수 있습니다.

> [!WARNING]
> TLS 1.2를 활성화해야 하며, 자동 협상으로 설정되는 것이 아닙니다.

### <a name="install"></a>설치

1. 사용할 Windows 버전에 대해 적절한 설치 관리자를 다운로드합니다.
   - 소프트웨어는 Microsoft 다운로드 센터에서 사용할 수 있습니다.[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. 설치하고 설치 관리자를 실행할 머신에 로그인합니다.
1. 설치 후에 다시 부팅하는 것이 좋습니다.
1. 재부팅 후 로그인 화면에서 사용자를 선택하고 "비밀번호 잊어버린것"을 클릭합니다. 암호 재설정 워크플로를 시작하려면
1. 암호를 재설정하는 화면 단계를 따라 워크플로를 완료합니다.

![예제 Windows 7 "암호 찾기" 클릭 SSPR 흐름](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>자동 설치

- 자동 설치의 경우 "msiexec /i SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.
- 자동 제거의 경우 "msiexec /x SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정 문제 해결

머신 및 Azure AD 모두에 이벤트가 기록됩니다. Azure AD 이벤트에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

추가 로깅이 필요한 경우 머신의 레지스트리 키를 변경하여 자세한 정보 표시 로깅을 사용할 수 있습니다. 문제 해결에 대해서만 자세한 정보 표시 로깅을 사용합니다.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- 자세한 로깅을 사용하려면 `REG_DWORD: “EnableLogging”`을 만들고 1로 설정합니다.
- 자세한 로깅을 사용하지 않으려면 0으로 `REG_DWORD: “EnableLogging”` 변경합니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

이제 Windows 장치에 대한 암호 재설정을 구성했으니 사용자에 대한 변경 사항은 무엇입니까? 로그인 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요?

![SSPR 링크가 표시된 예제 Windows 7 및 10 로그인 화면](./media/howto-sspr-windows/windows-reset-password.png)

사용자가 로그인을 시도하면 이제 로그인 화면에서 셀프 서비스 암호 재설정 환경을 여는 암호 **재설정** 또는 **잊어버린 암호** 링크가 표시됩니다. 사용자는 이 기능을 사용하면 다른 디바이스를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용하는 방법에 대한 지침은 [회사 또는 학교 암호 재설정](../user-help/active-directory-passwords-update-your-own-password.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[허용할 인증 방법 계획](concept-authentication-methods.md)

[윈도우 구성 10](https://docs.microsoft.com/windows/configuration/)
