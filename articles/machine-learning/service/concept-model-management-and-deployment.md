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
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c77e36b5fbeb1ecacd42352c8c52cfd4b617d0e6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123610"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Azure Machine Learning을 사용 하 여 모델 관리, 배포 및 모니터링

이 문서에서는 Azure Machine Learning 사용 하 여 모델의 수명 주기를 관리 하는 방법에 대해 알아봅니다. Azure Machine Learning는 MLOps (Machine Learning 작업) 방법을 사용 하 여 Machine Learning 솔루션의 품질과 일관성을 향상 시킵니다. 

Azure Machine Learning는 다음과 같은 MLOps 기능을 제공 합니다.

- **어디에서 나 ML 프로젝트 배포**
- **운영 및 ml 관련 문제에 대 한 ml 응용 프로그램 모니터링** -학습 및 유추 간 모델 입력을 비교 하 고, 모델 관련 메트릭을 탐색 하 고, ML 인프라에 대 한 모니터링 및 경고를 제공 합니다.
- 모델을 게시 하는 사람, 변경 하는 이유 및 프로덕션 환경에서 모델을 배포 하거나 사용한 경우를 포함 하 여 **ML 수명 주기에 대 한 종단 간 감사 내역을 설정 하는 데 필요한 데이터를 캡처합니다**.
- **Azure Machine Learning 및 Azure DevOps를 사용 하 여 종단 간 ML 수명 주기를 자동화** 하 여 모델을 자주 업데이트 하 고, 새 모델을 테스트 하 고, 다른 응용 프로그램 및 서비스와 함께 새 ML 모델을 지속적으로 롤아웃 하세요.

MLOps의 개념 및이를 Azure Machine Learning에 적용 하는 방법에 대 한 자세한 내용은 다음 동영상을 시청 하세요.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>어디에서 나 ML 프로젝트 배포

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>학습 프로세스를 재현 가능한 파이프라인으로 전환
Azure Machine Learning의 ML 파이프라인을 사용 하 여 데이터 준비부터 기능 추출부터 모델 평가까지 모델 학습 프로세스와 관련 된 모든 단계를 함께 연결 합니다.

자세한 내용은 [ML 파이프라인](concept-ml-pipelines.md)을 참조 하세요.

### <a name="register-and-track-ml-models"></a>ML 모델 등록 및 추적

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.

> [!TIP]
> 등록 된 모델은 모델을 구성 하는 하나 이상의 파일에 대 한 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 된 모든 파일을 받을 수 있습니다.
 
등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 또한 모델을 검색할 때 사용할 수 있는 추가 메타데이터 태그를 등록 중에 제공할 수도 있습니다. Azure Machine Learning는 Python 3.5.2 이상을 사용 하 여 로드할 수 있는 모든 모델을 지원 합니다.

> [!TIP]
> Azure Machine Learning 외부에서 학습 한 모델을 등록할 수도 있습니다.

활성 배포에서 사용 중인 등록 된 모델은 삭제할 수 없습니다.
자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

### <a name="package-and-debug-models"></a>패키지 및 디버그 모델

모델을 프로덕션 환경에 배포 하기 전에는 Docker 이미지로 패키지 됩니다. 대부분의 경우 이미지 생성은 배포 중에 백그라운드에서 자동으로 수행 됩니다. 고급 시나리오의 경우 이미지를 수동으로 지정할 수 있습니다.

배포에 문제가 발생 하는 경우 문제 해결 및 디버깅을 위해 로컬 개발 환경에를 배포할 수 있습니다.

자세한 내용은 [배포 모델](how-to-deploy-and-where.md#registermodel) 및 [배포 문제 해결](how-to-troubleshoot-deployment.md)을 참조 하세요.

### <a name="validate-and-profile-models"></a>모델 유효성 검사 및 프로 파일링

프로 파일링을 사용 하 여 모델을 배포할 때 사용할 이상적인 CPU 및 메모리 설정을 결정할 수 Azure Machine Learning. 모델 유효성 검사는 프로 파일링 프로세스에 제공 하는 데이터를 사용 하 여이 프로세스의 일부로 수행 됩니다.

### <a name="convert-and-optimize-models"></a>모델 변환 및 최적화

모델을 onnx ( [신경망 교환](https://onnx.ai) )를 열도록 변환 하면 성능이 향상 될 수 있습니다. 평균적으로 ONNX로 변환 하면 성능이 2 배 늘어날 수 있습니다.

Azure Machine Learning에 대 한 자세한 내용은 [ML 모델 만들기 및 가속화](concept-onnx.md) 문서를 참조 하세요.

### <a name="use-models"></a>모델 사용

학습 된 기계 학습 모델은 클라우드에서 웹 서비스로 배포 하거나 개발 환경에서 로컬로 배포할 수 있습니다. 또한 Azure IoT Edge 장치에 모델을 배포할 수 있습니다. 배포에서는 추론에 대해 CPU, GPU 또는 FPGA (필드 프로그래밍 가능 게이트 배열)를 사용할 수 있습니다. Power BI에서 모델을 사용할 수도 있습니다.

모델을 웹 서비스 또는 IoT Edge 장치로 사용 하는 경우 다음 항목을 제공 합니다.

* 서비스/장치에 전송 된 데이터의 점수를 매기는 데 사용 되는 모델입니다.
* 항목 스크립트입니다. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 데이터의 점수를 매기고, 응답을 반환 합니다.
* 모델 및 항목 스크립트에 필요한 종속성을 설명 하는 conda 환경 파일입니다.
* 모델 및 항목 스크립트에 필요한 텍스트, 데이터 등의 추가 자산입니다.

이러한 자산은 Docker 이미지로 패키지 되 고 웹 서비스 또는 IoT Edge 모듈로 배포 됩니다.

필요에 따라 다음 매개 변수를 사용 하 여 배포를 추가로 조정할 수 있습니다.

* GPU 사용: Docker 이미지에서 GPU 지원을 사용 하도록 설정 하는 데 사용 됩니다. 이미지는 Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning 계산 또는 Azure Virtual Machines와 같은 Microsoft Azure 서비스에서 사용 해야 합니다.
* 추가 docker 파일 단계: Docker 이미지를 만들 때 실행할 추가 Docker 단계를 포함 하는 파일입니다.
* 기본 이미지: 기본 이미지로 사용할 사용자 지정 이미지입니다. 사용자 지정 이미지를 사용 하지 않는 경우 Azure Machine Learning에서 기본 이미지를 제공 합니다.

또한 대상 배포 플랫폼의 구성을 제공 합니다. 예를 들어 Azure Kubernetes Service에 배포할 때 VM 제품군 유형, 사용 가능한 메모리 및 코어 수가 있습니다.

이미지를 만들 때 Azure Machine Learning에 필요한 구성 요소도 추가 됩니다. 예를 들어, 웹 서비스를 실행 하 고 IoT Edge와 상호 작용 하는 데 필요한 자산이 있습니다.

> [!NOTE]
> Docker 이미지에 사용 되는 웹 서버 또는 IoT Edge 구성 요소를 수정 하거나 변경할 수 없습니다. Azure Machine Learning는 Microsoft에서 테스트 및 지 원하는 웹 서버 구성 및 IoT Edge 구성 요소를 사용 합니다.

#### <a name="web-service"></a>웹 서비스

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


## <a name="monitor-for-operational--ml-issues"></a>운영 & ML 문제에 대 한 모니터

모니터링을 사용 하면 모델에 전송 되는 데이터 및 반환 하는 예측을 이해할 수 있습니다.

이 정보는 모델을 사용 하는 방법을 이해 하는 데 도움이 됩니다. 수집 된 입력 데이터는 모델의 이후 버전을 학습 하는 데 유용할 수도 있습니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.


## <a name="audit-trail-of-the-ml-lifecycle"></a>ML 수명 주기의 감사 내역

Azure ML은 모든 ML 자산의 종단 간 감사 내역을 추적 하는 기능을 제공 합니다. 구체적으로 살펴보면 다음과 같습니다.

- Azure ML [은 Git와 통합 되어](how-to-set-up-training-targets.md#gitintegration) 코드를 가져온 리포지토리/분기/커밋 정보를 추적 합니다.
- [AZURE ML 데이터 집합](how-to-create-register-datasets.md) 을 통해 데이터를 추적 하 고 버전 데이터를 관리할 수 있습니다.
- Azure ML 실행 기록은 모델 학습에 사용 되는 코드, 데이터 및 계산에 대 한 스냅숏을 저장 합니다.
- Azure ML 모델 레지스트리는 모델과 연결 된 모든 메타 데이터를 캡처합니다 (배포 하는 경우 실험을 통해 배포 되는 위치, 배포가 정상 인 경우).

## <a name="automate-the-ml-lifecycle"></a>ML 수명 주기 자동화 

GitHub 및 Azure Pipelines를 사용 하 여 모델을 학습 하는 연속 통합 프로세스를 만들 수 있습니다. 일반적인 시나리오에서 데이터 과학자 프로젝트에 대 한 Git 리포지토리 변경을 확인 하면 Azure 파이프라인이 학습 실행을 시작 합니다. 그런 다음 실행 결과를 검사 하 여 학습 된 모델의 성능 특성을 확인할 수 있습니다. 모델을 웹 서비스로 배포 하는 파이프라인을 만들 수도 있습니다.

[Azure Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) 을 사용 하면 Azure Pipelines 더 쉽게 작업할 수 있습니다. Azure Pipelines에 대 한 다음과 같은 향상 된 기능을 제공 합니다.

* 서비스 연결을 정의할 때 작업 영역을 선택할 수 있습니다.
* 학습 파이프라인에서 생성 된 학습 된 모델을 사용 하 여 릴리스 파이프라인을 트리거할 수 있습니다.

Azure Machine Learning에서 Azure Pipelines를 사용 하는 방법에 대 한 자세한 내용은 Azure Pipelines 문서 및 [Azure Machine Learning MLOps](https://aka.ms/mlops) 리포지토리를 사용 하 여 [ML 모델의 연속 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 리소스를 읽고 탐색 하 여 자세히 알아보세요.

+ Azure Machine Learning를 사용 하 여 [모델을 배포 하는 위치 &](how-to-deploy-and-where.md)

+ [자습서: ACI에서 이미지 분류 모델을 배포](tutorial-deploy-models-with-aml.md)합니다.

+ [종단 간 MLOps 예제 리포지토리](https://github.com/microsoft/MLOps)

+ [Azure Pipelines 있는 ML 모델의 CI/CD](/azure/devops/pipelines/targets/azure-machine-learning)

+ [배포 된 모델을 사용](how-to-consume-web-service.md) 하는 클라이언트 만들기

+ [규모에 맞는 기계 학습](/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI 참조 아키텍처 & 모범 사례 담당자](https://github.com/microsoft/AI)
