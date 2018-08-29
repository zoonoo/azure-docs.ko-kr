---
title: Azure Portal을 사용하여 사용자가 할당한 관리 ID를 관리하는 방법
description: 사용자가 할당한 관리 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140767"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Azure Portal을 사용하여 사용자 할당 ID 생성, 나열 또는 삭제

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure Portal을 사용하여 사용자 할당 ID를 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 역할이 할당되어야 합니다.
    - [관리 ID 기여자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)는 사용자 할당 ID를 만들고 읽고(나열하고), 업데이트하고 삭제하는 역할을 합니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator)는 사용자 할당 ID의 속성을 읽는(나열하는) 역할을 합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

1. 사용자가 할당한 관리 ID를 만들려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, **서비스** 아래에서 **관리 ID**를 클릭합니다.
3. **추가**를 클릭하고 **사용자가 할당한 관리 ID 만들기** 창 아래에서 다음 필드에 값을 입력합니다.
   - **리소스 이름**: 사용자가 할당한 관리 ID의 이름(예: UAI1)입니다.
   - **구독**: 사용자가 할당한 관리 ID를 만들 구독을 선택합니다.
   - **리소스 그룹**: 사용자가 할당한 관리 ID를 포함할 새 리소스 그룹을 만들거나 **기존 항목 사용**을 선택하고 기존 리소스 그룹에 사용자가 할당한 관리 ID를 만듭니다.
   - **위치**: 사용자가 할당한 관리 ID를 배포할 위치(예: **미국 서부**)를 선택합니다.
4. **만들기**를 클릭합니다.

![사용자 할당 관리 ID 만들기](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>사용자 할당 ID 나열

1. 사용자가 할당한 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, 서비스 아래에서 **관리 ID**를 클릭합니다.
3. 구독의 사용자가 할당한 관리 ID 목록이 반환됩니다.  사용자 할당 세부 정보를 보려면 해당 이름을 클릭합니다.

![사용자가 할당한 관리 ID 목록](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>사용자 할당 ID 삭제

1. 사용자가 할당한 관리 ID를 삭제하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 사용자 할당 ID를 선택하고 **삭제**를 클릭합니다.
3. 확인 상자 아래에서 **예**를 선택합니다.

![사용자가 할당한 관리 ID 삭제](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)