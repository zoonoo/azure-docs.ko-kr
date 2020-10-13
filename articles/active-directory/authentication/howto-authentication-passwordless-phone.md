---
title: Microsoft Authenticator 앱을 사용 하 여 passwordless 로그인-Azure Active Directory
description: Microsoft Authenticator 앱 (미리 보기)을 사용 하 여 Azure AD에 대해 암호 없는 로그인을 사용 하도록 설정
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053a489993c31344b96e83253c88eed93b27b145
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964828"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Microsoft Authenticator 앱 (미리 보기)을 사용 하 여 암호 없는 로그인 사용

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 기술과 마찬가지로 Microsoft Authenticator는 키 기반 인증을 사용하여 디바이스에 연결되고 생체 인식 또는 PIN을 사용하는 사용자 자격 증명을 사용하도록 설정합니다. 이 인증 방법은 모바일을 비롯 한 모든 장치 플랫폼과 Microsoft 인증 라이브러리와 통합 되는 모든 앱 또는 웹 사이트에서 사용할 수 있습니다.

![사용자가 로그인을 승인 하도록 요청 하는 브라우저 로그인의 예](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

사용자 이름을 입력 한 후 암호를 입력 하 라는 메시지가 표시 되는 대신, Microsoft Authenticator 앱에서 휴대폰 로그인을 사용 하도록 설정한 사용자는 앱에서 번호를 탭 하도록 요청 하는 메시지를 볼 수 있습니다. 앱에서 로그인 프로세스를 완료 하려면 사용자가 번호와 일치 하 고 **승인**을 선택한 다음 PIN 또는 생체 인식 기능을 제공 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Microsoft Authenticator 앱에서 암호 없는 휴대폰 로그인을 사용 하려면 다음 필수 구성 요소를 충족 해야 합니다.

- Azure Multi-Factor Authentication, 푸시 알림이 확인 방법으로 허용 됩니다.
- iOS 8.0 이상 또는 Android 6.0 이상을 실행하는 디바이스에 설치된 최신 버전의 Microsoft Authenticator

> [!NOTE]
> Azure AD PowerShell을 사용 하 여 app 암호 없는 로그인 미리 보기를 사용 Microsoft Authenticator 하도록 설정한 경우 전체 디렉터리에 대해 사용 하도록 설정 되었습니다. 이 새 메서드를 사용 하도록 설정 하면 PowerShell 정책이 대신 됩니다. 새 *인증 방법* 메뉴를 통해 테 넌 트의 모든 사용자에 대해을 사용 하도록 설정 하는 것이 좋습니다. 그렇지 않으면 새 정책에 없는 사용자는 더 이상 암호 없이 로그인 할 수 없습니다.

## <a name="enable-passwordless-authentication-methods"></a>암호 없는 인증 방법 사용

Azure AD에서 암호 없는 인증을 사용 하려면 먼저 결합 된 등록 환경을 사용 하도록 설정한 다음 사용자가 암호를 사용 하지 않는 방법으로 사용 하도록 설정 합니다.

### <a name="enable-the-combined-registration-experience"></a>결합 된 등록 환경 사용

암호 없는 인증 방법에 대 한 등록 기능은 결합 된 등록 기능을 사용 합니다. 사용자가 결합 된 등록을 완료 하도록 하려면 단계에 따라 [결합 된 보안 정보 등록을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md)합니다.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>암호 없는 휴대폰 로그인 인증 방법 사용

Azure AD를 사용 하면 로그인 프로세스 중에 사용할 수 있는 인증 방법을 선택할 수 있습니다. 그런 다음 사용자가 사용 하려는 메서드에 등록 합니다.

암호 없는 휴대폰 로그인에 대 한 인증 방법을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. *전역 관리자* 계정을 사용 하 여 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. *Azure Active Directory*를 검색 하 고 선택한 다음, **보안**  >  **인증 방법**  >  **인증 방법 정책 (미리 보기)** 으로 이동 합니다.
1. **Passwordless 휴대폰 로그인**에서 다음 옵션을 선택 합니다.
   1. **사용** -예 또는 아니요
   1. **대상** -모든 사용자 또는 사용자 선택
1. 새 정책을 적용 하려면 **저장**을 선택 합니다.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 앱의 사용자 등록 및 관리

Azure AD에서 사용할 수 있는 암호 없는 인증 방법을 사용 하는 경우 사용자는 다음 단계를 사용 하 여 암호 없는 인증 방법에 대해 자신을 등록 해야 합니다.

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 으로 이동합니다.
1. 로그인 한 다음 add **메서드 > authenticator 앱**, **추가**를 차례로 선택 하 여 인증자 앱을 추가 합니다.
1. 지침에 따라 장치에 Microsoft Authenticator 앱을 설치 하 고 구성 합니다.
1. **완료** 를 선택 하 여 인증자 구성을 완료 합니다.
1. 앱 **Microsoft Authenticator** 에서 등록 된 계정에 대 한 드롭다운 메뉴에서 **휴대폰 로그인 사용** 을 선택 합니다.
1. 앱의 지침에 따라 암호 없는 휴대폰 로그인에 대 한 계정 등록을 완료 합니다.

조직에서는 사용자가 암호를 [사용](../user-help/user-help-auth-app-sign-in.md) 하 여 사용자에 게 로그인 하도록 지시 하 여 Microsoft Authenticator 앱을 구성 하 고 휴대폰 로그인을 사용 하도록 설정할 수 있습니다.

> [!NOTE]
> 정책에서 휴대폰 로그인을 사용 하도록 허용 하지 않은 사용자는 더 이상 Microsoft Authenticator 앱 내에서 사용 하도록 설정할 수 없습니다.  

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명을 사용 하 여 로그인

관리자가 테 넌 트를 사용 하도록 설정 하 고 사용자가 휴대폰 로그인을 사용 하도록 Microsoft Authenticator 앱을 업데이트 한 후 사용자가 암호 없는 로그인 사용을 시작할 수 있습니다.

로그인 페이지에서 사용자 이름을 입력 하 고 **다음**을 선택 하 고 나 서 휴대폰 로그인 사용을 시작 하려면 사용자가 **로그인 할 다른 방법을**선택 하 고 **내 Microsoft Authenticator 앱에서 요청을 승인**해야 할 수 있습니다. 그러면 사용자에 게 숫자가 표시 되 고 해당 암호를 사용 하는 대신 인증에 적합 한 수를 선택 하도록 Microsoft Authenticator 앱에 메시지가 표시 됩니다. 사용자가 암호 없는 휴대폰 로그인을 사용 하는 경우 다른 방법 선택을 사용할 때까지 다시 사용 하 라는 메시지가 표시 됩니다.

![Microsoft Authenticator 앱을 사용한 브라우저 로그인 예](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>알려진 문제

현재 미리 보기 환경에는 다음과 같은 알려진 문제가 있습니다.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>암호 없는 휴대폰 로그인에 대 한 옵션을 표시 하지 않음

사용자가 응답 하지 않는 암호를 사용 하 여 로그인 인증을 보류 중이 고 다시 로그인을 시도 하는 경우 사용자에 게 암호를 입력 하는 옵션만 표시 될 수 있습니다. Microsoft Authenticator를 열고 알림 메시지에 응답 하 여 암호 없는 휴대폰 로그인을 계속 사용 합니다.

### <a name="federated-accounts"></a>페더레이션된 계정

사용자가 암호 없는 자격 증명을 사용 하도록 설정 하면 Azure AD 로그인은 login_hint 사용을 중지 하 여 페더레이션 로그인 위치로 사용자를 가속화 합니다. 이 논리를 통해 하이브리드 테 넌 트의 사용자가 "대신 암호 사용"을 클릭 하는 추가 단계를 수행 하지 않고 로그인 확인을 위해 AD FS로 전달 되지 않습니다.

### <a name="azure-mfa-server"></a>Azure MFA 서버

조직의 온-프레미스 Azure MFA 서버를 통해 MFA를 사용 하도록 설정 된 최종 사용자는 여전히 단일 암호 없는 휴대폰 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명으로 Microsoft Authenticator의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.  

### <a name="device-registration"></a>디바이스 등록

이 새로운 강력한 자격 증명을 만들기 위한 필수 구성 요소 중 하나는 Microsoft Authenticator 앱이 설치 된 장치가 Azure AD 테 넌 트 내에 개별 사용자에 등록 되어야 한다는 것입니다. 현재 장치 등록 제한으로 인해 장치는 단일 테 넌 트에만 등록할 수 있습니다. 이 제한은 Microsoft Authenticator 앱에서 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 설정할 수 있음을 의미합니다.

> [!NOTE]
> 장치 등록은 장치 관리 또는 "MDM"과 동일 하지 않습니다. Azure AD 디렉터리에서 장치 ID와 사용자 ID를 함께 연결 합니다.  

## <a name="next-steps"></a>다음 단계

Azure AD 인증 및 암호 없는 방법에 대해 알아보려면 다음 문서를 참조 하세요.

* [암호 없는의 작동 원리 알아보기](concept-authentication-passwordless.md)
* [디바이스 등록에 대한 자세한 정보](../devices/overview.md#getting-devices-in-azure-ad)
* [Azure Multi-Factor Authentication에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)
