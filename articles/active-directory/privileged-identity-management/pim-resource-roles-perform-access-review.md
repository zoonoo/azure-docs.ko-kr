---
title: PIM에서 Azure 리소스 역할에 대한 액세스 검토 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할의 액세스를 검토하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743680"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대한 액세스 검토

PIM(Privileged Identity Management) 액세스 검토는 Azure AD(Azure Active Directory)에서 권한 있는 역할에 대한 안전한 액세스에 도움이 됩니다. 이 문서에서는 Azure AD 액세스 검토에서 권한 있는 역할 할당 검토를 완료하는 단계를 설명합니다.

관리자 역할에 할당된 경우 역할에 대한 요구 사항을 확인하기 위해 관리자에 의한 액세스 검토를 완료해야 할 수 있습니다. 확인 요청은 링크를 포함하는 메일로 받거나 [Azure Portal](https://portal.azure.com)에서 확인할 수 있습니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자인 경우 [액세스 검토를 시작하는 방법](pim-resource-roles-start-access-review.md)에서 자세한 내용을 참조하세요.

## <a name="approve-or-deny-access"></a>액세스 승인 또는 거부

해당 역할을 사용하는지 여부에 따라 액세스를 승인하거나 거부할 수 있습니다. 역할을 유지하려면 **승인** 을, 또는 더 이상 액세스를 필요로 하지 않는 경우 **거부** 를 선택합니다. 검토자가 결과를 적용한 후에만 상태가 변경됩니다.

액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 를 선택하고 **Privileged Identity Management** 를 엽니다.
1. **액세스 검토** 를 선택합니다.

   ![액세스 검토 블레이드가 선택된 Privileged Identity Management 애플리케이션의 스크린샷](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 완료할 검토를 선택합니다.
1. **승인** 또는 **거부** 를 선택합니다. 필요한 경우 **이유 제공 상자** 에 결정에 대한 비즈니스 타당성을 입력합니다.

   ![세부 정보 검토 페이지의 스크린샷](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할에 대한 액세스 검토 수행](pim-how-to-perform-security-review.md)
