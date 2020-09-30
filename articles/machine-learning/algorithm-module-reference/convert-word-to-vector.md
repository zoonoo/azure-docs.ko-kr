---
title: 'Word를 Vector로 변환: 모듈 참조'
titleSuffix: Azure Machine Learning
description: 제공된 세 개의 Word2Vec 모델을 사용하여 텍스트 모음에서 어휘 및 해당하는 단어 임베딩을 추출하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536735"
---
# <a name="convert-word-to-vector-module"></a>Word를 Vector 모듈로 변환

이 문서에서는 Azure Machine Learning 디자이너의 Vector to Vector로 변환 모듈을 사용 하 여 다음 작업을 수행 하는 방법을 설명 합니다.

- 입력으로 지정한 텍스트의 모음에 다양 한 Word2Vec 모델 (Word2Vec, fasttext, 글러브 미리 학습 된 된 모델)을 적용 합니다.
- 단어 포함를 사용 하 여 어휘를 생성 합니다.

이 모듈은 Gensim 라이브러리를 사용합니다. Gensim에 대 한 자세한 내용은 자습서 및 알고리즘에 대 한 설명이 포함 된 [공식 웹 사이트](https://radimrehurek.com/gensim/apiref.html)를 참조 하세요.

### <a name="more-about-converting-words-to-vectors"></a>단어를 벡터로 변환 하는 방법에 대 한 자세한 정보

단어를 벡터 또는 단어 벡터화로 변환 하는 것은 NLP (자연어 처리) 프로세스입니다. 이 프로세스에서는 언어 모델을 사용 하 여 단어를 벡터 공간에 매핑합니다. 벡터 공간은 실수의 벡터로 각 단어를 나타냅니다. 또한 비슷한 의미를 가진 단어가 비슷한 표현을 가질 수 있습니다.

텍스트 분류 및 감정 분석과 같은 NLP 다운스트림 작업의 초기 입력으로 word 포함을 사용 합니다.

이 모듈에서는 다양 한 단어 포함 기술 중에서 널리 사용 되는 세 가지 메서드를 구현 했습니다. 2, Word2Vec 및 FastText는 온라인 학습 모델입니다. 다른는 미리 학습 된 모델인 글러브-gigaword-100입니다. 

온라인-학습 모델은 입력 데이터에 대해 학습 됩니다. 사전 학습 된 모델은 일반적으로 약 1000억 단어를 포함 하는 더 큰 텍스트 모음 (예: 위키백과, Google News)에서 오프 라인으로 학습 됩니다. 단어 포함은 단어를 벡터화 하는 동안 일정 하 게 유지 됩니다. 사전 학습 된 단어 모델은 교육 시간 감소, 향상 된 단어 벡터 인코딩, 전반적인 성능 향상 등의 이점을 제공 합니다.

메서드에 대 한 몇 가지 정보는 다음과 같습니다.

+ Word2Vec는 단순 신경망을 사용 하 여 단어 포함을 배우는 가장 인기 있는 기술 중 하나입니다. 이 문서에서는이 문서에서 설명 합니다 .이 문서에서는 PDF 다운로드로 사용할 수 있습니다. [벡터 공간에서 단어 표현의 효율성을 효율적으로 추정](https://arxiv.org/pdf/1301.3781.pdf)합니다. 이 모듈의 구현은 [Word2Vec 용 Gensim 라이브러리](https://radimrehurek.com/gensim/models/word2vec.html)를 기반으로 합니다.

+ FastText 이론은이 문서에 설명 되어 있습니다 .이 문서는 PDF 다운로드로 사용할 수 있습니다. [보강 Word 벡터 (부분 단어까지 정보 포함](https://arxiv.org/pdf/1607.04606.pdf)). 이 모듈의 구현은 [FastText에 대 한 Gensim 라이브러리](https://radimrehurek.com/gensim/models/fasttext.html)를 기반으로 합니다.

+ 글러브 미리 학습 된 된 모델은 글러브-gigaword-100입니다. 56억 토큰 및 40만 uncased 어휘 단어를 포함 하는 위키백과 텍스트 모음을 기반으로 하는 사전 학습 벡터의 컬렉션입니다. PDF 다운로드를 사용할 수 있습니다. [글러브: 단어 표현의 전역 벡터](https://nlp.stanford.edu/pubs/glove.pdf)입니다.

## <a name="how-to-configure-convert-word-to-vector"></a>단어에서 벡터로의 변환을 구성하는 방법

이 모듈에는 텍스트 열을 포함 하는 데이터 집합이 필요 합니다. 전처리 된 텍스트가 더 좋습니다.

1. **단어를 벡터로 변환** 모듈을 파이프라인에 추가합니다.

2. 모듈에 대 한 입력으로 하나 이상의 텍스트 열을 포함 하는 데이터 집합을 제공 합니다.

3. **대상 열**에서 처리할 텍스트가 포함 된 열을 하나만 선택 합니다.

    이 모듈은 텍스트에서 어휘를 만들기 때문에 열 내용이 서로 다르므로 다른 어휘 내용으로 이어집니다. 따라서 모듈은 하나의 대상 열만 허용 합니다.

4. **Word2Vec 전략**의 경우 **글러브 미리 학습 된 된 English Model**, **gensim Word2Vec**및 **gensim fasttext**에서 선택 합니다.

5. **Word2Vec 전략이** **gensim Word2Vec** 또는 **gensim fasttext**인 경우:

    + **Word2Vec 교육 알고리즘**의 경우 **Skip_gram** 및 **cbow**를 선택 합니다. 차이점은 [원본 용지 (PDF)](https://arxiv.org/pdf/1301.3781.pdf)에 도입 되었습니다.

        기본 메서드는 **Skip_gram**입니다.

    + **단어 포함의 길이**는 단어 벡터의 차원을 지정 합니다. 이 설정은 `size` Gensim의 매개 변수에 해당 합니다.

        기본 포함 크기는 100입니다.

    + **컨텍스트 창 크기**에 대해 예측 하는 단어와 현재 단어 사이의 최대 거리를 지정 합니다. 이 설정은 `window` Gensim의 매개 변수에 해당 합니다.

        기본 창 크기는 5입니다.

    + **Epoch 수**에 대해 모음에 대 한 epoch (반복) 수를 지정 합니다. `iter`Gensim의 매개 변수에 해당 합니다.

        기본 epoch 번호는 5입니다.

6. **최대 어휘 크기**에 대해 생성 된 어휘에 있는 단어의 최대 개수를 지정 합니다.

    최대 크기 보다 고유한 단어가 더 많은 경우 자주 발생 하지 않는 단어를 정리 합니다.

    기본 어휘 크기는 1만입니다.

7. **최소 단어 수**에 대해 최소 단어 수를 제공 합니다. 모듈은 frequency가이 값 보다 낮은 모든 단어를 무시 합니다.

    기본값은 5입니다.

8. 파이프라인을 제출합니다.

## <a name="examples"></a>예

모듈의 두 출력은 다음과 같습니다.

+ **포함가 있는 어휘**: 생성 된 어휘와 함께 각 단어 포함을 포함 합니다. 한 차원은 하나의 열을 차지 합니다.

다음 예에서는 Word를 Vector로 변환 모듈의 작동 방식을 보여 줍니다. 기본 설정을 사용 하 여 전처리 된 위키백과 SP 500 데이터 집합으로 Word를 Vector로 변환을 사용 합니다.

### <a name="source-dataset"></a>원본 데이터 세트

데이터 집합은 위키백과에서 가져온 전체 텍스트와 함께 범주 열을 포함 합니다. 다음 표에서는 몇 가지 대표적인 예를 보여 줍니다.

|텍스트|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>임베딩이 있는 어휘 출력

다음 표에는 위키백과 SP 500 데이터 집합을 입력으로 가져오는이 모듈의 출력이 포함 되어 있습니다. 가장 왼쪽에 있는 열은 어휘를 나타냅니다. 포함 벡터는 동일한 행의 나머지 열 값으로 표현 됩니다.

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

이 예제에서는 기본 **Gensim Word2Vec** for **Word2Vec 전략**을 사용 했으며 **학습 알고리즘** 은 **건너뜀-영문법**입니다. **단어 포함의 길이** 는 100 이므로 100 포함 열이 있습니다.

## <a name="technical-notes"></a>기술 정보

이 섹션에서는 팁과 함께 자주 묻는 질문에 대한 대답이 포함되어 있습니다.

+ 온라인 학습 및 미리 학습 된 모델의 차이점:

    이 Word를 Vector로 변환 모듈에서는 두 가지 방법, 즉 온라인 학습 모델과 미리 학습 된 모델 두 가지를 제공 했습니다. 온라인 학습 모델은 입력 데이터 집합을 학습 데이터로 사용 하 고 학습 중에 어휘 및 단어 벡터를 생성 합니다. 미리 학습 된 모델은 위키백과 또는 Twitter 텍스트와 같은 훨씬 더 큰 텍스트 모음 이미 학습 되었습니다. 사전 학습 된 모델은 실제로 단어/포함 쌍의 컬렉션입니다.  

    글러브 미리 학습 된 모델은 입력 데이터 집합의 어휘를 요약 하 고 사전 학습 된 모델의 각 단어에 대 한 포함 벡터를 생성 합니다. 온라인 교육이 없으면 사전 학습 된 모델을 사용 하 여 학습 시간을 절약할 수 있습니다. 특히 입력 데이터 집합 크기가 비교적 작은 경우 성능이 향상 됩니다.

+ 포함 크기:

    일반적으로 단어 포함의 길이는 몇 백로 설정 됩니다. 예: 100, 200, 300. 작은 포함 크기는 작은 벡터 공간을 의미 하므로 단어 포함 충돌을 일으킬 수 있습니다.  

    포함 단어의 길이는 미리 학습 된 모델에 대해 고정 됩니다. 이 예제에서 글러브-gigaword-100의 포함 크기는 100입니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 

디자이너 모듈과 관련 된 오류 목록은 [Machine Learning 오류 코드](designer-error-codes.md)를 참조 하세요.
