---
title: Azure Machine Learning Studio란? | Microsoft Docs
description: Azure ML Studio의 개요로, 알고리즘 및 모듈의 사용할 준비가 되어 있는 라이브러리에서 신속하게 모델을 빌드하기 위한 끌어서 놓기 도구입니다.
keywords: azure 기계 학습, azure ml, 기계 학습 스튜디오
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.openlocfilehash: 9911d56807a95a5f8199cecdff13ad06497fb33e
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836956"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio란?
Microsoft Azure Machine Learning Studio는 데이터에 대한 예측 분석 솔루션을 빌드, 테스트, 배포할 수 있는 공동 끌어서 놓기 도구입니다. Machine Learning Studio는 Excel과 같은 BI 도구 또는 사용자 지정 앱에서 쉽게 사용할 수 있는 웹 서비스로 모델을 게시합니다.

Machine Learning Studio는 데이터 과학, 예측 분석, 클라우드 리소스 및 데이터가 만나는 장소입니다.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Machine Learning Studio 대화형 작업 영역
예측 분석 모델을 개발하려면 일반적으로 원본 하나 이상의 데이터를 사용하고, 다양한 데이터 조작과 통계 함수를 통해 해당 데이터를 변환 및 분석하고, 결과 집합을 생성합니다. 이와 같은 모델을 개발하는 과정은 반복 프로세스이며, 다양한 함수와 해당 매개 변수를 수정할 때 학습된 효과적인 모델을 마련했다고 만족할 때까지 결과가 수렴됩니다.

**Azure Machine Learning Studio**에서는 예측 분석 모델을 간편하게 빌드, 테스트, 반복할 수 있는 대화형 시각적 작업 영역을 제공합니다. ***데이터 집합***과 분석 ***모듈***을 대화형 캔버스로 끌어서 놓고 함께 연결하여 ***실험***을 생성하고 Machine Learning 스튜디오에서 실행합니다. 모델 디자인을 반복하려면 실험을 편집하고 필요에 따라 복사본을 저장하고 실험을 다시 실행합니다. 준비가 되면 ***학습 실험***을 ***예측 실험***으로 변환한 다음 다른 사용자가 모델에 액세스할 수 있도록 ***웹 서비스***로 게시할 수 있습니다.

프로그래밍이 필요하지 않고 데이터 집합과 모듈을 시각적으로 연결하면 예측 분석 모델을 구성할 수 있습니다.

> [!TIP]
> Machine Learning Studio의 기능을 개략적으로 제공하는 다이어그램을 다운로드하고 인쇄하려면 [Azure Machine Learning Studio 기능 개요](studio-overview-diagram.md)를 참조하세요.
> 
> 

![Azure ML Studio 다이어그램: 실험을 만들고, 여러 원본에 대한 데이터 읽고, 점수 데이터를 쓰고, 모델을 작성합니다.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Machine Learning Studio 시작
[Machine Learning Studio](https://studio.azureml.net)를 처음 시작하면 **홈** 페이지가 표시됩니다. 여기에서 설명서, 동영상, 웹 세미나를 보고 다른 유용한 리소스를 찾을 수 있습니다.

왼쪽 위 메뉴를 클릭합니다. ![메뉴](./media/what-is-ml-studio/menu.png) 몇 가지 옵션이 표시됩니다.

### <a name="cortana-intelligence"></a>Cortana Intelligence
**Cortana Intelligence**를 클릭하면 [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite)의 홈페이지로 이동합니다. Cortana Intelligence Suite은 데이터를 지능형 작업으로 변환할 수 있는 완전히 관리되는 빅 데이터 및 고급 분석 제품군입니다. 고객 사례를 포함한 전체 설명서에 대한 Suite 홈 페이지를 참조하세요.

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
여기에 시작한 페이지인 **홈**과 **Studio**, 두 가지 옵션이 있습니다.

**Studio**를 클릭하여 **Azure Machine Learning Studio**로 이동합니다. 먼저 Microsoft 계정이나 회사 또는 학교 계정을 사용하여 로그인하라는 메시지가 표시됩니다. 로그인하면 왼쪽에 다음과 같은 탭이 표시됩니다.

* **프로젝트** - 단일 프로젝트를 나타내는 실험, 데이터 집합, notebooks 및 기타 리소스의 컬렉션입니다.
* **실험** - 만들고 실행하거나 초안으로 저장한 실험입니다.
* **웹 서비스** - 실험에서 배포한 웹 서비스입니다.
* **노트북** - 사용자가 만든 Jupyter 노트북입니다.
* **데이터 집합** - 스튜디오로 업로드한 데이터 집합입니다.
* **학습된 모델** - 실험에서 학습하고 스튜디오에 저장한 모델입니다.
* **설정** - 계정과 리소스를 구성하는 데 사용할 수 있는 설정 모음입니다.

### <a name="gallery"></a>갤러리
**갤러리** 탭을 클릭하면 **[Azure AI Gallery](http://gallery.cortanaintelligence.com/)** 로 이동합니다. 갤러리는 데이터 과학자 및 개발자 커뮤니티가 Cortana Intelligence Suite의 구성 요소를 사용하여 만든 솔루션을 공유하는 곳입니다.

갤러리에 대한 자세한 내용은 [Azure AI Gallery의 솔루션 공유 및 검색](gallery-how-to-use-contribute-publish.md)을 참조하세요.

## <a name="components-of-an-experiment"></a>실험 구성 요소
실험은 함께 연결하여 예측 분석 모델을 구성하는 분석 모듈에 데이터를 제공하는 데이터 집합으로 구성됩니다. 특히 유효한 실험에는 다음 특성이 포함됩니다.

* 실험에는 하나 이상의 데이터 집합과 하나의 모듈이 있습니다.
* 데이터 집합은 모듈에만 연결될 수 있습니다.
* 모듈은 데이터 집합 또는 다른 모듈에 연결될 수 있습니다.
* 모듈에 대한 모든 입력 포트에는 데이터 흐름에 대한 연결이 포함되어야 합니다.
* 각 모듈에 대한 모든 필수 매개 변수를 설정해야 합니다.

실험을 처음부터 만들거나 기존 샘플 실험을 템플릿으로 사용할 수 있습니다. 자세한 내용은 [샘플 실험을 복사하여 새로운 기계 학습 실험 만들기](sample-experiments.md)를 참조하세요.

간단한 실험을 만드는 예는 [Azure Machine Learning Studio에서 간단한 실험 만들기](create-experiment.md)를 참조하세요.

예측 분석 솔루션을 만드는 자세한 연습 과정은 [Azure Machine Learning을 사용한 예측 솔루션 개발](walkthrough-develop-predictive-solution.md)을 참조하세요.

### <a name="datasets"></a>데이터 집합
데이터 집합은 모델링 프로세스에서 사용할 수 있도록 Machine Learning Studio에 업로드된 데이터입니다. Machine Learning Studio에는 실험에 사용할 다양한 샘플 데이터 집합이 포함되고, 필요할 때 추가 데이터 집합을 업로드할 수 있습니다. 포함된 데이터 집합의 몇 가지 예제는 다음과 같습니다.

* **다양한 자동차에 대한 MPG 데이터** - 실린더 수, 마력 등으로 식별되는 자동차에 대한 MPG 값
* **유방암 데이터** - 유방암 진단 데이터
* **산불 데이터** - 포르투갈 북동부에서 발생한 산불 규모

실험을 빌드할 때 캔버스의 왼쪽에서 사용할 수 있는 데이터 집합의 목록에서 선택할 수 있습니다.

Machine Learning Studio에 포함된 샘플 데이터 집합의 목록은 [Azure Machine Learning Studio에서 예제 데이터 집합 사용](use-sample-datasets.md)을 참조하세요.

### <a name="modules"></a>모듈
모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. Machine Learning Studio에는 데이터 가져오기 함수부터 학습, 점수 매기기 및 유효성 검사 프로세스에 이르는 다양한 모듈이 있습니다. 포함된 모듈의 몇 가지 예제는 다음과 같습니다.

* [ARFF로 변환][convert-to-arff] - .NET 직렬화된 데이터 집합을 ARFF(Attribute-Relation File Format) 형식으로 변환합니다.
* [기본 통계 계산][elementary-statistics] - 평균, 표준 편차 등의 기본 통계를 계산합니다.
* [선형 회귀][linear-regression] - 온라인 기울기 하강 기반 선형 회귀 모델을 만듭니다.
* [모델 점수 매기기][score-model] - 학습된 분류 또는 회귀 모델의 점수를 매깁니다.

실험을 빌드할 때 캔버스의 왼쪽에서 사용할 수 있는 모듈의 목록에서 선택할 수 있습니다.  

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택할 때 모듈 매개 변수가 캔버스 오른쪽의 **속성** 창에 표시됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다.

사용 가능한 기계 학습 알고리즘의 대규모 라이브러리를 탐색하는 방법에 대한 도움말은 [Microsoft Azure Machine Learning을 위한 알고리즘 선택 방법](algorithm-choice.md)을 참조하세요.

## <a name="deploying-a-predictive-analytics-web-service"></a>예측 분석 웹 서비스 배포
예측 분석 모델이 준비되면 Machine Learning Studio에서 곧바로 해당 모델을 웹 서비스로 배포할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg



## <a name="key-machine-learning-terms-and-concepts"></a>주요 기계 학습 용어 및 개념
기계 학습 용어는 혼동될 수 있습니다. 주요 용어의 정의는 다음과 같습니다. 이 밖에도 정의하고 싶은 용어가 있다면 아래 Comments(사용자 의견)를 이용해 주세요.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>데이터 탐색, 설명이 포함된 분석 및 예측 분석

**데이터 탐색**은 집중 분석을 위한 특성을 찾기 위해 규모가 크고 구조화되지 않은 데이터 집합에 대한 정보를 수집하는 프로세스입니다.

**데이터 마이닝**은 자동화된 데이터 탐색을 나타냅니다.

**설명이 포함된 분석** 은 발생한 현상을 요약하기 위해 데이터 집합을 분석하는 프로세스입니다. 대부분의 비즈니스 분석(예: 판매 보고서, 웹 메트릭 및 소셜 네트워크 분석)은 설명을 포함합니다.

**예측 분석** 은 미래 결과를 예측하기 위해 기록 데이터 또는 현재 데이터에서 모델을 작성하는 프로세스입니다.

### <a name="supervised-and-unsupervised-learning"></a>감독 학습 및 자율 학습
 **감독 학습** 알고리즘은 레이블이 지정된 데이터, 즉 원하는 응답 예제로 구성된 데이터로 학습됩니다. 예를 들어 사기성 신용 카드 사용을 식별하는 모델은 알려진 사기와 유효한 요금의 레이블된 데이터 요소가 있는 데이터 집합에서 학습됩니다. 대부분의 기계 학습은 감독 학습입니다.

 **자율 학습** 이 사용되며 그 목표는 데이터에서 관계를 찾아내는 것입니다. 예를 들어 비슷한 구매 습관을 갖는 고객 인구 통계 그룹을 찾으려고 할 수 있습니다.

### <a name="model-training-and-evaluation"></a>모델 학습 및 평가
기계 학습 모델은 대답하려는 질문이나 예측하려는 결과를 추상화한 것입니다. 모델은 기존 데이터에서 학습되고 평가됩니다.

#### <a name="training-data"></a>학습 데이터
데이터에서 모델을 학습하는 경우 가장 정확한 답을 얻기 위해 알려진 데이터 집합을 사용하고 데이터 특성을 기반으로 한 모델에 맞게 조정합니다. Azure Machine Learning에서는 학습 데이터 및 기능 모듈(예: 점수 매기기 모듈)을 처리하는 알고리즘 모듈에서 모델이 작성됩니다.

감독 학습에서 부정 행위 감지 모델을 학습하는 경우 사기 또는 유효한 것으로 레이블이 지정된 트랜잭션 집합을 사용하게 됩니다. 데이터 집합을 임의로 분할한 후 일부를 사용하여 모델을 학습하고, 일부를 사용하여 모델을 테스트하거나 평가합니다.

#### <a name="evaluation-data"></a>평가 데이터
학습된 모델이 있는 경우 나머지 테스트 데이터를 사용하여 모델을 평가합니다. 결과를 이미 알고 있는 데이터를 사용하여 모델이 정확하게 예측하는지 여부를 알 수 있습니다.

## <a name="other-common-machine-learning-terms"></a>기타 일반적인 기계 학습 용어
* **알고리즘**: 데이터 처리, 계산 및 자동 추론을 통해 문제를 해결하는 데 사용되는 완전한 규칙 집합입니다.
* **이상 감지**: 비정상적인 이벤트 또는 값에 플래그를 지정하고 문제를 검색하도록 해주는 모델입니다. 예를 들어, 신용 카드 부정 행위 감지의 경우 비정상적인 구매를 찾습니다.
* **범주 데이터**: 범주별로 구성되고 그룹으로 나눌 수 있는 데이터입니다. 예를 들어 자동차에 대한 범주 데이터 집합은 연식, 제조업체, 모델 및 가격을 지정할 수 있습니다.
* **분류**: 범주 그룹화가 이미 알려진 데이터 집합을 기반으로 데이터 요소를 범주로 구성하는 모델입니다.
* **기능 엔지니어링**: 데이터 집합을 향상시키고 결과를 개선하기 위해 데이터 집합과 관련된 기능을 추출하거나 선택하는 프로세스입니다. 예를 들어 항공 요금 데이터를 요일 및 휴일에 따라 늘릴 수 있습니다. [Azure Machine Learning의 기능 선택 및 엔지니어링](../team-data-science-process/create-features.md)을 참조하세요.
* **모듈**: 소규모 데이터 집합을 입력 및 편집할 수 있도록 하는 데이터 입력 모듈과 같은 Machine Learning Studio 모델의 기능 부분입니다. 알고리즘 또한 Machine Learning Studio의 모듈 유형에 해당합니다.
* **모델**: 감독 학습의 경우, 모델은 학습 데이터, 알고리즘 모듈 및 기능 모듈(예: 모델 점수 매기기 모듈)로 구성된 기계 학습 실험 제품입니다.
* **숫자 데이터**: 측정(연속 데이터) 또는 개수(불연속 데이터)의 의미를 갖는 데이터입니다. *양적 데이터*라고도 합니다.
* **파티션**: 데이터를 샘플로 구분하는 방법입니다. 자세한 내용은 [파티션 및 샘플](https://msdn.microsoft.com/library/azure/dn905960.aspx) 을 참조하세요.
* **예측**: 예측은 기계 학습 모델에서 값을 예측하는 것입니다. "예측된 점수" 용어도 볼 수 있습니다. 그러나 예측된 점수는 모델의 최종 출력이 아닙니다. 모델에 대한 평가 점수는 다음과 같습니다.
* **회귀**: 연식 및 제조업체에 따라 자동차 가격을 예측하는 것처럼 독립 변수를 기반으로 값을 예측하는 모델입니다.
* **점수**: Machine Learning Studio의 [모델 점수 매기기 모듈](https://msdn.microsoft.com/library/azure/dn905995.aspx) 을 사용하여 학습된 분류 또는 회귀 모델에서 생성하는 예측된 값입니다. 또한 분류 모델은 예측된 값의 확률 점수도 반환합니다. 모델에서 점수를 생성한 후 [모델 평가 모듈](https://msdn.microsoft.com/library/azure/dn905915.aspx)을 사용하여 모델의 정확도를 평가할 수 있습니다.
* **샘플**: 전체를 대표하는 데이터 집합의 부분입니다. 샘플을 임의로 선택하거나 데이터 집합의 특정 기능에 따라 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[단계별 자습서](create-experiment.md)를 사용하거나 [샘플을 기반으로 작업](sample-experiments.md)하여 예측 분석 및 기계 학습의 기본 사항을 알아볼 수 있습니다.  


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
