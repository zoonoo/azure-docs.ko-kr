---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182871"
---
DHCP가 아닌 환경에서 부팅하는 경우 다음 단계에 따라 데이터 박스 게이트웨이에 대한 가상 컴퓨터를 배포합니다.

1. [장치의 Windows PowerShell 인터페이스에 연결합니다.](#connect-to-the-powershell-interface)
2. cmdlet을 `Get-HcsIpAddress` 사용하여 가상 장치에서 활성화된 네트워크 인터페이스를 나열합니다. 디바이스에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

    다음 예제에서는 이 cmdlet의 사용을 보여 주며 있습니다.

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

