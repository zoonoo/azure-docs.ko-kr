---
title: Azure Portal을 사용하여 사용자가 할당한 관리 ID를 관리하는 방법
description: 사용자 할당 관리 ID에 역할을 만들고 나열하고 삭제하고 할당하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18a15b8039322fc5e43a2b9dfed8a9bd3fc8b5fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441649"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure Portal을 사용하여 사용자 할당 관리 ID를 만들고 나열하고 삭제하거나 할당하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

1. 사용자가 할당한 관리 ID를 만들려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, **서비스** 아래에서 **관리 ID**를 클릭합니다.
3. **추가**를 클릭하고 **사용자가 할당한 관리 ID 만들기** 창 아래에서 다음 필드에 값을 입력합니다.
   - **리소스 이름**: 사용자 할당 관리 ID의 이름(예: UAI1)입니다.
   - **구독**: 사용자 할당 관리 ID를 만들 구독 선택
   - **리소스 그룹**: 사용자 할당 관리 ID를 포함할 새 리소스 그룹을 만들거나 **기존 항목 사용**을 선택하여 기존 리소스 그룹에서 사용자 할당 관리 ID를 만듭니다.
   - **위치**: 사용자 할당 관리 ID를 배포할 위치(예: **미국 서부**)를 선택합니다.
4. **만들기**를 클릭합니다.

![사용자 할당 관리 ID 만들기](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 또는 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

1. 사용자 할당 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, 서비스 아래에서 **관리 ID**를 클릭합니다.
3. 구독의 사용자 할당 관리 ID 목록이 반환됩니다.  사용자 할당 관리 ID 세부 정보를 보려면 해당 이름을 클릭합니다.

![사용자 할당 관리 ID 나열](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 ID를 삭제해도 ID가 할당된 VM이나 리소스에서는 ID가 제거되지 않습니다.  사용자 할당 ID를 VM에서 제거하려는 경우 [VM에서 사용자 할당 관리 ID 제거](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm)를 참조하세요.

1. 사용자 할당 관리 ID를 삭제하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 사용자 할당 관리 ID를 선택하고 **삭제**를 클릭합니다.
3. 확인 상자 아래에서 **예**를 선택합니다.

![사용자 할당 관리 ID 삭제](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID에 역할 할당 

사용자 할당 관리 ID에 역할을 할당하려면 계정에 [사용자 액세스 관리자](/azure/role-based-access-control/built-in-roles#user-access-administrator) 역할 할당이 필요합니다.

1. 사용자 할당 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, 서비스 아래에서 **관리 ID**를 클릭합니다.
3. 구독의 사용자 할당 관리 ID 목록이 반환됩니다.  역할을 할당하려는 사용자 할당 관리 ID를 선택합니다.
4. **액세스 제어(IAM)**, **역할 할당 추가**를 차례로 선택합니다.

   ![사용자 할당 관리 ID 시작](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. 역할 할당 추가 블레이드에서 다음 값을 구성한 다음, **저장**을 클릭합니다.
   - **역할** - 할당할 역할
   - **다음에 대한 액세스 할당** - 사용자 할당 관리 ID를 할당할 리소스
   - **선택** - 액세스를 할당할 구성원
   
   ![사용자 할당 관리 ID IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
