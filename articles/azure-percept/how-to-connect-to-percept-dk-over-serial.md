---
title: Azure Percept DK에 직렬 연결
description: USB-TTL 직렬 케이블을 사용하여 Azure Percept DK에 직렬 연결을 설정하는 방법
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: b1cb975c65f2234892cfef919220c68ebf5b2dca
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109488270"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Azure Percept DK에 직렬 연결

아래 단계를 따라 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)를 통해 Azure Percept DK에 대한 직렬 연결을 설정합니다.

> [!WARNING]
> 극단적인 실패 사례(예, 디바이스를 먹통으로 만든 경우)를 제외하면 devkit은 직렬로 연결하려 하지 **마세요**. 직렬 케이블에 연결하기 위해 캐리어 보드 엔클로저를 분리하는 것은 매우 어려운 작업이며 Wi-Fi 안테나 케이블을 분해합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- 호스트 PC
- Azure Percept DK
- [USB-TTL 직렬 케이블](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB-TTL 직렬 케이블.":::

## <a name="start-the-serial-connection"></a>직렬 연결 시작

1. 캐리어 보드가 80/20 레일에 연결되어 있는 경우 devkit 시작 카드에 포함된 6각형 키를 사용해 레일에서 분리합니다.

1. 캐리어 보드 인클로저 아래쪽의 나사를 제거하고 마더보드를 꺼냅니다.

    > [!WARNING]
    > 마더보드를 분리하면 Wi-Fi 안테나 케이블이 분리됩니다. 직렬 연결이 디바이스를 복구할 최후의 수단인 경우가 아니라면 직렬 연결은 진행하지 **마세요**.

1. 히트싱크를 분리합니다.

1. GPIO 핀에서 점퍼 보드를 분리합니다.

    > [!TIP]
    > 분리하기 전에 점퍼 보드의 방향을 메모하세요. 예를 들어 참조용으로 점퍼 보드에서 회로를 향하는 쪽에 화살표를 그려놓거나 스티커를 붙여놓습니다. 점퍼 보드에는 키가 없어서 캐리어 보드 재조립 시에 잘못해서 뒤집힌 채로 연결될 수도 있습니다.

1. 아래와 같이 [USB-TTL 직렬 케이블](https://www.adafruit.com/product/954)을 마더보드의 GPIO 핀에 연결합니다.

    - 검정색 케이블(GND)을 6번 핀에 연결합니다.
    - 흰색 케이블(RX)을 8번 핀에 연결합니다.
    - 녹색 케이블(TX)을 10번 핀에 연결합니다.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="캐리어 보드 직렬 핀 연결.":::

1. Devkit을 켜고 직렬 케이블의 USB쪽 면을 PC에 연결합니다.

1. Windows에서 **시작** -> **Windows 업데이트 설정** -> **옵션 업데이트 보기** -> **드라이버 업데이트** 로 이동합니다. 목록에서 직렬-USB 업데이트를 찾아 옆에 있는 확인란을 선택하고 **다운로드 및 설치** 를 선택합니다.  

1. 이어서, Windows 디바이스 관리자(**시작** -> **디바이스 관리자**)를 엽니다. **포트** 로 이동하고 **USB에서 UART** 를 선택하여 **속성** 을 엽니다. 디바이스가 연결되어 있는 COM 포트를 확인합니다.

1. **포트 설정** 탭을 선택합니다. **초당 비트 수** 가 115200으로 설정되어 있는지 확인합니다.

1. PuTTY를 엽니다. 다음 내용을 입력하고 **열기** 를 선택해 직렬로 devkit을 연결합니다.

    1. 직렬 회선: COM[포트 번호]
    1. 속도: 115200
    1. 연결 형식: 직렬

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="직렬 매개 변수가 선택된 PuTTY 세션 창.":::
