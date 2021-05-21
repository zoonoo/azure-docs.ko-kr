---
title: Azure AD 권한 관리에서 연결된 조직 추가 - Azure Active Directory
description: 조직 외부 사용자가 프로젝트에 대한 협업을 수행할 수 있도록 액세스 패키지 요청을 허용하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b6a1ead2fe3c1ec4e2206d1ffbaea4e5ec57433
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222524"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 연결된 조직 추가

Azure AD(Azure Active Directory) 권한 관리를 통해 조직 외부 사용자와 협업을 수행할 수 있습니다. 외부 Azure AD 디렉터리 또는 도메인의 사용자와 협업을 자주 수행하는 경우 연결된 조직으로 추가할 수 있습니다. 이 문서에서는 조직 외부 사용자가 디렉터리의 리소스 요청을 허용할 수 있도록 연결된 조직을 추가하는 방법을 설명합니다.

## <a name="what-is-a-connected-organization"></a>연결된 조직이란?

연결된 조직은 나와 관계가 있는 다른 조직을 말합니다.  해당 조직의 사용자가 SharePoint Online 사이트 또는 앱과 같은 내 리소스에 액세스할 수 있도록 하려면 해당 디렉터리에서 해당 조직의 사용자에 대한 표현이 필요합니다.  대부분의 경우 해당 조직의 사용자는 아직 Azure AD 디렉터리에 없으므로 필요에 따라 권한 관리를 사용하여 Azure AD 디렉터리로 가져올 수 있습니다.  

세 가지 방법으로 권한 관리를 통해 연결된 조직을 형성하는 사용자를 지정할 수 있습니다.  다음과 같습니다.

* 다른 Azure AD 디렉터리의 사용자
* 직접 페더레이션을 위해 구성된 다른 비 Azure AD 디렉터리의 사용자 또는
* 모두 공통된 동일한 도메인 이름을 포함하는 이메일 주소가 있는 다른 비 Azure AD 디렉터리의 사용자

예를 들어, Woodgrove Bank에서 일하고 있고, 두 개의 외부 조직과 협업하려는 경우를 가정해 보겠습니다. 이러한 두 조직의 구성은 다음과 같이 다릅니다.

- Graphic Design Institute는 Azure AD를 사용하며, 사용자는 *graphicdesigninstitute.com* 으로 끝나는 사용자 계정 이름을 갖고 있습니다.
- Contoso는 아직 Azure AD를 사용하지 않습니다. Contoso 사용자에게는 *contoso.com* 으로 끝나는 사용자 계정 이름이 있습니다.

이 경우 연결된 두 조직을 구성할 수 있습니다. Graphic Design Institute용과 Contoso용으로 하나씩 연결된 조직을 만듭니다. 그런 다음, 두 개의 연결된 조직을 정책에 추가하면 정책과 일치하는 사용자 계정 이름을 가진 각 조직의 사용자가 액세스 패키지를 요청할 수 있습니다. contoso.com 도메인이 포함된 사용자 계정 이름을 가진 사용자는 Contoso 연결 조직과 일치하고 패키지 요청이 허용됩니다. *graphicdesigninstitute.com* 도메인이 포함된 사용자 계정 이름을 가진 사용자는 Graphic Design Institute 연결 조직과 일치하고 요청 제출이 허용됩니다. 또한 Graphic Design Institute는 Azure AD를 사용하기 때문에 *graphicdesigninstitute.example* 등 자신들의 테넌트에 추가된 [확인된 도메인](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)과 일치하는 계정 이름을 가진 사용자도 동일한 정책을 사용하여 액세스 패키지를 요청할 수 있습니다. [이메일 OTP(일회용 암호) 인증](../external-identities/one-time-passcode.md)이 설정된 경우 리소스에 액세스할 때 이메일 OTP를 사용하여 인증하는 Azure AD 계정이 아직 없는 도메인의 사용자를 포함합니다. 

![연결된 조직 예제](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 디렉터리 또는 도메인의 사용자가 인증하는 방법은 인증 유형에 따라 달라집니다. 연결된 조직의 인증 유형은 다음과 같습니다.

- Azure AD
- [직접 페더레이션](../external-identities/direct-federation.md)
- [일회성 암호](../external-identities/one-time-passcode.md)(도메인)

연결된 조직을 추가하는 방법에 대한 데모는 다음 비디오를 시청하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>연결된 조직 추가

외부 Azure AD 디렉터리 또는 도메인을 연결된 조직으로 추가하려면 이 섹션의 지침을 따르세요.

**필수 역할**: *전역 관리자* 또는 *사용자 관리자*

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다.

1. 왼쪽 창에서 **연결된 조직** 을 선택한 다음, **연결된 조직 추가** 를 선택합니다.

    ![“연결된 조직 추가” 단추](./media/entitlement-management-organization/connected-organization.png)

1. **기본** 탭을 선택한 다음, 조직의 표시 이름 및 설명을 입력합니다.

    ![“연결된 조직 추가” 기본 창](./media/entitlement-management-organization/organization-basics.png)

1. 새 연결된 조직을 만들 때 상태가 자동으로 **구성됨** 으로 설정됩니다. 상태 속성에 대한 자세한 내용은 [연결된 조직의 상태 속성](#state-properties-of-connected-organizations)을 참조하세요.

1. **디렉터리 + 도메인** 탭을 선택하고 **디렉터리 + 도메인 추가** 를 선택합니다.

    **디렉터리 + 도메인 선택** 창이 열립니다.

1. 검색 상자에 도메인 이름을 입력하여 Azure AD 디렉터리 또는 도메인을 검색합니다. 전체 도메인 이름을 입력해야 합니다.

1. 조직 이름과 인증 유형이 올바른지 확인합니다. 사용자가 로그인하는 방법은 인증 유형에 따라 달라집니다.

    ![“디렉터리 + 도메인 선택” 창](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. **추가** 를 선택하여 Azure AD 디렉터리 또는 도메인을 추가합니다. 현재 연결된 조직마다 하나의 Azure AD 디렉터리 또는 도메인만 추가할 수 있습니다.

    > [!NOTE]
    > Azure AD 디렉터리 또는 도메인의 모든 사용자가 이 액세스 패키지를 요청할 수 있습니다. 여기에는 Azure AD B2B(business to business) 허용 또는 거부 목록에 의해 차단되지 않는 한, 디렉터리와 연결된 모든 하위 도메인에 있는 Azure AD의 사용자가 포함됩니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](../external-identities/allow-deny-list.md)을 참조하세요.

1. Azure AD 디렉터리 또는 도메인을 추가한 후 **선택** 을 선택합니다.

    조직이 목록에 표시됩니다.

    ![“디렉터리 + 도메인” 창](./media/entitlement-management-organization/organization-directory-domain.png)

1. **스폰서** 탭을 선택한 다음, 이 연결된 조직의 스폰서(선택 사항)를 추가합니다.

    스폰서는 이 연결된 조직과의 관계에 대한 연락 지점인 디렉터리에 이미 있는 내부 또는 외부 사용자입니다. 내부 스폰서는 디렉터리의 멤버 사용자입니다. 외부 스폰서는 이전에 초대되었으며 이미 디렉터리에 있는 연결된 조직의 게스트 사용자입니다. 이 연결된 조직의 사용자가 이 액세스 패키지에 대한 액세스를 요청하는 경우 스폰서를 승인자로 사용할 수 있습니다. 디렉터리에 게스트 사용자를 초대하는 방법에 대한 정보는 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../external-identities/add-users-administrator.md)를 참조하세요.

    **추가/제거** 를 선택하면 내부 또는 외부 스폰서를 선택할 수 있는 창이 열립니다. 창에는 디렉터리의 필터링되지 않은 사용자 및 그룹 목록이 표시됩니다.

    ![스폰서 창](./media/entitlement-management-organization/organization-sponsors.png)

1. **검토 + 만들기** 탭을 선택하고 조직 설정을 검토한 다음, **만들기** 를 선택합니다.

    ![“검토 + 만들기” 창](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>연결된 조직 업데이트 

연결된 조직이 다른 도메인으로 변경되거나 조직의 이름이 변경되거나 스폰서를 변경하려는 경우 이 섹션의 지침에 따라 연결된 조직을 업데이트할 수 있습니다.

**필수 역할**: *전역 관리자* 또는 *사용자 관리자*

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다.

1. 왼쪽 창에서 **연결된 조직** 을 선택한 다음, 열 연결된 조직을 선택합니다.

1. 연결된 조직의 개요 창에서 **편집** 을 선택하여 조직 이름, 설명 또는 상태를 변경합니다.  

1. **디렉터리 + 도메인** 창에서 **디렉터리 + 도메인 업데이트** 를 선택하여 다른 디렉터리나 도메인으로 변경합니다.

1. **스폰서** 창에서 **내부 스폰서 추가** 또는 **외부 스폰서 추가** 를 선택하여 사용자를 스폰서로 추가합니다. 스폰서를 제거하려면 스폰서를 선택하고 오른쪽 창에서 **삭제** 를 선택합니다.


## <a name="delete-a-connected-organization"></a>연결된 조직 삭제

외부 Azure AD 디렉터리 또는 도메인과 더 이상 관계가 없는 경우 연결된 조직을 삭제할 수 있습니다.

**필수 역할**: *전역 관리자* 또는 *사용자 관리자*

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다.

1. 왼쪽 창에서 **연결된 조직** 을 선택한 다음, 열 연결된 조직을 선택합니다.

1. 연결된 조직의 개요 창에서 **삭제** 를 선택하여 삭제합니다.

    ![연결된 조직 삭제 단추](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>프로그래밍 방식으로 연결된 조직 관리

또한 Microsoft Graph를 사용하여 연결된 조직을 만들고, 나열하고, 업데이트하고, 삭제할 수 있습니다. 위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 API를 호출하여 [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) 개체를 관리하고 스폰서를 설정할 수 있습니다.

## <a name="state-properties-of-connected-organizations"></a>연결된 조직의 상태 속성

Azure AD 권한 관리에서 연결된 조직의 상태 속성에는 현재 구성되어 있고 제안된 두 가지 유형이 있습니다. 

- 구성된 연결된 조직은 해당 조직 내의 사용자가 액세스 패키지에 액세스할 수 있도록 허용하는 완전한 기능을 갖춘 연결된 조직입니다. 관리자가 Azure Portal에 새로운 연결된 조직을 만드는 경우 관리자가 이 연결된 조직을 만들었고 사용하려고 하므로 기본적으로 **구성됨** 상태가 됩니다. 또한 API를 통해 프로그래밍 방식으로 연결된 조직을 만든 경우에는 명시적으로 다른 상태로 설정하지 않는 한 기본 상태를 **구성해야** 합니다. 

    구성된 연결된 조직은 연결된 조직의 선택기에 표시되고 “모든” 연결된 조직을 대상으로 하는 모든 정책 범위 내에 있게 됩니다.

- 제안된 연결된 구성은 자동으로 생성되었지만 관리자가 조직을 만들거나 승인하지 않은 연결된 조직입니다. 사용자가 구성된 연결된 조직 외부에서 액세스 패키지에 등록하는 경우 테넌트의 관리자가 해당 파트너 관계를 설정하지 않았기 때문에 자동으로 생성된 모든 연결된 조직은 **제안됨** 상태가 됩니다. 
    
    제안된 연결된 조직은 모든 정책에 대한 “구성된 모든 연결된 조직”에 대한 범위에 속하지 않지만 특정 조직을 대상으로 하는 정책에 대해서만 정책에서 사용할 수 있습니다. 

구성된 연결된 조직의 사용자만 구성된 모든 조직의 사용자가 사용할 수 있는 액세스 패키지를 요청할 수 있습니다. 제안된 연결된 조직의 사용자에게는 마치 해당 도메인에 대해 연결된 조직이 없는 것 같은 환경이 제공됩니다. 특정 조직으로 범위가 지정되거나 어떠한 사용자에게 범위가 지정된 액세스 패키지만 보고 요청할 수 있습니다.

> [!NOTE]
> 이 새로운 기능을 롤아웃하는 과정의 일환으로 2020년 9월 9일 이전에 생성된 모든 연결된 조직은 **구성됨** 으로 간주됩니다. 모든 조직의 사용자가 가입할 수 있도록 허용된 액세스 패키지가 있는 경우 해당 날짜 이전에 생성된 연결된 조직의 목록을 검토하여 **구성됨** 으로 잘못 분류되지 않도록 해야 합니다.  관리자는 **상태** 속성을 적절하게 업데이트할 수 있습니다. 지침은 [연결된 조직 업데이트](#update-a-connected-organization)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [외부 사용자를 위한 액세스 제어](./entitlement-management-external-users.md)
- [디렉터리에 없는 사용자에 대한 액세스 거버넌스](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
