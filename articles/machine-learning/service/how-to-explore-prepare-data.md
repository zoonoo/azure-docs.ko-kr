---
title: 탐색 하 고 (데이터 집합 클래스) 데이터를 변환 합니다.
titleSuffix: Azure Machine Learning service
description: 요약 통계를 사용 하 여 데이터를 탐색 하 고 데이터 정리, 변환 및 기능 엔지니어링을 통해 데이터를 준비 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: e29ef2616a43223ec582575ca6363f78b26e5f22
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753050"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>탐색 하 고 데이터 집합 클래스 (미리 보기)를 사용 하 여 데이터 준비

탐색에서 azureml 데이터 집합 패키지를 사용 하 여 데이터를 준비 하는 방법을 알아봅니다 합니다 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)합니다. 합니다 [데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 클래스 (미리 보기)을 사용 하면 탐색 하 고 같은 함수를 제공 하 여 데이터를 준비할 수 있습니다: 샘플링, 요약 통계 및 지능형 변환 합니다. 변환 단계에 저장 됩니다 [데이터 집합 정의](how-to-manage-dataset-definitions.md) 확장성이 뛰어난 방식으로 스키마가 서로 다른 여러 큰 파일을 처리 하는 기능을 사용 하 여 합니다.

> [!Important]
> 일부 데이터 집합 클래스 (미리 보기)에 종속 되어는 [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지 (GA). 변환 함수는 GA'ed를 사용 하 여 직접 수행할 수 있습니다 하는 동안 [데이터 준비 함수](how-to-transform-data.md), 새 솔루션을 빌드하는 경우이 문서에 설명 된 데이터 집합 패키지 래퍼 것이 좋습니다. Azure Machine Learning 데이터 집합 (미리 보기) 수 뿐만 아니라 데이터를 변환 하는 데 뿐만 [스냅숏 데이터](how-to-create-dataset-snapshots.md) 저장 하 고 [버전이 지정 된 데이터 집합 정의](how-to-manage-dataset-definitions.md)합니다. 데이터 집합에는 AI 솔루션에서 데이터 집합을 관리 하기 위한 확장된 기능을 제공 하는 데이터 준비 SDK의 다음 버전입니다.

## <a name="prerequisites"></a>필수 조건

탐색 하 고 데이터 준비를 해야 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* Azure Machine Learning 서비스 작업 영역. 참조 [Azure Machine Learning 서비스 작업 영역 만들기](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)합니다.

* Python 용 Azure Machine Learning SDK (버전 1.0.21 이상), azureml 데이터 집합 패키지를 포함 하는 합니다. 를 설치 하거나 SDK의 최신 버전으로 업데이트를 참조 하세요 [설치 또는 업데이트 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.

* Azure Machine Learning 데이터 준비 SDK입니다. 를 설치 하거나 최신 버전으로 업데이트를 참조 하세요 [설치 또는 데이터 준비 SDK 업데이트](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install)합니다.

* 예제를 따라가려면 샘플 파일을 다운로드 합니다. [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) 하 고 [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json)합니다.

## <a name="sampling"></a>샘플링

초기 데이터 아키텍처 및 콘텐츠를 파악 하기 위해 데이터의 샘플을 사용 합니다. 이번에 [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) Dataset 클래스 메서드에서 Top N, 간단한 임의 및 Stratified 샘플링 전략을 지원 합니다.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>상위 n 개 샘플

Top N 샘플링에 대 한 데이터 집합의 처음 n 개 레코드 샘플 됩니다. 또는 데이터에 있는 필드를 같은 어떤 데이터 레코드 확인의 아이디어를 얻을 하려는 경우에 유용 합니다.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|거짓 연습|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|거짓 연습|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S 어떤 AVE|1120|거짓 연습|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="simple-random-sample"></a>간단한 무작위 샘플링

간단한 무작위 샘플링 데이터 채우기의 모든 멤버에는 샘플의 일부로는 같은 기회가 있습니다. 에 `simple_random` 샘플 전략, 데이터 집합에서 레코드를 지정 된 확률을 기준으로 선택 및 수정 된 데이터 집합을 반환 합니다. 초기값 매개 변수는 선택 사항입니다.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|거짓 연습|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="stratified-sample"></a>층 화 샘플

층 화 샘플 샘플에서 모집단의 특정 그룹을 표시 한다는 것을 확인 합니다. 에 `stratified` 샘플 모집단 층, 또는 유사성에 따라 하위 그룹으로 나뉩니다 전략과 레코드에 나타난 층 가중치에 따라 각 층에서 임의로 질문이 선택 됩니다는 `fractions` 매개 변수입니다.

다음 예에서는 지정된 된 열에 각 레코드를 그룹화 하 고 층 X 가중치 정보에 따라 언급된 레코드를 포함 `fractions`합니다. 층 지정 되지 않았거나 레코드를 그룹화 할 수 없습니다, 하는 경우 기본 가중치입니다. 샘플은 0입니다.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Case Number|Date|Block|IUCR|Primary Type|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>요약 통계를 사용 하 여 탐색

 누락 된 값을 문제를 감지 하거나 오류를 사용 하 여 계산 된 [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) 메서드. 이 함수는 프로필을 가져옵니다 및 요약 통계에 도움이 되는 데이터를 적용 하는 데 필요한 데이터 준비 작업을 확인 합니다.

```Python
dataset.get_profile()
```

||Type|Min|max|개수|누락된 수|누락되지 않은 수|누락 백분율|오류 수|비어 있는 수|0.1% 분위수|1% 분위수|5% 분위수|25% 분위수|50% 분위수|75% 분위수|95% 분위수|99% 분위수|99.9% 분위수|평균|표준 편차|Variance|왜곡도|첨도
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Case Number|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Block|FieldType.STRING|004XX S KILBOURN AVE|113XX S 어떤 AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primary Type|FieldType.STRING|거짓 연습|THEFT|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
설명|FieldType.STRING|가짜 확인|500 달러를 통해|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Location Description|FieldType.STRING||학교, 공용 빌드|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Arrest|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domestic|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
비트|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
구역|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Community Area|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI Code|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
X Coordinate|FieldType.INTEGER|1.16309e+06|1.18336e+06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
Y Coordinate|FieldType.INTEGER|1.8315e+06|1.908e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
Year|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Updated On|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
위도|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
경도|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
위치|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>누락 값 입력

데이터 집합에서 null 값과 NaN의 없는 콘텐츠를 포함 하는 값에 누락 된 값으로 간주 됩니다. 기계 학습 모델의 성능에 영향을 줄 또는 잘못 된 결론을 수 있습니다 이러한 합니다. 대체 값이 누락을 해결 하기 위해 일반적인 방법 중 하나는 및의 누락 된 비율이 높은 경우 유용 간단히 삭제할 수 없는 레코드 값입니다.

이전 섹션에서 생성 된 데이터 집합 프로필에서 했습니다 보면 `Latitude` 고 `Longitude` 열에 누락 값의 비율이 높으면 합니다. 이 예제에서는 평균을 계산 하 고 이러한 두 개의 열에 누락 값을 대체 합니다.

먼저, 사용 하 여 데이터 집합의 최신 정의 가져옵니다 [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) 사용 하 여 데이터를 줄이려면 및 [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)이므로 주소 하고자 하는 열만 봅니다.

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
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

다음으로 확인 합니다 `MEAN` 사용 하 여 위도 열 값을 [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) 함수입니다. 이 함수는 `group_by_columns` 매개 변수의 열 배열을 수락하여 집계 수준을 지정합니다. `summary_columns` 매개 변수는 허용 합니다 `SummaryColumnsValue` 현재 열 이름을, 새 계산된 필드 이름을 지정 하는 함수 및 `SummaryFunction` 하는 데.

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
|0|False|41.780049|

사용 하 여 대체 값을 확인 합니다 [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) imputes 열을 계산된 하는 고정 된 식에 알아보려면 `MIN`에 `MAX`를 `MEAN` 값 또는 `CUSTOM` 값입니다. `group_by_columns`를 지정하면 그룹별로 계산된 `MIN`, `MAX` 및 `MEAN`을 사용하여 누락 값이 그룹을 기준으로 입력됩니다.

합니다 [ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) column_id 문자열을 허용 및 `ReplaceValueFunction` impute 형식을 지정 합니다. 누락 된 경도 값에 대 한-87 외부 기술 자료를 기반으로 돌립니다.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

대체 단계를 함께 연결할 수 있습니다는 `ImputeMissingValuesBuilder` 를 사용 하 여 개체를 [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) 함수 클래스 [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)합니다. `impute_columns` 매개 변수는 `ImputeColumnArguments` 개체의 배열을 수락합니다.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

호출 된 [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) impute 단계 저장 함수를 사용 하 여 데이터 흐름 개체에 적용할 [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow)합니다.

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

누락 된 위도 사용 하 여 귀속 된 다음 출력 테이블에 표시 된 대로 합니다 `MEAN` 의 값 `Arrest==False` -87을 사용 하 여 그룹 및 누락 된 경도 귀속 되었습니다.

||ID|Arrest|위도|경도
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

를 사용 하 여 데이터 집합 정의 업데이트할 [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) 수행된 변환 단계를 유지 하 합니다.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Arrest|위도|경도
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>어설션 규칙 만들기

데이터 작업을 정리 하는 동안 및 프로덕션에 필요한 총 데이터의 일부만 데이터 준비는 경우가 많습니다. 결과적으로, false로 판명 있도록 하는 정리의 일부로 가정 중 일부가 될 수 있습니다. 예를 들어 지속적으로 업데이트 하는 데이터 집합에서 원래 숫자가 특정 범위 내에 포함 하는 열 광범위 한 나중에 실행할 때 값을 포함할 수 있습니다. 이러한 오류 끊어진된 파이프라인 또는 잘못 된 데이터에서 일으키는 경우가 많습니다.

파이프라인 실행 될 때 평가 되는 데이터에 대해 어설션을 만드는 데이터 집합 지원 합니다. 이러한 어설션은 계속 작동 하는지 확인 데이터에 대해 가정 정확한 수 있도록 하 고, 그렇지 않은 경우 오류를 적절 하 게 처리 하도록 합니다.

예를 들어 제한 하려는 `Latitude` 및 `Longitude` 특정 숫자 범위를 데이터 집합의 값을 [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) 메서드를 사용 하면이 경우 항상 합니다.

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

||Type|Min|max|개수|누락된 수|누락되지 않은 수|누락 백분율|오류 수|비어 있는 수|0.1% 분위수|1% 분위수|5% 분위수|25% 분위수|50% 분위수|75% 분위수|95% 분위수|99% 분위수|99.9% 분위수|평균|표준 편차|Variance|왜곡도|첨도
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Arrest|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
위도|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
경도|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

프로필에서 표시 합니다 `Error Count` 에 대 한는 `Longitude` 열은 3입니다. 다음 코드는 오류를 검색 하며 어떤 값 이면 어설션이 실패 하 게 데이터 집합을 필터링 합니다. 여기에서 코드를 조정 하 고 적절 하 게 데이터를 정리 합니다.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>예제 별 열 파생

데이터 집합에 대 한 고급 도구 중 하나는 원하는 결과의 예제를 사용 하 여 열을 파생 시키는 기능입니다. 이렇게 하면 의도 한 변환을 수행 하는 코드를 생성할 수 있도록 SDK 예제를 제공할 수 있습니다.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Case Number|Date|Block|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

날짜 및 시간 형식 '에서 2016으로 변환 해야 한다고 가정해 봅니다-04-04 오후 10 시-오전 12 시 '. 에 [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) 인수를에 원하는 출력의 예제를 제공 합니다 `example_data` 형식으로 매개 변수: *(원래 출력, 원하는 출력)* .

다음 코드에서는 원하는 출력의 두 가지 예 ("2016-04-04 23시 56분: 00", "2016-04-04 오후 10-오전 12 시") 및 ("2016-04-15 17시: 00", "2016-04-15 오후 4 시-오후 6 시")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

다음 표에 새 열을 Date_Time_Range 포함 지정 된 형식의 레코드를 확인 합니다.

||ID|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>유사 항목 그룹화

다양 한 원본에서 데이터를 수집할 때 철자, 대/소문자를 동일한 엔터티의 약어에 변형이 발생할 수 있습니다. 자동으로 표준화 하 고 SDK의 유사 항목 그룹화 또는 텍스트 클러스터링 기능을 사용 하 여 이러한 변형은 조정 합니다.

예를 들어 열 `inspections.business.city` 여러 형태의 도시 이름 "San Francisco"를 포함 합니다.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|빠른-N-Ezee 인도어 Foods|3861 24th St|SF|...
1|67565|태국어 Noodles 카페의 King|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|태국어 Noodles 카페의 King|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 clement St|SF|...
4|69186|프리미어 이며 & 이벤트, Inc.|1255 22nd St|S.F.|...

사용 하 여 합니다 [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) "샌프란시스코"의 정규 형식이 자동으로 검색된을 사용 하 여 열을 추가 하는 방법입니다. 인수 `source_column` 고 `new_column_name` 필요 합니다. 하는 옵션이 있습니다.

* 새 열을 만들 `similarity_score_column_name`, 원래 값과 정식 값의 모든 쌍 간의 유사성 점수를 보여 주는 합니다.

* 제공을 `similarity_threshold`를 그룹화 할 값에 대 한 최소 유사성 점수는 합니다.

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
0|16162|빠른-N-Ezee 인도어 Foods|3861 24th St|SF|San Francisco|0.814806|...
1|67565|태국어 Noodles 카페의 King|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|태국어 Noodles 카페의 King|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|...
4|69186|프리미어 이며 & 이벤트, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

결과 데이터 집합 정의에서 동일한 문자열에 "샌프란시스코" 모든 다양 한 데이터의 "San Francisco"를 나타내는 정규화 된 합니다.

이 유사 항목 그룹화 단계를 사용 하 여 최신 데이터 집합 정의에 저장 `update_definition()`합니다.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>다음 단계

* [데이터 집합 정의의 수명 주기 관리](how-to-manage-dataset-definitions.md)합니다.

* 자동화 된 기계 학습을 참조 하세요 [자습서](tutorial-auto-train-models.md) 회귀 모델 예제에 대 한 합니다.

* SDK를 참조 하세요 [개요](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 디자인 패턴 및 사용 예제입니다.

* 데이터 집합을 사용 하 여 예제를 참조 하세요. 합니다 [샘플 notebook](https://aka.ms/dataset-tutorial)합니다.
