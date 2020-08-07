---
title: Synapse Studio 노트북
description: 이 문서에서는 데이터 준비 및 시각화를 수행하기 위해 Azure Synapse Studio(미리 보기) Notebook을 만들고 개발하는 방법을 알아봅니다.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6e0062450889a2bbbdfcd47137ffbe36b83cae57
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849101"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse Studio (미리 보기) 노트북 만들기, 개발 및 유지 관리

Synapse Studio (미리 보기) 노트북은 라이브 코드, 시각화 및 설명을 포함 하는 파일을 만들 수 있는 웹 인터페이스입니다. Notebook은 아이디어를 검증하고 빠른 실험을 사용하여 데이터를 통해 인사이트를 확보하기 좋은 도구입니다. Notebook은 데이터 준비, 데이터 시각화, 기계 학습 및 기타 빅 데이터 시나리오에서도 널리 사용됩니다.

Azure Synapse Studio Notebook을 사용하면 다음이 가능합니다.

* 시작에 필요한 설정 작업이 전혀 없습니다.
* 기본 제공 엔터프라이즈 보안 기능으로 데이터 보안을 유지합니다.
* 원시 형식(CSV, txt, JSON 등), 처리된 파일 형식(parquet, Delta Lake, ORC 등), SQL 및 Spark에 대한 SQL 테이블 형식 데이터 파일 모두에서 데이터를 분석합니다.
* 향상된 제작 기능 및 기본 제공 데이터 시각화를 통해 생산성을 높일 수 있습니다.

이 문서에서는 Azure Synapse Studio에서 Notebook을 사용하는 방법을 설명합니다.

## <a name="create-a-notebook"></a>Notebook 만들기

Notebook을 만드는 방법은 두 가지입니다. **개체 탐색기**에서 새 Notebook을 만들거나 Azure Synapse 작업 영역으로 기존 Notebook을 가져올 수 있습니다. Azure Synapse Studio Notebook은 표준 Jupyter Notebook IPYNB 파일을 인식할 수 있습니다.

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Notebook 개발

노트북은 셀로 구성됩니다. 셀은 독립적으로 또는 그룹으로 실행할 수 있는 개별 코드 또는 텍스트 블록입니다.

### <a name="add-a-cell"></a>셀 추가

Notebook에 새 셀을 추가하는 방법은 여러 가지입니다.

1. 왼쪽 위에 있는 **+ 셀** 단추를 펼치고 **코드 셀 추가** 또는 **텍스트 셀 추가**를 선택합니다.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 두 셀 사이의 공간을 마우스로 가리키고 **코드 추가** 또는 **텍스트 추가**를 선택합니다.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. 현재 셀 위에 셀을 삽입하려면 **A** 를 누릅니다. 현재 셀 아래에 셀을 삽입하려면 **B**를 누릅니다.

### <a name="set-a-primary-language"></a>주 언어 설정

Azure Synapse Studio Notebook은 네 가지 Apache Spark 언어를 지원합니다.

* pySpark(Python)
* Spark(Scala)
* SparkSQL
* .NET for Apache Spark(C#)

새로 추가한 셀의 주 언어는 위쪽 명령 모음의 드롭다운 목록에서 설정할 수 있습니다.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>여러 언어 사용

셀 시작 부분에 올바른 언어 매직 명령을 지정하면 Notebook 하나에서 여러 언어를 사용할 수 있습니다. 셀 언어를 전환하는 매직 명령은 다음 표에 나열되어 있습니다.

|매직 명령 |언어 | Description |  
|---|------|-----|
|%%pyspark| Python | Spark 컨텍스트에 대해 **Python** 쿼리를 실행합니다.  |
|%%spark| 스칼라 | Spark 컨텍스트에 대해 **Scala** 쿼리를 실행합니다.  |  
|%%sql| SparkSQL | Spark 컨텍스트에 대해 **SparkSQL** 쿼리를 실행합니다.  |
|%%csharp | .NET for Spark C# | Spark 컨텍스트에 대해 **.NET for Spark C#** 쿼리를 실행합니다. |

다음 이미지는 **Spark(Scala)** Notebook에서 **%%pyspark** 매직 명령을 사용하여 PySpark 쿼리를 작성하거나 **%%sql** 매직 명령을 사용하여 SparkSQL 쿼리를 작성하는 방법의 예입니다. Notebook의 주 언어는 pySpark로 설정되어 있습니다.

   ![synapse-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>임시 테이블을 사용하여 언어 간 데이터 참조

Synapse Studio Notebook의 여러 언어 간에 직접 데이터나 변수를 참조할 수 없습니다. Spark에서는 여러 언어 간에 임시 테이블을 참조할 수 있습니다. 다음은 차선책으로 Spark temp 테이블을 사용하여 `PySpark`와 `SparkSQL`에서 `Scala` DataFrame을 읽는 방법의 예입니다.

1. 셀 1에서는 Scala를 사용하여 SQL 풀 커넥터에서 DataFrame을 읽고 임시 테이블을 생성합니다.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 셀 2에서는 Spark SQL을 사용하여 데이터를 쿼리합니다.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 셀 3에서는 PySpark의 데이터를 사용합니다.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 스타일 IntelliSense

Azure Synapse Studio Notebook은 Monaco 편집기와 통합되어 IDE 스타일 IntelliSense를 셀 편집기로 가져옵니다. 구문 강조 표시, 오류 표식 및 자동 코드 완성을 통해 코드를 작성 하 고 문제를 더 빠르게 식별할 수 있습니다.

IntelliSense 기능은 완성도 수준이 언어마다 다릅니다. 지원되는 기능은 아래 표를 참조하세요.

|언어| 구문 강조 표시 | 구문 오류 표식  | 구문 코드 완성 | 변수 코드 완성| 시스템 함수 코드 완성| 사용자 함수 코드 완성| 스마트 들여쓰기 | 코드 접기|
|--|--|--|--|--|--|--|--|--|
|PySpark(Python)|예|예|예|예|예|예|예|예|
|Spark(Scala)|예|예|예|예|-|-|-|예|
|SparkSQL|예|예|-|-|-|-|-|-|
|.NET for Spark(C#)|예|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>도구 모음 단추를 사용하여 텍스트 셀 서식 지정

텍스트 셀 도구 모음의 서식 단추를 사용하여 일반적인 Markdown 작업을 수행할 수 있습니다. 여기에는 텍스트 굵게 표시, 텍스트에 기울임꼴 적용, 코드 조각 삽입, 순서가 지정되지 않은 목록 삽입, 순서가 지정된 목록 삽입, URL의 이미지 삽입이 포함됩니다.

  ![synapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>셀 작업 실행 취소
**실행 취소** 단추를 클릭하거나 **Ctrl+Z**를 눌러서 최근 셀 작업을 취소합니다. 이제 기록된 최근 셀 작업을 20개까지 실행 취소할 수 있습니다. 

   ![synapse-undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>셀 이동

줄임표(...)를 선택하면 오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스할 수 있습니다. 그런 다음, **위로 셀 이동** 또는 **아래로 셀 이동**을 선택하여 현재 셀을 이동합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 셀을 위로 이동하려면 **Ctrl+Alt+↑** 키를 누릅니다. 현재 셀을 아래로 이동하려면 **Ctrl+Alt+↓** 키를 누릅니다.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>셀 삭제

셀을 삭제하려면 줄임표(...)를 선택하여 오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스한 후 **셀 삭제**를 선택합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 행을 삭제하려면 **D,D**를 누릅니다.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>셀 입력 접기
현재 셀의 아래쪽에 있는 화살표 단추를 클릭하면 셀이 접힙니다. 셀을 펼치려면 셀이 접힌 상태에서 화살표 단추를 클릭합니다.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>셀 출력 접기

현재 셀 출력의 왼쪽 위에 있는 **collapse output**(출력 접기) 단추를 클릭하면 출력이 접힙니다. 출력을 펼치려면 셀 출력이 접힌 상태에서 **Show cell output**(셀 출력 표시)를 클릭합니다.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Notebook 실행

Notebook의 코드 셀을 개별적으로 또는 한꺼번에 실행할 수 있습니다. 각 셀의 상태와 진행률이 Notebook에 표시됩니다.

### <a name="run-a-cell"></a>셀 실행

셀에서 코드를 실행하는 방법은 여러 가지입니다.

1. 실행할 셀을 마우스로 가리킨 다음, **셀 실행** 단추를 선택하거나 **Ctrl+Enter** 키를 누릅니다.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스하려면 줄임표( **...** )를 선택합니다. 그런 다음, **셀 실행**을 선택합니다.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. **Shift+Enter** 키를 눌러서 현재 셀을 실행하고 아래 셀을 선택합니다. **Alt+Enter** 키를 눌러 현재 셀을 실행하고 아래에 새 셀을 삽입합니다.


### <a name="run-all-cells"></a>모든 셀 실행
**모두 실행** 단추를 클릭하면 현재 노트북의 모든 셀이 순서대로 실행됩니다.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>위 또는 아래 셀 모두 실행

오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스하려면 줄임표( **...** )를 선택합니다. 그런 다음, 현재 셀 위에 있는 모든 셀을 순서대로 실행하려면 **Run cells above**(위 셀 실행)를 선택합니다. 현재 셀 아래 모든 셀을 순서대로 실행하려면 **Run cells below**(아래 셀 실행)을 선택합니다.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>실행 중인 모든 셀 취소
실행 중인 셀 또는 큐에서 대기 중인 셀을 취소 하려면 **모두 취소** 단추를 클릭 합니다. 
   ![모두 취소-셀](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>셀 상태 표시기

셀 아래에 단계별 셀 실행 상태가 표시되어 현재 진행 상황을 확인할 수 있습니다. 셀 실행이 완료되면 총 지속 시간 및 종료 시간이 포함된 실행 요약이 표시되고 나중에 참조할 수 있도록 보관됩니다.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 진행률 표시기

Azure Synapse Studio Notebook은 순전히 Spark 기반입니다. 코드 셀은 Spark 풀에서 원격으로 실행됩니다. Spark 작업 진행률 표시기에 실시간 진행률 표시줄이 제공되어 작업 실행 상태를 이해하는 데 유용합니다.
각 작업 또는 단계 당 태스크 수를 통해 spark 작업의 병렬 수준을 식별할 수 있습니다. 또한 작업 (또는 단계) 이름에 대 한 링크를 클릭 하 여 특정 작업 (또는 단계)의 Spark UI를 자세히 살펴볼 수 있습니다.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 세션 구성

**세션 구성**에서 현재 Spark 세션에 제공할 실행기의 크기와 수, 제한 시간을 지정할 수 있습니다. Spark 세션을 다시 시작하면 구성 변경 사항이 적용됩니다. 캐시된 Notebook 변수는 모두 지워집니다.

[![세션 관리](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Spark 세션 구성 패널에서 spark 세션 추천를 사용할 수 있습니다. 세션 구성 패널에서 직접 spark 풀을 선택 하 고 사용 중인 노드 수와 사용 가능한 남은 실행자 수를 확인할 수 있습니다. 이러한 정보를 통해 세션 크기를 나중에 수정 하는 대신 적절 하 게 설정할 수 있습니다.

![세션-권장](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Notebook에 데이터 가져오기

아래 코드 샘플과 같이 Azure Blob Storage, Azure Data Lake Store Gen 2 및 SQL 풀에서 데이터를 로드할 수 있습니다.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2에서 Spark DataFrame으로 CSV 읽기

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage에서 Spark DataFrame으로 CSV 읽기

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>기본 스토리지 계정에서 데이터 읽기

기본 스토리지 계정의 데이터에 직접 액세스할 수 있습니다. 비밀 키를 제공할 필요가 없습니다. 데이터 탐색기에서 파일을 마우스 오른쪽 단추로 클릭하고 **새 Notebook**을 선택하여 데이터 추출기가 자동으로 생성된 새 Notebook을 확인합니다.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Notebook의 데이터 시각화

### <a name="produce-rendered-table-view"></a>렌더링 된 테이블 뷰 생성

테이블 형식 결과 뷰에는 가로 막대형 차트, 꺾은선형 차트, 원형 차트, 분산형 차트, 영역 차트를 만드는 옵션이 제공됩니다. 코드를 작성하지 않고도 데이터를 시각화할 수 있습니다. 차트는 **차트 옵션**에서 사용자 지정할 수 있습니다. 

**%%sql** 매직 명령의 출력은 기본적으로 렌더링된 테이블 뷰에 표시됩니다. <code>display(df)</code>Spark 데이터 프레임 또는 RDD (복원 력 있는 분산 데이터 집합) 함수에서를 호출 하 여 렌더링 된 테이블 뷰를 생성할 수 있습니다.

   [![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>대규모 데이터 집합에서 기본 제공 차트 시각화 

기본적으로 <code>display(df)</code> 함수는 차트를 렌더링 하기 위해 데이터의 처음 1000 행만 사용 합니다. **모든 결과에 대 한 집계** 를 확인 하 고 **적용** 단추를 클릭 하면 전체 데이터 집합에서 차트 생성을 적용 합니다. Spark 작업은 차트 설정이 변경 될 때 트리거됩니다. 계산을 완료 하 고 차트를 렌더링 하는 데 시간이 걸립니다. 
    [![기본 제공 차트-집계-모두](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>데이터 통계 정보 시각화
를 사용 하 여 열 <code>display(df, summary = true)</code> 이름, 열 유형, 고유 값 및 각 열에 대 한 누락 값을 포함 하는 지정 된 Spark 데이터 프레임의 통계 요약을 확인할 수 있습니다. 특정 열을 선택 하 여 최소 값, 최대값, 평균 값 및 표준 편차를 볼 수도 있습니다.
    [![기본 제공 차트-요약 ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>HTML 또는 대화형 라이브러리 렌더링

**displayHTML()** 을 사용하여 **빛망울**과 같은 대화형 라이브러리나 HTML을 렌더링할 수 있습니다.

다음 이미지는 **빛망울**을 사용하여 지도 위에 문자 모양을 그리는 예입니다.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

위의 이미지를 그리려면 다음 샘플 코드를 실행합니다.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Notebook 저장

작업 영역에 단일 Notebook 또는 모든 Notebook을 저장할 수 있습니다.

1. 단일 Notebook에 변경한 내용을 저장하려면 노트북 명령 모음에서 **게시** 단추를 선택합니다.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 작업 영역에 모든 노트북을 저장하려면 작업 영역 명령 모음에서 **모두 게시** 단추를 선택합니다. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Notebook 속성에서 저장할 때 셀 출력을 포함할지 여부를 구성할 수 있습니다.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>매직 명령
Azure Synapse Studio 노트북에서 익숙한 Jupyter 매직 명령을 사용할 수 있습니다. 현재 사용 가능한 매직 명령은 아래 목록을 참조하세요. 필요에 맞는 매직 명령을 계속 빌드할 수 있도록 GitHub에 사용 사례를 알려주세요.

사용 가능한 줄 매직: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time),% [time it](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

사용 가능한 셀 매직: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>노트북 오케스트레이션

### <a name="add-a-notebook-to-a-pipeline"></a>파이프라인에 노트북 추가

오른쪽 위 모서리에서 **파이프라인에 추가** 단추를 클릭 하 여 기존 파이프라인에 노트북을 추가 하거나 새 파이프라인을 만듭니다.

![파이프라인 추가](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>매개 변수 지정 셀

노트북을 매개 변수화 하려면 맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스 하려면 줄임표 (...)를 선택 합니다. 그런 다음 매개 변수 셀 **설정/해제** 를 선택 하 여 셀을 매개 변수 셀로 지정 합니다.

![토글 매개 변수](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory는 매개 변수 셀을 찾고 실행 시 전달 되는 매개 변수의 기본값으로이 셀을 처리 합니다. 실행 엔진은 기본값을 덮어쓰기 위해 입력 매개 변수를 사용 하 여 매개 변수 셀 아래에 새 셀을 추가 합니다. 매개 변수를 지정 하지 않으면 삽입 된 셀이 노트북 맨 위에 삽입 됩니다.

### <a name="assign-parameters-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수가 포함 된 노트북을 만든 후에는 Azure Synapse 노트북 활동을 사용 하 여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가한 후에는 **설정** 탭의 **기본 매개 변수** 섹션에서 매개 변수 값을 설정할 수 있습니다. 

![assign-매개 변수](./media/apache-spark-development-using-notebooks/assign-parameter.png)

매개 변수 값을 할당 하는 경우 [파이프라인 식 언어](../../data-factory/control-flow-expression-language-functions.md) 또는 [시스템 변수](../../data-factory/control-flow-system-variables.md)를 사용할 수 있습니다.



## <a name="shortcut-keys"></a>바로 가기 키

Jupyter 노트북과 마찬가지로 Azure Synapse Studio Notebook에는 모달 사용자 인터페이스가 있습니다. 키보드는 Notebook 셀이 있는 모드에 따라 다른 작업을 수행합니다. Synapse Studio Notebook은 지정된 코드 셀에 대해 두 가지 모드(명령 모드 및 편집 모드)를 지원합니다.

1. 입력하라는 텍스트 커서가 없으면 셀은 명령 모드에 있습니다. 셀이 명령 모드에 있으면 Notebook을 전체적으로 편집할 수 있지만 개별 셀에는 입력할 수 없습니다. `ESC` 키를 누르거나 마우스를 사용하여 셀의 편집기 영역 외부를 클릭하면 명령 모드로 전환됩니다.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 편집 모드에서는 편집기 영역에 입력하라는 텍스트 커서가 표시됩니다. 셀이 편집 모드에 있는 경우 셀에 입력할 수 있습니다. `Enter` 키를 누르거나 마우스를 사용하여 셀의 편집기 영역을 클릭하면 편집 모드로 전환됩니다.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>명령 모드의 바로 가기 키

다음과 같은 키 입력 바로 가기를 사용하면 Azure Synapse Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 작업 |Synapse Studio Notebook 바로 가기  |
|--|--|
|현재 셀을 실행하고 아래 선택 | Shift+Enter |
|현재 셀을 실행하고 아래에 삽입 | Alt+Enter |
|위 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|위에 셀 삽입| A |
|아래에 셀 삽입| b |
|선택한 셀을 위로 확장| Shift+위쪽 화살표 |
|선택한 셀을 아래로 확장| Shift+아래쪽 화살표|
|위로 셀 이동| Ctrl+Alt+↑ |
|아래로 셀 이동| Ctrl+Alt+↓ |
|선택한 셀 삭제| D, D |
|편집 모드로 전환| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>편집 모드의 바로 가기 키

편집 모드에서 다음과 같은 키 입력 바로 가기를 사용하면 Azure Synapse Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 작업 |Synapse Studio Notebook 바로 가기  |
|--|--|
|커서를 위로 이동 | 위로 |
|커서를 아래로 이동|아래로|
|실행 취소|Ctrl + Z|
|다시 실행|Ctrl + Y|
|주석 처리/주석 처리 제거|Ctrl + /|
|이전 단어 삭제|Ctrl + Backspace|
|다음 단어 삭제|Ctrl + Delete|
|셀 시작으로 이동|Ctrl + Home|
|셀 끝으로 이동 |Ctrl + End|
|한 단어 왼쪽으로 이동|Ctrl + Left|
|한 단어 오른쪽으로 이동|Ctrl + 오른쪽 화살표|
|모두 선택|Ctrl + A|
|들여쓰기| Ctrl + ]|
|내어쓰기|Ctrl + [|
|명령 모드로 전환| Esc |

## <a name="next-steps"></a>다음 단계
- [Synapse 샘플 노트북 확인](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [빠른 시작: 웹 도구를 사용하여 Azure Synapse Analytics에서 Apache Spark 풀(미리 보기) 만들기](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics의 Apache Spark란?](apache-spark-overview.md)
- [Azure Synapse Analytics를 사용하여 Apache Spark에 .NET 사용](spark-dotnet.md)
- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
