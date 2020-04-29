---
title: Azure Synapse Studio (미리 보기) 노트북 만들기, 개발 및 유지 관리
description: 이 문서에서는 데이터 준비 및 시각화를 수행 하기 위해 Azure Synapse Studio (미리 보기) 노트북을 만들고 개발 하는 방법에 대해 알아봅니다.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430228"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Azure Synapse Studio (미리 보기) 노트북 만들기, 개발 및 유지 관리

Azure Synapse Studio (미리 보기) 노트북은 라이브 코드, 시각화 및 설명을 포함 하는 파일을 만들 수 있는 웹 인터페이스입니다. 노트북은 아이디어의 유효성을 검사 하 고 빠른 실험을 사용 하 여 데이터에서 통찰력을 얻을 수 있는 좋은 장소입니다. 또한 노트북은 데이터 준비, 데이터 시각화, 기계 학습 및 기타 빅 데이터 시나리오에서 널리 사용 됩니다.

Azure Synapse Studio 노트북을 사용 하 여 다음을 수행할 수 있습니다.

* 설정 작업을 0으로 시작 하세요.
* 기본 제공 엔터프라이즈 보안 기능을 통해 데이터를 안전 하 게 유지 합니다.
* 데이터를 원시 형식 (CSV, txt, JSON 등), 처리 된 파일 형식 (parquet, 델타 Lake, ORC 등) 및 Spark 및 SQL에 대 한 SQL 테이블 형식 데이터 파일을 분석 합니다.
* 향상 된 제작 기능 및 기본 제공 데이터 시각화를 통해 생산성을 높일 수 있습니다.

이 문서에서는 Azure Synapse Studio에서 노트북을 사용 하는 방법을 설명 합니다.

## <a name="create-a-notebook"></a>Notebook 만들기

노트북을 만드는 방법에는 두 가지가 있습니다. **개체 탐색기**에서 새 노트북을 만들거나 Azure Synapse 작업 영역으로 기존 노트북을 가져올 수 있습니다. Azure Synapse Studio 노트북은 표준 Jupyter Notebook IPYNB 파일을 인식할 수 있습니다.

![synapse-가져오기-전자 필기장](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Notebook 개발

노트북은 독립적으로 또는 그룹으로 실행할 수 있는 개별 코드 블록 또는 텍스트의 셀로 구성 됩니다.

### <a name="add-a-cell"></a>셀 추가

여러 가지 방법으로 노트북에 새 셀을 추가할 수 있습니다.

1. 왼쪽 위 **+ 셀** 단추를 확장 하 고 **코드 셀 추가** 또는 **텍스트 셀 추가**를 선택 합니다.

    ![셀 추가-셀-단추](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 두 셀 사이의 공간을 마우스로 가리키고 **코드 추가** 또는 **텍스트 추가**를 선택 합니다.

    ![셀 추가-공간 간](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용 합니다. **A** 를 눌러 현재 셀 위에 셀을 삽입 합니다. **B** 키를 눌러 현재 셀 아래에 셀을 삽입 합니다.

### <a name="set-a-primary-language"></a>주 언어 설정

Azure Synapse Studio 노트북은 네 가지 spark 언어를 지원 합니다.

* pyspark (python)
* spark (Scala)
* sparkSQL
* Spark.NET (c #)

위쪽 명령 모음의 드롭다운 목록에서 추가 된 새 셀의 주 언어를 설정할 수 있습니다.

   ![synapse-언어](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>여러 언어 사용

셀 시작 부분에 올바른 언어 매직 명령을 지정 하 여 한 노트북에서 여러 언어를 사용할 수 있습니다. 다음 표에서는 셀 언어를 전환 하는 매직 명령을 보여 줍니다.

|매직 명령 |언어 | Description |  
|---|------|-----|
|%% pyspark| Python | Spark 컨텍스트에 대해 **Python** 쿼리를 실행 합니다.  |
|%% spark| 스칼라 | Spark 컨텍스트에 대해 **Scala** 쿼리를 실행 합니다.  |  
|%% sql| SparkSQL | Spark 컨텍스트에 대해 **SparkSQL** 쿼리를 실행 합니다.  |
|%% csharp | Spark.NET C # | Spark 컨텍스트에 대해 **Spark.NET c #** 쿼리를 실행 합니다. |

다음 그림은 **%% PySpark** magic 명령 또는 **Spark (Scala)** 노트북에서 **%% sql** 매직 명령이 있는 SparkSQL 쿼리를 사용 하 여 PySpark 쿼리를 작성 하는 방법의 예입니다. 노트북의 주 언어가 Scala로 설정 되어 있습니다.

   ![synapse-매직](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>임시 테이블을 사용 하 여 여러 언어에서 데이터 참조

Synapse Studio 노트북의 여러 언어에서 직접 데이터 또는 변수를 참조할 수 없습니다. Spark에서는 여러 언어에서 임시 테이블을 참조할 수 있습니다. 다음은 `Scala` 데이터 프레임에서 `PySpark` 를 읽고 Spark 임시 테이블을 `SparkSQL` 사용 하 여 해결 하는 방법의 예입니다.

1. 1 셀에서 Scala을 사용 하 여 SQL 풀 커넥터에서 데이터 프레임를 읽고 임시 테이블을 만듭니다.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 2 셀에서 Spark SQL을 사용 하 여 데이터를 쿼리 합니다.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 3 셀에서 PySpark의 데이터를 사용 합니다.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 스타일 IntelliSense

Azure Synapse Studio 노트북은 모나코 편집기와 통합 되어 IDE 스타일 IntelliSense를 셀 편집기로 가져옵니다. 구문 강조 표시, 오류 작성자 및 자동 코드 완성을 통해 코드를 작성 하 고 문제를 더 빠르게 식별할 수 있습니다.

IntelliSense 기능은 언어에 따라 다양 한 수준의 완성도를 갖습니다. 아래 표를 사용 하 여 지원 되는 항목을 확인 하세요.

|언어| 구문 강조 표시 | 구문 오류 표식  | 구문 코드 완성 | 변수 코드 완성| 시스템 함수 코드 완성| 사용자 함수 코드 완성| 스마트 들여쓰기 | 코드 접기|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|예|예|예|예|예|예|예|예|
|Spark (Scala)|예|예|예|예|-|-|-|예|
|SparkSQL|예|예|-|-|-|-|-|-|
|Spark.NET (c #)|예|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>도구 모음 단추를 사용 하 여 텍스트 셀 서식 지정

텍스트 셀 도구 모음의 서식 단추를 사용 하 여 일반적인 markdown 작업을 수행할 수 있습니다. 여기에는 굵은 텍스트, italicizing 텍스트, 코드 조각 삽입, 순서가 지정 되지 않은 목록 삽입, 정렬 된 목록 삽입 및 URL의 이미지 삽입이 포함 됩니다.

  ![synapse--셀 도구 모음](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>셀 작업 실행 취소
**실행 취소** 단추를 클릭 하거나 **Ctrl + Z** 를 눌러 가장 최근 셀 작업을 취소 합니다. 이제 최신 20 개의 기록 셀 작업을 실행 취소할 수 있습니다. 

   ![synapse-셀](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>셀 이동

맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스 하려면 줄임표 (...)를 선택 합니다. 그런 다음 **셀 위로** 이동 또는 **아래로 셀 이동** 을 선택 하 여 현재 셀을 이동 합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. **Ctrl + Alt + ↑** 를 눌러 현재 셀을 위로 이동 합니다. **Ctrl + Alt + ↓** 를 눌러 현재 셀을 아래로 이동 합니다.

   ![셀 이동](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>셀 삭제

셀을 삭제 하려면 줄임표 (...)를 선택 하 여 맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스 한 다음 **셀 삭제**를 선택 합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. **D, d** 를 눌러 현재 셀을 삭제 합니다.
  
   ![셀 삭제](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>셀 입력 축소
현재 셀의 아래쪽에 있는 화살표 단추를 클릭 하 여 축소 합니다. 확장 하려면 셀이 축소 된 상태에서 화살표 단추를 클릭 합니다.

   ![셀 축소-입력](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>셀 출력 축소

현재 셀 출력의 왼쪽 위에 있는 **출력 축소** 단추를 클릭 하 여 축소 합니다. 확장 하려면 셀 출력이 축소 된 상태에서 **셀 출력 표시** 를 클릭 합니다.

   ![셀 축소-출력](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Notebook 실행

노트북의 코드 셀을 개별적으로 또는 한꺼번에 실행할 수 있습니다. 각 셀의 상태와 진행률이 노트북에 표시 됩니다.

### <a name="run-a-cell"></a>셀 실행

셀에서 코드를 실행 하는 방법에는 여러 가지가 있습니다.

1. 실행 하려는 셀을 마우스로 가리키고 **셀 실행** 단추를 선택 하거나 **ctrl + enter**를 누릅니다.

   ![실행 셀-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스 하려면 줄임표 (**...**)를 선택 합니다. 그런 다음 **셀 실행**을 선택 합니다.

   ![실행 셀-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용 합니다. **Shift + enter** 를 눌러 현재 셀을 실행 하 고 아래 셀을 선택 합니다. **Alt + enter** 를 눌러 현재 셀을 실행 하 고 아래에 새 셀을 삽입 합니다.


### <a name="run-all-cells"></a>모든 셀 실행
**모두 실행** 단추를 클릭 하 여 현재 노트북의 모든 셀을 순서 대로 실행 합니다.

   ![모두 셀 실행](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>위 또는 아래에 있는 모든 셀 실행

맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스 하려면 줄임표 (**...**)를 선택 합니다. 그런 다음 **위의 셀 실행** 을 선택 하 여 현재 범위 위에 있는 모든 셀을 순서 대로 실행 합니다. **아래 셀 실행** 을 선택 하 여 현재의 모든 셀을 순서 대로 실행 합니다.

   ![실행 셀-위 또는 아래](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>셀 상태 표시기

현재 진행 상황을 확인 하는 데 도움이 되는 단계별 셀 실행 상태를 셀 아래에 표시 합니다. 셀 실행이 완료 되 면 총 기간과 종료 시간이 포함 된 실행 요약이 표시 되 고 나중에 참조할 수 있도록 보관 됩니다.

![셀 상태](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 진행률 표시기

Azure Synapse Studio 노트북은 전적으로 Spark를 기반으로 합니다. 코드 셀은 Spark 풀에서 원격으로 실행 됩니다. 작업 실행 상태를 이해 하는 데 도움이 되는 실시간 진행률 표시줄이 포함 된 Spark 작업 진행률 표시기가 표시 됩니다.


![spark-진행률-표시기](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 세션 구성

**세션 구성**에서 현재 Spark 세션에 제공할 제한 시간, 번호 및 실행자 크기를 지정할 수 있습니다. 구성 변경 내용을 적용 하려면 Spark 세션을 다시 시작 합니다. 모든 캐시 된 노트북 변수가 지워집니다.

![세션 관리](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>전자 필기장에 데이터 가져오기

아래 코드 샘플에 표시 된 것 처럼 Azure Blob Storage, Azure Data Lake Store Gen 2 및 SQL 풀에서 데이터를 로드할 수 있습니다.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2에서 Spark 데이터 프레임로 CSV 읽기

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage에서 Spark 데이터 프레임 CSV 읽기

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

### <a name="read-data-from-the-primary-storage-account"></a>기본 저장소 계정에서 데이터 읽기

기본 저장소 계정의 데이터에 직접 액세스할 수 있습니다. 비밀 키를 제공할 필요가 없습니다. 데이터 탐색기에서 파일을 마우스 오른쪽 단추로 클릭 하 고 **새 노트북** 을 선택 하 여 데이터 추출기가 자동으로 생성 된 새 노트북을 확인 합니다.

![데이터-셀](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>노트북에서 데이터 시각화

### <a name="display"></a>Display ()

표 형식 결과 뷰는 가로 막대형 차트, 꺾은선형 차트, 원형 차트, 분산형 차트 및 영역형 차트를 만드는 옵션과 함께 제공 됩니다. 코드를 작성 하지 않고도 데이터를 시각화할 수 있습니다. **차트 옵션**에서 차트를 사용자 지정할 수 있습니다. 

**%% Sql** 매직 명령의 출력은 기본적으로 렌더링 된 테이블 뷰에 표시 됩니다. Spark 데이터 프레임 또는 rdd (복원 력 있는 분산 데이터 집합) 함수에서 **display (`<DataFrame name>`)** 를 호출 하 여 렌더링 된 테이블 뷰를 생성할 수 있습니다.

   ![기본 제공 차트](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML ()

**Displayhtml ()** 을 사용 하 여 **빛 망 울**와 같은 HTML 또는 대화형 라이브러리를 렌더링할 수 있습니다.

다음 이미지는 **빛 망 울**를 사용 하 여 지도에 대 한 문자 모양을 그리는 예입니다.

   ![bokeh-예제](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

다음 샘플 코드를 실행 하 여 위에 이미지를 그립니다.

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

## <a name="save-notebooks"></a>노트북 저장

작업 영역에 단일 노트북 또는 모든 노트북을 저장할 수 있습니다.

1. 단일 노트북에 대 한 변경 내용을 저장 하려면 노트북 명령 모음에서 **게시** 단추를 선택 합니다.

   ![게시-노트북](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 작업 영역에 모든 노트북을 저장 하려면 작업 영역 명령 모음에서 **모두 게시** 단추를 선택 합니다. 

   ![게시-모두](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

노트북 속성에서 저장할 때 셀 출력을 포함할지 여부를 구성할 수 있습니다.

   ![노트북-속성](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>매직 명령
Azure Synapse Studio 노트북에서 친숙 한 Jupyter 매직 명령을 사용할 수 있습니다. 아래 목록을 현재 사용 가능한 매직 명령으로 확인 합니다. 사용자의 요구를 충족 하기 위해 더 많은 매직 명령을 계속 빌드할 수 있도록 GitHub에서 사용 사례를 알려 주세요.

사용 가능한 줄 매직: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

사용 가능한 셀 매직: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% sql](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% spark](#use-multiple-languages), [%% csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>바로 가기 키

Jupyter 노트북과 마찬가지로 Azure Synapse Studio 노트북에는 모달 사용자 인터페이스가 있습니다. 키보드는 노트북 셀이 있는 모드에 따라 다른 작업을 수행 합니다. Synapse Studio 노트북은 지정 된 코드 셀에 대해 명령 모드 및 편집 모드의 두 가지 모드를 지원 합니다.

1. 입력 하 라는 텍스트 커서가 없는 경우 셀은 명령 모드에 있습니다. 셀이 명령 모드일 때 개별 셀에 입력할 수 있는 것이 아니라 전체 노트북을 편집할 수 있습니다. 마우스를 누르거나 마우스를 `ESC` 사용 하 여 셀의 편집기 영역 바깥쪽을 클릭 하 여 명령 모드를 입력 합니다.

   ![명령 모드](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 편집기 영역을 입력 하 라는 텍스트 커서로 편집 모드가 표시 됩니다. 셀이 편집 모드에 있는 경우 셀에 입력할 수 없습니다. 마우스를 누르거나 `Enter` 마우스를 사용 하 여 셀의 편집기 영역을 클릭 하 여 편집 모드를 시작 합니다.
   
   ![편집 모드](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>명령 모드의 바로 가기 키

다음 키 입력 바로 가기를 사용 하 여 Azure Synapse 노트북에서 코드를 보다 쉽게 탐색 하 고 실행할 수 있습니다.

| 작업 |Synapse Studio 노트북 바로 가기  |
|--|--|
|현재 셀을 실행 하 고 아래를 선택 합니다. | Shift+Enter |
|현재 셀을 실행 하 고 아래에 삽입 합니다. | Alt+Enter |
|위의 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|위에 셀 삽입| A |
|아래에 셀 삽입| b |
|위에서 선택한 셀 확장| Shift+위쪽 화살표 |
|선택한 셀 확장| Shift+아래쪽 화살표|
|위로 셀 이동| Ctrl + Alt + ↑ |
|아래로 셀 이동| Ctrl + Alt + ↓ |
|선택한 셀 삭제| D, D |
|편집 모드로 전환| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>편집 모드의 바로 가기 키

다음 키 입력 바로 가기를 사용 하 여 편집 모드에 있는 경우 Azure Synapse 노트북에서 코드를 보다 쉽게 탐색 하 고 실행할 수 있습니다.

| 작업 |Synapse Studio 노트북 바로 가기  |
|--|--|
|커서를 위로 이동 | 위로 |
|커서를 아래로 이동|아래로|
|실행 취소|Ctrl + Z|
|다시 실행|Ctrl + Y|
|주석 처리/주석 처리 제거|Ctrl +/|
|이전 단어 삭제|Ctrl + 백스페이스|
|다음 단어 삭제|Ctrl + Delete|
|셀 시작으로 이동|Ctrl + Home|
|셀 끝으로 이동 |Ctrl + End|
|한 단어 왼쪽으로 이동|Ctrl + Left|
|한 단어 오른쪽으로 이동|Ctrl + Right|
|모두 선택|Ctrl + A|
|들여쓰기| Ctrl +]|
|Dedent|Ctrl + [|
|명령 모드로 전환| Esc |

## <a name="next-steps"></a>다음 단계

- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
