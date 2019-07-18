---
title: '자습서: R에서 예측 모델 학습 및 비교'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 이 세 부분으로 이루어진 자습서 시리즈의 2부에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 두 가지 예측 모델을 만든 후 가장 정확한 모델을 선택합니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957514"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델 만들기

이 세 부분으로 이루어진 자습서 시리즈의 2부에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 두 가지 예측 모델을 만든 후 가장 정확한 모델을 선택합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 2가지 기계 학습 모델 학습
> * 두 모델에서 예측 수행
> * 결과를 비교하여 가장 정확한 모델 선택

[1부](sql-database-tutorial-predictive-model-prepare-data.md)에서는 샘플 데이터베이스를 Azure SQL Database로 가져오고, R에서 예측 모델을 학습하는 데 사용할 데이터를 준비하는 방법을 살펴보았습니다.

[3부](sql-database-tutorial-predictive-model-deploy.md)에서는 데이터베이스에 모델을 저장한 후, 새 데이터를 기준으로 예측을 수행할 수 있는 저장 프로시저를 만드는 방법을 알아봅니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

* 이 자습서 시리즈의 2부에서는 [**1부**](sql-database-tutorial-predictive-model-prepare-data.md) 및 해당 전제 작업을 완료했다고 가정합니다.

## <a name="train-two-models"></a>두 모델 학습

스키 대여 데이터에 가장 적합한 모델을 찾으려면 두 가지 다른 모델(선형 회귀와 의사 결정 트리)을 만들고 보다 정확하게 예약할 수 있는 모델을 확인합니다. 이 시리즈 1부에서 만든 데이터 프레임 `rentaldata`를 사용합니다.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>두 모델에서 예측 수행

예측 함수를 사용하여 학습된 각 모델로 대여 수를 예측합니다.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>결과 비교

이제 최상의 예측을 제공할 모델을 확인하려고 합니다. 이 작업을 수행하는 쉽고 빠른 방법은 기본적인 그리기 함수를 사용하여 학습 데이터의 실제 값과 예측된 값 간의 차이를 확인하는 것입니다.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![두 모델 비교](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

의사 결정 트리 모델이 두 모델 중에서 좀 더 정확한 것 같습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 계속 사용하지는 않으려면 Azure SQL Database 서버에서 TutorialDB 데이터베이스를 삭제합니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 또는 **SQL 데이터베이스**를 선택합니다.
1. **이름을 기준으로 필터링...** 필드에 **TutorialDB**를 입력하고 구독을 선택합니다.
1. TutorialDB 데이터베이스를 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서 시리즈의 제2부에서는 다음 단계를 완료했습니다.

* 2가지 기계 학습 모델 학습
* 두 모델에서 예측 수행
* 결과를 비교하여 가장 정확한 모델 선택

만든 기계 학습 모델을 배포하려면 이 자습서 시리즈의 3부를 수행합니다.

> [!div class="nextstepaction"]
> [자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델 배포](sql-database-tutorial-predictive-model-deploy.md)
