---
title: PIM-Azure Active Directory에서에서 Azure 리소스 역할의 액세스 검토 만들기 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure 리소스 역할의 액세스 검토를 만드는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0067bd6dc2f47c5460220295d486910d9195782d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476257"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할의 액세스 검토 만들기

직원에 대 한 액세스 권한이 부여 된 Azure 리소스 역할에는 시간이 지남에 따라 변경 합니다. 오래 된 역할 할당과 관련 된 위험을 줄이기 위해 정기적으로 액세스를 검토 해야 합니다. 권한 있는 Azure 리소스 역할에 대 한 액세스 검토를 만들려면 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)을 사용할 수 있습니다. 또한 자동으로 발생 하는 되풀이 액세스 검토를 구성할 수 있습니다.

이 문서에서는 권한 있는 Azure 리소스 역할에 대 한 하나 이상의 액세스 검토를 만드는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

- [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>개방형 액세스 검토

1. 에 로그인 [Azure portal](https://portal.azure.com/) 권한 있는 역할 관리자 역할의 구성원 인 사용자를 사용 하 여 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **Azure 리소스**합니다.

1. 관리하려는 리소스(예: 구독 또는 관리 그룹)를 클릭합니다.

1. 관리 아래에서 클릭 **액세스 검토**합니다.

    ![Azure 리소스-액세스 검토 모든 검토의 상태를 보여주는 목록](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>액세스 검토 시작

액세스 검토에 대한 설정을 지정했으면 **시작**을 클릭합니다. 액세스 검토는 해당 상태 표시기를 사용 하 여 목록에 표시 됩니다.

![액세스 검토 시작된 검토의 상태를 보여주는 목록](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. 방법에 대 한 지침을 표시할 수 있습니다 [Azure 리소스 역할에 대 한 액세스 검토](pim-resource-roles-perform-access-review.md)합니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리

검토자가 검토를 완료 진행률을 추적할 수는 **개요** 액세스 검토 페이지입니다. 액세스 권한이 없습니다. 디렉터리에 변경 되는 [검토가 완료 되](pim-resource-roles-complete-access-review.md)합니다.

![액세스 검토의 세부 정보를 표시 하는 개요 페이지를 검토](./media/pim-resource-roles-start-access-review/access-review-overview.png)

일회성 검토 인 경우 다음 액세스 검토 기간이 만료 또는 관리자가 액세스 검토를 중지 한 후의 단계에 따라 [Azure 리소스 역할의 액세스 검토 완료](pim-resource-roles-complete-access-review.md) 확인 하 고 결과 적용 합니다.  

일련의 액세스를 관리 하려면 검토, 액세스 검토로 이동 및는 예약 된 검토에 대 한 예정 된 찾습니다와 종료 날짜를 편집 또는 추가/제거 검토자 적절 하 게 합니다.

선택한 항목 기반 **설정 완료 시**, 자동 적용 됩니다가 검토의 종료 날짜 또는 검토를 수동으로 중지 후에 실행 합니다. 검토 상태가 변경 됩니다 **완료 됨** 와 같은 중간 상태 **적용** 마지막 상태로 **적용**합니다. 잠시 후에 역할에서 제거 되 고 있는 경우 거부 된 사용자를 참조 하시면 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스 역할에 대 한 액세스를 검토 합니다.](pim-resource-roles-perform-access-review.md)
- [Azure 리소스 역할의 액세스 검토를 완료 합니다.](pim-resource-roles-complete-access-review.md)
- [Azure AD 역할의 액세스 검토 만들기](pim-how-to-start-security-review.md)
