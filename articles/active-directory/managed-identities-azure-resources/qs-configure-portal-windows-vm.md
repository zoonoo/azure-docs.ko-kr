---
title: Azure Portal을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성하는 방법
description: Azure Portal을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 구성하는 단계별 지침입니다.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab0a5b021048f0f684473d3f54bbeadf870cd007
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66112809"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Portal을 사용하여 Azure VM(Virtual Machine)에 사용자 할당 관리 ID를 사용하거나 사용하지 않도록 설정하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure Portal을 사용하여 VM에서 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>VM 생성 중에 시스템 할당 관리 ID를 사용하도록 설정

VM을 만드는 중 VM에서 시스템 할당 관리 ID를 사용하도록 설정하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

- **ID** 섹션의 **관리** 탭에서 **관리 서비스 ID**를 **켜기**로 전환합니다.  

![VM 생성 중에 시스템 할당 ID를 사용하도록 설정](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

VM을 만들려면 다음 빠른 시작을 참조하세요. 

- [Azure Portal을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Azure Portal을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>기존 VM에서 시스템 할당 관리 ID를 사용하도록 설정

원래 시스템 할당 관리 ID 없이 프로비전된 VM에서 해당 ID를 사용하도록 설정하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 원하는 Virtual Machine으로 이동하여 **ID**를 선택합니다.

3. **시스템 할당**, **상태**에서 **켜기**를 선택한 다음, **저장**을 클릭합니다.

   ![구성 페이지 스크린샷](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>VM에서 시스템 할당 관리 ID 제거

VM에서 시스템 할당 관리 ID를 제거하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

시스템 할당 관리 ID가 더 이상 필요하지 않은 가상 머신이 있는 경우:

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. 원하는 Virtual Machine으로 이동하여 **ID**를 선택합니다.

3. **시스템 할당**, **상태**에서 **끄기**를 선택한 다음, **저장**을 클릭합니다.

   ![구성 페이지 스크린샷](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

 이 섹션에서는 Azure Portal을 사용하여 VM에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>VM 생성 중에 사용자 할당 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 및 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

현재 Azure Portal은 VM을 만드는 동안 사용자 할당 관리 ID를 할당하는 것을 지원하지 않습니다. 대신 다음 VM 만들기 빠른 시작 문서 중 하나를 참조하여 먼저 VM을 만든 다음, VM에 사용자 할당 관리 ID를 할당하는 데 관한 자세한 내용이 담긴 다음 섹션을 진행합니다.

- [Azure Portal을 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal을 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>기존 VM에 사용자 할당 관리 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 및 [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원하는 VM으로 이동하고 **ID**, **사용자 할당**을 클릭한 다음, **\+추가**를 클릭합니다.

   ![VM에 사용자 할당 관리 ID 추가](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. VM에 추가할 사용자 할당 ID를 클릭한 다음, **추가**를 클릭합니다.

    ![VM에 사용자 할당 관리 ID 추가](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>VM에서 사용자 할당 관리 ID 제거

VM에서 사용자 할당 ID를 제거하려면 계정에 [가상 머신 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원하는 VM으로 이동하고 **ID**, **사용자 할당**, 삭제할 사용자 할당 관리 ID의 이름을 클릭한 다음, **제거**를 클릭합니다(확인 창에서 **예** 클릭).

   ![VM에서 사용자 할당 관리 ID 제거](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 [다른 Azure 리소스에 대한 액세스 권한](howto-assign-access-portal.md)을 Azure VM의 관리 ID에 제공합니다.

