---
title: 직렬를 통해 Azure Percept에 연결
description: PuTTY 및 USB에서 TTL 직렬 케이블로 직렬 연결을 설정 하는 방법에 대해 알아봅니다.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662922"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>직렬를 통해 Azure Percept에 연결

아래 단계에 따라 Azure Percept 진한 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)에 대 한 직렬 연결을 설정 합니다.

> [!WARNING]
> 극단적인 실패 사례 (예: 장치를 bricked)를 제외 하 고는 devkit를 직렬에 연결 **하지** 마세요. 직렬 케이블을 연결 하는 데 캐리어 보드 엔클로저를 분리 하는 것은 매우 어렵고 Wi-Fi 안테나 케이블을 분리 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- 호스트 PC
- Azure Percept DK
- [USB-TTL 직렬 케이블](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB에서 TTL 직렬 케이블로 연결 합니다.":::

## <a name="initiate-the-serial-connection"></a>직렬 연결 시작

1. 캐리어 보드가 80/20 레일에 연결 된 경우 16 진수 키 (devkit 시작 카드에 포함)를 사용 하 여 레일에서 제거 합니다.

1. 캐리어 보드 인클로저 아래쪽의 나사를 제거 하 고 마더보드를 추출 합니다.

    > [!WARNING]
    > 마더보드를 제거 하면 Wi-Fi 안테나 케이블이 중단 됩니다. 장치를 마지막으로 복구 하는 경우를 제외 하 고는 직렬 연결을 진행 **하지** 마세요.

1. 히트싱크를 제거 합니다.

1. GPIO 핀에서 점퍼 보드를 제거 합니다.

    > [!TIP]
    > 제거 하기 전에 점퍼 보드의 방향에 유의 하십시오. 예를 들어, 참조를 위해 회로를 향하는 점퍼 보드에 대 한 화살표를 그리거나 스티커를 연결 합니다. 점퍼 보드는 키가 없으며, 캐리어 보드를 reassembling 때 실수로 역방향으로 연결 될 수 있습니다.

1. 아래와 같이 마더보드의 GPIO 핀에 [USB를 TTL 직렬 케이블로](https://www.adafruit.com/product/954) 연결 합니다. 빨간색 네트워크는 연결 되어 있지 않습니다.

    - 검정색 케이블 (GND)을 핀 6에 연결 합니다.
    - 흰색 케이블 (RX)을 pin 8에 연결 합니다.
    - 녹색 케이블 (TX)을 pin 10에 연결 합니다.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="운송 업체 보드 직렬 핀 연결입니다.":::

1. Devkit를 켜고 직렬 케이블의 USB 쪽을 PC에 연결 합니다.

1. Windows에서 **시작**  ->  **Windows 업데이트 설정**  ->  **보기 옵션 업데이트**  ->  **드라이버 업데이트** 로 이동 합니다. 목록에서 직렬-USB 업데이트를 찾아 옆에 있는 확인란을 선택 하 고 **다운로드 및 설치** 를 클릭 합니다.  

1. 다음으로 Windows 장치 관리자 (**시작**  ->  **장치 관리자**)를 엽니다. **포트** 로 이동 하 고 **USB에서 UART** 를 클릭 하 여 **속성** 을 엽니다. 장치가 연결 된 COM 포트를 확인 합니다.

1. **포트 설정** 탭을 클릭 합니다. **비트/초** 가 115200로 설정 되어 있는지 확인 합니다.

1. PuTTY를 엽니다. 다음을 입력 하 고 **열기** 를 클릭 하 여 직렬를 통해 devkit에 연결 합니다.

    1. 직렬 줄: COM [포트 #]
    1. 속도: 115200
    1. 연결 형식: 직렬

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="직렬 매개 변수가 선택 된 PuTTY 세션 창이 있습니다.":::

## <a name="next-steps"></a>다음 단계

[Usb에서 TTL 직렬 케이블로](https://www.adafruit.com/product/954)직렬로 부팅 불가능 한 장치를 업데이트 하려면 비표준 상황에 대 한 usb 업데이트 가이드를 참조 하세요.

[comment]: # (사용 가능한 경우 USB 업데이트 가이드에 대 한 링크를 추가 합니다.)