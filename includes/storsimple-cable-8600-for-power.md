---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8e6db54853efcba4d648c1d3bc793a9d1ce57441
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164807"
---
<!--author=alkohli last changed: 9/16/15-->


#### <a name="to-cable-your-device-for-power"></a>장치에 전원 케이블을 연결하려면
> [!NOTE]
> StorSimple 장치의 두 엔클로저 모두가 중복 PCM을 포함합니다. 각 엔클로저에 대해 PCM을 설치하고 서로 다른 전원에 연결하여 높은 가용성을 보장해야 합니다.
> 
> 

1. 모든 PCM상의 전원 스위치가 OFF 위치에 있는지 확인합니다.
2. 기본 엔클로저에서는 두 PCM 모두에 전원 코드를 연결합니다. 전원 코드는 아래 전원 케이블 연결 다이어그램에 빨간색으로 표시되어 있습니다.
3. 기본 엔클로저의 두 PCM이 별도의 전원을 사용하는지 확인합니다.
4. 전원 케이블 연결 다이어그램에 표시된 것처럼 랙 분배 장치의 전원에 전원 코드를 연결합니다.
5. EBOD 인클로저에 대해 2-4단계를 반복합니다.
6. 각 PCM의 전원 스위치를 ON 위치로 눌러 EBOD 엔클로저를 켭니다.
7. EBOD 컨트롤러 뒷면의 녹색 LED가 ON 상태인지 확인하여 EBOD 엔클로저가 켜져 있는지 확인합니다.
8. 각 PCM 스위치를 ON 위치로 눌러 기본 엔클로저의 전원을 켭니다.
9. 장치 컨트롤러 LED가 ON 상태인지 확인하여 시스템이 가동 중인지 확인합니다.
10. EBOD 컨트롤러의 SAS 포트 옆에 4개의 LED가 녹색인지 확인하여 EBOD 컨트롤러와 장치 컨트롤러 사이의 연결이 활성 상태인지 확인합니다.
    
    > [!IMPORTANT]
    > 시스템의 고가용성을 보장하려면 다음 다이어그램에 표시된 것처럼 전원 케이블 연결 방식을 엄격히 준수하는 것이 좋습니다.
    > 
    > 
    
    ![전원에 4U 장치를 케이블로 연결](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **전원 케이블 연결**
    
    | 레이블 | 설명 |
    |:--- |:--- |
    | 1 |기본 인클로저 |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |컨트롤러 0 |
    | 5 |컨트롤러 1 |
    | 6 |EBOD 컨트롤러 0 |
    | 7 |EBOD 컨트롤러 1 |
    | 8 |EBOD 인클로저 |
    | 9 |PDU |

