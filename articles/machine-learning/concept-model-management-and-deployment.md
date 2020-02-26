---
title: 'MLOps: ML 모델 관리'
titleSuffix: Azure Machine Learning
description: 'MLOps (Azure Machine Learning를 사용한 모델 관리에 대해 알아봅니다. 모델을 배포, 관리 및 모니터링 하 여 지속적으로 개선 합니다. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 02/21/2020
ms.custom: seodec18
ms.openlocfilehash: 11a6a668b1028ba1640ef076606d4aeb4c3aae6e
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589371"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Azure Machine Learning을 사용 하 여 모델 관리, 배포 및 모니터링

이 문서에서는 Azure Machine Learning 사용 하 여 모델의 수명 주기를 관리 하는 방법에 대해 알아봅니다. Azure Machine Learning는 MLOps (Machine Learning 작업) 방법을 사용 합니다. MLOps는 machine learning 솔루션의 품질 및 일관성을 개선 합니다. 

## <a name="what-is-mlops"></a>MLOps란?

MLOps (Machine Learning 작업)는 워크플로의 효율성을 향상 시키는 [Devops](https://azure.microsoft.com/overview/what-is-devops/) 원칙 및 사례를 기반으로 합니다. 예를 들어 지속적인 통합, 배달 및 배포입니다. MLOps는 다음과 같은 목표를 사용 하 여 이러한 보안 주체를 기계 학습 프로세스에 적용 합니다.

* 모델의 실험 및 개발 속도 향상
* 프로덕션 환경에 더 빠르게 모델 배포
* 품질 보증

Azure Machine Learning는 다음과 같은 MLOps 기능을 제공 합니다.

- **재현 가능한 ML 파이프라인을 만듭니다**. Machine Learning 파이프라인을 사용 하 여 데이터 준비, 학습 및 점수 매기기 프로세스에 대해 반복 가능 하 고 재사용 가능한 단계를 정의할 수 있습니다.
- 모델 학습 및 배포를 위한 **재사용 가능한 소프트웨어 환경을 만듭니다** .
- **어디에서 나 모델을 등록 하 고 패키지 하 고 배포**합니다. 또한 모델을 사용 하는 데 필요한 연결 된 메타 데이터를 추적할 수 있습니다.
- **종단 간 ML 수명 주기에 대 한 거 버 넌 스 데이터를 캡처합니다**. 기록 된 정보에는 모델을 게시 하는 사람, 변경 된 이유 및 프로덕션 환경에서 모델을 배포 하거나 사용한 시기 등이 포함 될 수 있습니다.
- **ML 수명 주기의 이벤트를 알리고 경고**합니다. 예를 들면 실험 완료, 모델 등록, 모델 배포 및 데이터 드리프트 검색입니다.
- **Ml 응용 프로그램을 모니터링 하 여 운영 및 ml 관련 문제를 해결**합니다. 학습 및 유추 간에 모델 입력을 비교 하 고, 모델 관련 메트릭을 탐색 하 고, ML 인프라에 대 한 모니터링 및 경고를 제공 합니다.
- **Azure Machine Learning 및 Azure Pipelines를 사용 하 여 종단 간 ML 수명 주기를 자동화**합니다. 파이프라인을 사용 하면 모델을 자주 업데이트 하 고, 새 모델을 테스트 하 고, 다른 응용 프로그램 및 서비스와 함께 새 ML 모델을 지속적으로 롤아웃할 수 있습니다.

## <a name="create-reproducible-ml-pipelines"></a>재현 가능한 ML 파이프라인 만들기

Azure Machine Learning의 ML 파이프라인을 사용 하 여 모델 학습 프로세스와 관련 된 모든 단계를 함께 연결 합니다.

ML 파이프라인은 모델 평가에 대 한 하이퍼 매개 변수 조정에 대 한 데이터 준비의 단계를 포함할 수 있습니다. 자세한 내용은 [ML 파이프라인](concept-ml-pipelines.md)을 참조 하세요.

[디자이너](concept-designer.md) 를 사용 하 여 ML 파이프라인을 만드는 경우 언제 든 지 디자이너 페이지의 오른쪽 위에 있는 **"..."** 를 클릭 하 고 **복제**를 선택할 수 있습니다. 파이프라인을 복제 하면 이전 버전을 잃지 않고 파이프라인 디자인을 반복할 수 있습니다.  

## <a name="create-reusable-software-environments"></a>재사용 가능한 소프트웨어 환경 만들기

Azure Machine Learning 환경을 사용 하면 프로젝트의 소프트웨어 종속성이 진화 함에 따라 추적 하 고 재현할 수 있습니다. 환경을 사용 하면 수동 소프트웨어 구성 없이도 빌드를 재현할 수 있습니다.

환경에서는 프로젝트의 pip 및 Conda 종속성을 설명 하 고 모델의 학습 및 배포 모두에 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning 환경 이란?](concept-environments.md)을 참조 하세요.

## <a name="register-package-and-deploy-models-from-anywhere"></a>어디에서 나 모델 등록, 패키징 및 배포

### <a name="register-and-track-ml-models"></a>ML 모델 등록 및 추적

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.

> [!TIP]
> 등록 된 모델은 모델을 구성 하는 하나 이상의 파일에 대 한 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 된 모든 파일을 받을 수 있습니다.

등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 등록 하는 동안 추가 메타 데이터 태그를 제공할 수 있습니다. 이러한 태그는 모델을 검색할 때 사용 됩니다. Azure Machine Learning는 Python 3.5.2 이상을 사용 하 여 로드할 수 있는 모든 모델을 지원 합니다.

> [!TIP]
> Azure Machine Learning 외부에서 학습 한 모델을 등록할 수도 있습니다.

활성 배포에서 사용 중인 등록 된 모델은 삭제할 수 없습니다.
자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

### <a name="package-and-debug-models"></a>패키지 및 디버그 모델

모델을 프로덕션 환경에 배포 하기 전에는 Docker 이미지로 패키지 됩니다. 대부분의 경우 이미지 생성은 배포 중에 백그라운드에서 자동으로 수행 됩니다. 이미지를 수동으로 지정할 수 있습니다.

배포에 문제가 발생 하는 경우 문제 해결 및 디버깅을 위해 로컬 개발 환경에를 배포할 수 있습니다.

자세한 내용은 [배포 모델](how-to-deploy-and-where.md#registermodel) 및 [배포 문제 해결](how-to-troubleshoot-deployment.md)을 참조 하세요.

### <a name="validate-and-profile-models"></a>모델 유효성 검사 및 프로 파일링

프로 파일링을 사용 하 여 모델을 배포할 때 사용할 이상적인 CPU 및 메모리 설정을 결정할 수 Azure Machine Learning. 모델 유효성 검사는 프로 파일링 프로세스에 제공 하는 데이터를 사용 하 여이 프로세스의 일부로 수행 됩니다.

### <a name="convert-and-optimize-models"></a>모델 변환 및 최적화

모델을 onnx ( [신경망 교환](https://onnx.ai) )를 열도록 변환 하면 성능이 향상 될 수 있습니다. 평균적으로 ONNX로 변환 하면 성능이 2 배 늘어날 수 있습니다.

Azure Machine Learning에 대 한 자세한 내용은 [ML 모델 만들기 및 가속화](concept-onnx.md) 문서를 참조 하세요.

### <a name="use-models"></a>모델 사용

학습 된 기계 학습 모델은 클라우드 또는 로컬에서 웹 서비스로 배포 됩니다. 또한 Azure IoT Edge 장치에 모델을 배포할 수 있습니다. 배포는 추론에 대 한 CPU, GPU 또는 FPGA (필드 프로그래밍 가능 게이트 배열)를 사용 합니다. Power BI에서 모델을 사용할 수도 있습니다.

모델을 웹 서비스 또는 IoT Edge 장치로 사용 하는 경우 다음 항목을 제공 합니다.

* 서비스/장치에 전송 된 데이터의 점수를 매기는 데 사용 되는 모델입니다.
* 항목 스크립트입니다. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 데이터의 점수를 매기고, 응답을 반환 합니다.
* 모델 및 항목 스크립트에 필요한 pip 및 Conda 종속성을 설명 하는 Azure Machine Learning 환경입니다.
* 모델 및 항목 스크립트에 필요한 텍스트, 데이터 등의 추가 자산입니다.

또한 대상 배포 플랫폼의 구성을 제공 합니다. 예를 들어 Azure Kubernetes Service에 배포할 때 VM 제품군 유형, 사용 가능한 메모리 및 코어 수가 있습니다.

이미지를 만들 때 Azure Machine Learning에 필요한 구성 요소도 추가 됩니다. 예를 들어, 웹 서비스를 실행 하 고 IoT Edge와 상호 작용 하는 데 필요한 자산이 있습니다.

#### <a name="batch-scoring"></a>일괄 처리 채점
일괄 처리 점수 매기기는 ML 파이프라인을 통해 지원 됩니다. 자세한 내용은 [빅 데이터의 일괄 처리 예측](how-to-use-parallel-run-step.md)을 참조 하세요.

#### <a name="real-time-web-services"></a>실시간 웹 서비스

다음 계산 대상을 사용 하 여 **웹 서비스** 에서 모델을 사용할 수 있습니다.

* Azure Container Instance
* Azure Kubernetes Service
* 로컬 개발 환경

모델을 웹 서비스로 배포 하려면 다음 항목을 제공 해야 합니다.

* 모델 또는 모델의 앙상블입니다.
* 모델을 사용 하려면 종속성이 필요 합니다. 예를 들어 요청을 수락 하 고 모델, conda 종속성 등을 호출 하는 스크립트입니다.
* 모델을 배포 하는 방법 및 위치를 설명 하는 배포 구성입니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

#### <a name="iot-edge-devices"></a>IoT Edge 장치

**Azure IoT Edge 모듈**을 통해 IoT 장치에서 모델을 사용할 수 있습니다. IoT Edge 모듈은 장치에서 유추 또는 모델 점수 매기기를 사용 하도록 설정 하는 하드웨어 장치에 배포 됩니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

### <a name="analytics"></a>분석

Microsoft Power BI는 데이터 분석을 위해 기계 학습 모델을 사용할 수 있도록 지원 합니다. 자세한 내용은 [Power BI (미리 보기)의 Azure Machine Learning 통합](https://docs.microsoft.com/power-bi/service-machine-learning-integration)을 참조 하세요.

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>종단 간 ML 수명 주기 캡처에 필요한 거 버 넌 스 데이터 캡처

Azure ML은 모든 ML 자산의 종단간 감사 내역을 추적 하는 기능을 제공 합니다. 특히 다음에 대한 내용을 설명합니다.

- Azure ML [은 Git와 통합 되어](how-to-set-up-training-targets.md#gitintegration) 코드를 가져온 리포지토리/분기/커밋에 대 한 정보를 추적 합니다.
- [AZURE ML 데이터 집합](how-to-create-register-datasets.md) 을 통해 데이터를 추적, 프로 파일링 및 버전 데이터를 관리할 수 있습니다. 
- Azure ML 실행 기록은 모델 학습에 사용 되는 코드, 데이터 및 계산에 대 한 스냅숏을 저장 합니다.
- Azure ML 모델 레지스트리는 모델과 연결 된 모든 메타 데이터를 캡처합니다 (배포 하는 경우 실험을 통해 배포 되는 위치, 배포가 정상 인 경우).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>ML 수명 주기의 이벤트를 알리고, 자동화 하 고, 경고 합니다.
Azure ML은 ML 수명 주기의 이벤트를 알리고 자동화 하는 데 사용할 수 있는 Azure EventGrid에 주요 이벤트를 게시 합니다. 자세한 내용은 [이 문서](how-to-use-event-grid.md)를 참조 하세요.


## <a name="monitor-for-operational--ml-issues"></a>운영 & ML 문제에 대 한 모니터

모니터링을 사용 하면 모델에 전송 되는 데이터 및 반환 하는 예측을 이해할 수 있습니다.

이 정보는 모델을 사용 하는 방법을 이해 하는 데 도움이 됩니다. 수집 된 입력 데이터는 모델의 이후 버전을 학습 하는 데 유용할 수도 있습니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.

## <a name="retrain-your-model-on-new-data"></a>새 데이터에 대 한 모델 다시 학습

모델을 업데이트 하거나 새 정보를 수신 하는 경우에는 모델을 처음부터 새로 다시 학습 할 수도 있습니다. 경우에 따라 새 데이터를 받는 것이 도메인의 예상 부분입니다. 다른 경우에는 [데이터 집합의 데이터 드리프트 (미리 보기) 검색](how-to-monitor-datasets.md)에 설명 된 대로 특정 센서에 대 한 변경, 계절 효과와 같은 자연 스러운 데이터 변경, 다른 기능과의 관계에서 변화 하는 기능 등의 측면에서 모델 성능이 저하 될 수 있습니다. 

"다시 학습 해야 하는 경우 어떻게 할까요? 알 수 있나요?"에 대 한 국제 답변이 없습니다. 그러나 앞에서 설명한 Azure ML 이벤트 및 모니터링 도구는 자동화를 위한 좋은 출발점입니다. 다시 학습를 결정 한 후에는 다음을 수행 해야 합니다. 

- 반복 가능한 자동화 된 프로세스를 사용 하 여 데이터를 전처리 합니다.
- 새 모델 학습
- 새 모델의 출력을 이전 모델의 출력과 비교 합니다.
- 미리 정의 된 조건을 사용 하 여 이전 모델을 바꿀지 여부를 선택 합니다. 

위의 단계를 수행 하는 것은 임시가 아닌 재 학습을 자동화 해야 한다는 것입니다. [Azure Machine Learning 파이프라인](concept-ml-pipelines.md) 은 데이터 준비, 학습, 유효성 검사 및 배포와 관련 된 워크플로를 만들 때 좋은 답입니다. [Azure Machine Learning designer (미리 보기)를 사용 하 여 다시 학습 모델](how-to-retrain-designer.md) 을 읽어 파이프라인과 Azure Machine Learning 디자이너가 재 학습 시나리오에 어떻게 부합 하는지 알아보세요. 

## <a name="automate-the-ml-lifecycle"></a>ML 수명 주기 자동화 

GitHub 및 Azure Pipelines를 사용 하 여 모델을 학습 하는 연속 통합 프로세스를 만들 수 있습니다. 일반적인 시나리오에서 데이터 과학자 프로젝트에 대 한 Git 리포지토리 변경을 확인 하면 Azure 파이프라인이 학습 실행을 시작 합니다. 그런 다음 실행 결과를 검사 하 여 학습 된 모델의 성능 특성을 확인할 수 있습니다. 모델을 웹 서비스로 배포 하는 파이프라인을 만들 수도 있습니다.

[Azure Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) 을 사용 하면 Azure Pipelines 더 쉽게 작업할 수 있습니다. Azure Pipelines에 대 한 다음과 같은 향상 된 기능을 제공 합니다.

* 서비스 연결을 정의할 때 작업 영역을 선택할 수 있습니다.
* 학습 파이프라인에서 생성 된 학습 된 모델을 사용 하 여 릴리스 파이프라인을 트리거할 수 있습니다.

Azure Machine Learning에서 Azure Pipelines를 사용 하는 방법에 대 한 자세한 내용은 Azure Pipelines 문서 및 [Azure Machine Learning MLOps](https://aka.ms/mlops) 리포지토리를 사용 하 여 [ML 모델의 연속 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning) 를 참조 하세요.

또한 Azure Data Factory를 사용 하 여 학습에 사용할 데이터를 준비 하는 데이터 수집 파이프라인을 만들 수 있습니다. 자세한 내용은 [데이터 수집 파이프라인](how-to-cicd-data-ingestion.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 리소스를 읽고 탐색 하 여 자세히 알아보세요.

+ Azure Machine Learning를 사용 하 여 [모델을 배포 하는 위치 &](how-to-deploy-and-where.md)

+ [자습서: ACI에서 이미지 분류 모델을 배포](tutorial-deploy-models-with-aml.md)합니다.

+ [종단 간 MLOps 예제 리포지토리](https://github.com/microsoft/MLOps)

+ [Azure Pipelines 있는 ML 모델의 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ [배포 된 모델을 사용](how-to-consume-web-service.md) 하는 클라이언트 만들기

+ [규모에 맞는 기계 학습](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 참조 아키텍처 & 모범 사례 담당자](https://github.com/microsoft/AI)
