---
title: Azure AD 자격 관리 (미리 보기)에서 액세스 패키지에 대 한 요청 보기 및 관리-Azure Active Directory
description: Azure Active Directory 자격 관리 (미리 보기)에서 액세스 패키지에 대 한 요청을 확인, 다시 처리 및 취소 하는 방법에 대해 알아봅니다.
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430288"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 액세스 패키지에 대 한 요청 보기 및 관리

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure AD 자격 관리에서 액세스 패키지, 정책 및 상태를 요청한 사용자를 확인할 수 있습니다. 이 문서에서는 액세스 패키지에 대 한 요청을 확인, 다시 처리 및 취소 하는 방법을 설명 합니다.

## <a name="view-requests"></a>요청 보기

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 특정 요청을 클릭 하면 추가 세부 정보를 볼 수 있습니다.

    ![액세스 패키지에 대 한 요청 목록](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>요청의 배달 오류 보기

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 보려는 요청을 선택 합니다.

    요청에 배달 오류가 있으면 요청 **상태가 배달 되지 않거나** **부분적으로 전달**됩니다.

    배달 오류가 있으면 요청 세부 정보 창에 배달 오류가 발생 한 것입니다.

1. 개수를 클릭 하면 요청의 모든 배달 오류가 표시 됩니다.

## <a name="reprocess-a-request"></a>요청 다시 처리

요청에 오류가 발생 하는 경우 요청을 다시 처리 하 여 다시 시도할 수 있습니다. **배달 실패** 또는 **부분적으로 배달** 된 상태와 1 주일 미만의 완료 된 날짜를 포함 하는 요청을 다시 처리할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 다시 처리 하려는 요청을 클릭 합니다.

1. 요청 정보 창에서 **요청 다시 처리**를 클릭 합니다.

    ![실패 한 요청 다시 처리](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

아직 배달 되지 않았거나 배달이 실패 한 보류 중인 요청만 취소할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 취소 하려는 요청을 클릭 합니다.

1. 요청 정보 창에서 **요청 취소**를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 변경 요청 및 승인 설정](entitlement-management-access-package-request-policy.md)
- [액세스 패키지에 대 한 할당 보기, 추가 및 제거](entitlement-management-access-package-assignments.md)