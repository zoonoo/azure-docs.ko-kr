---
title: Azure Machine Learning 서비스의 작동 원리
description: Azure Machine Learning 서비스를 구성하는 아키텍처, 용어 및 개념을 알아봅니다. 또한 서비스 사용의 일반적인 워크플로 및 Azure Machine Learning 서비스에서 사용되는 Azure 서비스를 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 3011fa85dbac2135f4d9113c6b76a8b667ee4013
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952135"
---
# <a name="architecture-and-concepts-how-does-azure-machine-learning-service-work"></a>아키텍처 및 개념: Azure Machine Learning 서비스의 작동 원리 

이 문서에서는 Azure Machine Learning 서비스에 대한 아키텍처 및 개념을 설명합니다. 다음 다이어그램은 서비스의 주요 구성 요소를 보여 주고, 서비스 사용 시 일반적인 일반 워크플로를 보여 줍니다. 

[![Azure Machine Learning 아키텍처 및 워크플로](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

일반적으로 워크플로는 다음 단계를 따릅니다.

1. __Python__에서 기계 학습의 학습 스크립트를 개발합니다.
1. ‘계산 대상’을 만들고 구성합니다.____
1. 해당 환경에서 실행할 구성된 계산 대상에 ‘스크립트를 제출’합니다.____ 학습 중에 계산 대상이 실행 기록을 ‘데이터 저장소’에 저장합니다.____ 레코드는 ‘실험’에 저장됩니다.____
1. ‘실험을 쿼리’하여 현재 및 과거 실행에서 기록된 메트릭을 확인합니다.____ 메트릭이 원하는 결과를 표시하지 않으면 1단계로 돌아가 스크립트를 반복합니다.
1. 만족스러운 실행이 발견되면 ‘모델 레지스트리’에 지속되는 모델을 등록합니다.____
1. 점수 매기기 스크립트를 개발합니다.
1. ‘이미지를 만들고’ ‘이미지 레지스트리’에 등록합니다.________ 
1. Azure에서 ‘웹 서비스’로 ‘이미지를 배포’합니다.________


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> 이 문서는 Azure Machine Learning에서 사용되는 용어와 개념을 정의하지만 Azure 플랫폼에 대한 용어와 개념을 정의하지는 않습니다. Azure 플랫폼 용어에 대한 자세한 내용은 [Microsoft Azure 용어집](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)을 참조하세요.

## <a name="workspace"></a>작업 영역

작업 영역은 Azure Machine Learning 서비스에 대한 최상위 리소스입니다. Azure Machine Learning을 사용하는 동안 만드는 모든 아티팩트를 작업할 수 있는 중앙 위치를 제공합니다.

작업 영역은 모델을 학습시키는 데 사용할 수 있는 계산 대상 목록을 유지합니다. 또한 스크립트의 로그, 메트릭, 출력 및 스냅숏을 포함하는 학습 실행 기록을 유지합니다. 이 정보는 최고의 모델을 생성하는 학습 실행을 판별하는 데 사용됩니다.

모델은 작업 영역에 등록됩니다. 등록된 모델 및 점수 매기기 스크립트는 이미지를 만드는 데 사용됩니다. 이미지는 REST 기반 HTTP 엔드포인트로 Azure Container Instances 또는 Azure Kubernetes Service에 배포하거나 FPGA(Field-Programmable Gate Array)에 배포할 수 있습니다. Azure IoT Edge 장치에 모듈로 배포할 수도 있습니다.

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. 작업 영역을 공유하는 경우 사용자에게 다음 역할을 할당하여 작업 영역에 대한 액세스를 제어합니다.

* 소유자
* 참가자
* 읽기 권한자

새 작업 영역을 만들면 작업 영역에서 사용되는 여러 Azure 리소스가 자동으로 생성됩니다.

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) - 학습 중에 사용되고 모델을 배포하는 동안 사용되는 docker 컨테이너를 등록합니다.
* [Azure Storage](https://azure.microsoft.com/services/storage/) - 작업 영역에 대한 기본 데이터 저장소로 사용됩니다.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) - 모델에 대한 모니터링 정보를 저장합니다.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) - 계산 대상에서 사용되는 비밀 및 작업 영역에 필요한 기타 중요한 정보를 저장합니다.

> [!NOTE]
> 새 버전을 만들지 않고 기존 Azure 서비스를 사용할 수도 있습니다. 

다음 다이어그램은 작업 영역의 분류입니다.

[![작업 영역 분류](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>모델

가장 간단하게, 모델은 입력을 사용하고 출력을 생성하는 코드 조각입니다. 기계 학습 모델을 만드는 동안 알고리즘을 선택하고, 데이터를 제공하고, 하이퍼 매개 변수를 조정합니다. 학습은 학습 프로세스 중에 모델이 학습한 내용을 캡슐화하는 학습된 모델을 생성하는 반복 프로세스입니다.

모델은 Azure Machine Learning에서 실행을 통해 생성됩니다. Azure Machine Learning 외부에서 학습된 모델을 사용할 수도 있습니다. 모델은 Azure Machine Learning 작업 영역 아래에 등록할 수 있습니다.

Azure Machine Learning은 프레임워크에 제약이 없습니다. 모델을 만드는 경우 scikit-learn, xgboost, PyTorch, TensorFlow, Chainer 및 CNTK와 같은 인기 있는 기계 학습 프레임워크를 사용할 수 있습니다.

모델 학습의 예제를 보려면 [빠른 시작: 기계 학습 작업 영역 만들기](quickstart-get-started.md) 문서를 참조하세요.

### <a name="model-registry"></a>모델 레지스트리

모델 레지스트리는 Azure Machine Learning 작업 영역에서 모든 모델을 추적합니다. 

모델은 이름 및 버전으로 식별됩니다. 기존 이름과 동일한 이름을 사용하여 모델을 등록할 때마다 레지스트리는 모델을 새 버전으로 간주합니다. 버전 번호가 증가하고 새 모델이 해당 이름으로 등록됩니다.

모델을 등록할 때 추가 메타데이터 태그를 제공한 다음, 모델을 검색할 때 이러한 태그를 사용할 수 있습니다.

이미지에서 사용 중인 모델은 삭제할 수 없습니다.

모델을 등록하는 예제를 보려면 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 문서를 참조하세요.

## <a name="image"></a>이미지

이미지는 모델을 사용하는 데 필요한 모든 구성 요소와 함께 모델을 안정적으로 배포하는 방법을 제공합니다. 이미지에는 다음 항목이 포함됩니다.

* 모델.
* 점수 매기기 스크립트 또는 응용 프로그램. 이 스크립트는 입력을 모델에 전달하고 모델의 출력을 반환하는 데 사용됩니다.
* 모델 또는 점수 매기기 스크립트/응용 프로그램에 필요한 종속성.  예를 들어 Python 패키지 종속성을 나열하는 Conda 환경 파일을 포함할 수 있습니다.

Azure Machine Learning에서 만들 수 있는 두 가지 유형의 이미지는 다음과 같습니다.

* FPGA 이미지: Azure 클라우드에서 FPGA(Field-Programmable Gate Array)에 배포할 때 사용됩니다.
* Docker 이미지: FPGA 이외의 계산 대상에 배포할 때 사용됩니다. 예를 들어 Azure Container Instances 및 Azure Kubernetes Service가 있습니다.

이미지를 만드는 예제를 보려면 [Azure Container Instance에 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md) 문서를 참조하세요.

### <a name="image-registry"></a>이미지 레지스트리

이미지 레지스트리는 모델에서 생성된 이미지를 추적합니다. 이미지를 만들 때 추가 메타데이터 태그를 제공할 수 있습니다. 메타데이터 태그는 이미지 레지스트리에 저장되고 이를 쿼리하여 이미지를 찾을 수 있습니다.

## <a name="deployment"></a>배포

배포는 통합형 클라우드에서 호스트될 수 있는 웹 서비스 또는 장치 배포를 위한 IoT 모듈로 이미지를 인스턴스화하는 것입니다. 

### <a name="web-service"></a>웹 서비스

배포된 웹 서비스는 Azure Container Instances, Azure Kubernetes Service 또는 FPGA(Field-Programmable Gate Array)를 사용할 수 있습니다.
서비스는 모델, 스크립트 및 관련된 파일을 캡슐화하는 이미지에서 만들어집니다. 이미지에는 웹 서비스에 전송된 점수 매기기 요청을 수신하는 부하 분산된 HTTP 엔드포인트가 있습니다.

Azure에서는 이 기능을 사용하도록 선택한 경우 Application Insight 원격 분석 및/또는 모델 원격 분석을 수집하여 웹 서비스 배포를 모니터링할 수 있습니다. 원격 분석 데이터는 관련 사용자에게만 제공되고 Application Insights 및 저장소 계정 인스턴스에 저장됩니다.

자동 크기 조정을 사용하도록 설정한 경우 Azure에서 배포 크기를 자동으로 조정합니다.

모델을 웹 서비스로 배포하는 예제를 보려면 [Azure Container Instance에 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md) 문서를 참조하세요.

### <a name="iot-module"></a>IoT 모듈

배포된 IoT 모듈은 모델 및 연결된 스크립트나 응용 프로그램과 모든 추가 종속성을 포함하는 Docker 컨테이너입니다. 이러한 모듈은 에지 장치에서 Azure IoT Edge를 사용하여 배포됩니다. 

모니터링을 사용하도록 설정한 경우 Azure는 Azure IoT Edge 모듈 내의 모델에서 원격 분석 데이터를 수집합니다. 원격 분석 데이터는 관련 사용자에게만 제공되고 저장소 계정 인스턴스에 저장됩니다.

Azure IoT Edge 모듈은 모듈이 실행 중인지 확인하고 모듈을 호스트 중인 장치를 모니터링합니다.

## <a name="datastore"></a>데이터 저장소

데이터 저장소는 Azure Storage 계정을 통한 저장소 추상화입니다. 데이터 저장소는 Azure Blob 컨테이너 또는 Azure 파일 공유 중 하나를 백 엔드 저장소로 사용할 수 있습니다. 각 작업 영역에는 기본 데이터 저장소가 있고 추가 데이터 저장소를 등록할 수 있습니다. 

Python SDK API 또는 Azure Machine Learning CLI를 사용하여 데이터 저장소의 파일을 저장하고 검색합니다. 

## <a name="run"></a>실행

실행은 다음 정보를 포함하는 레코드입니다.

* 실행에 대한 메타데이터(타임스탬프, 기간 등)
* 스크립트에 의해 기록되는 메트릭
* 실험을 통해 자동 수집되거나 사용자가 명시적으로 업로드한 출력 파일.
* 실행 전의 스크립트를 포함하는 디렉터리의 스냅숏

실행은 모델을 학습시키기 위해 스크립트를 제출할 때 생성됩니다. 실행에는 0개 이상의 자식 실행이 포함될 수 있습니다. 최상위 실행에는 두 개의 자식 실행이 포함될 수 있고, 각 자식 실행에는 자체 자식 실행이 포함될 수 있습니다.

모델 학습을 통해 생성된 실행을 보는 예제를 확인하려면 [빠른 시작: Azure Machine Learning 서비스 시작](quickstart-get-started.md) 문서를 참조하세요.

## <a name="experiment"></a>실험

실험은 지정된 스크립트의 많은 실행을 그룹화한 것입니다. 실험은 항상 작업 영역에 속합니다. 실행을 제출할 때 실험 이름을 제공합니다. 실행에 대한 정보는 해당 실험 아래에 저장됩니다. 실행을 제출하고 존재하지 않는 실험 이름을 지정하면 해당 이름을 가진 새 실험이 자동으로 생성됩니다.

실험을 사용하는 예제를 보려면 [빠른 시작: Azure Machine Learning 서비스 시작](quickstart-get-started.md) 문서를 참조하세요.

## <a name="compute-target"></a>계산 대상

계산 대상은 학습 스크립트를 실행하거나 웹 서비스 배포를 호스트하는 데 사용되는 계산 리소스입니다. 지원되는 계산 대상은 다음과 같습니다. 

* 로컬 컴퓨터
* Azure의 Linux VM(예: Data Science Virtual Machine)
* Azure Batch AI 클러스터
* HDInsight용 Apache Spark
* Azure Container Instance
* Azure Kubernetes Service

계산 대상은 작업 영역에 연결됩니다. 작업 영역의 사용자가 로컬 컴퓨터 이외의 계산 대상을 공유합니다.

대부분 계산 대상은 Azure Portal, Azure Machine Learning SDK 또는 Azure CLI를 사용하여 작업 영역을 통해 직접 만들 수 있습니다. 또 다른 프로세스에서 생성된 계산 대상(예: Azure Portal 또는 Azure CLI)이 있는 경우 계산 대상을 작업 영역에 추가(연결)할 수 있습니다. 일부 계산 대상은 작업 영역 외부에서 만든 다음, 연결해야 합니다.

학습에 대한 계산 대상을 선택하는 방법에 대한 자세한 내용은 [모델 학습을 위한 계산 대상 선택 및 사용](how-to-set-up-training-targets.md) 문서를 참조하세요.

배포를 위한 계산 대상을 선택하는 방법에 대한 자세한 내용은 [Azure Machine Learning 서비스를 사용하여 모델 배포](how-to-deploy-and-where.md) 문서를 참조하세요.

## <a name="run-configuration"></a>실행 구성

실행 구성은 지정된 계산 대상에서 스크립트를 실행해야 하는 방법을 정의하는 명령 집합을 보여 줍니다. 여기에는 기존 Python 환경을 사용할지 또는 사양에서 빌드된 Conda 환경을 사용할지 여부와 같은 광범위한 동작 정의 집합이 포함됩니다.

실행 구성은 학습 스크립트를 포함하는 디렉터리 내의 파일에 지속되거나, 메모리 내 개체로 구성되고 실행을 제출하는 데 사용될 수 있습니다.

예제 실행 구성을 보려면 [모델 학습을 위한 계산 대상 선택 및 사용](how-to-set-up-training-targets.md) 문서를 참조하세요.

## <a name="training-script"></a>학습 스크립트

모델을 학습시키려면 학습 스크립트 및 연결된 파일을 포함하는 디렉터리를 지정합니다. 학습 중에 수집된 정보를 저장하는 데 사용되는 실험 이름도 지정합니다. 학습 중에 전체 디렉터리가 학습 환경(계산 대상)에 복사되고 실행 구성을 통해 지정된 스크립트가 시작됩니다. 또한 디렉터리의 스냅숏이 작업 영역의 실험 아래에 저장됩니다.

스크립트를 사용하여 모델을 학습시키는 예제를 보려면 [Python을 사용하여 작업 영역 만들기](quickstart-get-started.md)를 참조하세요.

## <a name="logging"></a>로깅

솔루션을 개발할 때 Python 스크립트에서 Azure Machine Learning Python SDK를 사용하여 임의 메트릭을 기록합니다. 실행 후에 메트릭을 쿼리하여 실행에서 배포할 모델을 생성했는지 확인합니다. 

## <a name="snapshot"></a>스냅숏

실행을 제출하면 Azure Machine Learning은 스크립트를 포함하는 디렉터리를 zip 파일로 압축하고 이를 계산 대상으로 보냅니다. 그런 다음, zip이 확장되고 여기서 스크립트가 실행됩니다. 또한 Azure Machine Learning은 zip 파일을 실행 기록의 일부인 스냅숏으로 저장합니다. 작업 영역에 대한 액세스 권한이 있는 사용자는 실행 기록을 찾아보고 스냅숏을 다운로드할 수 있습니다.

## <a name="activity"></a>작업

작업은 장기 실행 작업을 나타냅니다. 다음 조작은 작업의 예입니다.

* 계산 대상 만들기 또는 삭제
* 계산 대상에서 스크립트 실행

작업은 SDK 또는 웹 UI를 통해 알림을 제공할 수 있으므로, 사용자가 이러한 조작의 진행 상황을 쉽게 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 링크를 사용하여 Azure Machine Learning 사용을 시작합니다.

* [Azure Machine Learning 서비스란?](overview-what-is-azure-ml.md)
* [빠른 시작: Python을 사용하여 작업 영역 만들기](quickstart-get-started.md)
* [자습서: 모델 학습](tutorial-train-models-with-aml.md)
