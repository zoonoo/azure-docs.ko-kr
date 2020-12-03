---
title: Azure Maps를 사용 하 여 지도 스타일 설정 Android SDK
description: 지도의 스타일을 설정 하는 두 가지 방법을 알아봅니다. 레이아웃 파일이 나 활동 클래스에서 Microsoft Azure 맵 Android SDK를 사용 하 여 스타일을 조정 하는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532486"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 지도 스타일 설정

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도 스타일을 설정 하는 방법을 보여 줍니다. Azure Maps에는 6 가지 맵 스타일을 선택할 수 있습니다. 지원 되는 지도 스타일에 대 한 자세한 내용은 [Azure Maps에서 지원 되는 맵 스타일](./supported-map-styles.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Azure Maps Android SDK](./how-to-use-android-map-control-library.md)를 다운로드 하 여 설치 합니다.


## <a name="set-map-style-in-the-layout"></a>레이아웃에서 지도 스타일 설정

활동 클래스의 레이아웃 파일에서 지도 스타일을 설정할 수 있습니다. 다음과 같이 편집 `res > layout > activity_main.xml` 합니다.

```XML
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

`mapcontrol_style`위의 특성은 지도 스타일을 **grayscale_dark** 설정 합니다.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps 스타일을 표시 하는 이미지를 grayscale_dark으로 매핑":::

## <a name="set-map-style-in-the-mainactivity-class"></a>MainActivity 클래스에서 지도 스타일 설정

지도 스타일은 MainActivity 클래스에서 설정할 수도 있습니다. `java > com.example.myapplication > MainActivity.java`파일을 열고 다음 코드 조각을 **onCreate ()** 메서드에 복사 합니다. 이 코드는 지도 스타일을 **satellite_road_labels** 설정 합니다.

>[!WARNING]
>Android Studio 필요한 클래스를 가져올 수 없습니다.  따라서 코드에 확인할 수 없는 참조가 포함 됩니다. 필요한 클래스를 가져오려면 확인 되지 않은 각 참조를 마우스로 가리키고 `Alt + Enter` (Mac에서 옵션 + 반환)를 누릅니다.

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps 스타일을 표시 하는 이미지를 satellite_road_labels으로 매핑":::