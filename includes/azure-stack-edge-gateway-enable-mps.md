---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102517571"
---
1. 시작하기 전에 다음 사항을 확인합니다.

    1. Azure에서 Azure Stack Edge 리소스를 사용하여 [Azure Stack Edge Pro 디바이스를 구성하고 활성화](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md)했습니다.
    1. [Azure Portal에서 이 디바이스의 컴퓨팅을 구성](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute)했습니다.
    
1. [PowerShell 인터페이스에 연결하세요](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. 다음 명령을 사용하여 디바이스에서 MPS를 사용하도록 설정합니다.

    ```powershell
    Start-HcsGpuMPS
    ```