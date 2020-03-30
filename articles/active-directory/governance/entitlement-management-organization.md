---
title: Azure AD 권한 관리 - Azure Active Directory에 연결된 조직 추가
description: 조직에서 공동 작업을 수행할 수 있도록 조직 외부의 사용자가 액세스 패키지를 요청하도록 허용하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128604"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 연결된 조직 추가

Azure Active Directory(Azure AD) 권한 관리를 사용하면 조직 외부의 사용자와 공동 작업할 수 있습니다. 외부 Azure AD 디렉터리 또는 도메인의 사용자와 자주 공동 작업하는 경우 연결된 조직으로 추가할 수 있습니다. 이 문서에서는 조직 외부의 사용자가 디렉터리에서 리소스를 요청할 수 있도록 연결된 조직을 추가하는 방법에 대해 설명합니다.

## <a name="what-is-a-connected-organization"></a>연결된 조직이란 무엇입니까?

연결된 조직은 연결된 외부 Azure AD 디렉터리 또는 도메인과 관계를 맺고 있습니다.

예를 들어 Woodgrove 은행에서 근무하고 두 외부 조직과 공동 작업을 수행한다고 가정해 보겠습니다. 이 두 조직은 서로 다른 구성을 갖습니다.

- 그래픽 디자인 연구소는 Azure AD를 사용하며 해당 *사용자에게는 graphicdesigninstitute.com*로 끝나는 사용자 주체 이름이 있습니다.
- Contoso는 아직 Azure AD를 사용하지 않습니다. Contoso 사용자에게는 *contoso.com*로 끝나는 사용자 주체 이름이 있습니다.

이 경우 연결된 두 조직을 구성할 수 있습니다. 그래픽 디자인 연구소에 대해 연결된 조직과 Contoso용 조직을 만듭니다. 그런 다음 연결된 두 조직을 정책에 추가하면 정책과 일치하는 사용자 주체 이름을 가진 각 조직의 사용자가 액세스 패키지를 요청할 수 있습니다. *graphicdesigninstitute.com* 도메인이 있는 사용자 주체 이름을 가진 사용자는 그래픽 디자인 연구소에 연결된 조직과 일치하며 요청을 제출할 수 있습니다. *contoso.com* 도메인이 있는 사용자 주체 이름을 가진 사용자는 Contoso 연결 조직과 일치하며 패키지를 요청할 수도 있습니다. 또한 그래픽 디자인 연구소는 Azure AD를 사용하기 때문에 *graphicdesigninstitute.example와*같이 테넌트에 [추가된 검증된 도메인과](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) 일치하는 주체 이름을 가진 모든 사용자는 동일한 정책을 사용하여 액세스 패키지를 요청할 수도 있습니다.

![연결된 조직 예제](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 디렉터리 또는 도메인 인증의 사용자는 인증 유형에 따라 다릅니다. 연결된 조직의 인증 유형은 다음과 같습니다.

- Azure AD
- [직접 페더레이션](../b2b/direct-federation.md)
- [일회용](../b2b/one-time-passcode.md) 암호(도메인)

연결된 조직을 추가하는 방법에 대한 데모를 보려면 다음 비디오를 시청하십시오.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>연결된 조직 추가

외부 Azure AD 디렉터리 또는 도메인을 연결된 조직으로 추가하려면 이 섹션의 지침을 따르십시오.

**필수 구성 조건 역할**: *글로벌 관리자,* *사용자 관리자*또는 *게스트 초대자*

1. Azure 포털에서 **Azure Active 디렉터리를**선택한 다음 **ID 거버넌스를**선택합니다.

1. 왼쪽 창에서 연결된 **조직**을 선택한 다음 **연결된 조직 추가를**선택합니다.

    !["연결된 조직 추가" 버튼](./media/entitlement-management-organization/connected-organization.png)

1. 기본 탭을 선택한 다음 조직의 표시 이름과 설명을 **입력합니다.**

    !["연결된 조직 추가" 기본 창](./media/entitlement-management-organization/organization-basics.png)

1. **디렉터리 + 도메인** 탭을 선택한 다음 **디렉터리 + 도메인 추가를**선택합니다.

    **디렉터리 선택 + 도메인 창이** 열립니다.

1. 검색 상자에 Azure AD 디렉터리 또는 도메인을 검색할 도메인 이름을 입력합니다. 전체 도메인 이름을 입력해야 합니다.

1. 조직 이름 및 인증 유형이 올바른지 확인합니다. 사용자가 로그인하는 방법은 인증 유형에 따라 다릅니다.

    !["디렉터리 선택 + 도메인" 창](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Azure AD 디렉터리 또는 도메인을 추가하려면 **추가를** 선택합니다. 현재 연결된 조직당 Azure AD 디렉터리 또는 도메인을 하나만 추가할 수 있습니다.

    > [!NOTE]
    > Azure AD 디렉터리 또는 도메인의 모든 사용자는 이 액세스 패키지를 요청할 수 있습니다. 여기에는 Azure AD 비즈니스에서 비즈니스로 의한 도메인이 차단되지 않는 한 디렉터리와 연결된 모든 하위 도메인의 Azure AD 사용자(B2B)가 허용하거나 거부목록으로 포함합니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](../b2b/allow-deny-list.md)을 참조하세요.

1. Azure AD 디렉터리 또는 도메인을 추가한 후 **을 선택합니다.**

    조직이 목록에 나타납니다.

    !["디렉터리 + 도메인" 창](./media/entitlement-management-organization/organization-directory-domain.png)

1. 스폰서 탭을 선택한 다음 이 연결된 조직에 대한 선택적 스폰서를 **추가합니다.**

    스폰서는 이 연결된 조직과의 관계에 대한 연락 지점인 디렉터리에 이미 있는 내부 또는 외부 사용자입니다. 내부 스폰서는 디렉터리에서 구성원 사용자입니다. 외부 스폰서는 이전에 초대되어 디렉터리에 이미 있는 연결된 조직의 게스트 사용자입니다. 이 연결된 조직의 사용자가 이 액세스 패키지에 대한 액세스를 요청할 때 스폰서를 승인자로 사용할 수 있습니다. 디렉터리에 게스트 사용자를 초대하는 방법에 대한 자세한 내용은 [Azure 포털의 Azure Active Directory B2B 공동 작업 사용자 추가를](../b2b/add-users-administrator.md)참조하십시오.

    **추가/제거를**선택하면 내부 또는 외부 스폰서를 선택할 수 있는 창이 열립니다. 창에는 디렉터리에서 필터링되지 않은 사용자 및 그룹 목록이 표시됩니다.

    ![스폰서 창](./media/entitlement-management-organization/organization-sponsors.png)

1. 검토 **+ 만들기** 탭을 선택하고 조직 설정을 검토한 다음 에서 **만들기를**선택합니다.

    !["검토 + 만들기" 창](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>연결된 조직 업데이트 

연결된 조직이 다른 도메인으로 변경하거나 조직의 이름이 변경하거나 스폰서를 변경하려는 경우 이 섹션의 지침에 따라 연결된 조직을 업데이트할 수 있습니다.

**필수 구성 조건 역할**: *글로벌 관리자,* *사용자 관리자*또는 *게스트 초대자*

1. Azure 포털에서 **Azure Active 디렉터리를**선택한 다음 **ID 거버넌스를**선택합니다.

1. 왼쪽 창에서 연결된 **조직을**선택한 다음 연결된 조직을 선택하여 엽니다.

1. 연결된 조직의 개요 창에서 **편집을** 선택하여 조직 이름 또는 설명을 변경합니다.  

1. **디렉터리 + 도메인** 창에서 **디렉터리 + 도메인 업데이트를** 선택하여 다른 디렉터리 또는 도메인으로 변경합니다.

1. 스폰서 창에서 **내부 스폰서 추가** 또는 **외부 스폰서 추가를** 선택하여 사용자를 스폰서로 **추가합니다.** 스폰서를 제거하려면 스폰서를 선택하고 오른쪽 창에서 **삭제를**선택합니다.


## <a name="delete-a-connected-organization"></a>연결된 조직 삭제

외부 Azure AD 디렉터리 또는 도메인과의 관계가 더 이상 없는 경우 연결된 조직을 삭제할 수 있습니다.

**필수 구성 조건 역할**: *글로벌 관리자,* *사용자 관리자*또는 *게스트 초대자*

1. Azure 포털에서 **Azure Active 디렉터리를**선택한 다음 **ID 거버넌스를**선택합니다.

1. 왼쪽 창에서 연결된 **조직을**선택한 다음 연결된 조직을 선택하여 엽니다.

1. 연결된 조직의 개요 창에서 **삭제를** 선택하여 삭제합니다.

    현재 연결된 사용자가 없는 경우에만 연결된 조직을 삭제할 수 있습니다.

    ![연결된 조직 삭제 버튼](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>다음 단계

- [외부 사용자를 위한 액세스 제어](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [디렉터리에 없는 사용자에 대한 액세스 관리](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
