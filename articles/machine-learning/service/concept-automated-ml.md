---
title: 자동화된 ML 알고리즘 선택 및 튜닝
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service에서 알고리즘을 자동으로 선택하고, 모델에 가장 적합한 알고리즘을 선택하기 위해 사용자가 제공한 매개 변수 및 기준에 따라 모델을 생성하여 시간을 절약하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: a489d1a282c924ec1df658a0244745b225f7123e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251268"
---
# <a name="what-is-automated-machine-learning"></a>자동화된 Machine Learning이란?

자동화된 Machine Learning은 정의된 대상 기능을 사용하여 데이터를 학습하고, 알고리즘 및 선택한 기능 조합을 반복하면서 학습 점수에 따라 데이터에 가장 적합한 모델을 자동으로 선택하는 프로세스입니다. 기존의 Machine Learning 모델 개발 프로세스는 리소스가 상당히 많이 필요하며, 수십 가지 모델을 실행하고 결과를 비교하는 데 많은 도메인 지식과 시간이 필요합니다. 자동화된 Machine Learning은 실행할 실험 시간 및 블랙리스트에 넣을 모델과 같이 실험에 대해 정의한 목표 및 제약 조건에 따라 조정된 모델을 생성하여 이 프로세스를 단순화합니다.

## <a name="how-it-works"></a>작동 방법

1. 해결하려는 Machine Learning 문제의 유형을 구성합니다. 지원되는 감독 방식 학습의 범주는 다음과 같습니다.
   + 분류
   + 회귀
   + 예측

   자동화된 Machine Learning은 일반 공급되지만 **예측 기능은 아직 공용 미리 보기로 제공됩니다.**

   학습 시에 Azure Machine Learning이 사용해 볼 수 있는 [모델 목록](how-to-configure-auto-train.md#select-your-experiment-type)을 참조하세요.

1. 학습 데이터의 원본과 형식을 지정합니다. 데이터는 레이블이 지정되어 있어야 하며 개발 환경이나 Azure Blob Storage에 저장할 수 있습니다. 개발 환경에 데이터를 저장하는 경우에는 학습 스크립트와 같은 디렉터리에 저장해야 합니다. 이 디렉터리는 학습용으로 선택하는 계산 대상에 복사됩니다.

    학습 스크립트에서는 데이터를 Numpy 배열이나 Pandas 데이터 프레임으로 읽어들일 수 있습니다.

    학습 및 유효성 검사 데이터 선택을 위한 분할 옵션을 구성할 수도 있고, 별도의 학습 및 유효성 검사 데이터 집합을 지정할 수도 있습니다.

1. 모델을 학습시키는 데 사용되는 [계산 대상](how-to-set-up-training-targets.md)을 구성합니다.

1. 자동화된 Machine Learning을 구성합니다. 이 구성 과정에서는 Azure Machine Learning이 여러 모델을 반복 학습하는 과정에서 사용되는 매개 변수, 하이퍼 매개 변수 설정, 그리고 최적 모델 결정 시에 확인할 메트릭을 제어합니다. 

1. 학습 실행을 제출합니다.

Azure Machine Learning 서비스는 학습 중에 각기 다른 알고리즘과 매개 변수 사용을 시도하는 여러 파이프라인을 만듭니다. 그리고 사용자가 입력한 반복 횟수 제한에 도달하거나, 사용자가 지정한 메트릭의 대상 값에 도달하면 학습을 중지합니다.

[ ![자동화된 Machine Learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

실행 중에 수집된 메트릭을 포함하는 기록된 실행 정보를 검사할 수 있습니다. 학습 실행에서는 모델 및 데이터 전처리를 포함하는 Python 직렬화 개체(`.pkl` 파일)도 생성됩니다.

## <a name="model-explainability"></a>모델 설명

자동화된 Machine Learning의 일반적인 단점은 엔드 투 엔드 프로세스를 볼 수 없다는 것입니다. Azure Machine Learning을 사용하면 모델에 대한 자세한 정보를 확인할 수 있으므로 백 엔드에서 실행되는 대상에 대한 투명성이 높아집니다. 선형 회귀와 같은 일부 모델은 매우 단순한 것으로 간주되므로 이해하기 쉽습니다. 그러나 더 많은 기능을 추가하고 좀 더 복잡한 Machine Learning 모델을 사용하면서 이해하는 것이 점점 더 어려워집니다. Machine Learning의 투명성과 관련된 두 가지 주요 측면은 다음과 같습니다.

1. Machine Learning 파이프라인과 데이터 전처리/기능화, 하이퍼 매개 변수 값을 포함하는 관련된 모든 단계 인식
1. 입력 변수("기능"이라고도 함)와 모델 출력 간 관계 이해  각 기능이 예측 값에 미치는 영향의 크기 및 방향을 잘 파악하면 모델을 더 잘 이해하고 설명할 수 있습니다. 이를 기능 중요도라고 합니다.

선택한 파이프라인에 대해 전역 기능 중요도 요청 시 사후 학습을 사용하도록 설정하거나 자동화된 ML 학습의 일부로 모든 파이프라인에 사용할 수 있도록 설정할 수 있습니다.  이 기능은 미리 보기 기능이며, Microsoft는 ML 모델을 더 잘 이해하는 데 도움이 되는 풍부한 정보를 제공하기 위해 지속적으로 투자하고 있습니다.  

Azure Machine Learning에서 모델 설명을 실험해 보려면 이 [샘플 노트](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb)를 따르세요.

## <a name="next-steps"></a>다음 단계

예제를 통해 자동화된 Machine Learning을 사용하는 모델을 작성하는 방법을 알아봅니다.
+ [샘플: Jupyter Notebook을 사용하여 Azure Machine Learning Service 검색](samples-notebooks.md#automated-ml-setup)

+ [자습서: Azure 자동화된 Machine Learning을 사용하여 자동으로 분류 모델 학습시키기](tutorial-auto-train-models.md)

+ [원격 리소스에 대한 자동 학습 사용](how-to-auto-train-remote.md)

+ [자동 학습용 설정 구성](how-to-configure-auto-train.md)
