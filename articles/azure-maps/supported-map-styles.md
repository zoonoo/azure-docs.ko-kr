---
title: Azure Maps에서 지원되는 지도 스타일 | Microsoft Docs
description: Azure Maps에서 지원되는 지도 스타일
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 457154a797a4b6d9853b1effe0d8121053653a99
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174777"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps에서 지원되는 지도 스타일
Azure Maps는 아래 설명된 대로 여러 가지 기본 제공 지도 스타일을 지원합니다.

## <a name="road"></a>도로
**도로** 지도는 도로, 자연적 특징 및 인공적 특징을 해당 특징에 대한 레이블과 함께 표시하는 표준 지도입니다.

![도로](./media/supported-map-styles/road.png)

**적용 가능한 API:**
* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="blank-and-blank_accessible"></a>blank 및 blank_accessible

**Blank** 및 **blank_accessible** 지도 스타일은 데이터를 시각화할 빈 캔버스를 제공 합니다. **Blank_accessible** 스타일은 지도를 표시 하지 않는 경우에도 지도가 있는 위치 세부 정보를 사용 하 여 화면 판독기 업데이트를 계속 제공 합니다.

> [!Note]
> 웹 SDK에서 지도 DIV 요소의 CSS `background-color` 스타일을 설정 하 여 지도의 배경색을 변경할 수 있습니다.

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤

## <a name="satellite"></a>위성 
**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**
* [위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="satellite_road_labels"></a>satellite_road_labels
이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="grayscale_dark"></a>grayscale_dark
**짙은 회색조**는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤 
* Android 맵 컨트롤


## <a name="grayscale_light"></a>grayscale_light
**회색조 라이트** 는도로 지도 스타일의 조명 버전입니다.

![회색조 밝게](./media/supported-map-styles/grayscale_light.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤


## <a name="night"></a>야간
**야간**은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간](./media/supported-map-styles/night.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="road_shaded_relief"></a>road_shaded_relief
**도로 음영 입체**는 지구의 등고선으로 채워진 Azure Maps 주요 스타일입니다.

![음영 입체](./media/supported-map-styles/shaded-relief.png)

**적용 가능한 API:**
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤


## <a name="next-steps"></a>다음 단계

Azure Maps에서 지도 스타일을 설정 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [지도 스타일 선택](https://docs.microsoft.com/en-us/azure/azure-maps/choose-map-style)