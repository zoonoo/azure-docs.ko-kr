---
title: Azure Open 데이터 집합을 사용 하 여 데이터 집합 만들기
titleSuffix: Azure Open Datasets
description: Azure Open 데이터 집합에서 Azure Machine Learning 데이터 집합을 만드는 방법에 대해 알아봅니다.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: db2e493f04d74308b21e8a63222f26b1e2af8546
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038239"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Open 데이터 집합에서 Azure Machine Learning 데이터 집합 만들기
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 로컬 또는 원격 실험의 데이터에 액세스 하기 위해 [Azure Open](https://azure.microsoft.com/services/open-datasets/) 데이터 집합에서 Azure Machine Learning 데이터 집합을 만드는 방법에 대해 알아봅니다. 

[Azure Machine Learning 데이터 집합](../machine-learning/how-to-create-register-datasets.md)을 만들어 메타 데이터의 복사본과 함께 데이터 원본 위치에 대 한 참조를 만듭니다. 데이터가 기존 위치에 남아 있으므로 추가 저장소 비용이 발생 하지 않으며 원래 데이터 원본을 변경 하지 못할 위험이 있습니다. 또한 데이터 집합은 지연 계산 되며 워크플로 성능 속도를 지원 합니다.
 
데이터 집합이 Azure Machine Learning의 전체 데이터 액세스 워크플로에 적합 한 위치를 이해 하려면 [안전 하 게 데이터 액세스](../machine-learning/concept-data.md#data-workflow) 문서를 참조 하세요.


개방형 데이터 집합은 Microsoft Azure의 클라우드에 있으며 [Azure Machine Learning PYTHON SDK](#create-datasets-with-the-sdk) 및 [Azure Machine Learning studio](#create-datasets-with-the-studio)에 모두 포함 되어 있습니다.

## <a name="why-use-azure-open-datasets"></a>Azure Open 데이터 집합을 사용 하는 이유 

Azure Open Datasets는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. 데이터 세트에는 기계 학습 모델을 학습시키고 예측 솔루션을 보강할 수 있는 날씨, 인구, 휴일, 공공 안전 및 위치에 대한 공개 도메인 데이터가 포함되어 있습니다. 

자세한 내용은 [개방형 데이터 집합 이란?](overview-what-are-open-datasets.md) 을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

데이터 집합을 만들고 작업 하려면 다음이 필요 합니다.

* Azure 구독 구독이 없으면 시작하기 전에 계정을 만드세요. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](../machine-learning/how-to-manage-workspace.md)입니다.

* Azureml 데이터 집합 패키지가 포함 된 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.

    * 통합 된 노트북 및 SDK가 이미 설치 되어 있는 완전히 구성 되 고 관리 되는 개발 환경인 [Azure Machine Learning 계산 인스턴스](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)를 만듭니다.

    **OR**

    * 자신의 Jupyter 노트북에서 작업 하 고 [이러한 지침](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)을 사용 하 여 SDK를 직접 설치 합니다.

> [!NOTE]
> 일부 데이터 집합 클래스에는 64 비트 Python과만 호환 되는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) 및 CentOS (7) 배포판 에서만 지원 됩니다.

## <a name="create-datasets-with-the-sdk"></a>SDK를 사용 하 여 데이터 집합 만들기

Azure Machine Learning Python SDK에서 개방형 데이터 집합 클래스를 통해 데이터 집합을 만들려면를 사용 하 여 패키지를 설치 했는지 확인 `pip install azureml-opendatasets` 합니다. 각 불연속 데이터 집합은 SDK에서 고유한 클래스로 표시 되며 특정 클래스는 `TabularDataset` , 또는 둘 다로 사용할 수 있습니다 `FileDataset` . 클래스의 전체 목록은 [참조 설명서](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) 를 참조 하세요.

특정 클래스를 또는로 검색 하 여 `TabularDataset` `FileDataset` 파일을 직접 조작 및/또는 다운로드할 수 있습니다. 다른 클래스는 또는 함수 중 하나를 사용 하는 경우에 **만** 데이터 집합을 가져올 수 있습니다 `get_tabular_dataset()` `get_file_dataset()` . 다음 코드 샘플에서는 이러한 형식의 클래스에 대 한 몇 가지 예를 보여 줍니다.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

개방형 데이터 집합에서 만든 데이터 집합을 등록 하면 데이터는 즉시 다운로드 되지 않지만 중앙 저장소 위치에서 요청 하는 경우 (예: 학습 중)에는 나중에 데이터에 액세스할 수 있습니다.

## <a name="create-datasets-with-the-studio"></a>Studio를 사용 하 여 데이터 집합 만들기

[Azure Machine Learning studio](https://ml.azure.com)를 사용 하 여 개방형 데이터 집합에서 데이터 집합을 만들 수도 있습니다. 작업 영역의 **자산**아래에서 **데이터 집합** 탭을 선택 합니다. **데이터 집합 만들기** 드롭다운 메뉴에서 **개방형 데이터 집합에서**를 선택 합니다.

![UI를 사용 하 여 데이터 집합 열기](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

해당 타일을 선택 하 여 데이터 집합을 선택 합니다. 검색 창을 사용 하 여 필터링 할 수 있는 옵션이 있습니다. **다음**을 선택 합니다.

![데이터 집합 선택](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

데이터 집합을 등록할 이름을 선택 하 고 필요에 따라 사용 가능한 필터를 사용 하 여 데이터를 필터링 합니다. 이 경우 공용 휴일 데이터 집합에 대해 기간을 1 년으로, 국가 코드를 미국 으로만 필터링 합니다. **만들기**를 선택합니다.

![데이터 집합 매개 변수 설정 및 데이터 집합 만들기](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

이제 **데이터 집합의**작업 영역에서 데이터 집합을 사용할 수 있습니다. 만든 다른 데이터 집합과 동일한 방법으로 사용할 수 있습니다.


## <a name="access-datasets-for-your-experiments"></a>실험에 대 한 데이터 집합 액세스

기계 학습 실험에서 ML 모델 학습을 위해 데이터 집합을 사용 합니다. [데이터 세트를 사용하여 학습시키는 방법을 알아보세요](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>노트북 예제

개방형 데이터 집합 기능의 예제와 데모는 이러한 [노트북](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [데이터 집합을 사용 하 여 모델을 학습](../machine-learning/how-to-train-with-datasets.md)합니다.

* [Azure machine learning 데이터 집합을 만듭니다](../machine-learning/how-to-create-register-datasets.md).



