---
title: Microsoft Azure Stack Edge Pro R 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Stack Edge Pro R 장치에 대 한 기술 사양 및 규정 준수에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467298"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R 기술 사양

Azure Stack Edge Pro R 장치의 하드웨어 구성 요소는이 문서에 설명 된 기술 사양을 준수 합니다. 기술 사양은 PSU(전원 공급 장치), 스토리지 용량, 엔클로저 및 환경 표준을 설명합니다.


## <a name="compute-memory-specifications"></a>컴퓨팅, 메모리 사양

Azure Stack Edge Pro R 장치에는 계산 및 메모리에 대 한 다음과 같은 사양이 있습니다.

| 사양       | 값                  |
|---------------------|------------------------|
| CPU    | 2 X 10 코어 CPU, Intel Xeon 실버 4114 |
| 메모리              | 256 g b RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>계산 가속 사양

GPU (그래픽 처리 장치)는 Kubernetes, 심층 학습 및 기계 학습 시나리오를 가능 하 게 하는 모든 장치에 포함 됩니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| GPU   | 단일 nVidia T4 GPU <br> 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조 하세요.| 

## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge Pro R 장치에는 고성능 팬이 있는 2 개의 100-240 V 전원 공급 장치 (PSUs)가 있습니다. 두 대의 PSU는 중복 전원 구성을 제공합니다. PSU가 실패하면, 디바이스는 실패한 모듈이 교체될 때까지 다른 PSU에서 정상적으로 작동합니다. 다음 표에는 PSU의 기술 사양이 나열되어 있습니다.

| 사양           | 550 W PSU                  |
|-------------------------|----------------------------|
| 최대 출력 전원    | 550 W                      |
| 열 분산 (최대)                   | 2891 BTU/hr                |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위: 115-230 V AC |
| 핫 플러그형           | 예                        |

## <a name="network-specifications"></a>네트워크 사양

Azure Stack Edge Pro R 장치에는 PORT1-PORT4의 4 개 네트워크 인터페이스가 있습니다. 


|사양  |Description                              |
|----------------------|----------------------------------|
|네트워크 인터페이스    |**2 x 1Gbe RJ45** <br> 포트 1은 초기 설치를 위한 관리 인터페이스로 사용 되며 기본적으로 정적입니다. 초기 설치가 완료 된 후에는 모든 IP 주소를 사용 하 여 데이터에 대 한 인터페이스를 사용할 수 있습니다. 그러나 다시 설정 시 인터페이스는 정적 IP로 돌아갑니다. <br>다른 인터페이스 포트 2는 사용자가 구성할 수 있으며, 데이터 전송에 사용할 수 있으며, 기본적으로 DHCP입니다.     |
|네트워크 인터페이스    |**2 x 25 Gbe SFP28** <br> 이러한 데이터 인터페이스 포트 3 및 포트 4는 DHCP (기본값) 또는 정적으로 구성할 수 있습니다.            |

Azure Stack Edge Pro R 장치는 다음과 같은 네트워크 하드웨어를 포함 합니다.

* **Mellanox 이중 포트 25G connectx-3-4 채널 네트워크 어댑터** -포트 3 및 포트 4입니다. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

이러한 네트워크 카드에 대해 지원 되는 케이블, 스위치 및 트랜시버의 전체 목록은 [Mellanox 이중 포트 25G connectx-3-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)으로 이동 하세요.

## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge Pro R 장치에는 데이터 디스크 8 개, 운영 체제 디스크 역할을 하는 2 M. 2 SATA 디스크가 있습니다. 자세한 내용은 [M. 2 SATA 디스크](https://en.wikipedia.org/wiki/M.2)를 참조 하세요.

#### <a name="storage-for-1-node-device"></a>1 개 노드 장치용 저장소

다음 표에는 1 개 노드 장치의 저장소 용량에 대 한 세부 정보가 나와 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    SSD(반도체 드라이브)의 수     |    8                  |
|    단일 SSD 용량                     |    8 TB               |
|    총 용량                          |    64TB              |
|    총 사용 가능한 용량 *                  |    ~ 42 TB          |

**일부 공간은 내부용으로 예약되어 있습니다.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기 

다음 표에는 장치 크기와 견고한 사례가 밀리미터 및 인치로 나열 되어 있습니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    301.2            |    11.86       |
|    너비          |    604.5            |    23.80       |
|    길이         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>엔클로저 무게 

디바이스의 무게는 엔클로저 구성에 따라 달라집니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    단일 노드 장치 + 종단 캡이 있는 견고한 사례의 총 가중치     |    ~ 114          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양

이 섹션에서는 온도, 진동, 충격, 고도 등 인클로저 환경과 관련 된 사양을 나열 합니다.


|     사양              |     값    |
|--------------------------------|-------------------------------------------------------------------|
|     온도 범위          |     0 – 43 ° C (운영)    |
|     Vibration                  |     MIL-STD-810 메서드 514.7 *<br>절차 I CAT 4, 20                  |
|     충격                      |     MIL-STD-810 메서드 516.7 *<br>프로시저 IV, 로지스틱                 |
|     고도                   |     작동: 1만 피트<br>비작동: 4만 피트          |

**모든 참조는 MIL-표준-810G 변경 1 (2014)에 대 한 것입니다.*

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포](azure-stack-edge-placeholder.md)
