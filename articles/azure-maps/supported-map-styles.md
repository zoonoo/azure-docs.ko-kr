---
title: Azure Maps에서 지원되는 지도 스타일 | Microsoft Docs
description: Azure Maps에서 지원되는 지도 스타일
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 33b0f5df57623f0b4433a4a09c7cd15688783485
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191586"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps에서 지원되는 지도 스타일
Azure Maps는 서로 다른 네 가지 기본 제공 지도 스타일을 지원합니다. 이러한 스타일은 설명과 함께 아래에 나와 있습니다.

## <a name="road"></a>도로
**도로** 지도는 도로, 자연적 특징 및 인공적 특징을 해당 특징에 대한 레이블과 함께 표시하는 표준 지도입니다.

![도로](./media/supported-map-styles/road.png)

**적용 가능한 API:**
* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS 지도 컨트롤

## <a name="satellite"></a>위성 
**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**
* [위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS 지도 컨트롤

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**적용 가능한 API:**
* JS 지도 컨트롤

## <a name="grayscaledark"></a>Grayscale_Dark
**짙은 회색조**는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**적용 가능한 API:**
* JS 지도 컨트롤