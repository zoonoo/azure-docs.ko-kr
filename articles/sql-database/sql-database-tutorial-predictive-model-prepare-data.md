---
title: '자습서: R에서 예측 모델을 학습하기 위한 데이터 준비'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 이 세 부분으로 이루어진 자습서 시리즈의 1부에서는 Azure SQL Database Machine Learning 서비스(미리 보기)를 사용하여 R에서 예측 모델을 학습하기 위해 Azure SQL Database의 데이터를 준비합니다.
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
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978739"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델을 학습하기 위한 데이터 준비

이 세 부분으로 이루어진 자습서 시리즈의 1부에서는 Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델을 학습하기 위해 Azure SQL Database의 데이터를 준비합니다.

이 자습서 시리즈에서는 스키 대여 사업을 소유하고 있으며 향후 유지할 대여 수를 예측하려고 한다고 가정해 보겠습니다. 이 정보는 재고, 직원 및 시설을 준비하는 데 도움이 됩니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 샘플 데이터베이스를 Azure SQL Database로 가져오기
> * R을 사용하여 Azure SQL Database의 데이터를 데이터 프레임으로 로드
> * 일부 열을 범주로 식별하여 데이터 준비

[2부](sql-database-tutorial-predictive-model-build-compare.md)에서는 여러 모델을 만들고 학습한 후 가장 정확한 모델을 선택하는 방법을 알아봅니다.

[3부](sql-database-tutorial-predictive-model-deploy.md)에서는 데이터베이스에 모델을 저장한 후, 새 데이터를 기준으로 예측을 수행할 수 있는 저장 프로시저를 만드는 방법을 알아봅니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - Azure 구독이 아직 없는 경우 시작하기 전에 [계정을 만듭니다](https://azure.microsoft.com/free/).

* Machine Learning Services가 사용하도록 설정된 Azure SQL Database 서버 - Microsoft는 위에서 설명한 것처럼 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 머신 러닝을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

* RevoScaleR 패키지 - 이 패키지를 로컬로 설치하기 위한 옵션에 대해서는 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms)을 참조하세요.

* R IDE - 이 자습서에서는 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)을 사용합니다.

* SQL 쿼리 도구 - 이 자습서에서는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 또는 SSMS([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms))를 사용하고 있다고 가정합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="import-the-sample-database"></a>샘플 데이터베이스 가져오기

이 자습서에서 사용된 샘플 데이터 세트는 사용자가 다운로드하여 사용할 수 있도록 **.bacpac** 데이터베이스 백업 파일에 저장되었습니다.

1. 파일 [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac)를 다운로드합니다.

1. 다음 세부 정보를 사용하여 [BACPAC 파일을 가져와 Azure SQL Database 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-import)의 지침을 따르세요.

   * 다운로드한 **TutorialDB.bacpac** 파일에서 가져오기
   * 공개 미리 보기 동안 새 데이터베이스에 대해 **Gen5/vCore** 구성 선택
   * 새 데이터베이스 이름을 "TutorialDB"로 지정

## <a name="load-the-data-into-a-data-frame"></a>데이터 프레임으로 데이터 로드

R에서 데이터를 사용하려면 Azure SQL Database의 데이터를 데이터 프레임(`rentaldata`)으로 로드합니다.

RStudio에서 새 RScript 파일을 만든 후 다음 스크립트를 실행합니다. **Server**, **UID** 및 **PWD**를 고유한 연결 정보를 바꿉니다.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

다음과 유사한 결과가 표시됩니다.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>데이터 준비

이 샘플 데이터베이스에서는 대부분의 준비가 이미 수행되었지만 여기서 준비 작업을 하나 더 수행합니다.
다음 R 스크립트를 사용하고 데이터 형식을 *비율*로 변경하여 세 개의 열을 *범주*로 식별합니다.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

다음과 유사한 결과가 표시됩니다.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

이제 해당 데이터를 학습에 사용할 준비가 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 계속 사용하지는 않으려면 Azure SQL Database 서버에서 TutorialDB 데이터베이스를 삭제합니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 또는 **SQL 데이터베이스**를 선택합니다.
1. **이름을 기준으로 필터링...** 필드에 **TutorialDB**를 입력하고 구독을 선택합니다.
1. TutorialDB 데이터베이스를 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서 시리즈의 제1부에서는 다음 단계를 완료했습니다.

* 데이터베이스 백업 파일을 Azure SQL Database로 가져오기
* R을 사용하여 Azure SQL Database의 데이터를 데이터 프레임으로 로드
* 일부 열을 범주로 식별하여 데이터 준비

TutorialDB 데이터베이스의 데이터를 사용하는 Machine Learning 모델을 만들려면 이 자습서 시리즈의 2부를 따르세요.

> [!div class="nextstepaction"]
> [자습서: Azure SQL Database Machine Learning Services(미리 보기)를 사용하여 R에서 예측 모델 만들기](sql-database-tutorial-predictive-model-build-compare.md)
