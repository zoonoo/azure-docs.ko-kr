---
title: SSPR 및 MFA에 대한 결합된 등록 - Azure Active Directory
description: Azure AD 다단계 인증 및 셀프 서비스 암호 재설정 등록(미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cb5aca128679b21072a2a3daa503dc43a8e2885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942884"
---
# <a name="combined-security-information-registration-preview"></a>통합 보안 정보 등록(미리 보기)

결합된 등록 전에 사용자는 Azure 다단계 인증 및 셀프 서비스 암호 재설정(SSPR)에 대한 인증 방법을 별도로 등록했습니다. 사람들은 다단계 인증 및 SSPR에 유사한 방법이 사용되었지만 두 기능에 대해 등록해야 한다는 사실에 혼란스러워했습니다. 이제 결합 된 등록으로 사용자는 한 번 등록하고 다단계 인증 및 SSPR의 이점을 얻을 수 있습니다.

![사용자에 대한 등록된 보안 정보를 보여주는 내 프로필](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

새 환경을 활성화하기 전에 이 관리자 중심 설명서와 사용자 중심 설명서를 검토하여 이 기능의 기능과 효과를 이해합니다. [사용자 설명서를](../user-help/user-help-security-info-overview.md) 기반으로 교육을 기반으로 사용자가 새로운 환경을 준비할 수 있도록 하고 성공적인 롤아웃을 보장하는 데 도움이 됩니다.

Azure AD 결합 보안 정보 등록은 현재 Azure 미국 정부, Azure 독일 또는 Azure China 21Vianet과 같은 국가 클라우드에서 사용할 수 없습니다.

|     |
| --- |
| 다단계 인증 및 Azure Active Directory(Azure AD) 셀프 서비스 암호 재설정에 대한 통합 보안 정보 등록은 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.|
|     |

> [!IMPORTANT]
> 원래 미리 보기와 향상된 통합 등록 환경을 모두 사용할 수 있는 사용자는 새 동작을 볼 수 있습니다. 두 경험 모두에 대해 사용하도록 설정된 사용자는 새 내 프로필 환경만 볼 수 있습니다. 새로운 내 프로필은 통합 등록의 모양과 느낌에 부합하며 사용자에게 원활한 환경을 제공합니다. 사용자가 내 프로필로 이동하여 내 프로필을 볼 수 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)있습니다.

> [!NOTE] 
> 보안 정보 옵션에 액세스하는 동안 오류 메시지가 표시될 수 있습니다. 예를 들어 "죄송합니다, 로그인할 수 없습니다". 이 경우 웹 브라우저에서 타사 쿠키를 차단하는 구성 또는 그룹 정책 개체가 없는지 확인합니다. 

내 프로필 페이지는 페이지에 액세스하는 컴퓨터의 언어 설정에 따라 지역화됩니다. Microsoft는 브라우저 캐시에 사용된 최신 언어를 저장하므로 이후 페이지에 액세스하려는 시도는 사용된 마지막 언어로 계속 렌더링됩니다. 캐시를 지우면 페이지가 다시 렌더링됩니다. 특정 언어를 강제로 사용하려는 경우 렌더링하려는 언어의 코드가 `?lng=<language>` 있는 `<language>` URL 끝에 추가할 수 있습니다.

![SSPR 또는 기타 보안 검증 방법 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>결합 등록에서 사용할 수 있는 방법

결합 등록은 다음 인증 방법 및 작업을 지원합니다.

|   | 등록 | 변경 | DELETE |
| --- | --- | --- | --- |
| Microsoft Authenticator | 예(최대 5개) | 예 | yes |
| 기타 인증자 앱 | 예(최대 5개) | 예 | yes |
| 하드웨어 토큰 | 예 | 예 | yes |
| Phone | yes | yes | yes |
| 대체 전화 | yes | yes | yes |
| 사무실 전화 | 예 | 예 | 예 |
| Email | yes | yes | yes |
| 보안 질문 | yes | 예 | yes |
| 앱 암호 | yes | 예 | yes |
| FIDO2 보안 키<br />*[보안 정보](https://mysignins.microsoft.com/security-info) 페이지에서만 관리 모드*| yes | yes | yes |

> [!NOTE]
> 앱 암호는 다단계 인증을 적용한 사용자만 사용할 수 있습니다. 조건부 액세스 정책을 통해 다단계 인증을 사용하도록 설정된 사용자는 앱 암호를 사용할 수 없습니다.

사용자는 다음 옵션 중 하나를 기본 다단계 인증 방법으로 설정할 수 있습니다.

- 마이크로 소프트 인증자 - 알림.
- 인증자 앱 또는 하드웨어 토큰 - 코드.
- 전화 통화.
- 문자 메시지.

Azure AD에 인증 메서드를 계속 추가하면 이러한 메서드를 결합된 등록에서 사용할 수 있습니다.

## <a name="combined-registration-modes"></a>결합 된 등록 모드

통합 등록에는 인터럽트 및 관리의 두 가지 모드가 있습니다.

- **인터럽트 모드는** 로그인 시 보안 정보를 등록하거나 새로 고칠 때 사용자에게 표시되는 마법사와 같은 환경입니다.

- **관리 모드는** 사용자 프로필의 일부이며 사용자가 보안 정보를 관리할 수 있도록 합니다.

두 모드 모두 이전에 다단계 인증에 사용할 수 있는 메서드를 등록한 사용자는 보안 정보에 액세스하기 전에 다단계 인증을 수행해야 합니다.

### <a name="interrupt-mode"></a>인터럽트 모드

결합된 등록은 테넌트에 대해 둘 다 활성화된 경우 다단계 인증 및 SSPR 정책을 모두 고려합니다. 이러한 정책은 사용자가 로그인 하는 동안 등록에 대 한 중단 여부 및 등록에 사용할 수 있는 메서드를 제어 합니다.

다음은 사용자가 보안 정보를 등록하거나 새로 고치라는 메시지가 표시될 수 있는 몇 가지 시나리오입니다.

- ID 보호를 통해 적용되는 다단계 인증 등록: 로그인 하는 동안 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR에 대해 사용하도록 설정된 경우 다단계 인증 방법 및 SSPR 메서드를 등록합니다.
- 사용자별 다단계 인증을 통해 적용되는 다단계 인증 등록: 로그인 하는 동안 등록 하라는 메시지가 표시됩니다. 사용자가 SSPR에 대해 사용하도록 설정된 경우 다단계 인증 방법 및 SSPR 메서드를 등록합니다.
- 조건부 액세스 또는 기타 정책을 통해 적용되는 다단계 인증 등록: 다단계 인증이 필요한 리소스를 사용할 때 사용자에게 등록하라는 메시지가 표시됩니다. 사용자가 SSPR에 대해 사용하도록 설정된 경우 다단계 인증 방법 및 SSPR 메서드를 등록합니다.
- SSPR 등록 시행: 로그인 하는 동안 등록 하 라는 메시지가 표시 됩니다. SSPR 메서드만 등록합니다.
- SSPR 새로 고침 적용: 사용자는 관리자가 설정한 간격으로 보안 정보를 검토해야 합니다. 사용자에게 정보가 표시되며 현재 정보를 확인하거나 필요한 경우 변경할 수 있습니다.

등록이 적용되면 사용자는 다단계 인증 및 SSPR 정책을 모두 준수하는 데 필요한 최소 방법 수가 가장 안전한 방법부터 가장 안전하지 않은 메서드까지 표시됩니다.

예를 들어:

- 사용자는 SSPR에 대해 사용할 수 있습니다. SSPR 정책에는 재설정하는 두 가지 방법이 필요하며 모바일 앱 코드, 전자 메일 및 전화를 사용하도록 설정했습니다.
   - 이 사용자는 두 가지 방법을 등록해야 합니다.
      - 사용자는 기본적으로 인증자 앱과 전화가 표시됩니다.
      - 사용자는 인증자 앱 또는 전화 대신 전자 메일을 등록하도록 선택할 수 있습니다.

이 순서도는 로그인 중에 등록을 중단할 때 사용자에게 표시되는 메서드를 설명합니다.

![결합된 보안 정보 순서도](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

다단계 인증과 SSPR을 모두 사용하도록 설정한 경우 다단계 인증 등록을 적용하는 것이 좋습니다.

SSPR 정책에서 사용자가 정기적으로 보안 정보를 검토하도록 요구하는 경우 로그인 중에 사용자가 중단되고 등록된 모든 메서드가 표시됩니다. 최신 상태인 경우 현재 정보를 확인하거나 필요한 경우 변경할 수 있습니다. 사용자는 이 페이지에 액세스할 때 다단계 인증을 수행해야 합니다.

### <a name="manage-mode"></a>관리 모드

사용자는 내 프로필에서 보안 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) **정보를** 선택하거나 이동하여 관리 모드에 액세스할 수 있습니다. 거기에서 사용자는 메서드를 추가하고, 기존 메서드를 삭제 또는 변경하고, 기본 메서드를 변경하는 등의 작업을 할 수 있습니다.

## <a name="key-usage-scenarios"></a>주요 사용 시나리오

### <a name="set-up-security-info-during-sign-in"></a>로그인하는 동안 보안 정보 설정

관리자가 등록을 적용했습니다.

사용자가 필요한 모든 보안 정보를 설정하지 않았으며 Azure 포털로 이동합니다. 사용자 이름과 암호를 입력하면 보안 정보를 설정하라는 메시지가 표시됩니다. 그런 다음 사용자는 마법사에 표시된 단계를 수행하여 필요한 보안 정보를 설정합니다. 설정에서 허용하는 경우 사용자는 기본적으로 표시된 방법 이외의 메서드를 설정하도록 선택할 수 있습니다. 마법사를 완료한 후 사용자는 설정한 방법과 다단계 인증에 대한 기본 방법을 검토합니다. 설정 프로세스를 완료하기 위해 사용자는 정보를 확인하고 Azure 포털로 계속 이동합니다.

### <a name="set-up-security-info-from-my-profile"></a>내 프로필에서 보안 정보 설정

관리자가 등록을 적용하지 않았습니다.

필요한 모든 보안 정보를 아직 설정하지 않은 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)사용자는 로 이동합니다. 사용자는 왼쪽 창에서 **보안 정보를** 선택합니다. 거기에서 사용자는 메서드를 추가하도록 선택하고 사용 가능한 메서드 중 하나를 선택하고 해당 메서드를 설정하는 단계를 따릅니다. 작업이 완료되면 사용자는 보안 정보 페이지에서 방금 설정한 메서드를 볼 수 있습니다.

### <a name="delete-security-info-from-my-profile"></a>내 프로필에서 보안 정보 삭제

이전에 하나 이상의 메서드를 설정한 사용자는 로 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)이동합니다. 사용자는 이전에 등록된 메서드 중 하나를 삭제하도록 선택합니다. 완료되면 사용자는 보안 정보 페이지에서 해당 메서드를 더 이상 볼 수 없습니다.

### <a name="change-the-default-method-from-my-profile"></a>내 프로필에서 기본 메서드 변경

이전에 다단계 인증에 사용할 수 있는 하나 이상의 메서드를 설정한 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)사용자는 로 이동합니다. 사용자는 현재 기본 메서드를 다른 기본 메서드로 변경합니다. 작업이 완료되면 사용자는 보안 정보 페이지에 새 기본 메서드를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[사용자가 인증 방법을 다시 등록하도록 강제](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[테넌트에서 결합된 등록 사용](howto-registration-mfa-sspr-combined.md)

[SSPR 및 MFA 사용 및 인사이트 보고](howto-authentication-methods-usage-insights.md)

[다단계 인증 및 SSPR에 사용할 수 있는 방법](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication 구성](howto-mfa-getstarted.md)
