---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129426"
---
장치를 재설정하려면 데이터 디스크와 장치의 부팅 디스크의 모든 데이터를 안전하게 지워야합니다. 

cmdlet을 `Reset-HcsAppliance` 사용하여 데이터 디스크와 부팅 디스크 또는 데이터 디스크만 모두 지냅니다. `ClearData` 및 `BootDisk` 스위치를 사용하면 데이터 디스크와 부팅 디스크를 각각 초기화할 수 있습니다.

스위치는 `BootDisk` 부팅 디스크를 지우고 장치를 사용할 수 없게 만듭니다. 장치를 Microsoft로 반환해야 하는 경우에만 사용해야 합니다. 자세한 내용은 [장치를 Microsoft로 반환을](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)참조하십시오.

로컬 웹 UI에서 장치 재설정을 사용하는 경우 데이터 디스크만 안전하게 초기화되지만 부팅 디스크는 그대로 유지됩니다. 부팅 디스크에 장치 구성이 포함되어 있습니다.

1. [PowerShell 인터페이스에 연결합니다.](#connect-to-the-powershell-interface)
2. 명령 프롬프트에 다음을 입력합니다.

    `Reset-HcsAppliance -ClearData -BootDisk`

    다음 예제에서는 이 cmdlet을 사용하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주시면 됩니다.

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
