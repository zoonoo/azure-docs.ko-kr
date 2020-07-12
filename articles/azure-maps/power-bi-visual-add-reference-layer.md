---
title: Azure Maps Power BI 시각적 개체에 참조 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure 맵 시각적 개체에서 참조 계층을 사용 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261946"
---
# <a name="add-a-reference-layer"></a>참조 계층 추가

참조 계층 기능을 사용 하면 보조 공간 데이터 집합을 시각적 개체에 업로드 하 고 맵에 겹쳐서 추가 컨텍스트를 제공할 수 있습니다. 이 데이터 집합은 Power BI에서 호스팅되며 또는 파일 확장명을 가진 [GeoJSON 파일](https://wikipedia.org/wiki/GeoJSON) 이어야 합니다 `.json` `.geojson` .

**GeoJSON** 파일을 참조 계층으로 추가 하려면 **형식** 창으로 이동 하 여 **참조 계층** 섹션을 확장 하 고 **+ 로컬 파일 추가** 단추를 누릅니다.

GeoJSON 파일이 참조 계층에 추가 되 면 파일 이름이 **+ 로컬 파일 추가** 단추 대신 **X** 가 표시 됩니다. **X** 단추를 눌러 시각적 개체에서 데이터를 제거 하 고 Power BI GeoJSON 파일을 삭제 합니다.

다음 지도에는 [Colorado에 대 한 2016 인구 조사 tracts](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)(모집단으로 색 지정)가 표시 됩니다.

> [!div class="mx-imgBorder"]
> ![Colorado에 대 한 2016 인구 조사 tracts를 참조 계층으로 모집단으로 색을 표시 하는 지도](media/power-bi-visual/reference-layer-CO-census-tract.png)

다음은 **참조 계층** 섹션에서 사용할 수 있는 **서식** 창의 모든 설정입니다.

| 설정              | 설명   |
|----------------------|---------------|
| 참조 계층 데이터 | 지도 내에서 추가 계층으로 시각적 개체에 업로드할 데이터 GeoJSON 파일입니다. **+ 로컬 파일 추가** 단추는 사용자가 `.json` 또는 파일 확장명이 있는 GeoJSON 파일을 선택 하는 데 사용할 수 있는 파일 대화 상자를 엽니다 `.geojson` . |

> [!NOTE]
> Azure Maps 시각적 개체의이 미리 보기에서 참조 계층은 처음 5000 셰이프 기능만 맵에 로드 합니다. 이 제한은 향후 업데이트에서 증가 됩니다.

## <a name="styling-data-in-a-reference-layer"></a>참조 계층의 데이터 스타일 지정

GeoJSON 파일 내의 각 기능에 속성을 추가 하 여 맵에 스타일을 지정 하는 방법을 사용자 지정할 수 있습니다. 이 기능은 Azure Maps 웹 SDK의 단순 데이터 계층 기능을 사용 합니다. 자세한 내용은 [지원 되는 스타일 속성](spatial-io-add-simple-data-layer.md#default-supported-style-properties)에 대 한이 문서를 참조 하세요. 사용자 지정 아이콘 이미지는 보안 예방 조치로 Azure Maps 시각적 개체 내에서 지원 되지 않습니다.

다음은 표시 된 색을 빨강으로 설정 하는 GeoJSON point 기능의 예입니다.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>다음 단계

맵에 컨텍스트를 더 추가 합니다.

> [!div class="nextstepaction"]
> [타일 계층 추가](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [실시간 트래픽 표시](power-bi-visual-show-real-time-traffic.md)
