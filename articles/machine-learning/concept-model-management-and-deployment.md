---
title: 'MLOps: ML 모델 관리'
titleSuffix: Azure Machine Learning
description: 'Azure 기계 학습(MlOps)을 통해 모델 관리에 대해 알아봅니다. 모델을 배포, 관리 및 모니터링하여 지속적으로 개선합니다. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 7857d11c625911cd1b49dfcf0e0d612fc6a3871e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314302"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MlOps: Azure 기계 학습을 통해 모델 관리, 배포 및 모니터링

이 문서에서는 Azure 기계 학습을 사용하여 모델의 수명 주기를 관리하는 방법에 대해 알아봅니다. Azure 기계 학습은 MOP(기계 학습 작업) 접근 방식을 사용합니다. MLOps는 기계 학습 솔루션의 품질과 일관성을 향상시킵니다. 

## <a name="what-is-mlops"></a>MLOps란?

MOP(기계 학습 운영)는 워크플로의 효율성을 높이는 [DevOps](https://azure.microsoft.com/overview/what-is-devops/) 원칙및 관행을 기반으로 합니다. 예를 들어 지속적인 통합, 제공 및 배포를 예로 들 수 있습니다. MLOps는 다음을 목표로 기계 학습 프로세스에 이러한 원칙을 적용합니다.

* 모델의 빠른 실험 및 개발
* 모델의 생산 에 대한 빠른 배포
* 품질 보증

Azure 기계 학습은 다음과 같은 MlOps 기능을 제공합니다.

- **재현 가능한 ML 파이프라인을 만듭니다.** 머신 러닝 파이프라인을 사용하면 데이터 준비, 교육 및 채점 프로세스에 대해 반복 가능하고 재사용 가능한 단계를 정의할 수 있습니다.
- 모델을 교육하고 배포하기 위해 **재사용 가능한 소프트웨어 환경을 만듭니다.**
- **어디서나 모델을 등록, 패키지 및 배포합니다.** 모델을 사용하는 데 필요한 관련 메타데이터를 추적할 수도 있습니다.
- **종단 간 ML 수명 주기에 대한 거버넌스 데이터를 캡처합니다.** 기록된 정보에는 모델을 게시하는 사람, 변경된 이유 및 모델이 배포되거나 프로덕션 환경에서 사용된 시기가 포함될 수 있습니다.
- **ML 수명 주기의 이벤트에 대한 알림 및 경고.** 예를 들어 실험 완료, 모델 등록, 모델 배포 및 데이터 드리프트 감지를 예로 들 수 있습니다.
- **운영 및 ML 관련 문제에 대한 ML 응용 프로그램을 모니터링합니다.** 교육과 추론 간의 모델 입력을 비교하고, 모델별 메트릭을 탐색하고, ML 인프라에 대한 모니터링 및 경고를 제공합니다.
- **Azure 기계 학습 및 Azure 파이프라인을 사용하여 종단 간 ML 수명 주기를 자동화합니다.** 파이프라인을 사용하면 모델을 자주 업데이트하고, 새 모델을 테스트하고, 다른 응용 프로그램 및 서비스와 함께 새 ML 모델을 지속적으로 출시할 수 있습니다.

## <a name="create-reproducible-ml-pipelines"></a>재현 가능한 ML 파이프라인 만들기

Azure 기계 학습의 ML 파이프라인을 사용하여 모델 학습 프로세스와 관련된 모든 단계를 함께 스티치합니다.

ML 파이프라인에는 데이터 준비에서 기능 추출, 하이퍼매개 변수 튜닝에서 모델 평가까지의 단계가 포함될 수 있습니다. 자세한 내용은 [ML 파이프라인 을 참조하십시오.](concept-ml-pipelines.md)

[디자이너를](concept-designer.md) 사용하여 ML 파이프라인을 만드는 경우 언제든지 디자이너 페이지의 오른쪽 상단에 있는 **"..."를** 클릭한 다음 **복제를**선택할 수 있습니다. 파이프라인을 복제하면 이전 버전을 잃지 않고 파이프라인 디자인을 반복할 수 있습니다.  

## <a name="create-reusable-software-environments"></a>재사용 가능한 소프트웨어 환경 만들기

Azure 기계 학습 환경을 사용하면 프로젝트의 소프트웨어 종속성이 발전함에 따라 추적하고 재현할 수 있습니다. 환경을 사용하면 수동 소프트웨어 구성 없이 빌드를 재현할 수 있습니다.

환경은 프로젝트에 대한 pip 및 Conda 종속성을 설명하며 모델 의 학습 및 배포모두에 사용할 수 있습니다. 자세한 내용은 [Azure 기계 학습 환경입니다.](concept-environments.md)

## <a name="register-package-and-deploy-models-from-anywhere"></a>어디서나 모델 등록, 패키지 및 배포

### <a name="register-and-track-ml-models"></a>ML 모델 등록 및 추적

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.

> [!TIP]
> 등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리 컨테이너입니다. 예를 들어 여러 파일에 저장된 모델이 있는 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후 등록된 모델을 다운로드하거나 배포하고 등록된 모든 파일을 받을 수 있습니다.

등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 등록 하는 동안 추가 메타 데이터 태그를 제공할 수 있습니다. 그런 다음 모델을 검색할 때 이러한 태그가 사용됩니다. Azure 기계 학습은 Python 3.5.2 이상을 사용하여 로드할 수 있는 모든 모델을 지원합니다.

> [!TIP]
> Azure 기계 학습 외부에서 학습된 모델을 등록할 수도 있습니다.

활성 배포에 사용 중인 등록된 모델은 삭제할 수 없습니다.
자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

### <a name="profile-models"></a>프로파일 모델

Azure 기계 학습을 통해 모델을 배포할 때 생성되는 서비스의 CPU 및 메모리 요구 사항을 이해하는 데 도움이 됩니다. 프로파일링은 모델을 실행하는 서비스를 테스트하고 CPU 사용량, 메모리 사용량 및 응답 대기 시간과 같은 정보를 반환합니다. 또한 리소스 사용량에 따라 CPU 및 메모리 권장 사항을 제공합니다.
자세한 내용은 [모델 배포의](how-to-deploy-and-where.md#profilemodel)프로파일링 섹션을 참조하십시오.

### <a name="package-and-debug-models"></a>패키지 및 디버그 모델

모델을 프로덕션에 배포하기 전에 Docker 이미지로 패키징됩니다. 대부분의 경우 배포 하는 동안 백그라운드에서 자동으로 이미지 생성이 발생 합니다. 이미지를 수동으로 지정할 수 있습니다.

배포에 문제가 발생하면 문제 해결 및 디버깅을 위해 로컬 개발 환경에 배포할 수 있습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel) 및 배포 [문제 해결을](how-to-troubleshoot-deployment.md)참조하십시오.

### <a name="convert-and-optimize-models"></a>모델 변환 및 최적화

모델을 [개방형 신경망](https://onnx.ai) 교환(ONNX)으로 변환하면 성능이 향상될 수 있습니다. 평균적으로 ONNX로 변환하면 성능이 2배 향상될 수 있습니다.

Azure 기계 학습을 사용하여 ONNX에 대한 자세한 내용은 [ML 모델 만들기 및 가속화](concept-onnx.md) 문서를 참조하세요.

### <a name="use-models"></a>모델 사용

학습된 기계 학습 모델은 클라우드 또는 로컬에서 웹 서비스로 배포됩니다. Azure IoT Edge 장치에 모델을 배포할 수도 있습니다. 배포는 CPU, GPU 또는 현장 프로그래밍 가능한 게이트 어레이(FPGA)를 사용하여 추론합니다. Power BI의 모델을 사용할 수도 있습니다.

모델을 웹 서비스 또는 IoT Edge 장치로 사용하는 경우 다음 항목을 제공합니다.

* 서비스/장치에 제출된 데이터를 점수 매기는 데 사용되는 모델입니다.
* 항목 스크립트. 이 스크립트는 요청을 수락하고 모델을 사용하여 데이터 점수를 매기고 응답을 반환합니다.
* 모델 및 항목 스크립트에 필요한 pip 및 Conda 종속성을 설명하는 Azure 기계 학습 환경입니다.
* 모델 및 입력 스크립트에 필요한 텍스트, 데이터 등과 같은 추가 자산입니다.

또한 대상 배포 플랫폼의 구성을 제공합니다. 예를 들어 Azure Kubernetes 서비스에 배포할 때 VM 패밀리 유형, 사용 가능한 메모리 및 코어 수입니다.

이미지를 만들 면 Azure 기계 학습에 필요한 구성 요소도 추가 됩니다. 예를 들어 웹 서비스를 실행하고 IoT Edge와 상호 작용하는 데 필요한 자산입니다.

#### <a name="batch-scoring"></a>일괄 처리 점수 매기기
일괄 처리 점수 매기기는 ML 파이프라인을 통해 지원됩니다. 자세한 내용은 [빅 데이터에 대한 일괄 처리 예측을](how-to-use-parallel-run-step.md)참조하십시오.

#### <a name="real-time-web-services"></a>실시간 웹 서비스

다음과 같은 계산 대상을 사용하여 **웹 서비스에서** 모델을 사용할 수 있습니다.

* Azure Container Instance
* Azure Kubernetes Service
* 로컬 개발 환경

모델을 웹 서비스로 배포하려면 다음 항목을 제공해야 합니다.

* 모델의 모델 또는 앙상블입니다.
* 모델을 사용하는 데 필요한 종속성입니다. 예를 들어 요청을 수락하고 모델, conda 종속성 등을 호출하는 스크립트입니다.
* 모델을 배포하는 방법과 위치를 설명하는 배포 구성입니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md)를 참조하십시오.

#### <a name="controlled-rollout"></a>제어된 롤아웃

Azure Kubernetes 서비스에 배포할 때 제어된 롤아웃을 사용하여 다음 시나리오를 활성화할 수 있습니다.

* 배포를 위한 여러 버전의 엔드포인트 만들기
* 트래픽을 다른 버전의 끝점으로 라우팅하여 A/B 테스트를 수행합니다.
* 끝점 구성에서 트래픽 백분율을 업데이트하여 끝점 버전 간에 전환합니다.

자세한 내용은 [ML 모델의 제어형 롤아웃을](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)참조하십시오.

#### <a name="iot-edge-devices"></a>IoT 에지 장치

Azure IoT **Edge 모듈을**통해 IoT 장치와 함께 모델을 사용할 수 있습니다. IoT Edge 모듈은 하드웨어 장치에 배포되어 장치에서 추론 또는 모델 점수를 계산할 수 있습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md)를 참조하십시오.

### <a name="analytics"></a>분석

Microsoft Power BI는 데이터 분석을 위해 기계 학습 모델을 사용할 수 있도록 지원합니다. 자세한 내용은 [Power BI(미리 보기)의 Azure 기계 학습 통합을](https://docs.microsoft.com/power-bi/service-machine-learning-integration)참조하십시오.

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>종단 간 ML 수명 주기 캡처에 필요한 거버넌스 데이터 캡처

Azure ML은 메타데이터를 사용하여 모든 ML 자산의 종단 간 감사 추적추적 기능을 제공합니다.

- Azure [ML은 Git과 통합되어](how-to-set-up-training-targets.md#gitintegration) 코드가 어떤 리포지토리/분기/커밋에서 왔는지 에 대한 정보를 추적합니다.
- [Azure ML 데이터 집합을](how-to-create-register-datasets.md) 사용하면 데이터 추적, 프로필 및 버전 데이터를 추적할 수 있습니다.
- [해석기능을](how-to-machine-learning-interpretability.md) 통해 모델을 설명하고, 규정 준수를 충족하며, 주어진 입력에 대한 모델에 대한 결과를 이해할 수 있습니다.
- Azure ML 실행 기록은 모델을 학습하는 데 사용되는 코드, 데이터 및 계산의 스냅숏을 저장합니다.
- Azure ML 모델 레지스트리는 모델과 연결된 모든 메타데이터를 캡처합니다(배포가 정상인 경우 모델에서 학습된 실험, 배포중인 위치).
- [Azure 이벤트 그리드와의 통합을](concept-event-grid-integration.md) 사용하면 ML 수명 주기의 이벤트에 대해 조치를 수행할 수 있습니다. 예를 들어 모델 등록, 배포, 데이터 드리프트 및 교육(실행) 이벤트가 있습니다.

> [!TIP]
> 모델 및 데이터 집합에 대한 일부 정보가 자동으로 캡처되지만 태그를 사용하여 추가 정보를 추가할 수 __있습니다.__ 작업 영역에서 등록된 모델 및 데이터 집합을 검색할 때 태그를 필터로 사용할 수 있습니다.
>
> 데이터 집합을 등록된 모델과 연결하는 것은 선택적 단계입니다. 모델을 등록할 때 데이터 집합을 참조하는 방법에 대한 자세한 내용은 [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py) 클래스 참조를 참조하십시오.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>ML 수명 주기의 이벤트에 대한 알림, 자동화 및 경고
Azure ML은 ML 수명 주기의 이벤트를 알리고 자동화하는 데 사용할 수 있는 Azure EventGrid에 주요 이벤트를 게시합니다. 자세한 내용은 이 [문서를](how-to-use-event-grid.md)참조하십시오.


## <a name="monitor-for-operational--ml-issues"></a>운영 & ML 문제 모니터링

모니터링을 사용하면 모델로 전송되는 데이터와 반환되는 예측을 이해할 수 있습니다.

이 정보는 모델이 사용되는 방식을 이해하는 데 도움이 됩니다. 수집된 입력 데이터는 모델의 향후 버전을 학습하는 데에도 유용할 수 있습니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.

## <a name="retrain-your-model-on-new-data"></a>새 데이터에 대한 모델 재교육

새 정보를 받을 때 모델의 유효성을 검사하거나 업데이트하거나 처음부터 다시 학습하는 경우가 많습니다. 경우에 따라 새 데이터를 받는 것이 도메인의 예상 부분입니다. [데이터 집합의 데이터 드리프트(미리 보기)에서](how-to-monitor-datasets.md)설명한 것처럼 특정 센서의 변경, 계절적 효과와 같은 자연 데이터 변경 또는 다른 기능과의 관계에서 이동하는 기능 등의 측면에서 모델 성능이 저하될 수 있습니다. 

"재교육을 받아야 하는지 어떻게 알 수 있습니까?" 그러나 이전에 설명한 Azure ML 이벤트 및 모니터링 도구는 자동화를 위한 좋은 출발점입니다. 재교육하기로 결정한 후에는 다음을 수행해야 합니다. 

- 반복 가능한 자동화된 프로세스를 사용하여 데이터 사전 처리
- 새 모델 교육
- 새 모델의 출력을 이전 모델의 출력과 비교합니다.
- 미리 정의된 기준을 사용하여 이전 모델을 바꿀지 선택합니다. 

위의 단계의 주제는 재교육이 임시가 아니라 자동화되어야 한다는 것입니다. [Azure 기계 학습 파이프라인은](concept-ml-pipelines.md) 데이터 준비, 학습, 유효성 검사 및 배포와 관련된 워크플로를 만드는 데 적합합니다. [Azure 기계 학습 디자이너(미리 보기)를 사용하여 모델을 다시 학습하여](how-to-retrain-designer.md) 파이프라인과 Azure 기계 학습 디자이너가 재학습 시나리오에 어떻게 적합한지 확인합니다. 

## <a name="automate-the-ml-lifecycle"></a>ML 수명 주기 자동화 

GitHub 및 Azure 파이프라인을 사용하여 모델을 훈련하는 연속 통합 프로세스를 만들 수 있습니다. 일반적인 시나리오에서 데이터 과학자가 프로젝트에 대한 Git 리포지토리로 변경점을 검사하면 Azure 파이프라인이 학습 실행을 시작합니다. 그런 다음 실행 결과를 검사하여 학습된 모델의 성능 특성을 확인할 수 있습니다. 모델을 웹 서비스로 배포하는 파이프라인을 만들 수도 있습니다.

[Azure 기계 학습 확장을](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) 사용하면 Azure 파이프라인을 보다 쉽게 작업할 수 있습니다. Azure 파이프라인에 대한 다음과 같은 향상된 기능을 제공합니다.

* 서비스 연결을 정의할 때 작업 영역을 선택할 수 있습니다.
* 학습 파이프라인에서 만든 학습된 모델에 의해 릴리스 파이프라인을 트리거할 수 있습니다.

Azure 기계 학습을 사용하여 Azure 파이프라인을 사용하는 방법에 대한 자세한 내용은 다음 링크를 참조하십시오.

* [Azure 파이프라인을 통해 ML 모델의 지속적인 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure 기계 학습 MlOps](https://aka.ms/mlops) 리포지토리입니다.
* [Azure 기계 학습 MLOpsPython](https://github.com/Microsoft/MLOpspython) 리포지토리.

Azure Data Factory를 사용하여 학습에 사용할 데이터를 준비하는 데이터 수집 파이프라인을 만들 수도 있습니다. 자세한 내용은 [데이터 수집 파이프라인](how-to-cicd-data-ingestion.md)을 참조하십시오.

## <a name="next-steps"></a>다음 단계

다음 리소스를 읽고 탐색하여 자세히 알아보십시오.

+ Azure 기계 [학습을 사용하여 모델을 배포할 위치를 & 방법](how-to-deploy-and-where.md)

+ [자습서: ACI에 이미지 분류 모델을 배포합니다.](tutorial-deploy-models-with-aml.md)

+ [종단 간 MlOps 예제 리포지토리](https://github.com/microsoft/MLOps)

+ [Azure 파이프라인이 있는 ML 모델의 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ [배포된 모델을 사용하는](how-to-consume-web-service.md) 클라이언트 만들기

+ [대규모 Machine Learning](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 참조 아키텍처 & 모범 사례 담당자](https://github.com/microsoft/AI)
