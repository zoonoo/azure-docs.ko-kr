---
title: '디자이너: 책 리뷰 분류 예제'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너를 사용하여 Wikipedia SP 500 데이터 세트에서 회사 범주를 예측하는 다중 클래스 로지스틱 회귀 분류자를 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 4d22fd39eae5d5cf207d6d44819f0ce7ab2eceb5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963244"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 회사 범주를 예측하는 분류자 빌드

**디자이너(미리 보기) 샘플 7**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 샘플에서는 텍스트 분석 모듈을 사용하여 Azure Machine Learning 디자이너(미리 보기)에서 텍스트 분류 파이프라인을 빌드하는 방법을 보여 줍니다.

텍스트 분류의 목표는 텍스트 일부를 하나 이상의 미리 정의된 클래스 또는 범주에 할당하는 것입니다. 텍스트의 조각은 문서, 뉴스 기사, 검색 쿼리, 이메일, 트위터, 지원 티켓, 고객 피드백, 사용자 제품 검토 등일 수 있습니다. 텍스트 분류 애플리케이션은 신문 기사와 뉴스 제공 서비스 콘텐츠를 주제로 분류하고, 웹 페이지를 계층적 범주로 구성하며, 스팸 이메일을 필터링하고, 감정을 분석하며, 검색 쿼리에서 사용자 의도를 예측하고, 지원 티켓을 라우팅하며, 고객 피드백을 분석합니다. 

이 파이프라인은 **Wikipedia에서 파생된 Wikipedia SP 500 데이터 세트**를 사용하여 회사 범주를 예측하는 **다중 클래스 로지스틱 회귀 분류자**를 학습시킵니다.  

텍스트 데이터를 사용하는 기계 학습 모델의 기본 학습 단계는 다음과 같습니다.

1. 데이터 가져오기

1. 텍스트 데이터 전처리

1. 기능 엔지니어링

   텍스트 데이터에서 n-gram 기능을 추출하는 기능 해싱과 같은 기능 추출 모듈을 사용하여 텍스트 기능을 숫자 기능으로 변환합니다.

1. 모델 학습

1. 데이터 세트 점수 매기기

1. 모델 평가

다음은 우리가 작업할 파이프라인의 최종적으로 완성된 그래프입니다. 모든 모듈에 대한 근거를 제공하므로 비슷한 결정을 직접 내릴 수 있습니다.

[![파이프라인 그래프](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>데이터

이 파이프라인에서는 **Wikipedia SP 500** 데이터 세트를 사용합니다. 이 데이터 세트는 각 S&P 500개 회사의 문서를 기반으로 하는 Wikipedia(https://www.wikipedia.org/) )에서 파생된 것입니다. Azure Machine Learning 디자이너로 업로드하기 전에 데이터 세트는 다음과 같이 처리되었습니다.

- 각 특정 회사에 대한 텍스트 콘텐츠 추출
- 위치 형식 지정 제거
- 영숫자가 아닌 문자 제거
- 모든 텍스트를 소문자로 변환
- 알려진 회사 범주가 추가됨

일부 회사의 문서는 찾을 수 없으므로 레코드 수가 500개 미만입니다.

## <a name="pre-process-the-text-data"></a>텍스트 데이터 전처리

**텍스트 전처리** 모듈을 사용하여 문장 검색, 문장 토큰화 등을 포함하여 텍스트 데이터를 전처리합니다. 지원되는 모든 옵션은 [**텍스트 전처리**](algorithm-module-reference/preprocess-text.md) 문서에서 찾을 수 있습니다. 텍스트 데이터가 전처리되면 원본 데이터의 50%가 학습 데이터 세트에 포함되고 원본 데이터의 50%가 테스트 데이터 세트에 포함되도록 **데이터 분할** 모듈을 사용하여 입력 데이터를 임의로 분할합니다.

## <a name="feature-engineering"></a>기능 엔지니어링
이 샘플에서는 기능 엔지니어링을 수행하는 두 가지 방법을 사용합니다.

### <a name="feature-hashing"></a>기능 해싱
[**기능 해싱**](algorithm-module-reference/feature-hashing.md) 모듈을 사용하여 문서의 일반 텍스트를 정수로 변환하고 정수 값을 모델에 대한 입력 기능으로 사용했습니다. 

**기능 해싱** 모듈은 Vowpal Wabbit 라이브러리에서 제공하는 32비트 murmurhash v3 해싱 방법을 사용하여 가변 길이 텍스트 문서를 동일한 길이의 숫자 기능 벡터로 변환할 수 있습니다. 기능 해싱의 사용 목적은 차원성을 줄이기 위한 것입니다. 또한 기능 해싱은 문자열 비교 대신 해시 값 비교를 사용하므로 분류 시간에 기능 가중치를 더 빨리 조회할 수 있습니다.

샘플 파이프라인에서 해싱 비트 수를 14로 설정하고 n-gram 수를 2로 설정합니다. 이러한 설정을 사용하면 2^14개의 항목이 해시 테이블에 포함될 수 있습니다. 이 경우 각 해시 기능은 하나 이상의 n-gram 기능을 나타내고, 해당 값은 텍스트 인스턴스에서 해당 n-gram의 발생 빈도를 나타냅니다. 많은 문제의 경우 이 크기의 해시 테이블이 적절하지만, 경우에 따라 충돌을 방지하기 위해 더 많은 공간이 필요할 수 있습니다. 서로 다른 수의 비트를 사용하여 기계 학습 솔루션의 성능을 평가합니다. 

### <a name="extract-n-gram-feature-from-text"></a>텍스트에서 n-gram 기능 추출

n-gram은 지정된 텍스트 시퀀스에서 n개 항이 연속적으로 시퀀스된 것입니다. 크기 1의 n-gram은 unigram, 크기 2의 n-gram은 bigram, 크기 3의 n-그램은 trigram이라고 합니다. 더 큰 크기의 n-gram은 "4-gram", "5-gram" 등과 같이 n 값으로 언급되기도 합니다.

기능 엔지니어링의 또 다른 솔루션으로 [**텍스트에서 n-gram 기능 추출**](algorithm-module-reference/extract-n-gram-features-from-text.md) 모듈을 사용했습니다. 이 모듈은 먼저 n-gram 외에도 n-gram 세트를 추출하고, 텍스트에서 각 n-gram이 나타나는 문서의 수(DF)를 계산합니다. 이 샘플에서는 TF-IDF 메트릭을 사용하여 기능 값을 계산합니다. 그런 다음, 비정형 텍스트 데이터를 동일한 길이의 숫자 기능 벡터로 변환합니다. 여기서 각 기능은 텍스트 인스턴스에서 n-gram의 TF-IDF를 나타냅니다.

텍스트 데이터가 숫자 기능 벡터로 변환되면 **열 선택** 모듈을 사용하여 데이터 세트에서 텍스트 데이터를 제거합니다. 

## <a name="train-the-model"></a>모델 학습

알고리즘의 선택은 사용 사례의 요구 사항에 따라 달라지는 경우가 많습니다. 이 파이프라인의 목표는 회사의 범주를 예측하는 것이므로 다중 클래스 분류자 모델을 선택하는 것이 좋습니다. 기능 수가 많고 이러한 기능이 희박하다는 점을 고려하여 이 파이프라인에는 **다중 클래스 로지스틱 회귀** 모델을 사용합니다.

## <a name="test-evaluate-and-compare"></a>테스트, 평가 및 비교

 모델을 더 객관적으로 평가하기 위해 데이터 세트를 분할하고, 서로 다른 데이터 세트를 사용하여 모델을 학습시키고 테스트합니다.

모델이 학습되면 **모델 점수 매기기** 및 **모델 평가** 모듈을 사용하여 예측 결과를 생성하고 모델을 평가합니다. 그러나 **모델 점수 매기기** 모듈을 사용하기 전에 먼저 학습 중에 수행한 작업과 같이 기능 엔지니어링을 수행해야 합니다. 

**기능 해싱** 모듈의 경우 점수 매기기 흐름에 대한 기능 엔지니어를 학습 흐름으로 쉽게 수행할 수 있습니다. **기능 해싱** 모듈을 사용하여 입력 텍스트 데이터를 직접 처리합니다.

**텍스트에서 n-gram 기능 추출** 모듈의 경우 **결과 어휘 출력**을 학습 데이터 흐름에서 점수 매기기 데이터 흐름의 **입력 어휘**에 연결하고, **어휘 모드** 매개 변수를 **ReadOnly**로 설정합니다.
[![n-gram 점수 그래프](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

엔지니어링 단계가 완료되면 학습된 모델을 사용하여 **모델 점수 매기기**에서 테스트 데이터 세트에 대한 예측을 생성할 수 있습니다. 결과를 확인하려면 **모델 점수 매기기**의 출력 포트를 선택한 다음, **시각화**를 선택합니다.

그런 다음, 점수를 **모델 평가** 모듈에 전달하여 평가 메트릭을 생성합니다. **모델 평가**에는 두 개의 입력 포트가 있으므로 서로 다른 방법으로 생성된 점수가 매겨진 데이터 세트를 평가하고 비교할 수 있습니다. 이 샘플에서는 기능 해싱 방법 및 n-gram 방법을 사용하여 생성된 결과의 성능을 비교합니다.
결과를 확인하려면 **모델 평가**의 출력 포트를 선택한 다음, **시각화**를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 살펴봅니다.
- [샘플 1 - 회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2 - 회귀: 자동차 가격 예측 알고리즘에 대한 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3 - 기능 선택을 사용하여 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4 - 분류: 신용 위험 예측(비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5 - 분류: 이탈 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6 - 분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
