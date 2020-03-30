---
title: PIM - Azure AD에서 Azure AD 역할에 대한 액세스 검토 만들기 | 마이크로 소프트 문서
description: PIM(Azure AD 권한 ID 관리)에서 Azure AD 역할에 대한 액세스 검토를 만드는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 2f7e9ef503a9a3469ecbc835be8d9229fbd0167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847105"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할에 대한 액세스 검토 만들기

부실 역할 할당과 관련된 위험을 줄이려면 정기적으로 액세스를 검토해야 합니다. PIM(Azure AD 권한 ID 관리)을 사용하여 권한 있는 Azure AD 역할에 대한 액세스 검토를 만들 수 있습니다. 자동으로 발생하는 되풀이 액세스 검토를 구성할 수도 있습니다.

이 문서에서는 권한 있는 Azure AD 역할에 대해 하나 이상의 액세스 검토를 만드는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

[권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>오픈 액세스 리뷰

1. 권한 있는 역할 관리자 역할의 구성원인 사용자와 [Azure 포털에](https://portal.azure.com/) 로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. 왼쪽 메뉴에서 **Azure AD 역할을** 선택한 다음 검토 에 대한 액세스를 **선택합니다.**

1. 관리에서 **검토 에 대한 액세스 검토를 선택합니다.**

    ![Azure AD 역할 - 모든 리뷰의 상태를 보여주는 액세스 검토 목록](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>액세스 검토 시작

액세스 검토에 대한 설정을 지정한 후 **에서 시작을**선택합니다. 액세스 검토는 해당 상태의 표시기와 함께 목록에 표시됩니다.

![시작된 리뷰의 상태를 보여주는 액세스 리뷰 목록](./media/pim-how-to-start-security-review/access-reviews-list.png)

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. Azure AD 역할에 대한 [액세스를 검토하는](pim-how-to-perform-security-review.md)방법에 대한 지침을 표시할 수 있습니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리

검토자가 액세스 검토의 **개요** 페이지에서 검토를 완료할 때 진행 상황을 추적할 수 있습니다. [검토가 완료될](pim-how-to-complete-review.md)때까지 디렉터리에서 액세스 권한이 변경되지 않습니다.

![리뷰의 세부 정보를 보여주는 액세스 리뷰 개요 페이지](./media/pim-how-to-start-security-review/access-review-overview.png)

일회성 검토인 경우 액세스 검토 기간이 끝났거나 관리자가 액세스 검토를 중지한 후 [Azure AD 역할에 대한 액세스 검토 완료](pim-how-to-complete-review.md) 단계를 수행하여 결과를 보고 적용합니다.  

일련의 액세스 검토를 관리하려면 액세스 검토로 이동하여 예약된 리뷰에서 예정된 발생을 찾아종료일을 편집하거나 그에 따라 검토자를 추가/제거합니다.

**완료 설정에서**선택한 항목에 따라 검토 종료일 이후 또는 수동으로 검토를 중지하면 자동 적용이 실행됩니다. 검토 상태는 **완료됨에서** **적용** 과 같은 중간 상태까지 변경되고 마지막으로 **응용**상태입니다. 거부된 사용자가 몇 분 안에 역할에서 제거되는 것으로 예상해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 역할에 대한 액세스 검토](pim-how-to-perform-security-review.md)
- [Azure AD 역할에 대한 액세스 검토 완료](pim-how-to-complete-review.md)
- [Azure 리소스 역할에 대한 액세스 검토 만들기](pim-resource-roles-start-access-review.md)
