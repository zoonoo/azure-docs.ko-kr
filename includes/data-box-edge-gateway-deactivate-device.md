---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562704"
---
디바이스를 초기화하려면 데이터 디스크의 모든 데이터와 디바이스의 부팅 디스크를 안전하게 지워야 합니다. 

Cmdlet을 사용 `Reset-HcsAppliance` 하 여 데이터 디스크와 부팅 디스크 또는 데이터 디스크를 모두 지울 수 있습니다. `ClearData`및 스위치를 사용 하 여 `BootDisk` 데이터 디스크와 부팅 디스크를 각각 초기화할 수 있습니다.

`BootDisk`스위치는 부팅 디스크를 초기화 하 고 장치를 사용할 수 없게 만듭니다. 디바이스를 Microsoft에 반환해야 하는 경우에만 사용해야 합니다. 자세한 내용은 [Microsoft로 장치 반환](../articles/databox-online/azure-stack-edge-return-device.md)을 참조 하세요.

로컬 웹 UI에서 디바이스 초기화를 사용하는 경우 데이터 디스크만 안전하게 지워지고 부팅 디스크는 그대로 유지됩니다. 부팅 디스크에는 디바이스 구성이 포함되어 있습니다.

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 명령 프롬프트에 다음을 입력합니다.

    `Reset-HcsAppliance -ClearData -BootDisk`

    다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```