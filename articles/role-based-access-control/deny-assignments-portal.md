---
title: Azure 포털을 사용할 수 있는 Azure 리소스에 대한 할당 거부 목록
description: Azure 포털을 사용하여 특정 범위에서 특정 Azure 리소스 작업에 대한 액세스가 거부된 사용자, 그룹, 서비스 주체 및 관리되는 ID를 나열하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137434"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 리소스에 대한 할당 거부 목록

[할당 거부는](deny-assignments.md) 역할 할당이 액세스 권한을 부여하는 경우에도 사용자가 특정 Azure 리소스 작업을 수행하지 못하도록 차단합니다. 이 문서에서는 Azure 포털을 사용하여 거부 할당을 나열하는 방법을 설명합니다.

> [!NOTE]
> 거부 할당을 직접 만들 수는 없습니다. 할당 거부를 생성하는 방법에 대한 자세한 내용은 [할당 거부를](deny-assignments.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

거부 할당에 대한 정보를 얻으려면 다음이 있어야 합니다.

- `Microsoft.Authorization/denyAssignments/read`권한은 [Azure 리소스에 대한](built-in-roles.md)대부분의 기본 제공 역할에 포함됩니다.

## <a name="list-deny-assignments"></a>거부 할당 목록

다음 단계에 따라 구독 또는 관리 그룹 범위에서 거부 할당을 나열합니다.

1. Azure Portal에서 **모든 서비스**를 클릭한 후에 **관리 그룹** 또는 **구독**을 클릭합니다.

1. 나열할 관리 그룹 또는 구독을 클릭합니다.

1. **액세스 제어(IAM)를 클릭합니다.**

1. **거부 할당** 탭을 클릭하거나 거부 할당 보기 타일의 **보기** 단추를 클릭합니다.

    이 범위에 거부 할당이 있거나 이 범위에 상속된 거부 할당이 있으면 나열됩니다.

    ![액세스 제어 - 거부 할당 탭](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. 추가 열을 표시하려면 **열 편집**을 클릭합니다.

    ![거부 할당 - 열](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **이름** | 거부 할당의 이름입니다. |
    | **주체 유형** | 사용자, 그룹, 시스템 정의 그룹 또는 서비스 주체입니다. |
    | **거부됨**  | 거부 할당에 포함된 보안 주체의 이름입니다. |
    | **Id** | 거부 할당의 고유 식별자입니다. |
    | **제외된 보안 주체** | 거부 할당에서 제외된 보안 주체가 있는지 여부입니다. |
    | **자식에 적용되지 않음** | 거부 할당이 하위 범위에 상속되는지 여부입니다. |
    | **시스템 보호됨** | 거부 할당이 Azure에서 관리되는지 여부입니다. 현재, 항상 예입니다. |
    | **Scope** | 관리 그룹, 구독, 리소스 그룹 또는 리소스입니다. |

1. 사용 가능한 항목에 확인 표시를 추가한 후 **확인**을 클릭하여 선택한 열을 표시합니다.

## <a name="list-details-about-a-deny-assignment"></a>거부 할당에 대한 세부 정보 나열

다음 단계에 따라 거부 할당에 대한 추가 세부 정보를 나열합니다.

1. 이전 섹션에 설명한 대로 **거부 할당** 창을 엽니다.

1. 거부 할당 이름을 클릭하여 **사용자** 블레이드를 엽니다.

    ![거부 할당 - 사용자](./media/deny-assignments-portal/deny-assignment-users.png)

    **사용자** 블레이드에는 다음 두 개의 섹션이 있습니다.

    |  |  |
    | --- | --- |
    | **거부 할당 적용 대상**  | 거부 할당이 적용되는 보안 주체입니다. |
    | **거부 할당 제외 대상** | 거부 할당에서 제외된 보안 주체입니다. |

    **시스템 정의 보안 주체**는 Azure AD 디렉터리의 모든 사용자, 그룹, 서비스 주체 및 관리 ID를 나타냅니다.

1. 거부된 권한 목록을 보려면 **거부된 사용 권한**을 클릭합니다.

    ![거부 할당 - 거부된 사용 권한](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | 동작 유형 | 설명 |
    | --- | --- |
    | **작업**  | 거부된 관리 작업입니다. |
    | **작업 아님** | 거부된 관리 작업에서 제외되는 관리 작업입니다. |
    | **데이터 작업**  | 거부된 데이터 작업입니다. |
    | **NotDataActions** | 거부된 데이터 작업에서 제외되는 데이터 작업입니다. |

    이전 스크린샷에 나온 예의 경우 다음이 유효 권한입니다.

    - 컴퓨팅 작업을 제외하고 데이터 평면에 있는 모든 스토리지 작업이 거부됩니다.

1. 거부 할당에 대한 속성을 보려면 **속성**을 클릭합니다.

    ![거부 할당 - 속성](./media/deny-assignments-portal/deny-assignment-properties.png)

    **속성** 블레이드에서 거부 할당 이름, ID, 설명 및 범위를 볼 수 있습니다. **자식에 적용되지 않음** 스위치는 거부 할당이 하위 범위에 상속되는지 여부를 나타냅니다. **시스템 보호됨** 스위치는 이 거부 할당이 Azure에서 관리되는지 여부를 나타냅니다. 현재, 모든 경우에 대해 **예**입니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스에 대한 거부 할당 이해](deny-assignments.md)
* [Azure PowerShell을 사용하여 Azure 리소스에 대한 할당 거부 목록](deny-assignments-powershell.md)
