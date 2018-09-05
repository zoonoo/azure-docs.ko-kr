---
title: PIM 사용 - Azure | Microsoft Docs
description: Azure Portal에서 Azure AD PIM(Privileged Identity Management)을 사용하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190092"
---
# <a name="start-using-pim"></a>PIM 사용

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 조직 내에서 액세스를 관리, 제어 및 모니터링할 수 있습니다. 여기에는 Azure 리소스, Azure AD 및 다른 Microsoft 온라인 서비스(예: Office 365 또는 Microsoft Intune)에 대한 액세스가 포함됩니다.

이 문서에서는 Azure AD PIM 앱을 Azure 포털 대시보드에 추가하는 방법을 알려줍니다.

## <a name="first-person-to-use-pim"></a>PIM을 처음 사용하는 사용자

디렉터리에서 PIM을 처음 사용하는 경우 해당 사용자에게 디렉터리에 대한 [보안 관리자](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) 및 [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할이 자동으로 할당됩니다. 권한 있는 역할 관리자만 사용자의 Azure AD 디렉터리 역할 할당을 관리할 수 있습니다. 또한 [보안 마법사](pim-security-wizard.md)를 실행하도록 선택할 수도 있습니다. 초기 검색 및 할당 경험을 안내합니다.

## <a name="add-pim-tile-to-the-dashboard"></a>대시보드에 PIM 타일 추가

PIM을 더 쉽게 열려면 Azure Portal 대시보드에 PIM 타일을 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 디렉터리에 대한 글로벌 관리자 권한으로 로그인합니다.

1. **모든 서비스**를 클릭하고, **Azure AD Privileged Identity Management** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. PIM 빠른 시작을 클릭하여 엽니다.

1. **대시보드에 블레이드 고정**을 선택하여 PIM 빠른 시작 블레이드를 대시보드에 고정합니다.

    ![대시보드에 블레이드 고정](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure 대시보드에 다음과 같은 타일이 표시됩니다.

    ![PIM 빠른 시작 타일](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>태스크로 이동합니다.

PIM이 설정되면 이 블레이드를 사용하여 ID 관리 작업을 수행할 수 있습니다.

![PIM의 최상위 태스크 - 스크린샷](./media/pim-getting-started/pim-quickstart-tasks.png)

| 작업 + 관리 | 설명 |
| --- | --- |
| **내 역할**  | 사용자에게 할당된 적격 및 활성 역할의 목록을 표시합니다. 여기서 할당된 적합한 역할을 활성화할 수 있습니다. |
| **내 요청** | 적격 역할 할당을 활성화할 보류 중인 요청을 표시합니다. |
| **응용 프로그램 액세스** | 잠재적 지연을 줄이고 역할을 활성화하는 즉시 사용할 수 있습니다. |
| **요청 승인** | 승인하도록 지정된 디렉터리에서 사용자를 통해 적격 역할을 활성화하기 위한 요청 목록을 표시합니다. |
| **액세스 검토** | 사용자 자신 또는 다른 사용자에 대한 액세스를 검토하는지와 관계없이 사용자에게 수행하도록 할당된 활성 액세스 검토를 나열합니다. |
| **Azure AD 디렉터리 역할** | 권한 있는 역할 관리자가 Azure AD 디렉터리 역할 할당을 관리하기 위한 대시보드 및 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |
| **Azure 리소스** | 권한 있는 역할 관리자가 Azure 리소스 역할 할당을 관리하기 위한 대시보드 및 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할 활성화](pim-how-to-activate-role.md)
- [PIM에서 Azure 리소스 역할 활성화](pim-resource-roles-activate-your-roles.md)
