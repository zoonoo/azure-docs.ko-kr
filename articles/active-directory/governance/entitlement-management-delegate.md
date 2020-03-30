---
title: 권한 관리의 위임 및 역할 - Azure AD
description: 액세스 거버넌스를 IT 관리자에서 부서 관리자 및 프로젝트 관리자로 위임하여 액세스 권한을 스스로 관리할 수 있도록 하는 방법을 알아봅니다.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261841"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리의 위임 및 역할

기본적으로 글로벌 관리자와 사용자 관리자는 Azure AD 권한 관리의 모든 측면을 만들고 관리할 수 있습니다. 그러나 이러한 역할의 사용자는 액세스 패키지가 필요한 모든 상황을 알지 못할 수 있습니다. 일반적으로 각 부서, 팀 또는 프로젝트 내의 사용자는 누구와 협력하고 있는지, 어떤 리소스를 사용하고, 얼마나 오래 작업하는지 알고 있습니다. 관리자가 아닌 사람에게 무제한 권한을 부여하는 대신 사용자에게 작업을 수행하는 데 필요한 최소한의 권한을 부여하고 충돌하거나 부적절한 액세스 권한을 만들지 않도록 할 수 있습니다.

이 비디오에서는 IT 관리자에서 관리자가 아닌 사용자에게 액세스 거버넌스를 위임하는 방법에 대한 개요를 제공합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>대리자 예제

권한 관리에서 액세스 거버넌스를 위임하는 방법을 이해하려면 예제를 고려하는 것이 좋습니다. 조직에 다음과 같은 관리자와 관리자가 있다고 가정합니다.

![IT 관리자에서 관리자로 위임](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

IT 관리자인 Hana는 각 부서의 마케팅 부서인 Mamta, 재무 마크, 법률 부문의 조 와 같은 부서의 연락처를 보유하고 있으며, 부서의 리소스 및 비즈니스 크리티컬 콘텐츠를 담당하고 있습니다.

권한 부여 관리를 사용하면 액세스가 필요한 사용자, 액세스 기간 및 리소스를 알고 있는 사용자이므로 이러한 비관리자에게 액세스 거버넌스를 위임할 수 있습니다. 이렇게 하면 적절한 사람이 부서에 대한 액세스를 관리할 수 있습니다.

Hana가 마케팅, 재무 및 법률 부서에 액세스 거버넌스를 위임할 수 있는 한 가지 방법은 다음과 같습니다.

1. Hana는 새 Azure AD 보안 그룹을 만들고 맘타, 마크 및 Joe를 그룹의 구성원으로 추가합니다.

1. Hana는 해당 그룹을 카탈로그 작성자 역할에 추가합니다.

    이제 Mamta, Mark 및 Joe는 부서에 대한 카탈로그를 만들고, 부서에 필요한 리소스를 추가하고, 카탈로그 내에서 추가 위임을 수행할 수 있습니다.

    맘타, 마크, 조는 서로의 카탈로그를 볼 수 없습니다.

1. Mamta는 리소스 컨테이너인 **마케팅** 카탈로그를 만듭니다.

1. Mamta는 마케팅 부서가 소유한 리소스를 이 카탈로그에 추가합니다.

1. Mamta는 이 카탈로그의 카탈로그 소유자로 부서의 추가 인력을 추가할 수 있습니다. 이렇게 하면 카탈로그 관리 책임을 공유하는 데 도움이 됩니다.

1. Mamta는 마케팅 카탈로그의 액세스 패키지 생성 및 관리를 마케팅 부서의 프로젝트 관리자에게 위임할 수 있습니다. 액세스 패키지 관리자 역할에 할당하여 이 작업을 수행할 수 있습니다. 액세스 패키지 관리자는 액세스 패키지를 만들고 관리할 수 있습니다. 

다음 다이어그램은 마케팅, 재무 및 법률 부서에 대한 리소스가 있는 카탈로그를 보여 주며, 카탈로그를 보여 주며, 프로젝트 관리자는 이러한 카탈로그를 사용하여 팀 또는 프로젝트에 대한 액세스 패키지를 만들 수 있습니다.

![권한 관리 대리자 예제](./media/entitlement-management-delegate/elm-delegate.png)

위임 후 마케팅 부서에는 다음 표와 유사한 역할이 있을 수 있습니다.

| 사용자 | 직무 역할 | Azure AD 역할 | 권한 관리 역할 |
| --- | --- | --- | --- |
| 하나 | IT 관리자 | 전역 관리자 또는 사용자 관리자 |  |
| 맘타 ("맘타 주) | 마케팅 관리자 | 사용자 | 카탈로그 작성자 및 카탈로그 소유자 |
| Bob | 마케팅 리더 | 사용자 | 카탈로그 소유자 |
| 제시카 | 마케팅 프로젝트 관리자 | 사용자 | 액세스 패키지 관리자 |

## <a name="entitlement-management-roles"></a>권한 관리 역할

권한 부여 관리에는 권한 관리와 관련된 다음 역할이 있습니다.

| 권한 관리 역할 | 설명 |
| --- | --- |
| 카탈로그 작성자 | 카탈로그를 만들고 관리합니다. 일반적으로 글로벌 관리자가 아닌 IT 관리자 또는 리소스 컬렉션의 리소스 소유자입니다. 카탈로그를 자동으로 만드는 사람은 카탈로그의 첫 번째 카탈로그 소유자가 되며 카탈로그 소유자를 추가할 수 있습니다. 카탈로그 작성자는 자신이 소유하지 않은 카탈로그를 관리하거나 볼 수 없으며 카탈로그에 소유하지 않은 리소스를 추가할 수 없습니다. 카탈로그 작성자가 다른 카탈로그를 관리하거나 소유하지 않은 리소스를 추가해야 하는 경우 해당 카탈로그 또는 리소스의 공동 소유자로 요청할 수 있습니다. |
| 카탈로그 소유자 | 기존 카탈로그를 편집하고 관리합니다. 일반적으로 IT 관리자 또는 리소스 소유자 또는 카탈로그 소유자가 지정한 사용자입니다. |
| 액세스 패키지 관리자 | 카탈로그 내의 모든 기존 액세스 패키지를 편집하고 관리합니다. |

또한 지정된 승인자와 액세스 패키지 요청자도 역할이 아니지만 권한이 있습니다.

| Right | 설명 |
| --- | --- |
| 승인자 | 액세스 패키지 정의를 변경할 수는 없지만 패키지에 대한 액세스 요청을 승인하거나 거부할 수 있는 정책의 승인입니다. |
| Requestor | 해당 액세스 패키지를 요청하는 액세스 패키지정책에 의해 승인되었습니다. |

다음 표에는 권한 부여 관리 역할이 수행할 수 있는 작업이 나열되어 있습니다.

| Task | Admin | 카탈로그 작성자 | 카탈로그 소유자 | 액세스 패키지 관리자 |
| --- | :---: | :---: | :---: | :---: |
| [카탈로그 작성자에게 위임](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [연결된 조직 추가](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [새 카탈로그 만들기](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그 소유자 추가](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그 편집](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그 삭제](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [액세스 패키지 관리자에 위임](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [액세스 패키지 관리자 제거](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그에 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [액세스 패키지의 리소스 역할 변경](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [정책 작성 및 편집](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지에 사용자를 직접 할당](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지에 대한 할당이 있는 사용자 보기](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지의 요청 보기](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [요청의 배달 오류 보기](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [요청 다시 처리](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [보류 중인 요청 취소](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지 숨기기](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지 삭제](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>카탈로그에 리소스를 추가하는 데 필요한 역할

글로벌 관리자는 카탈로그에 그룹(클라우드에서 만든 보안 그룹 또는 클라우드에서 만든 Office 365 그룹), 응용 프로그램 또는 SharePoint Online 사이트를 추가하거나 제거할 수 있습니다. 사용자 관리자는 카탈로그에 있는 그룹 또는 응용 프로그램을 추가하거나 제거할 수 있습니다.

글로벌 관리자 나 사용자 관리자가 아닌 사용자의 경우 그룹, 응용 프로그램 또는 SharePoint Online 사이트를 카탈로그에 추가하려면 해당 사용자는 필요한 Azure AD 디렉터리 역할과 카탈로그 소유자 권한 관리 역할을 *모두* 가져야 합니다. 다음 표에는 카탈로그에 리소스를 추가하는 데 필요한 역할 조합이 나열되어 있습니다. 카탈로그에서 리소스를 제거하려면 동일한 역할이 있어야 합니다.

| Azure AD 디렉터리 역할 | 권한 관리 역할 | 보안 그룹을 추가할 수 있습니다. | Office 365 그룹을 추가할 수 있습니다. | 응용 프로그램을 추가 할 수 있습니다 | 쉐어포인트 온라인 사이트 추가 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [인튠 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [팀 서비스 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [SharePoint 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [응용 프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| [클라우드 애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| 사용자 | 카탈로그 소유자 | 그룹 소유자가 | 그룹 소유자가 | 앱 소유자인 경우에만 |  |

작업에 대한 최소 권한 역할을 확인하려면 Azure Active Directory 의 [관리자 작업별로 관리자 역할을](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [카탈로그 작성자에게 액세스 거버넌스 위임](entitlement-management-delegate-catalog.md)
- [리소스 카탈로그 생성 및 관리](entitlement-management-catalog-create.md)
