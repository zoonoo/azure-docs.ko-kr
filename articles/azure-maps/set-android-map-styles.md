---
title: Azure 지도 Android SDK를 사용하여 맵 스타일 설정 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Android SDK에 대한 Microsoft Azure Maps 스타일 관련 기능에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334363"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure지도 안드로이드 SDK를 사용하여지도 스타일을 설정

이 문서에서는 Azure 지도 Android SDK를 사용하여 맵 스타일을 설정하는 두 가지 방법을 보여 주었습니다. Azure Maps에는 선택할 수 있는 6가지 맵 스타일이 있습니다. 지원되는 맵 스타일에 대한 자세한 내용은 [Azure Maps에서 지원되는 맵 스타일을](./supported-map-styles.md)참조하십시오.


## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 프로세스를 완료하려면 맵을 로드하려면 [Azure Maps Android SDK를](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 설치해야 합니다.


## <a name="set-map-style-in-the-layout"></a>레이아웃에서 맵 스타일 설정

활동 클래스의 레이아웃 파일에서 맵 스타일을 설정할 수 있습니다. **activity_main.xml에 > 레이아웃을 > reitres를**편집하므로 아래와 같습니다.

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

위의 `mapcontrol_style` 속성은 맵 스타일을 **grayscale_dark.** 

<center>

![스타일 grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>활동 클래스에서 맵 스타일 설정

활동 클래스에서 맵 스타일을 설정할 수 있습니다. 다음 코드 조각을 **클래스의 onCreate()** 메서드에 `MainActivity.java` 복사합니다. 이 코드는 맵 스타일을 **satellite_road_labels**설정합니다.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![스타일 위성 도로 라벨](./media/set-android-map-styles/satellite-road-labels.png)</center>