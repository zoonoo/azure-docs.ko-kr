---
title: Azure AD 자격 관리에서 액세스 패키지에 대 한 요청 및 승인 설정 변경-Azure Active Directory
description: Azure Active Directory 자격 관리에서 액세스 패키지에 대 한 요청 및 승인 설정을 변경 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ef2faf2a1d1a131dc5f2a01d3fa46cc61a06fb6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078808"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 요청 및 승인 설정 변경

액세스 패키지 관리자로 서 언제 든 지 정책을 편집 하거나 새 정책을 추가 하 여 액세스 패키지를 요청할 수 있는 사용자를 변경할 수 있습니다. 승인 설정을 변경할 수도 있습니다.

이 문서에서는 기존 액세스 패키지에 대 한 요청 및 승인 설정을 변경 하는 방법을 설명 합니다.

## <a name="choose-between-one-or-multiple-polices"></a>하나 또는 여러 정책 중에서 선택

액세스 패키지를 요청할 수 있는 사용자를 지정 하는 방법은 정책을 사용 하는 것입니다. 액세스 패키지를 만들 때 정책을 만드는 요청 및 승인 설정을 지정 합니다. 대부분의 액세스 패키지에는 단일 정책이 있지만 단일 액세스 패키지는 여러 정책을 포함할 수 있습니다. 서로 다른 요청 및 승인 설정을 사용 하 여 다른 사용자 집합에 할당을 부여할 수 있도록 허용 하려면 액세스 패키지에 대 한 정책을 여러 개 만듭니다. 예를 들어 동일한 액세스 패키지에 내부 및 외부 사용자를 할당 하는 데는 단일 정책을 사용할 수 없습니다. 그러나 동일한 액세스 패키지에 두 개의 정책을 만들 수 있습니다. 하나는 내부 사용자를 위한 것이 고 다른 하나는 외부 사용자를 위한 것입니다. 사용자에 게 적용 되는 정책이 여러 개 있는 경우 요청 시 사용자에 게 할당 하려는 정책을 선택 하 라는 메시지가 표시 됩니다. 다음 다이어그램에서는 두 개의 정책을 포함 하는 액세스 패키지를 보여 줍니다.

![액세스 패키지의 여러 정책](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 시나리오 | 정책 수 |
| --- | --- |
| 내 디렉터리의 모든 사용자에 게 액세스 패키지에 대 한 동일한 요청 및 승인 설정을 포함 하려고 합니다. | 하나 |
| 연결 된 특정 조직의 모든 사용자가 액세스 패키지를 요청할 수 있도록 하려고 합니다. | 하나 |
| 내 디렉터리의 사용자와 내 디렉터리의 사용자가 액세스 패키지를 요청 하도록 허용 합니다. | 여러 |
| 일부 사용자에 대해 다른 승인 설정을 지정 하려고 합니다. | 여러 |
| 일부 사용자가 패키지 할당을 액세스 하 여 다른 사용자가 액세스를 확장할 수 있도록 하려고 합니다. | 여러 |

여러 정책이 적용 될 때 사용 되는 우선 순위 논리에 대 한 자세한 내용은 [여러 정책](entitlement-management-troubleshoot.md#multiple-policies
)을 참조 하세요.

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>요청 및 승인 설정의 기존 정책 열기

액세스 패키지에 대 한 요청 및 승인 설정을 변경 하려면 해당 정책을 열어야 합니다. 액세스 패키지에 대 한 요청 및 승인 설정을 열려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 편집 하려는 정책을 클릭 합니다.

    정책 세부 정보 창이 페이지 아래쪽에 열립니다.

    ![액세스 패키지-정책 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집 하려면 **편집** 을 클릭 합니다.

    ![액세스 패키지-정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **요청 탭을** 클릭 하 여 요청 및 승인 설정을 엽니다.

1. 다음 요청 섹션 중 하나에서 단계를 수행 합니다.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>요청 및 승인 설정의 새 정책 추가

다른 요청 및 승인 설정을 가져야 하는 사용자 집합이 있는 경우 새 정책을 만들어야 할 가능성이 높습니다. 기존 액세스 패키지에 새 정책 추가를 시작 하려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 **정책 추가**를 클릭 합니다.

1. 정책에 대 한 이름 및 설명을 입력 합니다.

    ![이름 및 설명을 사용 하 여 정책 만들기](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **다음**을 클릭하여 **요청** 탭을 엽니다.

1. 다음 요청 섹션 중 하나에서 단계를 수행 합니다.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

정책을 편집 하는 경우 **업데이트**를 클릭 합니다. 새 정책을 추가 하는 경우 **만들기**를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 수명 주기 설정 변경](entitlement-management-access-package-lifecycle-policy.md)
- [액세스 패키지에 대 한 요청 보기](entitlement-management-access-package-requests.md)