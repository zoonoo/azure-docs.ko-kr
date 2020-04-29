---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67182871"
---
DHCP가 아닌 환경에서 부팅 하는 경우 다음 단계를 수행 하 여 Data Box Gateway에 대 한 가상 컴퓨터를 배포 합니다.

1. [장치의 Windows PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. `Get-HcsIpAddress` Cmdlet을 사용 하 여 가상 장치에서 사용 하도록 설정 된 네트워크 인터페이스를 나열 합니다. 디바이스에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

    다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. `Set-HcsIpAddress` cmdlet을 사용하여 네트워크를 구성합니다. 다음 예제를 참조하십시오.

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

