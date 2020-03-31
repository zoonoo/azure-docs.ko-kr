---
title: Azure AD 권한 관리에서 액세스 패키지 자체 검토
description: Azure Active Directory 액세스 검토(미리 보기)에서 권한 부여 관리 액세스 패키지에 대한 사용자 액세스를 검토하는 방법을 알아봅니다.
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
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967767"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지 자체 검토

Azure AD 권한 관리에서는 기업이 그룹, 응용 프로그램 및 SharePoint 사이트에 대한 액세스를 관리하는 방법을 간소화합니다. 이 문서에서는 사용자가 할당된 액세스 패키지를 자체 검토하는 방법을 설명합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행하려면 먼저 액세스 검토를 열어야 합니다. 다음 절차를 사용하여 액세스 검토를 찾아 엽니다.

1. Microsoft에서 액세스 검토를 요청하는 전자 메일을 받을 수 있습니다. 전자 메일을 찾아 액세스 검토를 엽니다. 다음은 액세스 검토를 요청하는 전자 메일의 예입니다. 
    
    ![액세스 검토 자 이메일](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. 액세스 **검토** 링크를 클릭합니다.

1. 이메일을 받지 못한 https://myaccess.microsoft.com 경우 대기 중인 액세스 리뷰를 찾기 위해 직접 이동하여 확인할 수도 있습니다.  (미국 정부의 경우 `https://myaccess.microsoft.us` 대신 사용하십시오.)

1. 왼쪽 탐색 모음에서 **검토 액세스를** 클릭하여 사용자에게 할당된 보류 중인 액세스 검토 목록을 확인합니다.


1.  시작하려는 리뷰를 클릭합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 액세스 권한을 볼 수 있습니다. 다음 절차를 사용하여 액세스 검토를 수행합니다.

1.  액세스 패키지에 대한 액세스가 필요한지 여부를 결정합니다. 예를 들어 작업 중인 프로젝트가 완료되지 않으므로 프로젝트 작업을 계속하려면 액세스 권한이 필요합니다.

1.  **아니요를** 클릭하여 액세스를 유지하거나 **아니요를** 클릭하여 액세스를 제거합니다.
    >[!NOTE]
    >더 이상 액세스가 필요하지 않다고 언급된 경우 액세스 패키지에서 즉시 제거되지 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 액세스 패키지에서 제거됩니다.

1.  **예를**클릭한 경우 **이유** 상자에 자리 맞추기 문을 포함해야 할 수 있습니다.

1.  **제출**을 클릭합니다.

마음이 바뀌고 검토가 끝나기 전에 응답을 변경하기로 결정한 경우 리뷰로 돌아갈 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [패키지 액세스 에 대한 액세스 검토](entitlement-management-access-reviews-review-access.md) 
