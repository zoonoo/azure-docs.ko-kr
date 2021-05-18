---
title: 라우팅을 위한 차량 소비 모델 | Microsoft Azure Maps
description: Azure Maps에서 지원하는 연소 및 전기 소비 모델에 대해 알아봅니다. 각 모델에서 사용하는 매개 변수를 확인하고 매개 변수 제약 조건을 봅니다.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013142"
---
# <a name="consumption-model"></a>소비 모델

라우팅 서비스는 차량 관련 소비 모델에 대해 자세히 설명할 수 있는 매개 변수 집합을 제공합니다.
**vehicleEngineType** 의 값에 따라 _Combustion_ 및 _Electric_ 의 두 가지 주요 소비 모델이 지원됩니다. 같은 요청에서 서로 다른 모델에 속해 있는 매개 변수를 지정하는 것은 올바르지 않습니다. 또한 소비 모델 매개 변수는 **travelMode** 값인 _bicycle_ 및 _pedestrian_ 과 함께 사용할 수 없습니다.

## <a name="parameter-constraints-for-consumption-model"></a>소비 모델에 대한 매개 변수 제약 조건

두 소비 모델 모두에서 매개 변수를 지정할 때는 몇 가지 종속성이 있습니다. 즉, 일부 매개 변수를 명시적으로 지정하려면 다른 매개 변수를 지정해야 할 수 있습니다. 주의해야 할 종속성은 다음과 같습니다.

* 모든 매개 변수에서 **constantSpeedConsumption** 은 사용자가 지정해야 합니다. **constantSpeedConsumption** 이 지정되지 않은 경우에 다른 소비 모델 매개 변수를 지정하면 오류가 발생합니다. **vehicleWeight** 매개 변수는 이 요구 사항에 대해 예외입니다.
* **accelerationEfficiency** 및 **decelerationEfficiency** 는 항상 쌍으로 지정해야 합니다. 즉, 둘 다 지정하거나 모두 지정하지 않아야 합니다.
* **accelerationEfficiency** 및 **decelerationEfficiency** 가 지정된 경우 해당 값은 1 이하여야 합니다(영구적 동작을 방지하기 위해).
* **uphillEfficiency** 및 **downhillEfficiency** 는 항상 쌍으로 지정해야 합니다. 즉, 둘 다 지정하거나 모두 지정하지 않아야 합니다.
* **uphillEfficiency** 및 **downhillEfficiency** 가 지정된 경우 해당 값은 1 이하여야 합니다(영구적 동작을 방지하기 위해).
* 사용자가 \*__Efficiency__ 매개 변수를 지정한 경우 **vehicleWeight** 도 지정해야 합니다. **vehicleEngineType** 이 _combustion_ 인 경우 **fuelEnergyDensityInMJoulesPerLiter** 도 지정해야 합니다.
* **maxChargeInkWh** and **currentChargeInkWh** 는 항상 쌍으로 지정해야 합니다. 즉, 둘 다 지정하거나 모두 지정하지 않아야 합니다.

> [!NOTE]
> **constantSpeedConsumption** 이 지정된 경우에만 경사 및 차량 가속과 같은 다른 소비 측면이 소비 계산에 대해 고려되지 않습니다.

## <a name="combustion-consumption-model"></a>연소식 소비 모델

연소식 소비 모델은 **vehicleEngineType** 이 _combustion_ 으로 설정된 경우에 사용됩니다.
이 모델에 속해 있는 매개 변수 목록은 다음과 같습니다. 자세한 설명은 매개 변수 섹션을 참조하세요.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>일렉트릭 소비 모델

일렉트릭 소비 모델은 **vehicleEngineType** 이 _electric_ 으로 설정된 경우에 사용됩니다.
이 모델에 속해 있는 매개 변수 목록은 다음과 같습니다. 자세한 설명은 매개 변수 섹션을 참조하세요.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>소비 매개 변수의 적절한 값

특정 소비 매개 변수 집합은 모든 명시적 요구 사항을 충족하더라도 거부될 수 있습니다. 이는 특정 매개 변수의 값 또는 여러 매개 변수의 값 조합이 불합리한 크기의 소비 값을 초래하는 것으로 간주되는 경우 발생합니다. 발생하면 소비 매개 변수의 모든 적절한 값에 맞도록 적절한 주의가 요구되므로 입력 오류가 표시될 가능성이 있습니다. 특정 소비 매개 변수 집합이 거부되는 경우 함께 제공되는 오류 메시지에 이유에 관한 텍스트 설명이 포함됩니다.
자세한 매개 변수 설명에 두 모델 모두에 적절한 값의 예제가 포함되어 있습니다.
