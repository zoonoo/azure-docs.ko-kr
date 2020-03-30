---
title: 권한 부여 관리에서 새 액세스 패키지 만들기 - Azure AD
description: Azure Active Directory 권한 관리에서 공유하려는 새 리소스 패키지를 만드는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262010"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 새 액세스 패키지 만들기

액세스 패키지를 사용하면 액세스 패키지의 수명 동안 액세스를 자동으로 관리하는 리소스 및 정책을 일회성으로 설정할 수 있습니다. 이 문서에서는 새 액세스 패키지를 만드는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

모든 액세스 패키지는 카탈로그라는 컨테이너에 넣어야 합니다. 카탈로그는 액세스 패키지에 추가할 수 있는 리소스를 정의합니다. 카탈로그를 지정하지 않으면 액세스 패키지가 일반 카탈로그에 저장됩니다. 현재 기존 액세스 패키지를 다른 카탈로그로 이동할 수 없습니다.

액세스 패키지 관리자인 경우 소유한 리소스를 카탈로그에 추가할 수 없습니다. 카탈로그에서 사용할 수 있는 리소스를 사용할 수 있습니다. 카탈로그에 리소스를 추가해야 하는 경우 카탈로그 소유자에게 문의할 수 있습니다.

모든 액세스 패키지에는 하나 이상의 정책이 있어야 합니다. 정책은 액세스 패키지를 요청할 수 있는 사용자와 승인 및 수명 주기 설정을 지정합니다. 새 액세스 패키지를 만들 때 디렉터리, 디렉터리에 없는 사용자, 관리자 직접 할당에 대해서만 사용자에 대한 초기 정책을 만들거나 나중에 정책을 만들도록 선택할 수 있습니다.

![액세스 패키지 만들기](./media/entitlement-management-access-package-create/access-package-create.png)

다음은 새 액세스 패키지를 만드는 상위 수준 단계입니다.

1. ID 거버넌스에서 프로세스를 시작하여 새 액세스 패키지를 만듭니다.

1. 액세스 패키지를 만들 카탈로그를 선택합니다.

1. 카탈로그에서 액세스 패키지에 리소스를 추가합니다.

1. 각 리소스에 대한 리소스 역할을 할당합니다.

1. 액세스를 요청할 수 있는 사용자를 지정합니다.

1. 승인 설정을 지정합니다.

1. 수명 주기 설정을 지정합니다.

## <a name="start-new-access-package"></a>새 액세스 패키지 시작

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지**를 클릭합니다.

1. **새 액세스 패키지**를 클릭합니다.
   
    ![Azure Portal에서 권한 관리](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>기본 사항

**기본** 탭에서 액세스 패키지에 이름을 지정하고 액세스 패키지를 만들 카탈로그를 지정합니다.

1. 액세스 패키지에 대한 표시 이름과 설명을 입력합니다. 액세스 패키지에 대한 요청을 제출할 때 이 정보가 표시됩니다.

1. **카탈로그** 드롭다운 목록에서 액세스 패키지를 만들 카탈로그를 선택합니다. 예를 들어 요청할 수 있는 모든 마케팅 리소스를 관리하는 카탈로그 소유자가 있을 수 있습니다. 이 경우 마케팅 카탈로그를 선택할 수 있습니다.

    액세스 패키지를 만들 수 있는 권한이 있는 카탈로그만 표시됩니다. 기존 카탈로그에서 액세스 패키지를 만들려면 글로벌 관리자 또는 사용자 관리자여야 하거나 해당 카탈로그의 카탈로그 소유자 또는 액세스 패키지 관리자여야 합니다.

    ![액세스 패키지 - 기본 사항](./media/entitlement-management-access-package-create/basics.png)

    글로벌 관리자, 사용자 관리자 또는 카탈로그 작성자인 경우 나열되지 않은 새 카탈로그에 액세스 패키지를 만들려면 **새 카탈로그 만들기를**클릭합니다. 카탈로그 이름과 설명을 입력한 다음 **을 클릭합니다.**

    만드는 액세스 패키지와 해당 패키지에 포함된 모든 리소스가 새 카탈로그에 추가됩니다. 나중에 카탈로그 소유자를 추가할 수도 있습니다.

1. **다음**을 클릭합니다.

## <a name="resource-roles"></a>리소스 역할

리소스 **역할** 탭에서 액세스 패키지에 포함할 리소스를 선택합니다. 액세스 패키지를 요청하고 받는 사용자는 액세스 패키지의 모든 리소스 역할을 받게 됩니다.

1. 추가하려는 리소스 유형(그룹**및 팀,** 응용 프로그램 또는 **SharePoint 사이트)을** **클릭합니다.**

1. 표시되는 선택 창에서 목록에서 하나 이상의 리소스를 선택합니다.

    ![액세스 패키지 - 리소스 역할](./media/entitlement-management-access-package-create/resource-roles.png)

    일반 카탈로그 또는 새 카탈로그에서 액세스 패키지를 만드는 경우 소유한 디렉터리에서 리소스를 선택할 수 있습니다. 최소한 글로벌 관리자, 사용자 관리자 또는 카탈로그 작성자여야 합니다.

    기존 카탈로그에서 액세스 패키지를 만드는 경우 카탈로그를 소유하지 않고 카탈로그에 이미 있는 리소스를 선택할 수 있습니다.

    글로벌 관리자, 사용자 관리자 또는 카탈로그 소유자인 경우 카탈로그에 아직 없는 리소스를 선택할 수 있는 추가 옵션이 있습니다. 현재 선택한 카탈로그에 없는 리소스를 선택하면 다른 카탈로그 관리자가 액세스 패키지를 빌드할 수 있도록 이러한 리소스도 카탈로그에 추가됩니다. 선택한 카탈로그에 있는 리소스만 선택하려면 선택 창 상단의 **유일한 확인란을** 선택합니다.

1. 리소스를 선택한 후 **역할** 목록에서 리소스에 대해 사용자를 할당할 역할을 선택합니다.

    ![액세스 패키지 - 리소스 역할 선택](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. **다음**을 클릭합니다.

## <a name="requests"></a>요청

**요청** 탭에서 첫 번째 정책을 만들어 액세스 패키지를 요청할 수 있는 사용자 및 승인 설정도 지정합니다. 나중에 추가 사용자 그룹이 자체 승인 설정으로 액세스 패키지를 요청할 수 있도록 더 많은 요청 정책을 만들 수 있습니다.

![액세스 패키지 - 요청 탭](./media/entitlement-management-access-package-create/requests.png)

이 액세스 패키지를 요청할 사람에 따라 다음 섹션 중 하나에서 단계를 수행합니다.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>검토 + 만들기

검토 **+ 만들기** 탭에서 설정을 검토하고 유효성 검사 오류를 확인할 수 있습니다.

1. 액세스 패키지의 설정 검토

    ![액세스 패키지 - 정책 - 정책 설정 사용](./media/entitlement-management-access-package-create/review-create.png)

1. **만들기를** 클릭하여 액세스 패키지를 만듭니다.

    새 액세스 패키지가 액세스 패키지 목록에 나타납니다.

## <a name="creating-an-access-package-programmatically"></a>프로그래밍 방식으로 액세스 패키지 만들기

Microsoft 그래프를 사용하여 액세스 패키지를 만들 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 적절한 역할의 사용자는 API를

1. [카탈로그에 accessPackageResources를 나열하고 카탈로그에](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) 아직 없는 리소스에 대한 [accessPackageResourceRequest를 만듭니다.](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)
1. [액세스패키지카탈로그에](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) 각 액세스패키지자원의 액세스패키지리소스를 나열한다. 그런 다음 이 역할 목록은 나중에 accessPackageResourceRoleScope를 만들 때 역할을 선택하는 데 사용됩니다.
1. [액세스 패키지 를 만듭니다.](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta)
1. [액세스 패키지 할당 정책 만들기](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. 액세스 패키지에 필요한 각 리소스 역할에 대한 [액세스패키지리소스RoleScope를 만듭니다.](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta)

## <a name="next-steps"></a>다음 단계

- [액세스 패키지를 요청하는 링크 공유](entitlement-management-access-package-settings.md)
- [액세스 패키지의 리소스 역할 변경](entitlement-management-access-package-resources.md)
- [액세스 패키지에 사용자를 직접 할당](entitlement-management-access-package-assignments.md)
