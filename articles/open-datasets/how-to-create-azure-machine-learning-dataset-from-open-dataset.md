---
title: Azure Open Datasets에서 데이터 세트 만들기
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 Azure Machine Learning 데이터 세트를 만드는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: ed43f73de5296a465d1878647a80135aa95c065a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528185"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Open Datasets에서 Azure Machine Learning 데이터 세트 만들기

이 문서에서는 [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) 데이터 세트 및 [Azure Open Datasets](./index.yml)를 사용하여 큐레이팅된 보강 데이터를 로컬 또는 원격 기계 학습 실험으로 가져오는 방법에 관해 알아봅니다. 

[Azure Machine Learning 데이터 세트](../machine-learning/how-to-create-register-datasets.md)를 만들면 데이터 원본 위치에 대한 참조와 해당 메타데이터의 복사본을 만들 수 있습니다. 데이터 세트는 지연 평가되고 데이터는 기존 위치에 남아 있기 때문에
* 추가 스토리지 비용이 들지 않습니다.
* 실수로 데이터 원본을 변경할 위험이 없습니다. 
* ML 워크플로 성능 속도를 향상시킵니다.

Azure Machine Learning의 데이터 액세스 워크플로 전체에서 데이터 세트가 적합한 위치를 이해하려면 [안전하게 데이터 액세스](../machine-learning/concept-data.md#data-workflow) 문서를 참조하세요.

Azure Open Datasets는 예측 솔루션을 보강하고 정확성을 향상시키기 위해 시나리오 관련 기능을 추가하는 데 사용할 수 있는 큐레이팅된 퍼블릭 데이터 세트입니다. 다음과 같은 기계 학습 모델을 학습하는 데 도움이 되는 퍼블릭 도메인 데이터에 관한 내용은 [Open Datasets 카탈로그](https://azure.microsoft.com/services/open-datasets/catalog/)를 참조하세요.

* [날씨](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [인구 조사](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [휴일](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [공공 안전](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* 위치

Open Datasets는 Microsoft Azure의 클라우드에 있으며 [Azure Machine Learning Python SDK](#create-datasets-with-the-sdk)와 [Azure Machine Learning 스튜디오](#create-datasets-with-the-studio)에 모두 포함되어 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

이 문서의 내용을 진행하려면 다음 항목이 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 계정을 만드세요. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](../machine-learning/how-to-manage-workspace.md)

* `azureml-datasets` 패키지가 포함된 [Python용 Azure Machine Learning SDK가 설치](/python/api/overview/azure/ml/install)됩니다.

    * 통합 Notebook이 포함되어 있고 SDK가 이미 설치된 완전히 구성되어 관리되는 개발 환경인 [Azure Machine Learning 컴퓨팅 인스턴스](../machine-learning/how-to-create-manage-compute-instance.md)를 만듭니다.

    **OR**

    * 고유의 Python 환경에서 작업하고 [이 지침](/python/api/overview/azure/ml/install)을 사용하여 직접 SDK를 설치합니다.

> [!NOTE]
> 일부 데이터 세트 클래스는 64비트 Python과만 호환되는 azureml-dataprep 패키지에 종속됩니다. Linux 사용자의 경우 이 클래스는 Red Hat Enterprise Linux(7, 8), Ubuntu(14.04, 16.04, 18.04), Fedora(27, 28), Debian(8, 9), CentOS(7) 배포에서만 지원됩니다.

## <a name="create-datasets-with-the-sdk"></a>SDK로 데이터 세트 만들기

Python SDK에서 Azure Open Datasets 클래스를 통해 Azure Machine Learning 데이터 세트를 만들려면 `pip install azureml-opendatasets`를 사용하여 패키지를 설치했는지 확인합니다. 각 불연속 데이터 세트는 SDK에서 고유의 클래스로 표시되며 특정 클래스는 Azure Machine Learning [`TabularDataset`, `FileDataset`](../machine-learning/how-to-create-register-datasets.md#dataset-types) 또는 두 가지 모두로 사용할 수 있습니다. `opendatasets` 클래스의 전체 목록은 [참조 설명서](/python/api/azureml-opendatasets/azureml.opendatasets)를 참조하세요.

특정 `opendatasets` 클래스를 `TabularDataset` 또는 `FileDataset`으로 검색하여 파일을 직접 조작 및/또는 다운로드할 수 있습니다. 다른 클래스는 Python SDK의 `Dataset` 클래스에서 `get_tabular_dataset()` 또는 `get_file_dataset()` 함수를 사용하는 **경우에만** 데이터 세트를 가져올 수 있습니다.

다음 코드는 MNIST `opendatasets` 클래스가 `TabularDataset` 또는 `FileDataset`을 반환할 수 있음을 보여 줍니다. 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

이 예제에서는 Diabetes `opendatasets` 클래스를 `TabularDataset`으로만 사용할 수 있으므로 `get_tabular_dataset()`을 사용합니다.

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>데이터 세트 등록

작업 영역을 사용하여 Azure Machine Learning 데이터 세트를 등록하여 이를 다른 사용자와 공유하고 작업 영역의 실험에서 다시 사용할 수 있습니다. Open Datasets에서 만든 Azure Machine Learning 데이터 세트를 등록하면 데이터가 즉시 다운로드되지는 않지만 나중에 중앙 스토리지 위치에서 요청하면(예: 학습 중) 데이터에 액세스할 수 있습니다.

작업 영역을 사용하여 데이터 세트를 등록하려면 [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#register-workspace--name--description-none--tags-none--create-new-version-false-) 메서드를 사용합니다. 

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>스튜디오로 데이터 세트 만들기

또한 모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 웹 인터페이스인 [Azure Machine Learning 스튜디오](https://ml.azure.com)를 사용하여 Azure Open Datasets에서 Azure Machine Learning 데이터 세트를 만들 수 있습니다.

> [!Note]
> Azure Machine Learning 스튜디오를 통해 만든 데이터 세트는 작업 영역에 자동으로 등록됩니다.

1. 작업 영역에서 **자산** 아래에 있는 **데이터 세트** 탭을 선택합니다. **데이터 세트 만들기** 드롭다운 메뉴에서 **Open Datasets에서** 를 선택합니다.

    ![UI를 사용하여 데이터 세트 열기](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 해당 타일을 선택하여 데이터 세트를 선택합니다. 검색 창을 사용하여 필터링할 수 있습니다. **다음** 을 선택합니다.

    ![데이터 세트 선택](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 데이터 세트를 등록할 이름을 선택하고 사용 가능한 필터를 사용하여 선택적으로 데이터를 필터링합니다. 이 경우 **공휴일** 데이터 세트에서 기간을 1년으로 필터링하고 국가 코드를 미국으로만 필터링합니다. 필드 설명 및 날짜 범위와 같은 데이터 세부 정보는 [Azure Open Datasets 카탈로그](https://azure.microsoft.com/services/open-datasets/catalog)를 참조하세요. **만들기** 를 선택합니다.

    ![데이터 세트 매개 변수 설정 및 데이터 세트 만들기](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    이제 **데이터 세트** 아래의 작업 영역에서 데이터 세트를 사용할 수 있습니다. 만든 다른 데이터 세트와 동일한 방식으로 사용할 수 있습니다.


## <a name="access-datasets-for-your-experiments"></a>실험을 위한 데이터 세트 액세스

ML 모델 학습을 위한 기계 학습 실험에서 데이터 세트를 사용합니다. [데이터 세트를 사용하여 학습시키는 방법을 알아보세요](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>노트북 예제

Open Datasets 기능의 예제와 데모는 이러한 [샘플 Notebook](samples.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [첫 번째 ML 모델 학습](../machine-learning/tutorial-1st-experiment-sdk-train.md)

* [데이터 세트로 학습](../machine-learning/how-to-train-with-datasets.md)

* [Azure 기계 학습 데이터 세트 만들기](../machine-learning/how-to-create-register-datasets.md)
