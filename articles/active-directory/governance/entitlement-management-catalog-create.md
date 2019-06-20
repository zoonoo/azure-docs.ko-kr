---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 카탈로그를 만들고 설정 합니다.
description: Azure Active Directory 권한 관리 (미리 보기)에서 리소스 및 액세스 패키지의 새 컨테이너를 만드는 방법에 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/29/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5988f4723f1ef73cf0767ef8ac1b9adf3c1435d
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190236"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리 (미리 보기)의 카탈로그를 만들고 설정 합니다.

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="create-a-catalog"></a>카탈로그 만들기

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 된 리소스 그룹 및 패키지에 액세스 하려는 경우에 카탈로그를 만듭니다. 첫 번째 카탈로그 소유자가 됩니다 누구 든 지 카탈로그를 만듭니다. 카탈로그 소유자 추가 카탈로그 소유자를 추가할 수 있습니다.

**필수 역할:** 사용자 관리자 또는 카탈로그를 만든

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **카탈로그**합니다.

    ![Azure portal에서 권한 관리 카탈로그](./media/entitlement-management-catalog-create/catalogs.png)

1. 클릭 **새 카탈로그**합니다.

1. 카탈로그에 대 한 고유한 이름을 입력 하 고 설명을 제공 합니다.

    사용자는이 정보는 액세스 패키지의 세부 정보에 표시 됩니다.

1. 사용자가 만들 때 요청에 대 한 사용 가능 하도록이 카탈로그에 액세스 패키지를 원한다 면 설정 **사용** 하 **예**합니다.

1. 이 카탈로그에서 패키지 액세스를 요청 하려면 설정할 수 있게 되기를 외부 선택한 디렉터리에서 사용자를 허용 하려는 경우 **외부 사용자가** 하 **예**합니다.

    ![새 카탈로그 창](./media/entitlement-management-catalog-create/new-catalog.png)

1. 클릭 **만들기** 카탈로그를 만들어야 합니다.

## <a name="add-resources-to-a-catalog"></a>카탈로그에 리소스 추가

리소스는 리소스에 액세스 패키지를 포함 하려면 카탈로그에 존재 해야 합니다. 추가할 수 있는 리소스의 유형은 그룹, 응용 프로그램 및 SharePoint Online 사이트입니다. 그룹 클라우드 만든 Office 365 그룹 또는 Azure 클라우드를 만들 수 AD 보안 그룹입니다. 응용 프로그램은 SaaS 응용 프로그램과 Azure AD에 페더레이션 응용 프로그램을 포함 하 여 Azure AD 엔터프라이즈 응용 프로그램을 수 있습니다. 사이트는 SharePoint Online 사이트 또는 SharePoint Online 사이트 모음 수 있습니다.

**필수 역할:** 참조 [역할 리소스 카탈로그를 추가 하는 데 필요한](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **카탈로그** 다음 리소스를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **리소스**합니다.

1. 클릭 **리소스 추가**합니다.

1. 리소스 종류를 클릭 합니다. **그룹**하십시오 **응용 프로그램**, 또는 **SharePoint 사이트**합니다.

    추가 하려는 리소스를이 표시 되지 않는 경우 리소스를 추가할 수 없는 했는지 필요한 Azure AD 디렉터리 역할 및 권한 관리 역할입니다. 다른 사람이에 필요한 역할을 사용 하 여 리소스 카탈로그를 추가 해야 합니다. 자세한 내용은 [역할을 카탈로그 리소스를 추가 하는 데 필요한](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)합니다.

1. 카탈로그에 추가 하려면 원하는 종류의 하나 이상의 리소스를 선택 합니다.

1. 완료 되 면, 클릭 **추가**합니다.

    이러한 리소스는 이제 카탈로그 내에서 액세스 패키지에 포함할 수 있습니다.

## <a name="remove-resources-from-a-catalog"></a>카탈로그에서 리소스를 제거 합니다.

카탈로그에서 리소스를 제거할 수 있습니다. 카탈로그의 액세스 패키지에서 사용 하지 않으면 카탈로그에서 리소스를 제거할만 있습니다.

**필수 역할:** 참조 [역할 리소스 카탈로그를 추가 하는 데 필요한](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **카탈로그** 다음 리소스를 제거 하려면 카탈로그를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **리소스**합니다.

1. 제거 하려는 리소스를 선택 합니다.

1. 클릭 **제거할** (하거나 줄임표 ( **...** )을 클릭 한 다음 **리소스를 제거할**).

## <a name="edit-a-catalog"></a>카탈로그를 편집 합니다.

이름 및 카탈로그에 대 한 설명을 편집할 수 있습니다. 사용자는 액세스 패키지의 세부 정보에서이 정보를 참조 하세요.

**필수 역할:** 카탈로그 소유자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **카탈로그** 을 편집 하려면 카탈로그를 엽니다.

1. 카탈로그의에 대해 **개요** 페이지에서 클릭 **편집**합니다.

1. 카탈로그의 이름 또는 설명을 편집 합니다.

1. **저장**을 클릭합니다.

## <a name="delete-a-catalog"></a>카탈로그를 삭제 합니다.

모든 액세스 패키지에 없는 경우에만 카탈로그를 삭제할 수 있습니다.

**필수 역할:** 카탈로그 소유자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **카탈로그** 을 삭제 하려면 카탈로그를 엽니다.

1. 카탈로그의 온 **개요**, 클릭 **삭제**합니다.

1. 표시되는 메시지 상자에서 **예**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [카탈로그 작성자를 추가 합니다.](entitlement-management-delegate.md#add-a-catalog-creator)
- [액세스 패키지 만들기 및 관리](entitlement-management-access-package-create.md)
