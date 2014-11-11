<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure 기계 학습 샘플: 정서 분석

*기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에서 이 모델과 연결된 샘플 실험을 찾을 수 있습니다. 실험 이름은 다음과 같습니다.*

    Sample Experiment - Sentiment Classification - Development

## 문제 설명

제품 리뷰 등급을 예측합니다. 등급은 1, 2, 3, 4, 5입니다. 이는 회귀 문제 또는 다중 클래스 분류 문제로 해결할 수 있는 서수 회귀 문제입니다.

## 데이터

UPenn 연구원이 Amazon 사이트에서 스크랩한 Amazon의 도서 리뷰입니다 [][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>. 원래 데이터 집합에는 1, 2, 3, 4, 5 등급의 975,000개 리뷰가 있습니다. 실험 시간을 단축하려고 데이터 집합의 10,000개 리뷰로 샘플 수를 줄였습니다. 모든 리뷰는 영어로 표시됩니다. 리뷰는 1997-2007년에 작성되었습니다.

## 모델

기능 해시 모듈을 사용하여 영어 텍스트를 정수 값 기능으로 변환했습니다. 다음 세 가지 모델을 비교했습니다.

1.  선형 회귀
2.  2클래스 로지스틱 회귀를 기본 분류자로 사용하는 서수 회귀
3.  다중 클래스 로지스틱 회귀 분류자를 사용하는 다중 클래스 분류

## 결과

알고리즘 | 평균 절대 오류 | 제곱 평균 오류
---------|

서수 회귀 | 0.82 | 1.41 |
선형 회귀 | 1.04 | 1.36 |
다중 클래스 분류 | 0.85 | 1.57

이러한 결과에 따라 서수 회귀 모델을 선택하고 이 모델을 기반으로 웹 서비스를 빌드합니다.

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
