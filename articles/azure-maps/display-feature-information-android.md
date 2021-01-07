---
title: Android maps에 기능 정보 표시 | Microsoft Azure 맵
description: 사용자가 지도 기능과 상호 작용할 때 정보를 표시 하는 방법을 알아봅니다. Azure Maps Android SDK를 사용 하 여 알림 메시지와 다른 유형의 메시지를 표시 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679996"
---
# <a name="display-feature-information"></a>기능 정보 표시

공간 데이터는 종종 점으로, 선 및 다각형을 사용 하 여 표현 됩니다. 이 데이터는 연결 된 메타 데이터 정보를 포함 하는 경우가 많습니다. 예를 들어 지점은 식당의 위치를 나타낼 수 있으며 해당 식당에 대 한 메타 데이터는 해당 이름, 주소 및 사용 되는 음식 형식이 될 수 있습니다. 이 메타 데이터는 GeoJSON의 속성으로 추가할 수 있습니다 `Feature` . 다음 코드에서는 `title` 값이 "Hello World!" 인 속성을 사용 하 여 간단한 point 기능을 만듭니다.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

지도에 데이터를 만들고 추가 하는 방법은 [데이터 원본 만들기](create-data-source-android-sdk.md) 설명서를 참조 하세요.

사용자가 맵의 기능과 상호 작용 하는 경우 이벤트를 사용 하 여 해당 작업에 대응할 수 있습니다. 일반적인 시나리오는 사용자가 상호 작용 한 기능의 메타 데이터 속성에 대 한 메시지를 표시 하는 것입니다. `OnFeatureClick`이벤트는 사용자가 지도의 기능을 탭 할 때를 검색 하는 데 사용 되는 기본 이벤트입니다. 이벤트도 있습니다 `OnLongFeatureClick` . 지도에 이벤트를 추가 하는 경우이 `OnFeatureClick` 를 제한 하려면 계층의 ID를 전달 하 여 단일 계층으로 제한 될 수 있습니다. 계층 ID가 전달 되지 않은 경우에는 해당 계층에 관계 없이 맵의 모든 기능을 눌러이 이벤트를 발생 시킬 수 있습니다. 다음 코드에서는 지도에서 point 데이터를 렌더링할 기호 계층을 만든 다음 이벤트를 추가 하 `OnFeatureClick` 고이 기호 계층으로 제한 합니다.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>알림 메시지 표시

알림 메시지는 사용자에 게 정보를 표시 하는 가장 쉬운 방법 중 하나 이며 모든 버전의 Android에서 사용할 수 있습니다. 모든 유형의 사용자 입력을 지원 하지 않으며 짧은 시간 동안만 표시 됩니다. 사용자가 누른 항목에 대 한 정보를 신속 하 게 알리려면 알림 메시지가 표시 될 수 있습니다. 다음 코드에서는 알림 메시지를 이벤트와 함께 사용할 수 있는 방법을 보여 줍니다 `OnFeatureClick` .

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![탭 하는 기능 및 표시 되는 알림 메시지의 애니메이션](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

알림 메시지 외에도 다음과 같은 다양 한 방법으로 기능의 메타 데이터 속성을 표시할 수 있습니다.

- [Snackbar 위젯](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` 작업에 대 한 간단한 피드백을 제공 합니다. 모바일에서 화면 아래쪽에 짧은 메시지를 표시 하 고 더 큰 장치에서 왼쪽 아래에 표시 합니다. `Snackbars` 화면의 다른 모든 요소 위에 표시 되며 한 번에 하나만 표시할 수 있습니다.
- [대화 상자-대화](https://developer.android.com/guide/topics/ui/dialogs) 상자는 사용자에 게 결정을 하거나 추가 정보를 입력 하 라는 메시지를 표시 하는 작은 창입니다. 대화 상자는 화면을 채우지 않으며 작업을 계속 하기 전에 사용자가 작업을 수행 해야 하는 모달 이벤트에 일반적으로 사용 됩니다.
- 현재 활동에 [조각을](https://developer.android.com/guide/components/fragments) 추가 합니다.
- 다른 활동 또는 뷰로 이동 합니다.

## <a name="next-steps"></a>다음 단계

지도에 데이터를 더 추가 하려면:

> [!div class="nextstepaction"]
> [지도 이벤트에 반응](android-map-events.md)

> [!div class="nextstepaction"]
> [데이터 원본 만들기](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
