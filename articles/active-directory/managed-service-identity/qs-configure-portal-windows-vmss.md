---
title: Azure Portal을 사용하여 Azure 가상 머신 확장 집합에서 관리 서비스 ID 구성
description: Azure Portal을 사용하여 Azure VMSS에서 관리 서비스 ID를 구성하기 위한 단계별 지침입니다.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887992"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신 확장 집합 관리 서비스 ID 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Portal을 사용하여 가상 머신 확장 집합에 시스템 및 사용자 할당 ID를 사용하거나 사용하지 않도록 설정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 역할이 할당되어야 합니다.
    - [Virtual Machine 기여자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 가상 머신 확장 집합에서 시스템이 할당한 관리 ID를 사용하도록 설정하고 제거합니다.

## <a name="system-assigned-identity"></a>시스템 할당 ID 

이 섹션에서는 Azure Portal을 사용하여 가상 머신 확장 집합에 시스템 할당 ID를 사용하거나 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 만드는 동안 시스템 할당 ID를 사용하도록 설정

현재 Azure Portal은 가상 머신 확장 집합을 만드는 동안 시스템 할당 ID를 사용하도록 설정하는 것을 지원하지 않습니다. 대신 다음 가상 머신 확장 집합 만들기 빠른 시작 문서를 참조하여 먼저 가상 머신 확장 집합을 만든 다음, 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정하는 데 관한 다음 섹션을 진행합니다.

- [Azure Portal에서 가상 머신 확장 집합 만들기](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>기존 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정

원래 시스템 할당 ID 없이 프로비전된 가상 머신 확장 집합에서 시스템 할당 ID를 사용하도록 설정하려면:

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 원하는 가상 머신 확장 집합으로 이동합니다.

3. **시스템 할당**, **상태**에서 **켜기**를 선택한 다음, **저장**을 클릭합니다.

   [![구성 페이지 스크린샷](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 시스템 할당 ID 제거

시스템 할당 ID가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속하는지 확인합니다.

2. 원하는 가상 머신 확장 집합으로 이동합니다.

3. **시스템 할당**, **상태**에서 **끄기**를 선택한 다음, **저장**을 클릭합니다.

   ![구성 페이지 스크린샷](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>사용자 할당 ID

이 섹션에서는 Azure Portal을 사용하여 가상 머신 확장 집합에서 사용자 할당 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 만드는 동안 사용자 할당 ID를 할당합니다.

현재 Azure Portal은 가상 머신 확장 집합을 만드는 동안 사용자 할당 ID를 할당하는 것을 지원하지 않습니다. 대신 다음 가상 머신 확장 집합 만들기 빠른 시작 문서를 참조하여 먼저 가상 머신 확장 집합을 만든 다음, 가상 머신 확장 집합에 사용자 할당 ID를 할당하는 데 관한 다음 섹션을 진행합니다.

- [Azure Portal에서 가상 머신 확장 집합 만들기](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>기존 가상 머신 확장 집합에 사용자 할당 ID 할당

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원하는 가상 머신 확장 집합으로 이동하고 **ID**, **사용자 할당**을 클릭한 다음, **\+추가**를 클릭합니다.

   ![VMSS에 사용자 할당 ID 추가](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. 가상 머신 확장 집합에 추가할 사용자 할당 ID를 클릭한 다음, **추가**를 클릭합니다.
   
   ![VMSS에 사용자 할당 ID 추가](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 사용자 할당 ID 제거

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원하는 가상 머신 확장 집합으로 이동하고 **ID**, **사용자 할당**, 삭제할 사용자 할당 ID의 이름을 클릭한 다음, **제거**를 클릭합니다(확인 창에서 **예** 클릭).

   ![VMSS에서 사용자 할당 ID 제거](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>관련 콘텐츠

- 관리 서비스 ID에 대한 개요는 [개요](overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 Azure 가상 머신 확장 집합 관리 서비스 ID [액세스 권한을 다른 Azure 리소스에](howto-assign-access-portal.md) 부여합니다.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
