---
title: 맵 생성
description: 이 문서에서는 Azure FarmBeats에서 맵을 생성하는 방법을 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271773"
---
# <a name="generate-maps"></a>맵 생성

Azure FarmBeats를 사용하면 위성 이미지 및 센서 데이터 입력을 사용하여 다음 맵을 생성할 수 있습니다. 지도는 팜의 지리적 위치를 확인하고 장치에 적합한 위치를 식별하는 데 도움이 됩니다.

  - **센서 배치 맵**: 사용할 센서 수와 팜에 배치할 위치에 대한 권장 사항을 제공합니다.
  - 위성 지수 맵 : 농장의 식수 지수와 물 지수를 **표시합니다.**
  - **토양 수분 히트맵**: 위성 데이터와 센서 데이터를 융합하여 토양 수분 분포를 표시합니다.

## <a name="sensor-placement-map"></a>센서 배치 맵

FarmBeats 센서 배치 맵은 토양 수분 센서의 배치를 지원합니다. 맵 출력은 센서 배포를 위한 좌표 목록으로 구성됩니다. 이러한 센서의 입력은 위성 이미지와 함께 토양 수분 히트맵을 생성하는 데 사용됩니다.

이 맵은 일년 내내 여러 날짜에 걸쳐 보이는 캐노피를 분할하여 파생됩니다. 심지어 벌거 벗은 토양과 건물은 캐노피의 일부입니다. 위치에 필요하지 않은 센서를 제거할 수 있습니다. 이 맵은 안내를 위한 것이며 사용자 지정 지식에 따라 위치와 숫자를 약간 변경할 수 있습니다. 센서를 추가해도 토양 수분 히트맵 결과가 회귀되지는 않지만 센서 수가 줄어들면 히트맵 정확도가 저하될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

센서 배치 맵을 생성하기 전에 다음 필수 구성 조건을 충족합니다.

- 팜 크기는 두 에이커 이상이어야 합니다.
- 선택한 날짜 범위에 대해 구름이 없는 센티넬 장면의 수는 6개 이상이어야 합니다.
- 구름이 없는 센티넬 장면6개 이상은 0.3보다 크거나 같은 정규화된 차이 식물 지수(NDVI)가 있어야 합니다.

    > [!NOTE]
    > Sentinel은 사용자당 두 개의 동시 스레드만 허용합니다. 따라서 작업이 큐에 대기되고 완료하는 데 시간이 더 오래 걸릴 수 있습니다.

### <a name="dependencies-on-sentinel"></a>센티넬에 대한 종속성

센티넬과 관련된 다음 종속성은 다음과 관련이 있습니다.

- 우리는 위성 이미지를 다운로드센티넬 성능에 의존. 센티넬 성능 상태 및 유지 관리 [활동을](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)확인합니다.
- Sentinel은 사용자당 두 개의 동시 [다운로드 스레드만 허용합니다.](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)
- 정밀 맵 생성은 [센티넬 커버리지 및 재방문 빈도의]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)영향을 받습니다.

## <a name="create-a-sensor-placement-map"></a>센서 배치 맵 만들기
이 섹션에서는 센서 배치 맵을 만드는 절차에 대해 자세히 설명합니다.

> [!NOTE]
> **지도** 페이지 또는 **농장 세부 정보** 페이지의 **정밀도** 생성 드롭다운 메뉴에서 센서 배치 맵을 시작할 수 있습니다.

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **지도로** 이동합니다.
2. **맵 만들기를**선택하고 드롭다운 메뉴에서 **센서 배치를** 선택합니다.

    ![센서 배치 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. **센서 배치를**선택하면 **센서 배치** 창이 나타납니다.

    ![센서 배치 창](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 팜 드롭다운 메뉴에서 **팜을** 선택합니다.
   팜을 검색하고 선택하려면 드롭다운 목록에서 스크롤하거나 텍스트 상자에 팜 이름을 입력할 수 있습니다.
5. 작년에 대한 맵을 생성하려면 **권장**을 선택합니다.
6. 사용자 지정 날짜 범위에 대한 맵을 생성하려면 **날짜 범위 선택 옵션을**선택합니다. 센서 배치 맵을 생성할 시작 날짜와 종료 날짜를 입력합니다.
7. **맵 생성을**선택합니다.
 작업 세부 정보가 있는 확인 메시지가 나타납니다.

  작업 상태에 대한 자세한 내용은 **작업 보기**섹션을 참조하십시오. 작업 상태가 *실패됨으로*표시되면 *Failed* 상태의 도구 설명에 자세한 오류 메시지가 나타납니다. 이 경우 이전 단계를 반복하고 다시 시도하십시오.

  문제가 지속되면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조하거나 [Azure FarmBeats 포럼에 문의하여](https://aka.ms/FarmBeatsMSDN) 관련 로그를 지원하십시오.

### <a name="view-and-download-a-sensor-placement-map"></a>센서 배치 맵 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **지도로** 이동합니다.

    ![왼쪽 탐색 메뉴에서 맵 선택](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색에서 **필터를** 선택합니다.
  **필터** 창에는 검색 조건이 표시됩니다.

    ![필터 창](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 드롭다운 메뉴에서 **유형,** **날짜**및 **이름** 값을 선택합니다. 그런 다음 **적용을** 선택하여 보려는 맵을 검색합니다.
  작업이 생성된 날짜는 type_farmname_YYYY-MM-DD 형식으로 표시됩니다.
4. 페이지 끝에 있는 탐색 모음을 사용하여 사용할 수 있는 맵 목록을 스크롤합니다.
5. 보려는 맵을 선택합니다. 팝업 창에는 선택한 맵의 미리 보기가 표시됩니다.
6. **다운로드를**선택하고 센서 좌표의 GeoJSON 파일을 다운로드합니다.

    ![센서 배치 맵 미리 보기](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>위성 지수 지도

다음 섹션에서는 위성 인덱스 맵을 만들고 보는 데 관련된 절차를 설명합니다.

> [!NOTE]
> **지도** 페이지 또는 **팜 세부 정보** 페이지의 **정밀도 생성** 드롭다운 메뉴에서 위성 지수 맵을 시작할 수 있습니다.

FarmBeats는 농장에 대한 NDVI, 향상된 식물 지수(EVI) 및 정규화된 차이 물 지수(NDWI) 맵을 생성하는 기능을 제공합니다. 이 지수는 작물이 현재 성장하고 있는 방법, 또는 과거에 성장한 방법, 그리고 지상의 대표적인 수위를 결정하는 데 도움이 됩니다.


> [!NOTE]
> 맵이 생성되는 날에는 파수꾼 이미지가 필요합니다.


## <a name="create-a-satellite-indices-map"></a>위성 지수 맵 만들기

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **지도로** 이동합니다.
2. **지도 만들기를**선택하고 드롭다운 메뉴에서 **위성 인덱스를** 선택합니다.

    ![드롭다운 메뉴에서 위성 지수 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. **위성 인덱스를**선택하면 위성 **인덱스 창이** 나타납니다.

    ![위성 지수 창](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 드롭다운 메뉴에서 팜을 선택합니다.
   팜을 검색하고 선택하려면 드롭다운 목록에서 스크롤하거나 팜 이름을 입력할 수 있습니다.   
5. 지난 주에 대한 맵을 생성하려면 **이번 주를**선택합니다.
6. 사용자 지정 날짜 범위에 대한 맵을 생성하려면 **날짜 범위 선택 옵션을**선택합니다. 위성 지수 맵을 생성할 시작 및 종료 날짜를 입력합니다.
7. **맵 생성을**선택합니다.
    작업 세부 정보가 있는 확인 메시지가 나타납니다.

    ![위성 지수 지도 확인 메시지](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    작업 상태에 대한 자세한 내용은 **작업 보기**섹션을 참조하십시오. 작업 상태가 *실패됨으로*표시되면 *Failed* 상태의 도구 설명에 자세한 오류 메시지가 나타납니다. 이 경우 이전 단계를 반복하고 다시 시도하십시오.

    문제가 지속되면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조하거나 [Azure FarmBeats 포럼에 문의하여](https://aka.ms/FarmBeatsMSDN) 관련 로그를 지원하십시오.

### <a name="view-and-download-a-map"></a>지도 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **지도로** 이동합니다.

    ![지도 선택](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색에서 **필터를** 선택합니다. **필터** 창에는 검색 조건이 표시됩니다.

    ![필터 창에는 검색 조건이 표시됩니다.](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 드롭다운 메뉴에서 **유형,** **날짜**및 **이름** 값을 선택합니다. 그런 다음 **적용을** 선택하여 보려는 맵을 검색합니다.
  작업이 생성된 날짜는 type_farmname_YYYY-MM-DD 형식으로 표시됩니다.

4. 페이지 끝에 있는 탐색 모음을 사용하여 사용할 수 있는 맵 목록을 스크롤합니다.
5. **농장 이름과** **날짜의**각 조합에 대해 다음 세 가지 맵을 사용할 수 있습니다.
    - NDVI
    - Evi
    - ND위 주
6. 보려는 맵을 선택합니다. 팝업 창에는 선택한 맵의 미리 보기가 표시됩니다.
7. 드롭다운 메뉴에서 **다운로드를** 선택하여 다운로드 형식을 선택합니다. 맵이 다운로드되어 컴퓨터의 로컬 폴더에 저장됩니다.

    ![선택한 위성 지수 맵 미리 보기](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>토양 수분 히트맵

토양 수분은 토양 입자 사이의 공간에 보관되는 물입니다.토양 수분 히트맵을 사용하면 농장 내의 고해상도로 모든 깊이의 토양 수분 데이터를 이해할 수 있습니다. 정확하고 사용 가능한 토양 수분 히트맵을 생성하려면 센서를 균일하게 배치해야 합니다. 모든 센서는 동일한 공급자에 있어야 합니다. 다른 공급자는 보정의 차이와 함께 토양 수분을 측정하는 방식에 차이가 있습니다. 히트맵은 해당 깊이에 배치된 센서를 사용하여 특정 깊이에 대해 생성됩니다.

### <a name="before-you-begin"></a>시작하기 전에

토양 수분 히트맵을 생성하기 전에 다음 필수 구성 조건을 충족하십시오.

- 적어도 3개의 토양 수분 센서를 배치해야 합니다. 센서를 배포하고 팜과 연결하기 전에 토양 수분 히트맵을 만들려고 하지 마십시오.
- 토양 수분 히트맵 생성은 센티넬의 경로 커버리지, 구름 덮개 및 구름 그림자의 영향을 받습니다. 토양 수분 히트맵이 요청된 날로부터 지난 120일 동안 구름이 없는 센티넬 씬을 하나 이상 사용할 수 있어야 합니다.
- 팜에 배포된 센서의 절반 이상이 온라인이어야 하며 데이터 허브로 스트리밍되는 데이터가 있어야 합니다.
- 히트맵은 동일한 공급자의 센서 측정값을 사용하여 생성되어야 합니다.


## <a name="create-a-soil-moisture-heatmap"></a>토양 수분 히트맵 만들기

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **지도로** 이동하여 **지도** 페이지를 봅니다.
2. **지도 작성을**선택하고 드롭다운 메뉴에서 **토양 수분을** 선택합니다.

    ![드롭다운 메뉴에서 토양 수분 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. **토양 수분을**선택하면 **토양 수분** 창이 나타납니다.

    ![토양 수분 창](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 팜 드롭다운 메뉴에서 **팜을** 선택합니다.
   팜을 검색하고 선택하려면 드롭다운 목록에서 스크롤하거나 **팜 드롭다운 선택** 메뉴에 팜 이름을 입력할 수 있습니다.
5. 토양 **수분 센서 선택** 드롭다운 메뉴에서 맵을 생성할 토양 수분 센서 측정값(깊이)을 선택합니다.
센서 측정값을 찾으려면 **센서로**이동하여 토양 수분 센서를 선택하십시오. 그런 다음 **센서 속성** 섹션에서 **측정값 이름에서**값을 사용합니다.
6. **오늘** 또는 **이번 주에**대한 맵을 생성하려면 옵션 중 하나를 선택합니다.
7. 사용자 지정 날짜 범위에 대한 맵을 생성하려면 **날짜 범위 선택 옵션을**선택합니다. 토양 수분 히트맵을 생성할 시작 및 종료 날짜를 입력합니다.
8. **맵 생성을**선택합니다.
 작업 세부 정보가 있는 확인 메시지가 나타납니다.

   ![토양 수분 지도 확인 메시지](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    작업 상태에 대한 자세한 내용은 **작업 보기**섹션을 참조하십시오. 작업 상태가 *실패됨으로*표시되면 *Failed* 상태의 도구 설명에 자세한 오류 메시지가 나타납니다. 이 경우 이전 단계를 반복하고 다시 시도하십시오.

    문제가 지속되면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조하거나 [Azure FarmBeats 포럼에 문의하여](https://aka.ms/FarmBeatsMSDN) 관련 로그를 지원하십시오.

### <a name="view-and-download-a-map"></a>지도 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴에서 **지도로** 이동합니다.

    ![지도로 이동](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색에서 **필터를** 선택합니다. **필터** 창은 맵을 검색할 수 있는 위치에 표시됩니다.

    ![왼쪽 탐색에서 필터 선택](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  드롭다운 메뉴에서 **유형,** **날짜**및 **이름** 값을 선택합니다. 그런 다음 **적용을** 선택하여 보려는 맵을 검색합니다. 작업이 생성된 날짜는 type_farmname_YYYY-MM-DD 형식으로 표시됩니다.
4. 테이블 헤더 옆에 있는 **정렬** 아이콘을 선택하여 **Farm,** **날짜,** **생성된**항목, **작업 ID**및 작업 **유형에**따라 정렬합니다.
5. 페이지 끝에 있는 탐색 단추를 사용하여 사용할 수 있는 맵 목록을 스크롤합니다.
6. 보려는 맵을 선택합니다. 팝업 창에는 선택한 맵의 미리 보기가 표시됩니다.
7. 드롭다운 메뉴에서 **다운로드를** 선택하여 다운로드 형식을 선택합니다. 맵이 다운로드되어 지정된 폴더에 저장됩니다.

    ![토양 수분 히트맵 미리보기](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
