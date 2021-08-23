---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: e0770eb27bfebc085d99af9fdc9749676b699b70
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106597"
---
다음 단계를 사용하여 클라이언트에서 디바이스에 액세스할 수 있는지 확인합니다.

클라이언트가 로컬 Azure Resource Manager에 연결할 수 있는지 확인합니다. 

1. 로컬 디바이스 API를 호출하여 인증합니다.

    ### <a name="az"></a>[Az](#tab/az)

    ```powershell
    login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

    ### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Azure Resource Manager를 통해 연결할 사용자 이름 `EdgeArmUser` 및 암호를 제공합니다. 암호를 기억하지 못하는 경우 [Azure Resource Manager의 암호를 다시 설정](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md)하고 이 암호를 사용하여 로그인합니다.