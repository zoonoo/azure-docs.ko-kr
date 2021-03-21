---
title: Android 맵에 컨트롤 추가 | Microsoft Azure 맵
description: Microsoft Azure 지도 Android SDK에서 지도에 확대/축소 컨트롤, 피치 컨트롤, 회전 컨트롤 및 스타일 선택기를 추가 하는 방법입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100222"
---
# <a name="add-controls-to-a-map-android-sdk"></a>지도에 컨트롤 추가 (Android SDK)

이 문서에서는 맵에 UI 컨트롤을 추가 하는 방법을 보여 줍니다.

## <a name="add-zoom-control"></a>확대/축소 컨트롤 추가

확대/축소 컨트롤은 지도를 확대/축소 하는 단추를 추가 합니다. 다음 코드 샘플에서는 클래스의 인스턴스를 만들어 `ZoomControl` 맵에 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

아래 스크린샷은 지도에 로드 된 확대/축소 컨트롤입니다.

![지도에 추가 된 확대/축소 컨트롤](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>피치 컨트롤 추가

피치 컨트롤은 피치를 기준으로 tilting 하는 단추를 추가 합니다. 다음 코드 샘플에서는 클래스의 인스턴스를 만들어 `PitchControl` 맵에 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

아래 스크린샷은 지도에 로드 된 피치 컨트롤입니다.

![지도에 추가 된 피치 컨트롤](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>나침반 컨트롤 추가

나침반 컨트롤은 지도를 회전 하는 단추를 추가 합니다. 다음 코드 샘플에서는 클래스의 인스턴스를 만들어 `CompassControl` 맵에 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

아래 스크린샷에는 맵에 로드 된 나침반 컨트롤이 있습니다.

![지도에 나침반 컨트롤이 추가 됨](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>트래픽 제어 추가

트래픽 컨트롤은 지도의 트래픽 데이터를 표시 하거나 숨기는 단추를 추가 합니다. 다음 코드 샘플에서는 클래스의 인스턴스를 만들어 `TrafficControl` 맵에 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

아래 스크린샷에서는 맵에 로드 된 트래픽 컨트롤이 있습니다.

![지도에 추가 된 트래픽 컨트롤](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>모든 컨트롤이 있는 지도

여러 컨트롤을 배열에 배치 하 고 한 번에 모두 맵에 추가 하 여 개발을 간소화할 수 있습니다. 다음은이 방법을 사용 하 여 맵에 표준 탐색 컨트롤을 추가 합니다.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

아래 스크린샷에서는 맵에 로드 된 모든 컨트롤을 보여 줍니다. 맵에 추가 되는 순서는 표시 되는 순서입니다.

![맵에 추가 된 모든 컨트롤](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
