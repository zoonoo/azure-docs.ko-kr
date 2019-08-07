---
title: 데이터 탐색 및 변환 (데이터 집합 클래스)
titleSuffix: Azure Machine Learning service
description: 데이터 정리, 변환 및 기능 엔지니어링을 통해 요약 통계를 사용 하 여 데이터를 탐색 하 고 데이터를 준비 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: 31a367fcde909f393efa4fea65b25716f95c56ee
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828441"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>데이터 집합 클래스를 사용 하 여 데이터 탐색 및 준비 (미리 보기)

[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)의 azureml 데이터 집합 패키지를 사용 하 여 데이터를 탐색 하 고 준비 하는 방법을 알아봅니다. 데이터 [집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 클래스 (미리 보기)를 사용 하면 샘플링, 요약 통계 및 인텔리전트 변환과 같은 함수를 제공 하 여 데이터를 탐색 하 고 준비할 수 있습니다. 변환 단계는 확장성이 뛰어난 방식으로 다양 한 스키마의 여러 큰 파일을 처리 하는 기능을 포함 하는 [데이터 집합 정의](how-to-manage-dataset-definitions.md) 에 저장 됩니다.

> [!Important]
> 일부 데이터 집합 클래스 (미리 보기)에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지 (GA)에 대 한 종속성이 있습니다. GA'ed [Data Prep 함수](how-to-transform-data.md)를 사용 하 여 변환 함수를 직접 수행할 수 있지만 새 솔루션을 빌드하는 경우이 문서에서 설명 하는 데이터 집합 패키지 래퍼를 사용 하는 것이 좋습니다. 데이터 집합 (미리 보기)을 사용 하면 데이터를 변환할 뿐만 아니라 [스냅숏 데이터](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) 및 저장 된 데이터 [집합 정의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)를 저장할 수도 있습니다. Azure Machine Learning 데이터 집합은 AI 솔루션의 데이터 집합 관리를 위해 확장 된 기능을 제공 하는 다음 버전의 데이터 준비 SDK입니다.

## <a name="prerequisites"></a>필수 구성 요소

데이터를 탐색 하 고 준비 하려면 다음이 필요 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* Azure Machine Learning 서비스 작업 영역. [Azure Machine Learning 서비스 작업 영역 만들기를](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)참조 하세요.

* Azureml 데이터 집합 패키지를 포함 하는 Python 용 Azure Machine Learning SDK (버전 1.0.21 이상) 최신 버전의 SDK를 설치 하거나 업데이트 하려면 [Sdk 설치 또는 업데이트](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 참조 하세요.

* Azure Machine Learning 데이터 준비 SDK입니다. 최신 버전을 설치 하거나 업데이트 하려면 [데이터 준비 SDK 설치 또는 업데이트](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py)를 참조 하세요.

* 예제를 사용 하 여 예제 파일을 다운로드 합니다. [범죄](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) 및 [도시별](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>샘플링

데이터의 샘플을 사용 하 여 데이터 아키텍처와 콘텐츠에 대 한 초기 이해를 받으세요. 현재 Dataset 클래스의 메서드 [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) 는 Top N, Simple 임의 및 층 화 샘플링 전략을 지원 합니다.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>상위 N 개 샘플

상위 N 표본의 경우 데이터 집합의 처음 n 개 레코드는 샘플입니다. 데이터 레코드의 모양을 파악 하거나 데이터에 있는 필드를 확인 하려는 경우에 유용 합니다.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|날짜|블록|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|거짓 사례|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|거짓 사례|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|거짓 사례|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="simple-random-sample"></a>간단한 무작위 샘플

간단한 무작위 샘플링에서 데이터 채우기의 모든 멤버는 샘플의 일부로 선택 될 확률이 같습니다. `simple_random` 샘플 전략에서 데이터 집합의 레코드는 지정 된 확률에 따라 선택 되 고 수정 된 데이터 집합을 반환 합니다. 초기값 매개 변수는 선택 사항입니다.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|날짜|블록|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|거짓 사례|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="stratified-sample"></a>층 화 샘플

층 화 샘플은 모집단의 특정 그룹이 샘플에 표시 되는지 확인 합니다. 샘플 전략에서 모집단은 유사성을 기준으로 층 또는 하위 그룹으로 구분 되며, `fractions` 매개 변수로 표시 되는 층 가중치에 따라 각 층에서 레코드가 무작위로 선택 됩니다. `stratified`

다음 예에서는 지정 된 열을 기준으로 각 레코드를 그룹화 하 고의 `fractions`층 X 가중치 정보를 기반으로 하는 레코드를 포함 합니다. 층가 지정 되지 않았거나 레코드를 그룹화 할 수 없는 경우에는 기본 가중치가 0입니다.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|날짜|블록|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>요약 통계를 사용 하 여 탐색

 [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) 메서드를 사용 하 여 변칙, 누락 값 또는 오류 개수를 검색 합니다. 이 함수는 데이터의 프로필 및 요약 통계를 가져오며,이를 통해 필요한 데이터 준비 작업을 결정 하는 데 도움이 됩니다.

```Python
dataset.get_profile()
```

||형식|Min|max|개수|누락된 수|누락되지 않은 수|누락 백분율|오류 수|비어 있는 수|0.1% 분위수|1% 분위수|5% 분위수|25% 분위수|50% 분위수|75% 분위수|95% 분위수|99% 분위수|99.9% 분위수|평균|표준 편차|Variance|왜곡도|첨도
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358 e + 08|-0.495701|-1.02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
날짜|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
블록|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primary Type|FieldType.STRING|거짓 사례|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
설명|FieldType.STRING|가짜 확인|$500 이상|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Location Description|FieldType.STRING||학교, 공용, 빌딩|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Arrest|FieldType|거짓|거짓|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domestic|FieldType|거짓|거짓|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
뜨|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
구|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Community Area|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI Code|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X Coordinate|FieldType.INTEGER|1.16309 e + 06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108 e + 06|10793.5|1.165e+08|0.335126|-2.33333
Y Coordinate|FieldType.INTEGER|1.8315e+06|1.908 e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005 e + 06|1.89352e+06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
Year|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Updated On|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
위도|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
경도|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
위치|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>누락 값 입력

데이터 집합에서 값이 null 인 NaN의 및 값은 누락 값으로 간주 됩니다. 이는 machine learning 모델의 성능에 영향을 줄 수도 있고 잘못 된 결론을 초래할 수도 있습니다. 대체는 누락 값을 처리 하는 일반적인 방법 중 하나 이며 단순히 삭제할 수 없는 누락 된 값 레코드의 비율이 높은 경우에 유용 합니다.

이전 섹션에서 생성 된 데이터 집합 프로필에서 및 `Latitude` `Longitude` 열이 높은 비율의 누락 값을 표시 합니다. 이 예에서는 이러한 두 열에 대해 mean 및 돌립니다 missing 값을 계산 합니다.

먼저를 사용 [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) 하 여 데이터 집합의 최신 정의를 가져오고를 사용 [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)하 여 데이터를 줄이려면 상당한 합니다. 그러면 주소를 지정할 열만 볼 수 있습니다.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|Arrest| 위도|경도|
-|---------|-----|---------|----------|
|0|10498554|거짓|41.692834|-87.604319|
|1|10516598|거짓| 41.744107 |-87.664494|
|2|10519196|거짓| NaN|NaN|

그런 다음 [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) 함수를 `MEAN` 사용 하 여 위도 열의 값을 확인 합니다. 이 함수는 `group_by_columns` 매개 변수의 열 배열을 수락하여 집계 수준을 지정합니다. 매개 `summary_columns` 변수는 현재 `SummaryColumnsValue` 열 이름, 새 계산 `SummaryFunction` 필드 이름 및 수행할을 지정 하는 함수를 허용 합니다.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Arrest|Latitude_MEAN|
--|-----|--------|
|0|거짓|41.780049|

돌립니다에 대 한 값을 확인 한 후 [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) 을 사용 하 여 계산 `MIN`된, `MAX`, `MEAN` 값 또는 `CUSTOM` 값이 있는 열을 imputes 하는 고정 식을 학습 합니다. `group_by_columns`를 지정하면 그룹별로 계산된 `MIN`, `MAX` 및 `MEAN`을 사용하여 누락 값이 그룹을 기준으로 입력됩니다.

는 [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) column_id 문자열을 허용 하 고는 `ReplaceValueFunction` 돌립니다 형식을 지정 하는입니다. 누락 된 경도 값의 경우 외부 기술 자료를 기반으로-87을 돌립니다 합니다.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

돌립니다 단계는 클래스 함수 `ImputeMissingValuesBuilder` [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)를 [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) 사용 하 여 개체에 연결 될 수 있습니다. `impute_columns` 매개 변수는 `ImputeColumnArguments` 개체의 배열을 수락합니다.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

함수를 호출 하 여 돌립니다 단계를 저장 하 고를 사용 하 여 [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)데이터 흐름 개체에 적용 합니다. [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

다음 출력 테이블에 표시 된 것 처럼 누락 된 위도는 `MEAN` `Arrest==False` group 값과 귀속, 누락 된 경도는-87로 귀속.

||ID|Arrest|위도|경도
-|---------|-----|---------|----------
0|10498554|거짓|41.692834|-87.604319
1|10516598|거짓|41.744107|-87.664494
2|10519196|거짓|41.780049|-87.000000

를 [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) 사용 하 여 데이터 집합 정의를 업데이트 하 여 수행 된 변환 단계를 유지 합니다.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Arrest|위도|경도
-|---------|-----|---------|----------
0|10498554|거짓|41.692834|-87.604319
1|10516598|거짓|41.744107|-87.664494
2|10519196|거짓|41.780049|-87.000000

## <a name="create-assertion-rules"></a>어설션 규칙 만들기

데이터를 정리 하 고 준비 하는 동안 데이터를 정리 하 고 준비 하는 작업은 프로덕션에 필요한 전체 데이터의 하위 집합 일 뿐입니다. 결과적으로, 치료의 일환으로 수행 하는 몇 가지 가정이 false로 전환 될 수 있습니다. 예를 들어 지속적으로 업데이트 되는 데이터 집합에서 원래 특정 범위 내에 숫자만 포함 된 열에는 이후 실행에서 더 넓은 범위의 값이 포함 될 수 있습니다. 이러한 오류는 종종 손상 된 파이프라인이 나 잘못 된 데이터를 초래 합니다.

데이터 집합은 파이프라인이 실행 될 때 평가 되는 데이터에 대 한 어설션을 만드는 기능을 지원 합니다. 이러한 어설션을 사용 하면 데이터에 대 한 가정이 계속 정확한 지 확인 하 고, 실패를 적절 하 게 처리할 수 있습니다.

예를 들어 데이터 집합의 및 `Latitude` `Longitude` 값을 [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) 특정 숫자 범위로 제한 하려는 경우이 메서드는 항상이에 해당 하는지 확인 합니다.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||형식|Min|max|개수|누락된 수|누락되지 않은 수|누락 백분율|오류 수|비어 있는 수|0.1% 분위수|1% 분위수|5% 분위수|25% 분위수|50% 분위수|75% 분위수|95% 분위수|99% 분위수|99.9% 분위수|평균|표준 편차|Variance|왜곡도|첨도
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358 e + 08|-0.495701|-1.02814
Arrest|FieldType|거짓|거짓|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
위도|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
경도|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

프로필에서 `Error Count` `Longitude` 열에 대 한이 3 임을 확인할 수 있습니다. 다음 코드는 데이터 집합을 필터링 하 고, 오류를 검색 하 고, 어설션이 실패 하 게 하는 값을 확인 합니다. 여기에서 코드를 조정 하 고 데이터를 적절 하 게 정리 합니다.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>예제 별 열 파생

데이터 집합에 대 한 고급 도구 중 하나는 원하는 결과의 예제를 사용 하 여 열을 파생 하는 기능입니다. 이를 통해 SDK에 예제를 제공할 수 있으므로 의도 한 변환을 수행 하는 코드를 생성할 수 있습니다.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Case Number|날짜|블록|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

날짜 및 시간 형식을 ' 2016-04-04 10PM-오전 12 시 '로 변환 해야 한다고 가정 합니다. 인수에서 *(원래 출력, 원하는 출력)* 형식으로 `example_data` 매개 변수에서 원하는 출력의 예를 제공 합니다. [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

다음 코드는 원하는 출력의 두 가지 예 ("2016-04-04 23:56:00", "2016-04-04 10PM-오전 12 시") 및 ("2016-04-15 17:00:00", "2016-04-15 4PM-오후 6 시")를 제공 합니다.

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

다음 표에서는 새 열인 Date_Time_Range에 지정 된 형식의 레코드가 포함 되어 있음을 확인 합니다.

||ID|날짜|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>유사 항목 그룹화

서로 다른 원본에서 데이터를 수집 하는 경우 동일한 엔터티의 철자, 대문자 표시 또는 약어의 변형이 발생할 수 있습니다. SDK의 유사 항목 그룹화 또는 텍스트 클러스터링 기능을 사용 하 여 이러한 변형을 자동으로 표준화 하 고 조정 합니다.

예를 들어 열 `inspections.business.city` 에는 여러 가지 형태의 도시 이름 "샌프란시스코"가 포함 되어 있습니다.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|빠른 N-Ezee 인도 Foods|3861 24th St|SF|...
1|67565|타이어 누 들 Cafe의 킹|1541 TARAVAL St|샌프란시스코|...
2|67565|타이어 누 들 Cafe의 킹|1541 TARAVAL St|샌프란시스코|...
3|68701|Grindz|832 clement St|SF|...
4|69186|프리미어 서비스 & 이벤트, i n c.|1255 22nd St|S.F.|...

[`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) 메서드를 사용 하 여 자동으로 검색 된 정규 형태의 "샌프란시스코"로 열을 추가 해 보겠습니다. `source_column` 및`new_column_name` 인수는 필수입니다. 또한 다음과 같은 옵션을 사용할 수 있습니다.

* 원래 값과 정식 값 `similarity_score_column_name`의 모든 쌍 사이에 유사성 점수를 표시 하는 새 열을 만듭니다.

* `similarity_threshold`그룹화 할 값의 최소 유사성 점수를 제공 합니다.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|빠른 N-Ezee 인도 Foods|3861 24th St|SF|San Francisco|0.814806|...
1|67565|타이어 누 들 Cafe의 킹|1541 TARAVAL St|샌프란시스코|San Francisco|1.000000|...
2|67565|타이어 누 들 Cafe의 킹|1541 TARAVAL St|샌프란시스코|San Francisco|1.000000|...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|...
4|69186|프리미어 서비스 & 이벤트, i n c.|1255 22nd St|S.F.|San Francisco|0.814806|...

결과 데이터 집합 정의에서 데이터의 "샌프란시스코"를 나타내는 다른 모든 변형은 동일한 문자열인 "샌프란시스코"로 정규화 되었습니다.

을 사용 하 여 `update_definition()`이 유사 항목 그룹화 단계를 최신 데이터 집합 정의에 저장 합니다.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>다음 단계

* 회귀 모델 예제는 자동화 된 machine learning [자습서](tutorial-auto-train-models.md) 를 참조 하세요.

* 디자인 패턴 및 사용 예제는 SDK [개요](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 를 참조 하세요.

* 데이터 집합 사용에 대 한 예제는 [샘플 노트북](https://aka.ms/dataset-tutorial)을 참조 하세요.
