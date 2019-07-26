---
title: Azureml 데이터 집합을 사용 하 여 데이터에 액세스 하는 데이터 집합 만들기
titleSuffix: Azure Machine Learning service
description: 다양 한 원본에서 데이터 집합을 만들고 작업 영역에 데이터 집합을 등록 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 765ec8291ba873c6b200cf330d82e6e2ab53357d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423122"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 집합 만들기 및 액세스 (미리 보기)

이 문서에서는 Azure Machine Learning 데이터 집합을 만드는 방법 (미리 보기)과 로컬 또는 원격 실험에서 데이터에 액세스 하는 방법에 대해 알아봅니다.

Azure Machine Learning 데이터 집합을 사용 하 여 다음을 수행할 수 있습니다. 

* 데이터 집합에서 참조 하 **는 저장소에 데이터의 단일 복사본 유지**

* 예비 데이터 분석을 통해 **데이터 분석** 

* 연결 문자열 또는 데이터 경로에 대해 걱정 하지 않고 **모델 학습 중 데이터에 쉽게 액세스**

* 다른 사용자와 **공동으로 데이터 & 공유**

## <a name="prerequisites"></a>필수 구성 요소

데이터 집합을 만들고 작업 하려면 다음이 필요 합니다.

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure Machine Learning 서비스 작업 영역](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* Azureml 데이터 집합 패키지가 포함 된 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.

> [!Note]
> 일부 데이터 집합 클래스 (미리 보기)에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지 (GA)에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 다음 배포판 에서만 지원 됩니다.  Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS입니다.

## <a name="data-formats"></a>데이터 형식

다음 형식에서 Azure Machine Learning 데이터 집합을 만들 수 있습니다.
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [pandas 데이터 프레임](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL query](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>데이터 세트 만들기 

데이터 집합을 만들면 데이터 원본 위치에 대 한 참조와 해당 메타 데이터의 복사본을 만듭니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 저장소 비용이 발생 하지 않습니다.

### <a name="create-from-local-files"></a>로컬 파일에서 만들기

`Dataset` 클래스의 [`auto_read_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) 메서드를 사용 하 여 파일 또는 폴더 경로를 지정 하 여 로컬 컴퓨터에서 파일을 로드 합니다.  이 메서드는 파일 형식 또는 구문 분석 인수를 지정 하지 않고 다음 단계를 수행 합니다.

* 구분 기호 유추 및 설정
* 파일의 맨 위에 있는 빈 레코드를 건너뜁니다.
* 머리글 행 유추 및 설정
* 열 데이터 형식 유추 및 변환

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

또는 파일 특정 함수를 사용 하 여 파일의 구문 분석을 명시적으로 제어 합니다. 


### <a name="create-from-azure-datastores"></a>Azure 데이터 저장소에서 만들기

[Azure 데이터 저장소](how-to-access-data.md)에서 데이터 집합을 만들려면 다음을 수행 합니다.

* 등록 된 Azure `contributor` 데이터 `owner` 저장소에 대 한 액세스 권한이 있는지 확인 합니다.

* 데이터 저장소에서 경로를 참조 하 여 데이터 집합 만들기 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

메서드를 `from_delimited_files()` 사용 하 여 [datareference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)에서 구분 된 파일을 읽고 등록 되지 않은 데이터 집합을 만듭니다.

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>데이터 집합 등록

만들기 프로세스를 완료 하려면 작업 영역에 데이터 집합을 등록 합니다.

메서드를 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) 사용 하 여 다른 사용자와 공유 하 고 다양 한 실험에서 다시 사용할 수 있도록 작업 영역에 데이터 집합을 등록 합니다.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> ( `exist_ok = False` 기본값) 이면 다른 이름과 같은 이름으로 데이터 집합을 등록 하려고 하면 오류가 발생 합니다. 기존를 `True` 덮어쓰려면로 설정 합니다.

## <a name="access-data-in-datasets"></a>데이터 집합의 데이터 액세스

등록 된 데이터 집합은 Azure Machine Learning 계산과 같은 계산 클러스터에서 로컬로 또는 원격으로 액세스할 수 있습니다. 실험에서 등록 된 데이터 집합에 액세스 하려면 다음 코드를 사용 하 여 작업 영역과 등록 된 데이터 집합을 이름별로 가져옵니다.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>다음 단계

* [데이터 집합을 탐색 하 고 준비](how-to-explore-prepare-data.md)합니다.
* 데이터 집합 사용에 대 한 예제는 [샘플 노트북](https://aka.ms/dataset-tutorial)을 참조 하세요.
