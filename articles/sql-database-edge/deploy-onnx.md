---
title: SQL 데이터베이스 에지 미리 보기에서 ONNX로 배포 및 예측
description: 모델을 학습하고, ONNX로 변환하고, Azure SQL Database Edge 미리 보기에 배포한 다음 업로드된 ONNX 모델을 사용하여 데이터에 대한 네이티브 예측을 실행하는 방법을 알아봅니다.
keywords: SQL 데이터베이스 에지 배포
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: aff9346595d3b8985d3558658af32d05f88c0554
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365454"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-sql-database-edge-preview"></a>SQL 데이터베이스 에지 미리 보기에서 ONNX 모델로 배포 및 예측

이 빠른 시작에서는 모델을 학습하고, ONNX로 변환하고, Azure SQL Database Edge 미리 보기에 배포한 다음 업로드된 ONNX 모델을 사용하여 데이터에 대한 네이티브 예측을 실행하는 방법을 알아봅니다. 자세한 내용은 [SQL 데이터베이스 에지 미리 보기에서 ONNX를 사용하여 기계 학습 및 AI를](onnx-overview.md)참조하십시오.

이 빠른 시작은 **scikit 학습을** 기반으로 하며 [보스턴 하우징 데이터 집합을](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)사용합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure SQL Database Edge 모듈을 배포하지 않은 경우 [Azure 포털을 사용하여 SQL Database Edge 미리 보기 배포](deploy-portal.md)단계를 따릅니다.

* [Azure 데이터 스튜디오 설치.](https://docs.microsoft.com/sql/azure-data-studio/download)

* Azure Data Studio를 열고 다음 단계를 수행하여 이 빠른 시작에 필요한 패키지를 설치합니다.

    1. 파이썬 3 커널에 연결된 [새 노트북을](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks) 엽니다. 
    1. **패키지 관리** 및 **새 추가**에서 를 클릭하고 **scikit-learn을**검색하고 scikit-learn 패키지를 설치합니다. 
    1. 또한, **설치 도구,** **numpy,** **onnxmltools,** **onnxruntime,** **skl2onnx,** **pyodbc**, 및 **sqlalchemy** 패키지를 설치합니다.
    
* 아래의 각 스크립트 부분에 대해 Azure Data Studio 전자 필기장의 셀에 입력하고 셀을 실행합니다.

## <a name="train-a-pipeline"></a>파이프라인 교육

피처를 사용하여 집중앙값을 예측하도록 데이터 집합을 분할합니다.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])

# x contains all predictors (features)
x = df.drop(['MEDV'], axis = 1)

# y is what we are trying to predict - the median value
y = df.iloc[:,-1]

# Split the data frame into features and target
x_train = df.drop(['MEDV'], axis = 1)
y_train = df.iloc[:,-1]

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**출력**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

선형 회귀 모델을 학습하는 파이프라인을 만듭니다. 다른 회귀 모델을 사용할 수도 있습니다.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

모델의 정확도를 확인한 다음 R2 점수와 평균 제곱 오차를 계산합니다.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**출력**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>모델을 ONNX로 변환

데이터 형식을 지원되는 SQL 데이터 유형으로 변환합니다. 이 변환은 다른 데이터 프레임에도 필요합니다.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

을 `skl2onnx`사용하여 Linear Regression 모델을 ONNX 형식으로 변환하고 로컬로 저장합니다.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>ONNX 모델 테스트

모델을 ONNX 형식으로 변환한 후 모델의 점수를 매기면 성능이 거의 저하되지 않습니다.

> [!NOTE]
> ONNX 런타임은 두 배 대신 부동 소수를 사용하므로 작은 불일치가 발생할 수 있습니다.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**출력**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>ONNX 모델 삽입

모델을 Azure SQL Database Edge에 `models` 데이터베이스의 `onnx`테이블에 저장합니다. 연결 문자열에서 서버 **주소,** **사용자 이름**및 **암호를**지정합니다.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>데이터 로드

Azure SQL 데이터베이스 가장자리에 데이터를 로드합니다.

먼저 두 개의 테이블, **피처** 및 **대상을**만들어 보스턴 하우징 데이터 집합의 하위 집합을 저장합니다.

* **피처에는** 대상, 중앙값을 예측하는 데 사용되는 모든 데이터가 포함됩니다. 
* **대상에는** 데이터 집합의 각 레코드에 대한 중앙값이 포함됩니다. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

`sqlalchemy` 마지막으로 `x_train` 테이블과 `y_train` 팬더 데이터 프레임을 각각 `features` 삽입하고, `target` 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

이제 데이터베이스에서 데이터를 볼 수 있습니다.

## <a name="run-predict-using-the-onnx-model"></a>ONNX 모델을 사용하여 예측 실행

Azure SQL Database Edge의 모델을 사용하여 업로드된 ONNX 모델을 사용하여 데이터에 대한 네이티브 PREDICT를 실행합니다.

> [!NOTE]
> 나머지 셀을 실행하려면 노트북 커널을 SQL로 변경합니다.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input) WITH (variable1 FLOAT) AS p
```

## <a name="next-steps"></a>다음 단계

* [SQL 데이터베이스 에지에서 ONNX를 사용하여 기계 학습 및 AI](onnx-overview.md)
