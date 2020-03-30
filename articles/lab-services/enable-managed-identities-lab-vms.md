---
title: Azure DevTest 랩의 랩 VM에서 관리되는 ID 를 사용하도록 설정합니다.
description: 이 문서에서는 랩 소유자가 랩 가상 컴퓨터에서 사용자 할당된 관리 ID를 사용하도록 설정하는 방법을 보여 줍니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692668"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩 가상 컴퓨터에서 사용자 할당된 관리ID 사용
랩 소유자는 Azure DevTest Labs의 VM(랩 가상 시스템)에서 사용자가 할당한 관리되는 ID를 사용하도록 설정할 수 있습니다.

관리되는 ID는 코드의 자격 증명을 전달하지 않고 키 볼트를 포함하여 AD(Azure Active Directory) 인증을 지원하는 모든 서비스에 인증하는 데 사용할 수 있습니다. 관리되는 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란 무엇입니까?](../active-directory/managed-identities-azure-resources/overview.md)

이 기능을 사용하면 랩 사용자는 랩컨텍스트에서 Azure SQL Database와 같은 Azure 리소스를 공유할 수 있습니다. 리소스에 대한 인증은 ID 자체에 의해 관리됩니다. 구성이 완료되면 이 ID로 기존/새로 만든 모든 랩 VM이 활성화됩니다. 랩 사용자는 컴퓨터에 로그인한 후 리소스에 액세스할 수 있습니다.

> [!NOTE]
> 랩 VM에서 사용하도록 할당된 여러 사용자 할당된 관리 ID를 추가할 수 있습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
랩 VM에 대해 관리되는 ID를 할당한 사용자를 추가하려면 다음 단계를 따르십시오.

1. [구독에서 사용자 할당관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. 랩의 **구성 및 정책** 페이지로 이동합니다.
2. 왼쪽 메뉴에서 **ID(미리 보기)를** 선택합니다.
3. 가상 **컴퓨터** 탭을 선택합니다.
4. 미리 채워진 드롭다운 목록에서 기존 ID를 선택하려면 **추가를** 선택합니다. 

    ![ID 추가 단추](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. 삭제된 목록에서 기존 **사용자 관리 ID를** 선택하고 **확인을**선택합니다. 

    ![ID 추가](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API 사용

1.  ID를 만든 후 ID의 리소스 ID를 기록합니다. 다음 샘플과 같아야 합니다. 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. PUT HTTPS 메서드를 실행하여 다음 예제와 같이 랩에 새 **ServiceRunner** 리소스를 추가합니다. 

    서비스 러너 리소스는 DevTest Labs에서 관리되는 ID를 관리하고 제어하는 프록시 리소스입니다. 서비스 러너 이름은 유효한 이름이 될 수 있지만 관리되는 ID 리소스의 이름을 사용하는 것이 좋습니다.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>다음 단계
관리되는 ID에 대해 자세히 알아보려면 [Azure 리소스에 대한 관리ID란 무엇입니까?](../active-directory/managed-identities-azure-resources/overview.md)







