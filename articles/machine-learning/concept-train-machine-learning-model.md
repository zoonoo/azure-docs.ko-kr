---
title: 모델 학습 방법
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 모델을 학습하는 데 사용할 수 있는 다양한 방법을 알아봅니다. 추정기는 Scikit 학습, 텐서 플로우, 케라스, 파이 토치 및 체인어와 같은 인기있는 프레임 워크로 작업 할 수있는 쉬운 방법을 제공합니다. 머신 러닝 파이프라인을 사용하면 무인 실행을 쉽게 예약하고, 이기종 컴퓨팅 환경을 사용하고, 워크플로의 일부를 재사용할 수 있습니다. 또한 실행 구성은 교육 프로세스가 실행되는 계산 대상을 세부적으로 제어할 수 있습니다.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129763"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 학습

Azure Machine Learning은 SDK를 사용하는 코드 첫 번째 솔루션에서 자동화된 기계 학습 및 시각적 디자이너와 같은 낮은 코드 솔루션에 이르기까지 모델을 학습하는 여러 가지 방법을 제공합니다. 다음 목록을 사용하여 적합한 교육 방법을 결정합니다.

+ [파이썬용 Azure 기계 학습 SDK](#python-sdk): 파이썬 SDK는 각각 다른 기능을 가진 모델을 학습하는 여러 가지 방법을 제공합니다.

    | 교육 방법 | 설명 |
    | ----- | ----- |
    | [실행 구성](#run-configuration) | **모델을 학습하는 일반적인 방법은** 학습 스크립트를 사용하고 구성을 실행하는 것입니다. 실행 구성은 모델을 학습하는 데 사용되는 교육 환경을 구성하는 데 필요한 정보를 제공합니다. 실행 구성, 교육 스크립트 및 계산 대상(교육 환경)을 수행하여 교육 작업을 실행할 수 있습니다. |
    | [자동화된 기계 학습](#automated-machine-learning) | 자동화된 기계 학습을 통해 **광범위한 데이터 과학 또는 프로그래밍 지식 없이도 모델을 학습할**수 있습니다. 데이터 과학 및 프로그래밍 배경을 가진 사람들을 위해 알고리즘 선택 및 하이퍼매개 변수 튜닝을 자동화하여 시간과 리소스를 절약할 수 있는 방법을 제공합니다. 자동화된 기계 학습을 사용할 때 실행 구성을 정의하는 것에 대해 걱정할 필요가 없습니다. |
    | [추정기](#estimators) | 추정기 **클래스를 통해 인기 있는 기계 학습 프레임워크를 기반으로 모델을 쉽게 학습할 수 있습니다.** **Scikit 학습,** **PyTorch,** **텐서 플로우**및 **체인어에**대한 추정클래스가 있습니다. 전용 추정기 클래스가 없는 프레임워크와 함께 사용할 수 있는 일반 추정기도 있습니다. 추정기를 사용할 때 실행 구성을 정의하는 것에 대해 걱정할 필요가 없습니다. |
    | [기계 학습 파이프라인](#machine-learning-pipeline) | 파이프라인은 다른 학습 방법이 아니라 워크플로의 일부로 교육을 포함할 수 있는 **모듈식 재사용 가능한 단계를 사용하여 워크플로를 정의하는 방법입니다.** 기계 학습 파이프라인은 자동화된 기계 학습, 추정기 및 실행 구성을 사용하여 모델을 학습하도록 지원합니다. 파이프라인은 교육에 중점을 두지 않으므로 파이프라인을 사용하는 이유는 다른 교육 방법보다 더 다양합니다. 일반적으로 다음과 같은 경우 파이프라인을 사용할 수 있습니다.<br>* 장기 실행 교육 작업 또는 데이터 준비와 같은 **무인 프로세스를 예약하려고** 합니다.<br>* 이기종 컴퓨팅 리소스 및 저장소 위치에서 조정되는 **여러 단계를** 사용합니다.<br>* 재교육 또는 일괄 처리 점수 매기기와 같은 특정 시나리오에 대해 파이프라인을 **재사용 가능한 템플릿으로** 사용합니다.<br>* 워크플로에 대한 **데이터 원본, 입력 및 출력을 추적및 버전합니다.**<br>* 워크플로는 **특정 단계에서 독립적으로 작업하는 여러 팀에 의해 구현됩니다.** 그런 다음 단계를 파이프라인에서 함께 조인하여 워크플로를 구현할 수 있습니다. |

+ [파이썬용 Azure 기계 학습 SDK](#r-sdk): SDK는 망상 패키지를 사용하여 Azure 기계 학습의 파이썬 SDK에 바인딩합니다. 이렇게하면 모든 R 환경에서 Python SDK에서 구현 된 핵심 개체 및 메서드에 액세스 할 수 있습니다.

+ **디자이너**: Azure 기계 학습 디자이너(미리 보기)는 개념 증명을 구축하거나 코딩 경험이 거의 없는 사용자를 위해 기계 학습에 대한 쉬운 진입점을 제공합니다. 그것은 당신이 드래그 앤 드롭 웹 기반 UI를 사용하여 모델을 학습 할 수 있습니다. Python 코드를 디자인의 일부로 사용하거나 코드를 작성하지 않고 모델을 학습할 수 있습니다.

+ **CLI**: 기계 학습 CLI는 Azure 기계 학습을 사용하여 일반적인 작업에 대한 명령을 제공하며 종종 **스크립팅 및 작업을 자동화하는**데 사용됩니다. 예를 들어 교육 스크립트 또는 파이프라인을 만든 후에는 CLI를 사용하여 일정에 따라 학습 실행을 시작하거나 교육에 사용되는 데이터 파일이 업데이트될 때 학습을 시작할 수 있습니다. 교육 모델의 경우 교육 작업을 제출하는 명령을 제공합니다. 실행 구성 또는 파이프라인을 사용하여 작업을 제출할 수 있습니다.

이러한 각 교육 방법은 교육에 서로 다른 유형의 계산 리소스를 사용할 수 있습니다. 전체적으로 이러한 리소스를 [__계산 대상이라고__](concept-azure-machine-learning-architecture.md#compute-targets)합니다. 계산 대상은 Azure 기계 학습 계산, Azure HDInsight 또는 원격 가상 컴퓨터와 같은 로컬 컴퓨터 또는 클라우드 리소스일 수 있습니다.

## <a name="python-sdk"></a>Python SDK

파이썬용 Azure 기계 학습 SDK를 사용하면 Azure 기계 학습을 사용하여 기계 학습 워크플로를 빌드하고 실행할 수 있습니다. 대화형 파이썬 세션, Jupyter 노트북, 비주얼 스튜디오 코드 또는 기타 IDE에서 서비스와 상호 작용할 수 있습니다.

* [파이썬을위한 Azure 기계 학습 SDK는 무엇입니까?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [SDK 설치/업데이트](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning용 개발 환경 구성](how-to-configure-environment.md)

### <a name="run-configuration"></a>실행 구성

Azure 기계 학습을 사용한 일반 교육 작업은 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)을 사용하여 정의할 수 있습니다. 그런 다음 실행 구성을 학습 스크립트와 함께 사용하여 계산 대상에서 모델을 학습합니다.

로컬 컴퓨터에 대한 실행 구성으로 시작한 다음 필요에 따라 클라우드 기반 계산 대상에 대해 구성으로 전환할 수 있습니다. 계산 대상을 변경할 때 사용하는 실행 구성만 변경합니다. 또한 실행은 입력, 출력 및 로그와 같은 교육 작업에 대한 정보를 기록합니다.

* [실행 구성이란 무엇입니까?](concept-azure-machine-learning-architecture.md#run-configurations)
* [튜토리얼 : 첫 번째 ML 모델 훈련](tutorial-1st-experiment-sdk-train.md)
* [예: 교육 모델의 주피터 노트북 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [방법: 모델 학습에 계산 대상 설정 및 사용](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

반복, 하이퍼매개 변수 설정, 위화및 기타 설정을 정의합니다. 학습 중에 Azure 기계 학습은 서로 다른 알고리즘과 매개 변수를 병렬로 시도합니다. 정의한 종료 기준에 도달하면 교육이 중지됩니다. 추정기를 사용할 때 실행 구성을 정의하는 것에 대해 걱정할 필요가 없습니다.

> [!TIP]
> 파이썬 SDK 외에도 [Azure 기계 학습 스튜디오를](https://ml.azure.com)통해 자동화 된 ML을 사용할 수도 있습니다.

* [자동화된 Machine Learning이란?](concept-automated-ml.md)
* [튜토리얼: 자동화된 기계 학습으로 첫 번째 분류 모델 만들기](tutorial-first-experiment-automated-ml.md)
* [자습서: 자동화된 기계 학습을 사용하여 택시 요금을 예측합니다.](tutorial-auto-train-models.md)
* [예: 자동화된 기계 학습을 위한 Jupyter 노트북 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [방법: 파이썬에서 자동화된 ML 실험 구성](how-to-configure-auto-train.md)
* [방법: 시계열 예측 모델 자동 학습](how-to-auto-train-forecast.md)
* [방법: Azure 기계 학습 스튜디오를 사용하여 자동화된 기계 학습 실험을 생성, 탐색 및 배포합니다.](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>추정기

추정기는 인기있는 ML 프레임 워크를 사용하여 모델을 쉽게 학습 할 수 있습니다. **Scikit 학습,** **PyTorch,** **TensorFlow**또는 **Chainer를**사용하는 경우 교육을 위해 추정기를 사용하는 것이 좋습니다. 전용 추정기 클래스가 없는 프레임워크와 함께 사용할 수 있는 일반 추정기도 있습니다. 추정기를 사용할 때 실행 구성을 정의하는 것에 대해 걱정할 필요가 없습니다.

* [추정자란 무엇입니까?](concept-azure-machine-learning-architecture.md#estimators)
* [자습서: Azure Machine Learning에서 MNIST 데이터와 scikit-learn을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)
* [예: 추정기 사용의 Jupyter 노트북 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [방법: 교육에서 추정기 만들기](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>기계 학습 파이프라인

기계 학습 파이프라인은 앞에서 언급한 교육 방법(구성, 추정기 및 자동화된 기계 학습 실행)을 사용할 수 있습니다. 파이프라인은 워크플로를 만드는 데 더 많은 부분을 하므로 모델 교육 이상의 것을 포괄합니다. 파이프라인에서 자동화된 기계 학습, 추정기 또는 실행 구성을 사용하여 모델을 학습할 수 있습니다.

* [Azure 기계 학습의 ML 파이프라인이란 무엇입니까?](concept-ml-pipelines.md)
* [Azure 기계 학습 SDK를 사용하여 기계 학습 파이프라인 생성 및 실행](how-to-create-your-first-pipeline.md)
* [자습서: 일괄 처리 점수 매기기에 Azure 기계 학습 파이프라인 사용](tutorial-pipeline-batch-scoring-classification.md)
* [예: 기계 학습 파이프라인에 대한 Jupyter 노트북 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [예: 자동화된 기계 학습을 갖춘 파이프라인](https://aka.ms/pl-automl)
* [예: 추정기가 있는 파이프라인](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

R SDK를 사용하면 Azure 기계 학습을 사용하여 R 언어를 사용할 수 있습니다. SDK는 망상 패키지를 사용하여 Azure 기계 학습의 파이썬 SDK에 바인딩합니다. 이렇게하면 모든 R 환경에서 Python SDK에서 구현 된 핵심 개체 및 메서드에 액세스 할 수 있습니다.

자세한 내용은 다음 문서를 참조하세요.

* [자습서: 로지스틱 회귀 모델 만들기](tutorial-1st-r-experiment.md)
* [R 참조용 Azure 기계 학습 SDK](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

디자이너를 사용하면 웹 브라우저에서 끌어서 놓기 인터페이스를 사용하여 모델을 학습할 수 있습니다.

+ [디자이너는 무엇입니까?](concept-designer.md)
+ [튜토리얼 : 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)
+ [회귀: 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
+ [분류: 소득 예측](how-to-designer-sample-classification-predict-income.md)
+ [분류: 이탈, 예상, 상향 판매](how-to-designer-sample-classification-churn.md)
+ [사용자 지정 R 스크립트로 분류: 항공편 지연 예측](how-to-designer-sample-classification-flight-delay.md)
+ [텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

기계 학습 CLI는 Azure CLI의 확장입니다. Azure 기계 학습 작업을 위한 플랫폼 간 CLI 명령을 제공합니다. 일반적으로 CLI를 사용하여 기계 학습 모델 교육과 같은 작업을 자동화합니다.

* [Azure 기계 학습에 CLI 확장 사용](reference-azure-machine-learning-cli.md)
* [Azure의 MlOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>다음 단계

[교육 환경을 설정하는](how-to-set-up-training-targets.md)방법에 대해 알아봅니다.
