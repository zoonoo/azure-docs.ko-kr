---
title: 시각적 인터페이스
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 시각적 인터페이스 (미리 보기)를 구성 하는 용어, 개념 및 워크플로에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 65daf1468d27825d9904a14e42e43796d3985321
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996516"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 시각적 인터페이스는 무엇 인가요? 

Azure Machine Learning의 시각적 인터페이스 (미리 보기)를 사용 하면 코드를 작성 하지 않고도 데이터를 준비 하 고, 학습, 테스트, 배포, 관리 및 추적할 수 있습니다.

모델을 생성 하기 위해 [데이터 집합](#dataset) 및 [모듈](#module) 을 시각적으로 연결 하는 데 필요한 프로그래밍은 없습니다.

시각적 인터페이스는 Azure Machine Learning [작업 영역](concept-workspace.md) 을 사용 하 여 다음 작업을 수행 합니다.

+ [실험](#experiment) 실행의 아티팩트를 작업 영역으로 작성 합니다.
+ [데이터 집합](#dataset)에 액세스 합니다.
+ 작업 영역의 [계산 리소스](#compute) 를 사용 하 여 실험을 실행 합니다. 
+ [모델](concept-azure-machine-learning-architecture.md#models)을 등록 합니다.
+ 작업 영역의 계산 리소스에 웹 서비스로 모델을 [배포](#deployment) 합니다.

![시각적 인터페이스 개요](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>워크플로

시각적 인터페이스는 모델을 신속 하 게 빌드, 테스트 및 반복 하는 대화형 시각적 캔버스를 제공 합니다. 

+ [모듈](#module) 을 캔버스로 끌어서 놓습니다.
+ 모듈을 함께 연결 하 여 [실험](#experiment)을 구성 합니다.
+ Machine Learning 서비스 작업 영역의 계산 리소스를 사용 하 여 실험을 실행 합니다.
+ 실험을 편집 하 고 다시 실행 하 여 모델 디자인을 반복 합니다.
+ 준비가 되 면 **학습 실험** 을 **예측 실험**으로 변환 합니다.
+ 다른 사용자가 모델에 액세스할 수 있도록 예측 실험을 웹 서비스로 [배포](#deployment) 합니다.

## <a name="experiment"></a>Experiment

처음부터 실험을 만들거나 기존 샘플 실험을 템플릿으로 사용 합니다.  실험을 실행할 때마다 아티팩트는 작업 영역에 저장 됩니다.

실험은 모델을 생성 하기 위해 함께 연결 하는 데이터 집합 및 분석 모듈로 구성 됩니다. 특히 유효한 실험에는 다음 특성이 포함됩니다.

* 데이터 집합은 모듈에만 연결 될 수 있습니다.
* 모듈은 데이터 집합 또는 다른 모듈에 연결 될 수 있습니다.
* 모듈에 대 한 모든 입력 포트는 데이터 흐름에 대 한 일부 연결을 포함 해야 합니다.
* 각 모듈에 필요한 모든 매개 변수를 설정 해야 합니다.


시각적 인터페이스를 시작 하는 방법에 대 한 자세한 내용은 [자습서: 시각적 개체 인터페이스로 자동차 가격 예측](ui-tutorial-automobile-price-train-score.md)을 사용하여 시각적 개체 인터페이스(미리 보기)를 시도해 보세요.

## <a name="dataset"></a>데이터 집합

데이터 집합은 모델링 프로세스에서 사용할 시각적 인터페이스에 업로드 된 데이터입니다. 시험해 볼 수 있는 다양 한 샘플 데이터 집합이 포함 되어 있으며 필요에 따라 더 많은 데이터 집합을 업로드할 수 있습니다.

## <a name="module"></a>모듈

모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. 시각적 인터페이스에는 데이터 수신 기능부터 학습, 점수 매기기 및 유효성 검사 프로세스에 이르는 다양 한 모듈이 있습니다.

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택 하면 모듈의 매개 변수가 캔버스 오른쪽의 속성 창에 표시 됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다.

![모듈 속성](media/ui-concept-visual-interface/properties.png)

사용 가능한 기계 학습 알고리즘 라이브러리를 탐색 하는 데 도움이 필요한 경우 [알고리즘 & 모듈 참조 개요](../algorithm-module-reference/module-reference.md) 를 참조 하세요.

## <a name="compute"></a>계산 리소스

작업 영역의 계산 리소스를 사용 하 여 실험을 실행 하거나 배포 된 모델을 웹 서비스로 호스트할 수 있습니다. 지원되는 컴퓨팅 대상은 다음과 같습니다.


| 컴퓨팅 대상 | 학습 | 배포 |
| ---- |:----:|:----:|
| Azure Machine Learning 컴퓨팅 | ✓ | |
| Azure Kubernetes Service | | ✓ |

계산 대상은 Machine Learning [작업 영역](concept-workspace.md)에 연결 됩니다. 작업 영역에서 [Azure Portal](https://portal.azure.com) 또는 [작업 영역 방문 페이지 (미리 보기)](https://ml.azure.com)에서 계산 대상을 관리 합니다.

## <a name="deployment"></a>배포

예측 분석 모델이 준비 되 면 시각적 인터페이스에서 바로 웹 서비스로 배포 합니다.

웹 서비스는 응용 프로그램과 점수 매기기 모델 간의 인터페이스를 제공 합니다. 외부 응용 프로그램은 실제 시간에 점수 매기기 모델과 통신할 수 있습니다. 웹 서비스에 대 한 호출은 외부 응용 프로그램에 예측 결과를 반환 합니다. 웹 서비스를 호출하려면 웹 서비스를 배포할 때 만들어진 API 키를 전달합니다. 웹 서비스는 웹 프로그래밍 프로젝트에서 널리 사용 되는 인기 있는 아키텍처를 기반으로 합니다.

모델을 배포 하는 방법에 대 한 [자세한 내용은 자습서: 시각적 인터페이스](ui-tutorial-automobile-price-deploy.md)를 사용 하 여 machine learning 모델을 배포 합니다.

## <a name="next-steps"></a>다음 단계

* 자습서를 사용 하 여 [예측 분석 및 기계 학습의 기본 사항에 대해 알아봅니다. 그래픽 인터페이스를 사용하여 자동차 가격 예측](ui-tutorial-automobile-price-train-score.md)
* 샘플 중 하나를 사용 하 고 요구 사항에 맞게 수정 합니다.
    * [샘플 1-회귀: 예측 가격](ui-sample-regression-predict-automobile-price-basic.md)
    * [샘플 2-회귀: 가격 예측 및 알고리즘 비교](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [샘플 3-분류: 신용 위험 예측](ui-sample-classification-predict-credit-risk-basic.md)
    * [샘플 4-분류: 신용 위험 예측 (비용 구분)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [샘플 5-분류: 변동, 욕구 및 업 판매 예측](ui-sample-classification-predict-churn.md)
