---
title: '자습서: 자동화된 기계 학습 모델 보강'
titleSuffix: Azure Open Datasets
description: Azure Machine Learning Service의 기능과 함께 Azure Open Datasets의 편리한 기능을 이용하여 뉴욕 시(NYC)의 택시 요금을 예측하는 회귀 모델을 만드는 방법을 알아봅니다.
services: open-datasets
ms.service: open-datasets
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: e753793b5da59d09a21991831046a43899c62ef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027519"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>자습서: 자동화된 기계 학습 및 Azure Open Datasets를 사용하여 회귀 모델 만들기

이 자습서에서는 Azure Machine Learning Service의 기능과 함께 Azure Open Datasets의 편리한 기능을 이용하여 뉴욕 시(NYC)의 택시 요금을 예측하는 회귀 모델을 만듭니다. 공개적으로 사용할 수 있는 택시, 휴일 및 기상 데이터를 쉽게 다운로드하고 Azure Machine Learning Service를 사용하여 자동화된 기계 학습 실험을 구성합니다. 이 프로세스는 학습 데이터 및 구성 설정을 적용하며 다양한 기능 정규화/표준화 메서드, 모델 및 하이퍼 매개 변수 설정의 결합을 자동으로 반복하여 최선의 모델에 도달합니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

- Azure Machine Learning 서비스 작업 영역 구성
- 로컬 Python 환경 설정
- Azure Open Datasets를 사용하여 데이터 액세스, 변환 및 조인
- 자동화된 기계 학습 회귀 모델 학습
- 모델 정확도 계산

## <a name="prerequisites"></a>필수 조건

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

* Azure Machine Learning Service 작업 영역
* Python 3.6 환경

### <a name="create-a-workspace"></a>작업 영역 만들기

아직 없는 경우 [지침](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace#portal)에 따라 Azure Portal을 통해 작업 영역을 만듭니다. 작업 영역을 만든 후 작업 영역 이름, 리소스 그룹 이름 및 구독 ID를 메모합니다.

### <a name="create-a-python-environment"></a>Python 환경 만들기

이 예제에서는 Jupyter Notebook과 Anaconda 환경을 사용하지만 이 코드를 임의의 3.6.x 환경에서 임의의 텍스트 편집기 또는 IDE를 사용하여 실행할 수 있습니다. 다음 단계에 따라 새 개발 환경을 만듭니다.

1. 아직 없는 경우 Anaconda를 [다운로드](https://www.anaconda.com/distribution/)하고 설치한 후 **Python 3.7 버전**을 선택합니다.
1. Anaconda 프롬프트를 열고 새 환경을 만듭니다. 구성 요소 및 패키지를 다운로드하는 동안에 환경을 만드는 데 몇 분 정도 걸립니다.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. 환경을 활성화합니다.
    ```
    conda activate tutorialenv
    ```
1. 환경별 IPython 커널을 활성화합니다.
    ```
    conda install notebook ipykernel
    ```
1. 커널을 만듭니다.
    ```
    ipython kernel install --user
    ```
1. 이 자습서에 필요한 패키지를 설치합니다. 이는 큰 패키지이며 설치하는 데 5 ~ 10분이 걸립니다.
    ```
    pip install azureml-sdk[automl] azureml-contrib-opendatasets
    ```
1. 환경에서 Notebook 커널을 시작합니다.
    ```
    jupyter notebook
    ```

이 단계를 완료한 후 [Open Datasets Notebook 리포지토리](https://github.com/Azure/OpenDatasetsNotebooks)를 복제하고 **tutorials/taxi-automl/01-tutorial-opendatasets-automl.ipynb** Notebook을 열어서 실행합니다.

## <a name="download-and-prepare-data"></a>데이터 다운로드 및 준비

필요한 패키지를 가져옵니다. Open Datasets 패키지는 다운로드하기 전에 필터 날짜 매개 변수를 쉽게 필터링하기 위해 각 데이터 원본(예: `NycTlcGreen`)을 표시하는 클래스를 포함합니다.


```python
from azureml.contrib.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

먼저 택시 데이터를 저장할 데이터 프레임을 만듭니다. Spark 이외의 환경에서 작업하는 경우 Open Datasets는 큰 데이터 세트와 관련된 `MemoryError`를 방지하기 위해 특정 클래스를 사용하여 한 번에 1개월 분량의 데이터만 다운로드할 수 있습니다. 1년 분량의 택시 데이터를 다운로드하려면 데이터 프레임의 급격한 증가를 방지하기 위해 한 번에 1개월 분량씩 반복해서 페치하고 `green_taxi_df`에 추가하기 전에 각 월 데이터에서 레코드 2000개를 임의로 샘플링합니다. 그런 다음, 데이터를 미리 봅니다.

>[!NOTE]
> Open Datasets는 데이터 크기 및 메모리가 문제가 되지 않는 Spark 환경에서 작동하기 위한 미러링 클래스를 포함합니다.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3.08</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5.</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1.04</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10행 × 23열</p>
</div>



이제 초기 데이터가 로드되었으므로 태운 날짜/시간 필드에서 다양한 시간 기반 기능을 만들기 위한 함수를 정의합니다. 그러면 월 숫자, 월간 일자, 요일 및 일간 시간에 대한 새 필드가 생성되며 모델에서 시간 기반 계절성을 고려할 수 있게 됩니다. 또한 이 함수는 휴일 데이터를 조인하기 위해 국가 코드에 대한 정적 기능을 추가합니다. 데이터 프레임에 대해 `apply()` 함수를 사용하여 택시 데이터의 각 행에 `build_time_features()` 함수를 반복해서 적용합니다.


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day", "country_code"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0.98</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3.08</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2.87</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.830894</td>
      <td>40.759434</td>
      <td>-73.842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.927109</td>
      <td>40.762848</td>
      <td>-73.909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>US</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0.80</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.881195</td>
      <td>40.741779</td>
      <td>-73.872086</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>US</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1.04</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.954376</td>
      <td>40.805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>US</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2.82</td>
      <td>없음</td>
      <td>없음</td>
      <td>-73.845200</td>
      <td>40.722134</td>
      <td>-73.810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
<p>10행 × 28열</p>
</div>

모델링 또는 추가 기능 만들기에 필요 없는 일부 열을 제거합니다. 태운 시간에 대한 시간 필드의 이름을 바꾸고 추가로 `pandas.Series.dt.normalize`를 사용하여 시간을 자정으로 변환합니다. 날짜/시간 구성 요소가 나중에 데이터 세트를 일 수준의 세분성으로 함께 조인할 때 키로 사용될 수 있도록 모든 시간 기능에 대해 이 작업을 수행합니다.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>Datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>US</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>US</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>휴일 데이터 보강

이제 택시 데이터를 다운로드하고 준비가 거의 완료되었으므로 추가 기능으로 휴일 데이터를 추가합니다. 주요 휴일은 택시 수요가 획기적으로 증가하고 공급이 제한되는 시기이므로 휴일별 기능은 모델 정확도를 높이는 데 도움이 됩니다. 휴일 데이터 세트는 상대적으로 작으므로 `PublicHolidays` 클래스 생성자를 필터링용 매개 변수 없이 사용하여 전체 세트를 페치합니다. 데이터를 미리 보고 형식을 확인합니다.

```python
from azureml.contrib.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>알바니아</td>
      <td>정월 초하루</td>
      <td>없음</td>
      <td>AL</td>
      <td>정월 초하루</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>알제리</td>
      <td>정월 초하루</td>
      <td>없음</td>
      <td>DZ</td>
      <td>정월 초하루</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>안도라</td>
      <td>정월 초하루</td>
      <td>없음</td>
      <td>AD</td>
      <td>정월 초하루</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>앙골라</td>
      <td>정월 초하루</td>
      <td>없음</td>
      <td>AO</td>
      <td>정월 초하루</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>아르헨티나</td>
      <td>정월 초하루</td>
      <td>없음</td>
      <td>AR</td>
      <td>정월 초하루</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



`countryRegionCode` 및 `date` 열의 이름을 택시 데이터의 해당 필드 이름과 일치하도록 바꾸고 시간을 키로 사용할 수 있도록 정규화합니다. 그런 다음, Pandas `merge()` 함수를 사용하여 왼쪽 조인을 수행하여 휴일 데이터를 택시 데이터와 조인합니다. 그러면 `green_taxi_df`의 모든 레코드는 유지되지만 해당 `datetime` 및 `country_code`에 대해 존재하는 휴일 데이터(이 경우 언제나 `"US"`)가 추가됩니다. 데이터를 미리 보고 올바르게 병합되었는지 확인합니다.

```python
holidays_df = holidays_df.rename(columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=["datetime", "country_code"])
taxi_holidays_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>Datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0.98</td>
      <td>-73.921715</td>
      <td>40.766682</td>
      <td>-73.916908</td>
      <td>40.761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>US</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3.08</td>
      <td>-73.979973</td>
      <td>40.677071</td>
      <td>-73.934349</td>
      <td>40.671654</td>
      <td>12.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>US</td>
      <td>True</td>
      <td>정월 초하루</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73.863045</td>
      <td>40.882923</td>
      <td>-73.839836</td>
      <td>40.868336</td>
      <td>13.8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>US</td>
      <td>True</td>
      <td>정월 초하루</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2.87</td>
      <td>-73.977730</td>
      <td>40.684647</td>
      <td>-73.931259</td>
      <td>40.694248</td>
      <td>13.8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>US</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73.942589</td>
      <td>40.841423</td>
      <td>-73.943672</td>
      <td>40.834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>US</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>기상 데이터로 보강

이제 NOAA(미국 해양대기청) 지상 기상 데이터를 택시 데이터 및 휴일 데이터에 추가합니다. 유사한 방법을 사용하여 기상 데이터를 한 번에 1개월 분량씩 반복해서 다운로드하여 페치합니다. 또한 문자열 배열과 함께 `cols` 매개 변수를 지정하여 다운로드할 열을 필터링합니다. 이는 세계 전역의 지상 기상 데이터를 포함하는 아주 큰 데이터 세트이므로 각 월을 추가하기 전에 데이터 프레임에 대해 `query()` 함수를 사용하여 위도/경도 필드를 뉴욕 시(NYC) 부근으로 필터링합니다. 그러면 `weather_df`가 너무 커지지 않습니다.

```python
from azureml.contrib.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016","%m/%d/%Y")
end = datetime.strptime("1/31/2016","%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>온도</th>
      <th>latitude</th>
      <th>precipDepth</th>
      <th>longitude</th>
      <th>Datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>5.0</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40.85</td>
      <td>NaN</td>
      <td>-74.061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40.85</td>
      <td>0.0</td>
      <td>-74.061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

기상 데이터의 `datetime` 필드에 대해 `taxi_holidays_df`의 시간 키와 일치하도록 `pandas.Series.dt.normalize`를 다시 호출합니다. 불필요한 열을 삭제하고 온도가 `NaN`인 레코드를 필터링하여 제거합니다.

그런 다음, 매일 기상 값이 집계되도록 기상 데이터를 그룹화합니다. 사전 `aggregations`를 정의하여 각 필드를 일일 수준으로 집계하는 방법을 정의합니다. `snowDepth` 및 `temperature`는 평균을 가져오며 `precipTime` 및 `precipDepth`는 일일 최댓값을 가져옵니다. `groupby()` 함수를 집계와 함께 사용하여 데이터를 그룹화합니다. 데이터를 미리 보고 일당 1개의 레코드가 있는지 확인합니다.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max", "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>온도</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>Datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5.197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3.846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0.123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7.206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0.896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3.180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4.384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6.710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24.0</td>
      <td>10.943655</td>
      <td>254.0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> 이 자습서의 예제에서는 Pandas 함수 및 사용자 지정 집계를 사용하여 데이터를 병합하지만 Open Datasets SDK에는 데이터 세트를 쉽게 병합 및 보강할 수 있는 클래스가 있습니다. 해당 디자인 패턴의 코드 예제는 [Notebook](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb)을 참조하십시오.

### <a name="cleanse-data"></a>데이터 정리

준비한 택시 및 휴일 데이터를 새 기상 데이터와 병합합니다. 이번에는 `datetime` 키만 필요하며 데이터의 왼쪽 조인을 다시 수행합니다. 새 데이터 프레임에 대해 `describe()` 함수를 실행하고 각 필드에 대한 요약 통계를 확인합니다.

```python
taxi_holidays_weather_df = pd.merge(taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>온도</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>1671.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
      <td>24000.000000</td>
    </tr>
    <tr>
      <th>평균</th>
      <td>1.786583</td>
      <td>6.576208</td>
      <td>1.582588</td>
      <td>20.505491</td>
      <td>84.936413</td>
      <td>-36.232825</td>
      <td>21.723144</td>
      <td>7.863018</td>
      <td>6.500000</td>
      <td>15.113708</td>
      <td>3.240250</td>
      <td>13.664125</td>
      <td>11.764141</td>
      <td>13.258875</td>
      <td>13.903524</td>
      <td>1056.644458</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.409728</td>
      <td>9.086857</td>
      <td>2.418177</td>
      <td>108.847821</td>
      <td>70.678506</td>
      <td>37.650276</td>
      <td>19.104384</td>
      <td>10.648766</td>
      <td>3.452124</td>
      <td>8.485155</td>
      <td>1.956895</td>
      <td>6.650676</td>
      <td>15.651884</td>
      <td>10.339720</td>
      <td>9.474396</td>
      <td>2815.592754</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>-60.000000</td>
      <td>-1.000000</td>
      <td>-74.179482</td>
      <td>0.000000</td>
      <td>-74.190704</td>
      <td>0.000000</td>
      <td>-52.800000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>0.330000</td>
      <td>-73.946680</td>
      <td>40.717712</td>
      <td>-73.945429</td>
      <td>1.770000</td>
      <td>1.000000</td>
      <td>3.750000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>6.620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>4.000000</td>
      <td>0.830000</td>
      <td>1.500000</td>
      <td>40.814129</td>
      <td>0.500000</td>
      <td>21.495000</td>
      <td>2.000000</td>
      <td>6.500000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.428571</td>
      <td>6.000000</td>
      <td>13.090753</td>
      <td>10.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>1.870000</td>
      <td>89.000000</td>
      <td>129.000000</td>
      <td>1.000000</td>
      <td>40.746146</td>
      <td>11.300000</td>
      <td>9.250000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>12.722222</td>
      <td>24.000000</td>
      <td>22.944737</td>
      <td>132.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.000000</td>
      <td>460.000000</td>
      <td>51.950000</td>
      <td>265.000000</td>
      <td>265.000000</td>
      <td>6.000000</td>
      <td>58.600000</td>
      <td>498.000000</td>
      <td>12.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>31.303665</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

요약 통계에서 이상값 또는 모델 정확도를 떨어뜨리는 값을 포함하는 여러 필드가 있음을 알 수 있습니다. 먼저 기상 위도/경도 필드를 데이터 필터링에 사용하는 것과 동일한 경계 이내가 되도록 필터링합니다. `tripDistance` 필드에는 최솟값이 음수이므로 무언가 잘못된 데이터가 있습니다. `passengerCount` 필드에도 최댓값이 승객 210명이므로 잘못된 데이터가 있습니다. 끝으로 `totalAmount` 필드에는 이 모델의 컨텍스트에 불합리한 음수 값이 있습니다.

쿼리 함수를 사용하여 이러한 이상값을 필터링한 다음, 학습에 불필요한 마지막 몇 개의 열을 제거합니다.

```python
final_df = taxi_holidays_weather_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

데이터에 대해 `describe()`를 다시 호출하여 정리가 필요한 대로 작동했는지 확인합니다. 이제 기계 학습 모델 학습에 사용할 택시, 휴일 및 기상 데이터 세트를 준비하고 정리했습니다.

```python
final_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>온도</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>1490.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
      <td>11765.000000</td>
    </tr>
    <tr>
      <th>평균</th>
      <td>1.786910</td>
      <td>1.343476</td>
      <td>2.848488</td>
      <td>14.689039</td>
      <td>3.499788</td>
      <td>14.948916</td>
      <td>3.234254</td>
      <td>13.647344</td>
      <td>12.508581</td>
      <td>11.855929</td>
      <td>10.301433</td>
      <td>208.432384</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.409508</td>
      <td>1.001232</td>
      <td>2.895960</td>
      <td>10.289832</td>
      <td>1.707865</td>
      <td>8.442438</td>
      <td>1.958477</td>
      <td>6.640280</td>
      <td>16.203195</td>
      <td>10.125701</td>
      <td>8.553512</td>
      <td>1284.892832</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.010000</td>
      <td>3.300000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>-13.379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.070000</td>
      <td>8.160000</td>
      <td>2.000000</td>
      <td>8.000000</td>
      <td>2.000000</td>
      <td>9.000000</td>
      <td>3.000000</td>
      <td>1.000000</td>
      <td>3.504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>1.900000</td>
      <td>11.300000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>3.000000</td>
      <td>15.000000</td>
      <td>4.250000</td>
      <td>6.000000</td>
      <td>10.168182</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>3.550000</td>
      <td>17.800000</td>
      <td>5.000000</td>
      <td>22.000000</td>
      <td>5.000000</td>
      <td>19.000000</td>
      <td>15.647059</td>
      <td>24.000000</td>
      <td>16.966923</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2.000000</td>
      <td>6.000000</td>
      <td>51.950000</td>
      <td>150.300000</td>
      <td>6.000000</td>
      <td>30.000000</td>
      <td>6.000000</td>
      <td>23.000000</td>
      <td>67.090909</td>
      <td>24.000000</td>
      <td>26.524107</td>
      <td>9999.000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>모델 학습

이제 준비된 데이터를 사용하여 자동화된 기계 학습 모델을 학습합니다. 먼저 `final_df`를 이 모델에 대한 택시 요금 비용인 기능(X 값) 및 레이블(y 값)으로 분할합니다.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

이제 `scikit-learn` 라이브러리에서 `train_test_split()` 함수를 사용하여 데이터를 학습 및 테스트 세트로 분할합니다. `test_size` 매개 변수는 테스트에 할당할 데이터 백분율을 정의합니다. `random_state` 매개 변수는 학습-테스트 분할이 항상 결정적이 되도록 난수 생성기에 시드를 설정합니다.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>작업 영역 로드 및 실험 구성

구독 및 작업 영역 정보와 함께 `get()` 함수를 사용하여 Azure Machine Learning Service 작업 영역을 로드합니다. 작업 영역 내에 모델 실행을 저장 및 모니터링하는 실험을 만듭니다.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>", name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

`AutoMLConfig` 클래스를 사용하여 실험에 대한 구성 개체를 만듭니다. 학습 데이터를 연결하고 학습 프로세스를 제어하는 설정 및 매개 변수를 추가로 지정합니다. 해당 매개 변수의 목적은 다음과 같습니다.

* `task`: 실행할 실험 유형
* `X`: 학습 기능
* `y`: 학습 레이블
* `iterations`: 실행할 반복 횟수. 각 반복에서 여러 하이퍼 매개 변수 설정을 사용하여 서로 다른 기능 정규화/표준화 메서드 및 서로 다른 모델의 결합을 시도합니다.
* `primary_metric`: 모델 학습 중에 최적화할 기본 메트릭. 이 메트릭을 기반으로 가장 적합한 모델이 선택됩니다.
* `preprocess`: 실험이 입력 데이터를 전처리하는지 여부를 제어합니다(누락 데이터 처리, 텍스트를 숫자로 변환 등).
* `n_cross_validations`: 유효성 검사 데이터가 지정되지 않은 경우 수행할 교차 유효성 검사 분할 수입니다.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                            )
```

### <a name="submit-experiment"></a>실험 제출

학습을 위한 실험을 제출합니다. 실험을 제출한 후 프로세스는 다른 기계 학습 알고리즘 및 하이퍼 매개 변수 설정을 반복하여 정의된 제약 조건을 준수합니다. 정의된 정확도 메트릭을 최적화하여 가장 적합한 모델을 선택합니다. `automl_config` 개체를 실험에 전달합니다. 실험 중에 진행률을 확인하려면 출력을 `True`로 설정합니다.

실험을 제출한 후 학습 프로세스에 대한 실시간 출력을 확인합니다. 각 반복에 대해 모델 유형 및 기능 정규화/표준화 메서드, 실행 기간 및 학습 정확도가 표시됩니다. 필드 `BEST`는 메트릭 유형에 따라 최적의 실행 학습 점수를 추적합니다.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>적합한 모델 검색

모든 학습 반복이 끝날 때 자동화된 기계 학습 프로세스에서 배깅 또는 스태킹을 사용하여 모든 개별 실행에서 앙상블 알고리즘을 만듭니다. 적합한 앙상블을 변수 `fitted_model`로 검색하고 최선의 개별 실행을 변수 `best_run`으로 검색합니다.

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>모델 정확도 테스트

적합한 앙상블 모델을 사용하여 테스트 데이터 세트에서 예측을 실행하여 택시 요금을 예측합니다. `predict()` 함수는 적합한 모델을 사용하고 `X_test` 데이터 세트에서 택시 요금 비용 y의 값을 예측합니다.


```python
y_predict = fitted_model.predict(X_test.values)
```

결과에 대한 제곱 평균 오차의 제곱근을 계산합니다. `y_test` 데이터 프레임을 사용하고 이를 목록 `y_actual`로 변환하여 예측 값과 비교합니다. `mean_squared_error` 함수는 두 개의 값 배열을 사용하고 두 배열 간의 평균 제곱 오차를 계산합니다. 결과의 제곱근을 구하면 y 변수(비용)와 동일한 단위로 오차가 나타납니다. 이는 오차 가중치를 매우 크게 적용하긴 하지만 대략적으로 실제 요금과 택시 요금 예측 간 차이를 나타냅니다.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



전체 `y_actual` 및 `y_predict` 데이터 세트를 사용하여 MAPE(절대 평균 백분율 오차)를 계산하려면 다음 코드를 실행합니다. 이 메트릭은 각 예측 및 실제 값 사이 절대값 차이를 계산하며 모든 차이를 합산합니다. 그런 다음, 실제 값의 합계에 대한 백분율로 해당 합산을 표시합니다.


```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

전체 데이터 세트(n=11748)를 기준으로 매우 작은 데이터 샘플을 사용한 점을 고려할 때 모델 정확도는 85%로 상당히 높으며, 택시 요금 가격 예측에서 RMSE(제곱 평균 오차의 제곱근)는 대략 +/- $4.00 오차에 해당합니다. 정확도를 개선할 가능성이 있는 다음 단계로, 이 Notebook의 두 번째 셀로 돌아가서 샘플 크기를 월간 레코드 2,000개에서 좀 더 높이고 더 많은 데이터를 사용하여 전체 실험을 다시 실행하여 모델을 다시 학습합니다.

## <a name="clean-up-resources"></a>리소스 정리

자신이 만든 리소스를 사용하지 않으려는 경우 요금이 발생하지 않도록 삭제하세요.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹**을 선택합니다.
1. 목록에서 만든 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다.
1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* 더 많은 코드 예제는 Azure Open Datasets [Notebook](https://github.com/Azure/OpenDatasetsNotebooks)을 참조하세요.
* Azure Machine Learning Service의 자동화된 기계 학습에 대한 자세한 내용은 [방법](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train)을 참조하세요.
