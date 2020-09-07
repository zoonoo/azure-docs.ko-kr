---
title: Machine Learning Studio(클래식)로 수행할 수 있는 작업 - Azure
description: Machine Learning Studio(클래식)는 즉시 사용 가능한 알고리즘 및 모듈의 라이브러리에서 모델을 신속하게 빌드하기 위한 끌어서 놓기 도구입니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 1677bb5e7d94b92376bc42308b7bbcb0a8c24f97
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89039310"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Machine Learning Studio(클래식)로 수행할 수 있는 작업은?

**적용 대상:**  ![예](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식)   ![아니요](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio(클래식)는 기계 학습 모델을 빌드, 테스트 및 배포하는 데 사용할 수 있는 끌어서 놓기 도구입니다. Studio(클래식)는 Excel과 같은 BI 도구 또는 사용자 지정 앱에서 쉽게 사용할 수 있는 웹 서비스로 모델을 게시합니다.


## <a name="studio-classic--interactive-workspace"></a>Studio(클래식) 대화형 작업 영역

예측 분석 모델을 개발하려면 일반적으로 하나 이상의 소스에서 얻은 데이터를 사용하고, 다양한 데이터 조작과 통계 함수를 통해 해당 데이터를 변환 및 분석하고, 결과 세트를 생성합니다. 이와 같은 모델을 개발하는 과정은 반복 프로세스이며, 다양한 함수와 해당 매개 변수를 수정할 때 학습된 효과적인 모델을 마련했다고 만족할 때까지 결과가 수렴됩니다.

Machine Learning Studio(클래식)에서는 예측 분석 모델을 간편하게 빌드, 테스트, 반복할 수 있는 대화형 시각적 작업 영역을 제공합니다. ***데이터 세트***와 분석 ***모듈***을 대화형 캔버스로 끌어서 놓고 함께 연결하여 ***실험***을 생성하고 Machine Learning Studio(클래식)에서 실행합니다. 모델 디자인을 반복하려면 실험을 편집하고 필요에 따라 복사본을 저장하고 실험을 다시 실행합니다. 준비가 되면 ***학습 실험***을 ***예측 실험***으로 변환한 다음 다른 사용자가 모델에 액세스할 수 있도록 ***웹 서비스***로 게시할 수 있습니다.

프로그래밍이 필요하지 않으며, 데이터 세트와 모듈을 시각적으로 연결하여 예측 분석 모델을 구성할 수 있습니다.

![Machine Learning Studio(클래식) 다이어그램: 실험을 만들고, 여러 원본에 대한 데이터 읽고, 점수 데이터를 쓰고, 모델을 작성합니다.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>ML Studio(클래식) 개요 다이어그램 다운로드
**Microsoft ML Studio(클래식) 기능 개요** 다이어그램을 다운로드하여 Machine Learning Studio(클래식)의 기능을 개략적으로 봅니다. 옆에 두고 보려면, tabloid 크기(11 x 17인치)로 다이어그램을 인쇄하면 됩니다.

**여기서 다이어그램 다운로드: [Microsoft Machine Learning Studio(클래식) 기능 개요](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Microsoft Machine Learning Studio(클래식) 기능 개요](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Studio(클래식) 실험의 구성 요소
실험은 함께 연결하여 예측 분석 모델을 구성하는 분석 모듈에 데이터를 제공하는 데이터 세트로 구성됩니다. 특히 유효한 실험에는 다음 특성이 포함됩니다.

* 실험에는 하나 이상의 데이터 세트와 하나의 모듈이 있습니다.
* 데이터 세트는 모듈에만 연결될 수 있습니다.
* 모듈은 데이터 세트 또는 다른 모듈에 연결될 수 있습니다.
* 모듈에 대한 모든 입력 포트에는 데이터 흐름에 대한 연결이 포함되어야 합니다.
* 각 모듈에 대한 모든 필수 매개 변수를 설정해야 합니다.

실험을 처음부터 만들거나 기존 샘플 실험을 템플릿으로 사용할 수 있습니다. 자세한 내용은 [샘플 실험을 복사하여 새로운 기계 학습 실험 만들기](sample-experiments.md)를 참조하세요.

실험을 만드는 예는 [Machine Learning Studio(클래식)에서 실험 만들기](create-experiment.md)를 참조하세요.

예측 분석 솔루션을 만드는 자세한 연습 과정은 [Machine Learning Studio(클래식)를 사용한 예측 솔루션 개발](tutorial-part1-credit-risk.md)을 참조하세요.

### <a name="datasets"></a>데이터 세트
데이터 세트는 모델링 프로세스에서 사용할 수 있도록 Machine Learning Studio(클래식)에 업로드된 데이터입니다. Machine Learning Studio(클래식)에는 실험에 사용할 다양한 샘플 데이터 세트가 포함되고, 필요할 때 추가 데이터 세트를 업로드할 수 있습니다. 포함된 데이터 세트의 몇 가지 예제는 다음과 같습니다.

* **다양한 자동차에 대한 MPG 데이터** - 실린더 수, 마력 등으로 식별되는 자동차에 대한 MPG 값
* **유방암 데이터** - 유방암 진단 데이터
* **산불 데이터** - 포르투갈 북동부에서 발생한 산불 규모

실험을 빌드할 때 캔버스의 왼쪽에 제공되는 데이터 세트 목록에서 선택할 수 있습니다.

Machine Learning Studio(클래식)에 포함된 샘플 데이터 세트의 목록은 [Machine Learning Studio(클래식)에서 샘플 데이터 세트 사용](use-sample-datasets.md)을 참조하세요.

### <a name="modules"></a>모듈
모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. Machine Learning Studio(클래식)에는 데이터 수신 함수부터 학습, 점수 매기기 및 유효성 검사 프로세스에 이르는 다양한 모듈이 있습니다. 포함된 모듈의 몇 가지 예제는 다음과 같습니다.

* [ARFF로 변환][convert-to-arff] - .NET 직렬화된 데이터 세트를 ARFF(Attribute-Relation File Format) 형식으로 변환합니다.
* [기본 통계 컴퓨팅][elementary-statistics] - 평균, 표준 편차 등의 기본 통계를 컴퓨팅합니다.
* [선형 회귀][linear-regression] - 온라인 기울기 하강 기반 선형 회귀 모델을 만듭니다.
* [모델 점수 매기기][score-model] - 학습된 분류 또는 회귀 모델의 점수를 매깁니다.

실험을 빌드할 때 캔버스의 왼쪽에서 사용할 수 있는 모듈의 목록에서 선택할 수 있습니다.

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택할 때 모듈 매개 변수가 캔버스 오른쪽의 **속성** 창에 표시됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다.

사용 가능한 기계 학습 알고리즘의 대규모 라이브러리를 탐색하는 방법에 대한 도움말은 [Microsoft Machine Learning Studio(클래식)를 위한 알고리즘 선택 방법](algorithm-choice.md)을 참조하세요.

## <a name="deploying-a-predictive-analytics-web-service"></a>예측 분석 웹 서비스 배포
예측 분석 모델이 준비되면 Machine Learning Studio(클래식)에서 곧바로 해당 모델을 웹 서비스로 배포할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](deploy-a-machine-learning-web-service.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[단계별 빠른 시작](create-experiment.md)을 사용하거나 [샘플 기반 빌드](sample-experiments.md)를 통해 예측 분석 및 기계 학습에 대한 기본 사항을 알아볼 수 있습니다.

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
