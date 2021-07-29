---
title: 지원되는 기본 제공 Azure Maps 지도 스타일
description: 도로, blank_accessible, 위성, satellite_road_labels, road_shaded_relief, 야간 등 Azure Maps에서 지원하는 기본 제공 지도 스타일에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: aae17a1071949ea935d7094539d31e85a354da08
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108326600"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps에서 지원하는 기본 제공 지도 스타일

Azure Maps는 아래 설명된 대로 여러 가지 기본 제공 지도 스타일을 지원합니다.

>[!important]
>이 섹션의 절차에는 Gen 1 또는 Gen 2 가격 책정 계층의 Azure Maps 계정이 필요합니다. 가격 책정 계층에 대한 자세한 내용은 [Azure Maps에서 적절한 가격 책정 계층 선택](choose-pricing-tier.md)을 참조하세요.

## <a name="road"></a>도로

**도로** 지도는 도로를 표시하는 표준 지도입니다. 또한 자연 및 인공 기능과 해당 기능의 레이블을 표시합니다.

![도로 지도 스타일](./media/supported-map-styles/road.png)

**적용 가능한 API:**

* [지도 이미지](/rest/api/maps/render/getmapimage)
* [지도 타일](/rest/api/maps/render/getmaptile)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="blank-and-blank_accessible"></a>blank 및 blank_accessible

**blank** 지도와 **blank_accessible** 지도 스타일은 데이터 시각화를 위한 빈 캔버스를 제공합니다. **blank_accessible** 스타일은 기본 지도가 표시되지 않더라도 지도의 위치 세부 정보와 함께 화면 읽기 프로그램 업데이트를 계속 제공합니다.

> [!Note]
> 웹 SDK에서 지도 DIV 요소의 CSS `background-color` 스타일을 설정하여 지도의 배경색을 변경할 수 있습니다.

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤

## <a name="satellite"></a>satellite

**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성 타일 지도 스타일](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**

* [위성 타일](/rest/api/maps/render/getmapimagerytilepreview)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="satellite_road_labels"></a>satellite_road_labels

이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels 지도 스타일](./media/supported-map-styles/satellite-road-labels.png)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="grayscale_dark"></a>grayscale_dark

**짙은 회색조** 는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale 지도 스타일](./media/supported-map-styles/grayscale-dark.png)

**적용 가능한 API:**

* [지도 이미지](/rest/api/maps/render/getmapimage)
* [지도 타일](/rest/api/maps/render/getmaptile)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="grayscale_light"></a>grayscale_light

**밝은 회색조** 는 도로 지도 스타일의 밝은 버전입니다.

![밝은 회색조 지도 스타일](./media/supported-map-styles/grayscale-light.png)

**적용 가능한 API:**
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="night"></a>야간

**야간** 은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간 지도 스타일](./media/supported-map-styles/night.png)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="road_shaded_relief"></a>road_shaded_relief

**도로 음영 입체** 는 지구의 등고선으로 채워진 Azure Maps 주요 스타일입니다.

![음영 입체 지도 스타일](./media/supported-map-styles/shaded-relief.png)

**적용 가능한 API:**

* [지도 타일](/rest/api/maps/render/getmaptile)
* 웹 SDK 지도 컨트롤
* Android 지도 컨트롤
* Power BI 시각적 개체

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** 는 다른 스타일보다 더 고대비의 어두운 지도 스타일입니다.

![고대비 어두운 지도 스타일](./media/supported-map-styles/high-contrast-dark.png)

**적용 가능한 API:**

* 웹 SDK 지도 컨트롤
* Power BI 시각적 개체

## <a name="next-steps"></a>다음 단계

Azure Maps에서 지도 스타일을 설정하는 방법에 대해 알아봅니다:

[지도 스타일 선택](./choose-map-style.md)