---
title: Azure Machine Learning과 Machine Learning Studio(클래식)
description: Azure Machine Learning과 Machine Learning Studio(클래식)의 차이점은?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 1d4896a2dd2115e5654baffe394618b30a294d5b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231746"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning 및 Machine Learning Studio(클래식)

이 문서에서는 Azure Machine Learning과 Machine Learning Studio(클래식)의 차이점에 대해 알아봅니다. 

Azure Machine Learning은 Python, R SDK **및** "끌어서 놓기" 디자이너를 제공하여 기계 학습 모델을 빌드 및 배포합니다. Studio(클래식)는 독립 실행형 끌어서 놓기 환경만 제공합니다.

새 사용자는 가장 광범위한 최첨단 기계 학습 도구를 위해 Azure Machine Learning을 선택하는 것이 좋습니다.

## <a name="quick-comparison"></a>빠른 비교

다음 표에는 Azure Machine Learning과 Studio(클래식)의 주요 차이점이 요약되어 있습니다.

| 기능 | Machine Learning Studio(클래식) | Azure Machine Learning |
|---| --- | --- |
| 끌어서 놓기 인터페이스 | 지원됨 | 지원됨 - [Azure Machine Learning 디자이너(미리 보기)](concept-designer.md) <br/>(엔터프라이즈 작업 영역 필요) | 
| 실험 | 크기 조정 가능(10GB 학습 데이터 제한) | 컴퓨팅 대상으로 크기 조정 |
| 컴퓨팅 대상 학습 | 전용 컴퓨팅 대상, CPU만 지원 | 광범위한 사용자 지정이 가능한 [컴퓨팅 대상 학습](concept-compute-target.md#train). GPU 및 CPU 지원 포함 | 
| 컴퓨팅 대상 배포 | 전용 웹 서비스 형식, 사용자 지정 불가능 | 광범위한 사용자 지정이 가능한 [컴퓨팅 대상 배포](concept-compute-target.md#deploy). GPU 및 CPU 지원 포함 |
| ML 파이프라인 | 지원되지 않음 | 유연한 모듈식 [파이프라인](concept-ml-pipelines.md)을 빌드하여 워크플로 자동화 |
| MLOps | 기본 모델 관리 및 배포 | 엔터티 버전 관리(모델, 데이터, 워크플로), 워크플로 자동화, CICD 도구와 통합 [등](concept-model-management-and-deployment.md) |
| 모델 형식 | 전용 형식, Studio(클래식)만 해당 | 학습 작업 유형에 따라 지원되는 여러 형식 |
| 자동화된 모델 학습 및 하이퍼 매개변수 튜닝 |  지원되지 않음 | [SDK 및 시각적 작업 영역에서 지원됨](concept-automated-ml.md) | 
| 데이터 드리프트 검색 | 지원되지 않음 | [SDK 및 시각적 작업 영역에서 지원됨](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Machine Learning Studio(클래식)에서 마이그레이션

현재 Studio(클래식) 자산을 Azure Machine Learning 디자이너(미리 보기)로 마이그레이션할 수 있는 방법이 없습니다. 현재 Studio(클래식) 사용자는 자신의 기계 학습 자산을 계속 사용할 수 있습니다. 그러나 모든 사용자가 디자이너 사용을 고려하는 것이 좋습니다. 이 디자이너는 향상된 워크플로 **plus** 확장성, 버전 제어 및 엔터프라이즈 보안을 통해 친숙한 끌어서 놓기 환경을 제공합니다.

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning 시작

다음 리소스는 Azure Machine Learning을 시작하는 데 도움이 될 수 있습니다. 

- [Azure Machine Learning 개요](overview-what-is-azure-ml.md)를 읽어 보세요.

- [Python SDK를 사용하여 첫 번째 실험](tutorial-1st-experiment-sdk-setup.md)을 만듭니다.

- 자동 가격을 예측하려면 [첫 번째 디자이너 파이프라인을 만듭니다](tutorial-designer-automobile-price-train-score.md).

![Azure Machine Learning 디자이너 예](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에는 디자이너의 끌어서 놓기 기능 외에도 다른 도구가 있습니다.  
  + [Python Notebook을 사용하여 ML 모델 학습 및 배포](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown을 사용하여 ML 모델을 학습 및 배포](tutorial-1st-r-experiment.md) 
  + [자동화된 기계 학습을 사용하여 ML 모델 학습 및 배포](tutorial-first-experiment-automated-ml.md)  
  + [기계 학습 CLI를 사용하여 모델 학습 및 배포](tutorial-train-deploy-model-cli.md)

