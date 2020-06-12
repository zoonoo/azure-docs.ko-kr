---
title: Word를 벡터로 변환
titleSuffix: Azure Machine Learning
description: 제공된 세 개의 Word2Vec 모델을 사용하여 텍스트 모음에서 어휘 및 해당하는 단어 임베딩을 추출하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853655"
---
# <a name="convert-word-to-vector"></a>Word를 벡터로 변환

이 문서에서는 Azure Machine Learning 디자이너(미리 보기)에서 **단어를 벡터로 변환** 모듈을 사용하여 입력으로 지정한 텍스트 모음에서 다양한 Word2Vec 모델(Word2Vec, FastText, Glove 미리 학습된 모델)을 적용하고 단어 임베딩을 통해 어휘를 생성하는 방법을 설명합니다.

이 모듈은 Gensim 라이브러리를 사용합니다. Gensim에 대한 자세한 내용은 알고리즘의 자습서 및 설명이 포함된 [공식 웹 사이트](https://radimrehurek.com/gensim/apiref.html)를 참조하세요.

### <a name="more-about-convert-word-to-vector"></a>단어를 벡터로 변환에 대한 자세한 정보

일반적으로 단어를 벡터 또는 단어 벡터화로 변환하는 것은 언어 모델 또는 기술을 사용하여 단어를 벡터 공간에 매핑하고(즉, 실수의 벡터를 기준으로 각 단어를 나타냄), 비슷한 의미를 가진 단어가 유사한 표현을 가질 수 있도록 하는 자연어 처리 프로세스입니다.

단어 임베딩은 텍스트 분류, 감정 분석 등과 같은 NLP 다운스트림 작업의 초기 입력으로 사용할 수 있습니다.

다양한 단어 임베딩 기술 중에서 이 모듈에서는 널리 사용되는 세 가지 방법(두 개의 온라인 학습 모델인 Word2Vec 및 FastText와 미리 학습된 모델인 glove-wiki-gigaword-100)을 구현했습니다. 온라인 학습 모델은 입력 데이터에서 학습되는 반면, 미리 학습된 모델은 더 큰 텍스트 모음(예: Wikipedia, Google News)에서 오프라인으로 학습되고 일반적으로 약 1000억 단어를 포함합니다. 또한 단어 벡터화 동안 단어 임베딩이 일정하게 유지됩니다. 미리 학습된 모델은 학습 시간 단축, 인코딩된 단어 벡터 개선 및 향상된 전체 성능과 같은 이점을 제공합니다.

+ Word2Vec은 단순 신경망을 사용하여 단어 임베딩을 배우는 가장 인기 있는 기술 중 하나입니다. 이론은 이 문서에서 논의되며 PDF 다운로드로 사용할 수 있습니다. [Efficient Estimation of Word Representations in Vector Space(벡터 공간에서 워드 표현의 효과적인 추정), Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). 이 모듈의 구현은 [Word2Vec에 대한 gensim 라이브러리](https://radimrehurek.com/gensim/models/word2vec.html)를 기반으로 합니다.

+ FastText 이론은 이 문서에서 설명하며 PDF 다운로드로 제공합니다. [Enriching Word Vectors with Subword Information(하위 단어 정보를 사용한 워드 벡터 보강), Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). 이 모듈의 구현은 [FastText에 대한 gensim 라이브러리](https://radimrehurek.com/gensim/models/fasttext.html)를 기반으로 합니다.

+ Glove 미리 학습된 모델인 glove-wiki-gigaword-100은 Wikipedia 텍스트 모음을 기반으로 미리 학습된 벡터 컬렉션으로, 5.6B 토큰과 400K 미포함 어휘를 포함하고 있습니다. pdf로 제공됩니다. [GloVe: Global Vectors for Word Representation(단어 표현을 위한 글로벌 벡터)](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>단어에서 벡터로의 변환을 구성하는 방법

이 모듈에는 텍스트 열을 포함하는 데이터 세트가 필요합니다. 전처리된 텍스트는 더 좋습니다.

1. **단어를 벡터로 변환** 모듈을 파이프라인에 추가합니다.

2. 모듈에 대한 입력으로 하나 이상의 텍스트 열을 포함하는 데이터 세트를 제공합니다.

3. **대상 열**에 처리할 텍스트가 포함된 열을 하나만 선택합니다.

    일반적으로 이 모듈은 텍스트에서 어휘를 만들기 때문에 다른 열의 콘텐츠가 서로 다릅니다. 다른 어휘 콘텐츠로 이어질 수 있으므로 모듈은 하나의 대상 열만 허용합니다.

4. **Word2Vec 전략**의 경우 `GloVe pretrained English Model`, `Gensim Word2Vec` 및 `Gensim FastText`에서 선택합니다.

5. **Word2Vec 전략**은 `Gensim Word2Vec` 또는 `Gensim FastText`입니다.

    + **Word2Vec 학습 알고리즘**. `Skip_gram` 및 `CBOW`에서 선택합니다. 차이점은 원본 [문서](https://arxiv.org/pdf/1301.3781.pdf)에 소개되었습니다.

        기본 메서드는 `Skip_gram`입니다.

    + **단어 임베딩의 길이**. 단어 벡터의 차원을 지정합니다. gensim에서 `size` 매개 변수에 해당합니다.

        기본 임베딩 크기는 100입니다.

    + **컨텍스트 창 크기**. 예측되는 단어와 현재 단어 사이의 최대 거리를 지정합니다. gensim에서 `window` 매개 변수에 해당합니다.

        기본 창 크기는 5입니다.

    + **Epoch의 수**. 모음에 대한 Epoch(반복) 수를 지정합니다. gensim에서 `iter` 매개 변수에 해당합니다.

        기본 Epoch 수는 5입니다.

6. **최대 어휘 크기**의 경우 생성된 어휘에서 최대 단어 수를 지정합니다.

    이보다 더 고유한 단어가 더 있으면 자주 발생하지 않는 단어를 정리합니다.

    기본 어휘 크기는 10000입니다.

7. **최소 단어 수**에는 최소 단어 수를 입력합니다. 그러면 모듈에서는 이 값보다 빈도가 낮은 모든 단어를 무시합니다.

    기본값은 5입니다.

8. 파이프라인을 제출합니다.

## <a name="examples"></a>예

모듈의 두 출력은 다음과 같습니다.

+ **임베딩이 있는 어휘**: 생성된 어휘를 포함하고, 각 단어의 임베딩과 함께 하나의 차원이 하나의 열을 차지합니다.

### <a name="result-examples"></a>결과 예제

**단어를 벡터로 변환** 모듈의 작동 방식을 설명하기 위해 다음 예제에서는 기본 설정의 모듈을 Azure Machine Learning(미리 보기)에서 제공된 전처리된 Wikipedia SP 500 데이터 세트에 적용합니다.

#### <a name="source-dataset"></a>원본 데이터 세트

데이터 세트에는 범주 열뿐만 아니라 Wikipedia에서 가져온 전체 텍스트가 포함됩니다. 이 표에서는 몇 가지 대표적인 예제만 보여 줍니다.

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>임베딩이 있는 어휘 출력

다음 표에는 Wikipedia SP 500 데이터 세트를 입력으로 가져오는 이 모듈의 출력이 포함되어 있습니다. 가장 왼쪽에 있는 열은 어휘를 나타내며, 임베딩 벡터는 동일한 행의 나머지 열 값으로 표현됩니다.

|어휘|임베딩 dim 0|임베딩 dim 1|임베딩 dim 2|임베딩 dim 3|임베딩 dim 4|임베딩 dim 5|...|임베딩 dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|구성 요소|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
위치|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

이 예제에서는 기본 `Gensim Word2Vec`을 **Word2Vec 전략**으로 사용하고, **학습 알고리즘**은 `Skip-gram`이며, **단어 임베딩의 길이**는 100입니다. 따라서 임베딩 열이 100개 있습니다.

## <a name="technical-notes"></a>기술 정보

이 섹션에서는 팁과 함께 자주 묻는 질문에 대한 대답이 포함되어 있습니다.

+ 온라인 학습과 미리 학습된 모델의 차이점

    이 **단어를 벡터로 변환 모듈**에서는 두 가지 온라인 학습 모델과 하나의 미리 학습된 모델의 세 가지 다른 전략을 제공했습니다. 온라인 학습 모델은 입력 데이터 세트를 학습 데이터로 사용하고, 학습 중에 어휘 및 단어 벡터를 생성합니다. 반면 미리 학습된 모델은 Wikipedia 또는 Twitter 텍스트와 같은 훨씬 더 큰 규모의 텍스트 모음으로 학습됩니다. 따라서 미리 학습된 모델은 실제 (단어, 임베딩) 쌍의 컬렉션입니다.  

    Glove 미리 학습된 모델을 단어 벡터화 전략으로 선택한 경우 입력 데이터 세트에서 어휘를 요약하고 온라인 교육 없이 미리 학습된 모델의 각 단어에 대한 임베딩 벡터를 생성합니다. 미리 학습된 모델을 사용하면 학습 시간을 절약할 수 있으며, 특히 입력 데이터 세트 크기가 비교적 작은 경우 성능이 향상됩니다.

+ 임베딩 크기

    일반적으로 단어 임베딩의 길이는 좋은 성능을 얻기 위해 수백(예: 100, 200, 300)으로 설정되어 있는데, 이는 임베딩 크기가 작으면 단어 임베딩 충돌을 일으킬 수 있는 벡터 공간이 작다는 뜻이기 때문입니다.  

    미리 학습된 모델의 경우 단어 임베딩의 길이는 고정되어 있으므로, 이 구현에서 glove-wiki-gigaword-100의 임베딩 크기는 100입니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

디자이너(미리 보기) 모듈과 관련된 오류 목록은 [Machine Learning 오류 코드](designer-error-codes.md)를 참조하세요.
