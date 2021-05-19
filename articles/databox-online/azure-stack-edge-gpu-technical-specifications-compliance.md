---
title: GPU가 있는 Microsoft Azure Stack Edge Pro 기술 사양 및 규정 준수 | Microsoft Docs
description: GPU가 있는 Azure Stack Edge Pro 디바이스에 대한 기술 사양 및 규정 준수에 대해 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: e0eb976f655308082671afe2dc1923f082a3373b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303171"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>GPU가 있는 Azure Stack Edge Pro에 대한 기술 사양 및 규정 준수 

GPU(그래픽 처리 장치)가 온보딩된 Azure Stack Edge Pro의 하드웨어 구성 요소는 이 문서에 설명된 기술 사양 및 규제 표준을 준수합니다. 기술 사양은 하드웨어, PSU(전원 공급 장치), 스토리지 용량, 엔클로저 및 환경 표준을 설명합니다.

## <a name="compute-and-memory-specifications"></a>컴퓨팅 및 메모리 사양

Azure Stack Edge Pro 디바이스에는 컴퓨팅 및 메모리에 대한 다음과 같은 사양이 있습니다.

| 사양  | 값                                                                       |
|----------------|-----------------------------------------------------------------------------|
| CPU 종류       | 이중 Intel Xeon Silver 4214(Cascade Lake) CPU                              |
| CPU: 원시       | 총 24개 코어, 총 48개 vCPU                                              |
| CPU: 사용 가능    | vCPU 40대                                                                    |
| 메모리 유형    | Dell 호환 가능 16GB PC4-23400 DDR4-2933Mhz 2Rx8 1.2v ECC Registered RDIMM |
| 메모리: 원시    | 128GB RAM(8 x 16GB)                                                      |
| 메모리: 사용 가능 | 102GB RAM                                                                  |


## <a name="compute-acceleration-specifications"></a>컴퓨팅 가속 사양

GPU(그래픽 처리 장치)는 Kubernetes, 딥 러닝 및 기계 학습 시나리오를 지원하는 모든 Azure Stack Edge Pro 디바이스에 포함되어 있습니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| GPU   | 하나 또는 두 개의 nVidia T4 GPU <br> 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조하세요.| 


## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge Pro 디바이스에는 고성능 팬이 달린 2대의 100-240V PSU(전원 공급 디바이스)가 있습니다. 두 대의 PSU는 중복 전원 구성을 제공합니다. PSU가 실패하면, 디바이스는 실패한 모듈이 교체될 때까지 다른 PSU에서 정상적으로 작동합니다. 다음 표에는 PSU의 기술 사양이 나열되어 있습니다.

| 사양           | 750W PSU                  |
|-------------------------|----------------------------|
| 최대 출력 전원    | 750W                      |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위 지정: 100-240V AC |
| 핫 플러그형           | 예                        |


## <a name="network-interface-specifications"></a>네트워크 인터페이스 사양

Azure Stack Edge Pro 디바이스에는 6개의 네트워크 인터페이스인 PORT1-PORT6가 있습니다.

| 사양           | Description                 |
|-------------------------|----------------------------|
|  네트워크 인터페이스    | **2 X 1 GbE 인터페이스** – 1 관리 인터페이스 포트 1은 초기 설치에 사용되며 기본적으로 정적입니다. 초기 설치가 완료된 후에는 모든 IP 주소를 사용하여 데이터에 대한 인터페이스를 사용할 수 있습니다. 그러나 다시 설정 시 인터페이스는 정적 IP로 돌아갑니다. <br>다른 인터페이스 포트 2는 사용자가 구성 가능하고 데이터 전송에 사용할 수 있으며, 기본적으로 DHCP입니다. <br>**4 X 25-GbE 인터페이스** – 포트 3부터 포트 6까지의 데이터 인터페이스는 사용자에 의해 DHCP(기본값) 또는 정적으로 구성할 수 있습니다. 또한 10-GbE 인터페이스로 작동할 수 있습니다.  | 

Azure Stack Edge Pro 디바이스에는 다음과 같은 네트워크 하드웨어가 있습니다.

* **사용자 지정 Microsoft `Qlogic` Cavium 25G NDC 어댑터** - 포트 1~포트 4.
* **Mellanox 이중 포트 25G ConnectX-4 채널 네트워크 어댑터** - 포트 5 및 포트 6

Mellanox 카드에 대한 세부 정보는 다음과 같습니다.

| 매개 변수           | Description                 |
|-------------------------|----------------------------|
| 모델    | ConnectX®-4 Lx EN 네트워크 인터페이스 카드                      |
| 모델 설명               | 25GbE 이중 포트 SFP28; PCIe3.0 x8; ROHS R6                    |
| 디바이스 부품 번호(R640) | MCX4121A-ACAT  |
| PSID(R640)           | MT_2420110034                         |

이러한 네트워크 카드에 대해 지원되는 케이블, 스위치 및 트랜시버의 전체 목록은 다음으로 이동합니다.

- [`Qlogic` Cavium 25G NDC 어댑터 상호 운용성 매트릭스](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox 이중 포트 25G ConnectX-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)  

## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge Pro 디바이스에는 각각 1.6TB의 용량의 2.5" NVMe DC P4610 SSD가 5개 있습니다. 부팅 드라이브는 240GB의 SATA SSD입니다. 디바이스에 사용할 수 있는 총 용량은 약 4.19TB입니다. 다음 표에는 디바이스의 스토리지 용량에 대한 세부 정보가 나와 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    NVMe SSD 수                     |    5                  |
|    단일 NVMe SSD 용량                |    1.6TB             |
|    부팅 SATA SSD(Solid State Drive)      |    1                  |
|    부팅 SSD 용량                       |    240GB             |
|    총 용량                          |    8.0 TB             |
|    총 사용 가능한 용량                   |    ~ 4.19TB          |
|    RAID 구성                      |    미러링 및 패리티의 조합을 사용한 스토리지 공간 다이렉트  |
|    SAS 컨트롤러                          |    HBA330 12Gbps     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

다음 표에서 인치 및 밀리미터로 1U 디바이스 엔클로저의 크기를 나타냅니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    44.45            |    1.75"       |
|    너비          |    434.1            |    17.09"      |
|    길이         |    740.4            |    29.15"      |

다음 표에서는 인치 및 밀리미터로 배송 패키지의 크기를 나타냅니다.

|     패키지     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    311.2            |    12.25인치          |
|    너비          |    642.8            |    25.31인치          |
|    길이         |    1,051.1          |    41.38인치          |

### <a name="enclosure-weight"></a>엔클로저 무게

디바이스 패키지의 무게는 66lbs이며 들어 올리려면 두 사람이 필요합니다. 디바이스의 무게는 엔클로저 구성에 따라 달라집니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    포장을 포함한 총 무게       |    61lbs          |
|    디바이스의 무게                       |    35lbs          |

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양

이 섹션에서는 온도, 습도 및 고도와 같은 엔클로저 환경과 관련된 사양을 나열합니다.

### <a name="temperature-and-humidity"></a>온도 및 습도

|     인클로저         |     주변 온도 범위     |     주변 상대 습도     |     최대 이슬점     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    작동        |    10°C - 35°C(50°F - 86°F)         |    10% - 80% 비응결         |    29°C(84°F)            |
|    작동 불가능    |    -40°C - 65°C(-40°F - 149°F)     |    5% - 95% 비응결          |    33°C(91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>기류, 고도, 충격, 진동, 방향, 안전성 및 EMC

|     인클로저                           |     운영 사양                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    기류                              |    시스템의 공기는 앞에서 뒤로 흐릅니다. 압력이 낮고, 후면 배기가 설치된 시스템을 작동할 수 있어야 합니다. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
| IP(방진 방수)                 |    일반적으로 실내에서 사용하는 랙 탑재 장비는 방진 방수에 대해 테스트되지 않습니다(전기 엔클로저의 경우 고체와 액체에 대한 보호가 테스트되지 않음). 제조업체의 안전 평가는 IPXO(방진 방수 없음)를 표시합니다.  |
|    작동 최대 고도        |    [작동 온도 출력 저하 사양](#operating-temperature-de-rating-specifications)에 따라 결정된 대로 최대 작동 온도 출력이 저하된 3048m(1만 피트)                                                                                |
|    작동 불가능 최대 고도    |    12,000m(39,370피트)                                                                                                                                                                                         |
|    충격, 작동                   |    11밀리초 동안 6G(6개 방향)                                                                                                                                                                         |
|    충격, 작동 불가능               |    2밀리초 동안 71G(6개 방향)                                                                                                                                                                           |
|    진동, 작동               |    0.26G<sub>RMS</sub> 5Hz-350Hz 임의                                                                                                                                                                                     |
|    진동, 작동 불가능           |    15분 동안 1.88G<sub>RMS</sub> 10Hz-500Hz(6개 측면으로 모두 테스트됨)                                                                                                                                                  |
|    방향 및 탑재             |    표준 19" 랙 탑재(1U)                                                                                                                                                                                       |
|    안전성 및 승인                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014(Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    에너지             |    위원회 규정(EU) 번호 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>작동 온도 출력 저하 사양

|     작동 온도 출력 저하     |     주변 온도 범위                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    최대 35°C(95°F)                       |    최대 온도는 950m(3,117피트) 위부터 1°C/300m(1°F/547피트)씩 감소합니다.    |
|    35°C - 40°C(95°F - 104°F)            |    최대 온도는 950m(3,117피트) 위부터 1°C/175m(1°F/319피트)씩 감소합니다.    |
|    40°C - 45°C(104°F - 113°F)           |    최대 온도는 950m(3,117피트) 위부터 1°C/125m(1°F/228피트)씩 감소합니다.    |

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 배포](azure-stack-edge-gpu-deploy-prep.md)
