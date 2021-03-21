---
title: Python에서 ML 모델의 공평 평가 (미리 보기)
titleSuffix: Azure Machine Learning
description: Fairlearn 및 Azure Machine Learning Python SDK를 사용 하 여 기계 학습 모델의 공평을 평가 하 고 완화 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 322b036fee840db58ed610795155af6c9e1320cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366975"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Fairlearn 오픈 소스 패키지와 함께 Azure Machine Learning를 사용 하 여 ML 모델의 공평을 평가 (미리 보기)

이 방법 가이드에서는 다음 작업을 수행 하는 Azure Machine Learning [Fairlearn](https://fairlearn.github.io/) 오픈 소스 Python 패키지를 사용 하는 방법을 배웁니다.

* 모델 예측의 공평을 평가 합니다. 기계 학습에 대 한 자세한 내용은 [machine learning의 공평 문서](concept-fairness-ml.md)를 참조 하세요.
* Azure Machine Learning studio에서 공평 하 게 평가 통찰력을 업로드, 나열 및 다운로드 합니다.
* Azure Machine Learning studio의 공평 평가 대시보드를 참조 하 여 모델과 상호 작용 하는 모델과 상호 작용 합니다.

>[!NOTE]
> 공평 평가는 전적으로 기술적인 연습이 아닙니다. **이 패키지는 기계 학습 모델의 공평을 평가 하는 데 도움이 될 수 있지만 모델이 수행 하는 방법에 대해서만 구성 하 고 결정을 내릴 수 있습니다.**  이 패키지는 양적 메트릭을 식별 하 여 공평 하 게 평가 하는 데 도움이 되지만 기계 학습 모델의 개발자는 모델의 공평도를 평가 하는 질적 분석을 수행 해야 합니다.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning 공평 SDK 

Azure Machine Learning 공평 SDK는 `azureml-contrib-fairness` Azure Machine Learning 내에서 오픈 소스 Python 패키지 [Fairlearn](http://fairlearn.github.io)를 통합 합니다. Azure Machine Learning 내에서 Fairlearn의 통합에 대해 자세히 알아보려면 이러한 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)을 확인 하세요. Fairlearn에 대 한 자세한 내용은 [예제 가이드](https://fairlearn.github.io/master/auto_examples/) 및 [샘플 노트북](https://github.com/fairlearn/fairlearn/tree/master/notebooks)을 참조 하세요. 

다음 명령을 사용 하 여 및 패키지를 설치 합니다 `azureml-contrib-fairness` `fairlearn` .
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
이후 버전의 Fairlearn는 다음 예제 코드 에서도 작동 합니다.



## <a name="upload-fairness-insights-for-a-single-model"></a>단일 모델에 대 한 공평 정보 업로드

다음 예에서는 공평 패키지를 사용 하는 방법을 보여 줍니다. Azure Machine Learning에 모델 공평 정보를 업로드 하 고 Azure Machine Learning studio의 공평 평가 대시보드를 확인 합니다.

1. Jupyter Notebook에서 샘플 모델을 학습 합니다. 

    데이터 집합에 대해 잘 알려진 성인 인구 조사 데이터 집합을 사용 합니다 .이 데이터 집합은 OpenML에서 가져옵니다. 개인이 이전 대출을 다시 지불 했는지 여부를 나타내는 레이블에 대출 결정 문제가 있는 것으로 가정 합니다. 이전에 보이지 않는 사람이 대출을 다시 지불할 지를 예측 하는 모델을 학습 합니다. 이러한 모델은 대출 결정을 내리는 데 사용 될 수 있습니다.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Azure Machine Learning에 로그인 하 여 모델을 등록 합니다.
   
    공평 대시보드는 등록 된 모델 또는 등록 되지 않은 모델과 통합할 수 있습니다. 다음 단계를 사용 하 여 Azure Machine Learning에 모델을 등록 합니다.
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. 공평 메트릭을 미리 계산 합니다.

    Fairlearn의 패키지를 사용 하 여 대시보드 사전을 만듭니다 `metrics` . 이 `_create_group_metric_set` 메서드에는 중요 한 기능이 사전으로 전달 되어 이름을 사용할 수 있도록 하는 점을 제외 하 고, 대시보드 생성자와 유사한 인수가 있습니다. 또한이 메서드를 호출할 때 예측 유형 (이 경우 이진 분류)을 지정 해야 합니다.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 사전 계산 공평 메트릭을 업로드 합니다.
    
    이제 패키지를 가져와서 `azureml.contrib.fairness` 업로드를 수행 합니다.

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    실험을 만들고 실행 한 다음 대시보드를 업로드 합니다.
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Azure Machine Learning studio에서 공평 대시보드 확인

    이전 단계를 완료 한 경우 (Azure Machine Learning에 생성 된 공평 통찰력 업로드) [Azure Machine Learning studio](https://ml.azure.com)에서 공평 한 대시보드를 볼 수 있습니다. 이 대시보드는 Fairlearn에서 제공 하는 것과 동일한 시각화 대시보드 이며,이를 통해 중요 한 기능의 하위 그룹 (예: 남성 및 여성) 간에 disparities을 분석할 수 있습니다.
    다음 경로 중 하나를 따라 Azure Machine Learning studio에서 시각화 대시보드에 액세스 합니다.

    * **실험 창 (미리 보기)**
    1. 왼쪽 창에서 **실험** 을 선택 하 여 Azure Machine Learning에서 실행 한 실험의 목록을 확인 합니다.
    1. 특정 실험을 선택 하 여 해당 실험의 모든 실행을 볼 수 있습니다.
    1. 실행을 선택한 다음, 설명 시각화 대시보드의 **공평** 탭을 선택 합니다.
    1. [ **공평** ] 탭을 마우스 오른쪽 단추로 클릭 한 후 오른쪽의 메뉴에서 **공평 id** 를 클릭 합니다.
    1. 중요 평가 페이지에서 관심 있는 중요 한 특성, 성능 메트릭 및 공평 메트릭을 선택 하 여 대시보드를 구성 합니다.
    1. 한 차트 종류에서 다른 항목으로 전환 하 여 **할당** 및 **서비스 품질** 을 모두 관찰할 수 있습니다.



    [![공평 대시보드 할당](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![서비스의 공평 대시보드 품질](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **모델 창**
    1. 이전 단계를 수행 하 여 원래 모델을 등록 한 경우 왼쪽 창에서 **모델** 을 선택 하 여 볼 수 있습니다.
    1. 모델을 선택 하 고 **공평** 탭을 클릭 하 여 설명 시각화 대시보드를 표시 합니다.

    시각화 대시보드 및 여기에 포함 된 내용에 대 한 자세한 내용은 Fairlearn의 [사용자 가이드를 참조](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)하세요.

## <a name="upload-fairness-insights-for-multiple-models"></a>여러 모델에 대 한 공평 정보 업로드

여러 모델을 비교 하 고 상호 평가가 어떻게 다른 지 확인 하기 위해 둘 이상의 모델을 시각화 대시보드에 전달 하 고 성능에 대 한 장단점을 비교할 수 있습니다.

1. 모델 학습:
    
    이제 지원 벡터 컴퓨터 평가기을 기반으로 두 번째 분류자를 만들고 Fairlearn의 패키지를 사용 하 여 공평 대시보드 사전을 업로드 `metrics` 합니다. 이전에 학습 된 모델을 계속 사용할 수 있다고 가정 합니다.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. 모델 등록

    그런 다음 Azure Machine Learning 내에서 두 모델을 등록 합니다. 편의를 위해 결과를 사전에 저장 합니다. 그러면 등록 된 `id` 모델의 ( `name:version` 형식 문자열)을 예측 자체에 매핑합니다.

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. 로컬에서 Fairlearn 대시보드 로드

    Azure Machine Learning에 대 한 공평 통찰력을 업로드 하기 전에 로컬에서 호출 된 Fairlearn 대시보드에서 이러한 예측을 검토할 수 있습니다. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. 공평 메트릭을 미리 계산 합니다.

    Fairlearn의 패키지를 사용 하 여 대시보드 사전을 만듭니다 `metrics` .

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. 사전 계산 공평 메트릭을 업로드 합니다.
    
    이제 패키지를 가져와서 `azureml.contrib.fairness` 업로드를 수행 합니다.

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    실험을 만들고 실행 한 다음 대시보드를 업로드 합니다.
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    이전 섹션과 마찬가지로 Azure Machine Learning studio에서 위에 설명 된 경로 중 하나를 수행 하 여 시각화  대시보드에 액세스 하 고 **두 모델을** 공평 성과 성능 측면에서 비교할 수 있습니다.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Unmitigated 업로드 및 완화 되는 공평 통찰력

Fairlearn의 [완화 알고리즘](https://fairlearn.github.io/master/user_guide/mitigation.html)을 사용 하 여 생성 된 완화 된 모델을 원래 unmitigated 모델과 비교 하 고 비교 된 모델 간에 성능/공평 절충을 탐색할 수 있습니다.

[표 검색](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) 완화 알고리즘을 사용 하는 방법을 보여 주는 예제를 보려면 (다양 한 공평 및 성능 절충으로 완화 된 모델의 컬렉션을 만드는 경우)이 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)을 확인 하세요. 

단일 실행에서 여러 모델의 공평 정보를 업로드 하는 것은 공평 성과 성능을 고려 하 여 모델을 비교할 수 있습니다. 모델 비교 차트에 표시 된 모델 중 하나를 클릭 하 여 특정 모델에 대 한 자세한 정보를 볼 수 있습니다.


[![모델 비교 Fairlearn 대시보드](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>다음 단계

[모델 공평에 대 한 자세한 정보](concept-fairness-ml.md)

[Azure Machine Learning 공평 샘플 노트북 확인](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
