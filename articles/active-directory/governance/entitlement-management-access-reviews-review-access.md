---
title: Azure AD 자격 관리에서 액세스 패키지의 액세스 검토
description: Azure Active Directory 액세스 검토 (미리 보기)에서 자격 관리 액세스 패키지에 대 한 액세스 검토를 완료 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
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
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798600"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지의 액세스 검토

Azure AD 자격 관리는 기업에서 그룹, 응용 프로그램 및 SharePoint 사이트에 대 한 액세스를 관리 하는 방법을 간소화 합니다. 이 문서에서는 지정 된 검토자로 액세스 패키지에 할당 된 다른 사용자에 대 한 액세스 검토를 수행 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

사용자의 활성 액세스 패키지 할당을 검토 하려면 액세스 검토를 수행 하기 위한 필수 구성 요소를 충족 해야 합니다.
- Azure AD Premium P2
- 전역 관리자
- 지정 된 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.


## <a name="open-the-access-review"></a>액세스 검토 열기

다음 단계를 사용 하 여 액세스 검토를 찾아 엽니다.

1. Microsoft에서 액세스를 검토 하도록 요청 하는 전자 메일을 받을 수 있습니다. 액세스 검토를 열려면 전자 메일을 찾습니다. 액세스를 검토 하는 예제 메일은 다음과 같습니다.
    
    ![액세스 검토 검토자 전자 메일](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. **사용자 액세스 검토** 링크를 클릭 하 여 액세스 검토를 엽니다. 

1. 전자 메일이 없는 경우로 직접 이동 하 여 보류 중인 액세스 검토를 찾을 수 있습니다 https://myaccess.microsoft.com .  (미국 정부의 경우 대신를 사용 `https://myaccess.microsoft.us` 합니다.)

1. 왼쪽 탐색 모음에서 **액세스 검토** 를 클릭 하 여 사용자에 게 할당 된 보류 중인 액세스 검토 목록을 표시 합니다.
    
    ![내 액세스에 대 한 액세스 검토 선택](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 시작 하려는 검토를 클릭 합니다.
    
    ![액세스 검토를 선택 합니다.](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 검토 해야 하는 사용자의 이름이 표시 됩니다. 액세스를 승인 하거나 거부할 수 있는 두 가지 방법이 있습니다.
- 하나 이상의 사용자에 대 한 액세스를 수동으로 승인 또는 거부할 수 있습니다.
- 시스템 권장 사항에 동의할 수 있습니다.

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대 한 액세스를 수동으로 승인 또는 거부
1. 사용자 목록을 검토 하 고 계속 액세스 해야 하는 사용자를 결정 합니다.

    ![검토할 사용자 목록](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 액세스를 승인 하거나 거부 하려면 사용자 이름 왼쪽에 있는 라디오 단추를 선택 합니다.

1. 사용자 이름 위의 표시줄에서 **승인** 또는 **거부** 를 선택 합니다.

    ![사용자 선택](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 확실 하지 않은 경우 [ **알 수 없음** ] 단추를 클릭 하면 됩니다.

    이 옵션을 선택 하면 사용자가 액세스를 유지 관리 하 고이 선택 항목이 감사 로그에 표시 됩니다. 이 로그에는 검토를 완료 한 다른 검토자가 표시 됩니다.

1. 결정에 대 한 이유를 제공 해야 할 수도 있습니다. 이유를 입력 하 고 **제출**을 클릭 합니다.

    ![액세스 승인 또는 거부](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 검토가 끝나기 전에 언제 든 지 결정을 변경할 수 있습니다. 이렇게 하려면 목록에서 사용자를 선택 하 고 결정을 변경 합니다. 예를 들어 이전에 거부 한 사용자에 대 한 액세스를 승인할 수 있습니다.

검토자가 여러 개인 경우 마지막 제출 된 응답이 기록 됩니다. 관리자가 Alice와 Bob의 두 검토자를 지정 하는 예를 살펴보겠습니다. Alice가 검토를 먼저 열고 액세스를 승인 합니다. 검토가 끝나기 전에 Bob은 검토를 열고 액세스를 거부 합니다. 이 경우 마지막 거부 액세스 결정이 기록 됩니다.

>[!NOTE]
>사용자의 액세스가 거부 되 면 액세스 패키지에서 즉시 제거 되지 않습니다. 검토가 종료 되거나 관리자가 검토를 끝내 면 액세스 패키지에서 사용자가 제거 됩니다.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>시스템 생성 권장 사항을 사용 하 여 액세스 승인 또는 거부

여러 사용자에 대 한 액세스를 더 신속 하 게 검토 하려면 시스템 생성 권장 사항을 사용 하 여 한 번의 클릭으로 권장 사항을 적용 하면 됩니다. 권장 사항은 사용자의 로그인 활동을 기반으로 생성 됩니다.

1.  페이지 위쪽의 표시줄에서 **권장 구성 적용**을 클릭 합니다.
    
    ![권장 구성 적용 선택](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    권장 되는 작업에 대 한 요약이 표시 됩니다.

1.  **제출** 을 클릭 하 여 권장 사항을 적용 합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지 자체 검토](entitlement-management-access-reviews-self-review.md)