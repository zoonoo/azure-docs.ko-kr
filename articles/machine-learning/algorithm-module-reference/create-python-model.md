---
title: '파이썬 모델 만들기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 파이썬 모델 만들기 모듈을 사용하여 사용자 지정 모델링 또는 데이터 처리 모듈을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682815"
---
# <a name="create-python-model-module"></a>파이썬 모델 모듈 만들기

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

파이썬 모델 만들기 모듈을 사용하여 파이썬 스크립트에서 학습되지 않은 모델을 만드는 방법에 대해 알아봅니다. Azure 기계 학습 디자이너 환경에서 Python 패키지에 포함된 모든 학습자의 모델을 기반으로 할 수 있습니다. 

모델을 만든 후 [Train Model을](train-model.md) 사용하여 Azure 기계 학습의 다른 수강생과 마찬가지로 데이터 집합에서 모델을 학습할 수 있습니다. 학습된 모델을 점수 [모델로](score-model.md) 전달하여 예측을 할 수 있습니다. 그런 다음 학습된 모델을 저장하고 점수 매기기 워크플로를 웹 서비스로 게시할 수 있습니다.

> [!WARNING]
> 현재 파이썬 모델의 점수가 매겨지는 결과를 [모델 평가에](evaluate-model.md)전달할 수 없습니다. 모델을 평가해야하는 경우 사용자 정의 파이썬 스크립트를 작성하고 [파이썬 스크립트 실행](execute-python-script.md) 모듈을 사용하여 실행할 수 있습니다.  


## <a name="configure-the-module"></a>모듈 구성

이 모듈을 사용하려면 파이썬에 대한 중간 또는 전문 지식이 필요합니다. 이 모듈은 Azure 기계 학습에 이미 설치된 Python 패키지에 포함된 모든 학습자의 사용을 지원합니다. [파이썬 스크립트 실행에서](execute-python-script.md)사전 설치된 파이썬 패키지 목록을 참조하십시오.

> [!NOTE]
> 스크립트를 작성할 때는 선언되지 않은 개체 또는 가져오지 않은 모듈을 사용하는 등 구문 오류가 없는지 확인하십시오.

> [!NOTE]
또한 [파이썬 스크립트 실행에서](execute-python-script.md)미리 설치된 모듈 목록에 주의를 기울이기 . 사전 설치된 모듈만 가져옵니다. 이 스크립트에 "핍 설치 xgboost"와 같은 추가 패키지를 설치하지 마십시오, 그렇지 않으면 오류가 다운 스트림 모듈에서 모델을 읽을 때 발생합니다.
  
이 문서에서는 간단한 파이프라인으로 **파이썬 모델 만들기를** 사용하는 방법을 보여 주며 이 문서에서는 파이프라인다이어그램은 다음과 같습니다.

![파이썬 모델 만들기 다이어그램](./media/module/create-python-model.png)

1. **파이썬 모델 만들기를**선택하고 모델링 또는 데이터 관리 프로세스를 구현하기 위해 스크립트를 편집합니다. Azure 기계 학습 환경에서 Python 패키지에 포함된 모든 학습자의 모델을 기반으로 할 수 있습니다.

> [!NOTE]
> 스크립트의 샘플 코드의 주석에 주의를 기울이고 스크립트가 클래스 이름, 메서드 및 메서드 서명을 포함하여 요구 사항을 엄격하게 준수하는지 확인하십시오. 위반시 예외가 발생할 수 있습니다. 

   2클래스 Naive Bayes 분류기의 다음 샘플 코드는 인기 있는 *sklearn* 패키지를 사용합니다.

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

1. 방금 만든 **파이썬 모델 만들기** 모듈을 연결하여 모델 및 **점수 모델을** **학습합니다.**

1. 모델을 평가해야하는 경우 [파이썬 스크립트](execute-python-script.md) 실행 모듈을 추가하고 파이썬 스크립트를 편집하십시오.

   다음 스크립트는 샘플 평가 코드입니다.

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function can contain up to two input arguments:
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

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 