---
title: Microsoft Authenticator 앱에 회사 또는 학교 계정 추가 - Azure AD
description: 2단계 인증을 사용하는 동안 Microsoft Authenticator 앱에 회사 또는 학교 계정을 추가하여 ID를 확인합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359118"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 회사 또는 학교 계정 추가

조직에서 2단계 인증을 사용하는 경우 인증 방법 중 하나로 Microsoft Authenticator 앱을 사용하도록 회사 또는 학교 계정을 설정할 수 있습니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

## <a name="add-your-work-or-school-account"></a>회사 또는 학교 계정 추가

다음 중 하나를 수행 하 여 Microsoft Authenticator 앱에 회사 또는 학교 계정을 추가할 수 있습니다.

- 회사 또는 학교 계정으로 로그인 자격 증명 (미리 보기)
- QR 코드 스캔

### <a name="sign-in-with-your-credentials"></a>자격 증명으로 로그인

>[!Note]
>이 기능은 관리자가 인증 앱을 사용 하 여 휴대폰 로그인을 사용 하도록 설정한 사용자만 사용할 수 있습니다.

자격 증명을 사용 하 여 회사 또는 학교 계정에 로그인 하 여 계정을 추가 하려면 다음을 수행 합니다.

1. Microsoft Authenticator 앱을 열고 단추를 선택 하 **+** 고 **회사 또는 학교 계정 추가** 를 탭 합니다. **로그인** 을 선택합니다.

1. 회사 또는 학교 계정 자격 증명을 입력 합니다. 임시 액세스 통과 (탭)를 사용 하는 경우이를 사용 하 여 로그인 할 수 있습니다. 이 시점에서 다음 조건 중 하나를 계속 진행 하지 못하도록 차단할 수 있습니다.

   - 강력한 인증 토큰을 얻기 위해 계정에 인증 방법이 충분 하지 않은 경우에는 계정 추가를 계속할 수 없습니다.

   - 메시지가 표시 `You might be signing in from a location that is restricted by your admin` 되 면 차단 되며 [보안 정보](https://mysignins.microsoft.com/security-info)에서 차단을 해제 하는 관리자가 필요 합니다.

   - 관리자가 Authenticator 앱을 사용 하 여 휴대폰 로그인을 차단 하지 않은 경우 장치 등록을 통해 암호 없는 휴대폰 로그인 및 MFA (Azure Multi-Factor Authentication)를 설정할 수 있습니다.

1. 이 시점에서 조직에서 제공 하는 QR 코드를 검색 하 여 앱에서 온-프레미스 multi-factor authentication 계정을 설정 하 라는 메시지가 표시 될 수 있습니다. 조직에서 온-프레미스 MFA 서버를 사용 하는 경우에만이 작업을 수행 해야 합니다.

1. 장치에서 계정을 탭 하 고 전체 화면 보기에서 계정이 올바른지와 연결 된 6 자리 확인 코드가 있는지 확인 합니다. 보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

## <a name="sign-in-with-a-qr-code"></a>QR 코드를 사용 하 여 로그인

QR 코드를 검색 하 여 계정을 추가 하려면 다음을 수행 합니다.

1. 컴퓨터에서 **추가 보안 확인** 페이지로 이동합니다.

   >[!Note]
   >**추가 보안 인증** 페이지가 표시되지 않는 경우에는 관리자가 보안 정보(미리 보기) 환경을 켰을 수 있습니다. 이 경우 [인증자 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md) 섹션의 지침을 따라야 합니다. 이외의 경우에는 조직의 지원 센터에 도움을 요청해야 합니다. 보안 정보에 대 한 자세한 내용은 [로그인 프롬프트에서 보안 정보 설정](security-info-setup-signin.md)을 참조 하세요.

1. Authenticator 앱 옆에 있는 확인란을 선택 하 고 **구성** 을 선택 합니다. **모바일 앱 구성** 페이지가 나타납니다.

   ![QR 코드를 제공 하는 화면](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Microsoft Authenticator 앱을 열고 더하기 아이콘을 선택 하 ![ 고, iOS 또는 Android 장치에서 더하기 아이콘을 선택 ](media/user-help-auth-app-add-work-school-account/plus-icon.png) 하 고, **계정 추가** 를 선택한 다음 **, 회사 또는 학교 계정,** **QR 코드 스캔** 을 차례로 선택 합니다.
   인증 앱에 계정이 설정 되어 있지 않으면 **계정 추가** 라는 파란색 단추가 표시 됩니다.

카메라를 사용 하 여 QR 코드를 스캔 하 라는 메시지가 표시 되지 않으면 휴대폰의 설정에서 인증자 앱이 전화 카메라에 액세스할 수 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
