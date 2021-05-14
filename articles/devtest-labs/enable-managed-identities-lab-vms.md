---
title: Azure DevTest Labs의 랩 VM에서 관리 ID 사용
description: 이 문서는 랩 소유자가 랩 가상 머신에서 사용자가 할당한 관리 ID를 사용하도록 설정할 수 있는 방법을 보여 줍니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88717638"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩 가상 머신에서 사용자가 할당한 관리 ID 사용
랩 소유자는 Azure DevTest Labs의 랩 VM(가상 머신)에서 사용자가 할당한 관리 ID를 사용하도록 설정할 수 있습니다.

관리 ID를 사용하면 코드에 자격 증명을 전달하지 않고도 Key Vault를 비롯한 Azure AD(Azure Active Directory) 인증을 지원하는 모든 서비스에 대해 인증할 수 있습니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

이 기능을 사용하면 랩 사용자가 랩 컨텍스트에서 Azure SQL Database와 같은 Azure 리소스를 공유할 수 있습니다. 리소스에 대한 인증은 ID 자체를 통해 처리됩니다. 구성된 후에는 이 ID를 통해 모든 기존 랩 VM/새로 만든 랩 VM을 사용할 수 있습니다. 랩 사용자는 머신에 로그인하면 리소스에 액세스할 수 있습니다.

> [!NOTE]
> 여러 개의 사용자가 할당한 관리 ID를 랩 VM에서 사용할 수 있도록 추가할 수 있습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
랩 VM에 사용자가 할당한 관리 ID를 추가하려면 다음 단계를 수행합니다.

1. [구독에서 사용자가 할당한 관리 ID를 만듭니다](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).
1. 랩의 **구성 및 정책** 페이지로 이동합니다.
1. 왼쪽 메뉴에서 **ID(미리 보기)** 를 선택합니다.
1. **가상 머신** 탭을 선택합니다.
1. **추가** 를 선택하여 미리 채워져 있는 드롭다운 목록에서 기존 ID를 선택합니다. 

    > [!div class="mx-imgBorder"]
    > ![ID 추가 단추](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. 드롭다운 목록에서 기존 **사용자 관리 ID** 를 선택하고 **확인** 을 선택합니다. 

    > [!div class="mx-imgBorder"]
    > ![ID 추가](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API 사용

1.  ID를 만든 후 해당 ID의 리소스 ID를 확인합니다. 다음 샘플과 유사해야 합니다. 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. PUT HTTPS 메서드를 실행하여 다음 예제와 같이 새 **서비스 실행기** 리소스를 랩에 추가합니다. 

    서비스 실행기 리소스는 DevTest Labs에서 관리 ID를 관리하고 제어하는 프록시 리소스입니다. 서비스 실행기 이름은 어떤 유효한 이름도 될 수 있지만 관리 ID 리소스 이름을 사용하는 것이 좋습니다.

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
관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.







