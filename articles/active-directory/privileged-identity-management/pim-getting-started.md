---
title: PIM을 사용 하 여 시작-Azure Active Directory | Microsoft Docs
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0ba7846b60ca6649b4342d5096e92dfd8c96601
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756305"
---
# <a name="start-using-privileged-identity-management"></a>Privileged Identity Management 사용 시작

PIM (Privileged Identity Management)을 사용 하 여 Azure Active Directory (Azure AD) 조직 내에서 액세스를 관리, 제어 및 모니터링할 수 있습니다. 이 범위에는 Azure 리소스, Azure AD 및 기타 Microsoft 온라인 서비스 (예: Office 365 또는 Microsoft Intune)에 대 한 액세스 권한이 포함 됩니다.

이 문서에서는 Privileged Identity Management을 사용 하도록 설정 하 고 시작 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>전제 조건

Privileged Identity Management를 사용 하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5

자세한 내용은 [Privileged Identity Management 사용할 라이선스 요구 사항](subscription-requirements.md)을 참조 하세요.

## <a name="first-person-to-use-pim"></a>PIM을 처음 사용하는 사용자

디렉터리에서 Privileged Identity Management를 처음 사용 하는 사용자의 경우 디렉터리에 [보안 관리자](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) 및 [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할이 자동으로 할당 됩니다. 권한 있는 역할 관리자만 사용자의 Azure AD 역할 할당을 관리할 수 있습니다. 또한 초기 검색 및 할당 환경을 안내하는 [보안 마법사](pim-security-wizard.md)를 실행할 수 있습니다.

## <a name="enable-pim"></a>PIM 사용

디렉터리에서 Privileged Identity Management 사용을 시작 하려면 먼저 Privileged Identity Management를 사용 하도록 설정 해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 디렉터리의 전역 관리자로 로그인합니다.

    디렉터리에 대 한 Privileged Identity Management를 사용 하도록 설정 하려면 Microsoft 계정 (예: @outlook.com)가 아닌 조직 계정 (예: @yourdomain.com)을 사용 하는 전역 관리자 여야 합니다.

1. **모든 서비스**를 클릭하고, **Azure AD Privileged Identity Management** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. Privileged Identity Management 빠른 시작을 열려면 클릭 합니다.

1. 목록에서 **PIM에 동의**를 클릭합니다.

    ![Privileged Identity Management을 사용 하도록 설정 Privileged Identity Management에 동의](./media/pim-getting-started/consent-pim.png)

1. **ID 확인**을 클릭하여 Azure MFA를 통해 ID를 확인합니다. 계정을 선택하라는 메시지가 표시됩니다.

    ![Id를 확인 하려면 계정 창을 선택 합니다.](./media/pim-getting-started/pick-account.png)

1. 확인을 위해 추가 정보가 필요한 경우 프로세스 안내가 제공됩니다. 자세한 내용은 [2단계 확인에 대한 도움말 보기](https://go.microsoft.com/fwlink/p/?LinkId=708614)를 참조하세요.

    ![조직에 추가 정보가 필요한 경우 추가 정보 필요 창](./media/pim-getting-started/more-information-required.png)

    예를 들어 전화 확인을 제공하도록 요청하는 메시지가 표시될 수 있습니다.

    ![사용자에 게 연락 하는 방법을 요청 하는 추가 보안 확인 페이지](./media/pim-getting-started/additional-security-verification.png)

1. 확인 절차를 완료하면 **동의** 단추를 클릭합니다.

1. 표시 되는 메시지에서 **예** 를 클릭 하 여 Privileged Identity Management 서비스에 동의 합니다.

    ![승인 프로세스를 완료 하기 위한 Privileged Identity Management 메시지에 동의](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD 역할을 위해 PIM에 가입

디렉터리에 대 한 Privileged Identity Management를 사용 하도록 설정한 후에는 Privileged Identity Management에 등록 하 여 Azure AD 역할을 관리 해야 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

    ![Azure AD 역할에 대 한 Privileged Identity Management 등록](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **가입**을 클릭합니다.

1. 표시 되는 메시지에서 **예** 를 클릭 하 여 Azure AD 역할을 관리 Privileged Identity Management 등록 합니다.

    ![Azure AD 역할 메시지에 Privileged Identity Management 등록](./media/pim-getting-started/sign-up-pim-message.png)

    가입이 완료되면 Azure AD 옵션을 사용할 수 있습니다. 포털을 새로 고쳐야 할 수 있습니다.

    Privileged Identity Management를 사용 하 여 보호할 Azure 리소스를 검색 하 고 선택 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management에서 관리할 azure 리소스 검색](pim-resource-roles-discover-resources.md)을 참조 하세요.

## <a name="navigate-to-your-tasks"></a>태스크로 이동합니다.

Privileged Identity Management 설정 되 면 id 관리 작업을 시작할 수 있습니다.

![작업 및 관리 옵션을 보여 주는 Privileged Identity Management의 탐색 창](./media/pim-getting-started/pim-quickstart-tasks.png)

| 작업 + 관리 | 설명 |
| --- | --- |
| **내 역할**  | 사용자에게 할당된 적격 및 활성 역할의 목록을 표시합니다. 여기서 할당된 적합한 역할을 활성화할 수 있습니다. |
| **내 요청** | 적격 역할 할당을 활성화할 보류 중인 요청을 표시합니다. |
| **요청 승인** | 승인하도록 지정된 디렉터리에서 사용자를 통해 적격 역할을 활성화하기 위한 요청 목록을 표시합니다. |
| **액세스 검토** | 사용자 자신 또는 다른 사용자에 대한 액세스를 검토하는지와 관계없이 사용자에게 수행하도록 할당된 활성 액세스 검토를 나열합니다. |
| **Azure AD 역할** | Azure AD 역할 할당을 관리 하는 권한 있는 역할 관리자에 대 한 대시보드 및 설정을 표시 합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |
| **Azure 리소스** | 권한 있는 역할 관리자가 Azure 리소스 역할 할당을 관리하기 위한 대시보드 및 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>대시보드에 PIM 타일 추가

Privileged Identity Management를 쉽게 열 수 있도록 Azure Portal 대시보드에 Privileged Identity Management 타일을 추가 해야 합니다.

1. [Azure portal](https://portal.azure.com/)에 로그인합니다.

1. **모든 서비스**를 클릭하고, **Azure AD Privileged Identity Management** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. Privileged Identity Management 빠른 시작을 열려면 클릭 합니다.

1. 대시보드에 **블레이드 고정** 을 선택 하 여 Privileged Identity Management 빠른 시작 블레이드를 대시보드에 고정 합니다.

    ![대시보드에 Privileged Identity Management 블레이드를 고정 하는 압정 아이콘](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure 대시보드에 다음과 같은 타일이 표시됩니다.

    ![대시보드의 Privileged Identity Management 빠른 시작 타일](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management에서 관리할 Azure 리소스 검색](pim-resource-roles-discover-resources.md)
