---
title: PIM에서 Azure AD 역할에 대 한 액세스 검토-Azure AD | Microsoft Docs
description: PIM (Azure AD Privileged Identity Management)의 Azure Active Directory 역할에 대 한 액세스를 검토 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847100"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 액세스 검토

PIM (Privileged Identity Management)은 기업에서 AD (Azure Active Directory) 및 기타 Microsoft 온라인 서비스 (예: Office 365 또는 Microsoft Intune)의 리소스에 대 한 권한 있는 액세스를 관리 하는 방법을 간소화 합니다. 이 문서의 단계에 따라 할당 된 역할을 직접 검토 합니다.

관리 역할에 할당된 경우 조직의 권한 있는 역할 관리자가 작업에 해당 역할이 여전히 필요한지 정기적으로 확인하도록 요청할 수 있습니다. 링크를 포함 하는 전자 메일을 받을 수도 있고, [Azure Portal](https://portal.azure.com) 로 바로 이동 하 여 시작할 수도 있습니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자 또는 전역 관리자인 경우 [액세스 검토를 시작하는 방법](pim-how-to-start-security-review.md)에서 자세한 내용을 참조하세요.

## <a name="add-a-pim-dashboard-tile"></a>PIM 대시보드 타일 추가

Azure AD Privileged Identity Management 서비스를 Azure Portal 대시보드에 고정 하지 않은 경우 다음 단계를 수행 하 여 시작 하세요.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure 포털의 오른쪽 위에서 사용자 이름을 선택하고 작동할 디렉터리를 선택합니다.
3. **모든 서비스**를 선택하고 필터 텍스트 상자를 사용하여 **Azure AD Privileged Identity Management**를 검색합니다.
4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 애플리케이션이 열립니다.

## <a name="approve-or-deny-access"></a>액세스 승인 또는 거부

액세스를 승인하거나 거부할 때, 단지 검토자에게 이 역할을 사용할지를 알리는 것입니다. 역할을 유지하려면 **승인**을, 또는 더 이상 액세스를 필요로 하지 않는 경우 **거부**를 선택합니다. 검토자가 결과를 적용할 때까지는 사용자의 상태가 바로 변경되지 않습니다.
액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. Privileged Identity Management 서비스에서 **권한 있는 액세스 검토**를 선택 합니다. 보류 중인 액세스 검토가 있으면 Azure AD **액세스 검토** 페이지에 표시 됩니다.
2. 완료할 검토를 선택합니다.
3. 검토를 만들지 않은 한, 검토에서 유일한 사용자로 나타납니다. 사용자 이름 옆에 있는 확인 표시를 선택합니다.
4. **승인** 또는 **거부**를 선택합니다. **이유 설명** 텍스트 상자에 결정 이유를 포함해야 합니다.  
5. **Review Azure AD roles** (Azure AD 역할 검토)를 닫습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 액세스 검토 수행](pim-resource-roles-perform-access-review.md)
