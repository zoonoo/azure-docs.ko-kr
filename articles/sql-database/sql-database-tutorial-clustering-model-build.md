---
title: '자습서: R에서 클러스터링 모델 빌드'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 3부작 자습서 시리즈의 2부인 이 자습서에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링을 수행하는 K-평균 모델을 빌드하겠습니다.
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453100"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링 모델 빌드

세 부분으로 구성된 이 자습서 시리즈의 2부에서는 R에서 K-평균 모델을 빌드하여 클러스터링을 수행합니다. 이 시리즈의 다음 부분에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 이 모델을 SQL 데이터베이스에 배포합니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * KI-평균 알고리즘에 대한 클러스터 수 정의
> * 클러스터링 수행
> * 결과 분석

[1부](sql-database-tutorial-clustering-model-prepare-data.md)에서는 클러스터링을 수행할 수 있도록 Azure SQL 데이터베이스에서 데이터를 준비하는 방법을 알아보았습니다.

[3부](sql-database-tutorial-clustering-model-deploy.md)에서는 새 데이터를 기반으로 R에서 클러스터링을 수행할 수 있는 Azure SQL 데이터베이스에 저장 프로시저를 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 자습서 시리즈의 2부에서는 [**1부**](sql-database-tutorial-clustering-model-prepare-data.md) 및 해당 전제 작업을 완료했다고 가정합니다.

## <a name="define-the-number-of-clusters"></a>클러스터 수 정의

고객 데이터를 클러스터링하기 위해서는 데이터 그룹화를 위해 가장 잘 알려져 있고 가장 간단한 방법 중 하나인 **K-평균** 클러스터링 알고리즘을 사용합니다.
K-평균에 대한 자세한 내용은 [K-평균 클러스터링 알고리즘에 대한 전체 가이드](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)를 참조하세요.

이 알고리즘은 두 가지 입력을 허용합니다. 이 두 가지는 데이터 자체와 생성할 클러스터 수를 나타내는 미리 정의된 숫자인 "*k*"입니다.
출력은 클러스터 간에 분할된 입력 데이터를 포함하는 *k* 클러스터입니다.

알고리즘에 사용할 클러스터 수를 결정하려면 추출된 클러스터 수만큼 제곱의 내부 그룹 합계의 플롯을 사용합니다. 사용할 적절한 클러스터 수는 플롯의 굽은 지점 또는 "elbow"에 있습니다.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Elbow 그래프](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

그래프에 따르면 *k = 4*가 적합한 값으로 보입니다. 이 *k* 값은 고객을 4개 클러스터로 그룹화합니다.

## <a name="perform-clustering"></a>클러스터링 수행

다음 R 스크립트에서는 RevoScaleR 패키지의 K-평균 함수인 **rxKmeans** 함수를 사용합니다.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>결과 분석

K-평균을 사용하여 클러스터링을 완료했으므로, 이제 결과를 분석하고 실행 가능한 정보를 찾을 수 있는지 확인할 차례입니다.

**클러스터** 개체는 K-평균 클러스터링의 결과를 포함하고 있습니다.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

[1부](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)에 정의된 변수를 사용하여 4개의 클러스터 평균이 제공되었습니다.

* *orderRatio* = 반품 주문 비율(전체 주문 수 대비 부분 또는 전체적으로 반품된 주문 합계 수)
* *itemsRatio* = 반품 항목 비율(구입한 항목 수 대비 반품된 총 항목 수)
* *monetaryRatio* = 반품 금액 비율(구입한 금액 대비 반품된 항목의 총 금액)
* *frequency* = 반품 빈도

K-평균을 사용한 데이터 마이닝에는 결과에 대한 추가 분석 및 각 클러스터에 대한 이해 수준 향상을 위한 추가 단계가 필요한 경우가 많지만, 이를 통해 좋은 효과를 얻을 수 있습니다.
이러한 결과를 해석할 수 있는 몇 가지 방법은 다음과 같습니다.

* 클러스터 1(가장 큰 클러스터)은 적극적이지 않은 고객 그룹인 것 같습니다(모든 값이 0).
* 클러스터 3은 반품 비중이 높은 그룹으로 보입니다.

## <a name="clean-up-resources"></a>리소스 정리

***이 자습서를 더 이상 진행하지 않을 분들은*** Azure SQL Database 서버에서 tpcxbb_1gb 데이터베이스를 삭제합니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 또는 **SQL 데이터베이스**를 선택합니다.
1. **이름을 기준으로 필터링...** 필드에 **tpcxbb_1gb**를 입력하고 구독을 선택합니다.
1. **tpcxbb_1gb** 데이터베이스를 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서 시리즈의 2부에서 다음 단계를 완료했습니다.

* KI-평균 알고리즘에 대한 클러스터 수 정의
* 클러스터링 수행
* 결과 분석

만든 기계 학습 모델을 배포하려면 이 자습서 시리즈의 3부를 수행합니다.

> [!div class="nextstepaction"]
> [자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 클러스터링 모델 배포](sql-database-tutorial-clustering-model-deploy.md)