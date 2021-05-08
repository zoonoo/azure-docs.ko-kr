---
title: Microsoft Azure Stack Edge Mini R 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Stack Edge Mini R 디바이스에 대한 기술 사양 및 규정 준수에 대해 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727484"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R 기술 사양

Microsoft Azure Stack Edge Mini R 디바이스의 하드웨어 구성 요소는 이 문서에 설명된 기술 사양 및 규제 표준을 준수합니다. 기술 사양은 CPU, 메모리, 전원 공급 장치(PSU), 스토리지 용량, 인클로저 크기, 무게를 설명합니다.


## <a name="compute-memory-specifications"></a>컴퓨팅, 메모리 사양

Azure Stack Edge Mini R 디바이스의 컴퓨팅 및 메모리 사양은 다음과 같습니다.

| 사양           | 값                  |
|-------------------------|------------------------|
| CPU    | 16코어 CPU, Intel Xeon-D 1577 |
| 메모리              | 48GB RAM(2400MT/s)                  |


## <a name="compute-acceleration-specifications"></a>컴퓨팅 가속 사양

Kubernetes, 심층 신경망, 컴퓨터 비전 기반 애플리케이션을 지원하는 모든 Azure Stack Edge Mini R 디바이스에 Vision Processing Unit(VPU)이 포함됩니다.

| 사양           | 값                  |
|-------------------------|------------------------|
| 컴퓨팅 가속 카드         | Intel Movidius Myriad X VPU <br> 자세한 내용은 [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)를 참조하세요. |


## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge Mini R 디바이스에는 데이터 디스크 1개와 부팅 디스크 1개가 있습니다(운영 체제 스토리지로 사용됨). 다음 표에는 디바이스의 스토리지 용량에 대한 세부 정보가 나와 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    SSD(반도체 드라이브)의 수     |    1TB 디스크 2개 <br> 데이터 디스크 1개, 부팅 디스크 1개                  |
|    단일 SSD 용량                     |    1TB               |
|    총 용량(데이터만)              |    1TB              |
|    총 사용 가능한 용량 *                  |    ~ 750GB        |

**일부 공간은 내부용으로 예약되어 있습니다.*

## <a name="network-specifications"></a>네트워크 사양

Azure Stack Edge Mini R 디바이스의 네트워크 사양은 다음과 같습니다.


|사양  |값  |
|---------|---------|
|네트워크 인터페이스    |10Gbe SFP+ 2개 <br> 로컬 UI에 포트 3 및 포트 4로 표시됩니다.           |
|네트워크 인터페이스    |1Gbe RJ45 2개 <br> 로컬 UI에 포트 1 및 포트 2로 표시됩니다.          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge Mini R 디바이스에는 전원을 공급하고 온보드 배터리를 충전하는 외장 85W AC 어댑터가 포함됩니다.

전원 공급 장치 사양은 다음 표와 같습니다.

| 사양           | 값                      |
|-------------------------|----------------------------|
| 최대 출력 전원    | 85W                       |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위 지정: 100-240V AC |



## <a name="included-battery"></a>배터리 포함

Azure Stack Edge Mini R 디바이스에는 전원 공급 장치가 충전하는 온보드 배터리도 포함됩니다.

온보드 배터리와 함께 추가적으로 [Type 2590 배터리](https://www.bren-tronics.com/bt-70791ck.html)를 사용하면 충전 빈도를 줄일 수 있습니다. 이 배터리는 사용 국가에서 적용되는 모든 안전, 교통, 환경 규정을 준수해야 합니다.


| 사양           | 값                      |
|-------------------------|----------------------------|
| 온보드 배터리 용량 | 73WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

디바이스와 러기드 케이스가 장착된 USP의 크기를 밀리미터와 인치로 나타내면 다음과 같습니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    68            |    2.68          |
|    너비          |    208          |      8.19          |
|    길이          |   259           |    10.20          |


### <a name="enclosure-weight"></a>엔클로저 무게

배터리를 포함한 디바이스 무게는 다음 표와 같습니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    디바이스의 총 무게     |    7lbs          |

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양


이 섹션에서는 온도, 습도 및 고도와 같은 엔클로저 환경과 관련된 사양을 나열합니다.


|     사양             |     Description                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     온도 범위          |     0 ~ 43°C(작동)                                              |
|     Vibration                  |     MIL-STD-810 메서드 514.7*<br> 프로시저 I CAT 4, 20                  |
|     충격                      |     MIL-STD-810 메서드 516.7 *<br> 프로시저 IV, 로지스틱                 |
|     고도                   |     작동: 1만 피트<br> 비작동: 4만 피트          |

모든 참조는 MIL-STD-810G Change 1(2014)에 따릅니다.*


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Mini R 배포](azure-stack-edge-placeholder.md)
