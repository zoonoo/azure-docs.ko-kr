---
title: Azure Maps에서 지원되는 지도 스타일 | Microsoft Docs
description: Azure Maps에서 지원되는 지도 스타일
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2f426ab85d9ab15dd080a487337b8d48358ed040
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888463"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps에서 지원되는 지도 스타일
Azure Maps는 아래 설명된 대로 몇 가지 다른 기본 제공 지도 스타일을 지원합니다.

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

## <a name="satelliteroadlabels"></a>satellite_road_labels
이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**적용 가능한 API:**
* JS 지도 컨트롤

## <a name="grayscaledark"></a>grayscale_dark
**짙은 회색조**는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**적용 가능한 API:**
* JS 지도 컨트롤 

## <a name="night"></a>야간
**야간**은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간](./media/supported-map-styles/night.PNG)

**적용 가능한 API:**
* JS 지도 컨트롤 