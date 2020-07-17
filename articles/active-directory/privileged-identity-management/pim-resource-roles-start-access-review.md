---
title: PIM에서 Azure 리소스 역할에 대 한 액세스 검토 만들기-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure 리소스 역할에 대 한 액세스 검토를 만드는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
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
ms.openlocfilehash: fd9e3aaf0747e9b28526a43d9f734084f8a75662
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743697"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대 한 액세스 검토 만들기

직원에 대 한 권한 있는 Azure 리소스 역할에 대 한 액세스는 시간이 지남에 따라 변경 됩니다. 부실 역할 할당과 관련 된 위험을 줄이려면 액세스를 정기적으로 검토 해야 합니다. Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 하 여 권한 있는 Azure 리소스 역할에 대 한 액세스 검토를 만들 수 있습니다. 자동으로 발생 하는 되풀이 액세스 검토를 구성할 수도 있습니다.

이 문서에서는 권한 있는 Azure 리소스 역할에 대 한 하나 이상의 액세스 검토를 만드는 방법을 설명 합니다.

## <a name="prerequisites"></a>전제 조건

[권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>액세스 검토 열기

1. 권한 있는 역할 관리자 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. 왼쪽 메뉴에서 **Azure 리소스**를 선택 합니다.

1. 관리 하려는 리소스 (예: 구독 또는 관리 그룹)를 선택 합니다.

1. 관리에서 **액세스 검토**를 선택 합니다.

    ![Azure 리소스-모든 리뷰의 상태를 보여 주는 액세스 검토 목록](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>액세스 검토 시작

액세스 검토에 대한 설정을 지정했으면 **시작**을 클릭합니다. 액세스 검토는 상태 표시기를 사용 하 여 목록에 표시 됩니다.

![시작 된 검토의 상태를 보여 주는 액세스 검토 목록](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

검토가 시작되면 Azure AD에서 기본적으로 검토자에게 전자 메일을 보냅니다. Azure AD에서 전자 메일을 보내지 않도록 선택한 경우 검토자에게 완료할 때까지 대기 중인 액세스 검토가 있음을 알려야 합니다. [Azure 리소스 역할에](pim-resource-roles-perform-access-review.md)대 한 액세스를 검토 하는 방법에 대 한 지침을 표시할 수 있습니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리

검토자가 액세스 검토의 **개요** 페이지에서 검토를 완료할 때 진행률을 추적할 수 있습니다. [검토가 완료](pim-resource-roles-complete-access-review.md)될 때까지 디렉터리에서 액세스 권한이 변경 되지 않습니다.

![검토 세부 정보를 표시 하는 액세스 검토 개요 페이지](./media/pim-resource-roles-start-access-review/access-review-overview.png)

일회성 검토 인 경우 액세스 검토 기간이 끝난 후 또는 관리자가 액세스 검토를 중지 한 후 [Azure 리소스 역할의 액세스 검토 완료](pim-resource-roles-complete-access-review.md) 의 단계에 따라 결과를 확인 하 고 적용 합니다.  

일련의 액세스 검토를 관리 하려면 액세스 검토로 이동 하 고 예정 된 검토에서 예정 된 항목을 찾아 종료 날짜를 편집 하거나 그에 따라 검토자를 추가/제거 합니다.

**완료 설정**후의 선택에 따라 자동 적용은 검토의 종료 날짜 이후에 또는 수동으로 검토를 중지 한 후 실행 됩니다. 검토 상태는 **완료 됨** 에서 **적용** 하는 등의 중간 상태와 마지막으로 **적용**된 상태 사이에서 변경 됩니다. 몇 분 안에 거부 된 사용자 (있는 경우)가 제거 되는 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스 역할에 대 한 액세스 검토](pim-resource-roles-perform-access-review.md)
- [Azure 리소스 역할에 대 한 액세스 검토 완료](pim-resource-roles-complete-access-review.md)
- [Azure AD 역할에 대 한 액세스 검토 만들기](pim-how-to-start-security-review.md)
