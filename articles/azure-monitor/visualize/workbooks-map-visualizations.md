---
title: Azure Monitor 통합 문서 맵 시각화
description: Azure Monitor 통합 문서 맵 시각화에 대해 알아봅니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615989"
---
# <a name="map-visualization"></a>맵 시각화

지도 시각화는 특정 지역의 핀 가리키기 문제를 지원 하 고 각 위치/국가/지역에 매핑된 모든 데이터를 집계 하는 기능을 제공 하 여 모니터링 데이터의 상위 수준 집계 보기를 표시 합니다.

아래 스크린샷에서는 여러 저장소 계정에 대 한 총 트랜잭션과 종단 간 대기 시간을 보여 줍니다. 여기에서 크기는 총 트랜잭션 수에 따라 결정 되며 지도 아래의 색 메트릭은 종단 간 대기 시간을 보여 줍니다. 첫 번째 관찰 시 미국 **서 부** 지역의 트랜잭션 수는 **미국 동부** 지역에 비해 작지만 미국 **서 부** 지역의 종단 간 대기 시간은 미국 **동부** 지역 보다 높습니다. 이는 **미국 서 부** 에 대 한 amiss를 제공 하는 초기 통찰력을 제공 합니다.

![Azure location map의 스크린샷](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>지도 추가

기본 데이터/메트릭에 위도/경도 정보, Azure 리소스 정보, Azure 위치 정보, 국가/지역, 이름 또는 국가/지역 코드가 있는 경우 맵을 시각화할 수 있습니다.

### <a name="using-azure-location"></a>Azure location 사용

1. **편집** 도구 모음 항목을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가** 를 선택 하 고 *쿼리 추가* 를 선택 합니다.
3. *데이터 원본을* 변경 하 여 `Azure Resource Graph` 저장소 계정이 있는 구독을 선택 합니다.
4. 분석에 대해 아래 쿼리를 입력 하 고 **실행 쿼리** 를 선택 합니다.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. *크기* 를로 설정 `Large` 합니다.
6. *시각화* 를로 설정 `Map` 합니다.
7. 모든 설정은 채워집니다 됩니다. 사용자 지정 설정에서 **지도 설정** 단추를 선택 하 여 설정 창을 엽니다.
8. 다음은 선택한 구독에 대 한 각 Azure 지역에 대 한 저장소 계정을 보여 주는 지도 시각화의 스크린샷입니다.

![위의 쿼리를 사용 하는 Azure location map의 스크린샷](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Azure 리소스 사용

1. **편집** 도구 모음 항목을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가** 를 선택 하 고 *메트릭 추가* 를 선택 합니다.
3. 저장소 계정이 있는 구독을 사용 합니다.
4. 리소스 *종류* 를로 변경 하 `storage account` 고 *리소스* 에서 여러 저장소 계정을 선택 합니다.
5. **메트릭 추가** 를 선택 하 고 트랜잭션 메트릭을 추가 합니다.
    1. 네임스페이스: `Account`
    2. 수치 `Transactions`
    3. 요약 `Sum`
    
    ![트랜잭션 메트릭의 스크린샷](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. **메트릭 추가** 를 선택 하 고 성공 E2E 대기 시간 메트릭을 추가 합니다.
    1. 네임스페이스: `Account`
    1. 수치 `Success E2E Latency`
    1. 요약 `Average`
    
    ![성공 종단 간 대기 시간 메트릭 스크린샷](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. *크기* 를로 설정 `Large` 합니다.
1. *시각화* 크기를로 설정 `Map` 합니다.
1. **지도 설정** 에서 다음 설정을 설정 합니다.
    1. 다음을 사용 하 여 위치 정보: `Azure Resource`
    1. Azure 리소스 필드: `Name`
    1. 크기: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. 위치 집계: `Sum of values`
    1. 색 지정 형식: `Heatmap`
    1. 색 기준: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 색에 대 한 집계: `Sum of values`
    1. 색상표: `Green to Red`
    1. 최 솟 값: `0`
    1. 메트릭 값: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. 다른 메트릭을 집계 합니다. `Sum of values`
    1. **사용자 지정 서식** 상자 선택
    1. 유닛의 `Milliseconds`
    1. Style `Decimal`
    1. 최대 소수 자릿수: `2`

### <a name="using-countryregion"></a>국가/지역 사용

1. **편집** 도구 모음 항목을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가* 를 선택한 다음 *쿼리 추가* 를 선택 합니다.
3. *데이터 원본* 을로 변경 `Log` 합니다.
4. *리소스 종류* 를 선택 하 `Application Insights` 고 pageviews 데이터를 포함 하는 Application Insights 리소스를 선택 합니다.
5. 쿼리 편집기를 사용 하 여 분석을 위한 KQL를 입력 하 고 **쿼리 실행** 을 선택 합니다.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. 크기 값을로 설정 `Large` 합니다.
7. 시각화를로 설정 `Map` 합니다.
8. 모든 설정은 채워집니다 됩니다. 사용자 지정 설정에서 **지도 설정** 을 선택 합니다.

### <a name="using-latitudelocation"></a>위도/위치 사용

1. **편집** 도구 모음 항목을 선택 하 여 통합 문서를 편집 모드로 전환 합니다.
2. **추가* 를 선택한 다음 *쿼리 추가* 를 선택 합니다.
3. *데이터 원본* 을로 변경 `JSON` 합니다.
1. 아래 쿼리 편집기에서 JSON 데이터를 입력 하 고 **쿼리 실행** 을 선택 합니다.
1. *크기* 값을로 설정 `Large` 합니다.
1. *시각화* 를로 설정 `Map` 합니다.
1. **맵** 설정의 "메트릭 설정"에서 *메트릭 레이블* 을으로 설정한 `displayName` 다음 **저장 후 닫기** 를 선택 합니다.

아래 맵 시각화는 메트릭에 대해 선택한 레이블이 있는 각 위도 및 경도 위치의 사용자를 보여 줍니다.

![메트릭에 대해 선택한 레이블을 사용 하 여 각 위도 및 경도 위치의 사용자를 보여 주는 지도 시각화의 스크린샷](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>맵 설정

### <a name="layout-settings"></a>레이아웃 설정

| 설정 | 설명 |
|:-------------|:-------------|
| `Location info using` | 지도에 표시 되는 항목의 위치를 가져올 방법을 선택 합니다. <br> **위도/경도**: 위도 및 경도 정보를 포함 하는 열이 있는 경우이 옵션을 선택 합니다. 위도 및 경도 데이터가 있는 각 행은 지도에 고유한 항목으로 표시 됩니다. <br> **Azure 위치**: azure location (eastus, westeurope, centralindia 등) 정보를 포함 하는 열이 있는 경우이 옵션을 선택 합니다. 해당 열을 지정 하 고 각 Azure 위치에 대 한 해당 위도 및 경도를 인출 하며, 동일한 위치 행 (지정 된 집계에 따라)을 함께 그룹화 하 여 지도의 위치를 표시 합니다. <br> **Azure 리소스**: azure 리소스 정보 (storage 계정, cosmosdb 계정 등)가 있는 열이 있는 경우이 옵션을 선택 합니다. 해당 열을 지정 하 고, 각 Azure 리소스에 대해 해당 위도 및 경도를 인출 하 고, 집계를 기준으로 동일한 위치 (Azure location) 행을 그룹화 하 여 지도에 위치를 표시 합니다. <br> **국가/지역**: 국가/지역 이름/코드가 있는 열 (US, 미국, IN, 인도, CN, 중국) 정보를 포함 하는 경우이 옵션을 선택 합니다. 해당 열을 지정 하 고 각 국가/지역/코드에 대해 해당 위도 및 경도를 인출 하며 동일한 Country-Region 코드/국가-지역 이름과 함께 행을 그룹화 하 여 지도의 위치를 표시 합니다. 국가 이름 및 국가 코드는 지도에서 단일 엔터티로 함께 그룹화 되지 않습니다.
| `Latitude/Longitude` | 위치 정보 필드 값이 위도/경도 인 경우 이러한 두 옵션이 표시 됩니다. 위도 필드에 위도를 포함 하는 열과 경도 필드의 경도를 각각 선택 합니다. |
| `Azure location field` | 위치 정보 필드 값이 Azure location 인 경우이 옵션이 표시 됩니다. Azure 위치 정보가 있는 열을 선택 합니다. |
| `Azure resource field` | 위치 정보 필드 값이 Azure 리소스 인 경우이 옵션이 표시 됩니다. Azure 리소스 정보에 해당 하는 열을 선택 합니다. |
| `Country/Region field` | 위치 정보 필드 값이 국가 또는 지역 인 경우이 옵션이 표시 됩니다. 국가/지역 정보를 나타내는 열을 선택 합니다. |
| `Size by` | 이 옵션은 맵에 표시 되는 항목의 크기를 제어 합니다. 크기는 사용자가 지정한 열의 값에 따라 달라 집니다. 현재 원의 반지름은 열 값의 제곱근에 비례 합니다. ' None ... ' 인 경우 를 선택 하면 모든 원으로 기본 지역 크기가 표시 됩니다.|
| `Aggregation for location` | 이 필드는 동일한 Azure Location/Azure Resource/Country 지역이 있는 열을 **기준으로 크기** 를 집계 하는 방법을 지정 합니다. |
| `Minimum region size` | 이 필드는 맵에 표시 되는 항목의 최소 반경을 지정 합니다. 이는 열 값에 따라 크기가 크게 다를 수 있으므로 작은 항목이 맵에 표시 되지 않습니다. |
| `Maximum region size` | 이 필드는 맵에 표시 되는 항목의 최대 반경을 지정 합니다. 열 값으로 크기가 매우 크고 지도의 큰 영역을 포함 하는 경우에 사용 됩니다.|
| `Default region size` | 이 필드는 맵에 표시 되는 항목의 기본 반지름을 지정 합니다. 기본 반지름은 열 크기 열이 ' 없음 ... ' 인 경우 사용 됩니다. 또는 값이 0입니다.|
| `Minimum value` | 영역 크기를 계산 하는 데 사용 되는 최소값입니다. 지정 하지 않으면 최소값이 집계 후 가장 작은 값이 됩니다. |
| `Maximum value` | 영역 크기를 계산 하는 데 사용 되는 최대값입니다. 지정 하지 않으면 최대값은 집계 후 가장 큰 값이 됩니다.|
| `Opacity of items on Map` | 이 필드는 맵에 표시 되는 항목의 투명도를 지정 합니다. 불투명도 1은 투명도 없음을 의미 하 고 불투명도가 0 이면 항목이 지도에 표시 되지 않습니다. 지도에 항목이 너무 많은 경우 겹치는 모든 항목을 표시 하도록 불투명도를 낮은 값으로 설정할 수 있습니다.|

### <a name="color-settings"></a>색 설정

| 색 지정 유형 | 설명 |
|:------------- |:-------------|
| `None` | 모든 노드의 색은 동일 합니다. |
| `Thresholds` | 이 형식에서 셀 색은 임계값 규칙에 의해 설정 됩니다 (예: _cpu > 90% => Red, 60% > cpu > 90% => 노랑, cpu < 60% => 녹색_). <ul><li> **색 기준**:이 열의 값은 임계값/열 지도 논리에 사용 됩니다.</li> <li>**Color에 대 한 집계**:이 필드는 동일한 azure Location/azure Resource/Country 지역이 있는 열을 **기준으로 색** 을 집계 하는 방법을 지정 합니다. </li> <ul> |
| `Heatmap` | 이 형식에서 셀은 색상표 및 색 기준 필드에 따라 색이 지정 됩니다. 또한 **색 옵션에 대 한** **색** 및 집계가 임계값으로 동일 합니다. |

### <a name="metric-settings"></a>메트릭 설정
| 설정 | 설명 |
|:------------- |:-------------|
| `Metric Label` | 위치 정보 필드 값이 위도/경도 인 경우이 옵션이 표시 됩니다. 사용자는이 기능을 사용 하 여 지도 아래에 표시 되는 메트릭에 대해 표시할 레이블을 선택할 수 있습니다. |
| `Metric Value` | 이 필드는 지도 아래에 표시할 메트릭 값을 지정 합니다. |
| `Create 'Others' group after` | 이 필드는 "기타" 그룹이 만들어지기 전의 제한을 지정 합니다. |
| `Aggregate 'Others' metrics by` | 이 필드는 표시 되는 경우 "기타" 그룹에 사용 되는 집계를 지정 합니다. |
| `Custom formatting` | 이 필드를 사용 하 여 숫자 값의 단위, 스타일 및 서식 옵션을 설정할 수 있습니다. 이는 [그리드의 사용자 지정 서식](../visualize/workbooks-grid-visualizations.md#custom-formatting)지정과 같습니다.|

## <a name="next-steps"></a>다음 단계

- [통합 문서에서 허니 수십억 시각화](../visualize/workbooks-honey-comb.md)를 만드는 방법에 대해 알아봅니다.