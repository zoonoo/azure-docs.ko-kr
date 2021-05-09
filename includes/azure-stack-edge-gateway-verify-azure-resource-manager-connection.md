---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305562"
---
다음 단계를 사용하여 클라이언트에서 디바이스에 액세스할 수 있는지 확인합니다.

클라이언트가 로컬 Azure Resource Manager에 연결할 수 있는지 확인합니다. 

1. 로컬 디바이스 API를 호출하여 인증합니다.

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Azure Resource Manager를 통해 연결할 사용자 이름 `EdgeArmUser` 및 암호를 제공합니다. 암호를 기억하지 못하는 경우 [Azure Resource Manager의 암호를 다시 설정](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md)하고 이 암호를 사용하여 로그인합니다.