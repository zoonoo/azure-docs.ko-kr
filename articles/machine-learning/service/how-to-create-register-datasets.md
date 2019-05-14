---
title: 만들기 및 데이터 집합을 작업 영역을 사용 하 여 등록
titleSuffix: Azure Machine Learning service
description: 다양 한 원본에서 데이터 집합 만들기 및 작업 영역을 사용 하 여 데이터 집합을 등록 하는 방법을 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 938f13524b22f34f4becc936885d1611cb854df1
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510503"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>만들기 및 Azure Machine Learning 데이터 집합 (미리 보기)를 등록 합니다.

이 문서에서는 Azure Machine Learning 워크플로를 만들고 등록 데이터 집합 및 다시 사용 하기 위해 로컬 및 원격 실험에서 액세스 하는 방법을 알아봅니다.

Azure Machine Learning 데이터 집합 (미리 보기) 쉽게 액세스 하 여 데이터를 사용 하 여 작업 합니다. 모델 학습 및 파이프라인 만들기와 같은 다양 한 시나리오에서 데이터를 관리 하는 데이터 집합입니다. 사용 하는 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), 수를 사용 하 여 인기 있는 형식으로 데이터를에서, 기본 저장소 액세스, 탐색 및 데이터 준비, 다른 데이터 집합 정의의 수명 주기 관리에 사용 되는 데이터 집합 간에 비교 교육 및 프로덕션 환경에서.

## <a name="prerequisites"></a>필수 조건

만들기 및 데이터 집합을 등록 하려면 다음을 수행 해야 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* Azure Machine Learning 서비스 작업 영역. 참조 [Azure Machine Learning 서비스 작업 영역 만들기](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)합니다.

* Python 용 SDK를 학습 하는 Azure 시스템입니다. 를 설치 하거나 SDK의 최신 버전으로 업데이트를 참조 하세요 [설치 또는 업데이트 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.

## <a name="create-datasets-from-local-files"></a>로컬 파일에서 데이터 집합 만들기

사용 하 여 파일 또는 폴더 경로 지정 하 여 로컬 컴퓨터에서 파일을 로드 합니다 [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) 메서드에서 `Dataset` 클래스입니다.  이 메서드는 파일 형식을 지정 하도록 요구 하거나 인수 구문 분석 하지 않고 다음 단계를 수행 합니다.

* 유추 및 구분 기호를 설정 합니다.
* 파일의 맨 위에 있는 빈 레코드를 건너뜁니다.
* 유추 하 고 머리글 행을 설정 합니다.
* 유추 하 고 열 데이터 형식으로 변환 합니다.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

또는 사용 하 여 파일에 관한 함수 파일의 구문 분석을 명시적으로 제어 합니다. 현재 데이터 집합 SDK 지원 [구분](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)를 [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)를 [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)를 [이진](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-), 및 [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) 파일 형식입니다.

## <a name="create-datasets-from-azure-datastores"></a>Azure 데이터 저장소에서 데이터 집합 만들기

Azure 데이터 저장소에서 데이터 집합을 만들려면 야 합니다.

* 참가자 또는 소유자 등록 된 Azure 데이터 저장소에 대 한 액세스를 확인 합니다.

* 가져오기의 [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 하 고 [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) 및 `Dataset` SDK에서 패키지 합니다.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()` 메서드는 작업 영역에서 데이터를 기존 저장소를 검색 합니다.

```
dstore = Datastore.get(workspace, datastore_name)
```

사용 된 `from_delimited_files()` 메서드를 구분 기호로 분리 된 파일에서 읽기 및 등록 되지 않은 데이터 집합을 만듭니다.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ID|Case Number|Date|Block|IUCR|Primary Type|설명|Location Description|Arrest|Domestic|...|Ward|Community Area|FBI Code|X Coordinate|Y Coordinate|Year|Updated On|위도|경도|Location|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|거짓 연습|$ 300 통해 금융 ID 도용|OTHER|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT| 구성에서|거주|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|거짓 연습|도용 300 달러 및|거주|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S 어떤 AVE|1120|거짓 연습|위조|거주|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|구성에서|학교, 공용 빌드|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>작업 영역을 사용 하 여 데이터 집합에 등록

사용 합니다 [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) 메서드를 공유 하는 것에 대 한 작업 영역에 데이터 집합을 등록 하 고 조직 내에서 및 다양 한 실험에서 다시 사용 합니다.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 에 대 한 기본 매개 변수 설정과 `register()` 는 `exist_ok = False`합니다. 이 설정을 변경 하지 않고 동일한 이름의 데이터 집합을 등록 하려고 하면 오류가 발생 합니다.

합니다 `register()` 메서드 매개 변수 설정 사용 하 여 이미 등록 된 데이터 집합을 반환 합니다. `exist_ok = True`합니다.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = True
                           )
```

사용 하 여 `list()` 모든 작업 영역에서 등록 된 데이터 집합을 볼 수 있습니다.

```Python
Dataset.list(workspace_name)
```

위의 코드는 다음과 같은 결과:

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>작업 영역에서 데이터 집합에 액세스

등록 된 데이터 집합은 로컬, 원격 및 Azure Machine Learning compute와 같은 계산 클러스터에 액세스할 수 및 사용할 수 있는입니다. 실험에 대해 등록 된 데이터 집합을 재사용 하 고 계산 환경에 다음 코드를 사용 하 여 이름을 기준으로 작업 영역 및 등록 된 데이터 집합을 가져옵니다.

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>다음 단계

* [탐색 하 고 데이터 집합 준비](how-to-explore-prepare-data.md)합니다.
* [데이터 집합 정의의 수명 주기 관리](how-to-manage-dataset-definitions.md)합니다.
* 데이터 집합을 사용 하 여 예제를 참조 하세요. 합니다 [샘플 notebook](https://aka.ms/dataset-tutorial)합니다.
