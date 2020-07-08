---
title: 'Python 모델 만들기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 Python 모델 만들기 모듈을 사용 하 여 사용자 지정 모델링 또는 데이터 처리 모듈을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 59a335e41116818e41579488cc6931ed1534c9ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984888"
---
# <a name="create-python-model-module"></a>Python 모델 모듈 만들기

이 문서에서는 Azure Machine Learning 디자이너(미리 보기)의 모듈에 대해 설명합니다.

Python 모델 만들기 모듈을 사용 하 여 Python 스크립트에서 학습 되지 않은 모델을 만드는 방법에 대해 알아봅니다. Azure Machine Learning designer 환경의 Python 패키지에 포함 된 모든 학습자 모델을 기반으로 할 수 있습니다. 

모델을 만든 후에는 [모델 학습](train-model.md) 을 사용 하 여 Azure Machine Learning의 다른 학습자 같은 데이터 집합에 대 한 모델 학습을 수행할 수 있습니다. 학습 된 모델을 [점수 모델](score-model.md) 에 전달 하 여 예측을 만들 수 있습니다. 그런 다음 학습 된 모델을 저장 하 고 점수 매기기 워크플로를 웹 서비스로 게시할 수 있습니다.

> [!WARNING]
> 현재이 모듈을 연결 하 여 **모델 Hyperparameters** 모듈을 튜닝 하거나 Python 모델의 점수가 매겨진 결과를 전달 하 여 모델을 [평가할](evaluate-model.md)수 없습니다. 하이퍼 매개 변수를 조정 하거나 모델을 평가 해야 하는 경우 [Python 스크립트 실행](execute-python-script.md) 모듈을 사용 하 여 사용자 지정 python 스크립트를 작성할 수 있습니다.


## <a name="configure-the-module"></a>모듈 구성

이 모듈을 사용 하려면 Python에 대해 중급 또는 전문 지식이 필요 합니다. 이 모듈은 Azure Machine Learning에 이미 설치 된 Python 패키지에 포함 된 모든 학습자의 사용을 지원 합니다. [Python 스크립트 실행](execute-python-script.md)에서 사전 설치 된 python 패키지 목록을 참조 하세요.

> [!NOTE]
> 스크립트를 작성할 때는 매우 주의 해야 하며 선언 되지 않은 개체 또는 가져온 모듈을 사용 하는 것과 같은 구문 오류가 없는지 확인 하십시오.

> [!NOTE]
> 또한 [Python 스크립트 실행](execute-python-script.md)에서 사전 설치 된 모듈 목록에 대 한 추가 주의 지불 합니다. 미리 설치 된 모듈만 가져옵니다. 이 스크립트에 "pip install xgboost"와 같은 추가 패키지를 설치 하지 마세요. 그렇지 않으면 다운 스트림 모듈에서 모델을 읽을 때 오류가 발생 합니다.
  
이 문서에서는 간단한 파이프라인에서 **Python 모델 만들기** 를 사용 하는 방법을 보여 줍니다. 파이프라인의 다이어그램은 다음과 같습니다.

![Python 모델 만들기 다이어그램](./media/module/create-python-model.png)

1. **Python 모델 만들기**를 선택 하 고 스크립트를 편집 하 여 모델링 또는 데이터 관리 프로세스를 구현 합니다. Azure Machine Learning 환경의 Python 패키지에 포함 된 모든 학습자 모델을 기반으로 할 수 있습니다.

> [!NOTE]
> 스크립트의 샘플 코드에서 주석에 대 한 추가 주의가 필요 하며, 스크립트는 클래스 이름, 메서드 및 메서드 시그니처를 비롯 한 요구 사항을 엄격 하 게 준수 하는지 확인 하세요. 위반으로 인해 예외가 발생 합니다. 

   2 클래스 Naive Bayes 분류자의 다음 샘플 코드 *는 인기 있는 기능을 사용 하* 는 다음 패키지를 사용 합니다.

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. 방금 만든 **Python 모델 만들기** 모듈을 연결 하 여 **모델을 학습** 하 고 **모델 점수**를 매길 수 있습니다.

3. 모델을 평가 해야 하는 경우 [Python 스크립트 실행](execute-python-script.md) 모듈을 추가 하 고 python 스크립트를 편집 합니다.

   다음 스크립트는 샘플 평가 코드입니다.

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 