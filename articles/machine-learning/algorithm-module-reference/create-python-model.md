---
title: Python 모델을 만듭니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: 사용자 지정 모델링 또는 데이터 처리 모듈을 만드는 Azure Machine Learning 서비스에서 Python 모델 만들기 모델을 사용 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029747"
---
# <a name="create-python-model"></a>Python 모델 만들기

이 문서에서는 사용 하는 방법을 설명 합니다 **Python 모델 만들기** 모듈을 Python 스크립트에서 학습 되지 않은 모델을 만들 합니다. 

Azure Machine Learning 환경에서 Python 패키지에 포함 된 학습자 기반 모델을 만들 수 있습니다. 

모델을 만든 후 사용할 수 있습니다 [모델 학습](train-model.md) 를 데이터 집합에서 모델을 학습, Azure Machine Learning의 다른 학습자와 같이 합니다. 학습 된 모델을 전달할 수 있습니다 [모델 점수 매기기](score-model.md) 예측 하도록 모델을 사용 하도록 합니다. 학습 된 모델을 저장할 수 있습니다, 그리고 및 점수 매기기 워크플로가 웹 서비스로 게시할 수 있습니다.

> [!WARNING]
> Python 모델의 점수가 매겨진된 결과 전달할 수 없는 현재 [모델 평가](evaluate-model.md)합니다. 사용자 지정 Python 스크립트를 사용 하 여 실행 모델을 평가 해야 하는 경우 작성할 수 있습니다 합니다 [Python 스크립트 실행](execute-python-script.md) 모듈입니다.  


## <a name="how-to-configure-create-python-model"></a>Python 모델 만들기를 구성 하는 방법

이 모듈을 사용 하 여 Python에 대 한 중간 또는 전문 지식이 필요합니다. 모듈에는 Azure Machine Learning에서 이미 설치 된 Python 패키지에 포함 된 학습자 사용을 지원 합니다. 사전 설치 된 Python 패키지 목록을 볼 [Python 스크립트 실행](execute-python-script.md)합니다.
  

이 문서에서는 사용 하는 방법을 보여 합니다 **Python 모델 만들기** 간단한 실험을 사용 하 여 합니다. 실험 그래프는 다음과 같습니다.

![create-python-model](./media/module/aml-create-python-model.png)

1.  클릭 **Python 모델 만들기**에 모델링을 구현 하는 스크립트를 편집 하거나 데이터 관리를 처리 합니다. Azure Machine Learning 환경에서 Python 패키지에 포함 된 학습자 기반 모델을 만들 수 있습니다.


    인기 있는 사용 하 여 2 클래스 Naive Bayes 분류자의 샘플 코드는 다음과 같습니다 *sklearn* 패키지 있습니다.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. 연결 된 **Python 모델 만들기** 방금 만든 모듈을 **모델 학습** 및 **모델 점수 매기기**

3. 모델을 평가 하는 경우 추가 된 [Python 스크립트 실행](execute-python-script.md) 및 평가 구현 하는 Python 스크립트를 편집 합니다.

다음은 샘플 평가 코드입니다.

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
