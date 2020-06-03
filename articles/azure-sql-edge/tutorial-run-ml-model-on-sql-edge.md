---
title: ONNX를 사용하여 Azure SQL Edge에 ML 모델 배포
description: 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 Azure SQL Edge 자습서 중 3부에서는 SQL Edge에서 ONNX 기계 학습 모델을 실행합니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 02850b8780e70d402cff2b8fd301c2f79cd6c55e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235057"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ONNX를 사용하여 Azure SQL Edge에 ML 모델 배포 

Azure SQL Edge에서 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 자습서 중 3부에서는 다음을 수행합니다.

1. Azure Data Studio를 사용하여 Azure SQL Edge 인스턴스의 SQL Database에 연결합니다.
2. Azure SQL Edge에서 ONNX로 철광석 불순물을 예측합니다.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Azure SQL Edge 인스턴스의 SQL Database에 연결

1. Azure Data Studio를 엽니다.

2. **시작** 탭에서 다음 정보를 사용하여 새 연결을 시작합니다.

   |_필드_|_값_|
   |-------|-------|
   |연결 형식| Microsoft SQL Server|
   |제공|이 데모에 대해 생성된 VM에서 언급된 공용 IP 주소|
   |사용자 이름|sa|
   |암호|Azure SQL Edge 인스턴스를 만드는 동안 사용된 강력한 암호입니다.|
   |데이터베이스|기본값|
   |서버 그룹|기본값|
   |이름(선택 사항)|필요에 따라 이름을 입력합니다.|

3. **연결**

4. **파일** 섹션에서 새 Notebook을 열거나 키보드 바로 가기 키 Alt + Windows + N을 사용합니다. 

5. 커널을 Python 3으로 설정합니다.

## <a name="predict-iron-ore-impurities-with-onnx"></a>ONNX로 철광석 불순물 예측

Azure Data Studio Notebook에 다음 Python 코드를 입력하고 실행합니다.

1. 먼저 필요한 패키지를 설치하고 가져옵니다.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. 회귀 실험에 대한 Azure AutoML 작업 영역 및 AutoML 실험 구성을 정의합니다.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. 데이터 세트를 Panda 프레임으로 가져옵니다. 모델 학습의 목적에 맞게 Kaggle에서 [마이닝 프로세스의 품질 예측](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) 학습 데이터 세트를 사용합니다. 데이터 파일을 다운로드하고 개발 컴퓨터에 로컬로 저장합니다. 이 데이터를 사용하여 철 정광의 불순물 양을 예측합니다.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. 데이터를 분석하여 왜곡도를 파악합니다. 이 과정에서 데이터 프레임의 각 열에 대한 분포와 기울이기 정보를 확인합니다.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. 데이터의 왜곡도 수준을 확인하고 수정합니다.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. 예측 기능과 다른 기능의 상관 관계를 확인합니다. 상관 관계가 높지 않으면 해당 기능을 제거합니다.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. AzureML 실험을 시작하여 최상의 알고리즘을 찾아 학습합니다. 이 경우 NRMSE(정규화된 제곱 평균 오차)의 기본 메트릭으로 모든 회귀 알고리즘을 사용하여 테스트합니다. 자세한 내용은 [Azure ML 실험 기본 메트릭](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric)을 참조하세요. 다음 코드에서는 ML 실험의 로컬 실행을 시작합니다.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. 로컬 점수 매기기를 위해 Azure SQL Edge 데이터베이스에 모델을 로드합니다.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. 마지막으로, Azure SQL Edge 모델을 사용하여 학습된 모델을 이용한 예측을 수행합니다.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Python을 사용하여 철 피드, 날짜/시간 및 실리카 피드에 대한 예측된 실리카 비율 차트를 만듭니다.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>다음 단계

Azure SQL Edge에서의 ONNX 모델 사용에 대한 자세한 내용은 [SQL Edge(미리 보기)에서 ONNX를 통한 기계 학습 및 AI](onnx-overview.md)를 참조하세요.
