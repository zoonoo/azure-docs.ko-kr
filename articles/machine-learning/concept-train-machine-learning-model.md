---
title: 빌드 & 학습 모델
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 모델을 학습 하는 데 사용할 수 있는 여러 가지 방법을 알아봅니다. 추정는 Scikit-배우기, TensorFlow, Keras, PyTorch 및 체 이너와 같은 인기 있는 프레임 워크를 사용 하는 쉬운 방법을 제공 합니다. Machine Learning 파이프라인을 사용 하면 간편 하 게 무인 실행을 예약 하 고, 다른 유형의 계산 환경을 사용 하 고, 워크플로의 일부를 다시 사용할 수 있습니다. 및 실행 구성은 학습 프로세스가 실행 되는 계산 대상에 대해 세부적인 제어를 제공 합니다.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c75c41012928b7bffb61a00a73f314e4c372b154
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792346"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 학습

Azure Machine Learning은 SDK를 사용 하는 code first 솔루션에서 자동화 된 Machine Learning 및 비주얼 디자이너와 같은 낮은 코드 솔루션까지 모델을 학습 하는 여러 가지 방법을 제공 합니다. 다음 목록을 사용 하 여 적합 한 학습 방법을 결정 합니다.

+ [Python 용 AZURE MACHINE LEARNING SDK](#python-sdk): python SDK는 다양 한 기능을 사용 하 여 모델을 학습 하는 여러 가지 방법을 제공 합니다.

    | 학습 방법 | 설명 |
    | ----- | ----- |
    | [실행 구성](#run-configuration) | **모델을 학습 하는 일반적인 방법은** 학습 스크립트 및 실행 구성을 사용 하는 것입니다. 실행 구성은 모델 학습에 사용 되는 학습 환경을 구성 하는 데 필요한 정보를 제공 합니다. 실행 구성, 학습 스크립트 및 계산 대상 (교육 환경)을 사용 하 여 교육 작업을 실행할 수 있습니다. |
    | [자동화된 기계 학습](#automated-machine-learning) | 자동화 된 기계 학습을 사용 하면 **광범위 한 데이터 과학 또는 프로그래밍 지식 없이 모델**을 학습할 수 있습니다. 데이터 과학 및 프로그래밍 배경을 사용 하는 사용자의 경우 알고리즘 선택 및 하이퍼 매개 변수 튜닝을 자동화 하 여 시간과 리소스를 절약 하는 방법을 제공 합니다. 자동화 된 machine learning을 사용할 때 실행 구성을 정의 하는 것에 대해 걱정할 필요가 없습니다. |
    | [추정](#estimators) | 평가기 클래스 **를 사용 하면 인기 있는 기계 학습 프레임 워크를 기반으로 모델을 쉽게 학습 시킬 수**있습니다. **Scikit**에 대 한 평가기, **PyTorch**, **TensorFlow**및 **체 이너**클래스가 있습니다. 또한 전용 평가기 클래스가 없는 프레임 워크에서 사용할 수 있는 일반 평가기 있습니다. 추정를 사용 하는 경우 실행 구성을 정의 하는 것에 대해 걱정할 필요가 없습니다. |
    | [Machine learning 파이프라인](#machine-learning-pipeline) | 파이프라인은 다른 학습 방법이 아니라 워크플로의 일부로 서 학습을 포함할 수 있는 **모듈 재사용 가능 단계를 사용 하 여 워크플로를 정의 하는 방법입니다**. 기계 학습 파이프라인은 자동화 된 machine learning, 추정 및 실행 구성을 사용 하 여 모델을 학습 하도록 지원 합니다. 파이프라인은 학습에 특별히 집중 되지 않으므로 파이프라인을 사용 하는 이유는 다른 학습 방법 보다 더 다양 합니다. 일반적으로 다음과 같은 경우에 파이프라인을 사용할 수 있습니다.<br>* 장기 실행 교육 작업 또는 데이터 준비와 같은 **무인 프로세스를 예약** 하려고 합니다.<br>* 다른 유형의 계산 리소스 및 저장소 위치에서 조정 된 **여러 단계** 를 사용 합니다.<br>* 파이프라인은 특정 시나리오 (예: 재 학습 또는 일괄 처리 점수 매기기)에 **재사용 가능한 템플릿으로** 사용 합니다.<br>* 워크플로의 **데이터 원본, 입력 및 출력을 추적 하 고 버전을 관리** 합니다.<br>* 워크플로는 **특정 단계에 독립적으로 작동 하는 여러 팀에 의해 구현**됩니다. 그런 다음 워크플로를 구현 하기 위해 파이프라인에서 단계를 함께 조인할 수 있습니다. |

+ [Python 용 AZURE MACHINE LEARNING sdk](#r-sdk): sdk는 reticulate 패키지를 사용 하 여 Azure Machine Learning의 python SDK에 바인딩합니다. 이를 통해 모든 R 환경에서 Python SDK에 구현 된 핵심 개체 및 메서드에 액세스할 수 있습니다.

+ **디자이너**: Azure Machine Learning designer (미리 보기)는 개념 증명을 빌드하기 위한 Machine Learning 또는 코딩 경험이 거의 없는 사용자를 위한 쉬운 진입점을 제공 합니다. 끌어서 놓기 웹 기반 UI를 사용 하 여 모델을 학습 시킬 수 있습니다. 디자인의 일부로 Python 코드를 사용 하거나 코드를 작성 하지 않고 모델을 학습 시킬 수 있습니다.

+ **Cli**: MACHINE learning cli는 Azure Machine Learning를 사용 하는 일반적인 태스크에 대 한 명령을 제공 하며, **작업을 스크립팅 및 자동화**하는 데 주로 사용 됩니다. 예를 들어 학습 스크립트나 파이프라인을 만들었으면 CLI를 사용 하 여 일정에 따라 또는 학습에 사용 된 데이터 파일을 업데이트할 때 학습 실행을 시작할 수 있습니다. 학습 모델의 경우 학습 작업을 제출 하는 명령을 제공 합니다. 실행 구성 또는 파이프라인을 사용 하 여 작업을 제출할 수 있습니다.

이러한 각 학습 방법은 학습에 다양 한 유형의 계산 리소스를 사용할 수 있습니다. 이러한 리소스를 집합적으로 [__계산 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 합니다. 계산 대상은 로컬 컴퓨터 또는 클라우드 리소스 (예: Azure Machine Learning 계산, Azure HDInsight 또는 원격 가상 컴퓨터) 일 수 있습니다.

## <a name="python-sdk"></a>Python SDK

Python 용 Azure Machine Learning SDK를 사용 하면 Azure Machine Learning를 사용 하 여 기계 학습 워크플로를 빌드하고 실행할 수 있습니다. 대화형 Python 세션, Jupyter 노트북, Visual Studio Code 또는 다른 IDE에서 서비스와 상호 작용할 수 있습니다.

* [Python 용 Azure Machine Learning SDK 란?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [SDK 설치/업데이트](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning용 개발 환경 구성](how-to-configure-environment.md)

### <a name="run-configuration"></a>실행 구성

[Runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)을 사용 하 여 Azure Machine Learning를 사용 하는 일반적인 학습 작업을 정의할 수 있습니다. 그런 다음 실행 구성이 계산 대상에서 모델을 학습 하는 학습 스크립트와 함께 사용 됩니다.

로컬 컴퓨터에 대 한 실행 구성으로 시작한 다음 필요에 따라 클라우드 기반 계산 대상에 대해 하나로 전환할 수 있습니다. 계산 대상을 변경할 때 사용 하는 실행 구성만 변경 합니다. 또한 실행은 입력, 출력 및 로그와 같은 학습 작업에 대 한 정보를 기록 합니다.

* [실행 구성 이란?](concept-azure-machine-learning-architecture.md#run-configurations)
* [자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)
* [예제: 학습 모델의 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [방법: 모델 학습을 위한 계산 대상 설정 및 사용](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

반복, 하이퍼 매개 변수 설정, 기능화 및 기타 설정을 정의 합니다. 학습 하는 동안 Azure Machine Learning는 서로 다른 알고리즘과 매개 변수를 병렬로 시도 합니다. 사용자가 정의한 종료 조건에 도달 하면 학습을 중지 합니다. 추정를 사용 하는 경우 실행 구성을 정의 하는 것에 대해 걱정할 필요가 없습니다.

> [!TIP]
> Python SDK 외에도 [Azure Machine Learning studio](https://ml.azure.com)를 통해 자동화 된 ML을 사용할 수 있습니다.

* [자동화된 Machine Learning이란?](concept-automated-ml.md)
* [자습서: 자동화 된 machine learning을 사용 하 여 첫 번째 분류 모델 만들기](tutorial-first-experiment-automated-ml.md)
* [자습서: 자동화 된 machine learning을 사용 하 여 taxi 정의 요금 예측](tutorial-auto-train-models.md)
* [예: 자동화 된 기계 학습에 대 한 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [방법: Python에서 자동화 된 ML 실험 구성](how-to-configure-auto-train.md)
* [방법: 시계열 예측 모델 자동 학습](how-to-auto-train-forecast.md)
* [방법: Azure Machine Learning studio를 사용 하 여 자동화 된 기계 학습 실험 만들기, 탐색 및 배포](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>추정

추정를 사용 하면 인기 있는 ML 프레임 워크를 사용 하 여 모델을 쉽게 학습 시킬 수 있습니다. **Scikit**, **PyTorch**, **TensorFlow**또는 **체 이너**를 사용 하는 경우 학습에 평가기를 사용 하는 것을 고려해 야 합니다. 또한 전용 평가기 클래스가 없는 프레임 워크에서 사용할 수 있는 일반 평가기 있습니다. 추정를 사용 하는 경우 실행 구성을 정의 하는 것에 대해 걱정할 필요가 없습니다.

* [추정 란?](concept-azure-machine-learning-architecture.md#estimators)
* [자습서: Azure Machine Learning에서 MNIST 데이터와 scikit-learn을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)
* [예: 추정를 사용 하는 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [방법: 학습에서 추정 만들기](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Machine learning 파이프라인

Machine learning 파이프라인은 앞서 언급 한 학습 메서드 (실행 구성, 추정 및 자동화 된 machine learning)를 사용할 수 있습니다. 파이프라인은 워크플로를 만드는 방법에 대 한 것 이므로 모델 학습 보다 더 많이 포함 됩니다. 파이프라인에서 자동화 된 machine learning, 추정 또는 실행 구성을 사용 하 여 모델을 학습 시킬 수 있습니다.

* [Azure Machine Learning의 ML 파이프라인 이란?](concept-ml-pipelines.md)
* [Azure Machine Learning SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md)
* [자습서: 일괄 처리 점수 매기기에 Azure Machine Learning 파이프라인 사용](tutorial-pipeline-batch-scoring-classification.md)
* [예: 기계 학습 파이프라인에 대 한 Jupyter Notebook 예제](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [예: 자동화 된 machine learning을 사용 하는 파이프라인](https://aka.ms/pl-automl)
* [예: 추정를 사용 하는 파이프라인](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

R SDK를 사용 하면 Azure Machine Learning에서 R 언어를 사용할 수 있습니다. SDK는 reticulate 패키지를 사용 하 여 Azure Machine Learning의 Python SDK에 바인딩합니다. 이를 통해 모든 R 환경에서 Python SDK에 구현 된 핵심 개체 및 메서드에 액세스할 수 있습니다.

자세한 내용은 다음 아티클을 참조하세요.

* [자습서: 로지스틱 회귀 모델 만들기](tutorial-1st-r-experiment.md)
* [R 참조용 Azure Machine Learning SDK](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

디자이너를 사용 하면 웹 브라우저에서 끌어서 놓기 인터페이스를 사용 하 여 모델을 학습 시킬 수 있습니다.

+ [디자이너 란 무엇 인가요?](concept-designer.md)
+ [자습서: 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)
+ [회귀: 예측 가격](how-to-designer-sample-regression-automobile-price-basic.md)
+ [분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
+ [분류: 변동, 욕구 및 업 판매 예측](how-to-designer-sample-classification-churn.md)
+ [사용자 지정 R 스크립트를 사용 하 여 분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
+ [텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

Machine learning CLI는 Azure CLI에 대 한 확장입니다. Azure Machine Learning 작업을 위한 플랫폼 간 CLI 명령을 제공 합니다. 일반적으로 CLI를 사용 하 여 기계 학습 모델 학습 등의 작업을 자동화할 수 있습니다.

* [Azure Machine Learning에 CLI 확장 사용](reference-azure-machine-learning-cli.md)
* [Azure의 MLOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>다음 단계

[학습 환경을 설정](how-to-set-up-training-targets.md)하는 방법에 대해 알아봅니다.
