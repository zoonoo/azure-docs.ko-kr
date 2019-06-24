---
title: Azure Maps의 지도 스타일 기능 | Microsoft Docs
description: Azure Maps 알아봅니다 Android SDK에 대 한 스타일 관련 기능입니다.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870974"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 지도 스타일 설정

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도 스타일을 설정 하려면 두 가지 방법으로 보여 줍니다. Azure Maps에 6 개의 다른 맵 스타일에서 선택할 수 있습니다. 지원 되는 지도 스타일에 대 한 자세한 내용은 참조 하세요. [지도 스타일을 지원 되는 Azure Maps에서](./supported-map-styles.md)합니다.


## <a name="prerequisites"></a>필수 조건

이 문서의 프로세스를 완료 하려면 설치 해야 [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 지도 로드 합니다.


## <a name="set-map-style-in-the-layout"></a>레이아웃에서 지도 스타일 설정

활동 클래스에 대 한 레이아웃 파일에서 지도 스타일을 설정할 수 있습니다. 편집 **리소스 > 레이아웃 > activity_main.xml**, 다음과 같이 표시 됩니다.

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

합니다 `mapcontrol_style` 지도 스타일을 설정 하는 위의 특성 **grayscale_dark**합니다. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Activity 클래스에서 지도 스타일 설정

Activity 클래스에서 지도 스타일을 설정할 수 있습니다. 다음 코드 조각을 복사 합니다 **onCreate()** 메서드의 프로그램 `MainActivity.java` 클래스입니다. 지도 스타일 설정 합니다 **satellite_road_labels**합니다.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>