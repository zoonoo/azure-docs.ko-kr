---
title: Azure AD SSPR 및 MFA (미리 보기)-Azure Active Directory에 대 한 결합 된 등록
description: Azure AD 다단계 인증 및 셀프 서비스 암호 재설정 등록 (미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2865c19e747ca1c5b0a6cda84b8be18bfaeb9335
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317666"
---
# <a name="combined-security-information-registration-preview"></a>결합 된 보안 정보 등록 (미리 보기)

결합 된 등록 하기 전에 사용자가 Azure Multi-factor Authentication (MFA) 및 두 가지 다른 환경을 통해 셀프 서비스 암호 재설정 (SSPR)에 대 한 인증 메서드를 등록 합니다. 사용자는 Azure MFA 및 SSPR에 대 한 유사한 메서드를 사용한 하지만 개별적으로 각 기능에 대 한 등록을 가졌던 혼동 했습니다. 이제 결합 된 등록 사용자를 두 번 등록할 수 있습니다 및 Azure MFA와 SSPR의 이점을 누릴 합니다.

![내 프로필 디렉터리에 샘플 사용자에 대 한 Microsoft Authenticator 및 전화 번호를 포함 하 여 사용자에 대해 등록 된 보안 정보를 보여 주는 보안 정보를 결합 합니다.](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

새 환경을 설정 하기 전에이 관리자에 초점을 맞춘 설명서 및 기능과이 기능에 미치는 영향을 이해 하도록 사용자에 초점을 맞춘 설명서를 검토 합니다. 새 환경에 대 한 사용자에 게 준비 하 고 출시를 확인 하는 데 도움이 사용자 문서에서 교육을 기본입니다.

|     |
| --- |
| Azure Multi-factor Authentication 및 Azure AD 셀프 서비스 암호 재설정에 대 한 통합된 보안 정보 등록에는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

> [!IMPORTANT]
> 사용자가 원래 미리 보기 및 향상 된 결합 된 등록 환경 모두에 대해 사용 하도록 설정 하는 경우 새 환경이 표시 됩니다. 두 환경에 대해 설정 된 사용자는 새 내 프로필 환경만 표시 됩니다. 새 내 프로필 결합 된 등록의 모양과 느낌에 부합 하 고 사용자를 위한 원활한 환경을 제공 합니다. 사용자로 이동 하 여 내 프로필을 볼 수 있습니다 [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)합니다.

Myprofile 페이지를 페이지에 액세스 하는 컴퓨터의 현재 언어 설정에 따라 지역화 됩니다. Microsoft는 사용 된 마지막 언어로 렌더링 계속 액세스 하는 후속 하려고 하므로 브라우저 캐시에 활용 가장 최근 언어를 저장 합니다. 캐시를 지우면 하면 페이지를 다시 렌더링 합니다. 추가 하는 특정 언어를 강제로 하려는 경우를 `?lng=de-DE` URL의 끝에 있는 `de-DE` 설정 된 코드는 페이지를 해당 언어로 렌더링을 강제 하는 데 적절 한 언어입니다.

![보안 정보 및 SSPR 또는 다른 추가 보안 확인 방법을 설정 하는 사용자에 대 한 기능을 보여 주는 내 프로필 인터페이스입니다.](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>수렴 형된 등록에 사용할 수 있는 방법

이 경우 결합 된 등록 해당 메서드에 대 한 다음 메서드 및 작업을 지원합니다.

|   | 등록 | 변경 | 삭제 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 예 (최대 5) | 아닙니다. | 예 |
| 다른 authenticator 앱 | 예 (최대 5) | 아닙니다. | 예 |
| 하드웨어 토큰 | 아닙니다. | 아니요 | 예 |
| Phone | 예 | 예 | 예 |
| 대체 전화 | 예 | 예 | 예 |
| 사무실 전화 | 아닙니다. | 아니요 | 아닙니다. |
| Email | 예 | 예 | 예 |
| 보안 질문 | 예 | no | 예 |
| 앱 암호 | 예 | no | 예 |

> [!NOTE]
> 앱 암호에만 사용자에 게 MFA에 대 한 적용 된가 제공 됩니다. 앱 암호는 MFA에 대 한 조건부 액세스 정책을 통해 사용 하도록 설정 하는 사용자에 게 제공 되지 않습니다.

사용자가 MFA에 대 한 기본 방법으로 다음 옵션을 설정할 수 있습니다.:

- Microsoft Authenticator-알림
- Authenticator 앱 또는 하드웨어 토큰 – 코드
- 전화 통화
- 문자 메시지

더 많은 인증 방법을 추가 하려면 이러한 Azure AD로 계속 하는 대로 해당 메서드에 결합 된 등록에 제공 됩니다.

## <a name="combined-registration-modes"></a>결합 된 등록 모드

결합 된 등록의 두 가지 "모드" 가지: 중단 하 고 관리 합니다.

중단 모드, 마법사와 같은 경험을 등록 또는 로그인 시 해당 보안 정보를 새로 고칠 때 사용자에 게 표시 됩니다.

관리 모드 사용자 프로필의 일부 이며 해당 보안 정보를 관리할 수 있습니다.

두 모드 모두에 대 한 사용자가 mfa를 사용할 수 있는 메서드를 이전에 등록 하는 경우 해야 해당 보안 정보에 액세스 하기 전에 MFA를 수행 합니다.

### <a name="interrupt-mode"></a>중단 모드

결합 된 등록 모두 테 넌 트에 사용 되 면 MFA와 SSPR 정책을 따릅니다. 이러한 정책 제어, 사용자 등록, 로그인 하는 동안 중단 되는 여부 및 메서드는 등록에 사용할 수 있습니다.

다음 등록 하거나 해당 보안 정보 새로 고침 하 라는 메시지가 사용자를 수 있는 몇 가지 시나리오를 나열 합니다.

* Id 보호를 통해 적용 되는 MFA 등록: 사용자는 로그인 시 등록 하 라는 메시지가 표시 됩니다. (사용자가 SSPR에 대 한 사용) 하는 경우 MFA 방법을 메서드와 SSPR 등록 합니다.
* 사용자별 MFA를 통해 적용 되는 MFA 등록: 사용자는 로그인 시 등록 하 라는 메시지가 표시 됩니다. (사용자가 SSPR에 대 한 사용) 하는 경우 MFA 방법을 메서드와 SSPR 등록 합니다.
* 조건부 액세스 또는 기타 정책을 통해 적용 되는 MFA 등록: 사용자는 MFA를 요구 하는 리소스에 액세스 하는 경우 등록 하 라는 메시지가 표시 됩니다. (사용자가 SSPR에 대 한 사용) 하는 경우 사용자는 MFA 방법 및 SSPR 메서드 등록 됩니다.
* SSPR 등록을 적용 합니다. 사용자는 로그인 시 등록 하 라는 메시지가 표시 됩니다. SSPR 메서드만 등록
* SSPR 새로 고침 작업을 적용 합니다. 사용자는 관리자가 설정한 간격에 해당 보안 정보를 검토 해야 사용자가 해당 정보를 표시 됩니다 및 "정상"를 선택 하거나 필요한 경우 변경할 수 있습니다.

등록을 적용 하는 경우 사용자는 가장 낮은 보안 수준에 메서드 대부분에서 MFA 및 SSPR 정책을 준수 하는 데 필요한 최소 수를 표시 됩니다.

예제:

* 사용자는 SSPR에 대 한 활성화 됩니다. SSPR 정책 다시 설정 하는 두 방법이 필요 하 고 모바일 앱 코드, 메일 및 전화를 활성화 합니다.
   * 이 사용자가 두 개의 메서드를 등록 해야 합니다.
      * 표시 된 authenticator 앱 및 phone 기본적으로 합니다.
      * 사용자는 authenticator 앱 또는 전화 대신 전자 메일 등록 하도록 선택할 수 있습니다.

다음 순서도에서는 로그인 시 등록 하는 메서드를 중단 하는 경우 사용자에 게 보여 줍니다 설명 합니다.

![로그인 할 때 자세한 정보가 필요할 때 필요한 방법 수를 설명 하는 보안 정보 흐름 차트를 결합 합니다. 이 MFA 또는 SSPR만 필요한 경우 변경 수 있습니다.](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

MFA 및 SSPR을 사용 하도록 설정 하는 경우에 MFA 등록을 적용 하는 것이 좋습니다.

SSPR 정책에 일정 한 간격으로 해당 보안 정보를 검토 하는 사용자가 필요한 경우 사용자가 로그인 하는 동안 중단 되며 등록 된 모든 메서드를 표시 됩니다. 정보가 최신 상태 인지 또는 "정보 편집"을 선택할 수 있는 경우 "정상" 수도 변경할 수 있도록 합니다.

### <a name="manage-mode"></a>관리 모드

사용자가 액세스할 수로 이동 하 여 모드를 관리할 [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) 또는 내 프로필에서 "보안 정보"를 선택 하 여 합니다. 여기에서 사용자 수 메서드를 추가, 삭제 또는 기존 메서드를 변경, 해당 기본 메서드 등을 변경 합니다.

## <a name="key-usage-scenarios"></a>주요 사용 시나리오

### <a name="set-up-security-info-during-sign-in"></a>로그인 하는 동안 보안 정보 설정

관리자가 적용 된 등록 합니다.

사용자가 모든 필요한 보안 정보 설정 및 Azure portal로 이동 합니다. 사용자 이름과 암호를 입력 한 후 사용자는 보안 정보를 설정 하 라는 메시지가 표시 됩니다. 사용자는 다음 필요한 보안 정보를 설정 하려면 마법사에서 표시 되는 단계를 따릅니다. 사용자는 설정을 허용 하는 경우 기본적으로 것 이외의 메서드를 설정할 수도 있습니다. 마법사의 끝 사용자 MFA에 대 한 설정 메서드와 해당 기본 방법을 검토 합니다. 설치 프로세스를 완료 하려면 사용자 정보를 확인 하 고 Azure portal로 계속 됩니다.

### <a name="set-up-security-info-from-my-profile"></a>내 프로필의 보안 정보 설정

관리자가 등록을 적용 되지 않습니다.

모든 필요한 보안 정보를 아직 설정 하지 않았으면 사용자가 이동할 [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)합니다. 다음 선택할 **보안 정보** 왼쪽 탐색 영역에서 합니다. 여기에서 사용자를 사용할 수 있는 방법 중 하나를 선택 하며 해당 메서드를 설정 하는 단계를 따릅니다 메서드를 추가 하려면 선택 합니다. 완료 되 면 메서드는 방금 설정한 보안 정보 페이지에서 사용자에 게 표시 합니다.

### <a name="delete-security-info-from-my-profile"></a>내 프로필의 보안 정보 삭제

하나 이상의 메서드 이전에 설정한 사용자가 이동할 [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)합니다. 사용자가 이전에 등록 된 방법 중 하나를 삭제 하려면 선택 합니다. 완료 되 면 사용자는 더 이상 메서드 보안 정보 페이지에서 표시 합니다.

### <a name="change-default-method-from-my-profile"></a>내 프로필의 기본 방법 변경

MFA에 대해 사용할 수 있는 하나 이상의 메서드 이전에 설정한 사용자가 이동할 [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)합니다. 사용자는 서로 다른 기본 메서드에 현재 기본 메서드를 변경합니다. 완료 되 면 보안 정보 페이지에서 새 기본 메서드는 사용자에 게 표시 합니다.

## <a name="next-steps"></a>다음 단계

[테 넌 트의 결합 된 등록을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md)

[MFA 및 SSPR에 대 한 사용 가능한 메서드](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-factor Authentication 구성](howto-mfa-getstarted.md)
