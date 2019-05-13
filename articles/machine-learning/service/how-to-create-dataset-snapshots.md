---
title: 비교 (&) 데이터 스냅숏 사용 하 여 시간이 지남에 따라 재현
titleSuffix: Azure Machine Learning service
description: 시간이 지남에 따라 데이터를 비교 하 고 데이터 집합 스냅숏 사용 하 여 재현 가능성을 보장 하는 방법을 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/02/2019
ms.openlocfilehash: 51d0dcfc543834e9a8725d11fa82b566a5132a6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205010"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>데이터를 비교 하 고 스냅숏 (미리 보기)를 사용 하 여 재현 가능성을 보장

이 문서에서는 스냅숏을 만들고 관리 하는 방법을 설명 하 [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md) (데이터 집합) 캡처 하거나 시간에 따라 데이터를 비교할 수 있도록 합니다. 데이터 집합 쉽게 액세스 하 고 다양 한 시나리오에서 클라우드에 있는 데이터를 사용 하 여 작동 합니다.

**데이터 집합 스냅숏을** 만들어질 때 데이터 프로필 (요약 통계)을 저장 합니다. 재현 가능성에 대 한 스냅숏의 데이터의 복사본을 저장할 수도 수 있습니다.

>[!Important]
> 스냅숏 저장소 비용이 발생 합니다. 더 많은 저장소가 필요 스냅숏의 데이터의 복사본을 저장 합니다. 사용 하 여 [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) 더 이상 필요한 경우.

## <a name="when-to-use-snapshots"></a>스냅숏을 사용 하는 경우

스냅숏에 대 한 세 가지 주요 용도 있습니다.

+ **모델 유효성 검사**: 교육 실행 간에 또는 프로덕션 데이터에 대해 서로 다른 스냅숏 데이터 프로필을 비교 합니다.

+ **재현성 모델**: 학습 하는 동안 데이터를 포함 하는 스냅숏으로 호출 하 여 결과 재현 합니다.

+ **시간에 따른 데이터를 추적할**: 데이터 집합으로 발전 하는 방법을 참조 하세요. [프로필 비교](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--)
  
## <a name="prerequisites"></a>필수 조건

데이터 집합 스냅숏 만들기, 등록 된 Azure Machine Learning 데이터 집합 필요 합니다. 하지 않은 경우 하나를 참조 하세요 [만들기 및 등록 데이터 집합](how-to-create-register-datasets.md)합니다.

## <a name="create-dataset-snapshots"></a>데이터 집합 스냅숏 만들기

사용 하 여 데이터 집합의 스냅숏을 만들려면 [ `dataset.create_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) Azure Machine Learning SDK에서.

기본적으로 스냅숏 프로필을 저장 합니다 (요약 통계)의 최신 데이터로 [데이터 집합 정의](how-to-manage-dataset-definitions.md) 적용 합니다. 데이터 집합 정의의 데이터에 대해 정의 된 모든 변환 단계 레코드를 포함 합니다. 작동 하 여 데이터 준비를 재현할 수 있도록 유용한 방법입니다.

필요에 따라 포함할 수도 있습니다는 데이터의 복사본을 스냅숏의 추가 하 여 `create_data_snapshot = True`입니다.  이 데이터는 재현 유용할 수 있습니다.

이 예제에서는 [crime 데이터 샘플](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) 데이터 집합을 호출 하 고 `dataset_crime` 문서를 사용 하 여 만든 ["만들기 및 등록 데이터 집합"](how-to-create-register-datasets.md)합니다.

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

스냅숏 비동기적으로 만들어지기 때문에 사용 된 [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-) 프로세스를 모니터링 하는 방법입니다.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

출력

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

사용 하 여 [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-) 더 이상 필요한 경우.

## <a name="find-snapshots"></a>스냅숏 찾기

기존 스냅숏을 검색 하려면 사용 [ `get_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-)합니다.

지정 된 데이터 집합의 저장 된 스냅숏의 목록을 가져오려면 [ `get_all_snapshots()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--)합니다.

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>스냅숏에서 데이터 및 프로필 가져오기

모든 스냅숏 요약 통계를 포함 하 고 데이터의 복사본을 저장 하는 옵션을 제공 하는 데이터 집합의 프로필을 생성 합니다.

### <a name="get-the-data-profile"></a>데이터 프로필 가져오기

사용 하 여 [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) 프로필 데이터를 액세스할 수 있습니다.  예를 들어 학습 및 프로덕션 데이터를 비교 하려면이 프로필을 사용할 수 있습니다.

```Python
snapshot.get_profile()
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
Arrest|FieldType.BOOLEAN|거짓|거짓|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domestic|FieldType.BOOLEAN|거짓|거짓|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
비트|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
구|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Ward|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Community Area|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>스냅숏에서 데이터 가져오기

데이터 집합 스냅숏에 저장 된 데이터의 복사본을 가져오려면 생성 pandas DataFrame을 사용 하 여 합니다 [ `to_pandas_dataframe()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--) 메서드.

이 메서드는 스냅숏 생성 중에 데이터의 복사본을 요청 되지 않았습니다 하는 경우 실패 합니다.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Case Number|Date|Block|IUCR|Primary Type|설명|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|Location
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|거짓 연습|$ 300 통해 금융 ID 도용|OTHER|거짓|거짓|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|THEFT|구성에서|거주|거짓|거짓|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|거짓 연습|도용 300 달러 및|거주|거짓|거짓|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>다음 단계

* SDK를 참조 하세요 [개요](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 디자인 패턴 및 사용 예제입니다.

* 스냅숏 데이터 집합을 사용 하 여 예제를 참조 하세요. 합니다 [샘플 notebook](https://aka.ms/dataset-tutorial)합니다.
