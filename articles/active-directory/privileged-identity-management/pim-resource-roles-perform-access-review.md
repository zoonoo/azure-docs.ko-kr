---
title: PIM - Azure AD에서 Azure 리소스 역할에 대한 액세스 검토 | 마이크로 소프트 문서
description: PIM(Azure AD 권한 ID 관리)에서 Azure 리소스 역할의 액세스를 검토하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847011"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure 리소스 역할에 대한 액세스 검토

PIM(권한 있는 ID 관리) 액세스 검토는 Azure Active Directory(Azure AD)에서 권한 있는 역할에 대한 액세스를 보호하는 데 도움이 될 수 있습니다. 이 문서에서는 Azure AD 액세스 검토에서 권한 있는 역할 할당 검토를 완료하는 단계를 수행합니다.

관리 역할에 할당된 경우 역할에 대한 필요성을 확인하기 위해 관리자가 액세스 검토를 완료해야 할 수 있습니다. 확인 요청은 링크가 포함된 전자 메일을 제공하거나 Azure [포털에서](https://portal.azure.com)확인할 수 있습니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자인 경우 [액세스 검토를 시작하는 방법](pim-resource-roles-start-access-review.md)에서 자세한 내용을 참조하세요.

## <a name="approve-or-deny-access"></a>액세스 승인 또는 거부

이 역할을 여전히 사용했는지 여부에 따라 액세스를 승인하거나 거부할 수 있습니다. 역할을 유지하려면 **승인**을, 또는 더 이상 액세스를 필요로 하지 않는 경우 **거부**를 선택합니다. 검토자가 결과를 적용한 후에만 상태가 변경됩니다.

액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
1. **Azure Active 디렉터리** 및 **권한 있는 ID 관리를**엽니다.
1. **액세스 검토를**선택합니다.

   ![검토 액세스 블레이드를 선택한 권한 있는 ID 관리 응용 프로그램의 스크린샷](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 완료할 검토를 선택합니다.
1. **승인** 또는 **거부를**선택합니다. 이유 **제공 상자에서**필요한 경우 의사 결정에 대한 비즈니스 근거를 입력합니다.

   ![세부 정보 검토 페이지의 스크린샷](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할에 대한 액세스 검토를 수행합니다.](pim-how-to-perform-security-review.md)
