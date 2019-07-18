---
title: Azure Data Box Edge 물리적 디바이스를 설치 - 개봉하고, 랙에 탑재하고, 케이블로 연결하는 방법에 대한 자습서 | Microsoft Docs
description: Azure Data Box Edge 설치에 대한 두 번째 자습서에서는 물리적 디바이스의 포장을 풀고, 랙에 탑재하고, 케이블을 연결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 0a9939155d92897019dc1ad5651d249cda11b993
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476931"
---
# <a name="tutorial-install-azure-data-box-edge"></a>자습서: Azure Data Box Edge 설치

이 자습서에서는 Data Box Edge 물리적 디바이스를 설치하는 방법에 대해 설명합니다. 설치 절차에는 포장 풀기, 랙 탑재 및 디바이스에 케이블 연결이 포함됩니다. 

설치가 완료되려면 약 2시간 정도 걸릴 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 디바이스 포장 풀기
> * 랙에 디바이스 탑재
> * 디바이스에 케이블 연결

## <a name="prerequisites"></a>필수 조건

물리적 디바이스를 설치하기 위한 필수 조건은 다음과 같습니다.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge 리소스의 경우

시작하기 전에 다음 사항을 확인합니다.

* [Azure Data Box Edge 배포 준비](data-box-edge-deploy-prep.md)의 모든 단계를 완료했습니다.
    * 디바이스를 배포할 Data Box Edge 리소스를 만들었습니다.
    * Data Box Edge 리소스를 통해 디바이스를 활성화하기 위한 활성화 키를 생성했습니다.

 
### <a name="for-the-data-box-edge-physical-device"></a>Data Box Edge 물리적 디바이스의 경우

디바이스를 배포하기 전에 다음 사항을 확인합니다.

- 디바이스를 평평하고 안정적이고 흔들림 없는 작업대에 안전하게 놓습니다.
- 장치를 설치하려는 사이트에 다음 장치가 있는지 확인합니다.
    - 독립적인 전원의 표준 AC 전원 장치

        또는
    - UPS(무정전 전원 공급 장치)가 장착된 PDU(랙 전원 분배 장치)
    - 디바이스를 탑재하려는 랙에 사용 가능한 하나의 1U 슬롯

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우

시작하기 전에

- Data Box Edge를 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성합니다. 자세한 내용은 [Data Box Edge 네트워킹 요구 사항](data-box-edge-system-requirements.md#networking-port-requirements)을 참조하세요.

- 디바이스가 최적으로 작동할 수 있도록 최소 인터넷 대역폭이 20Mbps인지 확인합니다.


## <a name="unpack-the-device"></a>디바이스 포장 풀기

이 디바이스는 하나의 상자에 제공됩니다. 디바이스의 포장을 풀려면 다음 단계를 수행합니다. 

1. 평평한 지면에 상자를 놓습니다.
2. 상자와 포장 발포 폼에 찌그러짐, 자르기, 물에 의한 손상 또는 기타 손상이 없는지 검사합니다. 상자나 포장이 심각하게 손상된 경우에는 열지 마세요. 디바이스가 제대로 작동하는지 평가하려면 [Microsoft 지원]에 문의하세요.
3. 상자를 개봉합니다. 상자를 개봉한 후 다음이 있는지 확인합니다.
    - 단일 인클로저 Data Box Edge 디바이스 1개
    - 전원 코드 2개
    - 하나의 레일 키트 어셈블리
    - 보안, 환경 및 규정 정보 소책자

여기에 나열된 항목 중에 받지 못한 것이 있으면 Data Box Edge 지원에 문의하세요. 다음 단계는 디바이스를 랙에 탑재하는 것입니다.


## <a name="rack-the-device"></a>랙에 디바이스 탑재

디바이스는 표준 19인치 랙에 설치해야 합니다. 다음 절차에 따라 표준 19인치 랙에 디바이스를 탑재합니다.

> [!IMPORTANT]
> Data Box Edge 디바이스는 올바른 작동을 위해 랙에 탑재되어야 합니다.


### <a name="prerequisites"></a>필수 조건

- 시작하기 전에 보안, 환경 및 규정 정보 소책자의 보안 지침을 읽으세요. 이 소책자는 디바이스와 함께 배송되었습니다.
- 랙 엔클로저의 맨 아래에 가장 가까운 할당된 공간에 레일 설치를 시작합니다.
- 툴링 레일 장착 구성의 경우:
    -  나사 8개를 제공해야 합니다. #10-32, #12 ~ 24, #M5 또는 #M6 나사의 헤드 지름은 10mm(0.4")보다 작아야 합니다.
    -  일자 드라이버가 필요합니다.

### <a name="identify-the-rail-kit-contents"></a>레일 키트 콘텐츠 식별

레일 키트 어셈블리를 설치하기 위한 구성 요소를 찾습니다.
1. A7 Dell ReadyRails II 슬라이딩 레일 어셈블리 2개
2. 벨크로 스트랩 2개

    ![레일 키트 콘텐츠 식별](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>도구가 필요 없는 레일의 설치 및 제거(사각형 구멍 또는 원형 구멍 랙)

> [!TIP]
> 이 옵션은 레일을 나사산이 없는 랙의 정사각형 또는 원형 구멍에 설치하고 분리하기 위한 도구가 필요하지 않으므로 도구가 없는 방식입니다.

1. **FRONT** 레이블이 표시된 왼쪽 및 오른쪽 레일 끝 부분을 안쪽을 향하도록 배치하고 각 끝 부분을 수직 랙 플랜지 전면의 구멍에 끼워지도록 방향을 조정합니다.
2. 각 끝 부분을 원하는 U 공간의 하단 및 상단 구멍에 맞춥니다.
3. 수직 랙 플랜지에 완전히 끼워지고 래치가 딸깍 소리를 내며 고정될 때까지 레일의 뒷쪽 끝을 연결합니다. 이러한 단계를 반복하여 프론트 엔드 피스를 수직 랙 플랜지에 배치하고 끼웁니다.
4. 레일을 제거하려면 끝 부분 중간 지점에 있는 래치 해제 버튼을 당긴 상태로 각 레일을 분리합니다.

    ![도구가 필요 없는 레일의 설치 및 제거](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>툴링 레일의 설치 및 제거(나사 구멍 랙)

> [!TIP]
> 이 옵션은 레일을 나사산이 있는 랙의 원형 구멍에 설치하고 분리하기 위해 도구(_일자 드라이버_)가 필요하므로 도구 방식입니다.

1. 일자 드라이버를 사용하여 전면 및 후면 탑재 브라켓에서 핀을 제거합니다.
2. 레일 래치 하위 부품을 당긴 후 회전시켜서 탑재 브라켓에서 제거합니다.
3. 두 쌍의 나사를 사용하여 왼쪽 및 오른쪽 탑재 레일을 전면 수직 랙 플랜지에 연결합니다.
4. 왼쪽 및 오른쪽 후면 브라켓을 후면 수직 랙 플랜지의 앞에 댄 후, 두 쌍의 나사를 사용하여 연결합니다.

    ![툴링 레일의 설치 및 제거](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>랙에 시스템 설치

1. 내부 슬라이드 레일이 제자리에 고정될 때까지 랙에서 잡아당깁니다.
2. 시스템의 각 면에서 후면 레일 스탠드오프를 찾아 슬라이드 어셈블리에서 후면 J-슬롯 쪽으로 내립니다. 모든 레일 스탠드오프가 J-슬롯에 장착될 때까지 시스템을 아래로 회전시킵니다.
3. 잠금 레버가 딸깍 소리를 내며 고정될 때까지 시스템을 안쪽으로 밀어넣습니다.
4. 양쪽 레일의 슬라이드 해제 잠금 버튼을 누르고 시스템을 랙으로 밀어넣습니다.

    ![랙에 시스템 설치](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>랙에서 시스템 제거

1. 내부 레일 양쪽에서 잠금 레버를 찾습니다.
2. 해당 해제 위치까지 회전시켜서 각 레버의 잠금을 해제합니다.
3. 시스템의 양쪽을 단단히 잡고 레일 스탠드오프가 J-슬롯 전면에 올 때까지 앞으로 당깁니다. 시스템으로 들어올려 랙에서 분리하고 수평면에 놓습니다.

    ![랙에서 시스템 제거](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>슬램 래치 연결 및 해제

> [!NOTE]
> 슬램 래치가 장착되지 않은 시스템의 경우 이 절차의 3단계에서 설명한 대로 나사를 사용하여 시스템을 고정하세요.

1. 전면을 본 상태에서 시스템 양쪽에 있는 슬램 래치를 찾습니다.
2. 래치는 시스템이 랙과 맞물리면 자동으로 연결되며 래치를 위로 당기면 해제됩니다.
3. 랙 또는 기타 불안정한 환경에서 배송하기 위해 시스템을 고정하려면 각 래치 아래에 있는 하드 마운트 나사를 찾아 #2 십자 드라이버로 각 나사를 조입니다.

    ![슬램 래치 연결 및 해제](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)

### <a name="route-the-cables"></a>케이블 연결

> [!NOTE]
>  선택 사항인 CMA(케이블 관리 암)를 주문하지 않은 경우 레일 키트에 제공된 두 개의 벨크로 스트랩을 사용하여 시스템 후면에서 케이블을 연결합니다.

1. 양쪽 랙 플랜지의 내부 측면에 외부 CMA 브라켓을 찾습니다.
2. 케이블을 시스템 커넥터에서 왼쪽 및 오른쪽으로 당겨 빼내서 조심스럽게 모읍니다.
3. 끈을 후크에 꿰고 시스템의 각 측면에 있는 바깥쪽 CMA 브래킷의 슬롯을 통해 감아서 케이블 다발을 고정합니다.


    ![케이블 연결](./media/data-box-edge-deploy-install/routing-cables.png)

## <a name="cable-the-device"></a>디바이스에 케이블 연결

다음 절차에서는 전원 및 네트워크를 위해 Data Box Edge 디바이스에 케이블을 연결하는 방법에 대해 설명합니다.

디바이스 케이블 연결을 시작하기 전에 다음이 필요합니다.

- 포장을 풀고 랙에 탑재된 Data Box Edge 물리적 디바이스
- 전원 케이블 2개
- 관리 인터페이스에 연결하는 하나 이상의 1-GbE RJ-45 네트워크 케이블 디바이스에는 두 개의 1-GbE 네트워크 인터페이스가 있습니다. 하나는 관리용이고 하나는 데이터용입니다.
- 구성할 각 데이터 네트워크 인터페이스에 대한 25-GbE SFP+ 동 케이블 1개 하나 이상의 데이터 네트워크 인터페이스 - PORT 2, PORT 3, PORT 4, PORT 5 또는 PORT 6을 인터넷에 연결해야 합니다(Azure에 연결).  
- 2개 전원 분배 장치에 대한 액세스(권장)

> [!NOTE]
> - 단 하나의 데이터 네트워크 인터페이스를 연결하는 경우 PORT 3, PORT 4, PORT 5 또는 PORT 6과 같은 25/10-GbE 네트워크 인터페이스를 사용하여 데이터를 Azure에 보내는 것이 좋습니다. 
> - 최상의 성능을 얻고 많은 양의 데이터를 처리하려면 모든 데이터 포트를 연결하는 것이 좋습니다.
> - Data Box Edge 디바이스는 데이터 원본 서버의 데이터를 수집할 수 있도록 데이터 센터 네트워크에 연결해야 합니다.

Data Box Edge 디바이스:

- 전면 패널에는 디스크 드라이브와 전원 단추가 있습니다.

    - 디바이스의 전면에 10개의 디스크 슬롯이 있습니다.
    - 슬롯 0에는 운영 체제 디스크로 사용하는 240GB SATA 드라이브가 있습니다. 슬롯 1은 비어 있고 슬롯 2 ~ 9는 데이터 디스크로 사용된 NVMe SSD입니다.
- 백플레인에는 중복 PSU(전원 공급 장치)가 있습니다.
- 백플레인에는 6개의 네트워크 인터페이스가 있습니다.

    - 1-Gbps 인터페이스 2개
    - 10-Gbps 인터페이스로도 사용할 수 있는 25-Gbps 인터페이스 4개
    - BMC(베이스보드 관리 컨트롤러)

- 백플레인에는 6개 포트에 해당하는 네트워크 카드가 두 개 있습니다.

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

이러한 네트워크 카드에 대해 지원되는 케이블, 스위치 및 트랜시버의 전체 목록은 [Cavium FastlinQ 41000 시리즈 상호 운용성 행렬](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)로 이동합니다.
 
디바이스의 전원 및 네트워크에 케이블을 연결하려면 다음 단계를 수행합니다.

1. 디바이스의 백플레인에서 다양한 포트를 식별합니다.

    ![케이블로 연결된 디바이스의 백플레인](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. 디바이스의 전면에서 디스크 슬롯과 전원 단추를 찾습니다.

    ![디바이스의 프런트플레인](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. 인클로저의 각 PSU에 전원 코드를 연결합니다. 고가용성을 보장하려면 두 PSU를 모두 설치하고 서로 다른 전원에 연결합니다.
4. 전원 코드를 랙 PDU(전원 분배 장치)에 연결합니다. 두 PSU에서 별도의 전원을 사용하는지 확인합니다.
5. 전원 단추를 눌러 디바이스를 켭니다.
6. 1-GbE 네트워크 인터페이스 PORT 1을 물리적 디바이스 구성에 사용되는 컴퓨터에 연결합니다. PORT 1은 전용 관리 인터페이스입니다.
7. 하나 이상의 PORT 2, PORT 3, PORT 4, PORT 5 또는 PORT 6을 데이터 센터 네트워크/인터넷에 연결합니다.

    - PORT 2를 연결하는 경우 RJ-45 네트워크 케이블을 사용합니다.
    - 10/25-GbE 네트워크 인터페이스의 경우 SFP+ 동선 케이블을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법과 같은 Data Box Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 디바이스 포장 풀기
> * 랙에 디바이스 탑재
> * 디바이스에 케이블 연결

디바이스를 연결하고, 설정하고, 활성화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Data Box Edge 연결 및 설정](./data-box-edge-deploy-connect-setup-activate.md)
