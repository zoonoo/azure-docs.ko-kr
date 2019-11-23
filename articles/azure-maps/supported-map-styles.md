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
ms.openlocfilehash: 5368aec04eb0a57654adf5b0d5914282a979dcfd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325408"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps에서 지원되는 지도 스타일
Azure Maps는 아래 설명된 대로 여러 가지 기본 제공 지도 스타일을 지원합니다.

## <a name="road"></a>도로
**도로** 지도는 도로, 자연적 특징 및 인공적 특징을 해당 특징에 대한 레이블과 함께 표시하는 표준 지도입니다.

![도로](./media/supported-map-styles/road.png)

**적용 가능한 API:**
* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control

## <a name="blank-and-blank_accessible"></a>blank and blank_accessible

The **blank** and **blank_accessible** map styles provide a blank canvas on which to visualize data on. The **blank_accessible** style will continue to provide screen reader updates with location details of where the map is located, even though the base map is not displayed.

> [!Note]
> In the web SDK you can change the background color of the map by setting the CSS `background-color` style of map DIV element.

**적용 가능한 API:**
* Web SDK map control

## <a name="satellite"></a>위성 
**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**
* [위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK map control
* Android map control

## <a name="satellite_road_labels"></a>satellite_road_labels
이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**적용 가능한 API:**
* Web SDK map control
* Android map control

## <a name="grayscale_dark"></a>grayscale_dark
**짙은 회색조**는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**적용 가능한 API:**
* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control 
* Android map control


## <a name="grayscale_light"></a>grayscale_light
**grayscale light** is a light version of the road map style.

![grayscale light](./media/supported-map-styles/grayscale_light.png)

**적용 가능한 API:**
* Web SDK map control
* Android map control


## <a name="night"></a>야간
**야간**은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간](./media/supported-map-styles/night.png)

**적용 가능한 API:**
* Web SDK map control
* Android map control

## <a name="road_shaded_relief"></a>road_shaded_relief
**도로 음영 입체**는 지구의 등고선으로 채워진 Azure Maps 주요 스타일입니다.

![음영 입체](./media/supported-map-styles/shaded-relief.png)

**적용 가능한 API:**
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control


## <a name="next-steps"></a>다음 단계

Learn about how to set a map style in Azure Maps:

> [!div class="nextstepaction"]
> [맵 스타일 선택](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
