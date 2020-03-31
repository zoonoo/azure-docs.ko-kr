---
title: Azure 관리 ID를 사용하여 DevTest 랩에서 환경을 만듭니다 | 마이크로 소프트 문서
description: Azure에서 관리되는 ID를 사용하여 Azure DevTest Labs의 랩에 환경을 배포하는 방법을 알아봅니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931146"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure 관리 ID를 사용하여 랩에 환경을 배포 
랩 소유자는 관리되는 ID를 사용하여 랩에 환경을 배포할 수 있습니다. 이 기능은 환경이 키 자격 증명 모음, 공유 이미지 갤러리 및 환경의 리소스 그룹에 외부인 네트워크와 같은 Azure 리소스를 포함하거나 참조하는 시나리오에서 유용합니다. 이를 통해 해당 환경의 리소스 그룹에 국한되지 않는 샌드박스 환경을 만들 수 있습니다.

> [!NOTE]
> 현재 단일 사용자 할당 ID는 랩당 지원됩니다. 

## <a name="prerequisites"></a>사전 요구 사항
- [Azure 포털을 사용하여 사용자가 할당한 관리 ID에 역할을 생성, 목록, 삭제 또는 할당합니다.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 랩 소유자로서 Azure 포털을 사용하여 사용자 관리 ID를 랩에 추가합니다. 

1. 랩 페이지에서 구성 **및 정책을**선택합니다. 
1. **설정** 섹션에서 **ID를** 선택합니다.
1. 사용자에게 할당된 ID를 추가하려면 도구 모음에 **추가를** 선택합니다. 
1. 미리 채워진 드롭다운 목록에서 **ID를** 선택합니다.
1. **확인**을 선택합니다.

    ![사용자 관리 ID 추가](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. 목록에 추가된 사용자 관리 ID가 표시됩니다. 

    ![목록에서 사용자 관리 ID](./media/use-managed-identities-environments/identity-in-list.png)

저장되면 랩은 모든 랩 환경을 배포하는 동안 이 ID를 사용합니다. 목록에서 ID를 선택하여 Azure의 ID 리소스에 액세스할 수도 있습니다. 

랩 소유자는 랩에 추가된 ID에 환경이 액세스해야 하는 외부 리소스에 대한 사용 권한이 있는 한 환경을 배포하는 동안 특별한 작업을 수행할 필요가 없습니다. 

랩에 할당된 사용자 관리 ID를 변경하려면 먼저 랩에 연결된 ID를 제거한 다음 랩에 다른 ID를 추가합니다. 랩에 연결된 ID를 제거하려면 **... (타원)을**클릭합니다. **Remove** 

![목록에서 사용자 관리 ID](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>API 사용

1. ID를 만든 후 이 ID의 리소스 ID를 기록합니다. 다음 샘플과 같아야 합니다. 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. PUT Https 메서드를 수행하여 `ServiceRunner` 다음 예제와 유사한 새 리소스를 랩에 추가합니다. 서비스 러너 리소스는 DevTest Labs에서 관리되는 ID를 관리하고 제어하는 프록시 리소스입니다. 서비스 러너 이름은 유효한 이름이될 수 있지만 관리되는 ID 리소스의 이름을 사용하는 것이 좋습니다. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    예를 들면 다음과 같습니다. 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
사용자에게 할당된 ID가 랩에 추가되면 Azure DevTest Labs 서비스는 Azure 리소스 관리자 환경을 배포하는 동안 해당 ID를 사용합니다. 예를 들어 외부 공유 이미지 갤러리 이미지에 액세스하기 위해 Resource Manager 템플릿이 필요한 경우 랩에 추가한 ID에 공유 이미지 갤러리 리소스에 필요한 최소 사용 권한이 있는지 확인합니다. 
