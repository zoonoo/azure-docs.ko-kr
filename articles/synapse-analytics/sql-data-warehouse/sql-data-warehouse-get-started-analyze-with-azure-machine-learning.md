---
title: Azure Machine Learning을 사용하여 데이터 분석
description: Azure Synapse를 사용하여 Azure SQL Data Warehouse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: ea4038e88d41a089958d4199e4c5a00f0d2acabd
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015569"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 데이터 분석

이 자습서에서는 [Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/concept-designer) 를 사용 하 여 예측 기계 학습 모델을 작성 합니다. 이 모델은 Azure Synapse에 저장 된 데이터를 기반으로 합니다. 이 자습서의 시나리오는 고객이 자전거를 구매할 가능성이 있는지 여부를 예측 하는 것입니다. 자전거 상점에서는 자전거 상점에서 대상 마케팅 캠페인을 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 단계별로 실행하려면 다음이 필요합니다.

* AdventureWorksDW 샘플 데이터와 함께 미리 로드 된 SQL 풀. 이 SQL 풀을 프로 비전 하려면 [sql 풀 만들기](create-data-warehouse-portal.md) 를 참조 하 고 샘플 데이터 로드를 선택 합니다. 데이터 웨어하우스가 이미 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드할](load-data-from-azure-blob-storage-using-polybase.md)수 있습니다.
* Azure Machine learning 작업 영역. [이 자습서](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) 에 따라 새 자습서를 만듭니다.

## <a name="get-the-data"></a>데이터 가져오기

사용 되는 데이터는 AdventureWorksDW의 dbo. vTargetMail 보기에 있습니다. 이 자습서에서 데이터 저장소를 사용 하려면 Azure Synapse에서 현재 데이터 집합을 지원 하지 않으므로 데이터를 먼저 Azure Data Lake Storage 계정으로 내보냅니다. Azure Data Factory은 [복사 작업](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)을 사용 하 여 데이터 웨어하우스에서 Azure Data Lake Storage 데이터를 내보내는 데 사용할 수 있습니다. 가져오기에 다음 쿼리를 사용 합니다.

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

Azure Data Lake Storage에서 데이터를 사용할 수 있게 되 면 Azure Machine Learning의 Datastores를 사용 하 여 [Azure Storage 서비스에 연결](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)합니다. 다음 단계를 수행 하 여 데이터 저장소 및 해당 데이터 집합을 만듭니다.

1. Azure Portal에서 또는 [Azure Machine Learning studio](https://ml.azure.com/)에서 로그인 하 Azure Machine Learning studio를 시작 합니다.

1. **관리** 섹션의 왼쪽 창에서 **datastores** 를 클릭 한 다음 **새 데이터 저장소**를 클릭 합니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

1. 데이터 저장소의 이름을 제공 하 고, 형식을 ' Azure Blob Storage '로 선택 하 고, 위치 및 자격 증명을 제공 합니다. 그런 다음 **만들기**를 클릭합니다.

1. 그런 다음 **자산** 섹션의 왼쪽 창에서 **데이터 집합** 을 클릭 합니다. 데이터 **저장소에서**옵션을 사용 하 여 **데이터 집합 만들기** 를 선택 합니다.

1. 데이터 집합의 이름을 지정 하 고 **테이블**형식으로 사용할 형식을 선택 합니다. 그런 다음 **다음** 을 클릭 하 여 앞으로 이동 합니다.

1. **데이터 저장소 선택 또는 만들기 섹션**에서 **이전에 만든 데이터 저장소**옵션을 선택 합니다. 이전에 만든 데이터 저장소를 선택 합니다. 다음을 클릭 하 고 경로 및 파일 설정을 지정 합니다. 파일에 열 머리글이 포함 된 경우 열 헤더를 지정 해야 합니다.

1. 마지막으로 **만들기** 를 클릭 하 여 데이터 집합을 만듭니다.

## <a name="configure-designer-experiment"></a>디자이너 실험 구성

다음으로 디자이너 구성에 대해 아래 단계를 수행 합니다.

1. **작성자** 섹션의 왼쪽 창에서 **디자이너** 탭을 클릭 합니다.

1. **사용 하기 쉬운 미리** 작성 된 모듈을 선택 하 여 새 파이프라인을 만듭니다.

1. 오른쪽의 설정 창에서 파이프라인의 이름을 지정 합니다.

1. 또한 설정 단추의 전체 실험에 대해 이전에 프로 비전 된 클러스터에 대 한 대상 계산 클러스터를 선택 합니다. 설정 창을 닫습니다.

## <a name="import-the-data"></a>데이터 가져오기

1. 검색 상자 아래의 왼쪽 창에서 **데이터 집합** 하위 탭를 선택 합니다.

1. 이전에 만든 데이터 집합을 캔버스로 끌어다 놓습니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

## <a name="clean-the-data"></a>데이터 정리

데이터를 정리 하려면 모델과 관련이 없는 열을 삭제 합니다. 아래 단계를 따릅니다.

1. 왼쪽 창에서 하위 탭 **모듈** 을 선택 합니다.

1. **데이터 변환 < 조작**의 **데이터 센터의 열 선택** 모듈을 캔버스로 끌어 옵니다. 이 모듈을 **데이터 집합** 모듈에 연결 합니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. 모듈을 클릭 하 여 속성 창을 엽니다. 열 편집을 클릭 하 여 삭제 하려는 열을 지정 합니다.

1. 열 2개(CustomerAlternateKey 및 GeographyKey)를 제외합니다. 페이지 맨 아래에 있는 **저장**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

## <a name="build-the-model"></a>모델 빌드

데이터는 machine learning 모델을 학습 하는 80-20:80%와 모델을 테스트 하기 위한 20%로 나뉩니다. 이러한 이진 분류 문제에는 "2 클래스" 알고리즘이 사용 됩니다.

1. **데이터 분할** 모듈을 캔버스로 끌어 옵니다.

1. 속성 창에서 **첫 번째 출력 데이터 집합의 행 분수**에 대해 0.8을 입력 합니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

1. **2클래스 향상된 의사 결정 트리** 모듈을 캔버스로 끌어서 놓습니다.

1. **모델 학습** 모듈을 캔버스로 끌어다 놓습니다. **2 클래스 승격 된 의사 결정 트리** (ML 알고리즘)에 연결 하 여 입력을 지정 하 고 데이터 (알고리즘 학습에 대 한 데이터) 모듈을 **분할** 합니다.

1. 모델 학습 모델의 경우 속성 창의 **레이블 열** 옵션에서 열 편집을 선택 합니다. 예측할 열로 **Bikebuyer** 열을 선택 하 고 **저장**을 선택 합니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

## <a name="score-the-model"></a>모델 점수 매기기

이제 모델에서 테스트 데이터에 대해 수행 하는 방법을 테스트 합니다. 두 가지 알고리즘을 비교 하 여 더 나은 성능을 확인할 수 있습니다. 아래 단계를 따릅니다.

1. **모델 점수 매기기** 모듈을 캔버스로 끌어 놓고 **모델 학습** 및 **데이터 분할** 모듈에 연결합니다.

1. **2 클래스 Bayes 평균 퍼셉트론** 를 실험 캔버스로 끕니다. Two-Class 승격 된 의사 결정 트리와 비교 하 여이 알고리즘이 수행 하는 방법을 비교 합니다.

1. 모듈 **학습 모델 학습** 및 **모델 점수 매기기 모델** 을 복사 하 여 붙여 넣습니다.

1. **모델 평가** 모듈을 캔버스로 끌어서 놓아 두 알고리즘을 비교합니다.

1. **전송** 을 클릭 하 여 파이프라인 실행을 설정 합니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. 실행이 완료 되 면 **모델 평가** 모듈을 마우스 오른쪽 단추로 클릭 하 고 **평가 결과 시각화**를 클릭 합니다.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Azure Machine Learning 인터페이스의 왼쪽 창 스크린샷":::

제공된 메트릭은 ROC 곡선, 정밀도-리콜 다이어그램 및 리프트 곡선입니다. 이러한 메트릭을 확인 하 여 첫 번째 모델이 두 번째 모델 보다 더 잘 수행 되었는지 확인 합니다. 첫 번째 모델이 예측 하는 것을 보려면 모델 점수 매기기 모듈을 마우스 오른쪽 단추로 클릭 하 고 점수가 매겨진 데이터 집합 시각화를 클릭 하 여 예측 결과를 확인 합니다.

테스트 데이터 집합에 추가 된 두 개의 열이 표시 됩니다.

* 점수가 매겨진 확률: 고객이 자전거 구매자일 가능성입니다.
* 점수가 매겨진 레이블: 모델에 의해 분류가 실행되었습니다. – 자전거 구매자(1) 혹은 아님(0) 레이블 지정에 대한 확률 임계값은 50%로 설정되고 조정할 수 있습니다.

BikeBuyer (실제) 열을 점수가 매겨진 레이블 (예측)과 비교 하 여 모델이 얼마나 잘 수행 되었는지 확인 합니다. 그런 다음이 모델을 사용 하 여 새 고객에 대 한 예측을 만들 수 있습니다. [이 모델을 웹 서비스로 게시](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) 하거나 결과를 Azure Synapse에 다시 쓸 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에 대 한 자세한 내용은 [Azure의 Machine Learning 소개](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)를 참조 하세요.

데이터 웨어하우스의 기본 제공 점수 매기기에 대해 자세히 [알아보세요.](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)