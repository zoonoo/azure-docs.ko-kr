---
title: Azure 시냅스 스튜디오(미리 보기) 노트북 생성, 개발 및 유지 관리
description: 이 문서에서는 Azure Synapse Studio(미리 보기) 노트북을 만들고 개발하여 데이터 준비 및 시각화를 수행하는 방법을 배웁니다.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430228"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Azure 시냅스 스튜디오(미리 보기) 노트북 생성, 개발 및 유지 관리

Azure Synapse Studio(미리 보기) 노트북은 라이브 코드, 시각화 및 내러티브 텍스트가 포함된 파일을 만들 수 있는 웹 인터페이스입니다. 노트북은 아이디어를 검증하고 빠른 실험을 사용하여 데이터에서 통찰력을 얻을 수 있는 좋은 장소입니다. 노트북은 데이터 준비, 데이터 시각화, 기계 학습 및 기타 빅 데이터 시나리오에도 널리 사용됩니다.

Azure 시냅스 스튜디오 노트북을 사용하면 다음을 수행할 수 있습니다.

* 제로 설치 노력으로 시작하십시오.
* 내장된 엔터프라이즈 보안 기능으로 데이터를 안전하게 보호합니다.
* Spark 및 SQL에 대한 원시 형식(CSV, txt, JSON 등), 처리된 파일 형식(마루, 델타 레이크, ORC 등) 및 SQL 테이블 형식 데이터 파일에서 데이터를 분석합니다.
* 향상된 작성 기능과 기본 제공 데이터 시각화를 통해 생산성을 높일 수 있습니다.

이 문서에서는 Azure Synapse Studio에서 전자 필기장을 사용하는 방법에 대해 설명합니다.

## <a name="create-a-notebook"></a>Notebook 만들기

전자 필기장을 만드는 방법에는 두 가지가 있습니다. 새 전자 필기장을 만들거나 **개체 탐색기에서**Azure Synapse 작업 영역으로 기존 전자 필기장을 가져올 수 있습니다. Azure 시냅스 스튜디오 노트북은 표준 주피터 노트북 IPYNB 파일을 인식할 수 있습니다.

![시냅스- 가져오기-노트북 만들기](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Notebook 개발

전자 필기전자는 독립적으로 또는 그룹으로 실행될 수 있는 개별 코드 또는 텍스트 블록인 셀로 구성됩니다.

### <a name="add-a-cell"></a>셀 추가

전자 필기장에 새 셀을 추가하는 방법에는 여러 가지가 있습니다.

1. 왼쪽 위 **+ 셀** 단추를 확장하고 **코드 셀 추가** 또는 텍스트 셀 **추가를**선택합니다.

    ![셀이 있는 추가 셀 버튼](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 두 셀 사이의 공백 위로 마우스를 가져가고 **코드 추가** 또는 **텍스트 추가를**선택합니다.

    ![공간 간 추가 셀](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [명령 모드에서 바로 가기 키를](#shortcut-keys-under-command-mode)사용합니다. **A를** 눌러 현재 셀 위에 셀을 삽입합니다. **B를** 눌러 현재 셀 아래에 셀을 삽입합니다.

### <a name="set-a-primary-language"></a>기본 언어 설정

Azure Synapse Studio 전자 필기전자는 다음 네 가지 스파크 언어를 지원합니다.

* 파이스파크 (파이썬)
* 스파크 (스칼라)
* 스파크SQL
* Spark.NET (C #)

위쪽 명령 모음의 드롭다운 목록에서 새로 추가된 셀의 기본 언어를 설정할 수 있습니다.

   ![기본 시냅스 언어](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>여러 언어 사용

셀의 시작 부분에 올바른 언어 매직 명령을 지정하여 하나의 전자 필기장에서 여러 언어를 사용할 수 있습니다. 다음 표에는 셀 언어를 전환하는 magic 명령이 나열되어 있습니다.

|매직 커맨드 |언어 | Description |  
|---|------|-----|
|%%파이스파크| Python | 스파크 컨텍스트에 대해 **파이썬** 쿼리를 실행합니다.  |
|%%스파크| 스칼라 | 스파크 컨텍스트에 대해 **스칼라** 쿼리를 실행합니다.  |  
|%%sql| 스파크SQL | 스파크 컨텍스트에 대해 **SparkSQL** 쿼리를 실행합니다.  |
|%%c샤프 | Spark.NET C # | Spark 컨텍스트에 대해 **Spark.NET C#** 쿼리를 실행합니다. |

다음 이미지는 **%%pyspark** 매직 명령 또는 SparkSQL 쿼리를 사용하여 Pypark 쿼리를 작성하는 방법의 예이며 **Spark(스칼라)** 노트북에서 **%%sql** magic 명령을 사용합니다. 전자 필기전자의 기본 언어는 스칼라로 설정되어 있습니다.

   ![시냅스 스파크 매직](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>임시 테이블을 사용하여 언어 간 데이터 참조

Synapse Studio 전자 필기전자에서는 여러 언어에서 직접 데이터 또는 변수를 참조할 수 없습니다. Spark에서 임시 테이블을 여러 언어로 참조할 수 있습니다. 다음은 `Scala` Spark 임시 테이블을 해결 방법으로 `PySpark` `SparkSQL` 사용하여 DataFrame을 읽는 방법의 예입니다.

1. 셀 1에서 스칼라를 사용하여 SQL 풀 커넥터의 DataFrame을 읽고 임시 테이블을 만듭니다.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 셀 2에서 Spark SQL을 사용하여 데이터를 쿼리합니다.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 셀 3에서 PySpark의 데이터를 사용합니다.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 스타일 인텔리센스

Azure Synapse Studio 노트북은 모나코 편집기와 통합되어 IDE 스타일 IntelliSense를 셀 편집기로 가져옵니다. 구문 강조 표시, 오류 작성기 및 자동 코드 완성을 통해 코드를 작성하고 문제를 더 빠르게 식별할 수 있습니다.

IntelliSense 기능은 다른 언어에 대한 성숙도의 다른 수준에 있습니다. 아래 표를 사용하여 지원되는 내용을 확인합니다.

|언어| 구문 강조 표시 | 구문 오류 마커  | 구문 코드 완료 | 가변 코드 완료| 시스템 기능 코드 완료| 사용자 기능 코드 완료| 스마트 들여쓰기 | 코드 접기|
|--|--|--|--|--|--|--|--|--|
|파이스파크 (파이썬)|예|예|예|예|예|예|예|예|
|스파크 (스칼라)|예|예|예|예|-|-|-|예|
|스파크SQL|예|예|-|-|-|-|-|-|
|Spark.NET (C #)|예|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>도구 모음 단추로 텍스트 셀 서식 지정

텍스트 셀 도구 모음의 형식 단추를 사용하여 일반적인 마크다운 작업을 수행할 수 있습니다. 여기에는 굵게 표시된 텍스트, 기울임꼴 텍스트 삽입, 코드 조각 삽입, 정렬되지 않은 목록 삽입, 정렬된 목록 삽입 및 URL에서 이미지 삽입이 포함됩니다.

  ![시냅스 텍스트 셀 도구 모음](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>셀 작업 취소
**취소** 단추를 클릭하거나 **Ctrl+Z를** 눌러 최신 셀 작업을 취소합니다. 이제 최신 20개의 기록 셀 작업을 취소할 수 있습니다. 

   ![시냅스 언도 셀](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>셀 이동

타원(...)을 선택하여 맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스합니다. 그런 다음 **셀 위로 이동하거나** **셀을 아래로 이동하여** 현재 셀을 이동합니다. 

명령 모드에서 [바로 가기 키를](#shortcut-keys-under-command-mode)사용할 수도 있습니다. **Ctrl+Alt+↑** 를 눌러 현재 셀을 위로 이동합니다. **Ctrl+Alt+↓를** 눌러 현재 셀을 아래로 이동합니다.

   ![셀 이동](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>셀 삭제

셀을 삭제하려면 타원(...)을 선택하여 맨 오른쪽의 추가 셀 작업 메뉴에 액세스한 다음 **셀 삭제를**선택합니다. 

명령 모드에서 [바로 가기 키를](#shortcut-keys-under-command-mode)사용할 수도 있습니다. **D,D를** 눌러 현재 셀을 삭제합니다.
  
   ![셀 삭제](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>셀 입력 축소
현재 셀 하단의 화살표 버튼을 클릭하여 축소합니다. 확장하려면 셀이 축소되는 동안 화살표 단추를 클릭합니다.

   ![축소 셀 입력](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>셀 출력 축소

현재 셀 **출력의** 왼쪽 상단에 있는 축소 출력 단추를 클릭하여 축소합니다. 확장하려면 셀 출력이 축소되는 동안 **셀 출력 표시를** 클릭합니다.

   ![축소 셀 출력](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>노트북 실행

노트북에서 코드 셀을 개별적으로 또는 한 번에 실행할 수 있습니다. 각 셀의 상태 및 진행 상태가 전자 필기장에 표시됩니다.

### <a name="run-a-cell"></a>셀 실행

셀에서 코드를 실행하는 방법에는 여러 가지가 있습니다.

1. 실행하려는 셀을 마우스로 가리키고 **셀 실행** 단추를 선택하거나 **Ctrl+Enter 를 누릅니다.**

   ![런 셀-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스하려면**타원(...**)을 선택합니다. 그런 다음 **셀 실행을**선택합니다.

   ![런 셀-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. [명령 모드에서 바로 가기 키를](#shortcut-keys-under-command-mode)사용합니다. **Shift+Enter를** 눌러 현재 셀을 실행하고 아래 셀을 선택합니다. **Alt+Enter를** 눌러 현재 셀을 실행하고 아래에 새 셀을 삽입합니다.


### <a name="run-all-cells"></a>모든 셀 실행
모두 **실행** 단추를 클릭하여 현재 전자 필기의 모든 셀을 순서대로 실행합니다.

   ![실행-모든 셀](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>위 또는 아래의 모든 셀 을 실행합니다.

맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스하려면**타원(...**)을 선택합니다. 그런 다음 **위의 셀 실행을** 선택하여 현재 위의 모든 셀을 순서대로 실행합니다. **아래 셀 실행을** 선택하여 현재 아래의 모든 셀을 순서대로 실행합니다.

   ![런 셀 위 또는 아래](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>셀 상태 표시기

현재 진행 상황을 볼 수 있도록 셀 아래에 단계별 셀 실행 상태가 표시됩니다. 셀 실행이 완료되면 총 기간 및 종료 시간이 포함된 실행 요약이 표시되고 나중에 참조할 수 있습니다.

![셀 상태](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>스파크 진행 표시기

Azure 시냅스 스튜디오 노트북은 순전히 스파크 기반입니다. 코드 셀은 Spark 풀에서 원격으로 실행됩니다. Spark 작업 진행률 표시기는 작업 실행 상태를 이해하는 데 도움이 되는 실시간 진행률 표시줄과 함께 제공됩니다.


![스파크 진행 표시기](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>스파크 세션 구성

**구성 세션에서**현재 Spark 세션에 제공할 시간 초과 시간, 수 및 실행기 크기를 지정할 수 있습니다. Spark 세션을 다시 시작하면 구성 변경 사항이 적용됩니다. 캐시된 모든 전자 필기장 변수가 지워집니다.

![세션-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>노트북에 데이터 가져오기

아래 코드 샘플과 같이 Azure Blob 저장소, Azure Data Lake 스토어 Gen 2 및 SQL 풀에서 데이터를 로드할 수 있습니다.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure 데이터 레이크 스토어 Gen2에서 CSV를 스파크 데이터프레임으로 읽기

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob 저장소에서 CSV를 스파크 데이터프레임으로 읽기

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

기본 저장소 계정의 데이터에 직접 액세스할 수 있습니다. 비밀 키를 제공할 필요가 없습니다. 데이터 탐색기에서 파일을 마우스 오른쪽 단추로 클릭하고 **새 전자 필기를** 선택하여 데이터 추출기가 자동으로 생성된 새 전자 필기장을 확인합니다.

![데이터-셀](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>노트북에서 데이터 시각화

### <a name="display"></a>디스플레이()

테이블 형식의 결과 보기에는 막대형 차트, 선급차트, 원형 차트, 분산형 차트 및 영역 형 차트를 만드는 옵션이 제공됩니다. 코드를 작성하지 않고도 데이터를 시각화할 수 있습니다. 차트는 **차트 옵션에서**사용자 지정할 수 있습니다. 

**%%sql** magic 명령의 출력은 기본적으로 렌더링된 테이블 보기에 나타납니다. Spark DataFrames 또는 복원력 있는 분산 데이터 집합(RDD) 함수에서 **display()를`<DataFrame name>`** 호출하여 렌더링된 테이블 뷰를 생성할 수 있습니다.

   ![기본 제공 차트](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>디스플레이HTML()

**디스플레이HTML()** 을 사용하여 **보케와**같은 HTML 또는 대화형 라이브러리를 렌더링할 수 있습니다.

다음 이미지는 **보케를**사용하여 맵 위에 글리프를 플로팅하는 예입니다.

   ![보케 예](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

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

## <a name="save-notebooks"></a>노트북 저장

단일 전자 필기장 또는 모든 전자 필기장을 작업 공간에 저장할 수 있습니다.

1. 단일 전자 필기장에 대한 변경 내용을 저장하려면 전자 필기장 명령 모음에서 **게시** 단추를 선택합니다.

   ![게시 전자 필기장](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 작업 영역에 있는 모든 전자 필기장을 저장하려면 작업 영역 명령 모음에서 **모든** 전자 필기 단추를 선택합니다. 

   ![게시 모두](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Notebook 속성에서 저장할 때 셀 출력을 포함할지 여부를 구성할 수 있습니다.

   ![노트북 속성](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>매직 명령
Azure Synapse Studio 노트북에서 익숙한 Jupyter 매직 명령을 사용할 수 있습니다. 현재 사용 가능한 마법 명령으로 아래 목록을 확인합니다. GitHub에서 사용 사례를 알려주시면 사용자의 요구를 충족하기 위해 더 많은 마법 명령을 계속 구축할 수 있습니다.

사용 가능한 라인 매직 : [% lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%시간,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%시간](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

사용 가능한 셀 매직 : [%% 시간](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%시간,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%[%%] 캡처](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)%, [%%csharp](#use-multiple-languages) [%%+쓰기파일,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [%%+++,0%+++++++++++++++++++++++++++++++++++++++++++++++++++.각성](#use-multiple-languages) [%%sql](#use-multiple-languages) [%%spark](#use-multiple-languages)

## <a name="shortcut-keys"></a>바로 가기 키

Jupyter 노트북과 마찬가지로 Azure Synapse Studio 노트북에는 모달 사용자 인터페이스가 있습니다. 키보드는 노트북 셀이 있는 모드에 따라 다른 작업을 수행합니다. Synapse Studio 노트북은 지정된 코드 셀에 대해 명령 모드와 편집 모드의 두 가지 모드를 지원합니다.

1. 입력하라는 텍스트 커서가 없는 경우 셀이 명령 모드에 있습니다. 셀이 명령 모드에 있는 경우 전자 필기장을 전체적으로 편집할 수 있지만 개별 셀에 입력하지는 않습니다. 마우스를 누르거나 `ESC` 마우스를 사용하여 셀의 편집기 영역 외부를 클릭하여 명령 모드를 입력합니다.

   ![명령 모드](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 편집 모드는 편집기 영역에 입력하라는 텍스트 커서로 표시됩니다. 셀이 편집 모드에 있으면 셀에 입력할 수 없습니다. 셀의 편집기 `Enter` 영역을 클릭하려면 마우스를 누르거나 마우스를 사용하여 편집 모드를 입력합니다.
   
   ![편집 모드](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>명령 모드에서 바로 가기 키

다음 키 입력 바로 가기를 사용하면 Azure Synapse 노트북에서 코드를 보다 쉽게 탐색하고 실행할 수 있습니다.

| 작업 |시냅스 스튜디오 노트북 바로 가기  |
|--|--|
|현재 셀을 실행하고 아래를 선택합니다. | Shift+Enter |
|현재 셀을 실행하고 아래 삽입 | Alt+Enter |
|위의 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|위의 셀 삽입| A |
|아래 셀 삽입| b |
|선택한 셀 을 위로 확장| Shift+위쪽 화살표 |
|선택한 셀을 아래에서 확장| Shift+아래쪽 화살표|
|셀 위로 이동| Ctrl+Alt+↑ |
|셀 아래로 이동| Ctrl+Alt+↓ |
|선택한 셀 삭제| D, D |
|편집 모드로 전환| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>편집 모드에서 바로 가기 키

다음 키 입력 바로 가기를 사용하면 편집 모드에서 Azure Synapse 노트북에서 코드를 보다 쉽게 탐색하고 실행할 수 있습니다.

| 작업 |시냅스 스튜디오 노트북 바로 가기  |
|--|--|
|커서 위로 이동 | 위로 |
|커서 아래로 이동|아래로|
|실행 취소|Ctrl + Z|
|다시 실행|Ctrl + Y|
|주석 처리/주석 처리 제거|Ctrl + /|
|전에 단어 삭제|Ctrl + 백스페이스|
|후 단어 삭제|Ctrl + 삭제|
|셀 시작으로 이동|Ctrl + Home|
|셀 끝으로 이동 |Ctrl + End|
|한 단어 왼쪽으로 이동|Ctrl + 왼쪽|
|한 단어 오른쪽으로 이동|Ctrl + 오른쪽|
|모두 선택|Ctrl + A|
|들여쓰기| Ctrl + ]|
|디덴트 (것)과 같은|Ctrl + [|
|명령 모드로 전환| Esc |

## <a name="next-steps"></a>다음 단계

- [아파치 스파크 문서용 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
