---
title: Azure 데이터 탐색기를 사용 하 여 Azure Data Lake의에서 데이터를 쿼리 합니다.
description: Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터를 쿼리 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453178"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Data Lake의에서 데이터를 쿼리 합니다.

Azure Data Lake Storage는 빅 데이터 분석용 뛰어나고 비용 효율적인 데이터 레이크 솔루션. 고성능 파일 시스템의 기능과 거대한 규모 및 경제성을 결합하여 인사이트 시간을 단축합니다. Data Lake Storage Gen2는 Azure Blob Storage 기능을 확장하며 분석 워크로드에 최적화되어 있습니다.
 
Azure Blob Storage를 사용 하 여 azure 데이터 탐색기 통합 및 Azure Data Lake 저장소 Gen2를 빠르게 제공 하는 캐시 인덱싱되고 lake의 데이터에 대 한 액세스. 분석 및 Azure 데이터 탐색기에 이전 수집 하지 않고 lake의 데이터를 쿼리할 수 있습니다. 쿼리할 수도 있습니다 수집 및 uningested 네이티브 lake 데이터에서 동시에 합니다.  

> [!TIP]
> 최고의 쿼리 성능을 Azure 데이터 탐색기로 데이터 수집을 해야 합니다. 기록 데이터 또는 자주 쿼리 되는 데이터에 대 한 이전 수집 하지 않고 Azure Data Lake 저장소 Gen2에서 데이터를 쿼리 하는 기능을 사용할만 해야 합니다.
 
## <a name="optimize-query-performance-in-the-lake"></a>레이크에 쿼리 성능 최적화 

* 향상 된 성능 및 최적화 된 쿼리 시간에 대 한 데이터를 분할 합니다.
* (최상의 압축 gzip, 최상의 성능을 위해 lz4) 성능 향상된을 위해 데이터를 압축 합니다.
* Azure 데이터 탐색기 클러스터와 동일한 영역을 사용 하 여 Azure Blob Storage 또는 Azure Data Lake 저장소 Gen2를 사용 합니다. 

## <a name="create-an-external-table"></a>외부 테이블 만들기

1. 사용 된 `.create external table` Azure 데이터 탐색기에서 외부 테이블을 만드는 명령입니다. 외부 테이블 추가 같은 명령 `.show`, `.drop`, 및 `.alter` 에 설명 되어 있습니다 [External table 명령은](/azure/kusto/management/externaltables)합니다.

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    이 쿼리는 매일 분할을 만듭니다 *container1/yyyy/MM/dd/all_exported_blobs.csv*합니다. 성능 향상된 보다 세분화 된 분할로 예상 됩니다. 예를 들어 위와 같은 매일 분할을 사용 하 여 외부 테이블에 대 한 쿼리 월간 분할 된 테이블을 사용 하 여 해당 쿼리보다 성능이 더 높습니다.

    > [!NOTE]
    > 현재 지원 되는 저장소 계정에는 Azure Blob Storage 또는 Azure Data Lake 저장소 Gen2 됩니다. 현재 지원 되는 데이터 형식은 csv, tsv 및 txt입니다.

1. 외부 테이블은 웹 UI의 왼쪽된 창에 표시

    ![웹 UI에서에서 외부 테이블](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>외부 테이블 권한
 
* 데이터베이스 사용자에는 외부 테이블을 만들 수 있습니다. 테이블을 만든 사람만 테이블 관리자를 자동으로 됩니다.
* 클러스터, 데이터베이스 또는 테이블 관리자는 기존 테이블을 편집할 수 있습니다.
* 모든 데이터베이스 사용자 또는 판독기는 외부 테이블을 쿼리할 수 있습니다.
 
## <a name="query-an-external-table"></a>외부 테이블을 쿼리 합니다.
 
외부 테이블을 쿼리하려면 다음을 사용 합니다 `external_table()` 함수 및 함수 인수로 테이블 이름을 제공 합니다. 쿼리의 나머지는 표준 Kusto 쿼리 언어입니다.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense는 외부 테이블 쿼리에 현재 지원 되지 않습니다.

## <a name="query-external-and-ingested-data-together"></a>외부 및 수집 된 데이터를 함께 쿼리

외부 테이블과 동일한 쿼리 내에서 수집 된 데이터 테이블을 쿼리할 수 있습니다. 있습니다 [ `join` ](/azure/kusto/query/joinoperator) 하거나 [ `union` ](/azure/kusto/query/unionoperator) Azure 데이터 탐색기, SQL 서버 또는 다른 원본에서 추가 데이터를 사용 하 여 외부 테이블입니다. 사용 된 [ `let( ) statement` ](/azure/kusto/query/letstatement) 약식 이름에 외부 테이블 참조를 할당할 합니다.

아래 예에서 *제품* 은 수집 된 데이터 테이블 및 *ArchivedProducts* 은 Azure Data Lake 저장소 Gen2의 데이터를 포함 하는 외부 테이블.

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>쿼리 *TaxiRides* 도움말 클러스터에 있는 외부 테이블

합니다 *TaxiRides* 에서 뉴욕시 택시 데이터를 포함 하는 샘플 데이터 집합 [NYC 택시 및 리무진 수수료](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)합니다.

### <a name="create-external-table-taxirides"></a>외부 테이블 만들기 *TaxiRides* 

> [!NOTE]
> 이 섹션을 만드는 데 사용 하는 쿼리를 보여 줍니다.는 *TaxiRides* 에서 외부 테이블의 *도움말* 클러스터. 이 테이블을 아직 생성 되지 않았기 때문에이 섹션을 건너뛸 고 수행할 수 있습니다 [쿼리 *TaxiRides* 외부 테이블 데이터](#query-taxirides-external-table-data)입니다. 

1. 다음 쿼리는 외부 테이블을 만드는 데 사용한 *TaxiRides* 도움말 클러스터에 있는 합니다. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. 결과 테이블에서 만든 합니다 *도움말* 클러스터:

    ![TaxiRides 외부 테이블](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>쿼리 *TaxiRides* 외부 테이블 데이터 

에 로그인 [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) 쿼리 하는 *TaxiRides* 외부 테이블입니다. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>쿼리 *TaxiRides* 분할 수 없이 외부 테이블

[이 쿼리를 실행할](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) 외부 테이블에 대해 *TaxiRides* 탑승 각 요일에 대 한 전체 데이터 집합에서 설명 하는 데 있습니다. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

이 쿼리는 가장 많이 사용 되는 요일을 표시합니다. 데이터 분할 되지 있으므로이 쿼리는 결과 (최대 몇 분 정도)을 반환 하는 데 시간이 오래 걸릴 수 있습니다.

![분할 되지 않은 쿼리를 렌더링 합니다.](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>TaxiRides 쿼리 분할 외부 테이블 

[이 쿼리를 실행할](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) 외부 테이블에 대해 *TaxiRides* 2017 년 1 월에에서 사용 되는 택시 cab 형식 (노란색 또는 녹색)를 표시 합니다. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

이 쿼리 성능과 쿼리 시간을 최적화 하는 분할을 사용 합니다. 쿼리는 분할된 된 열 (pickup_datetime)에 필터링 하 고 잠시 후에 결과 반환 합니다.

![분할 된 쿼리를 렌더링 합니다.](media/data-lake-query-data/taxirides-with-partition.png)
  
외부 테이블에서 실행 되도록 추가 쿼리를 작성할 수 있습니다 *TaxiRides* 데이터에 대해 알아봅니다. 

## <a name="next-steps"></a>다음 단계

Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터를 쿼리 합니다. 하는 방법을 알아봅니다 [쿼리를 작성할](write-queries.md) 데이터에서 추가 정보를 파생 합니다.