---
title: 작업 영역 이란
titleSuffix: Azure Machine Learning service
description: 작업 영역은 Azure Machine Learning 서비스의 최상위 리소스입니다. 로그, 메트릭, 출력 및 스크립트의 스냅숏을 포함 한 모든 학습 실행의 기록을 유지 합니다. 이 정보를 사용 하 여 최상의 모델을 생성 하는 교육 실행 하는 확인 하려면
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 3ecdf62cfed7d70873f3dc752bfacd134e367a90
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388963"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>서비스 작업 영역을 Azure Machine Learning 이란?

작업 영역은 Azure Machine Learning 서비스를 사용할 때 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 제공 하는 Azure Machine Learning 서비스에 대 한 최상위 리소스입니다.  작업 영역에는 로그, 메트릭, 출력 및 스크립트의 스냅숏을 포함 한 모든 학습 실행의 기록을 유지 합니다. 이 정보를 사용하여 최고의 모델을 생성하는 학습 실행을 확인합니다.  

원하는 모델을 만든 후 작업 영역에 등록 합니다. 사용 하 여 등록 된 모델 및 점수 매기기 스크립트를 프로그래밍할 수 필드 FPGA (gate array)는 REST 기반 HTTP 끝점으로 또는 Azure Kubernetes Service를 Azure Container Instances에 배포. 또한 Azure IoT Edge 장치에 모듈로 모델을 배포할 수 있습니다.

## <a name="taxonomy"></a>분류 

다음은 작업 영역의 분류 체계를 보여주는 다이어그램입니다.

[![작업 영역 분류](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

다이어그램의 작업 영역을 다음 구성 요소를 보여 줍니다.

+ 작업 영역을 포함할 수 있습니다 [Notebook Vm](quickstart-run-cloud-notebook.md), 클라우드 리소스를 Azure Machine Learning을 실행 하는 데 필요한 Python 환경을 사용 하 여 구성 합니다.
+ [사용자 역할](how-to-assign-roles.md) 사용 하면 다른 사용자, 팀 또는 프로젝트를 사용 하 여 작업 영역을 공유할 수 있습니다.
+ [계산 대상](concept-azure-machine-learning-architecture.md#compute-target) 실험을 실행 하는 데 사용 됩니다.
+ 작업 영역을 만들면 [관련 리소스](#resources) 도 자동으로 만들어집니다.
+ [실험](concept-azure-machine-learning-architecture.md#experiment) 교육 실행 모델을 빌드하는 데 사용 됩니다.  만들고 사용 하 여 실험을 실행할 수 있습니다.
    + 합니다 [Azure Machine Learning Python for SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)합니다.
    + 합니다 [기계 학습 실험 (미리 보기) 자동](how-to-create-portal-experiments.md) 섹션에서는 Azure portal에서 합니다.
    + 합니다 [시각적 인터페이스 (미리 보기)](ui-concept-visual-interface.md)합니다.
+ [파이프라인](concept-azure-machine-learning-architecture.md#pipeline) 학습 및 모델을 재 학습에 대 한 재사용 가능한 워크플로 됩니다.
+ [데이터 집합](concept-azure-machine-learning-architecture.md#dataset) 모델 학습 및 파이프라인 만들기에 대 한 사용 데이터를 관리할 수 있도록 지원 합니다.
+ 배포 하려는 모델을 만든 후 만든를 [등록 된 모델](concept-azure-machine-learning-architecture.md#model-registry)합니다.
+ 등록 된 모델 및 점수 매기기 스크립트를 만드는 데는 [배포](concept-azure-machine-learning-architecture.md#image-registry)합니다.

## <a name="tools-for-workspace-interaction"></a>작업 영역 상호 작용을 위한 도구

다음과 같은 방법으로 작업 영역을 조작할 수 있습니다.

+ 웹:
    + [Azure Portal](https://portal.azure.com)
    + [시각적 인터페이스 (미리 보기)](ui-concept-visual-interface.md)
+ Python에서 Azure Machine Learning을 사용 하 여 [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Azure Machine Learning을 사용 하 여 명령줄에서 [CLI 확장](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>기계 학습 작업 영역을 사용 하 여

기계 학습 작업 읽거나 작업 영역에 아티팩트를 작성 합니다. 

+ 쓰기 작업 영역에 실행된 결과 실험-모델을 학습 실험을 실행 합니다.
+ 사용 하 여 자동화-모델을 학습 하는 ML 작업 영역에 학습 결과 기록 합니다.
+ 작업 영역에서 모델을 등록 합니다.
+ 모델 배포-배포를 만드는 데는 등록 된 모델을 사용 합니다.
+ 만들고 재사용 가능한 워크플로 실행 합니다.
+ 보기 기계 학습 실험, 파이프라인, 모델, 배포 등의 아티팩트입니다.
+ 추적 및 모니터링 모델입니다.

## <a name="workspace-management"></a>작업 영역 관리

또한 다음 작업 영역 관리 작업을 수행할 수 있습니다.

| 작업 영역 관리 작업   | 포털              | SDK)        | CLI        |
|---------------------------|------------------|------------|------------|
| 작업 영역 만들기        | **&check;**     | **&check;** | **&check;** |
| 계산 리소스 만들기 및 관리    | **&check;**   | **&check;** |  **&check;**   |
| 작업 영역 액세스 관리    | **&check;**   | |  **&check;**    |
| Notebook VM을 만듭니다. | **&check;**   | |     |

하 여 서비스를 사용 하 여 시작 [작업 영역을 만드는](setup-create-workspace.md)합니다.

## <a name="resources"></a> 연결 된 리소스

새 작업 영역을 만들면 작업 영역에서 사용되는 여러 Azure 리소스가 자동으로 생성됩니다.

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): 학습 중 및 모델을 배포할 때 사용하는 docker 컨테이너를 등록합니다. ACR은 비용을 최소화 하려면 **지연 로드** 배포 이미지를 만들 때까지 합니다.
+ [Azure Storage 계정](https://azure.microsoft.com/services/storage/): 작업 영역에 대한 기본 데이터 저장소로 사용됩니다.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): 모델에 대한 모니터링 정보를 저장합니다.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 컴퓨팅 대상에서 사용되는 비밀 및 작업 영역에 필요한 기타 중요한 정보를 저장합니다.

> [!NOTE]
> 새 버전을 만드는 것 외에 기존 Azure 서비스를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning Service를 시작하려면 다음을 참조하세요.

+ [Azure Machine Learning 서비스 개요](overview-what-is-azure-ml.md)
+ [작업 영역 만들기](setup-create-workspace.md)
+ [작업 영역 관리](how-to-manage-workspace.md)
+ [자습서: 모델 학습시키기](tutorial-train-models-with-aml.md)
