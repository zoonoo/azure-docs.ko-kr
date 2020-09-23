---
title: Azure AD 자격 관리에서 액세스 패키지에 대 한 변경 요청 설정-Azure Active Directory
description: Azure Active Directory 자격 관리에서 액세스 패키지에 대 한 요청 설정을 변경 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 160137ca2d14e0012a524ee0818b7fb269fa7984
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90980258"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 변경 요청 설정

액세스 패키지 관리자로 서 언제 든 지 정책을 편집 하거나 새 정책을 추가 하 여 액세스 패키지를 요청할 수 있는 사용자를 변경할 수 있습니다. 이 문서에서는 기존 액세스 패키지에 대 한 요청 설정을 변경 하는 방법을 설명 합니다.

## <a name="choose-between-one-or-multiple-policies"></a>하나 또는 여러 정책 중에서 선택

액세스 패키지를 요청할 수 있는 사용자를 지정 하는 방법은 정책을 사용 하는 것입니다. 액세스 패키지에서 새 정책을 만들거나 기존 정책을 편집 하기 전에 액세스 패키지에 필요한 정책 수를 결정 해야 합니다. 

액세스 패키지를 만들 때 정책을 만드는 요청 설정을 지정 합니다. 대부분의 액세스 패키지에는 단일 정책이 있지만 단일 액세스 패키지는 여러 정책을 포함할 수 있습니다. 서로 다른 요청 및 승인 설정을 사용 하 여 다른 사용자 집합에 할당을 부여할 수 있도록 허용 하려면 액세스 패키지에 대 한 정책을 여러 개 만듭니다. 

예를 들어 동일한 액세스 패키지에 내부 및 외부 사용자를 할당 하는 데는 단일 정책을 사용할 수 없습니다. 그러나 동일한 액세스 패키지에 두 개의 정책을 만들 수 있습니다. 하나는 내부 사용자를 위한 것이 고 다른 하나는 외부 사용자를 위한 것입니다. 사용자에 게 적용 되는 정책이 여러 개 있는 경우 요청 시 사용자에 게 할당 하려는 정책을 선택 하 라는 메시지가 표시 됩니다. 다음 다이어그램에서는 두 개의 정책을 포함 하는 액세스 패키지를 보여 줍니다.

![액세스 패키지의 여러 정책](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>필요한 정책 수는 몇 개입니까?

| 시나리오 | 정책 수 |
| --- | --- |
| 내 디렉터리의 모든 사용자에 게 액세스 패키지에 대 한 동일한 요청 및 승인 설정을 포함 하려고 합니다. | 하나 |
| 연결 된 특정 조직의 모든 사용자가 액세스 패키지를 요청할 수 있도록 하려고 합니다. | 하나 |
| 내 디렉터리의 사용자와 내 디렉터리의 사용자가 액세스 패키지를 요청 하도록 허용 합니다. | 여러 |
| 일부 사용자에 대해 다른 승인 설정을 지정 하려고 합니다. | 여러 |
| 일부 사용자가 패키지 할당을 액세스 하 여 다른 사용자가 액세스를 확장할 수 있도록 하려고 합니다. | 여러 |

여러 정책이 적용 될 때 사용 되는 우선 순위 논리에 대 한 자세한 내용은 [여러 정책](entitlement-management-troubleshoot.md#multiple-policies
)을 참조 하세요.

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>기존 액세스 패키지를 열고 요청 설정의 새 정책 추가

다른 요청 및 승인 설정을 가져야 하는 사용자 집합이 있는 경우 새 정책을 만들어야 할 가능성이 높습니다. 기존 액세스 패키지에 새 정책 추가를 시작 하려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 **정책 추가**를 클릭 합니다.

1. **기본** 탭에서를 시작 합니다. 정책에 대 한 이름 및 설명을 입력 합니다.

    ![이름 및 설명을 사용 하 여 정책 만들기](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. **다음**을 클릭하여 **요청** 탭을 엽니다.

1. 액세스를 **요청할 수 있는 사용자** 를 변경 합니다. 다음 섹션의 단계를 사용 하 여 설정을 다음 옵션 중 하나로 변경할 수 있습니다. 
    - [디렉터리의 사용자](#for-users-in-your-directory) 
    - [디렉터리에 없는 사용자의 경우](#for-users-not-in-your-directory)
    - [없음 (관리자 직접 할당에만 해당)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>디렉터리의 사용자

디렉터리의 사용자가이 액세스 패키지를 요청할 수 있도록 하려면 다음 단계를 수행 합니다. 요청 정책을 정의할 때 개별 사용자 또는 보다 일반적으로 사용자 그룹을 지정할 수 있습니다. 예를 들어 조직에 이미 **모든 직원과**같은 그룹이 있을 수 있습니다.  액세스를 요청할 수 있는 사용자에 대 한 정책에 해당 그룹이 추가 되 면 해당 그룹의 모든 멤버는 액세스를 요청할 수 있습니다.

1. **Users who can request access**(액세스를 요청할 수 있는 사용자) 섹션에서 **For users in your directory**(디렉터리에 있는 사용자의 경우) 클릭합니다.

    이 옵션을 선택 하면 디렉터리에서이 액세스 패키지를 요청할 수 있는 사용자를 보다 구체화 하는 새로운 옵션이 표시 됩니다.

    ![액세스 패키지-요청-디렉터리의 사용자](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. 다음 옵션 중 하나를 선택합니다.

    |  |  |
    | --- | --- |
    | **특정 사용자 및 그룹** | 이 액세스 패키지를 요청할 수 있도록 지정 하는 디렉터리의 사용자 및 그룹만 하려면이 옵션을 선택 합니다. |
    | **모든 구성원 (게스트 제외)** | 디렉터리의 모든 구성원 사용자가이 액세스 패키지를 요청할 수 있도록 하려면이 옵션을 선택 합니다. 이 옵션에는 디렉터리에 초대 했을 수 있는 게스트 사용자가 포함 되지 않습니다. |
    | **모든 사용자 (게스트 포함)** | 디렉터리의 모든 구성원 사용자와 게스트 사용자가이 액세스 패키지를 요청할 수 있도록 하려면이 옵션을 선택 합니다. |

    게스트 사용자는 [AZURE AD B2B](../external-identities/what-is-b2b.md)를 사용 하 여 디렉터리에 초대 된 외부 사용자를 참조 합니다. 멤버 사용자와 게스트 사용자 간의 차이점에 대 한 자세한 내용은 [Azure Active Directory?의 기본 사용자 권한](../fundamentals/users-default-permissions.md)을 참조 하십시오.

1. **특정 사용자 및 그룹**을 선택한 경우 **사용자 및 그룹 추가**를 클릭 합니다.

1. 사용자 및 그룹 선택 창에서 추가 하려는 사용자 및 그룹을 선택 합니다.

    ![액세스 패키지-요청-사용자 및 그룹 선택](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. **선택** 을 클릭 하 여 사용자 및 그룹을 추가 합니다.

1. 승인이 필요한 경우 [AZURE AD 자격 관리에서 액세스 패키지에 대 한 승인 설정 변경](entitlement-management-access-package-approval-policy.md) 의 단계를 사용 하 여 승인 설정을 구성 합니다.

1. [요청 활성화](#enable-requests) 섹션으로 이동 합니다.
 
## <a name="for-users-not-in-your-directory"></a>디렉터리에 없는 사용자의 경우

 **디렉터리에 없는 사용자** 는 다른 Azure AD 디렉터리 또는 도메인에 있는 사용자를 의미 합니다. 이러한 사용자는 아직 디렉터리에 초대 되지 않았을 수 있습니다. **공동 작업 제한**에서 초대를 허용 하도록 Azure AD 디렉터리를 구성 해야 합니다. 자세한 내용은 [B2B 외부 공동 작업 사용 및 게스트를 초대할 수 있는 사용자 관리](../external-identities/delegate-invitations.md)를 참조 하세요.

> [!NOTE]
> 요청을 승인 하거나 자동으로 승인 하는 디렉터리에 아직 없는 사용자에 대 한 게스트 사용자 계정이 생성 됩니다. 게스트가 초대 되지만 초대 전자 메일은 수신 되지 않습니다. 대신, 액세스 패키지 할당이 전달 되 면 전자 메일을 받게 됩니다. 기본적으로 나중에 해당 게스트 사용자에 게 액세스 패키지 할당이 더 이상 없는 경우 마지막 할당이 만료 되었거나 취소 되었으므로 게스트 사용자 계정은 로그인 하 여 이후에 삭제 됩니다. 게스트 사용자에 게 무제한으로 디렉터리에 남아 있는 경우 액세스 패키지 할당이 없더라도 자격 관리 구성에 대 한 설정을 변경할 수 있습니다. 게스트 사용자 개체에 대 한 자세한 내용은 [AZURE ACTIVE DIRECTORY B2B 공동 작업 사용자의 속성](../external-identities/user-properties.md)을 참조 하세요.

디렉터리에 없는 사용자가이 액세스 패키지를 요청할 수 있도록 허용 하려면 다음 단계를 수행 합니다.

1. 액세스를 **요청할 수 있는 사용자** 섹션에서 **디렉터리에 없는 사용자를**클릭 합니다.

    이 옵션을 선택 하면 새 옵션이 표시 됩니다.

    ![액세스 패키지-요청-디렉터리에 없는 사용자](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. 다음 옵션 중 하나를 선택합니다.

    |  |  |
    | --- | --- |
    | **연결 된 특정 조직** | 관리자가 이전에 추가한 조직 목록에서 선택 하려면이 옵션을 선택 합니다. 선택한 조직의 모든 사용자가이 액세스 패키지를 요청할 수 있습니다. |
    | **연결 된 모든 조직** | 모든 연결 된 조직의 모든 사용자가이 액세스 패키지를 요청할 수 있는 경우이 옵션을 선택 합니다. |
    | **모든 사용자 (연결 된 모든 조직 + 모든 새 외부 사용자)** | 모든 연결 된 조직의 모든 사용자가이 액세스 패키지를 요청 하 고 B2B 허용 또는 거부 목록 설정이 새 외부 사용자에 우선 적용 되도록 하려면이 옵션을 선택 합니다. |

    연결 된 조직은 외부 Azure AD 디렉터리 또는 관계가 있는 도메인입니다.

1. **연결 된 특정 조직을**선택한 경우 **디렉터리 추가** 를 클릭 하 여 관리자가 이전에 추가한 연결 된 조직의 목록에서 선택 합니다.

1. 이전에 연결 된 조직을 검색할 이름 또는 도메인 이름을 입력 합니다.

    ![액세스 패키지-요청-디렉터리 선택](./media/entitlement-management-access-package-request-policy/select-directories.png)

    공동 작업 하려는 조직이 목록에 없는 경우 관리자에 게 연결 된 조직으로 추가 하도록 요청할 수 있습니다. 자세한 내용은 [연결 된 조직 추가](entitlement-management-organization.md)를 참조 하세요.

1. 모든 연결 된 조직을 선택 했으면 **선택**을 클릭 합니다.

    > [!NOTE]
    > 선택한 연결 된 조직의 모든 사용자가이 액세스 패키지를 요청할 수 있습니다. 이러한 도메인은 Azure B2B 허용 또는 거부 목록에 의해 차단 되는 경우를 제외 하 고 조직과 연결 된 모든 하위 도메인에서 Azure AD의 사용자를 포함 합니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](../external-identities/allow-deny-list.md)을 참조하세요.

1. 승인이 필요한 경우 [AZURE AD 자격 관리에서 액세스 패키지에 대 한 승인 설정 변경](entitlement-management-access-package-approval-policy.md) 의 단계를 사용 하 여 승인 설정을 구성 합니다.
 
1. [요청 활성화](#enable-requests) 섹션으로 이동 합니다.

## <a name="none-administrator-direct-assignments-only"></a>없음 (관리자 직접 할당에만 해당)

액세스 요청을 무시 하 고 관리자가이 액세스 패키지에 특정 사용자를 직접 할당할 수 있게 하려면 다음 단계를 수행 합니다. 사용자는 액세스 패키지를 요청할 필요가 없습니다. 계속 해 서 수명 주기 설정을 지정할 수 있지만 요청 설정이 없습니다.

1. 액세스를 **요청할 수 있는 사용자** 섹션에서 없음을 클릭 합니다 **(관리자 직접 할당에만 해당)**.

    ![액세스 패키지-요청-없음 관리자 직접 할당만](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    액세스 패키지를 만든 후에는 액세스 패키지에 특정 내부 및 외부 사용자를 직접 할당할 수 있습니다. 외부 사용자를 지정 하는 경우 게스트 사용자 계정이 디렉터리에 생성 됩니다. 사용자를 직접 할당 하는 방법에 대 한 자세한 내용은 [액세스 패키지에 대 한 할당 보기, 추가 및 제거](entitlement-management-access-package-assignments.md)를 참조 하세요.

1. [요청 활성화](#enable-requests) 섹션으로 건너뜁니다.


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>요청 설정의 기존 정책 열기 및 편집

액세스 패키지에 대 한 요청 및 승인 설정을 변경 하려면 해당 정책을 열어야 합니다. 다음 단계를 수행 하 여 액세스 패키지에 대 한 요청 설정을 열고 편집 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 편집 하려는 정책을 클릭 합니다.

    정책 세부 정보 창이 페이지 아래쪽에 열립니다.

    ![액세스 패키지-정책 정보 창](./media/entitlement-management-shared/policy-details.png)

1. 정책을 편집 하려면 **편집** 을 클릭 합니다.

    ![액세스 패키지-정책 편집](./media/entitlement-management-shared/policy-edit.png)

1. **요청 탭을** 클릭 하 여 요청 설정을 엽니다.

1. 필요에 따라 요청 설정을 변경 하려면 이전 섹션의 단계를 사용 합니다.

1. [요청 활성화](#enable-requests) 섹션으로 이동 합니다.

## <a name="enable-requests"></a>요청 사용

1. 요청 정책에 있는 사용자가 액세스 패키지를 즉시 사용할 수 있게 하려면 사용 설정/해제를 **예**로 이동 합니다.

    액세스 패키지 만들기를 완료 한 후에는 나중에 언제 든 지 사용할 수 있습니다.

    **없음 (관리자 직접 할당에만 해당)** 을 선택 하 고 사용을 **아니요**로 설정 하면 관리자가이 액세스 패키지를 직접 할당할 수 없습니다.

    ![액세스 패키지-정책-정책 설정 사용](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **다음**을 클릭합니다.

1. 액세스 패키지에 대 한 액세스를 요청할 때 요청자에 게 추가 정보를 제공 하도록 요구 하려면의 단계를 사용 []() 하 여 요청자 정보를 구성 합니다 (미리 보기).

1. 수명 주기 설정을 구성 합니다.

1. 정책을 편집 하는 경우 **업데이트**를 클릭 합니다. 새 정책을 추가 하는 경우 **만들기**를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 승인 설정 변경](entitlement-management-access-package-approval-policy.md)
- [액세스 패키지의 수명 주기 설정 변경](entitlement-management-access-package-lifecycle-policy.md)
- [액세스 패키지에 대 한 요청 보기](entitlement-management-access-package-requests.md)