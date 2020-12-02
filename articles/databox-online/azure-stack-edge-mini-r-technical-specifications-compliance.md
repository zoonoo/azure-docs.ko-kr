---
title: Microsoft Azure Stack Edge 미니 R 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Stack Edge 미니 R 장치에 대 한 기술 사양 및 규정 준수에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: e6dff06e92126e2fc4538273e229dcb0904e3101
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467454"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge 미니 R 기술 사양

Microsoft Azure Stack Edge 미니 R 장치의 하드웨어 구성 요소는이 문서에 설명 된 기술 사양과 규정 표준을 준수 합니다. 기술 사양은 CPU, 메모리, 전원 공급 장치 (PSUs), 저장소 용량, 엔클로저 차원 및 가중치를 설명 합니다.


## <a name="compute-memory-specifications"></a>컴퓨팅, 메모리 사양

Azure Stack Edge 미니 R 장치에는 계산 및 메모리에 대 한 다음과 같은 사양이 있습니다.

| 사양           | 값                  |
|-------------------------|------------------------|
| CPU    | 16 코어 CPU, Intel Xeon-D 1577 |
| 메모리              | 48 g b RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>계산 가속 사양

VPU (시야 프로세싱 Unit)는 Kubernetes, 심층 신경망 및 컴퓨터 비전 기반 응용 프로그램을 지 원하는 모든 Azure Stack Edge 미니 R 장치에 포함 되어 있습니다.

| 사양           | 값                  |
|-------------------------|------------------------|
| 계산 가속 카드         | Intel Movidius 방대한 X VPU <br> 자세한 내용은 [Intel Movidius 방대한 X VPU](https://www.movidius.com/MyriadX) (영문)를 참조 하세요. |


## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge 미니 R 장치에는 1 개의 데이터 디스크와 1 개의 부팅 디스크가 있습니다 (운영 체제 저장소로 사용 됨). 다음 표에서는 장치의 저장소 용량에 대 한 세부 정보를 보여 줍니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    SSD(반도체 드라이브)의 수     |    1TB 디스크 2 개 <br> 하나의 데이터 디스크 및 하나의 부팅 디스크                  |
|    단일 SSD 용량                     |    1TB               |
|    총 용량 (데이터만)              |    1TB              |
|    총 사용 가능한 용량 *                  |    ~ 750 G B        |

**일부 공간은 내부용으로 예약되어 있습니다.*

## <a name="network-specifications"></a>네트워크 사양

Azure Stack Edge 미니 R 장치에는 다음과 같은 네트워크 사양이 있습니다.


|사양  |값  |
|---------|---------|
|네트워크 인터페이스    |2 x 10 Gbe SFP + <br> 로컬 UI에서 포트 3 및 포트 4로 표시 됩니다.           |
|네트워크 인터페이스    |2 x 1Gbe RJ45 <br> 로컬 UI에 포트 1 및 포트 2로 표시 됩니다.          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge 미니 R 장치에는 전원을 공급 하 고 온보드 배터리를 청구 하는 외부 85 W AC 어댑터가 포함 됩니다.

다음 표에서는 전원 공급 장치 사양을 보여 줍니다.

| 사양           | 값                      |
|-------------------------|----------------------------|
| 최대 출력 전원    | 85 W                       |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위 지정: 100-240V AC |



## <a name="included-battery"></a>포함 된 배터리

Azure Stack Edge 미니 R 장치에는 전원 공급 장치에서 청구 되는 온보드 배터리도 포함 됩니다. 

추가 유형 2590 배터리를 온보드 배터리와 함께 사용 하 여 요금 간의 장치 사용을 확장할 수 있습니다. 이 배터리는 사용 국가에서 적용 가능한 모든 안전, 교통 및 환경 규정을 준수 해야 합니다.


| 사양           | 값                      |
|-------------------------|----------------------------|
| 배터리 용량 온보드 | 73 WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

다음 표에는 장치 크기와 견고한 사례가 밀리미터 및 인치로 나열 되어 있습니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    68            |    2.68          |
|    너비          |    208          |      8.19          |
|    길이          |   259           |    10.20          |


### <a name="enclosure-weight"></a>엔클로저 무게

다음 표에는 배터리를 비롯 한 장치의 가중치가 나열 되어 있습니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    장치의 총 무게     |    775          |

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양


이 섹션에서는 온도, 습도 및 고도와 같은 엔클로저 환경과 관련된 사양을 나열합니다.


|     사양             |     Description                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     온도 범위          |     0 – 43 ° C (운영)                                              |
|     Vibration                  |     MIL-STD-810 메서드 514.7 *<br> 절차 I CAT 4, 20                  |
|     충격                      |     MIL-STD-810 메서드 516.7 *<br> 프로시저 IV, 로지스틱                 |
|     고도                   |     작동: 1만 피트<br> 비작동: 4만 피트          |

**모든 참조는 MIL-표준-810G 변경 1 (2014)에 대 한 것입니다.*


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 미니 R 배포](azure-stack-edge-placeholder.md)
