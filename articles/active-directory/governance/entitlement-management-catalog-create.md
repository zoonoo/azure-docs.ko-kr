---
title: 권한 부여 관리에서 리소스 카탈로그를 관리하기 & 만들기 - Azure AD
description: Azure Active Directory 권한 관리에서 새 리소스 컨테이너를 만들고 패키지에 액세스하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437548"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 리소스 카탈로그 생성 및 관리

## <a name="create-a-catalog"></a>카탈로그 만들기

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 리소스및 액세스 패키지를 그룹화하려는 경우 카탈로그를 만듭니다. 카탈로그를 만드는 사람은 누구든지 첫 번째 카탈로그 소유자가 됩니다. 카탈로그 소유자는 카탈로그 소유자를 추가할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자 또는 카탈로그 작성자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그 를 클릭합니다.**

    ![Azure 포털의 권한 관리 카탈로그](./media/entitlement-management-catalog-create/catalogs.png)

1. **새 카탈로그를 클릭합니다.**

1. 카탈로그의 고유한 이름을 입력하고 설명을 제공합니다.

    사용자는 액세스 패키지의 세부 정보에서 이 정보를 볼 수 있습니다.

1. 이 카탈로그의 액세스 패키지를 사용자가 만들자마자 요청할 수 있도록 **하려면 사용 설정을** **예로**설정합니다.

1. 선택한 외부 디렉터리에서 사용자가 이 카탈로그에서 액세스 패키지를 요청할 수 있도록 하려면 **외부 사용자에 대해 사용 사용하도록** **설정합니다.**

    ![새 카탈로그 창](./media/entitlement-management-shared/new-catalog.png)

1. **만들기를** 클릭하여 카탈로그를 만듭니다.

### <a name="creating-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그 만들기

Microsoft 그래프를 사용하여 카탈로그를 만들 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 적절한 역할의 사용자는 API를 호출하여 [accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)를 만들 수 있습니다.

## <a name="add-resources-to-a-catalog"></a>카탈로그에 리소스 추가

액세스 패키지에 리소스를 포함하려면 리소스가 카탈로그에 있어야 합니다. 추가할 수 있는 리소스 유형은 그룹, 응용 프로그램 및 SharePoint 온라인 사이트입니다. 그룹은 클라우드에서 만든 Office 365 그룹 또는 클라우드에서 만든 Azure AD 보안 그룹일 수 있습니다. 응용 프로그램은 Azure AD에 페더레이션된 SaaS 응용 프로그램과 사용자 고유의 응용 프로그램을 모두 포함하여 Azure AD 엔터프라이즈 응용 프로그램일 수 있습니다. 사이트는 SharePoint 온라인 사이트 또는 SharePoint 온라인 사이트 모음일 수 있습니다.

**필수 구성 조건 역할:** [카탈로그에 리소스를 추가하는 필수 역할](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) 참조

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 카탈로그를 클릭한 다음 **리소스를** 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스**를 클릭합니다.

1. **리소스 추가를 클릭합니다.**

1. 리소스 유형: **그룹 및 팀,** 응용 프로그램 또는 **SharePoint 사이트를** **클릭합니다.**

    추가하려는 리소스가 표시되지 않거나 리소스를 추가할 수 없는 경우 필요한 Azure AD 디렉터리 역할 및 권한 관리 역할이 있는지 확인합니다. 필요한 역할을 가진 사람이 카탈로그에 리소스를 추가하도록 해야 할 수 있습니다. 자세한 내용은 [카탈로그에 리소스를 추가하는 필수 역할을](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)참조합니다.

1. 카탈로그에 추가할 형식의 리소스를 하나 이상 선택합니다.

    ![카탈로그에 리소스 추가](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 완료되면 **추가를**클릭합니다.

    이제 이러한 리소스를 카탈로그 내의 액세스 패키지에 포함할 수 있습니다.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그에 리소스 추가

Microsoft 그래프를 사용하여 카탈로그에 리소스를 추가할 수도 있습니다.  적절한 역할의 사용자 또는 위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 카탈로그 및 리소스 소유자는 API를 호출하여 [accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)를 만들 수 있습니다.

## <a name="remove-resources-from-a-catalog"></a>카탈로그에서 리소스 제거

카탈로그에서 리소스를 제거할 수 있습니다. 리소스는 카탈로그의 액세스 패키지에서 사용되지 않는 경우에만 카탈로그에서 제거할 수 있습니다.

**필수 구성 조건 역할:** [카탈로그에 리소스를 추가하는 필수 역할](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) 참조

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그를** 클릭한 다음 리소스를 제거할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스**를 클릭합니다.

1. 제거할 리소스를 선택합니다.

1. **제거(또는** 타원 **(~**)를 클릭한 다음 **리소스 제거를**클릭합니다.)

## <a name="add-additional-catalog-owners"></a>카탈로그 소유자 추가

카탈로그를 만든 사용자가 첫 번째 카탈로그 소유자가 됩니다. 카탈로그 관리를 위임하려면 카탈로그 소유자 역할에 사용자를 추가합니다. 이렇게 하면 카탈로그 관리 책임을 공유하는 데 도움이 됩니다. 

다음 단계에 따라 사용자를 카탈로그 소유자 역할에 할당합니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 카탈로그를 클릭한 다음 **관리자를** 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자를**클릭합니다.

    ![역할 및 관리자 카탈로그](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **소유자 추가를** 클릭하여 이러한 역할의 구성원을 선택합니다.

1. 이러한 멤버를 추가하려면 **선택을** 클릭합니다.

## <a name="edit-a-catalog"></a>카탈로그 편집

카탈로그의 이름과 설명을 편집할 수 있습니다. 사용자는 액세스 패키지의 세부 정보에서 이 정보를 볼 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그를** 클릭한 다음 편집할 카탈로그를 엽니다.

1. 카탈로그의 **개요** 페이지에서 **편집을**클릭합니다.

1. 카탈로그 이름, 설명 또는 사용 가능한 설정을 편집합니다.

    ![카탈로그 설정 편집](./media/entitlement-management-shared/catalog-edit.png)

1. **저장**을 클릭합니다.

## <a name="delete-a-catalog"></a>카탈로그 삭제

카탈로그를 삭제할 수 있지만 액세스 패키지가 없는 경우에만 카탈로그를 삭제할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그를** 클릭한 다음 삭제할 카탈로그를 엽니다.

1. 카탈로그 개요에서 **Overview** **에서 삭제를**클릭합니다.

1. 표시되는 메시지 상자에서 **예**를 클릭합니다.

### <a name="deleting-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그 삭제

Microsoft 그래프를 사용하여 카탈로그를 삭제할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램이 있는 적절한 역할의 사용자는 API를 호출하여 [accessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [패키지 관리자에 액세스하기 위해 액세스 거버넌스 위임](entitlement-management-delegate-managers.md)
