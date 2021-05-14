---
title: Azure AD 권한 관리에서 액세스 패키지 자체 검토
description: Azure Active Directory 액세스 검토(미리 보기)에서 권한 관리 액세스 패키지의 사용자 액세스 검토 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798446"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지 자체 검토

Azure AD 권한 관리는 기업의 그룹, 애플리케이션, SharePoint 사이트에 대한 액세스 관리 방법을 간소화합니다. 이 문서에서는 사용자가 할당된 액세스 패키지 자체 검토를 수행하는 방법을 설명합니다.

## <a name="open-the-access-review"></a>액세스 검토 열기

액세스 검토를 수행하려면 먼저 액세스 검토를 열어야 합니다. 다음 절차를 사용하여 액세스 검토를 찾아 엽니다.

1. 액세스 검토를 요청하는 Microsoft의 메일을 받을 수도 있습니다. 액세스 검토를 열려면 메일을 찾아야 합니다. 다음은 액세스 검토를 요청하는 메일의 예입니다. 
    
    ![액세스 검토 자체 검토자 메일](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. **액세스 검토** 링크를 클릭합니다.

1. 메일을 받지 못한 경우 https://myaccess.microsoft.com 으로 직접 이동하여 보류 중인 액세스 검토를 찾을 수도 있습니다.  (미국 정부의 경우 `https://myaccess.microsoft.us`를 사용합니다.)

1. 왼쪽 탐색 모음에서 **액세스 검토** 를 클릭하여 본인에게 할당된 보류 중인 액세스 검토 목록을 표시합니다.


1.  시작할 검토를 클릭합니다.

## <a name="perform-the-access-review"></a>액세스 검토 수행

액세스 검토를 열면 액세스를 볼 수 있습니다. 액세스 검토를 수행하려면 다음 절차를 따르세요.

1.  액세스 패키지에 계속 액세스해야 하는지 여부를 결정합니다. 예를 들어 작업 중인 프로젝트가 완료되지 않은 경우 프로젝트에서 계속 작업하려면 액세스 권한이 필요합니다.

1.  액세스를 유지하려면 **예** 를 선택하고 액세스를 제거하려면 **아니요** 를 선택합니다.
    >[!NOTE]
    >더 이상 액세스할 필요가 없다고 해도 곧바로 액세스 패키지에서 제거되지는 않습니다. 검토가 종료되거나 관리자가 검토를 중지하면 액세스 패키지에서 제거됩니다.

1.  **예** 를 클릭한 경우 **이유** 상자에 근거를 포함해야 할 수 있습니다.

1.  **제출** 을 클릭합니다.

검토를 종료하기 전에 생각이 바뀌어 응답을 변경하려는 경우 검토로 돌아갈 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 액세스 검토](entitlement-management-access-reviews-review-access.md) 
