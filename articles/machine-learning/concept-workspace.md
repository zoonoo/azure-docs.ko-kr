---
title: 작업 영역이란 무엇입니까?
titleSuffix: Azure Machine Learning
description: 작업 영역은 Azure 기계 학습을 위한 최상위 리소스입니다. 로그, 메트릭, 출력 및 스크립트의 스냅숏을 포함하여 모든 학습 실행 기록을 유지합니다. 이 정보를 사용하여 최상의 모델을 생성하는 교육 실행을 결정합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505568"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Azure 기계 학습 작업 영역이란 무엇입니까?

작업 영역은 Azure 기계 학습을 위한 최상위 리소스로, Azure 기계 학습을 사용할 때 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 위치를 제공합니다.  작업 영역은 로그, 메트릭, 출력 및 스크립트의 스냅숏을 포함하여 모든 학습 실행 기록을 유지합니다. 이 정보를 사용하여 최고의 모델을 생성하는 학습 실행을 확인합니다.  

원하는 모델이 있으면 작업 영역에 등록합니다. 그런 다음 등록된 모델 및 점수 매기기 스크립트를 사용하여 Azure 컨테이너 인스턴스, Azure Kubernetes 서비스 또는 현장 프로그래밍 가능한 게이트 배열(FPGA)에 REST 기반 HTTP 끝점으로 배포합니다. 모델을 Azure IoT Edge 장치에 모듈로 배포할 수도 있습니다.

사용 가능한 가격 및 기능은 작업 영역에 [대해 기본 버전 또는 엔터프라이즈 버전을](overview-what-is-azure-ml.md#sku) 선택했는지 여부에 따라 달라집니다. 작업 영역을 만들 때 [에디션을 선택합니다.](#create-workspace)  기본 버전에서 엔터프라이즈 버전으로 [업그레이드할](#upgrade) 수도 있습니다.

## <a name="taxonomy"></a>분류 

다음은 작업 영역의 분류 체계를 보여주는 다이어그램입니다.

[![작업 영역 분류](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

다이어그램은 작업 영역의 다음 구성 요소를 보여 주며 다음과 같은 구성 요소를 보여 주며 있습니다.

+ 작업 영역에는 [Azure 기계 학습 계산 인스턴스,](concept-compute-instance.md)Azure 기계 학습을 실행하는 데 필요한 Python 환경으로 구성된 클라우드 리소스가 포함될 수 있습니다.

+ [사용자 역할을](how-to-assign-roles.md) 사용하면 작업 영역을 다른 사용자, 팀 또는 프로젝트와 공유할 수 있습니다.
+ [계산 대상은](concept-azure-machine-learning-architecture.md#compute-targets) 실험을 실행하는 데 사용됩니다.
+ 작업 영역을 만들 때 [관련 리소스도](#resources) 만들어집니다.
+ [실험은](concept-azure-machine-learning-architecture.md#experiments) 모델을 빌드하는 데 사용하는 교육 실행입니다.  
+ [파이프라인은](concept-azure-machine-learning-architecture.md#ml-pipelines) 모델을 교육하고 재교육하기 위한 재사용 가능한 워크플로우입니다.
+ [데이터 집합은](concept-azure-machine-learning-architecture.md#datasets-and-datastores) 모델 학습 및 파이프라인 생성에 사용하는 데이터 관리를 지원합니다.
+ 배포할 모델이 있으면 등록된 모델을 만듭니다.
+ 등록된 모델과 점수 매기기 스크립트를 사용하여 [배포 끝점을](concept-azure-machine-learning-architecture.md#endpoints)만듭니다.

## <a name="tools-for-workspace-interaction"></a>작업 영역 상호 작용을 위한 도구

다음과 같은 방법으로 작업 영역과 상호 작용할 수 있습니다.

+ 웹에서:
    + [Azure 기계 학습 스튜디오](https://ml.azure.com) 
    + [Azure 기계 학습 디자이너(미리 보기)](concept-designer.md) - [엔터프라이즈 버전 작업](overview-what-is-azure-ml.md#sku) 공간에서만 사용할 수 있습니다.
+ 파이썬에 대한 [Azure 기계 학습 SDK와](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)모든 파이썬 환경에서 .
+ [R에 대한 Azure 기계 학습 SDK가](https://azure.github.io/azureml-sdk-for-r/reference/index.html)있는 모든 R 환경에서
+ Azure 기계 학습 [CLI 확장을](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) 사용하는 명령줄에서

## <a name="machine-learning-with-a-workspace"></a>작업 영역이 있는 기계 학습

기계 학습 작업은 작업 영역에 아티팩트를 읽거나 작성합니다.

+ 실험을 실행하여 모델을 학습- 작업 영역에 실험 실행 결과를 씁니다.
+ 자동화된 ML을 사용하여 모델을 학습- 작업 영역에 교육 결과를 기록합니다.
+ 작업 영역에 모델을 등록합니다.
+ 모델 배포 - 등록된 모델을 사용하여 배포를 만듭니다.
+ 재사용 가능한 워크플로를 만들고 실행합니다.
+ 실험, 파이프라인, 모델, 배포와 같은 기계 학습 아티팩트를 봅니다.
+ 모델을 추적하고 모니터링합니다.

## <a name="workspace-management"></a>작업 영역 관리

다음 작업 영역 관리 작업을 수행할 수도 있습니다.

| 작업 영역 관리 작업   | 포털              | 스튜디오 | 파이썬 SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| 작업 영역 만들기        | **&check;**     | | **&check;** | **&check;** |
| 작업 영역 액세스 관리    | **&check;**   || |  **&check;**    |
| 엔터프라이즈 버전으로 업그레이드    | **&check;** | **&check;**  | |     |
| 컴퓨팅 리소스 생성 및 관리    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| 노트북 VM 만들기 |   | **&check;** | |     |

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>작업 영역 만들기

작업 영역을 만들 때 [기본 버전 또는 엔터프라이즈 버전으로](overview-what-is-azure-ml.md#sku)만들지 여부를 결정합니다. 에디션은 작업 영역에서 사용할 수 있는 기능을 결정합니다. 엔터프라이즈 버전은 Azure 기계 학습 [디자이너](concept-designer.md) 및 자동화된 기계 학습 실험을 빌드하는 스튜디오 버전에 액세스할 [수 있습니다.](tutorial-first-experiment-automated-ml.md)  자세한 내용 및 가격 정보는 [Azure 기계 학습 가격 책정을](https://azure.microsoft.com/pricing/details/machine-learning/)참조하십시오.

작업 영역을 만드는 방법에는 여러 가지가 있습니다.  

* 포인트 앤 클릭 인터페이스에 [Azure 포털을](how-to-manage-workspace.md) 사용하여 각 단계를 안내합니다.
* [파이썬용 Azure 기계 학습 SDK를](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 사용하여 파이썬 스크립트 또는 목성 노트북에서 즉석에서 작업 공간을 만듭니다.
* 회사 보안 표준으로 생성을 자동화하거나 사용자 지정해야 하는 경우 [Azure 리소스 관리자 템플릿](how-to-create-workspace-template.md) 또는 Azure 기계 학습 [CLI를](reference-azure-machine-learning-cli.md) 사용합니다.
* Visual Studio 코드에서 작업하는 경우 [VS 코드 확장을](tutorial-setup-vscode-extension.md)사용합니다.

> [!NOTE]
> 작업 영역 이름은 대/소문자를 구분하지 않습니다.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>엔터프라이즈 버전으로 업그레이드

Azure [포털을 사용하여 작업 영역을 기본 버전에서 엔터프라이즈 버전으로 업그레이드할](how-to-manage-workspace.md#upgrade) 수 있습니다. 엔터프라이즈 에디션 작업 영역을 기본 에디션 작업 영역으로 다운그레이드할 수 없습니다. 

## <a name="associated-resources"></a><a name="resources"></a>관련 리소스

새 작업 영역을 만들면 작업 영역에서 사용되는 여러 Azure 리소스가 자동으로 생성됩니다.

+ [Azure 컨테이너 레지스트리:](https://azure.microsoft.com/services/container-registry/)학습 중 및 모델을 배포할 때 사용하는 docker 컨테이너를 등록합니다. 비용을 최소화하기 위해 배포 이미지가 만들어지때까지 ACR이 **지연 로드됩니다.**
+ [Azure 저장소 계정](https://azure.microsoft.com/services/storage/): 작업 영역의 기본 데이터 저장소로 사용됩니다.  Azure 기계 학습 계산 인스턴스와 함께 사용되는 Jupyter 노트북도 여기에 저장됩니다.
+ [Azure 응용 프로그램 인사이트](https://azure.microsoft.com/services/application-insights/): 모델에 대한 모니터링 정보를 저장합니다.
+ [Azure 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/): 계산 대상및 작업 영역에 필요한 기타 중요한 정보에 사용되는 비밀을 저장합니다.

> [!NOTE]
> 새 버전을 만드는 것 외에 기존 Azure 서비스를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습을 시작하려면 다음을 참조하십시오.

+ [Azure 기계 학습 개요](overview-what-is-azure-ml.md)
+ [작업 영역 만들기](how-to-manage-workspace.md)
+ [작업 영역 관리](how-to-manage-workspace.md)
+ [자습서: 파이썬 SDK로 첫 번째 ML 실험 만들기 시작](tutorial-1st-experiment-sdk-setup.md)
+ [자습서: R SDK를 통해 Azure 기계 학습을 시작](tutorial-1st-r-experiment.md)
+ [자습서: 자동화된 기계 학습을 사용하여 첫 번째 분류 모델 만들기(엔터프라이즈](tutorial-first-experiment-automated-ml.md) [버전](overview-what-is-azure-ml.md#sku) 작업 공간에서만 사용 가능)
+ [자습서: 디자이너와 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md) [(엔터프라이즈 버전](overview-what-is-azure-ml.md#sku) 작업 공간에서만 사용 가능)
