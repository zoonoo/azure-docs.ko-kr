---
title: Azure AD 권한 관리란? (미리 보기)-Azure Active Directory
description: Azure Active Directory 권한 관리 및 사용 하 여 내부 및 외부 사용자에 대 한 그룹, 응용 프로그램 및 SharePoint Online 사이트에 대 한 액세스를 관리 하는 방법을의 개요를 봅니다.
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
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efd3ff8a6e7ddf2aa6242cc322d8a6536a6bd26b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474054"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리란? (미리 보기)

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

조직에서 직원 들에는 다양 한 그룹, 응용 프로그램 및 사이트 작업 수행에 액세스를 해야 합니다. 이 액세스를 관리 하는 것은 어려운 문제입니다. 대부분의 경우에서 프로젝트에 대 한 사용자에 게 필요한 모든 리소스의 구성 된 목록이 없는 있습니다. 프로젝트 관리자에 이해가 필요한 리소스, 시간 및 관련 된 개인 프로젝트 지속 됩니다. 그러나 프로젝트 관리자는 일반적으로 권한이 없을을 승인 하거나 다른 사용자에 게 액세스 권한을 부여 합니다. 이 시나리오는 외부 개인 이나 회사를 사용 하려고 할 때 좀 더 복잡해 집니다.

Azure Active Directory (Azure AD) 권한 관리는 내부 사용자와 조직 외부 사용자에 대 한 그룹, 응용 프로그램 및 SharePoint Online 사이트에 대 한 액세스를 관리할 수 있습니다.

## <a name="why-use-entitlement-management"></a>권한 관리를 사용 하는 이유는?

기업이 직면 과제와 같은 리소스에 대 한 액세스를 관리:

- 사용자가 가져야 하는 액세스를 모를 수 있습니다.
- 사용자가 적절 한 개인 또는 적절 한 리소스를 찾는 데 문제가 있을 수 있습니다.
- 사용자에서 검색 하 고 리소스에 대 한 액세스 권한을 받게 되 면 이러한 유지할 수 있습니다 비즈니스 용도에 필요한 것 보다 더 이상 액세스

이러한 문제는 공급 체인 조직이 나 다른 비즈니스 파트너에 있는 외부 사용자와 같은 다른 디렉터리에서 액세스 해야 하는 사용자에 대 한 상황에서 복잡해 집니다. 예를 들면 다음과 같습니다.

- 조직 모를 수 초대할 수 있는 다른 디렉터리의 특정 개인의 모든
- 조직에서는 이러한 사용자를 초대할 수 인 경우에 조직 일관 되 게 모든 사용자의 액세스를 관리 하 기억 하지 수 없습니다.

Azure AD 권한 관리는 이러한 문제를 해결 하는 데 도움이 됩니다.

## <a name="what-can-i-do-with-entitlement-management"></a>권한 관리를 사용 하 여 어떻게 해야 합니까?

다음은 일부 권한 관리의 기능입니다.

- 사용자가 요청할 수 있는 관련된 리소스의 패키지 만들기
- 요청 리소스 및 액세스가 만료 되는 경우에 방법에 대 한 규칙을 정의 합니다.
- 내부 및 외부 사용자에 대 한 액세스의 수명 주기를 제어 합니다.
- 리소스 관리 위임
- 요청을 승인할 승인자를 지정 합니다.
- 기록을 추적 하는 보고서 만들기

Id 관리 및 권한 관리의 개요, 2018 Ignite 컨퍼런스에서 다음 동영상을 시청 합니다.

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>어떤 리소스를 관리할 수 있나요?

권한 관리에 대 한 액세스를 관리할 수 있는 리소스의 종류는 다음과 같습니다.

- Azure AD 보안 그룹
- Office 365 그룹
- Azure AD 엔터프라이즈 응용 프로그램, SaaS 응용 프로그램 및 페더레이션을 지 원하는 사용자 지정 통합 응용 프로그램을 포함 하거나 프로 비전
- SharePoint Online 사이트 모음 및 사이트

또한 Azure AD 보안 그룹 또는 Office 365 그룹에 의존 하는 다른 리소스에 대 한 액세스를 제어할 수 있습니다.  예를 들면 다음과 같습니다.

- 구성 및 액세스 패키지에는 Azure AD 보안 그룹을 사용 하 여 Microsoft Office 365에 대 한 사용자 라이선스를 제공할 수 있습니다 [그룹 기반 라이선스](../users-groups-roles/licensing-groups-assign.md) 해당 그룹에 대 한
- 사용자 만들기 및 액세스 패키지에는 Azure AD 보안 그룹을 사용 하 여 Azure 리소스 관리에 대 한 액세스를 제공할 수 있습니다는 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md) 해당 그룹에 대 한

## <a name="what-are-access-packages-and-policies"></a>액세스 패키지 및 정책 이란?

권한 관리의 개념을 소개는 *액세스 패키지*합니다. 액세스 패키지는 사용자가 프로젝트에서 작업 또는 해당 작업을 수행 해야 하는 모든 리소스 번들입니다. 리소스 그룹, 응용 프로그램 또는 사이트에 대 한 액세스를 포함 합니다. 액세스 패키지 내부 직원 및도 조직 외부 사용자에 대 한 액세스를 제어 하는 데 사용 됩니다. 액세스 패키지는 카탈로그(*catalogs*)라는 컨테이너에 정의됩니다.

하나 이상의 액세스 패키지 포함 *정책을*합니다. 정책 규칙 또는 액세스 패키지를 액세스 하는 guardrails를 정의 합니다. 적절 한 사용자만 적절 한 리소스와 적절 한 양의 시간에 대 한 액세스를 부여 되어 있는지 적용 정책을 사용 하도록 설정 합니다.

![패키지 액세스 및 정책](./media/entitlement-management-overview/elm-overview-access-package.png)

패키지를 액세스 및 해당 정책을 사용 하 여 액세스 패키지 관리자를 정의합니다.

- 리소스
- 리소스에 필요한 사용자 역할
- 내부 사용자와 액세스를 요청할 수 있는 외부 사용자
- 승인 프로세스 및 승인 하거나 액세스를 거부할 수 있는 사용자
- 사용자의 액세스 기간

다음 다이어그램은 자격 관리에서 다양 한 요소 예를 보여 줍니다. 두 가지 예제 액세스 패키지 보여 줍니다.

- **액세스 패키지 1** 단일 리소스 그룹을 포함 합니다. 액세스는 액세스를 요청 하려면 디렉터리의 사용자 집합을 사용 하는 정책을 사용 하 여 정의 됩니다.
- **액세스 패키지 2** 리소스로 그룹, 응용 프로그램 및 SharePoint Online 사이트를 포함 합니다. 액세스는 두 개의 다른 정책을 사용 하 여 정의 됩니다. 첫 번째 정책이 액세스를 요청 하려면 디렉터리의 사용자 집합을 수 있습니다. 두 번째 정책은 액세스를 요청 하는 외부 디렉터리에 사용자를 수 있습니다.

![권한 관리 개요](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>외부 사용자

사용 하는 경우는 [Azure AD-비즈니스 (b2b)](../b2b/what-is-b2b.md) 환경을 초대를 리소스 디렉터리로 가져오고 사용 하려는 외부 게스트 사용자의 전자 메일 주소를 알고 있어야 합니다. 참가자가 시간이 지남에 따라 변경 하는 경우 또는 작거나 단기 프로젝트에서 작업 하는 모든 참가자가 이미 알고 있지만 많은 사용자를 사용 하려는 경우 관리는이 어려운 경우에 유용한이 작동 합니다.  예를 들어, 다른 조직의 작업 및 해당 조직 연락처의 한 지점 수 있습니다 하지만 시간이 지남에 따라 해당 조직에서 추가 사용자도 액세스 해야 합니다.

권한 관리를 사용 하 여 사용자를 지정 하는 또한 액세스 패키지를 요청 하려면 Azure AD를 사용 하는 조직에서 허용 하는 정책을 정의할 수 있습니다. 승인이 필요한 여부와 액세스에 대 한 만료 날짜를 지정할 수 있습니다. 승인자로 지정할 수도 있습니다 승인이 필요한 경우 이전에 초대-액세스 해야 하는 조직에서 외부 사용자를 모르는 되므로 외부 조직에서 하나 이상의 사용자입니다. 액세스 패키지를 구성한 후에 외부 조직에서 사용자 연락처에 액세스 패키지에 링크를 보낼 수 있습니다. 해당 연락처는 외부 조직에서 다른 사용자와 공유할 수 및 액세스 패키지를 요청 하려면이 링크를 사용할 수 있습니다.  해당 조직의 디렉터리에 이미 초대 된 사용자는 해당 링크를 사용할 수도 있습니다.

요청이 승인 되 면 자격 관리 있지 않은 경우 이미 디렉터리에 사용자를 초대를 포함할 수 있는 필요한 액세스를 사용 하 여 사용자 프로 비전 됩니다. Azure AD에 B2B 계정을 자동으로 만듭니다.  Note는 관리자로 제한 되어 있습니다 이전에 조직에서 허용 하는 공동 작업에 대 한 설정를 [B2B 허용 또는 거부 목록을](../b2b/allow-deny-list.md) 다른 조직에 초대를 허용 하거나 차단 합니다.  사용자가 허용 또는 차단 목록에서 허용 되지 않는 경우 다음 이러한가 초대할 수 없습니다.

영원에 대 한 외부 사용자의 액세스 하지 않으려면 때문에 180 일 같은 정책에서 만료 날짜를 지정 합니다. 180 일 후에 대 한 액세스를 갱신 하지 않으면 자격 관리 제거 됩니다 해당 액세스 패키지와 관련 된 모든 액세스.  권한 관리를 통해 초대 된 사용자가 다른 액세스 패키지 할당이 없습니다, 다음 잃게가 마지막으로 할당 하는 경우 해당 B2B 계정 로그인에서 30 일 동안 차단 되며 이후에 제거 합니다.  이렇게 하면 불필요 한 계정의 확산 됩니다.  

## <a name="terminology"></a>용어

권한 관리 및 해당 설명서를 더 잘 이해 하려면 다음 용어를 검토 해야 합니다.

| 용어 또는 개념 | 설명 |
| --- | --- |
| 권한 관리 | 할당을 취소 하 고 액세스 패키지를 관리 하는 서비스입니다. |
| 패키지 액세스 | 컬렉션 사용 권한 및 정책을 사용자가 요청할 수 있는 리소스입니다. 카탈로그 액세스 패키지를 항상 포함 됩니다. |
| 액세스 요청 | 액세스 패키지를 액세스 하는 요청입니다. 요청은 일반적으로 워크플로 통해 이동합니다. |
| policy | 사용자 액세스를 얻는 방법, 승인할 수 있는, 사용자가 액세스할 기간 등 액세스 수명 주기를 정의 하는 규칙의 집합입니다. 예제 정책에 대 한 직원 액세스 및 외부 액세스 포함 됩니다. |
| catalog | 관련 된 리소스 및 액세스 패키지의 컨테이너입니다. |
| 일반 카탈로그 | 항상 사용할 수 있는 기본 제공 카탈로그입니다. 일반 카탈로그 리소스를 추가 하려면 특정 권한이 필요 합니다. |
| resource | 자산 또는 사용자 권한을 부여할 수 있는 서비스 (예: 그룹, 응용 프로그램 또는 사이트). |
| 리소스 종류 | 그룹, 응용 프로그램 및 SharePoint Online 사이트를 포함 하는 리소스의 형식입니다. |
| 리소스 역할 | 컬렉션 리소스를 사용 하 여 연결 된 권한입니다. |
| 리소스 디렉터리 | 하나 이상의 리소스를 공유할 수 있는 디렉터리입니다. |
| 할당 된 사용자 | 사용자 또는 그룹에 대 한 액세스 패키지를 할당 하는 것입니다. |
| enable | 프로세스는 액세스 패키지를 요청 하는 사용자를 사용할 수 있도록입니다. |

## <a name="roles-and-permissions"></a>역할 및 권한

권한 관리에 작업 함수를 기반으로 하는 다른 역할에 있습니다.

| 역할 | 설명 |
| --- | --- |
| [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) | 권한 관리의 모든 측면을 관리 합니다.<br/>사용자 및 그룹을 만듭니다. |
| 카탈로그를 만든 | 카탈로그 만들기 및 관리 합니다. 일반적으로 IT 관리자 또는 리소스 소유자입니다. 카탈로그를 자동으로 생성 하는 사람에는 카탈로그의 첫 번째 카탈로그 소유자가 됩니다. |
| 카탈로그 소유자 | 편집 하 고 기존 카탈로그를 관리 합니다. 일반적으로 IT 관리자 또는 리소스 소유자입니다. |
| 패키지 관리자 액세스 | 편집 하 고 카탈로그 내에서 모든 기존 액세스 패키지를 관리 합니다. |
| 승인자 | 패키지에 액세스 하려면 요청을 승인 합니다. |
| 요청자 | 패키지 액세스를 요청 합니다. |

다음 표에서 이러한 각 역할에 대 한 사용 권한을 나열합니다.

| Task | 사용자 관리자 | 카탈로그를 만든 | 카탈로그 소유자 | 패키지 관리자 액세스 | 승인자 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [일반 카탈로그에서 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) | :heavy_check_mark: |  :heavy_check_mark: |  |  |  |
| [카탈로그에서 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) | :heavy_check_mark: |   | :heavy_check_mark: |  |  |
| [액세스 패키지에서 리소스 역할 추가/제거](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 요청할 수 있는 사용자 지정](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [직접 액세스 패키지에 사용자 할당](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지에 할당 권한이 있는 사용자 보기](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 요청 보기](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [요청의 배달 오류 보기](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [보류 중인 요청 취소](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 숨기기](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 패키지를 삭제 합니다.](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [액세스 요청 승인](entitlement-management-request-approve.md) |  |  |  |  | :heavy_check_mark: |
| [카탈로그 만들기](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [일반 카탈로그에서 리소스를 추가/제거 합니다.](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  |  |  |  |
| [카탈로그에서 리소스를 추가/제거 합니다.](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [카탈로그 소유자를 추가 하거나 패키지 관리자에 액세스](entitlement-management-catalog-create.md#add-catalog-owners-or-access-package-managers) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [카탈로그를 편집/삭제](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Azure Government, Azure 독일 및 Azure 중국 21Vianet와 같은 특수 한 클라우드, 현재이 미리 보기에서 사용 하기 위해 사용할 수 없는 경우

## <a name="next-steps"></a>다음 단계

- [자습서: 첫 번째 액세스 패키지 만들기](entitlement-management-access-package-first.md)
- [일반적인 시나리오](entitlement-management-scenarios.md)
