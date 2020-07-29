---
title: 맵 생성
description: 이 문서에서는 Azure FarmBeats에서 맵을 생성 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84709065"
---
# <a name="generate-maps"></a>맵 생성

Azure FarmBeats를 사용 하 여 위성 이미지 및 센서 데이터 입력을 사용 하 여 다음 맵을 생성할 수 있습니다. 맵은 팜의 지리적 위치를 확인 하 고 장치에 대 한 적절 한 위치를 식별 하는 데 도움이 됩니다.

  - **센서 배치 맵**: 사용할 센서 수와 팜에 배치 하는 위치에 대 한 권장 사항을 제공 합니다.
  - **위성 인덱스 맵**: 팜에 대 한 vegetation 인덱스 및 급수 인덱스를 표시 합니다.
  - **토양 습기 열 지도**: 습기 위성 데이터 및 센서 데이터에의 한 토양 융합 분포를 표시 합니다.

## <a name="sensor-placement-map"></a>센서 배치 맵

FarmBeats 센서 배치 맵은 토양 습기 센서의 배치를 지원 합니다. 지도 출력은 센서 배포를 위한 좌표 목록으로 구성 됩니다. 이러한 센서의 입력은 위성 이미지와 함께 토양 습기 열 지도를 생성 하는 데 사용 됩니다.

이 맵은 연간 여러 날짜에 걸쳐 표시 된 대로 canopy을 분할 하 여 파생 됩니다. 토양와 빌딩도 canopy의 일부입니다. 위치에 필요 하지 않은 센서를 제거할 수 있습니다. 이 맵은 지침을 위한 것 이며, 사용자 지정 기술에 따라 위치 및 숫자를 약간 변경할 수 있습니다. 센서 추가는 회귀 토양 습기 열 지도 결과에 해당 되지 않지만 센서 번호가 줄어들면 하면 성능 저하가 정확도에서 열 지도가 발생할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

센서 배치 맵을 생성 하기 전에 다음 필수 구성 요소를 충족 합니다.

- 팜 크기는 둘 이상의 아크레 합니다.
- 클라우드의 사용 가능한 센티널 장면 수는 선택한 날짜 범위에 대해 6 개 보다 커야 합니다.
- 최소 6 개의 클라우드 사용 가능한 센티널 장면에는 0.3 보다 크거나 같은 정규화 된 Vegetation Index (NDVI)가 있어야 합니다.

    > [!NOTE]
    > 센티널은 사용자 당 두 개의 동시 스레드만 허용 합니다. 결과적으로 작업이 큐에 대기 되 고 완료 하는 데 시간이 오래 걸릴 수 있습니다.

### <a name="dependencies-on-sentinel"></a>센티널에 대 한 종속성

센티널과 관련 된 종속성은 다음과 같습니다.

- 위성 이미지를 다운로드 하기 위한 센티널 성능에 의존 합니다. 센티널 성능 상태 및 유지 관리 [활동](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)을 확인 합니다.
- 센티널은 사용자 당 두 개의 동시 [다운로드 스레드만](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) 허용 합니다.
- 전체 자릿수 맵 생성은 [센티널 적용 및 다시 방문 빈도]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)의 영향을 받습니다.

## <a name="create-a-sensor-placement-map"></a>센서 배치 맵 만들기
이 섹션에서는 센서 배치 맵을 만드는 절차에 대해 자세히 설명 합니다.

> [!NOTE]
> **지도** 페이지 또는 **팜 세부 정보** 페이지의 **전체 자릿수 맵 생성** 드롭다운 메뉴에서 센서 배치 맵을 시작할 수 있습니다.

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동 합니다.
2. **맵 만들기**를 선택 하 고 드롭다운 메뉴에서 **센서 배치** 를 선택 합니다.

    ![센서 배치 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. **센서 배치**를 선택 하면 **센서 배치** 창이 표시 됩니다.

    ![센서 배치 창](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. **팜** 드롭다운 메뉴에서 팜을 선택 합니다.
   팜을 검색 하 고 선택 하려면 드롭다운 목록에서 스크롤 하거나 텍스트 상자에 팜 이름을 입력 합니다.
5. 작년에 대 한 맵을 생성 하려면 **권장**을 선택 합니다.
6. 사용자 지정 날짜 범위에 대 한 맵을 생성 하려면 **날짜 범위 선택**옵션을 선택 합니다. 센서 배치 맵을 생성 하려는 시작 및 종료 날짜를 입력 합니다.
7. **맵 생성**을 선택 합니다.
 작업 정보를 포함 하는 확인 메시지가 나타납니다.

  작업 상태에 대 한 자세한 내용은 **작업 보기**섹션을 참조 하세요. 작업 상태가 *실패*로 표시 되는 경우 *실패* 상태의 도구 설명에 자세한 오류 메시지가 나타납니다. 이 경우에는 이전 단계를 반복 하 여 다시 시도 합니다.

  문제가 지속 되 면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조 하거나 Azure FarmBeats 포럼에 문의 하 여 관련 로그에 [대 한 지원을](https://aka.ms/FarmBeatsMSDN) 요청 하세요.

### <a name="view-and-download-a-sensor-placement-map"></a>센서 배치 맵 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동 합니다.

    ![왼쪽 탐색 메뉴에서 맵 선택](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색 영역에서 **필터** 를 선택 합니다.
  **필터** 창에 검색 조건이 표시 됩니다.

    ![필터 창](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 드롭다운 메뉴에서 **유형**, **날짜**및 **이름** 값을 선택 합니다. 그런 다음, **적용** 을 선택 하 여 보려는 맵을 검색 합니다.
  작업이 생성 된 날짜가 type_farmname_YYYY-MM-DD 형식으로 표시 됩니다.
4. 페이지 끝에 있는 탐색 모음을 사용 하 여 사용 가능한 지도 목록을 스크롤합니다.
5. 보려는 맵을 선택 합니다. 선택한 지도의 미리 보기가 팝업 창에 표시 됩니다.
6. **다운로드**를 선택 하 고 센서 좌표의 GeoJSON 파일을 다운로드 합니다.

    ![센서 배치 맵 미리 보기](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>위성 인덱스 맵

다음 섹션에서는 위성 인덱스 맵 만들기 및 보기와 관련 된 절차를 설명 합니다.

> [!NOTE]
> **맵** 페이지 또는 **팜 세부 정보** 페이지의 **전체 자릿수 맵 생성** 드롭다운 메뉴에서 위성 인덱스 맵을 시작할 수 있습니다.

FarmBeats는 Vegetation에 대 한 NDVI, EVI (향상 된 Index) 및 NDVI (정규화 된 차이 급수 인덱스) 맵을 생성 하는 기능을 제공 합니다. 이러한 인덱스는 자르기가 현재 증가 하 고 있는 방식을 결정 하는 데 도움이 됩니다.


> [!NOTE]
> 센티널 이미지는 지도가 생성 되는 날짜에 필요 합니다.


## <a name="create-a-satellite-indices-map"></a>위성 인덱스 맵 만들기

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동 합니다.
2. **맵 만들기**를 선택 하 고 드롭다운 메뉴에서 **위성 인덱스** 를 선택 합니다.

    ![드롭다운 메뉴에서 위성 인덱스를 선택 합니다.](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. **위성 인덱스**를 선택 하면 **위성 인덱스** 창이 표시 됩니다.

    ![위성 인덱스 창](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 드롭다운 메뉴에서 팜을 선택 합니다.
   팜을 검색 하 고 선택 하려면 드롭다운 목록에서 스크롤하거나 팜의 이름을 입력 합니다.   
5. 지난 주에 대 한 맵을 생성 하려면 **이번 주**를 선택 합니다.
6. 사용자 지정 날짜 범위에 대 한 맵을 생성 하려면 **날짜 범위 선택**옵션을 선택 합니다. 위성 인덱스 맵을 생성 하려는 시작 및 종료 날짜를 입력 합니다.
7. **맵 생성**을 선택 합니다.
    작업 정보를 포함 하는 확인 메시지가 나타납니다.

    ![위성 인덱스 맵 확인 메시지](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    작업 상태에 대 한 자세한 내용은 **작업 보기**섹션을 참조 하세요. 작업 상태가 *실패*로 표시 되는 경우 *실패* 상태의 도구 설명에 자세한 오류 메시지가 나타납니다. 이 경우에는 이전 단계를 반복 하 여 다시 시도 합니다.

    문제가 지속 되 면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조 하거나 Azure FarmBeats 포럼에 문의 하 여 관련 로그에 [대 한 지원을](https://aka.ms/FarmBeatsMSDN) 요청 하세요.

### <a name="view-and-download-a-map"></a>지도 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동 합니다.

    ![맵 선택](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색 영역에서 **필터** 를 선택 합니다. **필터** 창에 검색 조건이 표시 됩니다.

    ![필터 창에 검색 조건 표시](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 드롭다운 메뉴에서 **유형**, **날짜**및 **이름** 값을 선택 합니다. 그런 다음, **적용** 을 선택 하 여 보려는 맵을 검색 합니다.
  작업이 생성 된 날짜가 type_farmname_YYYY-MM-DD 형식으로 표시 됩니다.

4. 페이지 끝에 있는 탐색 모음을 사용 하 여 사용 가능한 지도 목록을 스크롤합니다.
5. **팜 이름과** **날짜**의 각 조합에 대해 다음과 같은 세 가지 맵을 사용할 수 있습니다.
    - NDVI
    - EVI
    - NDWI
6. 보려는 맵을 선택 합니다. 선택한 지도의 미리 보기가 팝업 창에 표시 됩니다.
7. 다운로드 형식을 선택 하려면 드롭다운 메뉴에서 **다운로드** 를 선택 합니다. 지도를 다운로드 하 여 컴퓨터의 로컬 폴더에 저장 합니다.

    ![선택한 위성 인덱스 맵 미리 보기](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>토양 습기 열 지도

토양 습기는 토양 파티클 사이에 있는 공간에 유지 되는 물입니다.토양 습기 열 지도를 사용 하면 팜 내에서 높은 해상도로 토양 습기 데이터를 자세히 파악할 수 있습니다. 정확 하 고 사용 가능한 토양 습기 열 지도을 생성 하기 위해 일관 된 센서 배포가 필요 합니다. 모든 센서는 동일한 공급자에 속해야 합니다. 토양 습기를 측정 하는 방식에 따라 다른 공급자는 차이가 있습니다. 열 지도는 해당 깊이에서 배포 된 센서를 사용 하 여 특정 깊이에 대해 생성 됩니다.

### <a name="before-you-begin"></a>시작하기 전에

토양 습기 열 지도 생성을 시도 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

- 토양 습기 센서를 세 개 이상 배포 해야 합니다. 센서가 배포 되어 팜에 연결 되기 전에 토양 습기 열 지도를 만들지 마세요.
- 토양 습기 열 지도 생성은 센티널의 경로 범위, 클라우드 덮개 및 클라우드 섀도 영향을 받습니다. 토양 습기 열 지도가 요청 된 날부터 지난 120 일 동안 하나 이상의 클라우드-무료 센티널 장면을 사용할 수 있어야 합니다.
- 팜에 배포 된 센서 중 절반 이상이 온라인 상태이 고 데이터를 데이터 허브로 스트리밍하 고 있어야 합니다.
- 열 지도는 동일한 공급자의 센서 측정값을 사용 하 여 생성 해야 합니다.


## <a name="create-a-soil-moisture-heatmap"></a>토양 습기 열 지도 만들기

다음 단계를 수행합니다.

1. 홈 페이지에서 왼쪽 탐색 메뉴의 **맵** 으로 이동 하 여 **맵** 페이지를 확인 합니다.
2. **맵 만들기**를 선택 하 고 드롭다운 메뉴에서 **토양 습기** 를 선택 합니다.

    ![드롭다운 메뉴에서 토양 습기를 선택 합니다.](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. **토양 습기**를 선택 하면 **토양 습기** 창이 표시 됩니다.

    ![토양 습기 창](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. **팜** 드롭다운 메뉴에서 팜을 선택 합니다.
   팜을 검색 하 고 선택 하려면 드롭다운 목록에서 스크롤하거나 팜 **선택** 드롭다운 메뉴에서 팜 이름을 입력할 수 있습니다.
5. **토양 습기 센서 측정값 선택** 드롭다운 메뉴에서 맵을 생성 하려는 토양 습기 센서 측정값 (깊이)을 선택 합니다.
센서 측정값을 찾으려면 **센서**로 이동 하 고 토양 습기 센서를 선택 합니다. 그런 다음 **센서 속성** 섹션에서 **측정값 이름**에 값을 사용 합니다.
6. **오늘** 또는 **이번 주**에 대 한 맵을 생성 하려면 옵션 중 하나를 선택 합니다.
7. 사용자 지정 날짜 범위에 대 한 맵을 생성 하려면 **날짜 범위 선택**옵션을 선택 합니다. 토양 습기 열 지도을 생성 하려는 시작 및 종료 날짜를 입력 합니다.
8. **맵 생성**을 선택 합니다.
 작업 정보를 포함 하는 확인 메시지가 나타납니다.

   ![토양 습기 map 확인 메시지](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    작업 상태에 대 한 자세한 내용은 **작업 보기**섹션을 참조 하세요. 작업 상태가 *실패*로 표시 되는 경우 *실패* 상태의 도구 설명에 자세한 오류 메시지가 나타납니다. 이 경우에는 이전 단계를 반복 하 여 다시 시도 합니다.

    문제가 지속 되 면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조 하거나 Azure FarmBeats 포럼에 문의 하 여 관련 로그에 [대 한 지원을](https://aka.ms/FarmBeatsMSDN) 요청 하세요.

### <a name="view-and-download-a-map"></a>지도 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동 합니다.

    ![맵으로 이동](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색 영역에서 **필터** 를 선택 합니다. 지도를 검색할 수 있는 위치에 **필터** 창이 표시 됩니다.

    ![왼쪽 탐색 모음에서 필터를 선택 합니다.](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  드롭다운 메뉴에서 **유형**, **날짜**및 **이름** 값을 선택 합니다. 그런 다음, **적용** 을 선택 하 여 보려는 맵을 검색 합니다. 작업이 생성 된 날짜가 type_farmname_YYYY-MM-DD 형식으로 표시 됩니다.
4. **팜**, 날짜, **만든** **날짜**, **작업 ID**및 **작업 유형에**따라 정렬 하려면 테이블 머리글 옆에 있는 **정렬** 아이콘을 선택 합니다.
5. 페이지 끝의 탐색 단추를 사용 하 여 사용 가능한 지도 목록을 스크롤합니다.
6. 보려는 맵을 선택 합니다. 선택한 지도의 미리 보기가 팝업 창에 표시 됩니다.
7. 다운로드 형식을 선택 하려면 드롭다운 메뉴에서 **다운로드** 를 선택 합니다. 맵이 다운로드 되어 지정 된 폴더에 저장 됩니다.

    ![토양 습기 열 지도 미리 보기](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
