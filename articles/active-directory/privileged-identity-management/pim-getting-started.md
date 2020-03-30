---
title: PIM 사용 시작 - Azure 활성 디렉터리 | 마이크로 소프트 문서
description: Azure Portal에서 Azure AD PIM(Privileged Identity Management)을 사용하도록 설정하고 시작하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472863"
---
# <a name="start-using-privileged-identity-management"></a>Privileged Identity Management 사용 시작

PIM(권한 있는 ID 관리)을 사용하면 Azure Active Directory(Azure AD) 조직 내에서 액세스를 관리, 제어 및 모니터링할 수 있습니다. 이 범위에는 Azure 리소스, Azure AD 및 Office 365 또는 Microsoft Intune과 같은 기타 Microsoft 온라인 서비스에 대한 액세스가 포함됩니다.

이 문서에서는 권한 있는 ID 관리를 사용 하 여 사용 하 여 사용 하 고 시작 하는 방법에 대해 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

권한 있는 ID 관리를 사용하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5

자세한 내용은 [권한 있는 ID 관리를 사용하는 라이센스 요구 사항을](subscription-requirements.md)참조하십시오.

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD 역할을 위해 PIM에 가입

디렉터리에서 권한 ID 관리를 사용하도록 설정한 후에는 Azure AD 역할을 관리하려면 권한 있는 ID 관리를 등록해야 합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을**선택합니다.

    ![Azure AD 역할에 대한 권한 있는 ID 관리 등록](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **가입을 선택합니다.**

1. 표시되는 메시지에서 **예를** 클릭하여 권한 있는 ID 관리를 등록하여 Azure AD 역할을 관리합니다.

    ![Azure AD 역할 메시지에 대한 권한 있는 ID 관리 등록](./media/pim-getting-started/sign-up-pim-message.png)

    가입이 완료되면 Azure AD 옵션을 사용할 수 있습니다. 포털을 새로 고쳐야 할 수 있습니다.

    권한 있는 ID 관리로 보호할 Azure 리소스를 검색하고 선택하는 방법에 대한 자세한 내용은 [권한 있는 ID 관리에서 관리할 Azure 리소스 검색을](pim-resource-roles-discover-resources.md)참조하십시오.

## <a name="navigate-to-your-tasks"></a>태스크로 이동합니다.

권한 있는 ID 관리가 설정되면 ID 관리 작업을 시작할 수 있습니다.

![작업 및 관리 옵션을 표시하는 권한 있는 ID 관리의 탐색 창](./media/pim-getting-started/pim-quickstart-tasks.png)

| 작업 + 관리 | 설명 |
| --- | --- |
| **내 역할**  | 사용자에게 할당된 적격 및 활성 역할의 목록을 표시합니다. 여기서 할당된 적합한 역할을 활성화할 수 있습니다. |
| **내 요청** | 적격 역할 할당을 활성화할 보류 중인 요청을 표시합니다. |
| **요청 승인** | 승인하도록 지정된 디렉터리에서 사용자를 통해 적격 역할을 활성화하기 위한 요청 목록을 표시합니다. |
| **액세스 검토** | 사용자 자신 또는 다른 사용자에 대한 액세스를 검토하는지와 관계없이 사용자에게 수행하도록 할당된 활성 액세스 검토를 나열합니다. |
| **Azure AD 역할** | Azure AD 역할 할당을 관리하기 위해 권한 있는 역할 관리자에 대한 대시보드 및 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |
| **Azure 리소스** | 권한 있는 역할 관리자가 Azure 리소스 역할 할당을 관리하기 위한 대시보드 및 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>대시보드에 PIM 타일 추가

권한 있는 ID 관리를 쉽게 열 수 있도록 Azure 포털 대시보드에 권한 있는 ID 관리 타일을 추가합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. **모든 서비스를** 선택하고 **Azure AD 권한 ID 관리** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 권한 있는 ID 관리 빠른 시작을 선택합니다.

1. **핀 블레이드에서 대시보드로 이동하여** 권한 있는 ID 관리 퀵스타트 블레이드를 대시보드에 고정합니다.

    ![푸시핀 아이콘으로 권한 있는 ID 관리 블레이드를 대시보드에 고정](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure 대시보드에 다음과 같은 타일이 표시됩니다.

    ![대시보드에서 권한 있는 ID 관리 빠른 시작 타일](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [권한 있는 ID 관리에서 관리할 Azure 리소스 검색](pim-resource-roles-discover-resources.md)
