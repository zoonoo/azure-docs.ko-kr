---
title: 포함 파일
description: 포함 파일
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 78a0dafeedc9aac4db69903b9f1193574cbd39c7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934742"
---
## <a name="for-users-in-your-directory"></a>디렉터리의 사용자

디렉터리의 사용자가이 액세스 패키지를 요청할 수 있도록 하려면 다음 단계를 수행 합니다. 요청 정책을 정의할 때 개별 사용자 또는 보다 일반적으로 사용자 그룹을 지정할 수 있습니다. 예를 들어 조직에 이미 **모든 직원과**같은 그룹이 있을 수 있습니다.  액세스를 요청할 수 있는 사용자에 대 한 정책에 해당 그룹이 추가 되 면 해당 그룹의 모든 멤버는 액세스를 요청할 수 있습니다.

1. **Users who can request access**(액세스를 요청할 수 있는 사용자) 섹션에서 **For users in your directory**(디렉터리에 있는 사용자의 경우) 클릭합니다.

    이 옵션을 선택 하면 디렉터리에서이 액세스 패키지를 요청할 수 있는 사용자를 보다 구체화 하는 새로운 옵션이 표시 됩니다.

    ![액세스 패키지-요청-디렉터리의 사용자](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 다음 옵션 중 하나를 선택합니다.

    |  |  |
    | --- | --- |
    | **특정 사용자 및 그룹** | 이 액세스 패키지를 요청할 수 있도록 지정 하는 디렉터리의 사용자 및 그룹만 하려면이 옵션을 선택 합니다. |
    | **모든 구성원 (게스트 제외)** | 디렉터리의 모든 구성원 사용자가이 액세스 패키지를 요청할 수 있도록 하려면이 옵션을 선택 합니다. 이 옵션에는 디렉터리에 초대 했을 수 있는 게스트 사용자가 포함 되지 않습니다. |
    | **모든 사용자 (게스트 포함)** | 디렉터리의 모든 구성원 사용자와 게스트 사용자가이 액세스 패키지를 요청할 수 있도록 하려면이 옵션을 선택 합니다. |

    게스트 사용자는 [AZURE AD B2B](../articles/active-directory/b2b/what-is-b2b.md)를 사용 하 여 디렉터리에 초대 된 외부 사용자를 참조 합니다. 멤버 사용자와 게스트 사용자 간의 차이점에 대 한 자세한 내용은 [Azure Active Directory?의 기본 사용자 권한](../articles/active-directory/fundamentals/users-default-permissions.md)을 참조 하십시오.

1. **특정 사용자 및 그룹**을 선택한 경우 **사용자 및 그룹 추가**를 클릭 합니다.

1. 사용자 및 그룹 선택 창에서 추가 하려는 사용자 및 그룹을 선택 합니다.

    ![액세스 패키지-요청-사용자 및 그룹 선택](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. **선택** 을 클릭 하 여 사용자 및 그룹을 추가 합니다.

1. [승인](#approval) 섹션으로 건너뜁니다.

## <a name="for-users-not-in-your-directory"></a>디렉터리에 없는 사용자의 경우

디렉터리에 없는 사용자가이 액세스 패키지를 요청할 수 있도록 허용 하려는 경우 다음 단계를 수행 합니다. **디렉터리에 없는 사용자** 는 다른 Azure AD 디렉터리 또는 도메인에 있는 사용자를 가리키며 디렉터리에 아직 초대 되지 않았을 수 있습니다. **공동 작업 제한**에서 초대를 허용 하도록 Azure AD 디렉터리를 구성 해야 합니다. 자세한 내용은 [B2B 외부 공동 작업 사용 및 게스트를 초대할 수 있는 사용자 관리](../articles/active-directory/b2b/delegate-invitations.md)를 참조 하세요.

> [!NOTE]
> 요청을 승인 하거나 자동으로 승인 하는 디렉터리에 아직 없는 사용자에 대 한 게스트 사용자 계정이 생성 됩니다. 게스트가 초대 되지만 초대 전자 메일은 수신 되지 않습니다. 대신, 액세스 패키지 할당이 전달 되 면 전자 메일을 받게 됩니다. 기본적으로 나중에 해당 게스트 사용자에 게 액세스 패키지 할당이 더 이상 없는 경우 마지막 할당이 만료 되었거나 취소 되었으므로 게스트 사용자 계정은 로그인 하 여 이후에 삭제 됩니다. 게스트 사용자에 게 무제한으로 디렉터리에 남아 있는 경우 액세스 패키지 할당이 없더라도 자격 관리 구성에 대 한 설정을 변경할 수 있습니다. 게스트 사용자 개체에 대 한 자세한 내용은 [AZURE ACTIVE DIRECTORY B2B 공동 작업 사용자의 속성](../articles/active-directory/b2b/user-properties.md)을 참조 하세요.

1. 액세스를 **요청할 수 있는 사용자** 섹션에서 **디렉터리에 없는 사용자를**클릭 합니다.

    이 옵션을 선택 하면 새 옵션이 표시 됩니다.

    ![액세스 패키지-요청-디렉터리에 없는 사용자](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 다음 옵션 중 하나를 선택합니다.

    |  |  |
    | --- | --- |
    | **연결 된 특정 조직** | 관리자가 이전에 추가한 조직 목록에서 선택 하려면이 옵션을 선택 합니다. 선택한 조직의 모든 사용자가이 액세스 패키지를 요청할 수 있습니다. |
    | **연결 된 모든 조직** | 모든 연결 된 조직의 모든 사용자가이 액세스 패키지를 요청할 수 있도록 하려면이 옵션을 선택 합니다. |

    연결 된 조직은 외부 Azure AD 디렉터리 또는 관계가 있는 도메인입니다.

1. **연결 된 특정 조직을**선택한 경우 **디렉터리 추가** 를 클릭 하 여 관리자가 이전에 추가한 연결 된 조직의 목록에서 선택 합니다.

1. 이전에 연결 된 조직을 검색할 이름 또는 도메인 이름을 입력 합니다.

    ![액세스 패키지-요청-디렉터리 선택](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    공동 작업 하려는 조직이 목록에 없는 경우 관리자에 게 연결 된 조직으로 추가 하도록 요청할 수 있습니다. 자세한 내용은 [연결 된 조직 추가](../articles/active-directory/governance/entitlement-management-organization.md)를 참조 하세요.

1. 모든 연결 된 조직을 선택 했으면 **선택**을 클릭 합니다.

    > [!NOTE]
    > 선택한 연결 된 조직의 모든 사용자가이 액세스 패키지를 요청할 수 있습니다. 이러한 도메인은 Azure B2B 허용 또는 거부 목록에 의해 차단 되는 경우를 제외 하 고 조직과 연결 된 모든 하위 도메인에서 Azure AD의 사용자를 포함 합니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](../articles/active-directory/b2b/allow-deny-list.md)을 참조하세요.

1. [승인](#approval) 섹션으로 건너뜁니다.

## <a name="none-administrator-direct-assignments-only"></a>없음 (관리자 직접 할당에만 해당)

액세스 요청을 무시 하 고 관리자가이 액세스 패키지에 특정 사용자를 직접 할당할 수 있게 하려면 다음 단계를 수행 합니다. 사용자는 액세스 패키지를 요청할 필요가 없습니다. 계속 해 서 수명 주기 설정을 지정할 수 있지만 요청 설정이 없습니다.

1. 액세스를 **요청할 수 있는 사용자** 섹션에서 없음을 클릭 합니다 **(관리자 직접 할당에만 해당)** .

    ![액세스 패키지-요청-없음 관리자 직접 할당만](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    액세스 패키지를 만든 후에는 액세스 패키지에 특정 내부 및 외부 사용자를 직접 할당할 수 있습니다. 외부 사용자를 지정 하는 경우 게스트 사용자 계정이 디렉터리에 생성 됩니다. 사용자를 직접 할당 하는 방법에 대 한 자세한 내용은 [액세스 패키지에 대 한 할당 보기, 추가 및 제거](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)를 참조 하세요.

1. [요청 활성화](#enable-requests) 섹션으로 건너뜁니다.

## <a name="approval"></a>승인

승인 섹션에서 사용자가이 액세스 패키지를 요청할 때 승인이 필요한 지 여부를 지정 합니다. 승인 설정은 다음과 같은 방식으로 작동 합니다.

- 선택한 승인자 또는 대체 승인자 중 하나만 요청을 승인 해야 합니다. 모든 승인자의 승인이 필요 하지는 않습니다.
- 승인 결정은 먼저 요청을 검토 하는 승인자에 따라 결정 됩니다.

이전에 선택한 사용자의 승인 설정을 지정 하려면 다음 단계를 수행 합니다.

1. 선택한 사용자의 요청에 대 한 승인을 요청 하려면 **승인 필요** 를 **예**로 설정 합니다. 요청을 자동으로 승인 하려면 토글을 **아니요**로 설정 합니다.

    ![액세스 패키지-요청-승인 설정](./media/active-directory-entitlement-management-request-policy/approval.png)

1. 사용자가 액세스 패키지를 요청 하는 근거를 제공 하도록 요구 하려면 **요청자 사유 필요** 를 **예**로 설정 합니다.

1. 요청에 단일 또는 다단계 승인이 필요한 지 여부를 결정 합니다. 단일 단계에 대해 전환 하 **는 단계 수** 를 **1** 로 설정 합니다.

1. 승인자의 경우 **관리자를 승인자로** 선택 하거나 **특정 승인자를 선택**합니다.

    관리자는 Azure AD의 사용자 프로필에 있는 **manager** 특성에 의해 결정 됩니다. 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자의 프로필 정보 추가 또는 업데이트](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)를 참조 하세요.

    ![사용자 프로필-관리자 특성 Azure Active Directory](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. 관리자를 승인자로 선택한 경우 자격 관리에서 관리자를 찾을 수 없는 경우 대체 승인자가 될 디렉터리에서 하나 이상의 사용자 또는 그룹을 선택 하려면 **대체 (fallback) 추가** 를 클릭 합니다.

1. 특정 승인자 선택을 선택한 경우 승인자 **추가** 를 클릭 하 여 승인자가 될 디렉터리에서 하나 이상의 사용자 또는 그룹을 선택 합니다.

1. 결정은 일 수에 대 한 **결정을 내려야 하며**승인자가이 액세스 패키지에 대 한 요청을 검토 해야 하는 일 수를 지정 합니다.

    이 기간 내에 승인 되지 않은 요청은 자동으로 거부 됩니다. 사용자는 액세스 패키지에 대 한 다른 요청을 제출 해야 합니다.

1. 사용자가 액세스 패키지를 요청 하는 근거를 제공 하도록 요구 하려면 **사유 필요** 를 **예**로 설정 합니다.

    다른 승인자와 요청자에 게 근거를 표시 합니다.

## <a name="enable-requests"></a>요청 사용

1. 요청할 요청 정책에 있는 사용자가 액세스 패키지를 즉시 사용할 수 있도록 하려면 **예** 를 클릭 하 여 사용 하도록 설정 합니다.

    액세스 패키지 만들기를 완료 한 후에는 나중에 언제 든 지 사용할 수 있습니다.

    **없음 (관리자 직접 할당에만 해당)** 을 선택 하 고 사용을 **아니요**로 설정 하면 관리자가이 액세스 패키지를 직접 할당할 수 없습니다.

    ![액세스 패키지-정책-정책 설정 사용](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. **다음**을 누릅니다.
