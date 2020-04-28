---
title: Azure AD 자격 관리에서 액세스 패키지에 대 한 액세스 검토 만들기
description: Azure Active Directory 액세스 검토 (미리 보기)에서 자격 관리 액세스 패키지에 대 한 액세스 검토 정책을 만드는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73608840"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 액세스 검토 만들기

부실 액세스의 위험을 줄이려면 Azure AD 자격 관리에서 액세스 패키지에 대 한 활성 할당을 사용 하는 사용자를 정기적으로 검토 해야 합니다. 새 액세스 패키지를 만들거나 기존 액세스 패키지를 편집 하는 경우 리뷰를 사용 하도록 설정할 수 있습니다. 이 문서에서는 액세스 패키지의 액세스 검토를 사용 하도록 설정 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>전제 조건

액세스 패키지의 검토를 사용 하도록 설정 하려면 액세스 패키지를 만들기 위한 필수 구성 요소를 충족 해야 합니다.
- Azure AD Premium P2
- 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.


## <a name="create-an-access-review-of-an-access-package"></a>액세스 패키지에 대 한 액세스 검토 만들기

[새 액세스 패키지를 만들거나](entitlement-management-access-package-create.md) [기존 액세스 패키지 정책을 편집](entitlement-management-access-package-lifecycle-policy.md) 하는 경우 액세스 검토를 사용 하도록 설정할 수 있습니다. 액세스 패키지의 액세스 검토를 사용 하도록 설정 하려면 다음 단계를 따르세요.

1. 액세스 패키지의 **수명 주기** 탭을 열고 **액세스 검토**로 스크롤합니다.

1. **액세스 검토 필요** 를 **예**로 이동 합니다.

    ![액세스 검토 추가](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. 검토가 시작 될 때까지 검토가 시작 되는 날짜를 지정 **합니다.**

1. 그런 다음 **검토 빈도** 를 **매년**, **매년**, **분기별로** 또는 **매월**로 설정 합니다.
이 설정은 액세스 검토를 수행 하는 빈도를 결정 합니다.

1. **기간** 을 설정 하 여 검토자의 입력에 대해 반복 되는 계열의 각 검토가 열리는 일 수를 정의 합니다. 예를 들어 1 월 1 일에 시작 하 여 30 일 동안 검토를 위해 열려 있는 연간 리뷰를 예약할 수 있습니다. 그러면 검토자가 해당 달의 끝까지 응답할 수 있습니다.

1. **검토자**옆에서 검토자를 지정 하려는 경우 사용자가 자신의 액세스 검토를 수행 하거나 **특정 검토자** 를 선택 하도록 하려면 **자체 검토** 를 선택 합니다.

    ![검토자 추가 선택](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. **특정 검토자**를 선택한 경우 액세스 검토를 수행할 사용자를 지정 합니다.
    1. **검토자 추가**를 선택 합니다.
    1. **검토자 선택** 창에서 검토자로 지정할 사용자를 검색 하 여 선택 합니다.
    1. 검토자를 선택한 경우 **선택** 단추를 클릭 합니다.

    ![검토자 지정](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. 페이지 맨 아래에 있는 액세스 패키지를 편집 하는 경우 새 액세스 패키지를 만들거나 **업데이트** 하는 경우 **검토 + 만들기** 를 클릭 합니다.

## <a name="view-the-status-of-the-access-review"></a>액세스 검토의 상태 보기

시작 날짜 **이후에 액세스 검토 섹션에** 액세스 검토가 나열 됩니다. 액세스 검토의 상태를 확인 하려면 다음 단계를 수행 합니다.

1. **Id 거 버 넌 스**에서 **액세스 패키지** 를 클릭 한 다음 확인 하려는 액세스 검토 상태를 포함 하는 액세스 패키지를 선택 합니다.   

1. 액세스 패키지 개요를 클릭 한 후 왼쪽 메뉴에서 **액세스 검토** 를 클릭 합니다.
    
    ![액세스 검토 선택](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. 액세스 검토가 연결 된 모든 정책이 포함 된 목록이 표시 됩니다. 검토를 클릭 하 여 해당 보고서를 확인 합니다.

    ![액세스 검토 목록](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. 보고서를 볼 때 검토 한 사용자 수와 검토자가 수행한 작업을 표시 합니다.

    ![검토 상태 보기](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>액세스 검토 메일 알림
검토자를 지정 하거나 사용자가 자신의 액세스를 검토할 수 있습니다. 기본적으로 Azure AD는 검토를 시작 하 고 나 서 곧바로 검토자 또는 자체 검토자에 게 전자 메일을 보냅니다.

전자 메일에 액세스 하 여 패키지 액세스를 검토 하는 방법에 대 한 지침이 포함 됩니다. 사용자가 액세스를 검토 하는 경우 해당 액세스 패키지에 대 한 자체 검토를 수행 하는 방법에 대 한 지침을 표시 합니다.
  
게스트 사용자를 검토자로 할당 했 고 Azure AD 게스트 초대를 수락 하지 않은 경우 Azure AD 액세스 검토에서 전자 메일을 받지 못합니다. 먼저 초대를 수락 하 고 Azure AD를 사용 하 여 계정을 만들어 전자 메일을 받을 수 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 패키지 액세스 검토](entitlement-management-access-reviews-review-access.md)
- [액세스 패키지 자체 검토](entitlement-management-access-reviews-self-review.md)
