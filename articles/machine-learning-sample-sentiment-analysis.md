<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="기계 학습 샘플: 정서 분석 | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Azure 기계 학습 샘플: 정서 분석

>[AZURE.NOTE]
>이 모델과 관련된 [샘플 실험] 및 [샘플 데이터 집합]은 기계 학습 스튜디오에서 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
[샘플 실험]: #sample-experiment
[샘플 데이터 집합]: #sample-dataset


##문제 설명
제품 리뷰 등급을 예측합니다. 등급은 1, 2, 3, 4, 5입니다. 이는 회귀 문제 또는 다중 클래스 분류 문제로 해결할 수 있는 서수 회귀 문제입니다.
 
##데이터
UPenn 연구원이 Amazon 사이트에서 스크랩한 Amazon의 도서 리뷰입니다([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). 원래 데이터 집합에는 1, 2, 3, 4, 5 등급의 975,000개 리뷰가 있습니다. 실험 시간을 단축하려고 데이터 집합의 10,000개 리뷰로 샘플 수를 줄였습니다. 모든 리뷰는 영어로 표시됩니다. 리뷰는 1997-2007년에 작성되었습니다. 
 
##모델
기능 해시 모듈을 사용하여 영어 텍스트를 정수 값 기능으로 변환했습니다. 다음 세 가지 모델을 비교했습니다.  
 
1. 선형 회귀   
2. 2클래스 로지스틱 회귀를 기본 분류자로 사용하는 서수 회귀
3. 다중 클래스 로지스틱 회귀 분류자를 사용하는 다중 클래스 분류
 
##결과

알고리즘                 | 절대 평균 오차 | 제곱 평균 오차
:---------                | :-----------------: | :--------------------:
서수 회귀        | 0.82                | 1.41
선형 회귀         | 1.04                | 1.36
다중 클래스 분류 | 0.85                | 1.57
 
이러한 결과에 따라 서수 회귀 모델을 선택하고 이 모델을 기반으로 웹 서비스를 빌드합니다.
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## 샘플 실험

이 모델과 연결된 다음 샘플 실험은 기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에 있습니다.

> **샘플 실험 - 정서 분류 - 개발**


## 샘플 데이터 집합

이 실험에서 사용되는 다음 샘플 데이터 집합은 기계 학습 스튜디오의 **저장된 데이터 집합** 아래 모듈 팔레트에 있습니다.

###Amazon의 도서 리뷰
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
