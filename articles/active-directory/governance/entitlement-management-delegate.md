---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 작업을 위임
description: Azure Active Directory 권한 관리에는 작업 대리자를 할당할 수 있습니다 역할에 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa0be8e2af7644564ba27e6d58fda09b1ae7bc7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191500"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리 (미리 보기)에서 작업을 위임

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

기본적으로 전역 관리자 및 사용자 관리자 만들기 하 고 Azure AD 권한 관리의 모든 측면을 관리할 수 있습니다. 그러나 이러한 역할의 사용자 액세스 패키지가 모두 필요 하는 모든 시나리오를 모를 수 있습니다. 부서 내의 사용자는 것이 일반적으로 공동 작업을 수행 해야 하는 알고 있는 사용자입니다.

관리자가 아닌 무제한 권한을 부여 하는 대신 최소 업무를 수행 하 고 충돌을 만들지 않도록 하는 데 필요한 권한만 또는 부적절 한 액세스 권한을 사용자에 부여할 수 있습니다. 이 문서에서는 권한 관리의 다양 한 작업 대리자를 할당할 수 있는 역할을 설명 합니다.

## <a name="delegate-example-for-departmental-adoption"></a>부서별 채택에 대 한 대리자 예제

권한 관리의 작업 위임 하는 방법을 이해 하는 예제를 고려해 야 할 수 있습니다. 조직에는 다음 5 명의 사용자를 가정 합니다.

| 사용자 | department | 메모 |
| --- | --- | --- |
| Alice | IT | 전역 관리자 |
| Bob | 연구 | Bob은 연구 그룹의 소유자 |
| Carole | 연구 |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa는 마케팅 응용 프로그램의 소유자 이기도 |

연구 및 마케팅 부서의 사용자에 대 한 권한 관리를 사용 하려고 합니다. Alice가 아직 권한 관리를 사용 하도록 다른 부서에 대 한 준비 되지 않았습니다. Alice가 작업을 조사 및 마케팅 부서의 위임할 수 있습니다 하는 한 가지 방법은 다음과 같습니다.

1. Alice에서는 새 Azure AD 보안 카탈로그 작성자에 대 한 그룹화 하 고 Bob, Carol, Dave, Elisa 해당 그룹의 구성원으로 추가 합니다.

1. Alice는 카탈로그 작성자 역할에 해당 그룹을 추가할 권한 관리 설정을 사용 합니다.

1. Carol 만듭니다는 **연구** 카탈로그 및 해당 카탈로그를 공동 소유자로 Bob을 추가 합니다. Bob를 담당 하 고 카탈로그를 리소스로 연구 그룹을 추가 하 고 리서치 공동 작업에 대 한 액세스 패키지에서 사용할 수 있습니다.

1. Dave 만듭니다는 **마케팅** 카탈로그 및 해당 카탈로그를 공동 소유자로 Elisa를 추가 합니다. Elisa 리소스로 카탈로그에 자신이 소유 하는 마케팅 응용 프로그램을 추가 하 고 공동 마케팅에 대 한 액세스 패키지에서 사용할 수 있습니다.

이제 연구 및 마케팅 부서의 권한 관리를 활용할 수 있습니다. Bob, Carol, Dave, 및 Elisa 수 만들고 해당 해당 카탈로그에서 패키지 액세스를 관리 합니다.

![권한 관리 대리자 예제](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>권한 관리 역할

권한 관리에는 권한 관리와 관련 된 다음 역할이 있습니다.

| 역할 | 설명 |
| --- | --- |
| 카탈로그를 만든 | 카탈로그 만들기 및 관리 합니다. 일반적으로 IT 관리자는 전역 관리자 또는 리소스의 컬렉션에 대 한 리소스 소유자는 아닙니다. 카탈로그를 자동으로 생성 하는 사용자는 카탈로그의 첫 번째 카탈로그 소유자 되며 추가 카탈로그 소유자를 추가할 수 있습니다. |
| 카탈로그 소유자 | 편집 하 고 기존 카탈로그를 관리 합니다. IT 관리자가 일반적으로 리소스 소유자 또는 카탈로그 소유자가 지정한 사용자입니다. |
| 패키지 관리자 액세스 | 편집 하 고 카탈로그 내에서 모든 기존 액세스 패키지를 관리 합니다. |

또한 지정 된 승인자 및 액세스 패키지의 요청 자가 권한이 역할 되지 않습니다.
 
* 승인자: 패키지에 액세스 하려면 요청을 승인 하거나 거부 정책에 의해 권한이 부여 하지만 변경할 수 없습니다 액세스 패키지 정의 합니다.
* 요청자: 패키지에 대 한 액세스를 요청 하도록 패키지를 액세스 하는 정책을 권한을 부여 합니다.

다음 표에서 이러한 역할을 수행할 수 있는 작업을 나열 합니다.

| Task | 카탈로그를 만든 | 카탈로그 소유자 | 패키지 관리자 액세스 | 승인자 |
| --- | :---: | :---: | :---: | :---: |
| [새 카탈로그 만들기](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [리소스를 카탈로그에 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [카탈로그를 편집 합니다.](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [카탈로그를 삭제 합니다.](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [카탈로그 소유자 또는 액세스 패키지 관리자를 카탈로그에 추가](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [카탈로그에서 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) |  | :heavy_check_mark: |  |  |
| [액세스 패키지의 리소스 역할 관리](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 요청할 수 있는 사용자 지정](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [직접 액세스 패키지에 사용자 할당](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지에 할당 권한이 있는 사용자 보기](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 요청 보기](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [요청의 배달 오류 보기](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [보류 중인 요청 취소](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 숨기기](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 삭제 합니다.](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 요청 승인](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>카탈로그에 리소스를 추가 하려면 필요한 역할

전역 관리자를 추가 하거나 카탈로그에서 모든 그룹 (클라우드 만든 보안 그룹 또는 Office 365 그룹 클라우드 생성), 응용 프로그램 또는 SharePoint Online 사이트를 제거할 수 있습니다. 사용자 관리자를 추가 하거나 그룹 또는 응용 프로그램 카탈로그에서 제거할 수 있습니다.

카탈로그에 그룹, 응용 프로그램 또는 SharePoint Online 사이트를 추가 하려면 전역 관리자 또는 사용자 관리자가 아닌 사용자에 대 한 해당 사용자 있어야 *둘 다* 를 Azure AD 디렉터리 역할 및 카탈로그 소유자 권한 필요 관리 역할입니다. 다음 표에서 리소스 카탈로그를 추가 하는 데 필요한 역할 조합을 나열 합니다. 카탈로그에서 리소스를 제거 하려면 동일한 역할이 있어야 합니다.

| Azure AD 디렉터리 역할 | 권한 관리 역할 | 보안 그룹을 추가할 수 있습니다. | Office 365 그룹을 추가할 수 있습니다. | 앱을 추가할 수 있습니다. | SharePoint Online 사이트를 추가할 수 있습니다. |
| --- | :---: | :---: | :---: | :---: | :---: |
| [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [팀 서비스 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [SharePoint 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| [클라우드 애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| 사용자 | 카탈로그 소유자 | 경우에만 그룹 소유자 | 경우에만 그룹 소유자 | 경우에만 앱 소유자 |  |

## <a name="add-a-catalog-creator"></a>카탈로그 작성자를 추가 합니다.

대리자 카탈로그 생성 하려는 경우에 카탈로그 작성자 역할에 사용자를 추가 합니다.  개별 사용자를 추가할 수 있습니다 하거나 편의 멤버가 카탈로그를 만들 수 있는 그룹을 추가할 수 있습니다. 카탈로그 작성자 역할에 사용자를 할당 하려면 다음이 단계를 수행 합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서에 **권한 관리** 섹션에서 클릭 **설정**합니다.

1. **편집**을 클릭합니다.

1. **권한 관리를 위임** 섹션에서 **카탈로그 작성자를 추가** 사용자나이 자격 관리 역할의 구성원이 될 그룹을 선택 합니다.

1. **선택**을 클릭합니다.

1. **저장**을 클릭합니다.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>카탈로그 소유자 또는 액세스 패키지 관리자를 추가 합니다.

카탈로그에서 카탈로그 또는 액세스의 관리를 위임 하려는 경우에 카탈로그 소유자 또는 액세스 패키지 관리자 역할에 사용자를 추가 합니다. 첫 번째 카탈로그 소유자가 됩니다 누구 든 지 카탈로그를 만듭니다. 카탈로그 소유자 또는 액세스 패키지 관리자 역할에 사용자를 할당 하려면 다음이 단계를 수행 합니다.

**필수 역할:** 카탈로그 소유자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **카탈로그** 한 다음 관리자를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **역할과 관리자**합니다.

1. 클릭 **소유자 추가** 또는 **액세스 패키지 관리자를 추가** 이러한 역할에 대 한 구성원을 선택 합니다.

1. 클릭 **선택** 이러한 멤버를 추가 합니다.

## <a name="next-steps"></a>다음 단계

- [승인자 추가](entitlement-management-access-package-edit.md#policy-request)
- [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
