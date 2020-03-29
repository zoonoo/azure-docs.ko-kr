---
title: Azure AD 권한 관리에서 액세스 패키지의 액세스 검토
description: Azure Active Directory 액세스 검토(미리 보기)에서 권한 부여 관리 액세스 패키지에 대한 액세스 검토를 완료하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968738"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지의 액세스 검토

Azure AD 권한 관리에서는 기업이 그룹, 응용 프로그램 및 SharePoint 사이트에 대한 액세스를 관리하는 방법을 간소화합니다. 이 문서에서는 지정된 검토자로 액세스 패키지에 할당된 다른 사용자에 대한 액세스 검토를 수행하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

사용자의 활성 액세스 패키지 할당을 검토하려면 액세스 검토를 수행하려면 필수 구성 조건을 충족해야 합니다.
- Azure AD Premium P2
- 전역 관리자
- 지정된 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.


## <a name="open-the-access-review"></a>액세스 검토 열기

다음 단계를 사용하여 액세스 검토를 찾아 엽니다.

1. Microsoft에서 액세스 검토를 요청하는 전자 메일을 받을 수 있습니다. 전자 메일을 찾아 액세스 검토를 엽니다. 다음은 액세스를 검토하는 예제 이메일입니다.
    
    ![액세스 검토자 이메일](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. 사용자 **액세스** 검토 링크를 클릭하여 액세스 검토를 엽니다. 

1. 이메일이 없는 경우 로 직접 이동하여 보류 중인 액세스 리뷰를 https://myaccess.microsoft.com찾을 수 있습니다.  (미국 정부의 경우 `https://myaccess.microsoft.us` 대신 사용하십시오.)

1. 왼쪽 탐색 모음에서 **검토 액세스를** 클릭하여 사용자에게 할당된 보류 중인 액세스 검토 목록을 확인합니다.
    
    ![내 액세스에서 액세스 리뷰 선택](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 시작하려는 리뷰를 클릭합니다.
    
    ![액세스 검토 선택](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 검토해야 하는 사용자의 이름이 표시됩니다. 액세스를 승인하거나 거부할 수 있는 방법에는 두 가지가 있습니다.
- 하나 이상의 사용자에 대한 액세스를 수동으로 승인하거나 거부할 수 있습니다.
- 시스템 권장 사항을 수락할 수 있습니다.

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>하나 이상의 사용자에 대한 액세스를 수동으로 승인 또는 거부
1. 사용자 목록을 검토하고 계속 액세스하려면 필요한 사용자를 결정합니다.

    ![검토할 사용자 목록](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 액세스를 승인하거나 거부하려면 사용자 이름 왼쪽에 있는 라디오 단추를 선택합니다.

1. 사용자 이름 위의 막대에서 **승인** 또는 **거부를** 선택합니다.

    ![사용자 선택](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 확실하지 않은 경우 **모르는 단추를** 클릭할 수 있습니다.

    이 옵션을 선택하면 사용자는 액세스를 유지하며 이 선택은 감사 로그에 들어갑니다. 로그에검토를 완료한 다른 검토자가 표시됩니다.

1. 귀하는 귀하의 결정에 대한 이유를 제공해야 할 수도 있습니다. 이유를 입력하고 **제출을**클릭합니다.

    ![액세스 승인 또는 거부](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 검토가 끝나기 전에 언제든지 결정을 변경할 수 있습니다. 이렇게 하려면 목록에서 사용자를 선택하고 결정을 변경합니다. 예를 들어 이전에 거부된 사용자에 대한 액세스를 승인할 수 있습니다.

검토자가 여러 개인 경우 마지막으로 제출된 응답이 기록됩니다. 관리자가 Alice와 Bob라는 두 명의 검토자를 지정하는 예를 생각해 보십시오. Alice는 먼저 검토를 열고 액세스를 승인합니다. 검토가 끝나기 전에 Bob은 검토를 열고 액세스를 거부합니다. 이 경우 마지막 거부 액세스 결정이 기록됩니다.

>[!NOTE]
>사용자가 액세스가 거부되면 액세스 패키지에서 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 종료하면 액세스 패키지에서 사용자가 제거됩니다.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>시스템에서 생성된 권장 사항을 사용하여 액세스 승인 또는 거부

여러 사용자의 액세스를 보다 빠르게 검토하려면 한 번의 클릭으로 권장 사항을 수락하는 시스템 생성 권장 사항을 사용할 수 있습니다. 권장 사항은 사용자의 로그인 활동에 따라 생성됩니다.

1.  페이지 상단의 막대에서 **권장 사항 수락을**클릭합니다.
    
    ![권장 사항 수락 선택](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    권장 작업의 요약이 표시됩니다.

1.  권장 사항을 수락하려면 **제출을** 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지 자체 검토](entitlement-management-access-reviews-self-review.md)