---
title: 시각적 개체 Power BI Azure Maps 시작 | Microsoft Azure 맵
description: 이 문서에서는 Power BI에 대 한 Microsoft Azure Maps 시각적 개체를 사용 하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9f7f44e053cfb26cbf7d897b8a1df1ad2861fd07
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552002"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Azure Maps Power BI 시각적 개체 시작

<Token>**적용 대상:** ![ 예 ](media/power-bi-visual/yes.png) Power BI 서비스 ***소비자*** 에 대 한 예 ![ ](media/power-bi-visual/yes.png) Power BI 서비스 디자이너 & 개발자 ![ 예 Power BI Desktop 아니요에는 ](media/power-bi-visual/yes.png) ![ ](media/power-bi-visual/no.png) Pro 또는 프리미엄 라이선스가 필요 합니다.</Token>

이 문서에서는 Power BI에 대 한 Microsoft Azure Maps 시각적 개체를 사용 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 시각적 개체는 Power BI Desktop 및 Power BI 서비스에서 만들고 볼 수 있습니다. 이 문서의 단계와 그림은 모두 Power BI Desktop에 해당합니다.

Power BI용 Azure Maps 시각적 개체는 지도 위에 공간 데이터에 대한 다양한 데이터 시각화 세트를 제공합니다. 비즈니스 데이터의 80% 이상이 위치 컨텍스트를 가지고 있는 것으로 추정됩니다. Azure Maps 시각적 개체를 사용 하 여이 위치 컨텍스트가 비즈니스 데이터와 어떻게 관련 되어 있는지에 대 한 통찰력을 얻을 수 있습니다.

![비즈니스 데이터를 표시하는 Azure Maps 시각적 개체가 포함된 Power BI 데스크톱](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>Azure로 전송 되는 항목

Azure Maps 시각적 개체는 Azure에서 호스트 되는 클라우드 서비스에 연결 하 여 지도 시각화를 만드는 데 사용 되는 지도 이미지 및 좌표와 같은 위치 데이터를 검색 합니다. 

-   지도를 중심으로 하는 영역에 대 한 세부 정보는 지도 캔버스 (지도 타일)를 렌더링 하는 데 필요한 이미지를 검색 하기 위해 Azure에 전송 됩니다. 
-   위치, 위도 및 경도 버킷의 데이터를 Azure로 전송 하 여 지도 좌표 (지 오 코딩 라는 프로세스)를 검색할 수 있습니다. 
-   Power BI에서 원격 분석 옵션을 사용 하는 경우 원격 분석 데이터가 시각적 개체의 상태 (예: 크래시 보고서)에 수집 될 수 있습니다.

위에서 설명한 시나리오 외에도 맵에 중첩 된 다른 데이터는 Azure Maps 서버로 전송 되지 않습니다. 모든 데이터 렌더링은 클라이언트 내에서 로컬로 발생 합니다.

사용자 또는 관리자는 다음 URL을 사용 하는 Azure Maps 플랫폼에 대 한 액세스를 허용 하도록 방화벽을 업데이트 해야 할 수 있습니다.

> `https://atlas.microsoft.com`

Azure Maps와 관련 된 개인 정보 및 사용 약관에 대해 자세히 알아보려면 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조 하세요.

## <a name="preview-behavior-and-requirements"></a>미리 보기 동작 및 요구 사항

이 **Azure Maps**Preview 릴리스에 대 한 몇 가지 고려 사항 및 요구 사항은 다음과 같습니다.

-   **Azure Maps** 시각적 개체는 미리 보기 상태 이며 Power BI Desktop에서 사용 하도록 설정 해야 합니다. **Azure Maps**를 사용 하도록 설정 하려면 **파일** &gt; **옵션 및 설정** &gt; **옵션** &gt; **미리 보기 기능**을 선택한 다음 **Azure Maps 시각적 개체** 확인란을 선택 합니다. 이 작업을 수행한 후 Azure Maps 시각적 개체를 사용할 수 없는 경우 관리 포털에서 테 넌 트 관리자 스위치를 사용 하도록 설정 해야 할 수 있습니다.
-   데이터 집합에는 **위도** 및 **경도** 정보를 포함 하는 필드가 있어야 합니다. 지 오 코딩 of location 필드는 이후 업데이트에서 추가 될 예정입니다.
-   Power BI에 대 한 기본 제공 범례 컨트롤이 현재이 미리 보기에 표시 되지 않습니다. 이후 업데이트에서 추가 될 예정입니다.

## <a name="use-the-azure-maps-visual"></a>Azure Maps 시각적 개체 사용

**Azure Maps** 시각적 개체를 사용할 수 있게 되 면 **시각화** 창에서 **Azure Maps** 아이콘을 선택 합니다.

![시각화 창의 Azure Maps 시각적 단추](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI 빈 Azure Maps 시각적 디자인 캔버스를 만듭니다. 미리 보기 중에는 추가 부인 사항이 표시 됩니다.

![Azure Maps 시각적 개체를 초기 상태로 로드 한 Power BI desktop](media/power-bi-visual/visual-initial-load.png)

Azure Maps 시각적 개체를 로드 하려면 다음 단계를 수행 합니다.

1.  **필드** 창에서 위도 및 경도 좌표 정보를 포함 하는 데이터 필드를 **위도** 및/또는 **경도** 버킷으로 끌어 옵니다. Azure Maps 시각적 개체를 로드 하는 데 필요한 최소한의 데이터입니다.
    
    > [!div class="mx-imgBorder"]
    > ![위도 및 경도 필드가 제공 된 후 지도에 표시 되는 요소를 거품으로 표시 Azure Maps](media/power-bi-visual/bubble-layer.png)

2.  분류에 따라 데이터의 색을 설정 하려면 범주 필드를 **필드** 창의 **범례** 버킷으로 끌어 옵니다. 이 예제에서는 **Admindistrict** 열 (시/도 라고도 함)을 사용 합니다.  
    
    > [!div class="mx-imgBorder"]
    > ![범례 필드를 제공한 후 지도에 색이 지정 된 거품으로 요소를 표시 하 Azure Maps 시각적 개체](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > Power BI에 대 한 기본 제공 범례 컨트롤이 현재이 미리 보기에 표시 되지 않습니다. 이후 업데이트에서 추가 될 예정입니다.

3.  데이터의 크기를 상대적으로 조정 하려면 **필드** 창의 **크기** 버킷에 측정값을 끌어 놓습니다. 이 예에서는 **Sales** 열을 사용 합니다.  
    
    > [!div class="mx-imgBorder"]
    > ![크기 필드를 제공한 후 지도에 색이 지정 되 고 크기가 조정 되는 시각적 개체를 시각적으로 표시 Azure Maps 합니다.](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  **서식** 창의 옵션을 사용 하 여 데이터가 렌더링 되는 방식을 사용자 지정할 수 있습니다. 다음 이미지는 위와 동일한 맵 이지만 거품형 레이어 채우기 투명도 옵션은 50%로 설정 되 고 고대비 윤곽선 옵션은 사용 하도록 설정 됩니다.  
    
    > [!div class="mx-imgBorder"]
    > ![사용자 지정 스타일을 사용 하 여 지도에서 요소를 거품으로 표시 Azure Maps](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>필드 창 버킷

다음 데이터 버킷은 Azure Maps 시각적 개체의 **필드** 창에서 사용할 수 있습니다.

| 필드     | Description  |
|-----------|--------------|
| 위도  | 데이터 요소의 위도 값을 지정 하는 데 사용 되는 필드입니다. 위도 값은-90과 90 사이의 10 진수도 형식 이어야 합니다.  |
| 경도 | 데이터 요소의 경도 값을 지정 하는 데 사용 되는 필드입니다. 경도 값은-180과 180 사이의 10 진수도 형식 이어야 합니다.  |
| 범례    | 데이터를 분류 하 고 각 범주의 데이터 요소에 고유한 색을 할당 하는 데 사용 되는 필드입니다. 이 버킷이 채워지면 색을 조정할 수 있는 **데이터 색** 섹션이 **서식** 창에 표시 됩니다. |
| 크기      | 지도에서 데이터 요소의 상대적인 크기 조정에 사용 되는 측정값입니다.   |
| 도구 설명  | 셰이프를 가리킬 때 도구 설명에 표시 되는 추가 데이터 필드입니다. |

## <a name="map-settings"></a>맵 설정

서식 창의 **맵 설정** 섹션에는 지도가 표시 되 고 업데이트에 반응 하는 방식을 사용자 지정 하는 옵션이 제공 됩니다.

| 설정             | Description  |
|---------------------|--------------|
| 자동 확대/축소           | 시각적 개체의 **필드** 창을 통해 로드 된 데이터로 자동으로 지도를 확대/축소 합니다. 데이터가 변경 되 면 지도는 그에 따라 해당 위치를 업데이트 합니다. 슬라이더가 **Off** 위치에 있으면 기본 지도 보기에 대 한 추가 지도 보기 설정이 표시 됩니다. |
| 세계 줄 바꿈          | 사용자가 맵을 무한히 세로로 이동할 수 있습니다. |
| 스타일 선택        | 보고서를 읽는 사람이 지도의 스타일을 변경할 수 있도록 하는 단추를 지도에 추가 합니다. |
| 탐색 컨트롤 | 보고서를 읽는 사람이 지도의 피치를 확대/축소, 회전 및 변경할 수 있도록 하는 다른 방법으로 지도에 단추를 추가 합니다. 자세한 내용은 사용자가 맵을 탐색할 수 있는 여러 가지 방법에 대 한 자세한 내용은 [맵 탐색](map-accessibility.md#navigating-the-map) 에 대 한이 문서를 참조 하세요. |
| 지도 스타일           | 지도의 스타일입니다. [지원 되는 맵 스타일](supported-map-styles.md)에 대 한 자세한 내용은이 문서를 참조 하세요. |

### <a name="map-view-settings"></a>지도 보기 설정

**자동 확대/축소** 슬라이더가 **Off** 위치에 있으면 다음 설정이 표시 되 고 사용자가 기본 지도 보기 정보를 지정할 수 있습니다.

| 설정          | Description   |
|------------------|---------------|
| Zoom             | 지도의 기본 확대/축소 수준입니다. 0에서 22 사이의 숫자가 될 수 있습니다. |
| 중심 위도  | 지도의 가운데에 있는 기본 위도입니다. |
| 가운데 경도 | 지도의 가운데에 있는 기본 경도입니다. |
| 제목          | 지도의 기본 방향 (도)입니다. 여기서 0은 북부, 90은 동부, 180는 남부, 270는 서 부입니다. 0에서 360 사이의 숫자가 될 수 있습니다. |
| 피치            | 지도의 기본 기울기는 0에서 60 사이입니다. 여기서 0은 지도에서 바로 아래를 조회 합니다. |

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

Azure Maps 시각적 개체는 다음 서비스 및 응용 프로그램에서 사용할 수 있습니다.

| 서비스/앱                              | 가용성 |
|------------------------------------------|--------------|
| Power BI Desktop                         | 예          |
| Power BI 서비스(app.powerbi.com)       | 예          |
| Power BI 모바일 애플리케이션             | 예          |
| Power BI 웹에 게시                  | 예           |
| Power BI Embedded                        | 아니요           |
| Power BI 서비스 포함(PowerBI.com) | 예          |

추가 Power BI 서비스/앱에 대 한 지원은 향후 업데이트에서 추가 될 예정입니다.

**Azure Maps 사용할 수 있는 위치**

현재는 다음을 제외한 모든 국가 및 지역에서 Azure Maps를 사용할 수 있습니다.

- 중국
- 대한민국

이 시각적 개체를 제공 하는 다양 한 Azure Maps 서비스에 대 한 검사 정보는 [지리적 범위 정보](geographic-coverage.md) 문서를 참조 하세요.

**Azure Maps 시각적 개체에서 지원 되는 웹 브라우저는 무엇입니까?**

[Azure Maps 웹 SDK 지원 브라우저](supported-browsers.md)에 대 한 자세한 내용은이 설명서를 참조 하세요.

**시각화할 수 있는 데이터 요소는 몇 개입니까?**

이 시각적 개체는 최대 3만 데이터 요소를 지원 합니다.

**이 시각적 개체에서 주소 또는 다른 위치 문자열을 사용할 수 있나요?**

이 시각적 개체의 초기 미리 보기는 10 진수도의 위도 및 경도 값만 지원 합니다. 이후 업데이트에서는 주소 및 기타 위치 문자열에 대 한 지원을 추가 합니다.

## <a name="next-steps"></a>다음 단계

Azure Maps Power BI 시각적 개체에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Maps Power BI 시각적 개체의 레이어 이해](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [조직 내에서 Azure Maps 시각적 개체 관리](power-bi-visual-manage-access.md)

시각적 개체를 사용자 지정 합니다.

> [!div class="nextstepaction"]
> [Power BI의 색 서식을 위한 팁과 힌트](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [시각화 제목, 배경 및 범례 사용자 지정](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
