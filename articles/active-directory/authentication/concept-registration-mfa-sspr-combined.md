---
title: Azure AD SSPR 및 Multi-factor Authentication (미리 보기)-Azure Active Directory에 대 한 등록을 결합
description: Azure AD 다단계 인증 및 셀프 서비스 암호 재설정 등록 (미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 572f8694d31728a3ca570f6ddb60475e13d71e80
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235581"
---
# <a name="combined-security-information-registration-preview"></a>결합 된 보안 정보 등록 (미리 보기)

결합 된 등록 하기 전에 사용자가 인증 방법을 Azure Multi-factor Authentication 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 별도로 등록 합니다. 사용자는 Multi-factor Authentication 및 SSPR에 대 한 유사한 메서드를 사용한 하지만 두 기능에 대 한 등록을 가졌던 혼동 했습니다. 이제 결합 된 등록 사용자를 두 번 등록할 수 및 Multi-factor Authentication 및 SSPR의 이점을 누릴 합니다.

![내 프로필 표시를 사용자에 대 한 보안 정보 등록](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

새 환경을 설정 하기 전에이 관리자에 초점을 맞춘 설명서 및 기능과이 기능의 효과 이해 하도록 사용자에 초점을 맞춘 설명서를 검토 합니다. 교육을 기준 합니다 [사용자 용 설명 문서](../user-help/user-help-security-info-overview.md) 새 환경 및 성공적인 롤아웃 하도록 도움을 위한 사용자를 준비 합니다.

Azure AD 등록 Azure US Government, Azure 독일 또는 Azure 중국 21Vianet 같은 국가별 클라우드에 현재 제공 되지 않습니다. 보안 정보를 결합 합니다.

|     |
| --- |
| Multi-factor Authentication 및 Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정에 대 한 통합된 보안 정보 등록에는 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

> [!IMPORTANT]
> 원본 미리 보기 및 향상 된 결합 된 등록 환경 모두에 대해 사용 하도록 설정 된 사용자는 새 동작을 표시 됩니다. 두 환경에 대해 설정 된 사용자는 새 내 프로필 환경만 표시 됩니다. 새 내 프로필 결합 된 등록의 모양과 느낌에 부합 하 고 사용자를 위한 원활한 환경을 제공 합니다. 사용자로 이동 하 여 내 프로필을 볼 수 있습니다 [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)합니다.

내 프로필 페이지는 페이지에 액세스 하는 컴퓨터의 언어 설정에 따라 지역화 됩니다. Microsoft는 이후에 페이지에 액세스 하는 데 사용 되는 마지막 언어로 렌더링 계속 되므로 브라우저 캐시에 사용 된 가장 최근 언어를 저장 합니다. 캐시의 선택을 취소 하면 페이지를 다시 렌더링 됩니다. 특정 언어를 강제로 하려는 경우 추가할 수 있습니다 `?lng=<language>` URL의 끝에 있는 `<language>` 렌더링 하려는 언어의 코드입니다.

![SSPR 또는 다른 보안 확인 방법으로 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>결합 된 등록에 사용할 수 있는 방법

다음 인증 방법 등록 지원 및 동작을 결합 합니다.

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
> 앱 암호는 Multi-factor authentication을 적용 된 한 사용자만 사용할 수 있습니다. 앱 암호는 Multi-factor Authentication에 대 한 조건부 액세스 정책을 통해 사용 하도록 설정 하는 사용자에 게 제공 되지 않습니다.

사용자는 기본 Multi-factor Authentication 방법으로 다음 옵션 중 하나를 설정할 수 있습니다.

- Microsoft Authenticator – 알림입니다.
- Authenticator 앱 또는 하드웨어 토큰 – 코드입니다.
- 전화 통화입니다.
- 텍스트 메시지입니다.

를 계속 더 많은 인증 방법을 Azure AD에 추가 됨에 따라 해당 메서드에 결합 된 등록에 제공 됩니다.

## <a name="combined-registration-modes"></a>결합 된 등록 모드

결합 된 등록의 두 가지 모드가 있습니다: 중단 하 고 관리 합니다.

- **중단 모드** 마법사와 같은 경험을 등록 또는 로그인 시 해당 보안 정보를 새로 고칠 때 사용자에 게 표시 됩니다.

- **관리 모드** 사용자 프로필의 일부 이며 해당 보안 정보를 관리할 수 있습니다.

두 모드 모두에 대 한 Multi-factor Authentication에 사용할 수 있는 메서드는 이전에 등록 하는 사용자가 보안 정보에 액세스 하기 전에 Multi-factor Authentication을 수행 해야 합니다.

### <a name="interrupt-mode"></a>중단 모드

결합 된 등록 모두 테 넌 트에 사용 되 면 Multi-factor Authentication 및 SSPR 정책을 따릅니다. 이러한 정책은 여부 사용자 로그인 시 등록에 대 한 중단 되 고 있는 메서드는 등록에 사용할 수를 제어 합니다.

등록 하거나 해당 보안 정보 새로 고침 하 라는 메시지가 표시 될 수 있습니다 사용자는 몇 가지 시나리오는 다음과 같습니다.

- Multi-factor Authentication 등록 Id 보호를 통해 적용 합니다. 사용자는 로그인 시 등록 하 라는 메시지가 표시 됩니다. (사용자가 SSPR에 대 한 사용) 하는 경우 Multi-factor Authentication 메서드와 SSPR 메서드 등록 합니다.
- Multi-factor Authentication 등록 사용자별 Multi-factor Authentication을 통해 적용 됩니다. 사용자는 로그인 시 등록 하 라는 메시지가 표시 됩니다. (사용자가 SSPR에 대 한 사용) 하는 경우 Multi-factor Authentication 메서드와 SSPR 메서드 등록 합니다.
- Multi-factor Authentication 등록 조건부 액세스 또는 기타 정책을 통해 적용 됩니다. 사용자는 Multi-factor Authentication을 요구 하는 리소스를 사용할 때 등록 하 라는 메시지가 표시 됩니다. (사용자가 SSPR에 대 한 사용) 하는 경우 Multi-factor Authentication 메서드와 SSPR 메서드 등록 합니다.
- SSPR 등록을 적용 합니다. 사용자는 로그인 시 등록 하 라는 메시지가 표시 됩니다. SSPR 방법만 등록 합니다.
- SSPR 새로 고침 작업을 적용 합니다. 사용자는 관리자가 설정한 간격에 해당 보안 정보를 검토 해야 사용자가 해당 정보를 표시 됩니다 및 현재 정보를 확인 하거나 필요한 경우 변경할 수 있습니다.

등록을 적용 하는 경우 사용자가 가장 낮은 보안 수준 가장의 Multi-factor Authentication 및 SSPR 정책을 준수 하는 데 필요한 방법 중 최소 수를 표시 됩니다.

예를 들면 다음과 같습니다.

- 사용자는 SSPR에 대 한 활성화 됩니다. SSPR 정책 다시 설정 하는 두 방법이 필요 하 고 모바일 앱 코드, 메일 및 전화를 활성화 합니다.
   - 이 사용자가 두 개의 메서드를 등록 해야 합니다.
      - 사용자는 기본적으로 authenticator 앱 및 phone 표시 됩니다.
      - 사용자는 authenticator 앱 또는 전화 대신 전자 메일 등록 하도록 선택할 수 있습니다.

이 순서도 설명 메서드는 로그인 시 등록을 중단 하는 경우 사용자에 게 표시 됩니다.

![결합 된 보안 정보 순서도](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Multi-factor Authentication 및 SSPR을 사용 하도록 설정 하는 경우에 Multi-factor Authentication 등록을 적용 하는 것이 좋습니다.

SSPR 정책 정기적으로 해당 보안 정보를 검토 하는 사용자가 필요한 경우 사용자가 로그인 하는 동안 중단 되며 등록 된 모든 메서드를 표시 됩니다. 최신 이거나 필요할 경우 변경할 수 현재 정보를 확인할 수 있습니다.

### <a name="manage-mode"></a>관리 모드

사용자가 액세스할 수로 이동 하 여 모드를 관리할 [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) 를 선택 하 여 **보안 정보** 내 프로필에서. 여기에서 사용자 수 메서드를 추가, 삭제 또는 기존 메서드를 변경, 기본 메서드 등을 변경 합니다.

## <a name="key-usage-scenarios"></a>주요 사용 시나리오

### <a name="set-up-security-info-during-sign-in"></a>로그인 하는 동안 보안 정보 설정

관리자가 적용 된 등록 합니다.

사용자가 모든 필요한 보안 정보 설정 및 Azure portal로 이동 합니다. 사용자 이름 및 암호를 입력 한 후 사용자는 보안 정보를 설정 하 라는 메시지가 표시 됩니다. 사용자는 다음 필요한 보안 정보를 설정 하려면 마법사에서 표시 되는 단계를 따릅니다. 설정을 허용 하는 경우 기본적으로 표시 된 것 이외의 메서드를 설정 하 선택할 수 있습니다. 마법사를 완료 한 후 사용자가 Multi-factor Authentication에 대 한 설정 메서드와 해당 기본 방법을 검토 합니다. 설치 프로세스를 완료 하려면 사용자 정보를 확인 하 고 Azure portal로 계속 됩니다.

### <a name="set-up-security-info-from-my-profile"></a>내 프로필의 보안 정보 설정

관리자가 등록을 적용 되지 않습니다.

아직 설정 하지 않은 모든 필요한 보안 정보는 사용자가 이동 [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)합니다. 사용자가 선택할 **보안 정보** 왼쪽된 창에서. 여기에서 사용자 메서드를 추가 하기로, 사용 가능한 방법 중 하나를 선택 하 고 해당 메서드를 설정 하는 단계를 따릅니다. 완료 되 면 사용자에 게 보안 정보 페이지에서 방금 설정 된 메서드를 표시 합니다.

### <a name="delete-security-info-from-my-profile"></a>내 프로필의 보안 정보 삭제

하나 이상의 메서드 이전에 설정한 사용자가 이동할 [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)합니다. 사용자가 이전에 등록 된 방법 중 하나를 삭제 하려면 선택 합니다. 완료 되 면 사용자는 더 이상 메서드 보안 정보 페이지에서 표시 합니다.

### <a name="change-the-default-method-from-my-profile"></a>내 프로필의 기본 메서드를 변경 합니다.

Multi-factor Authentication에 사용할 수 있는 하나 이상의 메서드 이전에 설정한 사용자가 이동할 [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo)합니다. 사용자는 서로 다른 기본 메서드에 현재 기본 메서드를 변경합니다. 완료 되 면 사용자에 게 보안 정보 페이지에서 새 기본 메서드의 표시 합니다.

## <a name="next-steps"></a>다음 단계

[테 넌 트의 결합 된 등록을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md)

[Multi-factor Authentication 및 SSPR에 대 한 사용 가능한 메서드](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-factor Authentication 구성](howto-mfa-getstarted.md)
