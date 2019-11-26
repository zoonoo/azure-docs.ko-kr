---
title: '디자이너: 책 리뷰 분류 예'
titleSuffix: Azure Machine Learning
description: 다중 클래스 로지스틱 회귀 분류자를 빌드하여 Azure Machine Learning designer를 사용 하 여 위키백과 SP 500 데이터 집합으로 회사 범주를 예측 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 16253abce2940690a80f84aa5b68521c09212bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213754"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Azure Machine Learning designer를 사용 하 여 회사 범주를 예측 하는 분류자를 빌드합니다.

**Designer (미리 보기) 샘플 7**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

이 샘플에서는 텍스트 분석 모듈을 사용 하 여 Azure Machine Learning designer (미리 보기)에서 텍스트 분류 파이프라인을 빌드하는 방법을 보여 줍니다.

텍스트 분류의 목표는 하나 이상의 미리 정의 된 클래스 또는 범주에 일부 텍스트를 할당 하는 것입니다. 텍스트의 조각은 문서, 뉴스 기사, 검색 쿼리, 전자 메일, 트 윗, 지원 티켓, 고객 피드백, 사용자 제품 검토 등 일 수 있습니다. 텍스트 분류의 응용 프로그램에는 신문 문서와 뉴스 관련 콘텐츠를 토픽으로 분류 하 고, 웹 페이지를 계층적 범주로 구성 하 고, 스팸 메일 필터링, 감정 분석, 검색 쿼리에서 사용자 의도 예측, 라우팅 등을 포함 합니다. 티켓을 지원 하 고 고객 의견을 분석 합니다. 

이 파이프라인은 **다중 클래스 로지스틱 회귀 분류자** 를 학습 하 여 **위키백과에서 파생 된 위키백과 SP 500 데이터 집합**으로 회사 범주를 예측 합니다.  

텍스트 데이터를 사용 하는 학습 기계 학습 모델의 기본 단계는 다음과 같습니다.

1. 데이터 가져오기

1. 텍스트 데이터를 사전 처리 합니다.

1. 기능 엔지니어링

   텍스트 기능을 기능 해싱, 텍스트 데이터에서 n-문법 풀기 기능과 같은 기능 추출 모듈을 사용 하 여 숫자 기능으로 변환 합니다.

1. 모델 학습

1. 점수 데이터 집합

1. 모델 평가

다음은 작업 중인 파이프라인의 최종 완료 된 그래프입니다. 모든 모듈에 대 한 근거를 제공 하므로 사용자에 게 비슷한 결정을 내릴 수 있습니다.

[파이프라인의 ![그래프](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Data

이 파이프라인에서는 **위키백과 SP 500** 데이터 집합을 사용 합니다. 데이터 집합은 위키백과 (https://www.wikipedia.org/) & P 500 회사의 문서를 기반으로 하는 위키백과에서 파생 됩니다. Azure Machine Learning designer에 업로드 하기 전에 데이터 집합이 다음과 같이 처리 되었습니다.

- 각 특정 회사에 대한 텍스트 콘텐츠 추출
- 위치 형식 지정 제거
- 영숫자가 아닌 문자 제거
- 모든 텍스트를 소문자로 변환
- 알려진 회사 범주가 추가됨

일부 회사의 문서를 찾을 수 없으므로 레코드 수가 500 미만입니다.

## <a name="pre-process-the-text-data"></a>텍스트 데이터를 사전 처리 합니다.

문장을 검색 하 고 문장을 토큰화 하는 등 텍스트 데이터를 전처리 하려면 **전처리 텍스트** 모듈을 사용 합니다. [**전처리 텍스트**](../algorithm-module-reference/preprocess-text.md) 문서에서 지원 되는 모든 옵션을 찾을 수 있습니다. Tex 데이터를 전처리 하 고 나 서 데이터 분할 모듈을 사용 하 여 학습 데이터 집합에 원래 데이터의 50%가 포함 되 고 테스트 데이터 집합에 원래 데이터의 50%가 포함 되도록 **데이터 분할** 모듈을 사용 하 여 입력 데이터를 임의로 나눕니다.

## <a name="feature-engineering"></a>기능 엔지니어링
이 샘플에서는 기능 엔지니어링을 수행 하는 두 가지 방법을 사용 합니다.

### <a name="feature-hashing"></a>기능 해싱
[**기능 해싱**](../algorithm-module-reference/feature-hashing.md) 모듈을 사용 하 여 아티클의 일반 텍스트를 정수로 변환 하 고 정수 값을 모델에 입력 기능으로 사용 했습니다. 

**기능 해싱** 모듈은 Vowpal Wabbit 라이브러리에서 제공 하는 32 비트 murmurhash v3 해시 메서드를 사용 하 여 가변 길이 텍스트 문서를 같은 길이의 숫자 기능 벡터로 변환 하는 데 사용할 수 있습니다. 기능 해시를 사용 하는 목적은 차원 감소입니다. 또한 기능 해시는 문자열 비교 대신 해시 값 비교를 사용 하기 때문에 분류 시간에 기능 가중치를 빠르게 조회할 수 있도록 합니다.

샘플 파이프라인에서 해시 비트 수를 14로 설정 하 고 n-그램의 수를 2로 설정 합니다. 이러한 설정을 사용 하는 경우 해시 테이블에는 2 ^ 14 개의 항목이 포함 될 수 있습니다 .이 항목에서는 각 해싱 기능이 하나 이상의 n-문법 기능을 나타내고 해당 값은 텍스트 인스턴스에서 해당 n-영문법의 발생 빈도를 나타냅니다. 많은 문제에 대해이 크기의 해시 테이블은 적절 한 크기 이지만 충돌을 방지 하기 위해 더 많은 공간이 필요할 수 있습니다. 다른 비트 수를 사용 하 여 machine learning 솔루션의 성능을 평가 합니다. 

### <a name="extract-n-gram-feature-from-text"></a>텍스트에서 N-영문법 기능 추출

N-영문법은 지정 된 텍스트 시퀀스에서 n 항의 연속 시퀀스입니다. 크기 1의 n-영문법은 전체 문법으로 지칭 됩니다. 크기 2의 n-영문법은 bigram입니다. 크기 3의 n-영문법은 trigram입니다. 더 큰 크기의 n 그램은 n의 값 (예를 들어, "4-4-4-4-", "5-5-5-5-4-4-")이 라고도 합니다.

기능 엔지니어링을 위한 다른 솔루션인 [**텍스트 모듈에서 N-영문법 기능을 추출**](../algorithm-module-reference/extract-n-gram-features-from-text.md)했습니다. 이 모듈은 먼저 n 그램의 집합을 추출 하 고, 각 n-영문법을 텍스트에 표시 되는 문서 수를 계산 합니다 (DF). 이 샘플에서 TF IDF 메트릭은 기능 값을 계산 하는 데 사용 됩니다. 그런 다음 구조화 되지 않은 텍스트 데이터를 같은 길이의 숫자 기능 벡터로 변환 합니다. 여기서 각 기능은 텍스트 인스턴스의 n-문법의 TF-IDF를 나타냅니다.

텍스트 데이터를 숫자 기능 벡터로 변환한 후 **열 선택** 모듈을 사용 하 여 데이터 집합에서 텍스트 데이터를 제거 합니다. 

## <a name="train-the-model"></a>모델 학습

알고리즘 선택은 종종 사용 사례의 요구 사항에 따라 달라 집니다. 이 파이프라인의 목표는 회사 범주를 예측 하는 것 이므로 다중 클래스 분류자 모델을 선택 하는 것이 좋습니다. 기능의 수가 크고 이러한 기능이 스파스 임을 고려 하 여이 파이프라인에 **다중 클래스 로지스틱 회귀** 모델을 사용 합니다.

## <a name="test-evaluate-and-compare"></a>테스트, 평가 및 비교

 데이터 집합을 분할 하 고 다양 한 데이터 집합을 사용 하 여 모델을 학습 하 고 테스트 하 여 모델을 더 목표로 계산 합니다.

모델을 학습 한 후 **모델 점수 매기기** 및 **모델 평가** 모듈을 사용 하 여 예측 결과를 생성 하 고 모델을 평가 합니다. 그러나 **모델 점수 매기기** 모듈을 사용 하기 전에 학습 중에 수행 된 작업으로 기능 엔지니어링을 수행 해야 합니다. 

**기능 해싱** 모듈의 경우 학습 흐름으로 점수 매기기 흐름에서 기능 엔지니어를 쉽게 수행할 수 있습니다. **기능 해싱** 모듈을 직접 사용 하 여 입력 텍스트 데이터를 처리 합니다.

**텍스트 모듈의 추출 N-영문법 기능** 에 대해 학습 데이터 흐름의 **Result 어휘 출력** 을 점수 흐름 데이터 흐름의 **입력 어휘** 에 연결 하 고 **어휘 모드** 매개 변수를 **ReadOnly**로 설정 합니다.
[n-영문법 점수의 ![그래프](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

엔지니어링 단계를 완료 한 후 **모델 점수 매기기** 를 사용 하 여 학습 된 모델을 통해 테스트 데이터 집합에 대 한 예측을 생성할 수 있습니다. 결과를 확인 하려면 **모델 점수 매기기** 의 출력 포트를 선택한 다음 **시각화**를 선택 합니다.

그런 다음 점수를 **모델 평가** 모듈에 전달 하 여 평가 메트릭을 생성 합니다. **모델 평가** 에는 다른 방법으로 생성 된 점수가 매겨진 데이터 집합을 평가 하 고 비교할 수 있도록 두 개의 입력 포트가 있습니다. 이 샘플에서는 기능 해싱 메서드 및 n-영문법 메서드를 사용 하 여 생성 된 결과의 성능을 비교 합니다.
결과를 확인 하려면 **평가 모델** 의 출력 포트를 선택한 다음 **시각화**를 선택 합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>다음 단계

디자이너에 사용할 수 있는 다른 샘플을 탐색 합니다.
- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3-기능 선택이 포함 된 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6-분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
