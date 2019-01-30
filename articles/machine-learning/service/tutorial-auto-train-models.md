---
title: '회귀 모델 자습서: 자동화된 ML'
titleSuffix: Azure Machine Learning service
description: 자동화된 Machine Learning을 사용하여 Machine Learning 모델을 만드는 방법에 대해 알아봅니다. Azure Machine Learning은 데이터 전처리, 알고리즘 선택 및 하이퍼 매개 변수 선택을 자동화된 방식으로 수행할 수 있습니다. 그런 다음, Azure Machine Learning Service를 사용하여 최종 모델을 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 97910241cb4f903deeeb9ff6971839530903efe2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823018"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>자습서: 자동화된 기계 학습을 사용하여 모델 빌드

이 자습서는 **2부로 구성된 자습서 시리즈 중 제2부**입니다. 이전 자습서에서는 [회귀 모델링을 위해 NYC 택시 데이터를 준비했습니다](tutorial-data-prep.md).

이제 Azure Machine Learning Service를 사용하여 모델 빌드를 시작할 준비가 되었습니다. 자습서의 이 파트에서는 준비된 데이터를 사용하여 택시 요금을 예측하는 회귀 모델을 자동으로 생성합니다. 서비스의 자동화된 Machine Learning 기능을 사용하여 Machine Learning 목표와 제약 조건을 정의합니다. 자동화된 Machine Learning 프로세스를 시작합니다. 그러면 알고리즘 선택 및 하이퍼 매개 변수 조정이 가능합니다. 자동화된 Machine Learning 기술은 사용자의 기준을 기반으로 최상의 모델을 발견할 때까지 알고리즘과 하이퍼 매개 변수의 여러 조합을 반복합니다.

![흐름 다이어그램](./media/tutorial-auto-train-models/flow2.png)

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * Python 환경 설정 및 SDK 패키지 가져오기
> * Azure Machine Learning Service 작업 영역 구성
> * 회귀 모델 자동 학습
> * 사용자 지정 매개 변수를 사용하여 로컬로 모델 실행
> * 결과 탐색
> * 최적 모델 등록

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](http://aka.ms/AMLFree)을 지금 사용해 보세요.

>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 1.0.0에서 테스트되었습니다.

## <a name="prerequisites"></a>필수 조건

> * [데이터 준비 자습서를 실행합니다](tutorial-data-prep.md).
> * 자동화된 Machine Learning이 구성된 환경. [Azure Notebooks](https://notebooks.azure.com/), 로컬 Python 환경 또는 Data Science Virtual Machine을 예로 들 수 있습니다. [자동화된 Machine Learning을 설정합니다](samples-notebooks.md).

## <a name="get-the-notebook"></a>Notebook 가져오기

사용자의 편의를 위해 이 자습서는 [Jupyter 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb)으로 제공됩니다. [Azure Notebooks](https://notebooks.azure.com/) 또는 사용자 고유의 Jupyter Notebook 서버에서 `regression-part2-automated-ml.ipynb` Notebook을 실행합니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>패키지 가져오기
이 자습서에 필요한 Python 패키지를 가져옵니다.


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging
import os
```

사용자 고유의 Python 환경에서 자습서를 수행하는 경우 필요한 패키지를 설치하려면 다음을 사용합니다.

```shell
pip install azureml-sdk[automl,notebooks] azureml-dataprep pandas scikit-learn matplotlib
```

## <a name="configure-workspace"></a>작업 영역 구성

기존 작업 영역에서 작업 영역 개체를 만듭니다. `Workspace`는 Azure 구독 및 리소스 정보를 허용하는 클래스입니다. 또한 클라우드 리소스를 만들어서 모델 실행을 모니터링하고 추적합니다.

`Workspace.from_config()`는 **aml_config/config.json** 파일을 읽고, 세부 정보를 `ws`라는 개체에 로드합니다.  `ws`는 이 자습서에서 나머지 코드에 사용됩니다.

작업 영역 개체가 생긴 후 실험에 사용할 이름을 지정합니다. 작업 영역을 사용하여 로컬 디렉터리를 만들고 등록합니다. 모든 실행에 대한 기록은 지정된 실험 및 [Azure Portal](https://portal.azure.com)에 기록됩니다.


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>데이터 탐색

이전 자습서에서 만든 데이터 흐름 개체를 사용합니다. 요약하자면 이 자습서의 1부는 NYC Taxi 데이터를 정리했으므로 기계 학습 모델에서 사용할 수 있습니다. 이제 데이터 세트에서 다양한 기능을 사용하고 자동화된 모델에서 기능과 택시 여정 간의 관계를 빌드하도록 허용합니다. 데이터 흐름을 열어 실행하고 결과를 검토합니다.


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

package_saved = dprep.Package.open(file_path)
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
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
      <th>평균</th>
      <th>표준 편차</th>
      <th>Variance</th>
      <th>왜곡도</th>
      <th>첨도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>공급업체</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>금요일</td>
      <td>수요일</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.90047</td>
      <td>2.69355</td>
      <td>9.72889</td>
      <td>16</td>
      <td>19.3713</td>
      <td>22.6974</td>
      <td>23</td>
      <td>23</td>
      <td>14.2731</td>
      <td>6.59242</td>
      <td>43.46</td>
      <td>-0.693723</td>
      <td>-0.570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99701</td>
      <td>4.95833</td>
      <td>14.1528</td>
      <td>29.3832</td>
      <td>44.6825</td>
      <td>56.4444</td>
      <td>58.9909</td>
      <td>59</td>
      <td>29.427</td>
      <td>17.4333</td>
      <td>303.921</td>
      <td>0.0120999</td>
      <td>-1.20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.28131</td>
      <td>5</td>
      <td>14.7832</td>
      <td>29.9293</td>
      <td>44.725</td>
      <td>56.7573</td>
      <td>59</td>
      <td>59</td>
      <td>29.7443</td>
      <td>17.3595</td>
      <td>301.351</td>
      <td>-0.0252399</td>
      <td>-1.19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>금요일</td>
      <td>수요일</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.57153</td>
      <td>2</td>
      <td>9.58795</td>
      <td>15.9994</td>
      <td>19.6184</td>
      <td>22.8317</td>
      <td>23</td>
      <td>23</td>
      <td>14.2105</td>
      <td>6.71093</td>
      <td>45.0365</td>
      <td>-0.687292</td>
      <td>-0.61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.44383</td>
      <td>4.84694</td>
      <td>14.1036</td>
      <td>28.8365</td>
      <td>44.3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29.2907</td>
      <td>17.4108</td>
      <td>303.136</td>
      <td>0.0222514</td>
      <td>-1.2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.07801</td>
      <td>5</td>
      <td>14.5751</td>
      <td>29.5972</td>
      <td>45.4649</td>
      <td>56.2729</td>
      <td>59</td>
      <td>59</td>
      <td>29.772</td>
      <td>17.5337</td>
      <td>307.429</td>
      <td>-0.0212575</td>
      <td>-1.226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
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
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0781</td>
      <td>-73.7459</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0578</td>
      <td>-73.9639</td>
      <td>-73.9656</td>
      <td>-73.9508</td>
      <td>-73.9255</td>
      <td>-73.8529</td>
      <td>-73.8302</td>
      <td>-73.8238</td>
      <td>-73.7697</td>
      <td>-73.9123</td>
      <td>0.0503757</td>
      <td>0.00253771</td>
      <td>0.352172</td>
      <td>-0.923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632</td>
      <td>40.7117</td>
      <td>40.7115</td>
      <td>40.7213</td>
      <td>40.7565</td>
      <td>40.8058</td>
      <td>40.8478</td>
      <td>40.8676</td>
      <td>40.8778</td>
      <td>40.7649</td>
      <td>0.0494674</td>
      <td>0.00244702</td>
      <td>0.205972</td>
      <td>-0.777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0775</td>
      <td>-73.9875</td>
      <td>-73.9882</td>
      <td>-73.9638</td>
      <td>-73.935</td>
      <td>-73.8755</td>
      <td>-73.8125</td>
      <td>-73.7759</td>
      <td>-73.7327</td>
      <td>-73.9202</td>
      <td>0.0584627</td>
      <td>0.00341789</td>
      <td>0.623622</td>
      <td>-0.262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5973</td>
      <td>40.6928</td>
      <td>40.6911</td>
      <td>40.7226</td>
      <td>40.7567</td>
      <td>40.7918</td>
      <td>40.8495</td>
      <td>40.868</td>
      <td>40.8787</td>
      <td>40.7583</td>
      <td>0.0517399</td>
      <td>0.00267701</td>
      <td>0.0390404</td>
      <td>-0.203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.39249</td>
      <td>1.83197</td>
      <td>3.3561</td>
      <td>0.763144</td>
      <td>-1.23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32.34</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0108744</td>
      <td>0.743898</td>
      <td>0.738194</td>
      <td>1.243</td>
      <td>2.40168</td>
      <td>4.74478</td>
      <td>10.5136</td>
      <td>14.9011</td>
      <td>21.8035</td>
      <td>3.5447</td>
      <td>3.2943</td>
      <td>10.8524</td>
      <td>1.91556</td>
      <td>4.99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.33837</td>
      <td>5.00491</td>
      <td>5</td>
      <td>6.93129</td>
      <td>10.524</td>
      <td>17.4811</td>
      <td>33.2343</td>
      <td>50.0093</td>
      <td>63.1753</td>
      <td>13.6843</td>
      <td>9.66571</td>
      <td>93.426</td>
      <td>1.78518</td>
      <td>4.13972</td>
    </tr>
  </tbody>
</table>

모델 생성을 위한 기능이 되도록 `dflow_x`에 열을 추가하여 실험용 데이터를 준비합니다. 예측 값인 **비용**이 되도록 `dflow_y`를 정의합니다.

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>학습 및 테스트 세트로 데이터 분할

이제 `sklearn` 라이브러리에서 `train_test_split` 함수를 사용하여 데이터를 학습 및 테스트 세트로 분할합니다. 이 함수는 데이터를 모델 학습용 x(**기능**) 데이터 세트 및 테스트용 y(**예측 값**) 데이터 세트로 분리합니다. `test_size` 매개 변수는 테스트에 할당할 데이터 백분율을 정의합니다. `random_state` 매개 변수는 학습-테스트 분할이 항상 결정적이 되도록 임의 생성기에 시드를 설정합니다.

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

이 단계의 목적은 데이터 요소에서 실제 정확도를 측정하기 위해 모델 학습에 사용되지 않은 완료된 모델을 테스트하는 것입니다. 즉, 잘 학습된 모델은 아직 확인되지 않은 데이터에서 정확한 예측을 수행할 수 있어야 합니다. 이제 모델을 위한 자동 학습에 필요한 패키지 및 데이터가 준비되었습니다.

## <a name="automatically-train-a-model"></a>자동으로 모델 학습

자동으로 모델을 학습하려면 다음 단계를 수행합니다.
1. 실험 실행을 위한 설정 정의 학습 데이터를 구성에 연결하고, 학습 프로세스를 제어하는 설정을 수정합니다.
1. 모델 튜닝을 위한 실험 제출 실험을 제출한 후 프로세스는 다른 기계 학습 알고리즘 및 하이퍼 매개 변수 설정을 반복하여 정의된 제약 조건을 준수합니다. 정확도 메트릭을 최적화하여 가장 적합한 모델을 선택합니다.

### <a name="define-settings-for-autogeneration-and-tuning"></a>자동 생성 및 튜닝을 위한 설정 정의

자동 생성 및 튜닝을 위해 실험 매개 변수 및 모델 설정을 정의합니다. [설정](how-to-configure-auto-train.md)의 전체 목록을 확인합니다. 이러한 기본 설정을 사용하여 실험을 제출하는 데 약 10-15분이 걸리지만 짧은 실행 시간을 원하는 경우 `iterations` 또는 `iteration_timeout_minutes` 중 하나를 줄입니다.


|자산| 이 자습서의 값 |설명|
|----|----|---|
|**iteration_timeout_minutes**|10|각 반복에 대한 분 단위 시간 제한 총 런타임을 줄이기 위해 이 값을 줄입니다.|
|**iterations**|30|반복 횟수입니다. 각 반복에서 데이터를 사용하여 새 기계 학습 모델을 학습합니다. 총 실행 시간에 영향을 주는 기본 값입니다.|
|**primary_metric**| spearman_correlation | 최적화하려는 메트릭입니다. 이 메트릭에 따라 최적화된 모델이 선택됩니다.|
|**preprocess**| True | **True**를 사용하여 실험은 입력 데이터를 전처리할 수 있습니다(누락 데이터 처리, 텍스트를 숫자로 변환 등).|
|**verbosity**| logging.INFO | 로깅 수준을 제어합니다.|
|**n_cross_validations**|5|유효성 검사 데이터가 지정되지 않은 경우 수행할 교차 유효성 검사 분할 수입니다.|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

`AutoMLConfig` 개체에 대한 매개 변수로 정의된 학습 설정을 사용합니다. 또한 학습 데이터 및 모델의 유형을 지정합니다. 이 경우 `regression`입니다.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>자동 회귀 모델 학습

실험을 시작하여 로컬로 실행합니다. 정의된 `automated_ml_config` 개체를 실험에 전달합니다. 실험하는 동안 진행률을 확인하려면 출력을 `True`로 설정합니다.


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

표시되는 출력은 실험이 실행됨에 따라 실시간으로 업데이트됩니다. 각 반복의 경우 모델 유형, 실행 지속 및 학습 정확도가 표시됩니다. 필드 `BEST`는 메트릭 유형에 따라 최적의 실행 학습 점수를 추적합니다.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>결과 탐색

Jupyter 위젯을 사용하거나 실험 기록을 검사하여 자동 학습 결과를 살펴봅니다.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>옵션 1: 결과를 보여 주는 Jupyter 위젯 추가

Jupyter Notebook을 사용하는 경우 이 Jupyter Notebook 위젯을 사용하여 모든 결과에 대한 그래프 및 테이블을 확인합니다.


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter 위젯 실행 세부 정보](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter 위젯 도표](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>옵션 2: Python에서 모든 실행 반복 가져오기 및 검사

또는 각 실험의 기록을 검색하고 각 반복 실행에 대한 개별 메트릭을 살펴볼 수 있습니다. 각 개별 모델 실행에 대해 RMSE(root_mean_squared_error)를 검사하여 대부분의 반복이 적절한 여백($3~4) 내에서 택시 요금을 예측하는 것을 알 수 있습니다.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.811037</td>
      <td>0.880553</td>
      <td>0.398582</td>
      <td>0.776040</td>
      <td>0.663869</td>
      <td>0.875911</td>
      <td>0.115632</td>
      <td>0.586905</td>
      <td>0.851911</td>
      <td>0.793964</td>
      <td>...</td>
      <td>0.850023</td>
      <td>0.883603</td>
      <td>0.883704</td>
      <td>0.880797</td>
      <td>0.881564</td>
      <td>0.883708</td>
      <td>0.881826</td>
      <td>0.585377</td>
      <td>0.883123</td>
      <td>0.886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2.189444</td>
      <td>1.500412</td>
      <td>5.480531</td>
      <td>2.626316</td>
      <td>2.973026</td>
      <td>1.550199</td>
      <td>6.383868</td>
      <td>4.414241</td>
      <td>1.743328</td>
      <td>2.294601</td>
      <td>...</td>
      <td>1.797402</td>
      <td>1.415815</td>
      <td>1.418167</td>
      <td>1.578617</td>
      <td>1.559427</td>
      <td>1.413042</td>
      <td>1.551698</td>
      <td>4.069196</td>
      <td>1.505795</td>
      <td>1.430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1.438417</td>
      <td>0.850899</td>
      <td>4.579662</td>
      <td>1.765210</td>
      <td>1.594600</td>
      <td>0.869883</td>
      <td>4.266450</td>
      <td>3.627355</td>
      <td>0.954992</td>
      <td>1.361014</td>
      <td>...</td>
      <td>0.973634</td>
      <td>0.774814</td>
      <td>0.797269</td>
      <td>1.147234</td>
      <td>1.116424</td>
      <td>0.783958</td>
      <td>1.098464</td>
      <td>2.709027</td>
      <td>1.003728</td>
      <td>0.851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.024908</td>
      <td>0.017070</td>
      <td>0.062350</td>
      <td>0.029878</td>
      <td>0.033823</td>
      <td>0.017636</td>
      <td>0.072626</td>
      <td>0.050219</td>
      <td>0.019833</td>
      <td>0.026105</td>
      <td>...</td>
      <td>0.020448</td>
      <td>0.016107</td>
      <td>0.016134</td>
      <td>0.017959</td>
      <td>0.017741</td>
      <td>0.016076</td>
      <td>0.017653</td>
      <td>0.046293</td>
      <td>0.017131</td>
      <td>0.016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.016364</td>
      <td>0.009680</td>
      <td>0.052101</td>
      <td>0.020082</td>
      <td>0.018141</td>
      <td>0.009896</td>
      <td>0.048538</td>
      <td>0.041267</td>
      <td>0.010865</td>
      <td>0.015484</td>
      <td>...</td>
      <td>0.011077</td>
      <td>0.008815</td>
      <td>0.009070</td>
      <td>0.013052</td>
      <td>0.012701</td>
      <td>0.008919</td>
      <td>0.012497</td>
      <td>0.030819</td>
      <td>0.011419</td>
      <td>0.009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.047968</td>
      <td>0.037882</td>
      <td>0.085572</td>
      <td>0.052282</td>
      <td>0.065809</td>
      <td>0.038664</td>
      <td>0.109401</td>
      <td>0.071104</td>
      <td>0.042294</td>
      <td>0.049967</td>
      <td>...</td>
      <td>0.042565</td>
      <td>0.037685</td>
      <td>0.037557</td>
      <td>0.037643</td>
      <td>0.037513</td>
      <td>0.037560</td>
      <td>0.037465</td>
      <td>0.072077</td>
      <td>0.037249</td>
      <td>0.036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.055353</td>
      <td>0.045000</td>
      <td>0.110219</td>
      <td>0.065633</td>
      <td>0.063589</td>
      <td>0.044412</td>
      <td>0.123433</td>
      <td>0.092312</td>
      <td>0.046130</td>
      <td>0.055243</td>
      <td>...</td>
      <td>0.046540</td>
      <td>0.041804</td>
      <td>0.041771</td>
      <td>0.045175</td>
      <td>0.044628</td>
      <td>0.041617</td>
      <td>0.044405</td>
      <td>0.079651</td>
      <td>0.042799</td>
      <td>0.041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.810900</td>
      <td>0.880328</td>
      <td>0.398076</td>
      <td>0.775957</td>
      <td>0.642812</td>
      <td>0.875719</td>
      <td>0.021603</td>
      <td>0.586514</td>
      <td>0.851767</td>
      <td>0.793671</td>
      <td>...</td>
      <td>0.849809</td>
      <td>0.880142</td>
      <td>0.880952</td>
      <td>0.880586</td>
      <td>0.881347</td>
      <td>0.880887</td>
      <td>0.881613</td>
      <td>0.548121</td>
      <td>0.882883</td>
      <td>0.886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4.216362</td>
      <td>3.329810</td>
      <td>7.521765</td>
      <td>4.595604</td>
      <td>5.784601</td>
      <td>3.398540</td>
      <td>9.616354</td>
      <td>6.250011</td>
      <td>3.717661</td>
      <td>4.392072</td>
      <td>...</td>
      <td>3.741447</td>
      <td>3.312533</td>
      <td>3.301242</td>
      <td>3.308795</td>
      <td>3.297389</td>
      <td>3.301485</td>
      <td>3.293182</td>
      <td>6.335581</td>
      <td>3.274209</td>
      <td>3.227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.243184</td>
      <td>0.197702</td>
      <td>0.484227</td>
      <td>0.288349</td>
      <td>0.279367</td>
      <td>0.195116</td>
      <td>0.542281</td>
      <td>0.405559</td>
      <td>0.202666</td>
      <td>0.242702</td>
      <td>...</td>
      <td>0.204464</td>
      <td>0.183658</td>
      <td>0.183514</td>
      <td>0.198468</td>
      <td>0.196067</td>
      <td>0.182836</td>
      <td>0.195087</td>
      <td>0.349935</td>
      <td>0.188031</td>
      <td>0.182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.857965</td>
      <td>0.914703</td>
      <td>0.939846</td>
      <td>0.956159</td>
      <td>0.828187</td>
      <td>0.942069</td>
      <td>0.952581</td>
      <td>0.935477</td>
      <td>...</td>
      <td>0.951287</td>
      <td>0.960335</td>
      <td>0.960195</td>
      <td>0.960279</td>
      <td>0.960288</td>
      <td>0.960323</td>
      <td>0.960161</td>
      <td>0.941254</td>
      <td>0.960293</td>
      <td>0.962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>...</td>
      <td>0.960303</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.962158</td>
    </tr>
  </tbody>
</table>
<p>12행 × 30열</p>
</div>

## <a name="retrieve-the-best-model"></a>최적 모델 검색

해당 반복에서 최적의 파이프라인을 선택합니다. `automl_classifier`에 대한 `get_output` 메서드는 마지막 맞춤 호출에 대한 최적의 실행 및 맞춤 모델을 반환합니다. `get_output`에 대한 오버로드를 사용하여 모든 기록된 메트릭 또는 특정 반복에 대한 최적의 실행 및 맞춤 모델을 검색할 수 있습니다.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>최적 모델 정확도 테스트

최적 모델을 사용하여 테스트 데이터 세트에서 예측을 실행하여 택시 요금을 예측합니다. `predict` 함수는 최적 모델을 사용하고 `x_test` 데이터 세트에서 y(**출장 비용**) 값을 예측합니다. `y_predict`에서 첫 10개의 예측 비용 값을 인쇄합니다.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

실제 비용 값과 비교하여 예측 비용 값을 시각화하는 산점도를 만듭니다. 다음 코드에서는 `distance` 기능을 x-축으로, 여행 `cost`를 y-축으로 사용합니다. 각 여행 거리 값에서 예측 비용의 차이를 비교하기 위해 처음 100개의 예측 및 실제 비용 값이 별도의 시리즈로 만들어집니다. 도표를 살펴보면 거리/비용 관계가 거의 선형이고, 대부분의 경우 예측 비용 값이 동일한 여행 거리에 대한 실제 비용 값에 매우 가깝다는 것을 알 수 있습니다.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![예측 산점도](./media/tutorial-auto-train-models/automl-scatter-plot.png)

결과의 `root mean squared error`를 계산합니다. `y_test` 데이터 프레임을 사용합니다. 이를 목록으로 변환하여 예측 값과 비교합니다. `mean_squared_error` 함수는 두 개의 값 배열을 사용하고 두 배열 간의 평균 제곱 오차를 계산합니다. 결과의 제곱근을 구하면 y 변수(**비용**)와 동일한 단위에서 오류가 나타납니다. 이는 대략적으로 실제 요금과 택시 요금 예측 간 차이를 나타냅니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

전체 `y_actual` 및 `y_predict` 데이터 세트를 사용하여 MAPE(절대 평균 백분율 오차)를 계산하려면 다음 코드를 실행합니다. 이 메트릭은 각 예측 및 실제 값 사이 절대값 차이를 계산하며 모든 차이를 합산합니다. 그런 다음, 실제 값의 합계에 대한 백분율로 해당 합산을 표현합니다.

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
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

최종 예측 정확도 메트릭에서 모델은 데이터 세트의 기능에서 택시 요금을 예측하는 데 적합하다는 것을 알 수 있습니다(일반적으로 +- $3.00 내). 기존의 Machine Learning 모델 개발 프로세스는 리소스가 상당히 많이 필요하며, 수십 가지 모델을 실행하고 결과를 비교하는 데 많은 도메인 지식과 시간이 필요합니다. 자동화된 기계 학습을 사용하는 것은 시나리오에 대한 다른 여러 모델을 신속하게 테스트하는 좋은 방법입니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 자동화된 Machine Learning 자습서에서 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 작업 영역을 구성하고 실험을 위해 데이터를 준비했습니다.
> * 사용자 지정 매개 변수를 통해 로컬로 자동화된 회귀 모델 사용을 학습했습니다.
> * 학습 결과를 탐색하고 검토했습니다.
> * 최적 모델을 등록했습니다.

Azure Machine Learning을 사용하여 [모델을 배포합니다](tutorial-deploy-models-with-aml.md).
