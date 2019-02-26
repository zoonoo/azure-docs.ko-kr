---
title: 정의
titleSuffix: Azure Machine Learning service
description: 고급 분석 애플리케이션을 클라우드 규모로 개발, 실험 및 배포하는 전문 데이터 과학자를 위한 통합 엔드투엔드 데이터 과학 솔루션인 Azure Machine Learning Service의 개요입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 36e0ca360cfbdfc77ce0a1768222f43f8864537c
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267262"
---
# <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning 서비스란?

Azure Machine Learning Service는 클라우드에서 제공하는 광범위한 모든 규모에서 Machine Learning 모델을 학습, 배포, 자동화 및 관리하는 데 사용할 수 있는 클라우드 서비스입니다.

## <a name="what-is-machine-learning"></a>기계 학습이란 무엇인가요?

Machine Learning은 컴퓨터에서 기존 데이터를 사용하여 미래 동작, 결과 및 추세를 예측하는 데이터 과학 기술입니다. Machine Learning을 사용하면 컴퓨터에서 명시적으로 프로그래밍하지 않고 학습합니다.

Machine Learning을 통한 예측은 좀 더 똑똑한 앱 및 디바이스를 만드는 데 도움이 됩니다. 온라인 쇼핑을 예로 들면, Machine Learning은 사용자가 구매한 제품에 따라 좋아할 만한 다른 제품을 추천하는 데 도움이 됩니다. 또는 신용 카드를 읽을 때 기계 학습은 해당 거래를 거래 데이터베이스와 비교하여 부정 행위를 검색하는 데 도움을 줍니다. 또한 로봇 진공 청소기가 방을 청소할 때, 기계 학습은 작업이 완료되었는지 여부를 판단하도록 해줍니다.

## <a name="what-is-azure-machine-learning-service"></a>Azure Machine Learning 서비스란?

Azure Machine Learning Service는 기계 학습 모델의 데이터 준비, 교육, 테스트, 배포, 관리 및 추적에 사용할 수 있는 클라우드 기반 환경을 제공합니다.

[ ![Azure Machine Learning Service 워크플로](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Azure Machine Learning Service는 오픈 소스 기술을 완벽하게 지원합니다. 따라서 Machine Learning 구성 요소와 함께 수많은 오픈 소스 Python 패키지를 사용할 수 있습니다. PyTorch, TensorFlow 및 scikit-learn을 그 예로 들 수 있습니다.
다양한 도구에 대한 지원을 통해 손쉽게 데이터를 대화식으로 탐색 및 준비하고 모델을 개발 및 테스트할 수 있습니다. [Jupyter Notebook](http://jupyter.org) 또는 [Visual Studio Code용 Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) 확장을 그 예로 들 수 있습니다.
Azure Machine Learning 서비스에는 간편하고 효율적이고 정확하게 모델을 만들 수 있는 [모델 생성 및 조정 자동화](tutorial-auto-train-models.md) 기능이 포함됩니다.

Azure Machine Learning Service를 사용하면 로컬 머신에서 학습을 시작한 다음, 클라우드로 확장할 수 있습니다. Azure Machine Learning 컴퓨팅 및 [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)와 같이 사용할 수 있는 여러 [컴퓨팅 대상](how-to-set-up-training-targets.md)과 [고급 하이퍼 매개 변수 튜닝 서비스](how-to-tune-hyperparameters.md)를 통해 클라우드의 강력한 기능을 사용하여 더 나은 모델을 더 빠르게 빌드할 수 있습니다.

적합한 모델이 있다면 Docker 같은 컨테이너에 쉽게 배포할 수 있습니다. 따라서 Azure Container Instances 또는 Azure Kubernetes Service에 간단히 배포할 수 있습니다. 또는 온-프레미스 또는 클라우드에서 고유한 배포의 컨테이너를 사용할 수 있습니다. 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서를 참조하세요.

테스트 중에는 배포된 모델을 관리하고 여러 실행을 추적하여 최적의 솔루션을 찾을 수 있습니다.
배포되면 모델은 많은 양의 데이터에서 [실시간](how-to-consume-web-service.md) 또는 [비동기적으로](how-to-run-batch-predictions.md) 예측을 반환할 수 있습니다.

또한 고급 [기계 학습 파이프라인](concept-ml-pipelines.md)을 사용하여 데이터 준비, 모델 학습, 평가 및 배포의 모든 단계에서 공동 작업을 수행할 수 있습니다.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스로 무엇을 할 수 있나요?

Azure Machine Learning용 <a href="https://aka.ms/aml-sdk" target="_blank">기본 Python SDK</a> 및 <a href="https://aka.ms/data-prep-sdk" target="_blank">Data Prep SDK</a>와 오픈 소스 Python 패키지를 함께 사용하여 Azure Machine Learning Service 작업 영역에서 매우 정확한 기계 학습 및 딥러닝 모델을 직접 빌드 및 학습할 수 있습니다.
오픈 소스 Python 패키지에서 다음 예제와 같이 다양한 Machine Learning 구성 요소를 선택할 수 있습니다.

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Azure Machine Learning Service는 모델을 자동으로 학습하여 사용자에 맞게 자동으로 조정할 수도 있습니다.
예제는 [자동화된 Machine Learning을 사용하여 회귀 모델 학습](tutorial-auto-train-models.md)을 참조하세요.

모델을 만든 후 이를 사용하여 테스트를 위해 로컬로 배포할 수 있는 컨테이너(예: Docker)를 만듭니다. 테스트가 완료되면 Azure Container Instances 또는 Azure Kubernetes Service에서 프로덕션 웹 서비스로 모델을 배포할 수 있습니다. 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서를 참조하세요.

그런 다음, [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 또는 [Azure Portal](https://portal.azure.com/)을 사용하여 배포된 모델을 관리할 수 있습니다.
모델의 실험을 추적하는 동안 모델 메트릭을 평가하고, 새 모델 버전을 다시 학습하고, 다시 배포할 수 있습니다.

Azure Machine Learning Service를 사용하기 시작하려면 [다음 단계](#next-steps)를 참조하세요.

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>Azure Machine Learning Service는 Machine Learning Studio와 어떻게 다른가?

Azure Machine Learning Studio는 코드 작성 없이 기계 학습 솔루션을 빌드, 테스트, 배포할 수 있는 끌어서 놓기 방식의 공동 작업 영역입니다. 미리 빌드되고 미리 구성된 Machine Learning 알고리즘과 데이터 처리 모듈을 사용합니다. 

기계 학습 모델을 쉽고 빠르게 실험 하고 솔루션에 기본 제공 기계 학습 알고리즘으로 충분한 경우 Machine Learning Studio를 사용합니다.

Python 환경에서 작업하거나, 기계 학습 알고리즘에 대한 제어 수준을 높이려 하거나, 오픈 소스 기계 학습 라이브리러를 사용하려는 경우 Machine Learning 서비스를 사용합니다.

> [!NOTE]
> Azure Machine Learning Studio에서 만든 모델은 Azure Machine Learning Service로 배포하거나 관리할 수 없습니다.

## <a name="free-trial"></a>평가판

Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 바로 [Azure Machine Learning Service의 무료 또는 유료 버전](http://aka.ms/AMLFree)을 사용해 보세요.

Azure 서비스에서 사용 가능한 크레딧을 제공합니다. 크레딧이 소진되더라도 계정이 유지되므로 [무료 Azure 서비스](https://azure.microsoft.com/free/)를 계속 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다. 또는 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)합니다. MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용](quickstart-get-started.md)(비설치 방법)하거나 [Python](quickstart-create-workspace-with-python.md)(SDK 설치 방법)에서 시작하도록 Machine Learning Service 작업 영역을 만듭니다.

- 다음 전체 자습서를 따릅니다. 
  + [Azure Machine Learning Service를 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 
  + [데이터를 준비하고 자동화된 기계 학습을 사용하여 회귀 모델 자동 학습](tutorial-data-prep.md)
  
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)를 사용하여 데이터를 준비합니다.

- 기계 학습 시나리오를 작성, 최적화 및 관리하는 [기계 학습 파이프라인](/azure/machine-learning/service/concept-ml-pipelines)에 대해 알아보세요.

- 심층적인 [Azure Machine Learning 서비스 아키텍처 및 개념](concept-azure-machine-learning-architecture.md) 문서를 참조하세요.

- 자세한 내용은 [Microsoft의 다른 Machine Learning 제품](./overview-more-machine-learning.md)을 참조하세요.
