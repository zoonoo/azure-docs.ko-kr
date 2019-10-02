---
title: 작업 영역 이란?
titleSuffix: Azure Machine Learning
description: 작업 영역은 Azure Machine Learning의 최상위 리소스입니다. 로그, 메트릭, 출력 및 스크립트의 스냅숏을 비롯 하 여 모든 학습 실행에 대 한 기록을 유지 합니다. 이 정보를 사용 하 여 최상의 모델을 생성 하는 학습 실행을 결정 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: de215502f1ecb55bc331f29057a4f7c3f30b0132
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720172"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역 이란 무엇 인가요?

작업 영역은 Azure Machine Learning의 최상위 리소스 이며 Azure Machine Learning를 사용할 때 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공 합니다.  작업 영역에는 로그, 메트릭, 출력 및 스크립트의 스냅숏을 비롯 하 여 모든 학습 실행의 기록이 유지 됩니다. 이 정보를 사용하여 최고의 모델을 생성하는 학습 실행을 확인합니다.  

원하는 모델이 있으면 작업 영역에 등록 합니다. 그런 다음 등록 된 모델 및 점수 매기기 스크립트를 사용 하 여 Azure Container Instances, Azure Kubernetes Service에 배포 하거나 FPGA (필드 프로그래밍 가능 게이트 배열)에 REST 기반 HTTP 끝점으로 배포 합니다. 모델을 Azure IoT Edge 장치에 모듈로 배포할 수도 있습니다.

## <a name="taxonomy"></a>분류 

다음은 작업 영역의 분류 체계를 보여주는 다이어그램입니다.

[![작업 영역 분류](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

다이어그램에는 작업 영역의 다음 구성 요소가 표시 됩니다.

+ 작업 영역에는 Azure Machine Learning를 실행 하는 데 필요한 Python 환경으로 구성 된 클라우드 리소스인 [노트북 vm](tutorial-1st-experiment-sdk-setup.md)이 포함 될 수 있습니다.
+ [사용자 역할](how-to-assign-roles.md) 을 사용 하면 작업 영역을 다른 사용자, 팀 또는 프로젝트와 공유할 수 있습니다.
+ [계산 대상은](concept-azure-machine-learning-architecture.md#compute-targets) 실험을 실행 하는 데 사용 됩니다.
+ 작업 영역을 만들 때 [연결 된 리소스도](#resources) 생성 됩니다.
+ [실험](concept-azure-machine-learning-architecture.md#experiments) 은 모델을 작성 하는 데 사용 하는 학습 실행입니다.  다음을 사용 하 여 실험을 만들고 실행할 수 있습니다.
    + [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)입니다.
    + Azure Portal 또는 작업 영역 방문 페이지 (미리 보기)의 [자동화 된 machine learning 실험 (미리 보기)](how-to-create-portal-experiments.md) 섹션
    + [시각적 인터페이스 (미리 보기)](ui-concept-visual-interface.md)입니다.
+ [파이프라인](concept-azure-machine-learning-architecture.md#ml-pipelines) 은 모델을 학습 하 고 재 학습 하는 데 사용할 수 있는 워크플로입니다.
+ 데이터 [집합](concept-azure-machine-learning-architecture.md#datasets-and-datastores) 은 모델 학습 및 파이프라인 생성에 사용 하는 데이터를 관리 하는 데 도움이 됩니다.
+ 배포 하려는 모델이 있으면 등록 된 모델을 만듭니다.
+ 등록 된 모델 및 점수 매기기 스크립트를 사용 하 여 [배포](concept-azure-machine-learning-architecture.md#deployment)를 만듭니다.

## <a name="tools-for-workspace-interaction"></a>작업 영역 상호 작용 도구

다음과 같은 방법으로 작업 영역과 상호 작용할 수 있습니다.

+ 웹에서:
    + [Azure Portal](https://portal.azure.com)
    + [작업 영역 방문 페이지 (미리 보기)](https://ml.azure.com)
    + [시각적 인터페이스 (미리 보기)](ui-concept-visual-interface.md)
+ Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 를 사용 하는 Python
+ Azure Machine Learning [CLI 확장](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) 을 사용 하 여 명령줄에서

## <a name="machine-learning-with-a-workspace"></a>작업 영역을 사용 하는 기계 학습

Machine learning 작업은 작업 영역에 대 한 아티팩트를 읽고 씁니다. 

+ 실험을 실행 하 여 모델 학습-작업 영역에 실험 실행 결과를 기록 합니다.
+ 자동화 된 ML을 사용 하 여 모델 학습-작업 영역에 학습 결과를 기록 합니다.
+ 작업 영역에 모델을 등록 합니다.
+ 모델 배포-등록 된 모델을 사용 하 여 배포를 만듭니다.
+ 재사용 가능한 워크플로를 만들고 실행 합니다.
+ 실험, 파이프라인, 모델, 배포 등 기계 학습 아티팩트를 봅니다.
+ 모델 추적 및 모니터링

## <a name="workspace-management"></a>작업 영역 관리

또한 다음 작업 영역 관리 작업을 수행할 수 있습니다.

| 작업 영역 관리 작업   | 포털              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| 작업 영역 만들기        | **&check;**     | **&check;** | **&check;** |
| 계산 리소스 만들기 및 관리    | **&check;**   | **&check;** |  **&check;**   |
| 작업 영역 액세스 관리    | **&check;**   | |  **&check;**    |
| Notebook VM을 만듭니다. | **&check;**   | |     |

## <a name='create-workspace'></a>작업 영역 만들기

여러 가지 방법으로 작업 영역을 만들 수 있습니다.

* 지점 및 클릭 인터페이스에 대 한 [Azure Portal](how-to-manage-workspace.md) 를 사용 하 여 각 단계를 안내 합니다.
* [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 를 사용 하 여 python 스크립트 또는 Jupiter 노트북에서 즉시 작업 영역 만들기
* 회사 보안 표준을 사용 하 여 만들기를 자동화 하거나 사용자 지정 해야 하는 경우 [Azure Resource Manager 템플릿](how-to-create-workspace-template.md) 또는 [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) 를 사용 합니다.
* Visual Studio Code에서 작업 하는 경우 [VS Code 확장](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)을 사용 합니다.

## <a name="resources"></a>연결 된 리소스

새 작업 영역을 만들면 작업 영역에서 사용되는 여러 Azure 리소스가 자동으로 생성됩니다.

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): 학습 중 및 모델을 배포할 때 사용하는 docker 컨테이너를 등록합니다. 비용을 최소화 하기 위해 ACR은 배포 이미지를 만들 때까지 **지연 로드** 됩니다.
+ [Azure Storage 계정](https://azure.microsoft.com/services/storage/): 작업 영역에 대한 기본 데이터 저장소로 사용됩니다.  노트북 Vm에서 사용 되는 jupyter 노트북도 여기에 저장 됩니다.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): 모델에 대한 모니터링 정보를 저장합니다.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 컴퓨팅 대상에서 사용되는 비밀 및 작업 영역에 필요한 기타 중요한 정보를 저장합니다.

> [!NOTE]
> 새 버전을 만드는 것 외에 기존 Azure 서비스를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning를 시작 하려면 다음을 참조 하세요.

+ [Azure Machine Learning 개요](overview-what-is-azure-ml.md)
+ [작업 영역 만들기](how-to-manage-workspace.md)
+ [작업 영역 관리](how-to-manage-workspace.md)
+ [자습서: 모델 학습시키기](tutorial-train-models-with-aml.md)
