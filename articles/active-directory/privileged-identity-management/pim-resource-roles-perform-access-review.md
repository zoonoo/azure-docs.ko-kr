---
title: PIM에서 Azure 리소스 역할에 대 한 액세스 검토-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure 리소스 역할에 대 한 액세스를 검토 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847011"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대 한 액세스 검토

PIM (Privileged Identity Management) 액세스 검토는 Azure Active Directory (Azure AD)에서 권한 있는 역할에 대 한 액세스를 보호 하는 데 도움이 됩니다. 이 문서에서는 Azure AD 액세스 검토에서 권한 있는 역할 할당 검토를 완료 하는 단계를 설명 합니다.

관리 역할에 할당 된 경우 관리자에 게 액세스 검토를 완료 하 여 역할에 대 한 요구 사항을 확인 해야 할 수 있습니다. 확인 요청은 링크를 포함 하는 전자 메일을 보내거나 [Azure Portal](https://portal.azure.com)를 확인할 수 있습니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자인 경우 [액세스 검토를 시작하는 방법](pim-resource-roles-start-access-review.md)에서 자세한 내용을 참조하세요.

## <a name="approve-or-deny-access"></a>액세스 승인 또는 거부

이 역할을 사용 하는지 여부에 따라 액세스를 승인 하거나 거부할 수 있습니다. 역할을 유지하려면 **승인**을, 또는 더 이상 액세스를 필요로 하지 않는 경우 **거부**를 선택합니다. 검토자가 결과를 적용 한 후에만 상태가 변경 됩니다.

액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 를 선택 하 고 **Privileged Identity Management**를 엽니다.
1. **액세스 검토**를 선택 합니다.

   ![액세스 검토 블레이드가 선택 된 Privileged Identity Management 응용 프로그램의 스크린샷](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 완료할 검토를 선택합니다.
1. **승인** 또는 **거부**를 선택 합니다. **이유 제공 상자**에 필요한 경우 결정에 대 한 비즈니스 근거를 입력 합니다.

   ![세부 정보 검토 페이지의 스크린샷](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 내 Azure AD 역할에 대 한 액세스 검토를 수행 합니다.](pim-how-to-perform-security-review.md)
