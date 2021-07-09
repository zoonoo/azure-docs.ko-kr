---
title: Azure Machine Learning이란?
description: Azure Machine Learning은 클라우드 규모로 ML 애플리케이션을 모델링하고 배포하기 위한 데이터 과학자 및 MLops에 통합된 데이터 과학 솔루션입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 89d839a9b507530eb0b3fc763b2fbba36208f79c
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112081896"
---
# <a name="what-is-azure-machine-learning"></a>Azure Machine Learning이란?

이 문서에서는 ML 모델의 학습, 배포, 자동화, 관리 및 추적에 사용할 수 있는 클라우드 기반 환경인 Azure Machine Learning에 대해 알아봅니다. 

Azure Machine Learning은 전통적인 기계 학습부터 딥 러닝, 감독 학습 및 자율 학습에 이르는 모든 종류의 기계 학습에 사용할 수 있습니다. SDK를 사용하여 Python 또는 R 코드를 작성하든, [스튜디오](#build-ml-models-in-the-studio)에서 코드 없음/낮은 코드 옵션으로 작업하든 상관없이 Azure Machine Learning 작업 영역에서 기계 학습 및 딥 러닝 모델을 빌드, 학습 및 추적할 수 있습니다. 

로컬 머신에서 학습을 시작한 다음, 클라우드로 확장할 수 있습니다. 

이 서비스는 PyTorch, TensorFlow, scikit-learn 및 Ray RLlib와 같은 널리 사용되는 딥 러닝 및 강화 오픈 소스 도구와도 상호 운용됩니다. 

> [!Tip]
> **평가판!**  Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요. Azure 서비스에서 사용 가능한 크레딧을 제공합니다. 크레딧이 소진되더라도 계정이 유지되므로 [무료 Azure 서비스](https://azure.microsoft.com/free/)를 계속 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.


## <a name="what-is-machine-learning"></a>머신 러닝이란 무엇인가요?

머신 러닝은 컴퓨터에서 기존 데이터를 사용하여 미래 동작, 결과 및 추세를 예측하는 데이터 과학 기술입니다. 머신 러닝을 사용하면 컴퓨터에서 명시적으로 프로그래밍하지 않고 학습합니다.

Machine Learning을 통한 예측은 좀 더 똑똑한 앱 및 디바이스를 만드는 데 도움이 됩니다. 온라인 쇼핑을 예로 들면, 머신 러닝은 사용자가 구매한 제품에 따라 좋아할 만한 다른 제품을 추천하는 데 도움이 됩니다. 또는 신용 카드를 읽을 때 머신 러닝은 해당 거래를 거래 데이터베이스와 비교하여 부정 행위를 검색하는 데 도움을 줍니다. 또한 로봇 진공 청소기가 방을 청소할 때, 머신 러닝은 작업이 완료되었는지 여부를 판단하도록 해줍니다.

## <a name="machine-learning-tools-to-fit-each-task"></a>각 작업에 적합한 기계 학습 도구 

Azure Machine Learning은 개발자와 데이터 과학자에게 다음을 비롯한 기계 학습 워크플로에 필요한 모든 도구를 제공합니다.
+ [Azure Machine Learning 디자이너](tutorial-designer-automobile-price-train-score.md): 모듈 끌어서 놓기를 통해 실험을 빌드한 다음, 낮은 코드 환경에서 파이프라인을 배포합니다.

+ Jupyter Notebook: [예제 Notebook](https://github.com/Azure/MachineLearningNotebooks)을 사용하거나 고유한 Notebook을 만들어 기계 학습에서 <a href="/python/api/overview/azure/ml/intro" target="_blank">Python용 Azure SDK</a> 샘플을 활용할 수 있습니다. 

+ [많은 모델 솔루션 가속기](https://aka.ms/many-models)(미리 보기)는 Azure Machine Learning을 기반으로 하며 수백 또는 수천 개의 기계 학습 모델을 학습, 운영 및 관리할 수 있습니다.

+ [Visual Studio Code용 기계 학습 확장(미리 보기)](how-to-set-up-vs-code-remote.md)은 기계 학습 프로젝트를 빌드하고 관리하기 위한 완전한 기능을 갖춘 개발 환경을 제공합니다.

+ [기계 학습 CLI](reference-azure-machine-learning-cli.md)는 명령줄에서 Azure Machine Learning 리소스로 관리하기 위한 명령을 제공하는 Azure CLI 확장입니다.

+ PyTorch, TensorFlow 및 scikit-learn과 같은 [오픈 소스 프레임워크와의 통합](concept-open-source.md)은 엔드투엔드 기계 학습 프로세스를 학습, 배포 및 관리하기 위한 다양한 기능을 포함합니다.

+ Ray RLlib를 사용하는 [보충 학습](how-to-use-reinforcement-learning.md)

[MLflow를 사용하여 메트릭을 추적](how-to-use-mlflow.md)하거나 Kubeflow를 사용하여 [엔드투엔드 워크플로 파이프라인을 빌드](https://www.kubeflow.org/docs/azure/)할 수도 있습니다.

## <a name="build-ml-models-in-with-the-python-sdk"></a>Python SDK를 사용하여 ML 모델 빌드

Azure Machine Learning <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a>를 사용하여 로컬 머신에서 학습을 시작합니다. 클라우드로 확장할 수 있습니다. 

Azure Machine Learning 컴퓨팅 및 [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)와 같이 사용할 수 있는 여러 [컴퓨팅 대상](how-to-create-attach-compute-studio.md)과 [고급 하이퍼 매개 변수 튜닝 서비스](how-to-tune-hyperparameters.md)를 통해 클라우드의 강력한 기능을 사용하여 더 나은 모델을 더 빠르게 빌드할 수 있습니다.

SDK를 사용하여 [모델 학습 및 튜닝을 자동화](tutorial-auto-train-models.md)할 수도 있습니다.

## <a name="build-ml-models-in-the-studio"></a>스튜디오에서 ML 모델 빌드

[Azure Machine Learning 스튜디오](https://studio.azureml.net)는 모델 학습, 배포 및 자산 관리를 위한 낮은 코드와 코드 없음 옵션에 대한 Azure Machine Learning의 웹 포털입니다. 스튜디오는 원활한 환경을 위해 Azure Machine Learning SDK와 통합됩니다. 자세한 내용은 [Azure Machine Learning 스튜디오란?](overview-what-is-machine-learning-studio.md)을 참조하세요.

+ **Azure Machine Learning 디자이너**

  [디자이너](concept-designer.md)를 사용하여 코드를 작성하지 않고도 기계 학습 모델을 학습하고 배포할 수 있습니다. 시작하려면 [디자이너 자습서](tutorial-designer-automobile-price-train-score.md)를 사용해 보세요. 

  ![Azure Machine Learning 디자이너의 끌어서 놓기 인터페이스 애니메이션 gif](media/concept-designer/designer-drag-and-drop.gif)

+ **실험 추적**

  스튜디오에서 [데이터 과학 실험을 추적하고 시각화](how-to-track-monitor-analyze-runs.md)하는 방법에 대해 알아봅니다. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Azure Machine Learning 스튜디오에서 세부 정보 실행":::


+ **이 외에도 많은 기능이 있습니다.**

  [ml.azure.com](https://studio.azureml.net)에서 Azure Machine Learning 스튜디오를 방문하세요.


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: 배포 및 수명 주기 관리
적합한 모델이 있는 경우 웹 서비스, IoT 디바이스 또는 Power BI에서 해당 모델을 쉽게 사용할 수 있습니다. 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서를 참조하세요.

그런 다음, [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/), [Azure Machine Learning Studio](https://ml.azure.com) 또는 [기계 학습 CLI](reference-azure-machine-learning-cli.md)를 사용하여 배포된 모델을 관리할 수 있습니다.

이러한 모델은 사용 가능하며 대량의 데이터에 대한 예측을 [실시간으로](how-to-consume-web-service.md) 또는 [비동기적으로](./tutorial-pipeline-batch-scoring-classification.md) 반환할 수 있습니다.

또한 고급 [기계 학습 파이프라인](concept-ml-pipelines.md)을 사용하여 데이터 준비부터 모델 학습, 평가 및 배포까지 각 단계에서 공동 작업을 수행할 수 있습니다. 파이프라인을 통해 다음을 수행할 수 있습니다.

* 클라우드에서 엔드투엔드 기계 학습 프로세스 자동화
* 구성 요소를 다시 사용하고 필요한 경우에만 단계를 다시 실행
* 각 단계에서 다른 컴퓨팅 리소스 사용
* 일괄 처리 점수 매기기 작업 실행

스크립트를 사용하여 기계 학습 워크플로를 자동화하려는 경우 [기계 학습 CLI](reference-azure-machine-learning-cli.md)가 제공하는 명령줄 도구를 사용하여 학습 실행 제출이나 모델 배포와 같은 일반적인 작업을 수행할 수 있습니다.

Azure Machine Learning을 시작하려면 [다음 단계](#next-steps)를 참조하세요.

## <a name="integration-with-other-services"></a>다른 서비스와 통합

Azure Machine Learning은 Azure 플랫폼의 다른 서비스와 함께 작동하며, Git 및 MLFlow 같은 오픈 소스 도구와 통합됩니다.

+ __Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__, __Azure HDInsight__ 등의 컴퓨팅 대상. 컴퓨팅 대상에 대한 자세한 내용은 [컴퓨팅 대상이란?](concept-compute-target.md)을 참조하세요.
+ __Azure Event Grid__. 자세한 내용은 [Azure Machine Learning 이벤트 사용](./how-to-use-event-grid.md)을 참조하세요.
+ __Azure Monitor__ 자세한 내용은 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.
+ __Azure Storage 계정__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__, __Azure Open Datasets__ 등의 데이터 저장소. 자세한 내용은 [Azure 스토리지 서비스에서 데이터 액세스](how-to-access-data.md) 및 [Azure Open Datasets로 데이터 세트 만들기](how-to-create-register-datasets.md)를 참조하세요.
+ __Azure Virtual Network__. 자세한 내용은 [Virtual Network 격리 및 개인 정보 개요](how-to-network-security-overview.md)를 참조하세요.
+ __Azure Pipelines__. 자세한 내용은 [기계 학습 모델의 학습 및 배포](/azure/devops/pipelines/targets/azure-machine-learning)를 참조하세요.
+ __Git 리포지토리 로그__. 자세한 내용은 [Git 통합](concept-train-model-git-integration.md)을 참조하세요.
+ __MLFlow__. 자세한 내용은 [MLflow를 사용하여 메트릭 추적](how-to-use-mlflow.md) 및 [웹 서비스로 Mlflow 모델 배포](how-to-deploy-mlflow-models.md)를 참조하세요. 
+ __Kubeflow__. 자세한 내용은 [엔드투엔드 워크플로 파이프라인 빌드](https://www.kubeflow.org/docs/azure/)를 참조하세요.

### <a name="secure-communications"></a>통신 보안

Azure Storage 계정, 컴퓨팅 대상 및 기타 리소스를 가상 네트워크 내에서 안전하게 사용하여 모델을 학습시키고 유추를 수행할 수 있습니다. 자세한 내용은 [Virtual Network 격리 및 개인 정보 개요](how-to-network-security-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작: Azure Machine Learning 서비스 시작](quickstart-create-resources.md)으로 시작합니다.  그런 다음 이러한 리소스를 사용하여 원하는 방법으로 첫 번째 실험을 만듭니다.

  + ["Hello World!" Python 스크립트 실행(1/3부)](tutorial-1st-experiment-hello-world.md)
  + [Jupyter Notebook을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)
  + [자동화된 기계 학습을 사용하여 ML 모델 학습 및 배포](tutorial-first-experiment-automated-ml.md) 
  + [Visual Studio Code에서 리소스 관리](how-to-manage-resources-vscode.md)
  + [Visual Studio Code를 사용하여 이미지 분류 모델 학습 및 배포](tutorial-train-deploy-image-classification-model-vscode.md)
  + [디자이너의 끌어서 놓기 기능을 사용하여 학습 및 배포](tutorial-designer-automobile-price-train-score.md) 
  + [기계 학습 CLI를 사용하여 모델 학습](how-to-train-cli.md)

- 머신 러닝 시나리오를 작성, 최적화 및 관리하는 [머신 러닝 파이프라인](concept-ml-pipelines.md)에 대해 알아보세요.

- 심층적인 [Azure Machine Learning 아키텍처 및 개념](concept-azure-machine-learning-architecture.md) 문서를 참조하세요.
