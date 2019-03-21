---
title: '회귀 모델 자습서: 데이터 준비'
titleSuffix: Azure Machine Learning service
description: 이 자습서의 1부에서는 Azure Machine Learning SDK를 사용하여 회귀 모델링을 위해 Python으로 데이터를 준비하는 방법을 배웁니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 02/04/2019
ms.custom: seodec18
ms.openlocfilehash: ca49cb65331373832b00b8489d731d51e8a8e004
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182917"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>자습서: 회귀 모델링을 위한 데이터 준비

이 자습서에서는 [Python용 Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)를 사용하여 회귀 모델링을 위해 데이터를 준비하는 방법을 배웁니다. 다양한 변환을 실행하여 두 개의 서로 다른 NYC 택시 데이터 세트를 필터링하고 결합합니다.

이 자습서는 **2부로 구성된 자습서 시리즈 중 제1부**입니다. 이 자습서 시리즈를 마치면 데이터 기능에 대해 모델을 학습하여 택시 운행 비용을 예측할 수 있습니다. 이러한 기능으로는 날짜 및 시간, 승객 수, 위치 선택이 포함됩니다.

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Python 환경을 설정하고 패키지를 가져옵니다.
> * 필드 이름이 다른 두 개의 데이터 세트를 로드합니다.
> * 데이터를 정리하여 잘못된 부분을 제거합니다.
> * 새 기능을 만드는 인텔리전트 변환을 사용하여 데이터를 변환합니다.
> * 회귀 모델에서 사용할 데이터 흐름 개체를 저장합니다.

## <a name="prerequisites"></a>필수 조건

[개발 환경 설정](#start)으로 건너뛰어 Notebook 단계를 읽어보거나, 아래 지침에 따라 Notebook을 가져와서 Azure Notebooks 또는 사용자 고유의 Notebook 서버에서 실행합니다. Notebook을 실행하려면 다음 항목이 필요합니다.

* 다음 요소가 설치된 Python 3.6 Notebook 서버:
    * Python용 Azure Machine Learning Data Prep SDK
* 자습서 Notebook

아래 섹션 중 하나에서 이러한 필수 구성 요소를 모두 가져옵니다.

* [Azure Notebooks](#azure) 사용
* [사용자 고유의 Notebook 서버](#server) 사용

### <a name="azure"></a>Azure Notebooks 사용: 클라우드의 무료 Jupyter Notebook

Azure Notebooks로 시작하는 것이 쉽습니다! Azure Machine Learning Data Prep SDK는 [Azure Notebooks](https://notebooks.azure.com/)에 이미 설치 및 구성되어 있습니다. 설치 및 향후 업데이트를 Azure 서비스를 통해 자동으로 관리합니다.

아래 단계를 완료한 후에는 **시작** 프로젝트의 **tutorials/regression-part1-data-prep.ipynb** Notebook을 실행합니다.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>사용자 고유의 Jupyter Notebook 서버 사용

사용자 컴퓨터에 로컬 Jupyter Notebook 서버를 만들려면 이러한 단계를 사용합니다.  이러한 단계를 완료한 후에는 **tutorials/regression-part1-data-prep.ipynb** Notebook을 실행합니다.

1. [Azure Machine Learning Python 빠른 시작](quickstart-create-workspace-with-python.md)을 완료하여 Miniconda 환경을 만듭니다.  원하는 경우 **작업 영역 만들기** 섹션을 건너뛰어도 되지만 작업 영역은 이 자습서 시리즈의 [2부](tutorial-auto-train-models.md)에서 필요합니다.
1. `pip install azureml-dataprep`를 사용하여 사용 환경에 Data Prep SDK를 설치합니다.
1. [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 복제합니다.

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 복제된 디렉터리에서 노트북 서버를 시작합니다.

    ```shell
    jupyter notebook

## <a name="start"></a>Set up your development environment

All the setup for your development work can be accomplished in a Python notebook. Setup includes the following actions:

* Install the SDK
* Import Python packages

### Install and import packages

Use the following to install necessary packages if you don't already have them.

```shell
pip install azureml-dataprep
```

SDK를 가져옵니다.

```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>데이터 로드

두 개의 서로 다른 NYC 택시 데이터 세트를 데이터 흐름 개체에 다운로드합니다. 두 데이터 세트의 필드는 서로 조금씩 다릅니다. `auto_read_file()` 메서드는 입력 파일 형식을 자동으로 인식합니다.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

`Dataflow` 개체는 데이터 프레임과 비슷하며, 데이터에 대한 일련의 지연 평가된 변경할 수 없는 작업을 나타냅니다. 사용 가능한 다른 변환 및 필터링 메서드를 호출하여 작업을 추가할 수 있습니다. `Dataflow`에 작업을 추가한 결과는 항상 새로운 `Dataflow` 개체입니다.

## <a name="cleanse-data"></a>데이터 정리

이제 모든 데이터 흐름에 적용할 바로 가기 변환으로 일부 변수를 채웁니다. `drop_if_all_null` 변수는 모든 필드가 Null인 레코드를 삭제하는 데 사용됩니다. `useful_columns` 변수는 각 데이터 흐름에 보존되는 열 설명의 배열을 보관합니다.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

먼저 녹색 택시 데이터를 작업하여 노란색 택시 데이터와 결합할 수 있는 유효한 모양으로 가져옵니다. 만들어 둔 바로 가기 변환 변수를 사용하여 `replace_na()`, `drop_nulls()` 및 `keep_columns()` 함수를 호출합니다. 또한 데이터 프레임의 모든 열 이름을 `useful_columns` 변수의 이름과 일치하도록 바꿉니다.


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

노란색 택시 데이터에 대해 동일한 변환 단계를 실행합니다. 이러한 함수는 데이터 세트에서 null 데이터가 제거되도록 보장하며, 이는 기계 학습 모델의 정확도를 높이는 데 도움이 됩니다.

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

녹색 택시 데이터에서 `append_rows()` 함수를 호출하여 노란색 택시 데이터를 추가합니다. 새로 조합된 데이터 프레임이 생성됩니다.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>형식 및 필터 변환

승차 및 하차 좌표 요약 통계를 조사하여 데이터가 어떤 식으로 분산되는지 확인합니다. 먼저 위도 및 경도 필드를 10진 형식으로 변경하는 `TypeConverter` 개체를 정의합니다. 다음으로, 출력을 위도 및 경도 필드로만 제한하는 `keep_columns()` 함수를 호출한 다음, `get_profile()` 함수를 호출합니다. 이러한 함수 호출은 누락된 또는 범위를 벗어나는 좌표를 쉽게 평가할 수 있도록 경도/위도 필드만 표시하는 요약된 데이터 흐름 보기를 생성합니다.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>개수</th>
      <th>누락된 수</th>
      <th>누락되지 않은 수</th>
      <th>누락 백분율</th>
      <th>오류 수</th>
      <th>비어 있는 수</th>
      <th>0.1% 분위수</th>
      <th>1% 분위수</th>
      <th>5% 분위수</th>
      <th>25% 분위수</th>
      <th>50% 분위수</th>
      <th>75% 분위수</th>
      <th>95% 분위수</th>
      <th>99% 분위수</th>
      <th>99.9% 분위수</th>
      <th>표준 편차</th>
      <th>평균</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



요약 통계 출력을 보면 누락된 좌표와 뉴욕시가 아닌 좌표가 있습니다(이는 주관적 분석에서 결정됨). 도시 경계 밖에 있는 위치의 좌표를 필터링합니다. `filter()` 함수 내에서 열 필터 명령을 연결하고 각 필드의 최소 및 최대 경계를 정의합니다. 그런 다음, `get_profile()` 함수를 다시 호출하여 변환을 확인합니다.


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>개수</th>
      <th>누락된 수</th>
      <th>누락되지 않은 수</th>
      <th>누락 백분율</th>
      <th>오류 수</th>
      <th>비어 있는 수</th>
      <th>0.1% 분위수</th>
      <th>1% 분위수</th>
      <th>5% 분위수</th>
      <th>25% 분위수</th>
      <th>50% 분위수</th>
      <th>75% 분위수</th>
      <th>95% 분위수</th>
      <th>99% 분위수</th>
      <th>99.9% 분위수</th>
      <th>표준 편차</th>
      <th>평균</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>열 분할 및 이름 바꾸기

`store_forward` 열에 대한 데이터 프로필을 살펴봅니다. 이 필드는 택시가 운행 후 서버에 연결되지 않아서 운행 데이터를 메모리에 저장해야 하고, 나중에 서버에 연결되면 데이터를 전송해야 하는 `Y`인 부울 플래그입니다.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>개수</th>
      <th>누락된 수</th>
      <th>누락되지 않은 수</th>
      <th>누락 백분율</th>
      <th>오류 수</th>
      <th>비어 있는 수</th>
      <th>0.1% 분위수</th>
      <th>1% 분위수</th>
      <th>5% 분위수</th>
      <th>25% 분위수</th>
      <th>50% 분위수</th>
      <th>75% 분위수</th>
      <th>95% 분위수</th>
      <th>99% 분위수</th>
      <th>99.9% 분위수</th>
      <th>표준 편차</th>
      <th>평균</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



`store_forward` 열의 데이터 프로필 출력을 보면 데이터가 일치하지 않으며 값이 누락되거나 Null 값이 있음을 알 수 있습니다. `replace()` 및 `fill_nulls()` 함수를 사용하여 이러한 값을 문자열 "N"으로 바꿉니다.


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

`distance` 필드에서 `replace` 함수를 실행합니다. 그러면 이 함수는 레이블이 `.00`으로 잘못 지정된 거리 값의 형식을 다시 지정하고 Null을 0으로 채웁니다. `distance` 필드를 숫자 형식으로 변환합니다. 이처럼 잘못된 데이터 요소는 택시의 데이터 컬렉션 시스템 이상이 그 원인일 수 있습니다.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

승차 및 하차 날짜/시간 값을 각각 날짜 및 시간 열로 분할합니다. `split_column_by_example()` 함수를 사용하여 분할합니다. 이 예에서는 `split_column_by_example()` 함수의 선택적 매개 변수 `example`이 생략되었습니다. 따라서 함수가 데이터를 기반으로 분할 위치를 자동으로 결정합니다.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

`split_column_by_example()` 함수에서 생성한 열 이름을 의미 있는 이름으로 바꿉니다.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

모든 정리 단계가 끝난 후 `get_profile()` 함수를 호출하여 전체 요약 통계를 봅니다.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>데이터 변환

승차 및 하차 날짜를 요일, 월간 일자, 월 값으로 추가 분할합니다. 요일 값을 가져오려면 `derive_column_by_example()` 함수를 사용합니다. 이 함수는 입력 데이터와 원하는 출력을 정의하는 예제 개체의 배열 매개 변수를 사용합니다. 이 함수는 사용자가 원하는 변환을 자동으로 확인합니다. 승차 및 하차 시간 열의 경우 예제 매개 변수 없이 `split_column_by_example()` 함수를 사용하여 시간을 시, 분, 초로 분할합니다.

새 기능을 생성한 후에는 새로 생성된 기능이 기본 기능이 되므로 `drop_columns()` 함수를 사용하여 원래 필드를 삭제합니다. 의미 있는 설명을 사용하도록 나머지 필드의 이름을 바꿉니다.

이 방식으로 데이터를 변환하여 새로운 시간 기반 기능을 만들면 기계 학습 모델 정확도가 향상됩니다. 예를 들어 평일에 대한 새 기능을 생성하면 평일과 택시 요금 간의 관계를 설정하는 데 도움이 되며, 특정 요일에는 수요가 많아 요금이 더 비싼 경우가 자주 있습니다.


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
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
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>목요일</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>목요일</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>목요일</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>목요일</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>화요일</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>화요일</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>화요일</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>화요일</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>화요일</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>화요일</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

데이터를 보면 파생된 변환에서 생성된 승/하차 날짜 및 시간 구성 요소가 정확한 것을 알 수 있습니다. `pickup_datetime` 및 `dropoff_datetime` 열은 더 이상 필요 없으므로 삭제합니다(시간, 분 및 초 같은 세밀한 시간 기능은 모델 학습에 유용).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

형식 유추 기능을 사용하여 각 필드의 데이터 형식을 자동으로 확인하고 추론 결과를 표시합니다.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

`type_infer`의 결과 출력은 다음과 같습니다.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

데이터와 비교해 보면 유추 결과가 올바른 것 같습니다. 이제 형식 변환을 데이터 흐름에 적용합니다.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

데이터 흐름을 패키징하기 전에 데이터 세트에서 두 개의 최종 필터를 실행합니다. 잘못 캡처된 데이터 요소를 제거하려면 `cost` 및 `distance` 변수 값이 모두 0보다 큰 레코드에서 데이터 흐름을 필터링합니다. 이 단계를 수행하면 기계 학습 모델 정확도가 대폭 향상됩니다. 비용 또는 거리가 0인 데이터 요소는 예측 정확도를 떨어트리는 중대한 이상값을 나타내기 때문입니다.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

기계 학습 모델에 사용할 수 있도록 완전히 변환 및 준비된 데이터 흐름 개체가 생겼습니다. SDK에는 다음 코드처럼 사용되는 개체 serialization 기능이 포함되어 있습니다.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>리소스 정리

자습서의 2부를 계속 진행하려면 현재 디렉터리에 **dflows.dprep** 파일이 있어야 합니다.

2부를 진행하지 않으려면 현재 디렉터리에서 **dflows.dprep** 파일을 삭제하세요. 로컬로 실행하든 [Azure Notebooks](https://notebooks.azure.com/)에서 실행하든, 이 파일을 삭제하세요.

## <a name="next-steps"></a>다음 단계

이 자습서의 1부에서 다음을 수행했습니다.

> [!div class="checklist"]
> * 개발 환경 설정
> * 데이터 세트 로드 및 정리
> * 스마트 변환을 사용하여 예제를 기반으로 논리 예측
> * 기계 학습 교육을 위해 데이터 세트 병합 및 패키징

자습서의 2부에서 학습 데이터를 사용할 준비가 완료되었습니다.

> [!div class="nextstepaction"]
> [자습서(2부): 회귀 모델 학습](tutorial-auto-train-models.md)
