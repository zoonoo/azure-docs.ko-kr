---
title: Azure 지도 Android SDK에 기능 정보 표시 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도 Android SDK를 사용하여 맵에 기능 정보를 표시하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911685"
---
# <a name="display-feature-information"></a>기능 정보 표시

공간 데이터는 종종 점, 선 및 다각형을 사용하여 표현됩니다. 이 데이터에는 종종 연결된 메타데이터 정보가 있습니다. 예를 들어, 지점은 해당 레스토랑에 대한 상점및 메타데이터의 위치를 나타낼 수 있으며 해당 레스토랑의 이름, 주소 및 음식 유형일 수 있습니다. 이 메타데이터는 `JsonObject`을 사용하여 이러한 기능의 속성으로 추가할 수 있습니다. 다음 코드는 "Hello World!" `title` 값이 있는 속성으로 간단한 포인트 기능을 만듭니다.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

사용자가 맵의 피처와 상호 작용할 때 이벤트를 사용하여 이러한 작업에 반응할 수 있습니다. 일반적인 시나리오는 사용자가 상호 작용하는 피처의 메타데이터 속성으로 만든 메시지를 표시하는 것입니다. 이 `OnFeatureClick` 이벤트는 사용자가 맵에서 피처를 탭했을 때 감지하는 데 사용되는 주요 이벤트입니다. 이벤트도 있습니다. `OnLongFeatureClick` 맵에 `OnFeatureClick` 이벤트를 추가할 때 레이어의 ID를 전달하여 단일 레이어로 제한할 수 있습니다. 레이어 ID가 전달되지 않으면 맵에 있는 레이어에 관계없이 맵의 피처를 탭하면 이 이벤트가 발생합니다. 다음 코드는 맵에서 포인트 데이터를 렌더링하는 기호 레이어를 `OnFeatureClick` 생성한 다음 이벤트를 추가하고 이 심볼 레이어로 제한합니다.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>알림 메시지 표시

알림 메시지는 사용자에게 정보를 표시하는 가장 쉬운 방법 중 하나이며 모든 버전의 Android에서 사용할 수 있습니다. 모든 유형의 사용자 입력을 지원하지 않으며 짧은 기간 동안만 표시됩니다. 사용자에게 탭한 내용에 대해 신속하게 알리려면 알림 메시지가 좋은 옵션일 수 있습니다. 다음 코드는 이벤트와 함께 알림 메시지를 `OnFeatureClick` 사용할 수 있는 방법을 보여 주며,

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![탭되는 피처의 애니메이션과 알림 메시지가 표시됩니다.](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

알림 메시지 외에도 다음과 같은 기능의 메타데이터 속성을 표시하는 다른 여러 가지 방법이 있습니다.

- [Snakbar 위젯](https://developer.android.com/training/snackbar/showing.html) - 스낵바는 작업에 대한 가벼운 피드백을 제공합니다. 모바일의 화면 하단에 간단한 메시지가 표시되고 더 큰 장치에서 왼쪽 아래에 표시됩니다. 스낵바는 화면의 다른 모든 요소 위에 나타나며 한 번에 하나만 표시할 수 있습니다.
- [대화 상자](https://developer.android.com/guide/topics/ui/dialogs) - 대화 상자는 사용자가 결정을 내리거나 추가 정보를 입력하라는 메시지를 표시하는 작은 창입니다. 대화 상자는 화면을 채우지 않으며 일반적으로 사용자가 작업을 계속하기 전에 작업을 수행해야 하는 모달 이벤트에 사용됩니다.
- 현재 활동에 [조각을](https://developer.android.com/guide/components/fragments) 추가합니다.
- 다른 활동 또는 보기로 이동합니다.

## <a name="next-steps"></a>다음 단계

맵에 더 많은 데이터를 추가하려면 다음 을 수행합니다.

> [!div class="nextstepaction"]
> [기호 레이어 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [안드로이드 맵에 셰이프 추가](how-to-add-shapes-to-android-map.md)
