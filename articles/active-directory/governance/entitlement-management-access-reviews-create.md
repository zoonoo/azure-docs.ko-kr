---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 액세스 검토 만들기
description: Azure Active Directory 액세스 검토(미리 보기)에서 권한 부여 관리 액세스 패키지에 대한 액세스 검토 정책을 만드는 방법을 알아봅니다.
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
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608840"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 액세스 검토 만들기

부실 액세스의 위험을 줄이려면 Azure AD 권한 관리에서 액세스 패키지에 대한 활성 할당이 있는 사용자를 정기적으로 검토할 수 있도록 설정해야 합니다. 새 액세스 패키지를 만들거나 기존 액세스 패키지를 편집할 때 검토를 활성화할 수 있습니다. 이 문서에서는 액세스 패키지에 대한 액세스 검토를 활성화하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

액세스 패키지를 검토하려면 액세스 패키지를 만들기 위한 필수 구성 조건을 충족해야 합니다.
- Azure AD Premium P2
- 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.


## <a name="create-an-access-review-of-an-access-package"></a>액세스 패키지의 액세스 검토 만들기

[새 액세스 패키지를 만들거나](entitlement-management-access-package-create.md) 기존 액세스 패키지 정책을 편집할 때 액세스 검토를 [활성화할](entitlement-management-access-package-lifecycle-policy.md) 수 있습니다. 다음 단계에 따라 액세스 패키지에 대한 액세스 검토를 활성화합니다.

1. 액세스 패키지의 **수명 주기** 탭을 열고 아래로 스크롤하여 **액세스 검토.**

1. 액세스 **요구 검토를** **예로**전환합니다.

    ![액세스 검토 추가](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 검토가 시작되는 날짜를 다음으로 **지정합니다.**

1. 다음으로 검토 **빈도를** **매년,** **격년,** **분기별** 또는 **월별로**설정합니다.
이 설정은 액세스 검토가 발생하는 빈도를 결정합니다.

1. 지속 **시간을** 설정하여 반복 시리즈의 각 검토가 검토자의 입력에 대해 열려 있는 일수를 정의합니다. 예를 들어 1월 1일에 시작하여 30일 동안 검토를 위해 열려 있는 연간 검토를 예약하여 검토자가 월말까지 응답할 수 있도록 할 수 있습니다.

1. **검토자**다음으로 사용자가 자신의 액세스 검토를 수행하도록 하려면 **자체 검토를** 선택하거나 검토자를 지정하려면 **특정 검토자를** 선택합니다.

    ![검토자 추가 선택](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. **특정 검토기를**선택한 경우 액세스 검토를 수행할 사용자를 지정합니다.
    1. **검토자 추가를**선택합니다.
    1. **검토자 선택** 창에서 검토자가 될 사용자를 검색하고 선택합니다.
    1. 검토인을 선택한 경우 **선택** 버튼을 클릭합니다.

    ![검토자 지정](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 페이지 하단에 있는 액세스 패키지를 편집하는 경우 새 액세스 패키지를 만들거나 업데이트를 만드는 경우 **검토 + 만들기를** 클릭합니다. **Update**

## <a name="view-the-status-of-the-access-review"></a>액세스 검토 의 상태 보기

시작 날짜 이후에 액세스 검토가 액세스 **검토** 섹션에 나열됩니다. 다음 단계에 따라 액세스 검토 상태를 확인합니다.

1. **ID 거버넌스에서** **액세스 패키지를** 클릭한 다음 확인하려는 액세스 검토 상태가 있는 액세스 패키지를 선택합니다.   

1. 액세스 패키지 개요에 있으면 왼쪽 메뉴에서 **리뷰 보기를 클릭합니다.**
    
    ![액세스 리뷰 선택](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 연결된 액세스 검토가 있는 모든 정책이 포함된 목록이 나타납니다. 리뷰를 클릭하여 보고서를 확인합니다.

    ![액세스 리뷰 일람](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 보고서를 볼 때 검토한 사용자 수와 검토자가 수행한 작업이 표시됩니다.

    ![검토 상태 보기](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>리뷰 이메일 알림 에 액세스
검토자를 지정하거나 사용자가 자신의 액세스 권한을 스스로 검토할 수 있습니다. 기본적으로 Azure AD는 검토가 시작된 직후 검토자 또는 자체 검토자에게 전자 메일을 보냅니다.

이메일에는 패키지 액세스에 대한 액세스를 검토하는 방법에 대한 지침이 포함됩니다. 사용자가 액세스 권한을 검토할 수 있는 경우 액세스 패키지에 대한 자체 검토를 수행하는 방법에 대한 지침을 보여 준다.
  
게스트 사용자를 검토자로 할당하고 Azure AD 게스트 초대를 수락하지 않은 경우 Azure AD 액세스 검토에서 전자 메일을 받지 않습니다. 먼저 초대를 수락하고 Azure AD를 사용하여 계정을 만들어야 전자 메일을 받을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 액세스 검토](entitlement-management-access-reviews-review-access.md)
- [액세스 패키지 자체 검토](entitlement-management-access-reviews-self-review.md)
