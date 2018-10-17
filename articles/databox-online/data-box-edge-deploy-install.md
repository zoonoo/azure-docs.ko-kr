---
title: Azure Data Box Edge 물리적 장치 설치 자습서 | Microsoft Docs
description: Azure Data Box Edge를 설치하는 두 번째 자습서에서는 물리적 장치의 포장을 풀고, 랙에 탑재하고, 케이블을 연결하는 방법을 설명합니다.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 17602abd9dab590c806c33779cf127f9a5305f64
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48833209"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>자습서: Azure Data Box Edge 설치(미리 보기)

이 자습서에서는 Data Box Edge 물리적 장치를 설치하는 방법에 대해 설명합니다. 설치 절차에는 포장 풀기, 랙 탑재 및 장치에 케이블 연결이 포함됩니다. 

설치를 완료하는 데 약 2시간이 걸릴 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 장치 포장 풀기
> * 랙에 장치 탑재
> * 장치에 케이블 연결

> [!IMPORTANT]
> Data Box Edge는 미리 보기로 있습니다. 이 솔루션을 주문하고 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요.

## <a name="prerequisites"></a>필수 조건

물리적 장치를 설치하기 위한 필수 조건은 다음과 같습니다.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge 리소스의 경우

시작하기 전에 다음 사항을 확인합니다.

* [Data Box Edge에 대한 포털 준비](data-box-edge-deploy-prep.md)의 모든 단계를 완료했습니다.
    * 장치를 배포할 Data Box Edge 리소스를 만들었습니다.
    * Data Box Edge 리소스를 통해 장치를 활성화하기 위한 활성화 키를 생성했습니다.

 
### <a name="for-the-data-box-edge-physical-device"></a>Data Box Edge 물리적 장치의 경우

장치를 배포하기 전에 다음 사항을 확인합니다.

- 장치를 평평하고 안정적이고 흔들림 없는 작업대(또는 이와 유사한)에 안전하게 놓습니다.
- 장치를 설치하려는 사이트에 다음 장치가 있는지 확인합니다.
    - 독립적인 전원의 표준 AC 전원 장치 또는
    - UPS(무정전 전원 공급 장치)가 장착된 PDU(랙 전원 분배 장치)
- 장치를 탑재하려는 랙에 하나의 1U 슬롯을 사용할 수 있는지 확인합니다.

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우

시작하기 전에

- Data Box Edge를 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성합니다. 자세한 내용은 [Data Box Edge 네트워킹 요구 사항](data-box-gateway-system-requirements.md#networking-requirements)을 참조하세요.
- 장치가 최적으로 작동할 수 있도록 최소 인터넷 대역폭이 20Mbps인지 확인합니다.


## <a name="unpack-the-device"></a>장치 포장 풀기

이 장치는 하나의 상자에 제공됩니다. 장치의 포장을 풀려면 다음 단계를 수행합니다. 

1. 평평한 지면에 상자를 놓습니다.
2. 상자와 포장 발포 폼에 찌그러짐, 자르기, 물에 의한 손상 또는 기타 손상이 없는지 검사합니다. 상자나 포장이 심각하게 손상된 경우에는 상자를 열지 마세요. 장치가 제대로 작동하는지 평가하려면 [Microsoft 지원]에 문의하세요.
3. 상자를 개봉합니다. 상자를 개봉한 후 다음이 있는지 확인합니다.
    - 단일 인클로저 Edge 장치 1개
    - 전원 코드 2개
    - 공구가 필요 없는 슬라이드형 랙 탑재 키트 1개(측면 레일 2개 및 장착 하드웨어 포함)
4. 위에 나열된 항목 중에 받지 못한 것이 있으면 [Data Box Edge 지원]에 문의하세요. 다음 단계는 장치를 랙에 탑재하는 것입니다.


## <a name="rack-the-device"></a>랙에 장치 탑재

장치는 표준 19인치 랙에 설치해야 합니다. 다음 절차에 따라 전면 및 후면 포스트가 있는 표준 19인치 랙에 장치를 탑재합니다.

> [!IMPORTANT]
> Data Box Edge 장치는 올바른 작동을 위해 랙에 탑재되어야 합니다.


1. 전면 제어기를 당겨 슬라이드 조립 부품에서 내부 레일의 잠금을 해제합니다. 걸쇠 잠금 장치를 풀고 중간 레일을 안쪽으로 밀어 레일을 후퇴시킵니다. 이제 내부 및 외부 레일이 분리됩니다.

    ![랙 탑재 레일 설치](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. 이제 랙 캐비닛 수직 멤버에 외부 레일을 설치합니다. 방향을 잡을 수 있도록 레일 슬라이드가 Front(전면)로 표시되어 있고 그 끝은 인클로저의 앞쪽을 향해 부착됩니다. 
    
    1. 레일 어셈블리의 앞면/뒷면에서 레일 핀을 찾으세요. 랙 포스트 사이에 맞도록 레일을 확장합니다. 먼저 랙의 뒤쪽에 외부 레일을 붙입니다. 후면 탑재 브래킷을 조정하여 후면 랙 탑재 구멍에 맞춥니다.   
    2. 금속 고리가 노출되도록 후면 브래킷의 제동기를 누르고 있습니다. 탑재 구멍에 맞춰 끼운 다음, 제동기에서 손을 뗍니다.
    3. 전면 브래킷을 탑재 구멍에 맞춥니다.
    4. 이제 전면 브래킷이 랙에 고정됩니다. 필요에 따라 포스트를 사용하여 레일을 안전하게 보호하도록 M5 X 10L 나사를 선택적으로 사용할 수 있습니다. 

    ![랙 탑재 레일 설치](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. 내부 레일을 섀시에 부착하려면 내부 레일의 열쇠 구멍 개구부를 섀시 측면의 위치 결정 핀에 맞춥니다. 섀시 위치 결정 핀의 머리 부분을 내부 레일의 열쇠 구멍 개구부로 내밉니다. 레일이 딸깍 소리를 내며 제자리에 고정될 때까지 섀시 전면을 향해 레일을 당깁니다. 다른 내부 레일에서도 반복합니다. 내부 레일을 사용하여 섀시를 슬라이드에 밀어 랙 설치를 완료합니다.

    ![랙 탑재 레일 설치](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>장치에 케이블 연결

다음 절차에서는 전원 및 네트워크를 위해 Edge 장치에 케이블을 연결하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

장치를 케이블로 연결하려면 다음이 필요합니다.

- 포장을 풀고 랙에 탑재된 Edge 물리적 장치
- 전원 케이블 2개 
- 1-GbE RJ-45 네트워크 케이블 2개 및 25-GbE SFP + 동 케이블 4개
- 2개 전원 분배 장치에 대한 액세스(권장)

Edge 장치에는 8개의 NVMe SSD가 있습니다. 전면 패널에는 상태 LED 및 전원 단추도 있습니다. 장치의 후면에는 중복 PSU(전원 공급 장치)가 있습니다. 장치에는 6개의 네트워크 인터페이스(2개의 1-Gbps 인터페이스 및 4개의 25-Gbps 인터페이스)가 있습니다. 장치에는 BMC(베이스보드 관리 컨트롤러)가 있습니다. 장치의 백플레인에서 다양한 포트가 식별됩니다.
 
  ![케이블로 연결된 장치의 백플레인](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
장치의 전원 및 네트워크에 케이블을 연결하려면 다음 단계를 수행합니다.

1. 인클로저의 각 PSU에 전원 코드를 연결합니다. 고가용성을 보장하려면 두 PSU를 모두 설치하고 서로 다른 전원에 연결합니다.
2. 전원 코드를 랙 PDU(전원 분배 장치)에 연결합니다. 두 PSU에서 별도의 전원을 사용하는지 확인합니다.
3. 1-GbE 네트워크 인터페이스 PORT 1을 물리적 장치 구성에 사용되는 컴퓨터에 연결합니다. PORT 1은 전용 관리 인터페이스입니다.
4. RJ-45 네트워크 케이블을 통해 1-GbE 네트워크 인터페이스 PORT 2를 데이터 센터 네트워크/인터넷에 연결합니다. 
5. SFP + 동 케이블을 사용하여 4개의 25-GbE 네트워크 인터페이스 PORT 3, PORT 4, PORT 5 및 PORT 6을 데이터 센터 네트워크/인터넷에 연결합니다. 

> [!NOTE]
> - 하나 이상의 데이터 네트워크 인터페이스(PORT 2, PORT 3, PORT 4, PORT 5 또는 PORT 6)를 인터넷에 연결해야 합니다(Azure에 연결). 
> - PORT 3, PORT 4, PORT 5 또는 PORT 6과 같은 25-GbE 네트워크 인터페이스를 사용하여 데이터를 Azure에 보내는 것이 좋습니다. 
> - Edge 장치는 데이터 원본 서버의 데이터를 수집할 수 있도록 데이터 센터 네트워크에 연결해야 합니다.  


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Data Box Edge 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 장치 포장 풀기
> * 랙에 장치 탑재
> * 장치에 케이블 연결

장치를 연결하고, 설정하고, 활성화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Data Box Edge 연결 및 설정](./data-box-edge-deploy-connect-setup-activate.md)


