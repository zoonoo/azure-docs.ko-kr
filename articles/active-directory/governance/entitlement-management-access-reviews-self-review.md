---
title: Azure AD 자격 관리에서 액세스 패키지의 자체 검토
description: Azure Active Directory 액세스 검토 (미리 보기)에서 자격 관리 액세스 패키지의 사용자 액세스를 검토 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798446"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지의 자체 검토

Azure AD 자격 관리는 기업에서 그룹, 응용 프로그램 및 SharePoint 사이트에 대 한 액세스를 관리 하는 방법을 간소화 합니다. 이 문서에서는 사용자가 할당 된 액세스 패키지에 대 한 자체 검토를 수행 하는 방법을 설명 합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행 하려면 먼저 액세스 검토를 열어야 합니다. 다음 절차를 사용 하 여 액세스 검토를 찾아 엽니다.

1. Microsoft에서 액세스를 검토 하도록 요청 하는 전자 메일을 받을 수 있습니다. 액세스 검토를 열려면 전자 메일을 찾습니다. 액세스 검토를 요청 하는 전자 메일의 예는 다음과 같습니다. 
    
    ![액세스 검토 자체 검토자 전자 메일](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. **액세스 검토** 링크를 클릭 합니다.

1. https://myaccess.microsoft.com전자 메일을 받지 못한 경우로 직접 이동 하 여 보류 중인 액세스 검토를 찾을 수도 있습니다.  (미국 정부의 경우 대신를 사용 `https://myaccess.microsoft.us` 합니다.)

1. 왼쪽 탐색 모음에서 **액세스 검토** 를 클릭 하 여 사용자에 게 할당 된 보류 중인 액세스 검토 목록을 표시 합니다.


1.  시작 하려는 검토를 클릭 합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 액세스를 볼 수 있습니다. 액세스 검토를 수행 하려면 다음 절차를 따르십시오.

1.  액세스 패키지에 계속 액세스 해야 하는지 여부를 결정 합니다. 예를 들어 작업 중인 프로젝트가 완료 되지 않았기 때문에 프로젝트에서 계속 작업 하려면 액세스 권한이 필요 합니다.

1.  액세스를 유지 하려면 **예** 를 클릭 하 고, 액세스를 제거 하려면 **아니요** 를 클릭 합니다.
    >[!NOTE]
    >더 이상 액세스 하지 않아도 되는 경우 액세스 패키지에서 즉시 제거 되지 않습니다. 검토가 종료 되거나 관리자가 검토를 중지 하면 액세스 패키지에서 제거 됩니다.

1.  **예**를 클릭 한 경우 **이유** 상자에 근거 문을 포함 해야 할 수 있습니다.

1.  **제출**을 클릭합니다.

검토를 완료 하기 전에이 점을 변경 하 고 응답을 변경 하도록 결정 하는 경우 검토로 돌아갈 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스를 검토 하 여 패키지 액세스](entitlement-management-access-reviews-review-access.md) 
