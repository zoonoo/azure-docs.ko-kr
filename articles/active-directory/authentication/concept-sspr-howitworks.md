---
title: 셀프 서비스 암호 재설정 심층 분석 - Azure Active Directory
description: SSPR(셀프 서비스 암호 재설정)은 어떻게 작동하나요?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a46d77b088d854bdd6ee8781b545d1236f357853
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071482"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>작동 방식: Azure AD SSPR(셀프 서비스 암호 재설정)

Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하면 관리자 또는 지원 센터에서 개입하지 않고도 사용자가 암호를 변경하거나 다시 설정할 수 있습니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다. [Azure AD에서 SSPR을 사용하도록 설정하고 구성하는 방법](https://www.youtube.com/watch?v=rA8TvhNcCvQ)에 대해 이 비디오를 권장합니다.

> [!IMPORTANT]
> 이 개념 문서에서는 관리자에게 셀프 서비스 암호 재설정이 작동하는 방법을 설명합니다. 셀프 서비스 암호 재설정에 이미 등록된 최종 사용자가 계정으로 다시 이동해야 하는 경우 [https://aka.ms/sspr](https://aka.ms/sspr)로 이동합니다.
>
> IT 팀이 자신의 암호를 재설정 하는 기능을 사용하도록 설정하지 않은 경우, 추가 지원이 필요 하면 기술 지원팀에 문의하세요.

## <a name="how-does-the-password-reset-process-work"></a>암호 재설정 프로세스 작동 방법

사용자는 [SSPR 포털](https://aka.ms/sspr)을 사용하여 암호를 재설정하거나 변경할 수 있습니다. 먼저 원하는 인증 방법을 등록해야 합니다. 사용자가 SSPR 포털에 액세스하는 경우 Azure 플랫폼은 다음 요인을 고려합니다.

* 페이지를 지역화하려면 어떻게 해야 하나요?
* 사용자 계정이 유효한가요?
* 사용자는 어떤 조직에 속해 있나요?
* 사용자의 암호는 어디서 관리하나요?
* 사용자가 기능을 사용하도록 허가되었나요?

사용자가 애플리케이션 또는 페이지에서 **계정에 액세스할 수 없음** 링크를 선택하거나 직접 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)로 이동하는 경우 SSPR 포털에서 사용되는 언어는 다음 옵션을 기반으로 합니다.

* 기본적으로 브라우저 로캘은 SSPR을 적절한 언어로 표시하는 데 사용됩니다. 암호 재설정 환경은 [Microsoft 365에서 지원](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)하는 것과 동일한 언어로 지역화됩니다.
* 특정 지역화된 언어로 SSPR에 연결하려면 암호 재설정 URL의 끝에 필요한 로캘과 함께 `?mkt=`를 추가합니다.
    * 예를 들어 스페인어 *es-us* 로캘을 지정하려면 `?mkt=es-us` - [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)를 사용합니다.

필요한 언어로 SSPR 포털이 표시되면 사용자에게 사용자 ID와 보안 문자를 입력하라는 메시지가 표시됩니다. Azure AD에서 다음 항목을 확인하여 사용자가 SSPR을 사용할 수 있는지 확인합니다.

* 사용자가 SSPR을 사용할 수 있고 Azure AD 라이선스가 할당되어 있는지 확인합니다.
  * 사용자가 SSPR을 사용할 수 없거나 라이선스가 할당되어 있지 않은 경우 사용자는 관리자에게 문의하여 암호를 재설정해야 합니다.
* 관리자 정책에 따라 사용자의 계정에 올바른 인증 방법이 정의되어 있는지 확인합니다.
  * 정책에 하나의 방법만 필요한 경우 사용자에게 관리자 정책에서 사용하도록 설정된 하나 이상의 인증 방법에 대해 정의된 적절한 데이터가 있는지 확인합니다.
    * 인증 방법이 구성되어 있지 않으면 사용자는 관리자에게 문의하여 암호를 재설정하라는 요청을 받습니다.
  * 정책에 두 방법만 필요한 경우 관리자 정책에서 사용하도록 설정된 두 인증 방법에 대해 정의된 적절한 데이터가 사용자에게 있는지 확인합니다.
    * 인증 방법이 구성되어 있지 않으면 사용자는 관리자에게 문의하여 암호를 재설정하라는 요청을 받습니다.
  * Azure 관리자 역할이 사용자에게 할당된 경우에는 강력한 2-게이트 암호 정책이 적용됩니다. 자세한 내용은 [관리자 재설정 정책의 차이점](concept-sspr-policy.md#administrator-reset-policy-differences)을 참조하세요.
* Azure AD 테넌트가 페더레이션, 통과 인증 또는 암호 해시 동기화를 사용하는 경우 등 사용자의 암호가 온-프레미스에서 관리되는지 확인합니다.
  * SSPR 쓰기 저장이 구성되어 있고 사용자의 암호가 온-프레미스에서 관리되는 경우 사용자가 인증을 진행하고 암호를 재설정할 수 있습니다.
  * SSPR 쓰기 저장이 배포되어 있지 않고 사용자의 암호가 온-프레미스에서 관리되는 경우 사용자는 관리자에게 문의하여 암호를 재설정해야 합니다.

이전 검사가 모두 성공적으로 완료되면 사용자에게 암호를 재설정하거나 변경하는 프로세스가 안내됩니다.

> [!NOTE]
> SSPR은 암호 재설정 프로세스의 일부로 사용자에게 이메일 알림을 보낼 수 있습니다. 이러한 이메일은 여러 지역에서 활성-활성 모드로 작동하는 SMTP 릴레이 서비스를 사용하여 전송됩니다.
>
> SMTP 릴레이 서비스는 이메일 본문을 수신하고 처리하지만 저장하지는 않습니다. 고객이 제공한 정보를 잠재적으로 포함할 수 있는 SSPR 이메일의 본문은 SMTP 릴레이 서비스 로그에 저장되지 않습니다. 로그에는 프로토콜 메타데이터만 포함됩니다.

SSPR을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: SSPR(셀프 서비스 암호 재설정) 사용](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>사용자가 로그인 시 등록하도록 요구하시겠습니까?

최신 인증 또는 웹 브라우저를 사용하여 Azure AD를 사용하는 애플리케이션에 로그인하는 경우 사용자가 SSPR 등록을 완료하도록 요구하는 옵션을 사용하도록 설정할 수 있습니다. 이 워크플로에는 다음 애플리케이션이 포함됩니다.

* Microsoft 365
* Azure Portal
* 액세스 패널
* 페더레이션된 애플리케이션
* Azure AD를 사용하여 애플리케이션 사용자 지정

등록이 필요하지 않은 경우에는 로그인 중에 사용자에 게 메시지가 표시되지 않지만 수동으로 등록할 수 있습니다. 액세스 패널의 **프로필** 탭 아래에서 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)을 방문하거나 **암호 재설정 등록** 링크를 선택할 수 있습니다.

![Azure Portal의 SSPR 등록 옵션][Registration]

> [!NOTE]
> 사용자가 **취소** 를 선택하거나 창을 닫으면 SSPR 등록 포털을 닫을 수 있습니다. 그러나 이 경우에는 등록을 완료할 때까지 로그인할 때마다 등록하도록 요구하는 메시지가 표시됩니다.
>
> SSPR에 등록하기 위한 이 인터럽트는 사용자가 이미 로그인되어 있는 경우 사용자의 연결을 중단하지 않습니다.

## <a name="reconfirm-authentication-information"></a>인증 정보 다시 확인

암호를 재설정하거나 변경해야 하는 경우 인증 방법이 올바른지 확인하기 위해 일정 시간 후 정보 등록 정보를 확인하도록 사용자에게 요구할 수 있습니다. 이 옵션은 **로그인 시 사용자에게 등록 요구** 옵션을 사용하도록 설정한 경우에만 사용할 수 있습니다.

사용자에게 등록된 방법을 확인하라는 메시지가 표시되는 유효한 값은 *0* 일-*730* 일입니다. 이 값을 *0* 으로 설정하면 사용자에게 인증 정보를 확인하도록 요청하지 않습니다. 결합 등록 환경을 사용하는 경우 사용자는 정보를 다시 확인하기 전에 ID를 확인해야 합니다.

## <a name="authentication-methods"></a>인증 방법

사용자가 SSPR을 사용하도록 설정된 경우 인증 방법을 하나 이상 등록해야 합니다. 사용자가 필요할 때 한 인증 방법에 액세스하지 못하는 경우, 더 유연하게 사용할 수 있도록 두 개 이상의 인증 방법을 선택하는 것이 좋습니다. 자세한 내용은 [인증 방법이란?](concept-authentication-methods.md)을 참조하세요.

SSPR에는 다음과 같은 인증 방법을 사용할 수 있습니다.

* 모바일 앱 알림
* 모바일 앱 코드
* Email
* 휴대폰
* 사무실 전화(유료 구독이 있는 테넌트에서만 사용 가능)
* 본인 확인 질문

사용자는 관리자가 사용하도록 설정한 인증 방법을 등록한 경우에만 암호를 재설정할 수 있습니다.

> [!WARNING]
> Azure *관리자* 역할이 할당된 계정은 [관리자 재설정 정책의 차이점](concept-sspr-policy.md#administrator-reset-policy-differences) 섹션에 정의된 방법을 사용해야 합니다.

![Azure Portal에서 인증 방법 선택][Authentication]

### <a name="number-of-authentication-methods-required"></a>필수 인증 방법의 수

사용자가 암호를 재설정하거나 잠금 해제하기 위해 제공해야 하는 사용 가능한 인증 방법의 수를 구성할 수 있습니다. 이 값은 *1* 또는 *2* 로 설정할 수 있습니다.

사용자는 여러 인증 방법을 등록할 수 있으며 등록해야 합니다. 다시 말해, 사용자가 필요할 때 한 인증 방법에 액세스할 수 없는 경우 더 유연하게 사용할 수 있도록 사용자는 두 개 이상의 인증 방법을 선택하는 것이 좋습니다.

사용자가 SSPR을 사용할 때 필요한 최소 방법 수를 등록하지 않은 경우 관리자에게 암호 재설정을 요청하도록 지시하는 오류 페이지가 나타납니다. 기존 사용자가 SSPR에 등록되어 있고 해당 기능을 사용할 수 없다면 필요한 방법 수를 1개에서 2개로 늘리는 경우 주의해야 합니다. 자세한 내용은 다음 [인증 방법 변경](#change-authentication-methods) 섹션을 참조하세요.

#### <a name="mobile-app-and-sspr"></a>모바일 앱 및 SSPR

Microsoft Authenticator 앱과 같은 모바일 앱을 암호 재설정 방법으로 사용할 경우 다음 사항을 고려해야 합니다.

* 관리자가 하나의 방법으로 암호를 재설정하도록 요구하는 경우 사용 가능한 유일한 옵션은 인증 코드입니다.
* 관리자가 두 가지 방법으로 암호를 재설정하도록 요구하는 경우, 사용자는 다른 활성화된 방법 외에 알림 **또는** 확인 코드를 사용할 수 있습니다.

| 재설정에 필요한 방법 수 | 하나 | 2 |
| :---: | :---: | :---: |
| 모바일 앱 기능 사용 가능 | 코드 | 코드 또는 알림 |

사용자는 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 셀프 서비스 암호 재설정을 등록할 때 모바일 앱을 등록하는 옵션이 없습니다. 사용자는 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 모바일 앱을 등록하거나 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 결합 보안 정보를 등록하여 모바일 앱을 등록할 수 있습니다.

> [!IMPORTANT]
> 하나의 방법만 필요한 경우 Authenticator 앱을 유일한 인증 방법으로 선택할 수 없습니다. 마찬가지로 두 가지 방법이 필요한 경우 Authenticator 앱과 하나의 추가 방법만 선택할 수도 없습니다.
>
> Authenticator 앱을 방법으로 포함하는 SSPR 정책을 구성할 때는 한 가지 방법이 필요한 경우에는 하나 이상의 추가 방법을 선택해야 하고, 두 가지 방법을 구성해야 할 경우에는 두 개 이상의 추가 방법을 선택해야 합니다.
>
> 이는 현재 SSPR 등록 환경에 Authenticator 앱을 등록하는 옵션이 없기 때문에 요구됩니다. Authenticator 앱을 등록하는 옵션은 새로운 [결합 등록 환경](./concept-registration-mfa-sspr-combined.md)에 포함되어 있습니다.
>
> Authenticator 앱(한 가지 방법이 필요한 경우) 또는 Authenticator 앱과 하나의 추가 방법(두 가지 방법이 필요한 경우)만 사용하는 정책을 허용하면 사용자가 새로운 결합 등록 환경을 사용하도록 구성될 때까지 SSPR 등록이 블록될 수 있습니다.

### <a name="change-authentication-methods"></a>인증 방법 변경

등록된 재설정 또는 잠금 해제에 필요한 인증 방법이 하나만 있는 정책으로 시작한 후에 이를 두 가지 방법으로 변경하면 어떻게 되나요?

| 등록 방법 수 | 필요한 방법 수 | 결과 |
| :---: | :---: | :---: |
| 1개 이상 | 1 | 다시 설정 또는 잠금 해제 **가능** |
| 1 | 2 | 다시 설정 또는 잠금 해제 **불가능** |
| 2 이상 | 2 | 다시 설정 또는 잠금 해제 **가능** |

사용 가능한 인증 방법을 변경하는 것도 사용자에게 문제가 발생할 수 있습니다. 사용자가 사용할 수 있는 인증 방법 유형을 변경하면 사용 가능한 최소 데이터 양이 없는 경우 사용자가 실수로 SSPR을 사용할 수 없게 될 수 있습니다.

다음 예제 시나리오를 고려해 보세요.

1. 원래 정책은 필요한 두 가지 인증 방법으로 구성됩니다. 사무실 전화 번호와 보안 질문만 사용합니다.
1. 관리자는 더 이상 보안 질문을 사용하지 않도록 정책을 변경하지만 휴대폰 및 보조 메일은 사용하도록 허용합니다.
1. 이제 휴대폰 또는 대체 이메일 필드를 입력하지 않은 사용자는 이제 암호를 재설정할 수 없습니다.

## <a name="notifications"></a>알림

SSPR을 사용하면 사용자와 ID 관리자 모두에 대한 알림을 구성하여 암호 이벤트의 인식을 향상시킬 수 있습니다.

### <a name="notify-users-on-password-resets"></a>사용자에게 암호 재설정에 대해 알림

이 옵션을 **예** 로 설정하면 암호를 재설정하는 사용자는 암호가 변경되었음을 알리는 이메일을 받습니다. 이 이메일은 SSPR 포털을 통해 Azure AD에 저장된 기본 및 대체 이메일 주소로 보내집니다. 누구도 재설정 이벤트의 알림을 받지 않습니다.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>다른 관리자가 암호를 재설정하면 모든 관리자에게 알림

이 옵션을 **예** 로 설정하면 다른 모든 Azure 관리자는 Azure AD의 파일에 저장된 기본 이메일 주소로 이메일을 받습니다. 전자 메일은 사용자에게 다른 관리자가 SSPR을 사용하여 암호를 변경했음을 알려줍니다.

다음 예제 시나리오를 고려해 보세요.

* 환경에 4명의 관리자가 있습니다.
* 관리자 *A* 가 SSPR을 사용하여 암호를 재설정합니다.
* 관리자 *B*, *C* 및 *D* 는 암호 재설정을 알리는 이메일을 받습니다.

## <a name="on-premises-integration"></a>온-프레미스 통합

하이브리드 환경이 있는 경우 Azure AD에서 온-프레미스 디렉터리로 암호 변경 이벤트를 다시 쓰도록 Azure AD Connect를 구성할 수 있습니다.

![암호 쓰기 저장이 사용하도록 설정되어 있고 작동하는지 확인][Writeback]

Azure AD는 현재 하이브리드 연결을 확인하여 Azure Portal에 다음 메시지 중 하나를 제공합니다.

* 온-프레미스 쓰기 저장 클라이언트가 실행 중입니다.
* Azure AD는 온라인 상태이며 온-프레미스 쓰기 저장 클라이언트에 연결되어 있습니다. 그러나 설치된 Azure AD Connect 버전이 만료된 것 같습니다. 최신 연결 기능을 사용하고 중요한 버그가 수정되도록 [Azure AD Connect 업그레이드](../hybrid/how-to-upgrade-previous-version.md)를 수행하는 것이 좋습니다.
* 안타깝게도 설치된 Azure AD Connect 버전이 오래되었으므로 온-프레미스 쓰기 저장 클라이언트 상태를 확인할 수 없습니다. 연결 상태를 확인할 수 있도록 [Azure AD Connect를 업그레이드](../hybrid/how-to-upgrade-previous-version.md)합니다.
* 현재 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. [Azure AD Connect 문제 해결](./troubleshoot-sspr-writeback.md)을 통해 연결을 복원합니다.
* 비밀번호 쓰기 저장이 올바르게 구성되지 않았으므로 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. [비밀번호 쓰기 저장 구성](./tutorial-enable-sspr-writeback.md)으로 연결을 복원합니다.
* 현재 온-프레미스 쓰기 저장 클라이언트에 연결할 수 없습니다. 일시적인 문제로 인한 것일 수 있습니다. 문제가 지속되면 [Azure AD Connect 문제 해결](./troubleshoot-sspr-writeback.md)을 통해 연결을 복원합니다.

SSPR 쓰기 저장을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정(SSPR) 쓰기 저장 사용](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>온-프레미스 디렉터리에 대한 비밀번호 쓰기 저장

Azure Portal을 사용하여 암호 쓰기 저장을 사용하도록 설정할 수 있습니다. 또한 Azure AD Connect를 다시 구성하지 않고 암호 쓰기 저장을 일시적으로 사용하지 않도록 설정할 수 있습니다.

* 옵션을 **예** 로 설정하면 쓰기 저장을 사용하도록 설정됩니다. 페더레이션되거나 통과 인증 또는 암호 해시 동기화된 사용자는 암호를 재설정할 수 있습니다.
* 옵션을 **아니요** 로 설정하면 쓰기 저장을 사용하지 않도록 설정됩니다. 페더레이션되거나 통과 인증 또는 암호 해시 동기화된 사용자는 암호를 재설정할 수 없습니다.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>사용자가 해당 암호를 재설정하지 않고 계정의 잠금을 해제할 수 있음

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 유연성을 제공하기 위해 사용자가 암호를 재설정하지 않고도 온-프레미스 계정의 잠금을 해제할 수 있도록 선택할 수 있습니다. 이 설정을 사용하여 다음 두 가지 작업을 분리합니다.

* **예** 로 설정하면 사용자에게는 암호를 재설정하고 계정을 잠금 해제하거나 암호를 재설정하지 않고도 계정을 잠금 해제할 수 있는 옵션이 제공됩니다.
* **아니요** 로 설정하면 사용자는 결합 암호 재설정 및 계정 잠금 해제 작업만 수행할 수 있습니다.

### <a name="on-premises-active-directory-password-filters"></a>온-프레미스 Active Directory 암호 필터

SSPR은 Active Directory에서 관리자가 시작한 암호 재설정과 동일한 작업을 수행합니다. 타사 암호 필터를 사용하여 사용자 지정 암호 규칙을 적용하고 Azure AD 셀프 서비스 암호 재설정 중에 이 암호 필터를 확인하도록 요구하는 경우에는 관리자 암호 재설정 시나리오에서 타사 암호 필터 솔루션을 적용하도록 구성해야 합니다. [ Active Directory Domain Service의 Azure AD 암호 보호](concept-password-ban-bad-on-premises.md)는 기본적으로 지원됩니다.

## <a name="password-reset-for-b2b-users"></a>B2B 사용자에 대한 암호 재설정

암호 재설정 및 변경은 모든 B2B(기업 간 전자 상거래) 구성에서 완전히 지원됩니다. B2B 사용자 암호 재설정은 다음 세 가지 경우에 지원됩니다.

* **기존 Azure AD 테넌트가 있는 파트너 조직의 사용자**: 파트너 조직에 기존 Azure AD 테넌트가 있는 경우 해당 테넌트에서 사용하도록 설정된 암호 재설정 정책이 모두 적용됩니다. 암호 재설정이 작동하려면 파트너 조직에서 Azure AD SSPR을 사용하도록 설정되어 있는지 확인해야 합니다. Microsoft 365 고객에게는 추가 요금이 부과되지 않습니다.
* 셀프 서비스 등록을 **통해 등록한 사용자**: 파트너 조직에서 [셀프 서비스 등록](../enterprise-users/directory-self-service-signup.md) 기능을 사용하여 테넌트에 들어간 경우 등록한 이메일로 암호를 재설정하도록 했습니다.
* **B2B 사용자**: 새 [Azure AD B2B 기능](../external-identities/what-is-b2b.md)을 사용하여 만든 모든 새 B2B 사용자도 초대 프로세스 중에 등록한 이메일을 통해 암호를 재설정할 수 있습니다.

이 시나리오를 테스트하려면 이러한 파트너 사용자 중 하나로 https://passwordreset.microsoftonline.com으로 이동합니다. 대체 전자 메일 또는 인증 전자 메일이 정의되어 있으면 암호 재설정이 예상대로 작동합니다.

> [!NOTE]
> Hotmail.com, Outlook.com 또는 기타 개인 이메일 주소와 같이 Azure AD 테넌트에 대한 게스트 액세스 권한이 부여된 Microsoft 계정은 Azure AD SSPR을 사용할 수 없습니다. [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에 있는 정보를 사용하여 암호를 다시 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

SSPR을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: SSPR(셀프 서비스 암호 재설정) 사용](tutorial-enable-sspr.md)

다음 문서에서는 Azure AD를 통한 암호 재설정에 대한 추가 정보를 제공합니다.

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "사용 가능한 Azure AD 인증 방법 및 필요한 수량"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Azure Portal에서 SSPR 등록 옵션 구성"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Azure Portal에서 SSPR의 온-프레미스 통합"
