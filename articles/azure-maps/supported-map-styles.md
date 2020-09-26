---
title: 지원 되는 기본 제공 Azure Maps 지도 스타일
description: 이동, blank_accessible, 위성, satellite_road_labels, road_shaded_relief, 야간 등 Azure Maps 지원 되는 기본 제공 지도 스타일에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4a50a9d6d4a485f7d8e63adb9ae5032f49edc261
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310359"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>지원 되는 기본 제공 지도 스타일 Azure Maps

Azure Maps는 아래 설명된 대로 여러 가지 기본 제공 지도 스타일을 지원합니다.

## <a name="road"></a>도로

**도로 지도는도로** 를 표시 하는 표준 지도입니다. 또한 자연 및 인공 특징과 이러한 기능에 대 한 레이블을 표시 합니다.

![도로 지도 스타일](./media/supported-map-styles/road.png)

**적용 가능한 API:**

* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="blank-and-blank_accessible"></a>blank 및 blank_accessible

**빈** 지도와 **blank_accessible** 지도 스타일은 데이터를 시각화 하기 위한 빈 캔버스를 제공 합니다. 기본 지도가 표시 되지 않더라도 **blank_accessible** 스타일은 지도 위치 세부 정보를 사용 하 여 화면 판독기 업데이트를 계속 제공 합니다.

> [!Note]
> 웹 SDK에서 지도 DIV 요소의 CSS 스타일을 설정 하 여 지도의 배경색을 변경할 수 있습니다 `background-color` .

**적용 가능한 API:**

* 웹 SDK 맵 컨트롤

## <a name="satellite"></a>satellite

**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성 타일 지도 스타일](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**

* [위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="satellite_road_labels"></a>satellite_road_labels

이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels 지도 스타일](./media/supported-map-styles/satellite-road-labels.png)

**적용 가능한 API:**

* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="grayscale_dark"></a>grayscale_dark

**회색조 진한** 은도로 지도 스타일의 어두운 버전입니다.

![gray_scale 지도 스타일](./media/supported-map-styles/grayscale-dark.png)

**적용 가능한 API:**

* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="grayscale_light"></a>grayscale_light

**회색조 라이트** 는도로 지도 스타일의 조명 버전입니다.

![회색조 연한 지도 스타일](./media/supported-map-styles/grayscale-light.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="night"></a>야간

**야간**은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간 지도 스타일](./media/supported-map-styles/night.png)

**적용 가능한 API:**

* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="road_shaded_relief"></a>road_shaded_relief

**도로 음영 입체**는 지구의 등고선으로 채워진 Azure Maps 주요 스타일입니다.

![음영 된 릴리프 맵 스타일](./media/supported-map-styles/shaded-relief.png)

**적용 가능한 API:**

* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤
* Power BI 시각적 개체

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** 는 다른 스타일 보다 대비가 높은 짙은 지도 스타일입니다.

![고대비 어둡게 지도 스타일](./media/supported-map-styles/high-contrast-dark.png)

**적용 가능한 API:**

* 웹 SDK 맵 컨트롤
* Power BI 시각적 개체

## <a name="next-steps"></a>다음 단계

Azure Maps에서 지도 스타일을 설정 하는 방법에 대해 알아봅니다.

[지도 스타일 선택](https://docs.microsoft.com/azure/azure-maps/choose-map-style)