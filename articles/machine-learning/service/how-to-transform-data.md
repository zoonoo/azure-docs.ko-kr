---
title: '변환: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Data Prep SDK를 사용하여 데이터를 변환하고 정리하는 방법을 알아봅니다. 변환 메서드를 사용하여 열을 추가하고, 원치 않는 행이나 열을 필터링하고, 누락 값을 입력할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 08cf646d63e1a295a1bc2ff28180983cc462f084
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360923"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK를 사용하여 데이터 변환

이 문서에서는 패키지를 `azureml-dataprep` 사용 하 여 데이터를 변환 하는 다양 한 방법에 대해 알아봅니다. 패키지는 열을 간단 하 게 추가 하 고, 원치 않는 행 또는 열을 필터링 하 고, 누락 된 값을 돌립니다 하는 함수를 제공 합니다. [Azureml-dataprep 패키지](https://aka.ms/data-prep-sdk)에 대 한 전체 참조 설명서를 참조 하세요.

> [!Important]
> 새 솔루션을 구축 하는 경우 데이터, 스냅숏 데이터 및 저장소 버전 데이터 집합 정의를 변환 하는 [Azure Machine Learning 데이터 집합](how-to-explore-prepare-data.md) (미리 보기)을 시도 합니다. 데이터 집합은 AI 솔루션의 데이터 집합 관리를 위해 확장 된 기능을 제공 하는 다음 버전의 데이터 준비 SDK입니다. 패키지를 사용 하 `azureml-dataprep` 여 데이터 집합을 만드는 대신 `azureml-datasets` 패키지를 사용 하 여 변환을 통해 데이터 흐름을 만드는 경우 나중에 스냅숏 또는 버전이 지정 된 데이터 집합을 사용할 수 없습니다.

이 방법에 대 한 예제는 다음 작업을 보여 줍니다.

- 식을 사용하여 열 추가
- [누락 값 입력](#impute-missing-values)
- [예제별 열 파생](#derive-column-by-example)
- [필터링](#filtering)
- [사용자 지정 Python 변환](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>식을 사용하여 열 추가

Azure Machine Learning Data Prep SDK에는 기존 열에서 값을 계산한 다음 새 열에 해당 값을 입력하는 데 사용할 수 있는 `substring` 식이 포함되어 있습니다. 이 예제에서는 데이터를 로드하고 해당 입력 데이터에 열을 추가해 보겠습니다.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|Case Number|날짜|블록|IUCR|Primary Type|Description|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|위치|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


`substring(start, length)` 식을 사용하여 새 열 `Case Category`에서 접두사를 추출한 다음, 새 열 Case Category에 해당 문자열을 입력합니다. `substring_expression` 변수를 `expression` 매개 변수에 전달하면 각 레코드에 대해 식을 실행하는 새 계산 열이 만들어집니다.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Case Number|Case Category|날짜|블록|IUCR|Primary Type|Description|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|위치|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


`substring(start)` 식을 사용하여 Case Number 열에서 숫자만 추출한 다음, 새 열을 만듭니다. `to_number()` 함수를 사용하여 숫자 데이터 형식으로 변환한 후 문자열 열 이름을 매개 변수로 전달합니다.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>누락 값 입력

SDK는 지정된 열에 누락 값을 귀속시킬 수 있습니다. 이 예제에서는 위도 및 경도 값을 로드한 다음, 입력 데이터에서 누락 값을 귀속시킵니다.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|Arrest|위도|경도|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

세 번째 레코드에서는 위도 및 경도 값이 누락되어 있습니다. 이러한 누락 값을 돌립니다 위해를 사용 [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) 하 여 고정 된 식을 학습 합니다. 이 프로그램은 계산된 `MIN`, `MAX` 또는 `MEAN` 값이나 `CUSTOM` 값을 열에 입력할 수 있습니다. `group_by_columns`를 지정하면 그룹별로 계산된 `MIN`, `MAX` 및 `MEAN`을 사용하여 누락 값이 그룹을 기준으로 입력됩니다.

함수를 `MEAN` [`summarize()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) 사용 하 여 위도 열의 값을 확인 합니다. 이 함수는 `group_by_columns` 매개 변수의 열 배열을 수락하여 집계 수준을 지정합니다. `summary_columns` 매개 변수는 `SummaryColumnsValue` 호출을 수락합니다. 이 함수 호출은 현재 열 이름, 새 계산 필드 이름 및 수행할 `SummaryFunction`을 지정합니다.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                             summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                        summary_column_name='Latitude_MEAN',
                                                                        summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Arrest|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

고도의 `MEAN` 값은 정확해 보이며 `ImputeColumnArguments` 함수를 사용하여 귀속시킵니다. 이 함수는 `column_id` 문자열 및 `ReplaceValueFunction`을 수락하여 귀속 형식을 지정합니다. 누락된 경고 값의 경우 외부 정보를 기준으로 42를 사용하여 귀속시킵니다.

작성기 함수 `impute_missing_values()`를 사용하여 귀속 단계를 `ImputeMissingValuesBuilder` 개체에 연결할 수 있습니다. `impute_columns` 매개 변수는 `ImputeColumnArguments` 개체의 배열을 수락합니다. `learn()` 함수를 호출하여 귀속 단계를 저장한 후 `to_dataflow()`를 사용하여 dataflow 개체에 적용합니다.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                      group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|Arrest|위도|경도|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

위의 결과에 나와 있는 것처럼 `Arrest=='false'` 그룹의 `MEAN` 값을 사용하여 누락된 위도를 입력했습니다. 누락된 위도는 42를 사용하여 입력되었습니다.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>예제별 열 파생

Azure Machine Learning Data Prep SDK의 고급 도구 중 하나는 원하는 결과의 예제를 사용하여 열을 파생하는 기능입니다. 이 기능을 사용하면 SDK가 코드를 생성하여 원하는 열을 변형하도록 SDK에 예제를 제공할 수 있습니다.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

날짜와 시간이 'Mar 10, 2018 | 2AM-4AM' 형식인 데이터 세트와 이 파일을 조인해야 하는 경우를 가정해 보겠습니다.

```python
builder = dflow.builders.derive_column_by_example(
    source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(
    source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5)
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|

위의 코드는 먼저 파생 열용으로 작성기를 만듭니다. 고려할 원본 열 배열(`DATE`)과 추가할 새 열의 이름을 입력합니다. 첫 번째 예제와 마찬가지로 두 번째 행(인덱스 1)을 전달하고 파생 열에 대해 원하는 값을 입력합니다.

마지막으로, `builder.preview(skip=30, count=5)`를 호출합니다. 그러면 원본 열 옆에 파생 열이 표시됩니다. 값 형식은 올바른 것처럼 보이지만 같은 날짜인 "Jan 1, 2015"의 값만 표시됩니다.

이제 맨 위에서부터 `skip`하려는 행 수를 전달하면 해당 수 이후의 행을 확인할 수 있습니다.

> [!NOTE]
> Preview () 함수는 행을 건너뛰고 출력 인덱스의 번호를 다시 표시 하지 않습니다. 아래 예에서는 테이블의 인덱스 0이 데이터 흐름의 인덱스 30에 해당 합니다.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|4|1/2/2015 1:00|Feb 1, 2015 12AM-2AM|

여기서 생성된 프로그램에는 문제가 있습니다. 파생 프로그램은 위에서 제공한 예제 하나만을 기준으로 하므로 날짜를 “일/월/년”으로 구문 분석했는데, 여기서는 날짜를 다른 형식으로 표시해야 하기 때문입니다. 이 문제를 해결 하려면 특정 레코드 인덱스를 대상으로 지정 하 고 `add_example()` `builder` 변수에 함수를 사용 하는 다른 예를 제공 합니다.

```python
builder.add_example(
    source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|

이제 행이 ' 1/2/2015 '를 ' Jan 2, 2015 '로 올바르게 처리 하지만 파생 열의 인덱스 76를 초과 하는 경우 끝에 있는 값이 파생 열에 아무것도 표시 되지 않습니다.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-오전 8 시|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-오전 8 시|
|2|1/29/2015 6:54|없음|
|3|1/29/2015 7:00|없음|
|4|1/29/2015 7:54|없음|

```python
builder.add_example(
    source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-오전 8 시|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-오전 8 시|
|2|1/29/2015 6:54|1 월 29 일 오전 2015 오전 8 시|
|3|1/29/2015 7:00|1 월 29 일 오전 2015 오전 8 시|
|4|1/29/2015 7:54|1 월 29 일 오전 2015 오전 8 시|

 현재 예제 목록에서 builder 개체에 대 한 `list_examples()` 호출을 확인 합니다.

```python
examples = builder.list_examples()
```

| |DATE|예제|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|


잘못 된 예를 삭제 하려는 경우에는 pandas 데이터 프레임 또는 `example_row` `example_id` 값 중 하나를 전달할 수 있습니다. 예를 들어를 실행 `builder.delete_example(example_id=-1)`하는 경우 첫 번째 변환 예제를 삭제 합니다.


원하는 `to_dataflow()` 파생 열이 추가 된 데이터 흐름을 반환 하는 작성기에서를 호출 합니다.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>필터링

SDK에는 열 또는 [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) 행 [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py) 을 필터링 할 수 있는 및 메서드가 포함 되어 있습니다.

### <a name="initial-setup"></a>초기 설치

> [!Note]
> 이 동일한 예제의 URL은 전체 URL이 아닙니다. 대신 BLOB의 demo 폴더를 말합니다. 데이터의 전체 URL은입니다. https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

자습서에서 수행 하는 작업은 폴더 내의 모든 파일을 로드 하 고 결과를 green_df_raw 및 yellow_df_raw로 집계 하는 것입니다.

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(
    path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>열 필터링

열을 필터링하려면 `drop_columns()`를 사용합니다. 이 메서드는 삭제할 열 목록이 나 더 복잡 한 인수를 호출 [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py)합니다.

#### <a name="filtering-columns-with-list-of-strings"></a>문자열 목록을 사용하여 열 필터링

이 예제에서 `drop_columns()`는 문자열의 목록을 가져옵니다. 각 문자열은 삭제하려는 열과 정확히 일치해야 합니다.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>regex를 사용하여 열 필터링

`ColumnSelector` 식을 사용하여 regex 식과 일치하는 열을 삭제합니다. 이 예제에서는 `Column*|.*longitude|.*latitude` 식과 일치하는 열을 모두 삭제합니다.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector(
    'Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|

## <a name="filtering-rows"></a>행 필터링

행을 필터링하려면 `filter()`를 사용합니다. 이 메서드는 Azure Machine Learning Data Prep SDK 식을 인수로 가져온 다음 식이 True로 평가되는 행이 포함된 새 데이터 흐름을 반환합니다. 식 작성기(`col`, `f_not`, `f_and`, `f_or`) 및 정규 연산자(>, <, >=, <=, ==, !=)를 사용하여 식을 작성합니다.

### <a name="filtering-rows-with-simple-expressions"></a>단순 식을 사용하여 행 필터링

식 작성기 `col`을 사용하여 열 이름을 문자열 인수 `col('column_name')`로 지정합니다. 이 식은 다음 표준 연산자 >, <, >=, <=, ==, != 중 하나와 함께 사용하여 `col('Tip_amount') > 0`과 같은 식을 작성합니다. 마지막으로 작성한 식을 `filter()` 함수에 전달합니다.

이 예제에서 `dflow.filter(col('Tip_amount') > 0)`는 `Tip_amount`의 값이 0보다 큰 행이 포함된 새 데이터 흐름을 반환합니다.

> [!NOTE] 
> `Tip_amount`는 먼저 numeric으로 변환되므로 식을 작성하여 다른 numeric 값과 비교할 수 있습니다.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>복잡한 식을 사용하여 행 필터링

복잡한 식을 사용하여 필터링하려면 단순 식 하이상나 을 식 작성기 `f_not`, `f_and` 또는 `f_or`과 결합합니다.

이 예제에서 `dflow.filter()`는 `'Passenger_count'`가 5보다 작고 `'Tolls_amount'`가 0보다 큰 행이 포함된 새 데이터 흐름을 반환합니다.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(
    dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

둘 이상의 표현식 작성기를 결합해 중첩 식을 만들어서 행을 필터링할 수도 있습니다.

> [!NOTE]
> `lpep_pickup_datetime` 및 `Lpep_dropoff_datetime`은 먼저 datetime으로 변환되므로 식을 작성하여 다른 datetime 값과 비교할 수 있습니다.

```python
dflow = dflow.to_datetime(
    ['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013, 7, 1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>사용자 지정 Python 변환

사용자 고유의 스크립트를 작성하여 변환하는 것이 가장 쉬운 경우가 있습니다. SDK는 사용자 지정 Python 스크립트에 사용할 수 있는 확장점 3개를 제공합니다.

- 새 스크립트 열
- 새 스크립트 필터
- 파티션 변환

이러한 각 확장은 강화 및 규모 확장 런타임에서 모두 지원됩니다. 이러한 확장점 사용 시의 가장 큰 장점은 데이터 프레임을 만들기 위해 모든 데이터를 끌어오지 않아도 된다는 것입니다. 사용자 지정 Python 코드는 다른 변환처럼 대규모로/파티션별로 실행되며 일반적으로는 병렬로 실행됩니다.

### <a name="initial-data-preparation"></a>초기 데이터 준비

먼저 Azure Blob에서 일부 데이터를 로드합니다.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|앨라배마|1|101710|Hale County|10171002158| |
|1|앨라배마|1|101710|Hale County|10171002162| |

데이터 집합을 축소 하 고 열 제거, 값 대체 및 형식 변환을 포함 하 여 몇 가지 기본 변환 작업을 수행 합니다.

```python
dflow = dflow.keep_columns(
    ['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(
    columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|앨라배마|Hale County|1.017100e+10|없음|
|1|앨라배마|Hale County|1.017100e+10|없음|

다음 필터를 사용하여 null 값을 찾습니다.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|앨라배마|Hale County|1.017100e+10|없음|
|1|앨라배마|Hale County|1.017100e+10|없음|

### <a name="transform-partition"></a>파티션 변환

를 [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) 사용 하 여 모든 null 값을 0으로 바꿉니다. 이 코드는 전체 데이터 세트에서 한 번에 실행되는 것이 아니라 파티션별로 실행됩니다. 즉, 큰 데이터 세트에서는 런타임이 데이터를 처리할 때 이 코드가 파티션별로 병렬 실행될 수 있습니다.

Python 스크립트는 `df` 및 `index`의 두 인수를 취하는 `transform()`라는 함수를 정의해야 합니다. `df` 인수는 파티션에 대한 데이터가 포함된 pandas 데이터 프레임이 되며, `index` 인수는 파티션의 고유 식별자입니다. 변환 함수는 전달된 데이터 프레임을 완전히 편집할 수 있지만 데이터 프레임을 반환해야 합니다. Python 스크립트를 가져오는 모든 라이브러리는 데이터 흐름이 실행되는 환경에 존재해야 합니다.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|앨라배마|Hale County|1.017100e+10|0.0|
|1|앨라배마|Hale County|1.017100e+10|0.0|

### <a name="new-script-column"></a>새 스크립트 열

Python 스크립트를 사용 하 여 군 이름 및 상태 이름을 포함 하는 새 열을 만들고 상태 이름을 대문자로 지정할 수 있습니다. 이렇게 하려면 데이터 흐름에서 메서드 [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) 를 사용 합니다.

Python 스크립트는 단일 인수 `row`를 취하는 `newvalue()`라는 함수를 정의해야 합니다. `row` 인수는 dict(`key`: 열 이름 `val`: 현재 값)이며, 데이터 세트의 각 행에 대해 이 함수에 전달됩니다. 이 함수는 새 열에 사용할 값을 반환해야 합니다. Python 스크립트를 가져오는 모든 라이브러리는 데이터 흐름이 실행되는 환경에 존재해야 합니다.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|앨라배마|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|1|앨라배마|Hale County|Hale County, Alabama|1.017100e+10|0.0|

### <a name="new-script-filter"></a>새 스크립트 필터

를 사용 하 여 데이터 [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) 집합을 ' Hale '가 새 `county_state` 열에 없는 행 으로만 필터링 하려면를 사용 하 여 Python 식을 작성 합니다. 행을 유지하려는 경우 식은 `True`를 반환하고, 행을 삭제하려는 경우에는 `False`를 반환합니다.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|앨라배마|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|앨라배마|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|

## <a name="next-steps"></a>다음 단계

* 특정 시나리오를 해결 하는 방법에 대 한 예제는 데이터 준비 SDK [자습서](tutorial-data-prep.md) Azure Machine Learning를 참조 하세요.
