---
title: 그룹 소유자 추가 또는 제거 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 그룹 소유자를 추가하거나 제거하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd684e1bd48f877a74280b33b4df65d7baaa0fe7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507184"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Azure Active Directory를 사용하여 그룹 소유자 추가 또는 제거
Azure AD(Azure Active Directory) 그룹은 그룹 소유자가 소유하고 관리합니다. 그룹 소유자 사용자 또는 서비스 주체를 수 있으며 멤버를 포함 한 그룹을 관리할 수 있습니다. 기존 그룹 소유자 또는 관리자 그룹 관리 그룹 onwers를 할당할 수 있습니다. 그룹 소유자는 그룹의 멤버일 필요가 없습니다.

그룹 소유자가 없는 경우 관리자 그룹 관리 그룹을 관리할 수 있습니다.

## <a name="add-an-owner-to-a-group"></a>그룹에 소유자 추가
다음 그룹에 소유자는 사용자를 추가 하기 위한 지침 사용 하는 Azure AD 포털입니다. 그룹의 소유자로 서 서비스 주체를 추가 하려면 지침에 따라 이렇게 사용 하 여 [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0)합니다.

### <a name="to-add-a-group-owner"></a>그룹 소유자를 추가하려면
1. 해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택하고 **그룹**을 선택한 다음, 소유자를 추가하려는 그룹을 선택합니다(예: *MDM 정책 - 서부*).

3. **MDM 정책 - 서부 개요** 페이지에서 **소유자**를 선택합니다.

    ![소유자 옵션이 강조 표시된 MDM 정책 - 서부 개요 페이지](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. **MDM 정책 - 서부 - 소유자** 페이지에서 **소유자 추가**를 선택한 다음, 새 그룹 소유자가 될 사용자를 검색하고 선택한 다음, **선택**을 선택합니다.

    ![소유자 추가 옵션이 강조 표시된 MDM 정책 - 서부 - 소유자 페이지](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    새 소유자를 선택한 후 **소유자** 페이지를 새로 고쳐서 소유자 목록에 추가된 이름을 확인할 수 있습니다.

## <a name="remove-an-owner-from-a-group"></a>그룹에서 소유자 제거
Azure AD를 사용하여 그룹에서 소유자를 제거합니다.

### <a name="to-remove-an-owner"></a>소유자를 제거하려면
1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택하고 **그룹**을 선택한 다음, 소유자를 제거하려는 그룹을 선택합니다(예: *MDM 정책 - 서부*).

3. **MDM 정책 - 서부 개요** 페이지에서 **소유자**를 선택합니다.

    ![소유자 옵션이 강조 표시된 MDM 정책 - 서부 개요 페이지](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. **MDM 정책 - 서부 - 소유자** 페이지에서 그룹 소유자로 제거하려는 사용자를 선택하고, 사용자의 정보 페이지에서 **제거**를 선택한 후, **예**를 선택하여 결정을 확인합니다.

    ![제거 옵션이 강조 표시된 사용자의 정보 페이지](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    소유자를 제거한 후 **소유자** 페이지로 돌아가면 해당 이름이 소유자 목록에서 제거되었음을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

- [그룹 설정을 구성하는 Azure Active Directory cmdlets](../users-groups-roles/groups-settings-cmdlets.md)

- [그룹을 사용하여 통합 SaaS 앱에 대한 액세스 할당](../users-groups-roles/groups-saasapps.md)

- [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)

- [그룹 설정을 구성하는 Azure Active Directory cmdlets](../users-groups-roles/groups-settings-v2-cmdlets.md)
