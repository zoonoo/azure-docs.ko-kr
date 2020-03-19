---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129426"
---
디바이스를 초기화하려면 데이터 디스크의 모든 데이터와 디바이스의 부팅 디스크를 안전하게 지워야 합니다. 

`Reset-HcsAppliance` cmdlet을 사용하여 데이터 디스크와 부팅 디스크 또는 데이터 디스크를 모두 지울 수 있습니다. `ClearData` 및 `BootDisk` 스위치를 사용하여 데이터 디스크와 부팅 디스크를 각각 지울 수 있습니다.

`BootDisk` 스위치는 부팅 디스크를 지워 디바이스를 사용할 수 없게 만듭니다. 디바이스를 Microsoft에 반환해야 하는 경우에만 사용해야 합니다. 자세한 내용은 [Microsoft로 디바이스 반환](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)을 참조하세요.

로컬 웹 UI에서 디바이스 초기화를 사용하는 경우 데이터 디스크만 안전하게 지워지고 부팅 디스크는 그대로 유지됩니다. 부팅 디스크에는 디바이스 구성이 포함되어 있습니다.

1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. 명령 프롬프트에 다음을 입력합니다.

    `Reset-HcsAppliance -ClearData -BootDisk`

    다음 예제는 이 cmdlet을 사용하는 방법을 보여줍니다.

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
