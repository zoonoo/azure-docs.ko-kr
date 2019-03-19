---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556765"
---
장치를 재설정 하려면 장치의 부팅 디스크 및 데이터 디스크의 모든 데이터가 안전 하 게 초기화 해야 합니다. 

사용 된 `Reset-HcsAppliance` 데이터 디스크를 부팅 디스크 또는 데이터 디스크에만 초기화 하는 cmdlet입니다. 합니다 `ClearData` 및 `BootDisk` 스위치를 사용 하면 각각의 데이터 디스크 및 부팅 디스크를 초기화 합니다.

로컬 웹 UI에서에서 재설정 한 장치를 사용 하는 경우 데이터 디스크만 안전 하 게 초기화 됩니다 있지만 부팅 디스크는 그대로 유지 됩니다. 부팅 디스크 장치 구성을 포함합니다.

1. [PowerShell 인터페이스를 연결할](#connect-to-the-powershell-interface)합니다.
2. 명령 프롬프트에 다음을 입력합니다.

    `Reset-HcsAppliance -ClearData -BootDisk`

    다음 예제에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
