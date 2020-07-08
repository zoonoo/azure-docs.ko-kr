---
title: Microsoft Azure Stack Edge 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Stack Edge에 대한 기술 사양 및 규정 준수에 대해 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 5a4ac2342ca36d83d1a579851c090c4713814c8d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652043"
---
# <a name="azure-stack-edge-technical-specifications"></a>Azure Stack Edge 기술 사양

Microsoft Azure Stack Edge 디바이스의 하드웨어 구성 요소는 이 문서에 설명된 기술 사양 및 규제 표준을 준수합니다. 기술 사양은 PSU(전원 공급 장치), 스토리지 용량, 엔클로저 및 환경 표준을 설명합니다.

## <a name="compute-memory-specifications"></a>컴퓨팅, 메모리 사양

Azure Stack Edge 디바이스에는 컴퓨팅 및 메모리에 대한 다음과 같은 사양이 있습니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| CPU    | 10코어 CPU 2개                     |
| 메모리              | 128GB RAM                  |

## <a name="fpga-specifications"></a>FPGA 사양

ML(기계 학습) 시나리오를 가능하게 하는 FPGA(Field Programmable Gate Array)는 모든 Azure Stack Edge 디바이스에 포함되어 있습니다.

| 사양           | 값                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 사용 가능한 DNN(심층 신경망) 모델은 [클라우드 FPGA 인스턴스에서 지원하는](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure) 모델과 동일합니다.|

## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge 디바이스에는 고성능 팬이 달린 2대의 100-240V PSU(전원 공급 장치)가 있습니다. 두 대의 PSU는 중복 전원 구성을 제공합니다. PSU가 실패하면, 디바이스는 실패한 모듈이 교체될 때까지 다른 PSU에서 정상적으로 작동합니다. 다음 표에는 PSU의 기술 사양이 나열되어 있습니다.

| 사양           | 750W PSU                  |
|-------------------------|----------------------------|
| 최대 출력 전원    | 750W                     |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위 지정: 100-240V AC |
| 핫 플러그형           | 예                        |

### <a name="azure-stack-edge-power-cord-specifications-by-region"></a>Azure Stack 지역별 전원 코드 사양

Azure Stack Edge 디바이스에는 Azure 지역에 따라 달라지는 전원 코드가 필요합니다.
지원되는 모든 전원 코드의 기술 사양에 대해서는 [Azure Stack Edge 지역별 전원 코드 사양](azure-stack-edge-technical-specifications-power-cords-regional.md)을 참조하세요.

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>네트워크 인터페이스 사양

Azure Stack Edge 디바이스에는 6개의 네트워크 인터페이스인 PORT1-PORT6가 있습니다.

| 사양           | Description                 |
|-------------------------|----------------------------|
|  네트워크 인터페이스    | 1GbE 인터페이스 2개 - 1개는 관리용으로 사용자가 구성할 수 없으며 초기 설정에 사용됩니다. 다른 인터페이스는 사용자가 구성 가능하고 데이터 전송에 사용할 수 있으며, 기본적으로 DHCP입니다. <br>25GbE 인터페이스 2개 - 10GbE 인터페이스로도 작동할 수 있습니다. 사용자는 이러한 데이터 인터페이스를 DHCP(기본값) 또는 정적으로 구성할 수 있습니다. <br> 25GbE 인터페이스 2개 - 사용자는 이러한 데이터 인터페이스를 DHCP(기본값) 또는 정적으로 구성할 수 있습니다.                  |

## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge 디바이스에는 각각 1.6TB의 용량을 포함하는 2.5" NVMe SSD가 9개 있습니다. 이러한 SSD 중 1개는 운영 체제 디스크이고 다른 8개는 데이터 디스크입니다. 디바이스에 사용할 수 있는 총 용량은 약 12.5TB입니다. 다음 표에는 디바이스의 스토리지 용량에 대한 세부 정보가 나와 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    SSD(반도체 드라이브)의 수     |    8                  |
|    단일 SSD 용량                     |    1.6TB             |
|    총 용량                          |    12.8TB            |
|    총 사용 가능한 용량 *                  |    12.5TB 미만            |

**일부 공간은 내부용으로 예약되어 있습니다.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

다음 표에서 인치 및 밀리미터로 엔클로저의 크기를 나타냅니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    44.45            |    1.75"          |
|    너비          |    434.1           |    17.09"          |
|    길이          |    740.4           |    29.15"          |

다음 표에서는 인치 및 밀리미터로 배송 패키지의 크기를 나타냅니다.

|     패키지     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    311.2            |    12.25인치          |
|    너비          |    642.8          |    25.31인치          |
|    길이          |   1,051.1          |    41.38인치          |

### <a name="enclosure-weight"></a>엔클로저 무게

디바이스 패키지의 무게는 61lbs이며 들어 올리려면 두 사람이 필요합니다. 디바이스의 무게는 엔클로저 구성에 따라 달라집니다.

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
|    작동 최대 고도        |    [작동 온도 출력 저하 사양](#operating-temperature-de-rating-specifications)에 따라 결정된 대로 최대 작동 온도 출력이 저하된 3048m(1만 피트)                                                                                |
|    작동 불가능 최대 고도    |    12,000m(39,370피트)                                                                                                                                                                                         |
|    충격, 작동                   |    11밀리초 동안 6G(6개 방향)                                                                                                                                                                         |
|    충격, 작동 불가능               |    2밀리초 동안 71G(6개 방향)                                                                                                                                                                           |
|    진동, 작동               |    0.26G<sub>RMS</sub> 5Hz-350Hz 임의                                                                                                                                                                                     |
|    진동, 작동 불가능           |    15분 동안 1.88G<sub>RMS</sub> 10Hz-500Hz(6개 측면으로 모두 테스트됨)                                                                                                                                                  |
|    방향 및 탑재             |    19" 랙 탑재                                                                                                                                                                                        |
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

- [Azure Stack Edge 배포](azure-stack-edge-deploy-prep.md)
