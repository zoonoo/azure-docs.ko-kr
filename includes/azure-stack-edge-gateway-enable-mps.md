---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517571"
---
1. 시작하기 전에 다음 사항을 확인합니다.

    1. Azure에서 Azure Stack Edge 리소스를 사용 하 여 [Azure Stack Edge Pro 장치를](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) 구성 하 고 활성화 했습니다.
    1. [Azure Portal에서이 장치에 대 한 계산을 구성](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute)했습니다.
    
1. [PowerShell 인터페이스에 연결](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.
1. 다음 명령을 사용 하 여 장치에서 MP를 사용 하도록 설정 합니다.

    ```powershell
    Start-HcsGpuMPS
    ```