---
title: 디자이너를 사용 하 여 ML 모델 빌드
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 디자이너를 구성 하는 용어, 개념 및 워크플로에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517866"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Azure Machine Learning 디자이너 (미리 보기) 란? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning designer를 사용 하면 코드를 작성 하지 않고도 데이터를 준비 하 고, 학습, 테스트, 배포, 관리 및 추적할 수 있습니다.

모델을 생성 하기 위해 [데이터 집합](#datasets) 및 [모듈](#module) 을 시각적으로 연결 하는 데 필요한 프로그래밍은 없습니다.

디자이너는 Azure Machine Learning [작업 영역](concept-workspace.md) 을 사용 하 여 다음 작업을 수행 합니다.

+ 작업 영역에서 [파이프라인](#pipeline) 을 만들고, 편집 하 고, 실행 합니다.
+ [데이터 집합](#datasets)에 액세스 합니다.
+ 작업 영역의 [계산 리소스](#compute) 를 사용 하 여 파이프라인을 실행 합니다. 
+ [모델](concept-azure-machine-learning-architecture.md#models)을 등록 합니다.
+ 파이프라인을 REST 끝점으로 [게시](#publish) 합니다.
+ 모델을 파이프라인 끝점 (일괄 처리 유추의 경우) 또는 작업 영역의 계산 리소스에 대 한 실시간 끝점으로 [배포](#deployment) 합니다.

![디자이너 개요](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>워크플로

디자이너는 모델을 신속 하 게 빌드, 테스트 및 반복할 수 있는 대화형 시각적 캔버스를 제공 합니다. 

+ [데이터 집합](#datasets) 및 [모듈](#module) 을 캔버스로 끌어서 놓습니다.
+ 모듈을 함께 연결 하 여 [파이프라인](#pipeline)을 구성 합니다.
+ Machine Learning 서비스 작업 영역의 계산 리소스를 사용 하 여 파이프라인을 실행 합니다.
+ 파이프라인을 편집 하 고 다시 실행 하 여 모델 디자인을 반복 합니다.
+ 준비가 되 면 **학습 파이프라인** 을 **유추 파이프라인**으로 변환 합니다.
+ Python 코드를 생성 하지 않고 다시 전송 하려면 파이프라인을 REST 끝점으로 [게시](#publish) 합니다.
+ 다른 사용자가 모델에 액세스할 수 있도록 유추 파이프라인을 파이프라인 끝점 또는 실시간 끝점으로 [배포](#deployment) 합니다.

## <a name="pipeline"></a>파이프라인

처음부터 ML [파이프라인](concept-azure-machine-learning-architecture.md#ml-pipelines) 을 만들거나 기존 샘플 파이프라인을 템플릿으로 사용 합니다. 파이프라인을 실행할 때마다 아티팩트는 작업 영역에 저장 됩니다. 파이프라인 실행은 [실험](concept-azure-machine-learning-architecture.md#experiments)으로 그룹화 됩니다.

파이프라인은 모델을 생성 하기 위해 함께 연결 하는 데이터 집합 및 분석 모듈로 구성 됩니다. 특히 유효한 파이프라인에는 다음과 같은 특징이 있습니다.

* 데이터 집합은 모듈에만 연결 될 수 있습니다.
* 모듈은 데이터 집합 또는 다른 모듈에 연결 될 수 있습니다.
* 모듈에 대 한 모든 입력 포트는 데이터 흐름에 대 한 일부 연결을 포함 해야 합니다.
* 각 모듈에 필요한 모든 매개 변수를 설정 해야 합니다.


디자이너를 시작 하는 방법을 알아보려면 [자습서: 디자이너를 사용 하 여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)을 참조 하세요.

## <a name="datasets"></a>데이터 세트

Machine learning 데이터 집합을 사용 하면 데이터에 쉽게 액세스 하 고 작업할 수 있습니다. 실험을 위해 디자이너에 많은 샘플 데이터 집합이 포함 되어 있습니다. 필요에 따라 더 많은 데이터 집합을 [등록할](./how-to-create-register-datasets.md) 수 있습니다.

## <a name="module"></a>모듈

모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. 디자이너에는 데이터 수신 기능부터 학습, 점수 매기기 및 유효성 검사 프로세스에 이르는 다양 한 모듈이 있습니다.

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택 하면 모듈의 매개 변수가 캔버스 오른쪽의 속성 창에 표시 됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다.

![모듈 속성](media/ui-concept-visual-interface/properties.png)

사용 가능한 기계 학습 알고리즘 라이브러리를 탐색 하는 데 도움이 필요한 경우 [알고리즘 & 모듈 참조 개요](../algorithm-module-reference/module-reference.md) 를 참조 하세요.

## <a name="compute"></a>계산 리소스

작업 영역의 계산 리소스를 사용 하 여 파이프라인을 실행 하 고 배포 된 모델을 실시간 끝점이 나 파이프라인 끝점 (일괄 처리 유추의 경우)으로 호스팅합니다. 지원되는 컴퓨팅 대상은 다음과 같습니다.

| 컴퓨팅 대상 | 교육 | 배포 |
| ---- |:----:|:----:|
| Azure Machine Learning 컴퓨팅 | ✓ | |
| Azure Kubernetes Service | | ✓ |

계산 대상은 Machine Learning [작업 영역](concept-workspace.md)에 연결 됩니다. [Azure Machine Learning studio](https://ml.azure.com)의 작업 영역에서 계산 대상을 관리 합니다.

## <a name="publish"></a>게시

파이프라인이 준비 되 면 REST 끝점으로 게시할 수 있습니다. [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) 를 생성 한 Python 코드 없이 제출할 수 있습니다.

또한 PublishedPipeline를 사용 하 여 다른 PipelineParameter 값 및 입력으로 파이프라인을 다시 제출할 수 있습니다.

## <a name="deployment"></a>배포

예측 모델이 준비 되 면 디자이너에서 바로 파이프라인 끝점 또는 실시간 끝점으로 배포 합니다.

파이프라인 끝점은 다양 한 PipelineParameter 값을 사용 하 여 실행 되는 파이프라인을 제출할 수 있는 PublishedPipeline 및 일괄 처리 유추를 위한 입력입니다.

실시간 끝점은 응용 프로그램과 점수 매기기 모델 간의 인터페이스를 제공 합니다. 외부 응용 프로그램은 실제 시간에 점수 매기기 모델과 통신할 수 있습니다. 실시간 끝점에 대 한 호출은 외부 응용 프로그램에 예측 결과를 반환 합니다. 실시간 끝점에 대 한 호출을 수행 하려면 끝점을 배포할 때 생성 된 API 키를 전달 합니다. 끝점은 웹 프로그래밍 프로젝트용으로 널리 사용 되는 다른 아키텍처를 기반으로 합니다.

모델을 배포 하는 방법을 알아보려면 [자습서: 디자이너를 사용 하 여 machine learning 모델 배포](tutorial-designer-automobile-price-deploy.md)를 참조 하세요.

## <a name="moving-from-the-visual-interface-to-the-designer"></a>시각적 인터페이스에서 디자이너로 이동

시각적 인터페이스 (미리 보기)가 업데이트 되었으며 현재 디자이너 (미리 보기)를 Azure Machine Learning 합니다. 디자이너는 Azure Machine Learning의 다른 기능과 완벽 하 게 통합 되는 파이프라인 기반 백 엔드를 사용 하도록 다시 설계 되었습니다. 

이러한 업데이트의 결과로 시각적 인터페이스의 일부 개념과 용어는 변경 되거나 이름이 변경 되었습니다. 가장 중요 한 개념 변경 내용에 대해서는 아래 표를 참조 하세요. 

| 디자이너의 개념 | 이전에 시각적 인터페이스에서 |
| ---- |:----:|
| 파이프라인 초안 | 실험 |
| 실시간 끝점 | 웹 서비스 |

### <a name="migrating-to-the-designer"></a>디자이너로 마이그레이션

디자이너에서 기존 시각적 인터페이스 실험 및 웹 서비스를 파이프라인과 실시간 끝점으로 변환할 수 있습니다. 다음 단계를 사용 하 여 시각적 인터페이스 자산을 마이그레이션합니다.

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>다음 단계

* [자습서: 디자이너를 사용 하 여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md) 을 통해 예측 분석 및 기계 학습의 기본 사항을 알아봅니다.
* 샘플 중 하나를 사용 하 고 요구 사항에 맞게 수정 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3-기능 선택이 포함 된 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6-분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7-텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)

