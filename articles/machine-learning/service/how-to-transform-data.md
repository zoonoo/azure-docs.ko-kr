---
title: Azure Machine Learning Data Prep SDK를 사용하여 데이터 변환 - Python
description: Azure Machine Learning Data Prep SDK를 사용하여 데이터를 변환하고 정리하는 방법을 알아봅니다. 변환 메서드를 사용하여 열을 추가하고, 원치 않는 행이나 열을 필터링하고, 누락 값을 입력할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 988301f24f710a3e29fad1254d405501166e8a4e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309796"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK를 사용하여 데이터 변환

이 문서에서는 [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)를 사용하여 데이터를 로드하는 다양한 방법을 알아봅니다. SDK는 간편하게 열을 추가하고, 원치 않는 행이나 열을 필터링하고, 누락 값을 귀속시키기 위한 함수를 제공합니다.

현재는 다음 작업을 위한 함수가 제공됩니다.

- [식을 사용하여 열 추가](#column)
- [누락 값 입력](#impute-missing-values)
- [예제별 열 파생](#derive-column-by-example)
- [필터링](#filtering)
- [사용자 지정 Python 변환](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>식을 사용하여 열 추가

Azure Machine Learning Data Prep SDK에는 기존 열에서 값을 계산한 다음 새 열에 해당 값을 입력하는 데 사용할 수 있는 `substring` 식이 포함되어 있습니다. 이 예제에서는 데이터를 로드하고 해당 입력 데이터에 열을 추가해 보겠습니다.

```python
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|Case Number|Date|Block|IUCR|Primary Type|설명|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|위치|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


`substring(start, length)` 식을 사용하여 새 열 `Case Category`에서 접두사를 추출한 다음, 새 열 Case Category에 해당 문자열을 입력합니다. `substring_expression` 변수를 `expression` 매개 변수에 전달하면 각 레코드에 대해 식을 실행하는 새 계산 열이 만들어집니다.

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Case Number|Case Category|Date|Block|IUCR|Primary Type|설명|Location Description|Arrest|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|위치|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



`substring(start)` 식을 사용하여 Case Number 열에서 숫자만 추출한 다음, 새 열을 만듭니다. `to_number()` 함수를 사용하여 숫자 데이터 형식으로 변환한 후 문자열 열 이름을 매개 변수로 전달합니다.

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|Case Number|Case Id|Date|Block|IUCR|Primary Type|설명|Location Description|Arrest|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|위치|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|

## <a name="impute-missing-values"></a>누락 값 입력

SDK는 지정된 열에 누락 값을 귀속시킬 수 있습니다. 이 예제에서는 위도 및 경도 값을 로드한 다음, 입력 데이터에서 누락 값을 귀속시킵니다.

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|Arrest|위도|경도|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

세 번째 레코드에서는 위도 및 경도 값이 누락되어 있습니다. 이러한 누락 값을 입력하려는 경우 `ImputeMissingValuesBuilder`를 사용하여 수정된 식을 학습합니다. 이 프로그램은 계산된 `MIN`, `MAX` 또는 `MEAN` 값이나 `CUSTOM` 값을 열에 입력할 수 있습니다. `group_by_columns`를 지정하면 그룹별로 계산된 `MIN`, `MAX` 및 `MEAN`을 사용하여 누락 값이 그룹을 기준으로 입력됩니다.

`summarize()` 함수를 사용하여 latitude 열의 `MEAN` 값을 확인합니다. 이 함수는 `group_by_columns` 매개 변수의 열 배열을 수락하여 집계 수준을 지정합니다. `summary_columns` 매개 변수는 `SummaryColumnsValue` 호출을 수락합니다. 이 함수 호출은 현재 열 이름, 새 계산 필드 이름 및 수행할 `SummaryFunction`을 지정합니다.

```python
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
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
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|Arrest|위도|경도|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

위의 결과에 나와 있는 것처럼 `Arrest=='false'` 그룹의 `MEAN` 값을 사용하여 누락된 위도를 입력했습니다. 누락된 위도는 42를 사용하여 입력되었습니다.

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>예제별 열 파생

Azure Machine Learning Data Prep SDK의 고급 도구 중 하나는 원하는 결과의 예제를 사용하여 열을 파생하는 기능입니다. 이 기능을 사용하면 SDK가 코드를 생성하여 원하는 열을 변형하도록 SDK에 예제를 제공할 수 있습니다.

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(10)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

날짜와 시간이 'Mar 10, 2018 | 2AM-4AM' 형식인 데이터 세트와 이 파일을 조인해야 하는 경우를 가정해 보겠습니다.

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|
|5|1/1/2015 4:00|Jan 1, 2015 4AM-6AM|
|6|1/1/2015 4:54|Jan 1, 2015 4AM-6AM|
|7|1/1/2015 5:54|Jan 1, 2015 4AM-6AM|
|8|1/1/2015 6:54|Jan 1, 2015 6AM-8AM|
|9|1/1/2015 7:00|Jan 1, 2015 6AM-8AM|

위의 코드는 먼저 파생 열용으로 작성기를 만듭니다. 고려할 원본 열 배열(`DATE`)과 추가할 새 열의 이름을 입력합니다. 첫 번째 예제와 마찬가지로 두 번째 행(인덱스 1)을 전달하고 파생 열에 대해 원하는 값을 입력합니다.

마지막으로, `builder.preview()`를 호출합니다. 그러면 원본 열 옆에 파생 열이 표시됩니다. 값 형식은 올바른 것처럼 보이지만 같은 날짜인 "Jan 1, 2015"의 값만 표시됩니다.

이제 맨 위에서부터 `skip`하려는 행 수를 전달하면 해당 수 이후의 행을 확인할 수 있습니다.

```
builder.preview(skip=30)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|11/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|11/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|11/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|11/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|34|11/2/2015 1:00|Feb 1, 2015 12AM-2AM|
|35|11/2/2015 1:54|Feb 1, 2015 12AM-2AM|
|36|11/2/2015 2:54|Feb 1, 2015 2AM-4AM|
|37|11/2/2015 3:54|Feb 1, 2015 2AM-4AM|
|38|11/2/2015 4:00|Feb 1, 2015 4AM-6AM|
|39|11/2/2015 4:54|Feb 1, 2015 4AM-6AM|

여기서 생성된 프로그램에는 문제가 있습니다. 파생 프로그램은 위에서 제공한 예제 하나만을 기준으로 하므로 날짜를 “일/월/년”으로 구문 분석했는데, 여기서는 날짜를 다른 형식으로 표시해야 하기 때문입니다. 이 문제를 해결하려면 `builder` 변수에 `add_example()` 함수를 사용하여 다른 예제를 제공합니다.

```python
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|34|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|35|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|36|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|37|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|38|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|39|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

이제 행이 '1/2/2015'를 'Jan 2, 2015'로 올바르게 처리합니다. 하지만 파생 열 아래쪽을 계속 확인하면 끝에 있는 값의 경우 파생 열에 아무 내용도 표시되지 않습니다. 이 문제를 해결하려면 행 66에 대해 다른 예제를 제공해야 합니다.

```python
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|5|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|6|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|7|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|8|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|9|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

'|'을 사용하여 날짜와 시간을 구분하려면 다른 예제를 추가합니다. 이번에는 미리 보기에서 행을 전달하는 대신 `source_data` 매개 변수의 값에 대해 열 이름 사전을 생성합니다.

```python
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|없음|
|1|1/1/2015 23:54|없음|
|2|1/1/2015 23:59|없음|
|3|1/2/2015 0:54|없음|
|4|1/2/2015 1:00|없음|
|5|1/2/2015 1:54|없음|
|6|1/2/2015 2:54|없음|
|7|1/2/2015 3:54|없음|
|8|1/2/2015 4:00|없음|
|9|1/2/2015 4:54|없음|

그런데 이 방식에는 명확한 단점이 있습니다. 파생 열에서 값이 있는 행은 제공한 예제와 정확히 일치하는 행뿐이기 때문입니다. 작성기 개체에서 `list_examples()`를 호출하여 현재 예제 파생 결과를 확인합니다.

```python
examples = builder.list_examples()
```

| |DATE|예제|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|
|3|11/11/2015 0:54|Nov 11, 2015 \| 12AM-2AM|-4|

이 경우 일치하지 않는 예제가 제공되었습니다. 이 문제를 해결하려면 날짜와 시간 사이에 '|'를 포함하여 처음 3개 예제를 올바른 예제로 바꿉니다.

pandas DataFrame에서 `example_row`를 전달하거나 `example_id` 값을 전달하여 잘못된 예제를 삭제한 다음, 수정된 새 예제를 다시 추가하여 일관되지 않은 예제를 수정합니다.

```python
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | Jan 1, 2015 \| 12AM-2AM |
| 1 | 1/1/2015 1:00 | Jan 1, 2015 \| 12AM-2AM |
| 2 | 1/1/2015 1:54 | Jan 1, 2015 \| 12AM-2AM |
| 3 | 1/1/2015 2:54 | Jan 1, 2015 \| 2AM-4AM |
| 4 | 1/1/2015 3:54 | Jan 1, 2015 \| 2AM-4AM |
| 5 | 1/1/2015 4:00 | Jan 1, 2015 \| 4AM-6AM|
| 6 | 1/1/2015 4:54 | Jan 1, 2015 \| 4AM-6AM|
| 7 | 1/1/2015 5:54 | Jan 1, 2015 \| 4AM-6AM|
| 8 | 1/1/2015 6:54 | Jan 1, 2015 \| 6AM-8AM|
| 9 | 1/1/2015 7:00 | Jan 1, 2015 \| 6AM-8AM|

이제 데이터가 정확한 듯하므로 작성기에서 `to_dataflow()`를 호출합니다. 그러면 원하는 파생 열이 추가된 데이터 흐름이 반환됩니다.

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>필터링

SDK에는 열이나 행을 필터링하는 데 사용할 수 있는 `Dataflow.drop_columns` 및 `Dataflow.filter`가 포함되어 있습니다.

### <a name="initial-setup"></a>초기 설치

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>열 필터링

열을 필터링하려면 `Dataflow.drop_columns`를 사용합니다. 이 메서드는 삭제할 열 목록을 가져오거나 더 복잡한 인수인 `ColumnSelector`를 가져옵니다.

#### <a name="filtering-columns-with-list-of-strings"></a>문자열 목록을 사용하여 열 필터링

이 예제에서 `drop_columns`는 문자열의 목록을 가져옵니다. 각 문자열은 삭제하려는 열과 정확히 일치해야 합니다.

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|없음|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>regex를 사용하여 열 필터링

`ColumnSelector` 식을 사용하여 regex 식과 일치하는 열을 삭제합니다. 이 예제에서는 `Column*|.*longitude|.*latitude` 식과 일치하는 열을 모두 삭제합니다.

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|없음|없음|없음|없음|없음|없음|없음|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>행 필터링

행을 필터링하려면 `DataFlow.filter`를 사용합니다. 이 메서드는 Azure Machine Learning Data Prep SDK 식을 인수로 가져온 다음 식이 True로 평가되는 행이 포함된 새 데이터 흐름을 반환합니다. 식 작성기(`col`, `f_not`, `f_and`, `f_or`) 및 정규 연산자(>, <, >=, <=, ==, !=)를 사용하여 식을 작성합니다.

### <a name="filtering-rows-with-simple-expressions"></a>단순 식을 사용하여 행 필터링

식 작성기 `col`을 사용하여 열 이름을 문자열 인수 `col('column_name')`로 지정합니다. 이 식은 다음 표준 연산자 >, <, >=, <=, ==, != 중 하나와 함께 사용하여 `col('Tip_amount') > 0`과 같은 식을 작성합니다. 마지막으로 작성한 식을 `Dataflow.filter` 함수에 전달합니다.

이 예제에서 `dataflow.filter(col('Tip_amount') > 0)`는 `Tip_amount`의 값이 0보다 큰 행이 포함된 새 데이터 흐름을 반환합니다.

> [!NOTE] 
> `Tip_amount`는 먼저 numeric으로 변환되므로 식을 작성하여 다른 numeric 값과 비교할 수 있습니다.

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1.05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>복잡한 식을 사용하여 행 필터링

복잡한 식을 사용하여 필터링하려면 단순 식 하이상나 을 식 작성기 `f_not`, `f_and` 또는 `f_or`과 결합합니다.

이 예제에서 `Dataflow.filter`는 `'Passenger_count'`가 5보다 작고 `'Tolls_amount'`가 0보다 큰 행이 포함된 새 데이터 흐름을 반환합니다.

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

둘 이상의 표현식 작성기를 결합해 중첩 식을 만들어서 행을 필터링할 수도 있습니다.

> [!NOTE]
> `lpep_pickup_datetime` 및 `Lpep_dropoff_datetime`은 먼저 datetime으로 변환되므로 식을 작성하여 다른 datetime 값과 비교할 수 있습니다.

```python
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52+00:00|2013-08-25 09:34:34+00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51+00:00|2013-08-25 17:13:55+00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11+00:00|2013-08-25 18:02:57+00:00|1.0|9.60|6.87|5.33|41.20|

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

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |
|2|ALABAMA|1|101710|Hale County|10171002156| |
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589| |

데이터 세트를 트리밍하고 기본적인 변환을 몇 가지 수행해 봅니다.

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|없음|
|1|ALABAMA|Hale County|1.017100e+10|없음|
|2|ALABAMA|Hale County|1.017100e+10|없음|
|3|ALABAMA|Hale County|1.017100e+10|2|
|4|ALABAMA|Hale County|1.017100e+10|없음|

다음 필터를 사용하여 null 값을 찾습니다.

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|없음|
|1|ALABAMA|Hale County|1.017100e+10|없음|
|2|ALABAMA|Hale County|1.017100e+10|없음|
|3|ALABAMA|Hale County|1.017100e+10|없음|
|4|ALABAMA|Hale County|1.017100e+10|없음|

### <a name="transform-partition"></a>파티션 변환

pandas 함수를 사용하여 모든 null 값을 0으로 바꿉니다. 이 코드는 전체 데이터 세트에서 한 번에 실행되는 것이 아니라 파티션별로 실행됩니다. 즉, 큰 데이터 세트에서는 런타임이 데이터를 처리할 때 이 코드가 파티션별로 병렬 실행될 수 있습니다.

Python 스크립트는 `df` 및 `index`의 두 인수를 취하는 `transform()`라는 함수를 정의해야 합니다. `df` 인수는 파티션에 대한 데이터가 포함된 pandas 데이터 프레임이 되며, `index` 인수는 파티션의 고유 식별자입니다. 변환 함수는 전달된 데이터 프레임을 완전히 편집할 수 있지만 데이터 프레임을 반환해야 합니다. Python 스크립트를 가져오는 모든 라이브러리는 데이터 흐름이 실행되는 환경에 존재해야 합니다.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(5)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e+10|0.0|
|1|ALABAMA|Hale County|1.017100e+10|0.0|
|2|ALABAMA|Hale County|1.017100e+10|0.0|
|3|ALABAMA|Hale County|1.017100e+10|2.0|
|4|ALABAMA|Hale County|1.017100e+10|0.0|

### <a name="new-script-column"></a>새 스크립트 열

Python 코드를 사용하여 county 이름과 state 이름이 포함된 새 열을 만들고 state 이름을 대문자로 표시할 수 있습니다. 이렇게 하려면 데이터 흐름에 대해 `new_script_column()` 메서드를 사용합니다.

Python 스크립트는 단일 인수 `row`를 취하는 `newvalue()`라는 함수를 정의해야 합니다. `row` 인수는 dict(`key`: 열 이름 `val`: 현재 값)이며, 데이터 세트의 각 행에 대해 이 함수에 전달됩니다. 이 함수는 새 열에 사용할 값을 반환해야 합니다. Python 스크립트를 가져오는 모든 라이브러리는 데이터 흐름이 실행되는 환경에 존재해야 합니다.

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|2|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|3|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|2.0|
|4|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|

### <a name="new-script-filter"></a>새 스크립트 필터

새 `county_state` 열에 ‘Hale’이 없는 행만 포함하도록 데이터 세트를 필터링하는 Python 식을 작성합니다. 행을 유지하려는 경우 식은 `True`를 반환하고, 행을 삭제하려는 경우에는 `False`를 반환합니다.

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|2|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|3|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
|4|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|
