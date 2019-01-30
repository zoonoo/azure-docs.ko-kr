---
title: 라이선스 할당 또는 제거 - Azure Active Directory | Microsoft Docs
description: 사용자 또는 그룹에서 Azure Active Directory 라이선스를 할당하거나 제거하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: b7abcb4820dde221f17efa1fcded05df41fa7bed
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449489"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Azure Active Directory 포털을 사용하여 라이선스 할당 또는 제거
많은 Azure AD(Azure Active Directory) 서비스를 사용하려면 Azure AD 제품을 활성화하고 해당 제품에 대한 각 사용자 또는 그룹(및 관련 멤버)에 라이선스를 부여해야 합니다. 활성 라이선스가 있는 사용자만 사용이 허가된 Azure AD 서비스에 액세스하여 사용할 수 있습니다.

## <a name="available-product-editions"></a>사용 가능한 제품 버전
Azure AD 제품에는 사용 가능한 몇 가지 버전이 있습니다.

- Azure AD Free

- Azure AD Basic

- Azure AD P1(Azure AD Premium 1)

- Azure AD P2(Azure AD Premium 2)

각 제품 버전 및 관련 라이선스 세부 정보에 대한 자세한 내용은 [어떤 라이선스가 필요한가요?](../authentication/concept-sspr-licensing.md)를 참조하세요.

## <a name="view-your-product-edition-and-license-details"></a>제품 버전 및 라이선스 세부 정보 보기
개별 라이선스를 포함하여 사용 가능한 제품을 보고, 보류 중인 만료 날짜 및 사용 가능한 할당 수를 확인할 수 있습니다.

### <a name="to-find-your-product-and-license-details"></a>제품 및 라이선스 세부 정보를 찾으려면
1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory**를 선택한 다음, **라이선스**를 선택합니다.

    **라이선스** 페이지가 표시됩니다.

    ![구입한 제품 수와 할당된 라이선스 수를 보여 주는 라이선스 페이지](media/license-users-groups/license-details-blade.png)
    
3. **제품** 페이지를 보고 각 특정 제품 버전에 대한 **할당됨**, **사용 가능** 및 **곧 만료됨** 세부 정보를 확인하려면 **구입한 제품** 링크를 선택합니다.

    ![제품 버전 및 관련 라이선스 정보가 있는 제품 페이지](media/license-users-groups/license-products-blade-with-products.png)

4. 제품 버전 이름을 선택하여 사용이 허가된 사용자 및 그룹을 확인합니다.

## <a name="assign-licenses-to-users-or-groups"></a>사용자 또는 그룹에 라이선스 할당
사용이 허가된 Azure AD 서비스를 사용해야 하는 모든 사용자에게 적절한 라이선스가 있는지 확인합니다. 개별 사용자 또는 전체 그룹에 라이선스 권한을 추가할지 여부는 사용자 자신에게 달려 있습니다.

>[!Note]
>그룹 기반 라이선싱은 Azure AD의 공개 미리 보기 기능이며, 모든 유료 Azure AD 라이선스 계획에서 사용할 수 있습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.<br><br>사용자를 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 사용자를 추가하거나 삭제하는 방법](add-users-azure-active-directory.md)을 참조하세요. 그룹을 만들고 멤버를 추가하는 방법에 대한 자세한 내용은 [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)를 참조하세요.

### <a name="to-assign-a-license-to-a-specific-user"></a>특정 사용자에게 라이선스를 할당하려면
1. **제품** 페이지에서 사용자에게 할당하려는 버전의 이름을 선택합니다. 예를 들어 _Azure Active Directory Premium 요금제 2_입니다.

    ![제품 버전이 강조 표시된 제품 페이지](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. **Azure Active Directory Premium 요금제 2** 페이지에서 **할당**을 선택합니다.

    ![할당 옵션이 강조 표시된 제품 페이지](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. **할당** 페이지에서 **사용자 및 그룹**을 선택한 다음, 라이선스를 할당하려는 사용자를 검색하여 선택합니다. 예를 들어 _Mary Parker_입니다.

    ![검색 및 선택 옵션이 강조 표시된 라이선스 할당 페이지](media/license-users-groups/assign-license-blade-with-highlight.png)

4. **할당 옵션**을 선택하고, 적절한 라이선스 옵션이 설정되어 있는지 확인한 다음, **확인**을 선택합니다.

    ![버전에서 사용 가능한 모든 옵션을 보여 주는 라이선스 옵션 페이지](media/license-users-groups/license-option-blade-assignments.png)

    **라이선스 할당** 페이지가 업데이트되어 사용자가 선택되고 할당이 구성되었음을 보여 줍니다.

    >[!NOTE]
    >일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 **사용 위치**를 지정해야 합니다. 이 값은 Azure AD의 **Azure Active Directory &gt; 사용자 &gt; 프로필 &gt; 설정** 영역에서 설정할 수 있습니다.

5. **할당**을 선택합니다.

    사용자가 사용이 허가된 사용자 목록에 추가되며, 포함된 Azure AD 서비스에 액세스할 수 있습니다.

### <a name="to-assign-a-license-to-an-entire-group"></a>전체 그룹에 라이선스를 할당하려면
1. **제품** 페이지에서 사용자에게 할당하려는 버전의 이름을 선택합니다. 예를 들어 _Azure Active Directory Premium 요금제 2_입니다.

    ![제품 버전이 강조 표시된 제품 블레이드](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. **Azure Active Directory Premium 요금제 2** 페이지에서 **할당**을 선택합니다.

    ![할당 옵션이 강조 표시된 제품 페이지](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. **할당** 페이지에서 **사용자 및 그룹**을 선택한 다음, 라이선스를 할당하려는 그룹을 검색하여 선택합니다. 예를 들어 _MDM 정책 - 서부_입니다.

    ![검색 및 선택 옵션이 강조 표시된 라이선스 할당 페이지](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. **할당 옵션**을 선택하고, 적절한 라이선스 옵션이 설정되어 있는지 확인한 다음, **확인**을 선택합니다.

    ![버전에서 사용 가능한 모든 옵션을 보여 주는 라이선스 옵션 페이지](media/license-users-groups/license-option-blade-group-assignments.png)

    **라이선스 할당** 페이지가 업데이트되어 사용자가 선택되고 할당이 구성되었음을 보여 줍니다.

    >[!NOTE]
    >일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 그룹에 라이선스를 할당하려면 먼저 모든 멤버에 대한 **사용 위치**를 지정해야 합니다. 이 값은 Azure AD의 **Azure Active Directory &gt; 사용자 &gt; 프로필 &gt; 설정** 영역에서 설정할 수 있습니다. 사용 위치가 지정되지 않은 모든 사용자는 테넌트의 위치를 상속합니다.

5. **할당**을 선택합니다.

    그룹이 사용이 허가된 그룹 목록에 추가되며, 모든 멤버가 포함된 Azure AD 서비스에 액세스할 수 있습니다.


## <a name="remove-a-license"></a>라이선스 제거
**라이선스** 페이지에서 사용자 또는 그룹의 라이선스를 제거할 수 있습니다.

### <a name="to-remove-a-license-from-a-specific-user"></a>특정 사용자로부터 라이선스를 제거하려면
1. 제품 버전에 대한 **라이선스 사용자** 페이지에서 더 이상 라이선스가 없어야 하는 사용자를 선택합니다. 예를 들어 _Alain Charon_입니다.

2. **라이선스 제거**를 선택합니다.

    ![라이선스 제거 옵션이 강조 표시된 허가된 사용자 페이지](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>그룹에서 라이선스를 제거하려면
1. 제품 버전에 대한 **허가된 그룹** 페이지에서 더 이상 라이선스가 없어야 하는 그룹을 선택합니다. 예를 들어 _MDM 정책 - 서부_입니다.

2. **라이선스 제거**를 선택합니다.

    ![라이선스 제거 옵션이 강조 표시된 허가된 그룹 페이지](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>사용자가 그룹에서 상속한 라이선스는 직접 제거할 수 없습니다. 대신, 라이선스를 상속하는 그룹에서 사용자를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계
라이선스가 할당되면 다음 프로세스를 수행할 수 있습니다.

- [라이선스 할당 문제 식별 및 해결](../users-groups-roles/licensing-groups-resolve-problems.md)

- [라이선싱을 위해 그룹에 허가된 사용자 추가](../users-groups-roles/licensing-groups-migrate-users.md)

- [Azure Active Directory에서 라이선스 관리를 위해 그룹을 사용하는 경우 시나리오, 제한 사항 및 알려진 문제](../users-groups-roles/licensing-group-advanced.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)
