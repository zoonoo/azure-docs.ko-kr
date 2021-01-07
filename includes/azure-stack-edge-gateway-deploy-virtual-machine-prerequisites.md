---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763434"
---
Azure Stack Edge 장치에서 Vm을 배포 하려면 먼저 Azure PowerShell를 통해 Azure Resource Manager를 통해 장치에 연결 하도록 클라이언트를 구성 해야 합니다. 자세한 단계는 [Azure Stack Edge 장치에서 Azure Resource Manager에 연결](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)로 이동 합니다.


클라이언트에서 장치에 액세스 하는 데 다음 단계를 사용할 수 있는지 확인 합니다 (Azure Resource Manager에 연결할 때이 구성을 완료 했 고 구성이 정상적으로 완료 되었는지 확인 하는 중). 

1. Azure Resource Manager 통신이 작동 하는지 확인 합니다. 형식:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. 로컬 장치 Api를 호출 하 여 인증 합니다. 형식: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Azure Resource Manager를 통해 연결할 사용자 이름- *EdgeARMuser* 및 암호를 제공 합니다.

1. Kubernetes에 대 한 **계산** 을 구성한 경우이 단계를 건너뛸 수 있습니다. 계산을 위한 네트워크 인터페이스를 사용 하도록 설정 했는지 확인 합니다. 로컬 UI에서 **계산** 설정으로 이동 합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다. 만든 Vm은이 포트 및 연결 된 네트워크에 연결 된 가상 스위치에 연결 됩니다. VM에 사용할 IP 주소와 일치 하는 네트워크를 선택 해야 합니다.  

    ![계산 설정 1 사용](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. Azure Stack Edge는 해당 네트워크 인터페이스에 해당 하는 가상 스위치를 만들고 관리 합니다. 현재 Kubernetes에 대 한 특정 Ip를 입력 하지 마세요. 계산을 사용 하도록 설정 하는 데 몇 분 정도 걸릴 수 있습니다.

    > [!NOTE]
    > GPU Vm을 만드는 경우 인터넷에 연결 된 네트워크 인터페이스를 선택 합니다. 그러면 장치에 GPU 확장을 설치할 수 있습니다.


1. Azure Portal에서 VM 역할을 사용 하도록 설정 합니다. 이 단계에서는 장치의 로컬 Api를 통해 Vm을 만드는 데 사용 되는 장치에 대 한 고유한 구독을 만듭니다. 

    1. VM 역할을 사용 하도록 설정 하려면 Azure Portal에서 Azure Stack Edge 장치에 대 한 Azure Stack Edge 리소스로 이동 합니다. **Edge compute > Virtual Machines** 로 이동 합니다.

        ![VM 이미지 1 추가](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. **Virtual Machines** 를 선택 하 여 **개요** 페이지로 이동 합니다. 가상 컴퓨터 클라우드 관리를 **사용 하도록 설정** 합니다.
        ![VM 이미지 2 추가](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)