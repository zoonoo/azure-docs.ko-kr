<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure 기계 학습 샘플: CRM 작업

*기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에서 이 모델과 연결된 샘플 실험을 찾을 수 있습니다. 실험 이름은 다음과 같습니다.*

    Sample Experiments - CRM - Development

## 문제 설명

고객 이탈(공급자 전환), 상향 판매(업그레이드 또는 추가 기능 구매) 및 새 제품 구매 성향(욕구)의 예측

## 데이터

프랑스 통신 회사 Orange in France의 50K 고객에 대한 데이터 집합. 각 고객에는 익명 처리된 기능 230개가 포함됩니다. 이 데이터는 KDD Cup 2009에 나옵니다.

숫자 및 문자열 기능이 둘 다 있습니다. 기능이 매우 희박합니다.

## 모델

데이터 전처리에서는 누락 값만 처리합니다. 문자열 기능에서 누락 값을 "0"으로 대체했습니다. 거의 모든 숫자 기능에는 음수가 아닌 값이 있습니다. 숫자 기능의 기존 값에 1을 추가했고 항목의 0을 누락 값으로 대체했습니다. 이 방법으로 실제 원래 0과 누락 값을 나타내는 0을 구분합니다. 먼저 수치 연산(+1)을 숫자 기능에만 적용하여 이 작업을 했습니다. 그리고 나서 모든 누락 값을 0(또는 문자열의 경우 "0")으로 바꾸었습니다.

숫자 및 범주 기능이 둘 다 있으므로 향상된 의사 결정 트리 분류자를 사용했습니다. 문제 3개가 모두 불균형 상태이고 긍정적 예제가 소수가 됩니다. 때때로 이 때문에 분류자가 기능 공간에서 긍정적 샘플 수가 적은 지역을 무시할 수 있습니다. 이를 테스트하기 위해 각 문제에 대한 두 가지 모델을 학습했습니다. 한 모델에서는 원시 데이터를 사용하고 다른 모델에서는 새로운 긍정적 예제의 수가 부정적 테스트 예제의 수와 대략 같도록 긍정적 예제를 복제합니다. 긍정적 및 부정적 예제를 분할하는 단순 R 스크립트를 사용하여 이 작업을 수행하고 나서 긍정적 집합 복사본 13개를 부정적 집합에 추가했습니다.

## 결과

모델은 각 작업에 대한 긍정적 결과의 가능성과 관련된 점수를 출력합니다. 관찰을 긍정적으로 레이블 지정하도록 임의 중단 임계값을 선택할 수 있으므로 ROC(수신자 조작 특성) 곡선(AUC)에서 영역별로 성능을 측정합니다. 이탈 및 상향 판매 사례의 경우 긍정적 샘플을 복제하여 더 균형 잡힌 집합을 만들면 모델 성능이 약간 향상되었습니다.

<table border="1">
<tr><td>이탈</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>복제 없음</td><td>0.711</td></tr>
<tr><td>복제된 긍정</td><td>0.728</td></tr>
</table>
<table border="0">
<tr><td>상향 판매</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>복제 없음</td><td>0.853</td></tr>
<tr><td>복제된 긍정</td><td>0.865</td></tr>
</table>
<table border="0">
<tr><td>욕구</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>복제 없음</td><td>0.805</td></tr>
<tr><td>복제된 긍정</td><td>0.8</td></tr>
</table>


## API

익명 기능이 있으므로 모델을 조작화하지 않았습니다.

