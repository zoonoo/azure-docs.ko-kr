<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="기계 학습 샘플: 비슷한 회사 찾기 | Azure" description="클러스터링 모델을 사용하여 유사한 회사를 찾는 샘플 Azure 기계 학습 실험입니다." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure 기계 학습 샘플: 비슷한 회사 찾기

>[AZURE.NOTE]
>이 모델과 관련된 [샘플 실험] 및 [샘플 데이터 집합]은 기계 학습 스튜디오에서 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
[샘플 실험]: #sample-experiment
[샘플 데이터 집합]: #sample-dataset


## 문제 설명 ##
각 회사의 Wikipedia 문서를 기반으로 S&P 500 지수의 회사를 클러스터링합니다. 예를 들어 이 모델을 사용하여 이 회사들이 기존 S&P 500 회사와 비슷한 정도에 따라 전도가 유망한 회사로 레이블을 지정할 수 있습니다.

## 데이터 ##
데이터는 Wikipedia XML 덤프에서 파생됩니다. 데이터는 각 특정 회사에 대한 텍스트 콘텐츠를 추출하고, Wiki 형식 지정을 제거하고, 영숫자 문자를 제거하고, 모든 텍스트를 소문자로 변환하도록 기계 학습 스튜디오 외부에서 전처리되었습니다. 또한 알려진 회사 범주가 추가되었습니다. 몇몇 회사에 대한 문서는 찾을 수가 없었습니다. 따라서 레코드 수는 500보다 적습니다.

## 모델 ##
먼저 문서 콘텐츠가 기능 해시 모듈에 전달되고 이 모듈이 각 토큰의 해시 값에 따라 텍스트 데이터를 숫자 형식으로 변환합니다. 이 데이터는 너무 고차원이고 희박하므로 K-평균 클러스터링에서 직접 사용할 수 없습니다. 따라서 PCA는 R 스크립트 실행 모듈 내에서 차원을 변수 10개로 줄이는 데 사용됩니다. R 모듈의 오른쪽 출력을 열어서 PCA 결과를 시각화할 수 있습니다. 

시행착오를 통해 PCA 변환 데이터의 차이가 가장 큰 첫 번째 변수는 클러스터링에 대해 해로운 영향을 미치는 것으로 파악되었습니다. 따라서 이 변수는 프로젝트 열 모듈을 사용하여 기능 집합에서 제거됩니다.

그리고 나서 데이터는 K-평균 알고리즘을 사용하여 클러스터 3개로 클러스터링됩니다. 시행착오를 통해 이 수는 합리적인 결과를 생성하는 것으로 파악되었습니다. 클러스터 4 -5개도 시도되었습니다. 

마지막으로 메타데이터 편집기를 사용하여 클러스터 레이블 변수를 범주로 변환합니다. 결과도 다운로드를 위해 CSV 파일로 변환됩니다.

## 결과 ##
메타데이터 편집기에서 출력을 시각화하고 Category 열(Wikipedia 데이터에서 알려짐)을 Assignments 열에 대해 플로팅하여 결과를 볼 수 있습니다. 클러스터 3개는 다음 알려진 범주와 대략 일치합니다.

클러스터 0: 소비자 재량지수, 필수 소비재, 재무, 의료

클러스터 1: 정보 기술

클러스터 2: 에너지, 유틸리티, 통신 서비스

**참고** 클러스터링은 분명하지 않습니다. 클러스터 0에는 여러 정보 기술 및 에너지 회사가 있고 산업과 자재는 클러스터 0과 클러스터 1 간에 분할됩니다.

**참고** 클러스터링은 실행할 때마다 달라질 수 있습니다. 



## 샘플 실험

이 모델과 연결된 다음 샘플 실험은 기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에 있습니다.

> **샘플 실험 - S & P 500 회사 클러스터링 - 개발**


## 샘플 데이터 집합

이 실험에서 사용되는 다음 샘플 데이터 집합은 기계 학습 스튜디오의 **저장된 데이터 집합** 아래 모듈 팔레트에 있습니다.

###Wikipedia SP 500 데이터 집합
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
