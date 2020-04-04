---
title: Azure AD 권한 관리 - Azure Active Directory에서 액세스 패키지에 대한 요청 및 승인 설정 변경
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 요청 및 승인 설정을 변경하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0eae4f53283c33f3d7372a606f8c0a3ac27d079
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655945"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 요청 및 승인 설정 변경

액세스 패키지 관리자는 정책을 편집하거나 새 정책을 추가하여 언제든지 액세스 패키지를 요청할 수 있는 사용자를 변경할 수 있습니다. 승인 설정을 변경할 수도 있습니다.

이 문서에서는 기존 액세스 패키지에 대한 요청 및 승인 설정을 변경하는 방법을 설명합니다.

## <a name="choose-between-one-or-multiple-polices"></a>하나 또는 여러 개의 경찰 중에서 선택

액세스 패키지를 요청할 수 있는 사람을 지정하는 방법은 정책입니다. 액세스 패키지를 만들 때 정책을 만드는 요청 및 승인 설정을 지정합니다. 대부분의 액세스 패키지에는 단일 정책이 있지만 단일 액세스 패키지에는 여러 정책이 있을 수 있습니다. 다른 요청 및 승인 설정을 사용하여 다른 사용자 집합에 할당을 부여하도록 허용하려는 경우 액세스 패키지에 대해 여러 정책을 만들 수 있습니다. 예를 들어 내부 및 외부 사용자를 동일한 액세스 패키지에 할당하는 데 단일 정책을 사용할 수 없습니다. 그러나 동일한 액세스 패키지에 내부 사용자를 위한 정책과 외부 사용자를 위한 두 개의 정책을 만들 수 있습니다. 사용자에게 적용되는 정책이 여러 개 있는 경우 할당할 정책을 선택하라는 요청 시 메시지가 표시됩니다. 다음 다이어그램은 두 개의 정책이 있는 액세스 패키지를 보여 주십습니다.

![액세스 패키지의 여러 정책](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| 시나리오 | 정책 수 |
| --- | --- |
| 내 디렉터리에 있는 모든 사용자가 액세스 패키지에 대해 동일한 요청 및 승인 설정을 갖도록 합니다. | 1 |
| 연결된 특정 조직의 모든 사용자가 액세스 패키지를 요청할 수 있기를 바랍니다. | 1 |
| 내 디렉터리에 있는 사용자와 내 디렉터리 외부의 사용자가 액세스 패키지를 요청할 수 있도록 허용하려고 합니다. | 여러 접두사 |
| 일부 사용자에 대해 다른 승인 설정을 지정하고 싶습니다. | 여러 접두사 |
| 일부 사용자가 패키지 할당에 액세스하는 동안 다른 사용자가 액세스를 확장할 수 있기를 원합니다. | 여러 접두사 |

여러 정책이 적용될 때 사용되는 우선 순위 논리에 대한 자세한 내용은 [여러 정책을](entitlement-management-troubleshoot.md#multiple-policies
)참조하십시오.

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>기존 요청 및 승인 설정 열기

액세스 패키지에 대한 요청 및 승인 설정을 변경하려면 해당 정책을 열어야 합니다. 다음 단계에 따라 액세스 패키지에 대한 요청 및 승인 설정을 엽니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **정책을** 클릭한 다음 편집할 정책을 클릭합니다.

    정책 세부 정보 창은 페이지 하단에서 열립니다.

    ![액세스 패키지 - 정책 세부 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집하려면 **편집을** 클릭합니다.

    ![액세스 패키지 - 정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **요청** 탭을 클릭하여 요청 및 승인 설정을 엽니다.

1. 다음 요청 섹션 중 하나에서 단계를 수행합니다.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>요청 및 승인 설정의 새 정책 추가

요청 및 승인 설정이 다른 사용자 집합이 있는 경우 새 정책을 만들어야 할 수 있습니다. 다음 단계에 따라 기존 액세스 패키지에 새 정책을 추가하기 시작합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **패키지 액세스를** 클릭한 다음 액세스 패키지를 엽니다.

1. **정책을** 클릭한 다음 **정책 추가.**

1. 정책에 대한 이름과 설명을 입력합니다.

    ![이름 및 설명이 있는 정책 만들기](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **다음**을 클릭하여 **요청** 탭을 엽니다.

1. 다음 요청 섹션 중 하나에서 단계를 수행합니다.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

정책을 편집하는 경우 **업데이트 를**클릭합니다. 새 정책을 추가하는 경우 을 **클릭합니다.**

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 수명 주기 설정 변경](entitlement-management-access-package-lifecycle-policy.md)
- [액세스 패키지에 대한 요청 보기](entitlement-management-access-package-requests.md)