---
title: Azure Portal을 사용하여 Azure 역할 할당 - Azure RBAC
description: Azure Portal 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹, 서비스 주체 또는 관리 ID의 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 6b159d9ca7d8d7739d623bb3a48752b4d03e24bc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385969"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 이 문서에서는 Azure Portal을 사용하여 역할을 할당하는 방법을 설명합니다.

Azure Active Directory에서 관리자 역할을 할당해야 하는 경우 [Azure Active Directory에서 관리자 역할 보기 및 할당](../active-directory/roles/manage-roles-portal.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>1단계: 필요한 범위 확인

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] 범위에 대한 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

![Azure RBAC의 범위 수준](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽의 검색 상자에서 액세스 권한을 부여할 범위를 검색합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 특정 리소스를 검색합니다.

    ![Azure Portal에서 리소스 그룹 검색](./media/shared/rg-portal-search.png)

1. 해당 범위에 대한 특정 리소스를 클릭합니다.

    다음은 예제 리소스 그룹을 보여줍니다.

    ![리소스 그룹 개요](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>2단계: 역할 할당 추가 창 열기

**액세스 제어(IAM)** 는 Azure 리소스에 대한 액세스 권한을 부여하기 위해 역할을 할당하는 데 일반적으로 사용하는 페이지입니다. IAM(ID 및 액세스 관리)이라고도 하며 Azure Portal의 여러 위치에 표시됩니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

    ![리소스 그룹에 대한 액세스 제어(IAM) 페이지](./media/shared/rg-access-control.png)

1. **역할 할당** 탭을 클릭하여 이 범위에서 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가** 를 클릭합니다.
   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![역할 할당 추가 메뉴](./media/shared/add-role-assignment-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 페이지](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-select-the-appropriate-role"></a>3단계: 적절한 역할 선택

1. **역할** 목록에서 검색하거나 스크롤하여 할당할 역할을 찾습니다.

    적절한 역할을 결정하는 데 도움이 되도록 정보 아이콘 위로 마우스를 가져가 역할에 대한 설명을 표시할 수 있습니다. 추가 정보는 [Azure 기본 제공 역할](built-in-roles.md) 문서를 볼 수 있습니다.

   ![역할 할당 추가에서 역할 선택](./media/role-assignments-portal/add-role-assignment-role.png)

1. 역할을 클릭하여 선택합니다.

## <a name="step-4-select-who-needs-access"></a>4단계: 액세스 권한이 필요한 사용자 선택

1. **액세스 할당 대상** 목록에서 액세스 권한을 할당할 보안 주체 유형을 선택합니다.

    | 형식 | 설명 |
    | --- | --- |
    | **사용자, 그룹 또는 서비스 주체** | 사용자, 그룹 또는 서비스 사용자(애플리케이션)에 역할을 할당하려면 이 유형을 선택합니다. |
    | **사용자가 할당한 관리 ID** | [사용자 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당하려면 이 형식을 선택합니다. |
    | *시스템 할당 관리 ID* | [시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당하려면 관리 ID가 있는 Azure 서비스 인스턴스를 선택합니다. |

   ![역할 할당 추가에서 보안 주체 유형 선택](./media/role-assignments-portal/add-role-assignment-type.png)

1. 사용자 할당 관리 ID 또는 시스템 할당 관리 ID를 선택한 경우 관리 ID가 있는 **구독** 을 선택합니다.

1. **선택** 섹션에서 문자열을 입력하거나 목록을 스크롤하여 보안 주체를 검색합니다.

   ![역할 할당 추가에서 사용자 선택](./media/role-assignments-portal/add-role-assignment-user.png)

1. 보안 주체를 찾았으면 클릭하여 선택합니다.

## <a name="step-5-assign-role"></a>5단계: 역할 할당

1. 역할을 할당하려면 **저장** 을 클릭합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

1. **역할 할당** 탭에서 목록에 역할 할당이 표시되는지 확인합니다.

    ![역할 할당 추가 저장](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>다음 단계

- [Azure 구독의 관리자로 사용자 할당](role-assignments-portal-subscription-admin.md)
- [Azure 역할 할당 제거](role-assignments-remove.md)
- [Azure RBAC 문제 해결](troubleshooting.md)
