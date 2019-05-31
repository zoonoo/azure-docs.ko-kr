---
title: 시각적 인터페이스
titleSuffix: Azure Machine Learning service
description: 용어, 개념 및 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)를 구성 하는 워크플로 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 0b158a1d713e0de8f3d1b2672aed442fce66e724
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917162"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Azure Machine Learning 서비스에 대 한 시각적 인터페이스는 무엇입니까? 

Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)를 사용 하면 데이터 준비, 학습, 테스트, 배포, 관리 및 코드를 작성 하지 않고 기계 학습 모델을 추적할 수 있습니다.

필요한 프로그래밍 없이, 시각적으로 연결 [데이터 집합](#dataset) 하 고 [모듈](#module) 모델을 생성 합니다.

Azure Machine Learning 서비스를 사용 하는 시각적 인터페이스 [작업 영역](concept-workspace.md) 에:

+ 아티팩트를 작성 [실험](#experiment) 작업 영역으로 실행 합니다.
+ 액세스 [데이터 집합](#dataset)합니다.
+ 사용 된 [계산 리소스](#compute) 실험을 실행 하려면 작업 영역에서 합니다. 
+ 등록할 [모델](concept-azure-machine-learning-architecture.md#model)합니다.
+ [배포](#deployment) 모델에서 웹 서비스 작업 영역에서 리소스를 계산 합니다.

![시각적 인터페이스 개요](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>워크플로

시각적 인터페이스는 신속 하 게 빌드, 테스트 및 반복 모델을 시각적, 대화형 캔버스를 제공 합니다. 

+ 하면 끌어서 놓기 [모듈](#module) 캔버스로 합니다.
+ 폼에 모듈을 함께 연결 된 [실험](#experiment)합니다.
+ Machine Learning 서비스 작업 영역의 계산 리소스를 사용 하 여 실험을 실행 합니다.
+ 실험을 편집 하 고 다시 실행 하 여 모델 디자인을 반복 합니다.
+ 준비 된 경우 변환에 **학습 실험** 에 **예측 실험**합니다.
+ [배포](#deployment) 예측 실험을 웹 서비스 모델에 다른 사용자가 액세스할 수 있도록 합니다.

## <a name="experiment"></a>실험

실험을 처음부터 만들거나 기존 샘플 실험을 템플릿으로 사용 합니다.  실험을 실행할 때마다 아티팩트 작업 영역에 저장 됩니다.

데이터 집합 및 모델을 생성 하는 함께 연결 하는 분석 모듈을 실험 구성 됩니다. 특히 유효한 실험에는 다음 특성이 포함됩니다.

* 데이터 집합 모듈에만 연결할 수 있습니다.
* 모듈은 데이터 집합 또는 다른 모듈에 연결할 수 있습니다.
* 모듈에 대 한 모든 입력된 포트에는 데이터 흐름에 대 한 연결이 있어야 합니다.
* 모든 필요한 각 모듈에 대 한 매개 변수를 설정 해야 합니다.

간단한 실험의 예제를 참조 하세요. [빠른 시작: 준비 하 고 Azure Machine Learning에서 코드를 작성 하지 않고도 데이터를 시각화](ui-quickstart-run-experiment.md)합니다.

예측 분석 솔루션의 자세한 연습을 참조 하세요. [자습서: 시각적 인터페이스를 사용 하 여 자동차 가격 예측](ui-tutorial-automobile-price-train-score.md)합니다.

## <a name="dataset"></a>데이터 세트

데이터 집합은 모델링 프로세스에서 사용 하는 시각적 인터페이스에 업로드 된 데이터입니다. 다양 한 샘플 데이터 집합을 실험할 수 포함 되며 필요에 따라 추가 데이터 집합을 업로드할 수 있습니다.

## <a name="module"></a>모듈

모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. 시각적 인터페이스에 다양 한 학습, 점수 매기기 및 유효성 검사 프로세스에서 데이터 수신 함수 사이의 모듈이 있습니다.

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택 하면 모듈의 매개 변수는 캔버스의 오른쪽 속성 창에 표시 됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다.

![모듈 속성](media/ui-concept-visual-interface/properties.png)

탐색 하기 위한 일부 도움말 가능한 기계 학습 알고리즘의 라이브러리를 참조 하세요. [알고리즘 및 모듈 참조 개요](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> 계산 리소스

사용 하 여 웹 서비스로 실험 또는 호스트에 배포 된 모델을 실행을 위해 작업 영역에서 리소스를 계산 합니다. 지원되는 계산 대상은 다음과 같습니다.


| 계산 대상 | 교육 | 배포 |
| ---- |:----:|:----:|
| Azure Machine Learning 컴퓨팅 | ✓ | |
| Azure Kubernetes Service | | ✓ |

계산 대상은 Machine Learning에 연결 된 [작업 영역](concept-workspace.md)합니다. 계산 대상에서 작업 영역에서 관리 하는 [Azure portal](https://portal.azure.com)합니다.

## <a name="deployment"></a>배포

예측 분석 모델이 준비 되 면 시각적 인터페이스에서 직접 웹 서비스로 배포 합니다.

웹 서비스 응용 프로그램 및 점수 매기기 모델 간의 인터페이스를 제공 합니다. 외부 응용 프로그램은 실시간에서 점수 매기기 모델을 사용 하 여 통신할 수 있습니다. 웹 서비스에 대 한 호출 외부 응용 프로그램에 예측 결과 반환합니다. 웹 서비스를 호출하려면 웹 서비스를 배포할 때 만들어진 API 키를 전달합니다. 웹 서비스는 웹 프로그래밍 프로젝트에 널리 사용 되는 아키텍처인 REST를 기반으로 합니다.

모델을 배포 하는 방법에 알아보려면 참조 [자습서: 시각적 인터페이스를 사용 하 여 기계 학습 모델을 배포](ui-tutorial-automobile-price-deploy.md)합니다.

## <a name="next-steps"></a>다음 단계

* 예측 분석의 기본 사항을 알아보고 기계 학습으로 [빠른 시작: 준비 하 고 Azure Machine Learning에서 코드를 작성 하지 않고도 데이터를 시각화](ui-quickstart-run-experiment.md)합니다.
* 샘플 중 하나를 사용 하 고 도구 모음에 맞게 수정 합니다.
    * [1-회귀 샘플: 가격 예측](ui-sample-regression-predict-automobile-price-basic.md)
    * [2-회귀 샘플: 가격을 예측 하 고 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [3-분류 샘플: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
    * [4-분류 샘플: (중요 한 비용) 신용 위험 예측](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [5-분류 샘플: 변동, 욕구를 및 상향 판매를 예측 합니다.](ui-sample-classification-predict-churn.md)
