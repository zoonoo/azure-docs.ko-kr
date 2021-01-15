---
title: 오픈 소스 machine learning 통합
titleSuffix: Azure Machine Learning
description: 오픈 소스 Python 기계 학습 프레임 워크를 사용 하 여 Azure Machine Learning에서 종단 간 기계 학습 솔루션을 학습, 배포 및 관리 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223077"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Azure Machine Learning 프로젝트와의 오픈 소스 통합

오픈 소스 Python machine learning 라이브러리 및 플랫폼을 사용 하 여 Azure Machine Learning에서 종단 간 기계 학습 프로세스를 학습, 배포 및 관리할 수 있습니다.  Jupyter 노트북 및 Visual Studio Code 같은 개발 도구를 사용 하 여 Azure Machine Learning에서 기존 모델 및 스크립트를 활용 합니다.  

이 문서에서는 이러한 오픈 소스 라이브러리 및 플랫폼에 대해 자세히 알아보세요.

## <a name="train-open-source-machine-learning-models"></a>오픈 소스 기계 학습 모델 학습

Machine learning 학습 프로세스에는 작업을 수행 하거나 문제를 해결 하기 위해 데이터에 대 한 알고리즘의 응용 프로그램이 포함 됩니다. 문제에 따라 작업 및 데이터에 가장 적합 한 다양 한 알고리즘을 선택할 수 있습니다. 기계 학습의 여러 분기에 대 한 자세한 내용은 [심층 학습 vs machine learning 문서](./concept-deep-learning-vs-machine-learning.md) 및 [machine learning 알고리즘 참고 자료 시트](algorithm-cheat-sheet.md)를 참조 하세요.

### <a name="preserve-data-privacy-using-differential-privacy"></a>차등 개인 정보를 사용 하 여 데이터 개인 정보 유지

Machine learning 모델을 학습 하려면 데이터가 필요 합니다. 데이터가 중요 한 경우에는 데이터를 안전 하 고 전용 데이터 인지 확인 하는 것이 중요 합니다. 차등 개인 정보는 데이터 집합에서 정보의 기밀성을 유지 하는 기술입니다. 자세히 알아보려면 [데이터 개인 정보 유지](concept-differential-privacy.md)에 대 한 문서를 참조 하세요. 

[Smartnoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) 와 같은 오픈 소스 차등 개인 정보 도구는 Azure Machine Learning 솔루션에서 [데이터의 개인 정보를 유지 하는](how-to-differential-privacy.md) 데 도움이 됩니다.

### <a name="classical-machine-learning-scikit-learn"></a>클래식 machine learning: scikit-배우기

분류, 클러스터링 및 회귀와 같은 기존 기계 학습 알고리즘 작업과 관련 된 학습 작업을 위해 Scikit-배우기와 같은 항목을 사용할 수 있습니다. 꽃 분류 모델을 학습 하는 방법에 대 한 자세한 내용은 [Scikit를 사용 하 여 학습 하는 방법-학습 문서](how-to-train-scikit-learn.md)를 참조 하세요.

### <a name="neural-networks-pytorch-tensorflow-keras"></a>신경망: PyTorch, TensorFlow, Keras

기계 학습의 하위 집합인 신경망 이라고 하는 오픈 소스 기계 학습 알고리즘은 Azure Machine Learning에서 심층 학습 모델을 학습 하는 데 유용 합니다.

오픈 소스 심층 학습 프레임 워크 및 방법 가이드는 다음과 같습니다.

 *  [PyTorch](https://github.com/pytorch/pytorch): [전송 학습을 사용 하 여 심층 학습 이미지 분류 모델 학습](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [TensorFlow를 사용 하 여 필기 숫자 인식](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [keras를 사용 하 여 이미지를 분석 하는 신경망 빌드](how-to-train-keras.md)

처음부터 심층 학습 모델을 학습 하려면 많은 양의 시간, 데이터 및 계산 리소스가 필요 합니다. 전송 학습을 사용 하 여 학습 프로세스의 바로 가기를 만들 수 있습니다. 이전 학습은 한 가지 문제를 해결 하기 위해 얻은 정보를 다른 문제에 적용 하는 기술입니다. 즉 기존 모델을 사용할 수 있습니다. 전송 학습에 대해 자세히 알아보려면 [심층 학습 vs machine learning 문서](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) 를 참조 하세요.

### <a name="reinforcement-learning-ray-rllib"></a>보충 learning: 광선 RLLib

보충 learning은 작업, 상태 및 보상을 사용 하 여 모델을 학습 하는 인공 지능 기술입니다. 보충 학습 에이전트는 환경의 현재 상태에 따라 지정 된 보상을 최대화 하는 미리 정의 된 작업 집합을 사용 하는 방법을 알아봅니다. 

[광선 RLLib](https://github.com/ray-project/ray) 프로젝트에는 학습 프로세스 전체에서 확장성을 높일 수 있는 기능 집합이 있습니다. 반복적인 프로세스는 보충 학습 에이전트에서 작업을 수행 하는 최적의 방법을 배울 수 있는 시간 및 리소스 집약적입니다.  또한 레이 RLLib는 TensorFlow 및 PyTorch와 같은 심층 학습 프레임 워크를 기본적으로 지원 합니다.  

Azure Machine Learning에서 광선 RLLib를 사용 하는 방법을 알아보려면 [보충 학습 모델을 학습](how-to-use-reinforcement-learning.md)하는 방법을 참조 하세요.

### <a name="monitor-model-performance-tensorboard"></a>모델 성능 모니터링: TensorBoard

단일 모델 또는 여러 모델을 학습 하려면 원하는 메트릭을 시각화 하 고 검사 하 여 모델이 예상 대로 수행 되도록 해야 합니다. [Azure Machine Learning에서 TensorBoard을 사용 하 여 실험 메트릭을 추적 하 고 시각화할](./how-to-monitor-tensorboard.md) 수 있습니다.

### <a name="frameworks-for-interpretable-and-fair-models"></a>해석할 때 사용할 프레임 워크 및 공평 모델

기계 학습 시스템은 은행, 교육, 의료 등의 다양 한 사회 영역에서 사용 됩니다. 따라서 이러한 시스템이 의도 하지 않은 결과를 방지 하기 위해 수행 하는 예측 및 권장 사항에 대해 책임을 지는 것이 중요 합니다.

[InterpretML](https://github.com/interpretml/interpret/) 및 Fairlearn와 같은 오픈 소스 프레임 워크 ( https://github.com/fairlearn/fairlearn) Azure Machine Learning 작업 하 여 보다 투명 하 고 동등 기계 학습 모델을 만듭니다.

공평 하 고 해석할 수 있는 모델을 작성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Machine Learning의 모델 해석력](how-to-machine-learning-interpretability.md)
- [기계 학습 모델 해석 및 설명](how-to-machine-learning-interpretability-aml.md)
- [AutoML 모델 설명](how-to-machine-learning-interpretability-automl.md)
- [기계 학습 모델의 공평 완화](concept-fairness-ml.md)
- [자산 모델 공평에 Azure Machine Learning 사용](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>모델 배포

모델을 학습 하 고 프로덕션에 사용할 준비가 되 면 배포 방법을 선택 해야 합니다. Azure Machine Learning 다양 한 배포 대상을 제공 합니다. 자세한 내용은 [위치 및 배포 방법 문서](./how-to-deploy-and-where.md)를 참조 하세요.

### <a name="standardize-model-formats-with-onnx"></a>ONNX를 사용 하 여 모델 형식 표준화

학습 후에는 학습 된 매개 변수와 같은 모델의 콘텐츠를 직렬화 하 여 파일에 저장 합니다. 각 프레임 워크에는 자체 serialization 형식이 있습니다. 다른 프레임 워크와 도구를 사용 하 여 작업할 때는 프레임 워크의 요구 사항에 따라 모델을 배포 해야 합니다. 이 프로세스를 표준화 하기 위해 오픈 신경망 교환 (ONNX) 형식을 사용할 수 있습니다. ONNX는 인공 지능 모델을 위한 오픈 소스 형식입니다. ONNX에서는 프레임워크 간의 상호 운용성을 지원합니다. 즉, PyTorch와 같은 널리 사용 되는 여러 기계 학습 프레임 워크 중 하나에서 모델을 학습 하 고, ONNX 형식으로 변환 하 고, ML.NET와 같은 다른 프레임 워크에서 ONNX 모델을 사용할 수 있습니다.

ONNX 및 ONNX 모델을 사용 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [ONNX를 사용 하 여 ML 모델 만들기 및 가속화](concept-onnx.md)
- [.NET 응용 프로그램에서 ONNX 모델 사용](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>컨테이너로 모델 패키지 및 배포

Docker와 같은 컨테이너 기술은 모델을 웹 서비스로 배포 하는 한 가지 방법입니다. 컨테이너는 재현 가능한 소프트웨어 환경을 빌드하고 오케스트레이션 할 수 있는 플랫폼 및 리소스를 독립적으로 제공 합니다. 이러한 핵심 기술을 사용 하 여 [미리](./how-to-use-environments.md)구성 된 환경, [미리 구성 된 컨테이너 이미지](./how-to-deploy-custom-docker-image.md) 또는 사용자 지정 항목을 사용 하 여 [Kubernetes 클러스터](./how-to-deploy-azure-kubernetes-service.md?tabs=python)와 같은 컴퓨터 학습 모델을 배포할 수 있습니다. GPU 집약적 워크플로의 경우 NVIDIA Triton 유추 서버와 같은 도구를 사용 하 여 [gpu를 사용 하 여 예측을 만들](how-to-deploy-with-triton.md?tabs=python)수 있습니다.

### <a name="secure-deployments-with-homomorphic-encryption"></a>Homomorphic 암호화를 사용 하 여 보안 배포

배포 보안은 배포 프로세스의 중요 한 부분입니다. [암호화 된 추론 서비스를 배포](how-to-homomorphic-encryption-seal.md)하려면 `encrypted-inference` 오픈 소스 Python 라이브러리를 사용 합니다. `encrypted inferencing`패키지는 homomorphic 암호화 라이브러리인 [Microsoft 봉인](https://github.com/Microsoft/SEAL)을 기반으로 하는 바인딩을 제공 합니다.

## <a name="machine-learning-operations-mlops"></a>MLOps (Machine Learning 작업)

기계 학습에 대 한 DevOps로 일반적으로 사용 되는 MLOps (Machine Learning 작업)를 사용 하면 보다 투명 하 고 탄력적 이며 재현 가능한 기계 학습 워크플로를 빌드할 수 있습니다. MLOps에 대해 자세히 알아보려면 [mlops 정의 문서](./concept-model-management-and-deployment.md) 를 참조 하세요. 

CI (지속적인 통합) 및 CD (지속적인 배포)와 같은 DevOps 관행을 사용 하 여 종단 간 기계 학습 수명 주기를 자동화 하 고이를 중심으로 거 버 넌 스 데이터를 캡처할 수 있습니다. [GitHub 작업에서 기계 학습 CI/CD 파이프라인](./how-to-github-actions-machine-learning.md) 을 정의 하 여 Azure Machine Learning 교육 및 배포 작업을 실행할 수 있습니다. 

소프트웨어 종속성, 메트릭, 메타 데이터, 데이터 및 모델 버전 관리는 투명 하 고 재현 가능 하며 감사할 수 있는 파이프라인을 빌드하기 위해 MLOps 프로세스의 중요 한 부분입니다. 이 작업의 경우 [Azure Databricks에서 기계 학습 모델을 학습](./how-to-use-mlflow-azure-databricks.md)하는 경우 뿐만 아니라 [Azure Machine Learning에서 mlflow를 사용할](how-to-use-mlflow.md) 수 있습니다. [MLflow 모델을 Azure 웹 서비스로 배포할](how-to-deploy-mlflow-models.md)수도 있습니다. 
