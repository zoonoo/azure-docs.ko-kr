---
title: 버전 azureml 데이터 집합을 사용 하 여 데이터 집합 정의 및 관리
titleSuffix: Azure Machine Learning service
description: 데이터 집합 정의 업데이트 하 고 정의의 수명 주기를 관리 하는 방법을 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 4476bdd902f054683fa544dc44b548689f3a1881
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241873"
---
# <a name="version-and-manage-your-dataset-definitions"></a>버전 및 데이터 집합 정의 관리 합니다.

업데이트 하 고 Azure Machine Learning 데이터 집합 (미리 보기)를 사용 하 여 데이터 집합 정의 관리 하는 방법에 알아봅니다.

데이터 집합 정의 원본 데이터에서 수행 되는 변환 단계를 모두 참조를 포함 하는 데이터 집합을 준비 하는 데 사용 하는 레시피입니다. 하나의 데이터 집합 정의가 많이 있습니다 있고 각 정의 자체 수명 주기입니다.

다음 시나리오는 데이터 집합 정의 사용 하는 예제:

1. 기계 학습 데이터 집합의 현재 정의 사용 하는 파이프라인을 빌드할 수 있습니다.
1. 기본 데이터가 변경 됩니다. 새 특성이 추가 되었습니다.
1. 새 특성을 처리 하는 추가 변환을 추가 하는 사용자 정의의 새 버전을 만들어야 합니다.

이 예제에서는 기존 파이프라인 계속 원래 정의 버전을 사용 합니다. 모델 학습 파이프라인을 만드는 등 새로운 시나리오에 대 한 새 정의 버전을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독 및 작업 영역 데이터 집합 정의의 수명 주기를 관리 하려면 데이터 집합을 등록 해야 합니다.

이 문서의 예제에서 사용 되는 샘플 파일에서 제공 됩니다 [ https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv ](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv)합니다.

## <a name="create-and-update-definitions"></a>만들기 및 정의 업데이트

먼저 만들고 작업 영역을 사용 하 여 데이터 집합을 등록 합니다.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

첫 번째 데이터 집합 정의 (`version_id` = 1) 데이터 집합 만들어질 때 생성 됩니다. 그런 다음 데이터 집합 정의 업데이트할 때마다 새 version_id 최신 정의에 할당 됩니다.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Arrest</th>
      <th>위도</th>
      <th>경도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

이 정의 업데이트 이전 데이터 집합 정의 지우지 않습니다. 여전히 액세스 하 고 이전 정의 사용할 수 있습니다.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>Case Number</th>
      <th>Date</th>
      <th>Block</th>
      <th>IUCR</th>
      <th>Primary Type</th>
      <th>설명</th>
      <th>Location Description</th>
      <th>Arrest</th>
      <th>Domestic</th>
      <th>...</th>
      <th>Ward</th>
      <th>Community Area</th>
      <th>FBI Code</th>
      <th>X Coordinate</th>
      <th>Y Coordinate</th>
      <th>Year</th>
      <th>Updated On</th>
      <th>위도</th>
      <th>경도</th>
      <th>Location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>거짓 연습</td>
      <td>$ 300 통해 금융 ID 도용</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>구성에서</td>
      <td>거주</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO AVE</td>
      <td>1154</td>
      <td>거짓 연습</td>
      <td>도용 300 달러 및</td>
      <td>거주</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S 어떤 AVE</td>
      <td>1120</td>
      <td>거짓 연습</td>
      <td>위조</td>
      <td>거주</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>구성에서</td>
      <td>학교, 공용 빌드</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

결과에서 보시 데이터 집합 정의의 첫 번째 버전 이후 업데이트에 관계 없이 crime 데이터에서 모든 열을 계속 유지 합니다. Machine learning 모델 데이터 집합의 첫 번째 버전을 사용 하 고 사용 하 여 현재 있는 경우에 예를 들어 `Date` 기능 중 하나로 데이터 집합에서 열을 발생 하지 것입니다만 유지하는최신정의업데이트를사용하여모든오류`ID`, `Arrest`를 `Latitude`, `Longitude` crime 데이터의 열입니다.

## <a name="how-to-access-dataset-definitions"></a>데이터 집합 정의 액세스 하는 방법

모든 정의 목록을 가져오려면 `get_definitions()`합니다. 특정 버전의 정의 가져오려면 `get_definition()`합니다. 다음 예제에서는 모든 정의 목록을 검색 하 고 버전 1을 검색 한 다음을 보여 줍니다.

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

출력 `get_definitions()` 다음 예제와 비슷합니다.

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

정의 검색 한 후 기계 학습 파이프라인 또는 기계 학습 모델을 사용 하 여 사용할 수 있습니다.

## <a name="manage-lifecycle-of-dataset-definitions"></a>데이터 집합 정의의 수명 주기 관리

관리할 수 있습니다 각 데이터 집합 정의의 수명 주기에 독립적으로 합니다. 세 가지 단계가 있습니다 수명 주기: 활성, 사용 되지 않으며, 또는 보관 합니다.

### <a name="active"></a>활성

새 데이터 집합 정의 만들면 기본적으로 활성화 됩니다. 

### <a name="deprecate"></a>사용 중단

사용은 더 이상 권장, 가능한 경우 데이터 집합 정의 사용 되지 않습니다 수 없습니다. Machine learning 파이프라인에서 사용 되지 않는 데이터 집합 정의 사용 하면 경고 메시지가 반환 가져옵니다 않지만 실행 차단 되지 않습니다.

데이터 집합 정의 사용 중단 하는 경우에 데이터 집합 ID 및 대체 정의 대 한 데이터 집합 정의 버전 ID를 지정 합니다. 이 정보는 저장 되 고 사용 되지 않는 데이터 집합 정의 사용 하려고 할 때 경고 메시지에 사용 합니다.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>보관

어떤 이유로 든 (예: 더 이상 사용할 수 있는 데이터 원본으로 사용)에 사용 되는 정의 안 하는 경우 데이터 집합 정의 보관할 수 없습니다. Machine learning 파이프라인에는 보관 된 데이터 집합 정의 사용 하면 오류를 사용 하 여 실행이 차단 됩니다.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>다시 활성화

모든 사용 되지 않는 또는 보관 된 데이터 집합 정의 쉽게 활성화할 수 있습니다.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>다음 단계

데이터 집합을 사용 하 여 작업에 대 한 자세한 내용은 참조 하세요. [만들기 및 Azure Machine Learning 데이터 집합 등록](how-to-create-register-datasets.md)합니다.

데이터 집합을 사용 하 여 예제를 참조 하세요. 합니다 [샘플 notebook](https://aka.ms/dataset-tutorial)합니다.
