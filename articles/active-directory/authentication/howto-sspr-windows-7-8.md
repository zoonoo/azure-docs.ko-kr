---
title: Windows 7 및 8.1-Azure Active Directory에 azure AD 셀프 서비스 암호 재설정
description: Windows 7 또는 8.1 로그온 화면에서 암호를 사용하여 셀프 서비스 암호 재설정을 사용하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a7752fac54f9dfb2f8fb0aecd3b6249c52c3bcf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316357"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>방법: Windows 7, 8 및 8.1에서 암호 재설정 사용

관리자 권한으로 SSPR(셀프 서비스 암호 재설정)을 사용하도록 설정했지만 사용자가 [SSPR 포털](https://aka.ms/sspr)에 액세스하기 위해 브라우저 창을 열 수 없기 때문에 해당 암호를 재설정하도록 지원 센터를 계속 호출합니다. Windows 10 머신의 경우 [로그인 화면에서 Azure AD 암호 재설정](tutorial-sspr-windows.md) 자습서를 사용하여 로그온 화면에서 "암호 재설정" 링크를 설정할 수 있습니다. 다음 지침을 통해 Windows 7, 8 및 8.1 사용자가 Windows 로그온 화면에서 SSPR을 사용하여 해당 암호를 재설정할 수 있습니다.

Windows 10 머신과 달리 Windows 7, 8 및 8.1 머신에는 암호 재설정을 위해 Azure AD 도메인에 조인되거나 Active Directory 도메인에 조인된 요구 사항이 없습니다.

!["암호 찾기"를 사용하는 예제 Windows 7 로그온 화면 표시된 링크](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>필수 조건

* Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정.
* 패치가 적용된 Windows 7 또는 Windows 8.1 운영 체제
* [TLS(전송 계층 보안) 레지스트리 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)에 있는 지침을 사용하여 TLS 1.2 사용하도록 설정

> [!WARNING]
> TLS 1.2를 자동 인식할 뿐만 아니라 사용하도록 설정해야 합니다.

## <a name="install"></a>설치

1. 사용할 Windows 버전에 대해 적절한 설치 관리자를 다운로드합니다.

   1. [https://aka.ms/sspraddin](https://aka.ms/sspraddin)의 Microsoft 다운로드 센터에서 소프트웨어를 지원합니다.

1. 설치하고 설치 관리자를 실행할 머신에 로그인합니다.
1. 설치 후에 다시 부팅하는 것이 좋습니다.
1. 다시 부팅한 후에 로그온 화면에서 사용자를 선택하고 "암호 찾기"를 클릭합니다. 암호 재설정 워크플로를 시작하려면
1. 암호를 재설정하는 화면 단계를 따라 워크플로를 완료합니다.

![예제 Windows 7 "암호 찾기" 클릭 셀프 서비스 암호 재설정 흐름](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>자동 설치

* 자동 설치의 경우 "msiexec /i SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.
* 자동 제거의 경우 "msiexec /x SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.

## <a name="caveats"></a>주의 사항

"암호 찾기" 링크를 사용하기 전에 SSPR에 등록해야 합니다.

![암호를 재설정하기 위해 추가 보안 정보가 필요합니다.](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

재설정할 알림 및 코드에 대해 Microsoft Authenticator 앱을 사용하면 암호가 이 초기 릴리스에서 작동하지 않습니다. 사용자는 정책 요구 사항을 충족하는 대체 방법을 등록해야 합니다.

## <a name="troubleshooting"></a>문제 해결

머신 및 Azure AD 모두에 이벤트가 기록됩니다.

Azure AD 이벤트에는 암호 재설정이 발생하는 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![예제 Windows 7 로그온 화면 Azure AD 감사 로그의 암호 재설정](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

추가 로깅이 필요한 경우 머신의 레지스트리 키를 변경하여 자세한 정보 표시 로깅을 사용할 수 있습니다. 문제 해결에 대해서만 자세한 정보 표시 로깅을 사용합니다.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* 자세한 정보 표시 로깅을 사용하려면 REG_DWORD: "EnableLogging"을 만들고, 1로 설정합니다.
* 자세한 정보 표시 로깅을 사용하지 않으려면 REG_DWORD: "EnableLogging"을 0으로 변경합니다.

Windows 7, 8 및 8.1 컴퓨터가 프록시 서버 또는 방화벽 뒤에 있는 경우 passwordreset.microsoftonline.com에 대한 HTTPS 트래픽(443)을 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

[Windows 10 사용자가 로그온 화면에서 해당 암호를 재설정할 수 있습니다](tutorial-sspr-windows.md).
