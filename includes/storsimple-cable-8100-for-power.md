---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0e8db8779958afe1fd3cf4bf12f2a6fd4a97c61c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164714"
---
<!--author=alkohli last changed: 9/16/15-->

#### <a name="to-cable-for-power"></a>전원을 케이블로 연결하려면
1. 각 PCM(전원 및 냉각 모듈)의 전원 스위치가 OFF 위치에 있는지 확인합니다.
2. 기본 엔클로저에서 각 PCM에 전원 코드를 연결합니다.
3. 다음 그림과 같이 전원 코드를 랙 PDU(전원 분배 장치)에 연결합니다. 두 개의 PCM이 개별 전원을 사용하는지 확인합니다.
   
   > [!IMPORTANT]
   > 시스템의 고가용성을 보장하려면 다음 다이어그램에 표시된 것처럼 전원 케이블 연결 방식을 엄격히 준수하는 것이 좋습니다. 
   > 
   > 
   
    ![전원에 2U 장치를 케이블로 연결](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **8100 장치에서 전원 케이블 연결**
   
   | 레이블 | 설명 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |컨트롤러 1 |
   | 3 |컨트롤러 0 |
   | 4 |PCM 1 |
   | 5 |PDU |
4. 시스템의 전원을 켜려면 양쪽 PCM의 전원 스위치를 ON 위치로 누릅니다.

