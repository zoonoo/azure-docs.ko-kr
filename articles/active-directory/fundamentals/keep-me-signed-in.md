---
title: "' 로그인 유지? '를 구성 합니다. Azure Active Directory 계정 확인"
description: 로그인 상태 유지를 표시 하는 KMSI (로그인 유지)에 대해 알아봅니다. 프롬프트, Azure Active Directory 포털에서 구성 하는 방법 및 로그인 문제를 해결 하는 방법을 설명 합니다.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320259"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>' 로그인 유지? '를 구성 합니다. Azure AD 계정에 대 한 프롬프트

로그인 상태 유지 (KMSI) 사용자가 성공적으로 로그인 한 후 로그인을 표시 **하 시겠습니까?** 라는 메시지를 표시 합니다. 사용자가이 프롬프트에 대 한 **예** 를 대답 하면 로그인 유지 서비스는 영구 [새로 고침 토큰](../develop/developer-glossary.md#refresh-token)을 제공 합니다. 페더레이션된 테 넌 트의 경우 사용자가 페더레이션된 id 서비스를 사용 하 여 성공적으로 인증 된 후 프롬프트가 표시 됩니다.

다음 다이어그램은 관리 되는 테 넌 트 및 페더레이션된 테 넌 트에 대 한 사용자 로그인 흐름과 새 로그인 유지 프롬프트를 보여 줍니다. 이 흐름에는 machine learning 시스템이 공유 장치에서 높은 위험 수준 로그인 또는 로그인을 감지 하는 경우 **로그인 유지** 가 표시 되지 않는 스마트 논리가 포함 되어 있습니다.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="관리 되는 테 넌 트 및 페더레이션 테 넌 트의 사용자 로그인 흐름을 보여 주는 다이어그램":::

> [!NOTE]
> 로그인 유지 옵션을 구성 하려면 Azure Active Directory (Azure AD) Premium 1, Premium 2 또는 Basic 버전을 사용 하거나 Microsoft 365 라이선스를 사용 해야 합니다. 라이선스 및 버전에 대한 자세한 내용은 [Azure AD Premium에 등록](active-directory-get-started-premium.md)을 참조하세요.<br><br>Azure AD Premium 및 Basic 버전은 전 세계의 Azure AD 인스턴스를 사용 하 여 중국의 고객에 게 제공 됩니다. Azure AD Premium 및 Basic 버전은 현재 중국 21Vianet이 운영하는 Azure 서비스에서 지원되지 않습니다. 자세한 내용은 [AZURE AD 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 사용 하 여 문의 하세요.

## <a name="configure-kmsi"></a>KMSI 구성

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory**를 선택 하 고 **회사 브랜딩**을 선택한 다음 **구성**을 선택 합니다.
1. **고급 설정** 섹션에서 **로그인 상태를 유지 하려면 표시 옵션** 을 찾습니다.

   이 설정을 통해 사용자가 명시적으로 로그 아웃할 때까지 Azure AD에 로그인 상태를 유지할지 여부를 선택할 수 있습니다.
   * **아니요**를 선택 하는 경우 사용자가 성공적으로 로그인 하 고 브라우저를 닫았다가 다시 열 때마다 사용자가 로그인 해야 하는 경우 로그인 **유지?** 옵션이 숨겨집니다.
   * **예**를 선택 하면 사용자에 게 **로그인 유지?** 옵션이 표시 됩니다.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="로그인 설정 상태를 유지 하는 표시 옵션을 보여 주는 스크린샷":::

## <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

사용자가 로그인 **유지** 를 수행 하지 않는 경우 다음 다이어그램에 표시 된 것 처럼 프롬프트를 표시 하지만 로그인 시도를 중단 하면 인터럽트를 나타내는 로그인 로그 항목이 표시 됩니다.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="로그인 상태를 유지 하 시겠습니까? 요청할":::

로그인 오류에 대 한 자세한 내용은 예제에서 다음과 같이 강조 표시 되어 있습니다.

* **로그인 오류 코드**: 50140
* **실패 이유**: 사용자가 로그인 할 때 "로그인 유지" 인터럽트 때문에이 오류가 발생 했습니다.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="로그인 유지 인터럽트가 포함 된 로그인 로그 항목의 예":::

고급 브랜딩 설정에서 **표시 옵션을 로그인 상태로 유지 설정을** **아니요** 로 설정 하 여 사용자가 인터럽트를 보지 못하도록 중지할 수 있습니다. 이렇게 하면 Azure AD 디렉터리의 모든 사용자에 대해 KMSI 프롬프트가 비활성화 됩니다.

조건부 액세스에서 영구 브라우저 세션 컨트롤을 사용 하 여 사용자가 KMSI 프롬프트를 볼 수 없도록 할 수도 있습니다. 이 옵션을 사용 하면 디렉터리에서 남은 사용자의 로그인 동작에 영향을 주지 않고 사용자의 선택 그룹 (예: 전역 관리자)에 대 한 KMSI 프롬프트를 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [사용자 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)를 참조 하세요. 

사용자에 게 유용할 수 있는 경우에만 KMSI 프롬프트가 표시 되도록 하기 위해 다음 시나리오에는 KMSI 프롬프트가 의도적으로 표시 되지 않습니다.

* 사용자가 원활한 SSO 및 IWA (Windows 통합 인증)를 통해 로그인 됨
* 사용자가 Active Directory Federation Services 및 IWA를 통해 로그인 됨
* 사용자가 테 넌 트의 게스트입니다.
* 사용자의 위험 점수가 높습니다.
* 로그인은 사용자 또는 관리자 동의 흐름 중에 발생 합니다.
* 영구 브라우저 세션 제어가 조건부 액세스 정책에서 구성 됨

## <a name="next-steps"></a>다음 단계

로그인 세션 시간 제한에 영향을 주는 기타 설정에 대해 알아봅니다.

* Microsoft 365- [유휴 세션 제한 시간](/sharepoint/sign-out-inactive-users)
* Azure AD 조건부 액세스- [사용자 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure Portal – [디렉터리 수준 비활성 시간 제한](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)