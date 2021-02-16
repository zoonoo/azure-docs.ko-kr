---
title: '자습서: 스토리지 계정에서 데이터 분석 시작하기'
description: 이 자습서에서는 스토리지 계정에 있는 데이터를 분석하는 방법을 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: ad16b63360364acd88ab12fb4715d1fd3115c0fb
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209375"
---
# <a name="analyze-data-in-a-storage-account"></a>스토리지 계정에서 데이터 분석

이 자습서에서는 스토리지 계정에 있는 데이터를 분석하는 방법을 알아봅니다.

## <a name="overview"></a>개요

지금까지 데이터가 작업 영역의 데이터베이스에 있는 시나리오에 대해 살펴보았습니다. 이제 스토리지 계정에서 파일을 사용하는 방법을 살펴보겠습니다. 이 시나리오에서는 작업 영역을 만들 때 지정한 작업 영역 및 컨테이너의 기본 스토리지 계정을 사용합니다.

* 스토리지 계정의 이름: **contosolake**
* 스토리지 계정에 있는 컨테이너의 이름: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>스토리지 계정에 CSV 및 Parquet 파일 만들기

새 코드 셀의 Notebook에서 다음 코드를 실행합니다. CSV 파일 및 parquet 파일을 스토리지 계정에 만듭니다.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>스토리지 계정에서 데이터 분석

작업 영역 기본 ADLS Gen2 계정의 데이터를 분석하거나 "**관리**" > "**연결된 서비스**" > "**새로 만들기**"를 통해 ADLS Gen2 또는 Blob 스토리지 계정을 작업 영역에 연결할 수 있습니다(아래 단계는 기본 ADLS Gen2 계정 참조).

1. Synapse Studio에서 **데이터** 허브로 이동한 다음, **연결됨** 을 선택합니다.
1. **스토리지 계정** > **myworkspace(기본 - contosolake)** 로 차례로 이동합니다.
1. **users(기본)** 를 선택합니다. **NYCTaxi** 폴더가 표시됩니다. 내부에는 **PassengerCountStats_csvformat** 및 **PassengerCountStats_parquetformat** 이라는 두 개의 폴더가 표시됩니다.
1. **PassengerCountStats_parquetformat** 폴더를 엽니다. 내부에 이름이 `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`인 parquet 파일이 표시됩니다.
1. **.parquet** 를 마우스 오른쪽 단추로 클릭하고 **새 Notebook** 을 선택한 다음, **DataFrame에 로드** 를 선택합니다. 새 Notebook이 다음과 같은 셀로 생성됩니다.

    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    display(df.limit(10))
    ```

1. **Spark1** 이라는 Spark 풀에 연결합니다. 셀을 실행합니다.
1. **사용자** 폴더로 돌아가기를 클릭합니다. **.parquet** 파일을 다시 마우스 오른쪽 단추로 클릭한 다음, **새 SQL 스크립트** > **상위 100개 행 선택** 을 선택합니다. 다음과 같은 SQL 스크립트를 만듭니다.

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    스크립트 창에서 **연결 대상** 필드가 **기본 제공** 서버리스 SQL 풀로 설정되어 있는지 확인합니다.

1. 스크립트를 실행합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [파이프라인을 사용하여 작업 오케스트레이션](get-started-pipelines.md)
