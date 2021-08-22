---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: f96012c3ffa587a80d601447d99efc804956096e
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113120972"
---
Azure Stack Edge 디바이스에서 VM을 배포하려면 먼저 Azure PowerShell에 대한 Azure Resource Manager를 통해 디바이스에 연결하도록 클라이언트를 구성해야 합니다. 자세한 지침은 [Azure Stack Edge 디바이스에서 Azure Resource Manager에 연결](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md)을 참조하세요.

다음 단계를 사용하여 클라이언트에서 디바이스에 액세스할 수 있는지 확인합니다. Azure Resource Manager에 연결할 때 이 구성을 이미 수행했으며, 이제 구성이 성공했는지 확인하는 중입니다. 



1. 다음 명령을 실행하여 Azure Resource Manager 통신이 작동하는지 확인합니다.     

    ### <a name="az"></a>[Az](#tab/Az)

    ```powershell
    Add-AzEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```
    ### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. 인증할 로컬 디바이스 API를 호출하려면 다음을 입력합니다. 

    ### <a name="az"></a>[Az](#tab/Az)

    ```powershell
    login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d
    ```

    Azure Resource Manager를 통해 연결하려면 사용자 이름 *EdgeArmUser* 와 암호를 제공합니다.

    ### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d
    ```

    Azure Resource Manager를 통해 연결하려면 사용자 이름 *EdgeArmUser* 와 암호를 제공합니다.

1. Kubernetes용 컴퓨팅을 구성한 경우 이 단계를 건너뛸 수 있습니다. 그렇지 않으면 다음을 수행하여 컴퓨팅용 네트워크 인터페이스를 사용하도록 설정했는지 확인합니다. 

   a. 로컬 사용자 인터페이스에서 **컴퓨팅** 설정으로 이동합니다.  
   b. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다. 사용자가 만든 VM은 이 포트 및 관련 네트워크에 연결된 가상 스위치에 연결됩니다. VM에 사용할 IP 주소와 일치하는 네트워크를 선택해야 합니다.  

    ![컴퓨팅 구성 네트워크 설정 창의 스크린샷.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. 네트워크 인터페이스의 **컴퓨팅 사용** 에서 **예** 를 선택합니다. Azure Stack Edge는 해당 네트워크 인터페이스에 해당하는 가상 스위치를 만들고 관리합니다. 지금은 Kubernetes에 대한 특정 IP를 입력하지 마세요. 컴퓨팅을 활성화하는 데 몇 분 정도 걸릴 수 있습니다.

    > [!NOTE]
    > GPU VM을 만드는 경우 인터넷에 연결된 네트워크 인터페이스를 선택합니다. 이렇게 하면 디바이스에 GPU 확장을 설치할 수 있습니다.