---
title: Azure Machine Learning을 사용하여 데이터 분석
description: Azure 기계 학습을 사용하여 Azure Synapse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 빌드합니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416182"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 데이터 분석
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

이 자습서에서는 Azure 기계 학습을 사용하여 Azure Synapse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 빌드합니다. 특히 고객이 자전거를 구매할 가능성 여부를 예측하여 자전거 매장인 Adventure Works에 대한 대상 마케팅 캠페인을 구축합니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 단계별로 실행하려면 다음이 필요합니다.

* AdventureWorksDW 샘플 데이터가 미리 로드된 SQL 풀입니다. 이를 프로비전하려면 [SQL 풀 만들기를](create-data-warehouse-portal.md) 참조하고 샘플 데이터를 로드하도록 선택합니다. 데이터 웨어하우스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드](load-data-from-azure-blob-storage-using-polybase.md)할 수 있습니다.

## <a name="1-get-the-data"></a>1. 데이터 얻기
데이터는 AdventureWorksDW 데이터베이스의 dbo.vTargetMail 보기에 있습니다. 이 데이터를 읽으려면:

1. [Azure Machine Learning 스튜디오](https://studio.azureml.net/)에 로그인하고 내 실험을 클릭합니다.
2. 화면 왼쪽 하단에 **있는 +NEW를** 클릭하고 **빈 실험을**선택합니다.
3. 실험: 대상 마케팅에 대한 이름을 입력합니다.
4. 데이터 입력 및 **출력에서 데이터** **가져오기** 모듈을 드래그하여 모듈 창에서 캔버스로 출력합니다.
5. 속성 창에서 SQL 풀의 세부 정보를 지정합니다.
6. 관련 데이터를 읽을 데이터베이스 **쿼리** 를 지정합니다.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

실험 캔버스 아래에서 **실행을** 클릭하여 실험을 실행합니다.

![실험 실행](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

실험이 성공적으로 실행되고 나면 판독기 모듈 아래쪽에서 출력 포트를 클릭하고 **시각화** 를 선택하여 가져온 데이터를 확인합니다.

![가져온 데이터 확인](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. 데이터 정리
데이터를 정리하려면 모델에 관련되지 않은 일부 열을 삭제합니다. 다음을 수행합니다.

1. **데이터 변환 < 조작** 에서 데이터 **집합의 열 선택 모듈을** 캔버스로 끕니다. 이 모듈을 **데이터 가져오기** 모듈에 연결합니다.
2. 속성 창에서 **열 선택기 시작** 을 클릭하여 삭제하려는 열을 지정합니다.

   ![프로젝트 열](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. 다음 두 열을 제외합니다.

   ![불필요한 열 제거](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. 모델 빌드
데이터를 80-20으로 분할합니다. 80%는 기계 학습 모델을 학습하고 20%는 모델을 테스트합니다. 이 이진 분류 문제에 대해 "2클래스" 알고리즘을 사용합니다.

1. **분할** 모듈을 캔버스로 끌어서 놓습니다.
2. 속성 창에서 첫 번째 출력 데이터 집합에서 행의 분수에 대해 0.8을 입력합니다.

   ![훈련 및 테스트 집합으로 데이터 분할](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. **2클래스 향상된 의사 결정 트리** 모듈을 캔버스로 끌어서 놓습니다.
4. 학습 **모델** 모듈을 캔버스로 드래그하고 **2클래스 부스트 의사 결정 트리(ML** 알고리즘) 및 **분할(알고리즘을** 학습하는 데이터) 모듈에 연결하여 입력을 지정합니다. 

     ![모델 학습 모듈 연결](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. 그런 다음 속성 창에서 **열 선택기 시작** 을 클릭합니다. **BikeBuyer** 열을 예측할 열로 선택합니다.

   ![예측할 열 선택](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. 모델 점수 매기기
이제 모델이 테스트 데이터를 수행하는 방법을 테스트합니다. 더 잘 수행하는 알고리즘을 확인하도록 다른 알고리즘을 선택하여 비교합니다.

1. **점수 모델** 모듈을 캔버스에 드래그하여 **모델** 및 **분할 데이터** 모듈에 연결합니다.

   ![모델 점수 매기기](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. **2클래스 Bayes 지점 컴퓨터** 를 실험 캔버스로 끌어서 놓습니다. 2클래스 향상된 의사 결정 트리와 비교하여 이 알고리즘이 수행하는 방법을 비교합니다.
3. 캔버스에서 모델 학습 및 모델 점수 매기기 모듈을 복사하고 붙여 넣습니다.
4. **모델 평가** 모듈을 캔버스로 끌어서 놓아 두 알고리즘을 비교합니다.
5. **실행** 합니다.

   ![실험 실행](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. 모델 평가 모듈의 아래쪽에서 출력 포트를 클릭하고 시각화를 클릭합니다.

   ![평가 결과 시각화](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

제공된 메트릭은 ROC 곡선, 정밀 도표 및 리프트 곡선입니다. 이러한 메트릭을 살펴보면 첫 번째 모델이 두 번째보다 더 잘 실행하는 것을 볼 수 있습니다. 첫 번째 모델이 예측한 내용을 보려면 점수 모델의 출력 포트를 클릭하고 시각화를 클릭합니다.

![점수 결과 시각화](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

테스트 데이터 세트에 두 개의 열이 추가됩니다.

* 점수가 매겨진 확률: 고객이 자전거 구매자일 가능성입니다.
* 점수가 매겨진 레이블: 모델에 의해 분류가 실행되었습니다. – 자전거 구매자(1) 혹은 아님(0) 레이블 지정에 대한 확률 임계값은 50%로 설정되고 조정할 수 있습니다.

점수가 매겨진 레이블(예측)로 열 BikeBuyer(실제) 비교를 통해 모델이 얼마나 잘 실행했는지 확인할 수 있습니다. 그런 다음 이 모델을 사용하여 새 고객을 예측하고 이 모델을 웹 서비스로 게시하거나 결과를 Azure Synapse에 다시 쓸 수 있습니다.

## <a name="next-steps"></a>다음 단계
예측 Machine Learning 모델을 구축하는 방법에 대한 자세한 내용은 [Azure의 Machine Learning 소개](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/)를 참고하세요.