<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="기계 학습 샘플: 임대 자전거 대수 예측 | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure 기계 학습 샘플: 임대 자전거 대수 예측

>[AZURE.NOTE]
>이 모델과 관련된 [샘플 실험] 및 [샘플 데이터 집합]은 기계 학습 스튜디오에서 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
[샘플 실험]: #sample-experiment
[샘플 데이터 집합]: #sample-dataset


## 문제 설명 ##
이전 임대 기록, 매시간 측정된 날씨, 오늘이 휴일/주중/주말인지 표시에 따라 오늘 매시간 임대될 자전거 대수 예측. 예측은 시간에 따라 다릅니다(예:
 아침에는 임대가 많고 저녁에는 임대가 거의 없음). 

## 데이터 ##
Washington DC에서 자전거 임대망을 유지 관리하는 Capital Bikeshare 회사의 실제 데이터를 기반으로 하는 UCI 자전거 임대 데이터 집합. 데이터 집합에는 2011년 및 2012년의 매일 매시간에 대한 행 하나가 있고, 전체 17379개의 행이 있습니다. 매시간 자전거 임대 범위는 1-977입니다.

## 모델 ##
2011년 데이터를 학습 집합으로 사용하고 2012년 데이터를 테스트 집합으로 사용했습니다. 기능 집합 4개를 비교했습니다.

1. 예측된 날에 대한 날씨 + 휴일 + 주중 + 주말 기능
1. 이전 12시간마다 임대된 자전거 대수
1. 이전 12일마다 같은 시간에 임대된 자전거 대수
1. 이전 12주마다 같은 시간, 같은 요일에 임대된 자전거 대수

기능 B는 자전거에 대한 가장 최근 수요를 파악합니다. 기능 C는 특정 시간에 자전거에 대한 수요를 파악합니다. 기능 D는 특정 시간 및 특정 요일에 자전거에 대한 수요를 파악합니다.

레이블(임대 수)은 실제 값이므로 회귀 설정을 포함합니다. 또한 기능 수가 비교적 작고(100 미만) 희박하지 않으므로 의사 결정 경계는 비선형일 수 있습니다. 이를 기반으로 향상된 의사 결정 트리 회귀 알고리즘을 사용하도록 결정했습니다.

## 결과 ##

<table border="1">
<tr><th>기능</th><th>절대 평균 오차</th> <th>제곱 평균 오차</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89.7</td> <td>124.9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51.2 </td> <td>86.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48.5</td> <td> 83.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48.8 </td> <td>83.2 </td></tr>
</table>

</table>

최적의 결과는 기능 A+B+C 및 A+B+C+D로 표시됩니다. 뜻밖에 기능 D는 A+B+C보다 크게 향상되지 않습니다. 

## 샘플 실험

이 모델과 연결된 다음 샘플 실험은 기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에 있습니다.

> **샘플 실험 - 자전거 수요 예측**


## 샘플 데이터 집합

이 실험에서 사용되는 다음 샘플 데이터 집합은 기계 학습 스튜디오의 **저장된 데이터 집합** 아래 모듈 팔레트에 있습니다.

###자전거 임대 UCI 데이터 집합
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


