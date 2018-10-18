---
title: AMLPTA(텍스트 분석을 위한 AML(Azure Machine Learning) 패키지) 및 TDSP(Team Data Science Process)를 사용한 Twitter 감정 분류 | Microsoft Docs
description: 감정 분류를 위한 TDSP(Team Data Science Process) 및 AMLPTA 사용에 대한 설명
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: acad053712d237fdb86934faa7c5c4546c630387
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394564"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>AMLPTA(텍스트 분석을 위한 AML(Azure Machine Learning) 패키지) 및 TDSP(Team Data Science Process)를 사용한 Twitter 감정 분류

## <a name="introduction"></a>소개
데이터 과학 팀의 공동 작업 효율성을 높이기 위한 핵심은 설정된 [데이터 과학 수명 주기](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)에 고정된 데이터 과학 프로젝트의 구조 및 문서를 표준화하는 것입니다.

이전에 [TDSP 프로젝트 구조 및 템플릿에 대한 GitHub 리포지토리](https://github.com/Azure/Azure-TDSP-ProjectTemplate)를 릴리스했습니다. [Azure Machine Learning에 대한 TDSP 구조 및 문서 템플릿](https://github.com/amlsamples/tdsp)에서 인스턴스화된 Azure Machine Learning 프로젝트를 생성하도록 설정되었습니다. Azure Machine Learning에서 TDSP 구조와 템플릿을 사용하는 방법에 대한 지침은 [여기](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml)에서 제공됩니다. 

이 샘플에서는 텍스트 분석을 위한 Azure Machine Learning 패키지와 TDSP를 사용하여 Twitter 감정 분류를 위한 예측 모델을 개발하고 배포하는 방법을 보여줍니다


## <a name="use-case"></a>사용 사례
### <a name="twitter-sentiment-polarity-sample"></a>Twitter 감정 극성 샘플
이 문서에서는 샘플을 사용하여 Machine Learning 프로젝트를 인스턴스화하고 실행하는 방법을 보여 줍니다. 샘플은 Azure Machine Learning Workbench에서 TDSP 구조와 템플릿을 사용합니다. 전체 샘플은 이 연습에서 제공됩니다. 모델링 작업은 트윗의 텍스트를 사용하여 감정 극성(양수 또는 음수)을 예측합니다. 이 문서에서는 연습에서 설명하는 데이터 모델링 작업에 대해 간략히 설명합니다. 이 연습에서 다루는 작업은 다음과 같습니다.

1. 사용 사례 개요에서 설명한 예측 문제를 해결하는 기계 학습 모델의 데이터 탐색, 학습 및 배포. Twitter 감정 데이터가 이러한 작업에 사용됩니다.
2. 이 프로젝트에 Azure Machine Learning의 TDSP 템플릿을 사용하여 프로젝트 실행. TDSP 수명 주기가 프로젝트 실행 및 보고에 사용됩니다.
3. Azure Container Service의 Azure Machine Learning에서 솔루션 직접 운영

이 프로젝트는 Azure Machine Learning용 텍스트 분석 패키지 사용에 중점을 두고 있습니다.


## <a name="link-to-github-repository"></a>GitHub 리포지토리에 연결
GitHub 리포지토리에 대한 링크는 [여기](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction)에 있습니다. 

### <a name="purpose"></a>목적
이 샘플의 주된 목적은 Azure Machine Learning Work Bench에서 TDSP(Team Data Science Process) 구조 및 템플릿을 사용하여 Machine Learning 프로젝트를 인스턴스화하고 실행하는 방법을 보여주는 것입니다. 이러한 목적을 위해 [Twitter 감정 데이터](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)가 사용됩니다. 모델링 작업은 트윗의 텍스트를 사용하여 감정 극성(양수 또는 음수)을 예측하기 위한 작업입니다.

### <a name="scope"></a>범위
- 사용 사례 개요에 설명된 예측 문제를 해결하는 Machine Learning 모델의 데이터 탐색, 학습 및 배포
- 이 프로젝트에 Azure Machine Learning의 TDSP(Team Data Science Process) 템플릿을 사용하여 Azure Machine Learning의 프로젝트 실행 프로젝트 실행 및 보고를 위해 TDSP 수명 주기를 사용하도록 하겠습니다.
- Azure Container Service에서 Azure Machine Learning의 솔루션 직접 운영

이 프로젝트는 TDSP 구조 인스턴스화 및 사용, Azure Machine Learning Work Bench에서 코드 실행, Docker와 Kubernetes를 사용하는 Azure Container Service의 간편한 운영화와 같은 Azure Machine Learning의 몇 가지 기능에 중점을 두고 있습니다.

## <a name="team-data-science-process-tds"></a>TDSP(Team Data Science Process)
이 샘플을 실행하기 위해 TDSP 프로젝트 구조와 문서 템플릿이 사용됩니다. [TDSP 수명 주기](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle)를 따릅니다. 프로젝트는 [여기](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)에 제공된 지침을 기반으로 생성됩니다.


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>사용 사례 개요
트위터 텍스트에서 추출한 단어 임베딩 기능을 사용하여 각 트위터의 감정 이진 극성을 예측하는 작업입니다. 자세한 설명은 [리포지토리](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction)를 참조하세요.

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[데이터 취득 및 이해](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
이 샘플의 첫 번째 단계는 sentiment140 데이터 집합을 다운로드하여 학습 및 테스트 데이터 집합으로 나누는 것입니다. Sentiment140 데이터 집합은 중립 트윗이 제거되고 각 트윗의 극성(음성=0, 양성=4)과 함께 트윗의 실제 콘텐츠(이모티콘은 제거됨)를 포함합니다. 생성된 학습 데이터에는 130만개의 행이 있고 테스트 데이터에는 32만개의 행이 있습니다.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[모델링](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
샘플의 이 부분은 세 개의 하위 부분으로 세분화됩니다. 
- **기능 엔지니어링**은 Word2Vec라는 다른 단어 임베딩 알고리즘을 사용하는 기능의 생성에 해당합니다. 
- **모델 만들기**는 입력 텍스트의 감정을 예측하기 위해 로지스틱 회귀(_logistic regression_) 및 그라데이션 부스트(_gradient boosting_)와 같은 다양한 모델의 학습을 처리합니다. 
- **모델 평가**는 학습된 모델을 테스트 데이터에 적용합니다.

#### <a name="feature-engineering"></a>기능 엔지니어링
<b>Word2Vec</b>를 사용하여 단어 임베딩을 생성합니다. 먼저 [Mikolov, Tomas, et al. Distributed representations of words and phrases and their compositionality. 신경 정보 처리 시스템의 발전. 2013.](https://arxiv.org/abs/1310.4546) 이라는 논문의 설명대로 Skipgram 모드에서 Word2Vec 알고리즘을 사용하여 단어 임베딩을 생성합니다.

Skip-gram은 하나의 핫 벡터(hot vector)를 인코딩된 대상 단어로 취하여 주변 단어를 예측하는 데 사용하는 얕은(shallow) 신경망입니다. V가 어휘의 크기이면 출력 계층의 크기는 __C*V__이며, 여기서 C는 컨텍스트 윈도우의 크기입니다. skip-gram 기반 아키텍처는 아래 그림과 같습니다.
 
<table class="image" align="center">
<caption align="bottom">Skip-gram 모델</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Word2Vec 알고리즘과 skip-gram 모델에 대한 세부 사항은 이 샘플의 범위에 포함되지 않으며 관심이 있는 독자는 다음 링크를 통해 자세한 내용을 확인할 수 있습니다. 코드 02_A_Word2Vec.py에는 [tensorflow 예제](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)가 참조되었습니다.

* [단어의 벡터 표현](https://www.tensorflow.org/tutorials/word2vec)
* [word2vec은 정확히 어떻게 작동할까요?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [노이즈 대비 추정 및 음수 샘플링에 대한 참고 사항](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

학습 프로세스가 완료되면 모델링 단계에서 TSV 형식으로 두 개의 임베딩 파일이 생성됩니다.

#### <a name="model-training"></a>모델 학습
SSWE 또는 Word2vec 알고리즘 중 하나를 사용하여 단어 벡터가 생성되면 다음 단계에서는 실제 감정 극성을 예측하도록 분류 모델을 학습시킵니다. Word2Vec 및 SSWE라는 두 가지 유형의 기능이 로지스틱 회귀 및 CNN(나선형 신경망)이라는 두 가지 모델에 적용됩니다. 

#### <a name="model-evaluation"></a>모델 평가
테스트 데이터 집합에 여러 학습 모델을 로드하고 평가하는 방법에 대한 코드가 제공됩니다.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[배포](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
이 부분에는 AKS(Azure Container Service)에서 클러스터의 웹 서비스에 미리 학습된 감정 예측 모델을 운용하는 방법에 대한 지침이 제공됩니다. 운영 환경에서는 클러스터의 Docker 및 Kubernetes를 프로비전하여 웹 서비스 배포를 관리합니다. 운영 프로세스에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy)를 참조하세요.

## <a name="conclusion"></a>결론
Word2Vec를 사용하여 단어 임베딩 모델을 학습시킨 다음, 추출된 임베딩을 기능으로 사용하여 두 가지 다른 모델을 학습시켜서 Twitter 텍스트 데이터의 감정 점수를 예측하는 방법을 자세히 살펴봤습니다. 이러한 모델 중 하나가 AKS(Azure Container Services)에 배포되어 있습니다. 

## <a name="next-steps"></a>다음 단계
시작하려면 [AMLPTA(Azure Machine Learning Package for Text Analytics)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) 및 [TDSP(Team Data Science Process)](https://aka.ms/tdsp)에 대한 설명서를 참조하세요.

## <a name="references"></a>참조
* [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Azure Machine Learning에서 TDSP(Team Data Science Process)를 사용하는 방법](https://aka.ms/how-to-use-tdsp-in-aml)
* [Azure Machine Learning의 TDSP 프로젝트 템플릿](https://aka.ms/tdspamlgithubrepo)
* [Azure ML Work Bench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomas 외 공저, Distributed representations of words and phrases and their compositionality. 신경 정보 처리 시스템의 발전. 2013.](https://arxiv.org/abs/1310.4546)
