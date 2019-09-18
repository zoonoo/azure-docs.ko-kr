---
title: Azure AD 자격 관리 (미리 보기)에서 작업 위임-Azure Active Directory
description: Azure Active Directory 자격 관리에서 작업을 위임 하기 위해 할당할 수 있는 역할에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6857697423e494c515bd052cb42af3ad1d9fe188
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057787"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 작업 위임

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

기본적으로 전역 관리자 및 사용자 관리자는 Azure AD 자격 관리의 모든 측면을 만들고 관리할 수 있습니다. 그러나 이러한 역할의 사용자는 액세스 패키지가 필요한 모든 시나리오를 모를 수 있습니다. 일반적으로 공동 작업 해야 하는 사용자를 아는 부서 내의 사용자입니다. 

관리자가 아닌 사용자에 게 무제한 권한을 부여 하는 대신 사용자에 게 작업을 수행 하는 데 필요한 최소 권한을 부여 하 고 충돌 하거나 부적절 한 액세스 권한을 만들지 않도록 할 수 있습니다. 이 문서에서는 자격 관리의 다양 한 작업을 위임 하기 위해 할당할 수 있는 역할에 대해 설명 합니다. 

## <a name="delegate-example-for-departmental-adoption"></a>부서별 채택을 위한 대리자 예제

자격 관리에서 작업을 위임 하는 방법을 이해 하기 위해 예제를 사용 하는 것이 좋습니다. 

조직에 다음 5 명의 사용자가 있다고 가정 합니다.

| 사용자 | Department | 참고 |
| --- | --- | --- |
| Alice | IT | 전역 관리자 |
| Bob | 연구 | Bob은 연구 그룹의 소유자 이기도 합니다. |
| Carole | 연구 |  |
| Dave | 마케팅 |  |
| Elisa | 마케팅 | Elisa는 마케팅 응용 프로그램의 소유자 이기도 합니다. |

연구와 마케팅 부서 모두 사용자에 대 한 자격 관리를 사용 하려고 합니다. Alice는 아직 다른 부서가 자격 관리를 사용할 준비가 되지 않았습니다. Alice가 연구 및 마케팅 부서에 작업을 위임할 수 있는 한 가지 방법은 다음과 같습니다.

1. Alice는 카탈로그 작성자를 위한 새 Azure AD 보안 그룹을 만들고 Bob, Elisa, Dave 및을 해당 그룹의 멤버로 추가 합니다.

1. Alice는 자격 관리 설정을 사용 하 여 해당 그룹을 카탈로그 작성자 역할에 추가 합니다.

1. 추가는 **연구** 카탈로그를 만들고 Bob을 해당 카탈로그의 공동 소유자로 추가 합니다. Bob은 소유 하 고 있는 리서치 그룹을 리소스로 추가 하 여 리서치 공동 작업을 위해 액세스 패키지에 사용할 수 있도록 합니다.

1. Dave **마케팅** 카탈로그를 만들고 Elisa을 해당 카탈로그의 공동 소유자로 추가 합니다. Elisa는 소유 하 고 있는 마케팅 응용 프로그램을 리소스로 카탈로그에 추가 하 여 마케팅 공동 작업을 위한 액세스 패키지에 사용할 수 있도록 합니다.

이제 연구 및 마케팅 부서에서 자격 관리를 활용할 수 있습니다. Bob, Dave 및 Elisa는 해당 카탈로그에서 액세스 패키지를 만들고 관리할 수 있습니다.

![자격 관리 위임 예제](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>권한 관리 역할

자격 관리에는 자격 관리와 관련 된 다음과 같은 역할이 있습니다.

| 역할 | Description |
| --- | --- |
| 카탈로그 작성자 | 카탈로그를 만들고 관리 합니다. 일반적으로 전역 관리자가 아닌 IT 관리자 또는 리소스 컬렉션에 대 한 리소스 소유자입니다. 카탈로그를 만드는 사람은 자동으로 카탈로그의 첫 번째 카탈로그 소유자가 되며 카탈로그 소유자를 더 추가할 수 있습니다. 카탈로그 작성자는 소유 하지 않은 카탈로그를 관리 하거나 볼 수 없으며 소유 하지 않은 리소스를 카탈로그에 추가할 수 없습니다. 카탈로그 작성자가 다른 카탈로그를 관리 하거나 소유 하지 않은 리소스를 추가 해야 하는 경우 해당 카탈로그 또는 리소스의 공동 소유자가 되도록 요청할 수 있습니다. |
| 카탈로그 소유자 | 기존 카탈로그를 편집 하 고 관리 합니다. 일반적으로 IT 관리자 또는 리소스 소유자 이거나 카탈로그 소유자가 지정 하는 사용자입니다. |
| 액세스 패키지 관리자 | 카탈로그 내 모든 기존 액세스 패키지를 편집 하 고 관리 합니다. |

또한 액세스 패키지의 지정 된 승인자와 요청자는 역할이 아니라 권한도 갖습니다.
 
* 명의 패키지에 대 한 액세스 요청을 승인 하거나 거부 하기 위해 정책에서 권한을 부여 합니다. 단, 액세스 패키지 정의는 변경할 수 없습니다.
* 요청 액세스 패키지를 요청 하는 액세스 패키지의 정책에 의해 권한이 부여 됩니다.

다음 표에서는 이러한 역할이 수행할 수 있는 작업을 보여 줍니다.

| 태스크 | 카탈로그 작성자 | 카탈로그 소유자 | 액세스 패키지 관리자 | 승인자 |
| --- | :---: | :---: | :---: | :---: |
| [새 카탈로그 만들기](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [카탈로그 편집](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [카탈로그 삭제](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [카탈로그에 카탈로그 소유자 또는 액세스 패키지 관리자 추가](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [카탈로그에 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [액세스 패키지의 리소스 역할 관리](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [정책 만들기 및 편집](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [사용자를 액세스 패키지에 직접 할당](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지에 할당 된 사용자 보기](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지의 요청 보기](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [요청의 배달 오류 보기](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [보류 중인 요청 취소](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지 숨기기](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지 삭제](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 요청 승인](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>카탈로그에 리소스를 추가 하는 데 필요한 역할

전역 관리자는 카탈로그에서 그룹 (클라우드로 생성 된 보안 그룹 또는 클라우드 생성 Office 365 그룹), 응용 프로그램 또는 SharePoint Online 사이트를 추가 하거나 제거할 수 있습니다. 사용자 관리자는 카탈로그에서 그룹 또는 응용 프로그램을 추가 하거나 제거할 수 있습니다.

전역 관리자 또는 사용자 관리자가 아닌 사용자의 경우 카탈로그에 그룹, 응용 프로그램 또는 SharePoint Online 사이트를 추가 하려면 해당 사용자에 게 필요한 Azure AD 디렉터리 역할과 카탈로그 소유자 자격 관리 역할이 *둘 다* 있어야 합니다. 다음 표에는 카탈로그에 리소스를 추가 하는 데 필요한 역할 조합이 나와 있습니다. 카탈로그에서 리소스를 제거 하려면 동일한 역할이 있어야 합니다.

| Azure AD 디렉터리 역할 | 자격 관리 역할 | 보안 그룹을 추가할 수 있음 | Office 365 그룹을 추가할 수 있습니다. | 앱 추가 가능 | SharePoint Online 사이트 추가 가능 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md) | n/a |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [팀 서비스 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [SharePoint 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| [클라우드 애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| 사용자 | 카탈로그 소유자 | 그룹 소유자 인 경우에만 | 그룹 소유자 인 경우에만 | 앱 소유자 인 경우에만 |  |

태스크에 대 한 최소 권한 있는 역할을 확인 하려면 [Azure Active Directory에서 관리자 작업을 통해 관리자 역할](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)을 참조할 수도 있습니다.

## <a name="add-a-catalog-creator"></a>카탈로그 작성자 추가

카탈로그 만들기를 위임 하려면 카탈로그 작성자 역할에 사용자를 추가 합니다.  개별 사용자를 추가 하거나 편의를 위해 구성원이 카탈로그를 만들 수 있는 그룹을 추가할 수 있습니다. 카탈로그 작성자 역할에 사용자를 할당 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴의 **자격 관리** 섹션에서 **설정**을 클릭 합니다.

1. **편집**을 클릭합니다.

1. **위임 권한 관리** 섹션에서 **카탈로그 작성자 추가** 를 클릭 하 여이 자격 관리 역할의 구성원이 될 사용자 또는 그룹을 선택 합니다.

1. **선택**을 클릭합니다.

1. **Save**을 클릭합니다.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>카탈로그 소유자 또는 액세스 패키지 관리자 추가

카탈로그에 있는 카탈로그 또는 액세스 패키지의 관리를 위임 하려면 카탈로그 소유자에 게 사용자를 추가 하거나 패키지 관리자 역할에 액세스 합니다. 카탈로그를 만드는 사람은 첫 번째 카탈로그 소유자가 됩니다. 

할당 된 카탈로그 소유자 또는 액세스 패키지 관리자는 프로젝트에 대해 잘 알고 있어야 합니다. 카탈로그 작성자는 프로젝트의 일상적인 작업과 관련 하 여 액세스 패키지를 만들고 다음 정보를 알고 있어야 합니다.
- 필요한 리소스
- 액세스 해야 하는 사용자
- 액세스를 승인 해야 하는 사용자
- 프로젝트가 마지막으로 지속 되는 시간

카탈로그 작성자는 프로젝트의 일상 업무에 포함 되지 않은 경우 access 패키지를 만들고 관리 하는 프로젝트 책임자에 게 작업을 위임 해야 합니다. 카탈로그 소유자 또는 액세스 패키지 관리자 역할에 사용자를 할당 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 관리자를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자**를 클릭 합니다.

1. **소유자 추가** 또는 **액세스 패키지 관리자 추가** 를 클릭 하 여 이러한 역할에 대 한 멤버를 선택 합니다.

1. **선택** 을 클릭 하 여 이러한 멤버를 추가 합니다.

## <a name="next-steps"></a>다음 단계

- [승인자 추가](entitlement-management-access-package-edit.md#policy-request)
- [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
