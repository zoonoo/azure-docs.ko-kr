---
title: 사용자 지정 장치 모델 구성 - Azure | Microsoft Docs
description: 이 문서에서는 장치 시뮬레이션 솔루션 가속기에서 사용자 지정 장치 모델을 구성하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967494"
---
# <a name="device-models"></a>장치 모델

시뮬레이션을 구성할 때 미리 정의된 센서 집합과 함께 기존 장치 모델을 사용하도록 선택하거나, 선택한 시뮬레이트된 센서로 사용자 지정 장치 모델을 만들 수 있습니다. 사용자 지정 센서를 사용하면 실제 장치를 좀 더 유사하게 모델링할 수 있습니다.

## <a name="pre-configured-device-models"></a>미리 구성된 장치 모델

장치 시뮬레이션 솔루션 가속기는 냉각기, 엘리베이터 및 트럭의 세 가지 미리 구성된 장치 모델을 제공합니다.

미리 구성된 장치 모델에는 JavaScript 파일에 정의된 고급 동작의 여러 센서가 있습니다. 웹 UI에서는 이러한 동작을 구성할 수 없습니다.

다음 표에서는 미리 구성된 각 장치 모델에 대한 구성을 보여 줍니다.

| 장치 모델  | 센서           | 단위  |
| ------------- | ---------------- | ----- |
| 냉각기       | humidity         | %     |
|               | pressure         | psig  |
|               | 온도      | F     |
| 엘리베이터      | Floor            |       |
|               | Vibration        | MM    |
|               | 온도      | F     |
| 트럭         | 위도         |       |
|               | 경도        |       |
|               | speed            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>사용자 지정 장치 모델

사용자 지정 장치 모델을 사용하여 장치를 보다 가깝게 모델링하는 센서를 모델링할 수 있습니다. 사용자 지정 장치에는 최대 10개의 사용자 지정 센서가 있을 수 있습니다.

사용자 지정 장치 모델 종류를 선택하는 경우 **+ 센서 추가**를 클릭하여 새 센서를 추가합니다.

[![센서 구성](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

사용자 지정 센서에는 다음과 같은 속성이 있습니다.

| 필드     | 설명 |
| --------- | ----------- |
| 센서 이름 | 센서의 이름(예: **temperature** 또는 **speed**)입니다.  |
| 동작  | 동작을 사용하여 원격 분석 데이터를 메시지마다 다르게 하여, 실제 데이터를 시뮬레이트할 수 있습니다. **증분**은 전송된 각 메시지의 값을 최소값부터 시작해서 1씩 늘립니다. 최대값이 충족되면 최소값에서 다시 시작됩니다. **감소**는 **증분**과 같은 방식으로 동작하지만 카운트다운합니다. **무작위** 동작은 최소값과 최대값 사이에 임의 값을 생성합니다. |
| 최소값 | 허용되는 범위의 가장 낮은 숫자입니다. |
| 최대값 | 허용되는 범위의 가장 큰 숫자입니다. |
| 단위      | 센서의 측정 단위(예: °F 또는 MPH)입니다. |

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 시뮬레이션에 대한 사용자 지정 장치 모델을 구성하는 방법을 알아보았습니다. 다음으로, 기타 [IoT 솔루션 가속기](about-iot-accelerators.md) 일부를 알아볼 수 있습니다.