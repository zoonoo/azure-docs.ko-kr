---
title: Azureml 데이터 집합을 사용 하 여 데이터에 액세스 하는 데이터 집합 만들기
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
ms.date: 05/21/2019
ms.openlocfilehash: b4c22caae86e20b8379db2b7feffb1ca82001239
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753161"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>만들기 및 Azure Machine Learning에서 데이터 집합 (미리 보기)에 액세스

이 문서에서는 Azure Machine Learning 데이터 집합 (미리 보기)를 만드는 방법 및 로컬 및 원격 실험에서 데이터에 액세스 하는 방법을 알아봅니다.

관리 되는 데이터 집합을 사용 하 여 다음을 수행할 수 있습니다. 
* **모델을 학습 하는 동안 데이터에 쉽게 액세스할** 기본 저장소에 다시 연결 하지 않고

* **데이터 일관성 및 재현성 확인** 실험에서 동일한 포인터를 사용 하 여: 노트북, 자동화 된 기계 학습, 파이프라인, 시각적 인터페이스

* **데이터를 공유 및 공동 작업** 다른 사용자와

* **데이터 탐색** & 데이터 스냅숏 및 버전의 수명 주기 관리

* **데이터 비교** 프로덕션 교육에


## <a name="prerequisites"></a>필수 조건

를 만들고 데이터 집합을 사용 하려면 다음을 수행 해야 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure Machine learning 서비스](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* 합니다 [설치 된 Python에 대 한 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), azureml 데이터 집합 패키지를 포함 하는 합니다.

> [!Note]
> 일부 데이터 집합 클래스 (미리 보기)에 종속 되어는 [azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지 (GA). Linux 사용자에 대 한 이러한 클래스는 다음 배포판에 대해서만 지원 됩니다.  Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS

## <a name="data-formats"></a>데이터 형식

다음 데이터에서 Azure Machine Learning 데이터 집합을 만들 수 있습니다.
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake 일반 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>데이터 세트 만들기 

데이터 집합에서 azureml 데이터 집합 패키지와 상호 작용할 수 있습니다 합니다 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 및 특히 [는 `Dataset` 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py)합니다.

### <a name="create-from-local-files"></a>로컬 파일에서 만들기

사용 하 여 파일 또는 폴더 경로 지정 하 여 로컬 컴퓨터에서 파일을 로드 합니다 [ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) 메서드에서 `Dataset` 클래스입니다.  이 메서드는 파일 형식을 지정 하도록 요구 하거나 인수 구문 분석 하지 않고 다음 단계를 수행 합니다.

* 유추 및 구분 기호를 설정 합니다.
* 파일의 맨 위에 있는 빈 레코드를 건너뜁니다.
* 유추 하 고 머리글 행을 설정 합니다.
* 유추 하 고 열 데이터 형식으로 변환 합니다.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

또는 사용 하 여 파일에 관한 함수 파일의 구문 분석을 명시적으로 제어 합니다. 


### <a name="create-from-azure-datastores"></a>Azure 데이터 저장소에서 만들기

Azure 데이터 저장소에서 데이터 집합을 만들려면:

* 했는지 확인 `contributor` 또는 `owner` 등록 된 Azure 데이터 저장소에 액세스 합니다.

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

## <a name="register-datasets"></a>등록 데이터 집합

만들기 프로세스를 완료 하려면 작업 영역을 사용 하 여 데이터 집합을 등록 합니다.

사용 된 [ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) 다른 사람과 공유 하 고 다양 한 실험에 재사용할 수 있도록 작업 영역에 데이터 집합을 등록 하는 방법입니다.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 경우 `exist_ok = False` (기본값) 이면 오류가 발생 하는 다른, 동일한 이름의 데이터 집합을 등록 하려고 합니다. 로 `True` 기존 덮어쓸 수 있습니다.

## <a name="access-data-in-datasets"></a>데이터 집합의 데이터에 액세스

등록 된 데이터 집합은 로컬, 원격 및 Azure Machine Learning compute와 같은 계산 클러스터에 액세스할 수 및 사용할 수 있는입니다. 실험에 대해 등록 된 데이터 집합을 재사용 하 고 계산 환경에 다음 코드를 사용 하 여 이름을 기준으로 작업 영역 및 등록 된 데이터 집합을 가져옵니다.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>다음 단계

* [탐색 하 고 데이터 집합 준비](how-to-explore-prepare-data.md)합니다.
* [데이터 집합 정의의 수명 주기 관리](how-to-manage-dataset-definitions.md)합니다.
* 데이터 집합을 사용 하 여 예제를 참조 하세요. 합니다 [샘플 notebook](https://aka.ms/dataset-tutorial)합니다.
