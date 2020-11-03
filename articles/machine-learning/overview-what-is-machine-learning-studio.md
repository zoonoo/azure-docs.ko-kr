---
title: Azure Machine Learning Studio란?
description: Azure Machine Learning 스튜디오는 Azure Machine Learning 작업 영역에 대한 웹 포털입니다. 스튜디오는 코드 없는 환경과 코드 우선 환경을 결합하여 포괄적인 데이터 과학 플랫폼을 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 31e129b9b1a4b5161ed33c27ea4da76168170dbe
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495993"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio란?

이 문서에서는 [Azure Machine Learning](overview-what-is-azure-ml.md)의 데이터 과학자 개발자를 위한 웹 포털인 Azure Machine Learning 스튜디오에 대해 알아봅니다. 스튜디오는 포괄적인 데이터 과학 플랫폼을 위해 코드 없는 환경과 코드 우선 환경을 결합합니다.

이 문서에서는 다음에 대해 알아봅니다.
>[!div class="checklist"]
> - 스튜디오에서 [기계 학습 프로젝트를 작성](#author-machine-learning-projects)하는 방법.
> - 스튜디오에서 [자산 및 리소스를 관리](#manage-assets-and-resources)하는 방법.
> - [Azure Machine Learning 스튜디오와 ML Studio(클래식)](#ml-studio-classic-vs-azure-machine-learning-studio) 간의 차이점.


## <a name="author-machine-learning-projects"></a>기계 학습 프로젝트 작성

스튜디오는 형식 프로젝트와 사용자 환경 수준에 따라 여러 제작 환경을 제공합니다.

+ **Notebooks**

  스튜디오에 직접 통합된 관리되는 [Jupyter Notebook 서버](how-to-run-jupyter-notebooks.md)에서 직접 코드를 작성하고 실행합니다. 

+ **Azure Machine Learning 디자이너**

  디자이너를 사용하여 코드를 작성하지 않고도 기계 학습 모델을 학습하고 배포할 수 있습니다. 데이터 세트 및 모듈을 끌어서 놓아 ML 파이프라인을 만듭니다. [디자이너 자습서](tutorial-designer-automobile-price-train-score.md)를 사용해 보세요.

    ![Azure Machine Learning 디자이너 예](media/concept-designer/designer-drag-and-drop.gif)

+ **자동화된 기계 학습 UI**

  사용하기 쉬운 인터페이스로 [자동화된 ML 실험](tutorial-first-experiment-automated-ml.md)을 만드는 방법을 알아보세요. 

  [![Azure Machine Learning Studio 탐색 창](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **데이터 레이블 지정**

    [Azure Machine Learning 데이터 레이블 지정](how-to-create-labeling-projects.md)을 사용하여 데이터 레이블 지정 프로젝트를 효율적으로 조정합니다.

## <a name="manage-assets-and-resources"></a>자산 및 리소스 관리

브라우저에서 직접 기계 학습 자산을 관리합니다. 원활한 환경을 위해 SDK와 스튜디오 간에 자산이 동일한 작업 영역에서 공유됩니다. 스튜디오를 사용하여 다음을 관리합니다.

- 모델
- 데이터 세트
- 데이터 저장소
- Compute 리소스
- Notebooks
- 실험
- 실행 로그
- Pipelines 
- 파이프라인 엔드포인트

숙련된 개발자라 하더라도 스튜디오는 작업 영역 리소스를 관리하는 방법을 단순화할 수 있습니다.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio(클래식) 및 Azure Machine Learning 스튜디오

2015년에 출시된 **ML Studio(클래식)** 는 첫 번째 끌어서 놓기 기계 학습 작성기였습니다. 시각적 환경만 제공하는 독립 실행형 서비스입니다. Studio(클래식)는 Azure Machine Learning과 상호 운용되지 않습니다.

**Azure Machine Learning** 은 완전한 데이터 과학 플랫폼을 제공하는 별도의 현대화된 서비스입니다. 코드 우선 및 낮은 코드 환경을 모두 지원합니다.

**Azure Machine Learning 스튜디오** 는 프로젝트 작성 및 자산 관리를 위한 낮은 코드와 코드 없음 옵션이 포함된 Azure Machine Learning *의* 웹 포털입니다. 

새로운 사용자는 최신 데이터 과학 도구 범위에 ML Studio(클래식) 대신 **Azure Machine Learning** 을 선택하는 것이 좋습니다.

### <a name="feature-comparison"></a>기능 비교

다음 표에는 ML Studio(클래식)와 Azure Machine Learning 간에 주요 차이점이 요약되어 있습니다.

| 기능 | ML Studio(클래식) | Azure Machine Learning |
|---| --- | --- |
| 끌어서 놓기 인터페이스 | 클래식 환경 | 환경이 업데이트됨 - [Azure Machine Learning 디자이너](concept-designer.md)| 
| 코드 SDK | 지원되지 않음 | [Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/) 및 [R](tutorial-1st-r-experiment.md) SDK와 완전히 통합됨 |
| 실험 | 크기 조정 가능(10GB 학습 데이터 제한) | 컴퓨팅 대상으로 크기 조정 |
| 컴퓨팅 대상 학습 | 전용 컴퓨팅 대상, CPU만 지원 | 광범위한 사용자 지정이 가능한 [컴퓨팅 대상 학습](concept-compute-target.md#train). GPU 및 CPU 지원 포함 | 
| 컴퓨팅 대상 배포 | 전용 웹 서비스 형식, 사용자 지정 불가능 | 광범위한 사용자 지정이 가능한 [컴퓨팅 대상 배포](concept-compute-target.md#deploy). GPU 및 CPU 지원 포함 |
| ML 파이프라인 | 지원되지 않음 | 유연한 모듈식 [파이프라인](concept-ml-pipelines.md)을 빌드하여 워크플로 자동화 |
| MLOps | 기본 모델 관리 및 배포, CPU 전용 배포 | 엔터티 버전 관리(모델, 데이터, 워크플로), 워크플로 자동화, CICD 도구와의 통합, CPU 및 GPU 배포 [등](concept-model-management-and-deployment.md) |
| 모델 형식 | 전용 형식, Studio(클래식)만 해당 | 학습 작업 유형에 따라 지원되는 여러 형식 |
| 자동화된 모델 학습 및 하이퍼 매개변수 튜닝 |  지원되지 않음 | [지원됨](concept-automated-ml.md)이라는 의미입니다. 코드 우선 및 코드 없음 옵션. | 
| 데이터 드리프트 검색 | 지원되지 않음 | [지원됨](how-to-monitor-datasets.md) |
| 데이터 레이블 지정 프로젝트 | 지원되지 않음 | [지원됨](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>다음 단계

[스튜디오](https://ml.azure.com)를 방문하거나 다음 자습서를 통해 다양한 제작 옵션을 살펴보세요.  

- + [자체 개발 환경에서 시작](tutorial-1st-experiment-sdk-setup-local.md)
  + [컴퓨팅 인스턴스에서 Jupyter Notebooks를 사용하여 모델 학습 및 배포](tutorial-1st-experiment-sdk-setup.md)
  + [자동화된 기계 학습을 사용하여 모델 학습 및 배포](tutorial-first-experiment-automated-ml.md)  
  + [디자이너를 사용하여 모델 학습 및 배포](tutorial-designer-automobile-price-train-score.md)

