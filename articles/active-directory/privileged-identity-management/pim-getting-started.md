---
title: PIM-Azure Active Directory를 사용 하 여 시작 | Microsoft Docs
description: Azure Portal에서 Azure AD PIM(Privileged Identity Management)을 사용하도록 설정하고 시작하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408991ffc3922986234f7d40e1cd589b1d126ba1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476481"
---
# <a name="start-using-pim"></a>PIM 사용

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하면 조직 내에서 액세스를 관리, 제어 및 모니터링할 수 있습니다. 여기에는 Azure 리소스, Azure AD 및 다른 Microsoft 온라인 서비스(예: Office 365 또는 Microsoft Intune)에 대한 액세스가 포함됩니다.

이 문서에서는 PIM을 사용하도록 설정하고 사용을 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

PIM을 사용하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5

자세한 내용은 [PIM을 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)을 참조하세요.

## <a name="first-person-to-use-pim"></a>PIM을 처음 사용하는 사용자

디렉터리에서 PIM을 처음 사용하는 경우 해당 사용자에게 디렉터리에 대한 [보안 관리자](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) 및 [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할이 자동으로 할당됩니다. 권한 있는 역할 관리자만 사용자의 Azure AD 역할 할당을 관리할 수 있습니다. 또한 초기 검색 및 할당 환경을 안내하는 [보안 마법사](pim-security-wizard.md)를 실행할 수 있습니다.

## <a name="enable-pim"></a>PIM 사용

디렉터리에서 PIM 사용을 시작하려면 먼저 PIM을 사용하도록 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 디렉터리의 전역 관리자로 로그인합니다.

    디렉터리에서 PIM을 사용하도록 설정하려면 Microsoft 계정(예: @outlook.com)이 아닌 조직 계정(예: @yourdomain.com)의 전역 관리자여야 합니다.

1. **모든 서비스**를 클릭하고, **Azure AD Privileged Identity Management** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. PIM 빠른 시작을 클릭하여 엽니다.

1. 목록에서 **PIM에 동의**를 클릭합니다.

    ![PIM을 사용 하도록 설정 하려면 PIM에 동의](./media/pim-getting-started/consent-pim.png)

1. **ID 확인**을 클릭하여 Azure MFA를 통해 ID를 확인합니다. 계정을 선택하라는 메시지가 표시됩니다.

    ![본인 여부를 확인 하기 위해 계정 창을 선택 합니다.](./media/pim-getting-started/pick-account.png)

1. 확인을 위해 추가 정보가 필요한 경우 프로세스 안내가 제공됩니다. 자세한 내용은 [2단계 확인에 대한 도움말 보기](https://go.microsoft.com/fwlink/p/?LinkId=708614)를 참조하세요.

    ![자세한 내용은 조직 정보가 더 필요한 경우 창 필수](./media/pim-getting-started/more-information-required.png)

    예를 들어 전화 확인을 제공하도록 요청하는 메시지가 표시될 수 있습니다.

    ![사용자에 게 연락 하는 방법을 요청 하는 추가 보안 인증 페이지](./media/pim-getting-started/additional-security-verification.png)

1. 확인 절차를 완료하면 **동의** 단추를 클릭합니다.

1. 나타나는 메시지에서 **예**를 클릭하여 PIM 서비스에 동의합니다.

    ![PIM 승인 프로세스를 완료 하는 메시지에 동의](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD 역할을 위해 PIM에 가입

디렉터리에서 PIM을 사용하도록 설정한 후 Azure AD 역할을 관리하려면 PIM에 가입해야 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

    ![Azure AD 역할을 위해 PIM에 가입](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **가입**을 클릭합니다.

1. 나타나는 메시지에서 **예**를 클릭하여 Azure AD 역할을 관리하기 위해 PIM에 가입합니다.

    ![Azure AD 역할을 위해 PIM에 가입 메시지](./media/pim-getting-started/sign-up-pim-message.png)

    가입이 완료되면 Azure AD 옵션을 사용할 수 있습니다. 포털을 새로 고쳐야 할 수 있습니다.

    PIM으로 보호할 Azure 리소스를 검색 및 선택하는 방법에 대한 자세한 내용은 [PIM에서 관리할 Azure 리소스 검색](pim-resource-roles-discover-resources.md)을 참조하세요.

## <a name="navigate-to-your-tasks"></a>태스크로 이동합니다.

PIM이 설정되면 ID 관리 작업을 수행할 수 있습니다.

![탐색 창에서 PIM 보여 주는 작업 및 관리 옵션](./media/pim-getting-started/pim-quickstart-tasks.png)

| 작업 + 관리 | 설명 |
| --- | --- |
| **내 역할**  | 사용자에게 할당된 적격 및 활성 역할의 목록을 표시합니다. 여기서 할당된 적합한 역할을 활성화할 수 있습니다. |
| **내 요청** | 적격 역할 할당을 활성화할 보류 중인 요청을 표시합니다. |
| **요청 승인** | 승인하도록 지정된 디렉터리에서 사용자를 통해 적격 역할을 활성화하기 위한 요청 목록을 표시합니다. |
| **액세스 검토** | 사용자 자신 또는 다른 사용자에 대한 액세스를 검토하는지와 관계없이 사용자에게 수행하도록 할당된 활성 액세스 검토를 나열합니다. |
| **Azure AD 역할** | 대시보드 및 Azure AD 역할 할당을 관리 하는 권한 있는 역할 관리자에 대 한 설정을 표시 합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |
| **Azure 리소스** | 권한 있는 역할 관리자가 Azure 리소스 역할 할당을 관리하기 위한 대시보드 및 설정을 표시합니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>대시보드에 PIM 타일 추가

PIM을 더 쉽게 열려면 Azure Portal 대시보드에 PIM 타일을 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **모든 서비스**를 클릭하고, **Azure AD Privileged Identity Management** 서비스를 찾습니다.

    ![모든 서비스의 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. PIM 빠른 시작을 클릭하여 엽니다.

1. **대시보드에 블레이드 고정**을 선택하여 PIM 빠른 시작 블레이드를 대시보드에 고정합니다.

    ![대시보드에 PIM 블레이드를 고정 하려면 압정 아이콘](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure 대시보드에 다음과 같은 타일이 표시됩니다.

    ![대시보드의 PIM 빠른 시작 타일](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [PIM에서 관리할 Azure 리소스 검색](pim-resource-roles-discover-resources.md)
