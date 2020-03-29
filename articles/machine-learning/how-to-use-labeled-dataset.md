---
title: 레이블이 있는 데이터 집합 생성 및 탐색
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 레이블 지정 프로젝트에서 데이터 레이블을 내보내고 이를 기계 학습 작업에 사용하는 방법을 알아봅니다.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549490"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>레이블이 있는 Azure 기계 학습 데이터 집합 생성 및 탐색

이 문서에서는 Azure Machine Learning 데이터 레이블 프로젝트에서 데이터 레이블을 내보내고 데이터 탐색을 위한 팬더 데이터 프레임 또는 이미지 변환을 위한 Torchvision 데이터 집합과 같은 인기 있는 형식으로 로드하는 방법을 알아봅니다. 

## <a name="what-are-datasets-with-labels"></a>레이블이 있는 데이터 집합이란 무엇입니까? 

레이블이 있는 Azure 기계 학습 데이터 집합은 Label 속성이 있는 [TabularData집합이며](how-to-create-register-datasets.md#dataset-types) 레이블이 지정된 데이터 집합이라고 합니다. 이러한 특정 유형의 TabularDataset은 Azure 기계 학습 데이터 레이블 링 프로젝트의 출력으로만 만들어집니다. [다음 단계를 사용하여](how-to-create-labeling-projects.md)데이터 레이블 프로젝트를 만듭니다. 기계 학습은 이미지 분류(다중 레이블 또는 다중 클래스)에 대한 데이터 라벨링 프로젝트와 경계가 있는 상자와 함께 개체 식별을 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 [무료 계정을](https://aka.ms/AMLFree) 만드세요.
* [파이썬용 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure 기계 학습 스튜디오에](https://ml.azure.com/)대한 액세스.
    * [Azure-contrib 데이터 집합](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) 패키지 설치
* Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
* Azure 기계 학습 데이터 레이블 링 프로젝트에 대한 액세스. 레이블 프로젝트가 없는 경우 [다음 단계를](how-to-create-labeling-projects.md)사용하여 레이블을 만듭니다.

## <a name="export-data-labels"></a>데이터 레이블 내보내기 

데이터 레이블 링 프로젝트를 완료하면 레이블 프로젝트에서 레이블 데이터를 내보낼 수 있습니다. 이렇게 하면 데이터와 해당 레이블에 대한 참조를 모두 캡처하고 [COCO 형식으로](http://cocodataset.org/#format-data) 내보내거나 Azure 기계 학습 데이터 집합으로 내보낼 수 있습니다. 레이블 지정 프로젝트의 **프로젝트 세부 정보** 페이지에서 **내보내기** 단추를 사용합니다.

### <a name="coco"></a>코 코 

 COCO 파일은 *export/coco* 내의 폴더에 있는 Azure Machine Learning 작업 영역의 기본 Blob 저장소에 만들어집니다. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning 데이터 세트

Azure 기계 학습 스튜디오의 **데이터 집합** 섹션에서 내보낸 Azure 기계 학습 데이터 집합에 액세스할 수 있습니다. 데이터 집합 **세부 정보** 페이지는 파이썬에서 레이블에 액세스하는 샘플 코드도 제공합니다.

![내보낸 데이터 세트](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>레이블이 지정된 데이터 집합 탐색

표리된 데이터 세트를 팬더 데이터프레임 또는 Torchvision 데이터 집합에 로드하여 데이터 탐색을 위해 인기 있는 오픈 소스 라이브러리를 활용하고 PyTorch가 이미지 변환 및 교육을 위한 라이브러리를 제공합니다.

### <a name="pandas-dataframe"></a>pandas 데이터 프레임

레이블이 지정된 데이터 집합을 클래스의 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 메서드를 사용하여 `azureml-contrib-dataset` 팬더 데이터 프레임에 로드할 수 있습니다. 다음 셸 명령을 통해 클래스를 설치합니다. 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 네임스페이스는 서비스를 개선하기 위해 노력함에 따라 자주 변경됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.

팬더 데이터 프레임으로 변환할 때 파일 스트림에 대해 다음과 같은 파일 처리 옵션을 제공합니다.
* 다운로드: 데이터 파일을 로컬 경로로 다운로드합니다.
* 마운트: 데이터 파일을 마운트 지점에 마운트합니다. 마운트는 Azure 기계 학습 노트북 VM 및 Azure 기계 학습 계산을 포함하여 Linux 기반 계산에만 작동합니다.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>토치비전 데이터 세트

레이블이 지정된 데이터 집합을 클래스에서 [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) 메서드를 `azureml-contrib-dataset` 사용하여 Torchvision 데이터 집합으로 로드할 수 있습니다. 이 방법을 사용하려면 [PyTorch를](https://pytorch.org/) 설치해야 합니다. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>다음 단계

* 전체 교육 샘플은 [레이블 노트북이 있는 데이터 집합을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) 참조하십시오.
