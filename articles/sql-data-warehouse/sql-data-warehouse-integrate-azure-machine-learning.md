---
title: "SQL Data Warehouse와 함께 Azure Machine Learning 사용 | Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Azure 기계 학습 사용을 위한 팁"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: ac6bc731-6add-47a9-b3fe-68996e656f4d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 30dcbe33b359afc3f118effce07f6574bb35d5d5
ms.lasthandoff: 12/08/2016


---
# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>SQL 데이터 웨어하우스와 함께 Azure 기계 학습 사용
Azure 기계 학습은 SQL 데이터 웨어하우스의 데이터에 대해 예측 모델을 만드는 데 사용할 수 있는 완전한 관리 예측 분석 서비스로, 사용할 준비가 된 웹 서비스로 게시할 수 있습니다. [Azure에서 기계 학습 소개][Introduction to Machine Learning on Azure]를 읽어 예측 분석의 기본 사항 및 기계 학습에 대해 알 수 있습니다.  그런 다음 [실험 만들기 자습서][Create experiment tutorial]를 사용하여 기계 학습 모델을.만들고, 훈련하고, 점수를 매기고 테스트하는 방법에 대해 알 수 있습니다.

이 문서에서는 [Azure 기계 학습 스튜디오][Azure Machine Learning Studio]를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

* 데이터베이스에서 데이터를 읽어, 예측 모델을 만들고 훈련하고 점수 매기기
* 데이터베이스에 데이터 쓰기

## <a name="read-data-from-sql-data-warehouse"></a>SQL 데이터 웨어하우스에서 데이터 읽기
AdventureWorksDW 데이터베이스의 Product 테이블에서 데이터를 읽습니다.

### <a name="step-1"></a>1단계
기계 학습 스튜디오 창의 아래쪽에서 +NEW를 클릭하여 새 실험을 시작한 다음 EXPERIMENT, Blank Experiment를 선택합니다. 캔버스 위에서 기본 실험 이름을 선택하고 의미 있는 이름(예: 자전거 가격 예측)으로 바꿉니다.

### <a name="step-2"></a>2단계
데이터 집합의 팔레트에서 판독기 모듈 및 실험 캔버스의 왼쪽에 있는 모듈을 찾습니다. 실험 캔버스에 모듈을 끌어 놓습니다.
![][drag_reader]

### <a name="step-3"></a>3단계
판독기 모듈을 선택하고 속성 창을 완성합니다.

1. Azure SQL 데이터베이스를 데이터 원본으로 선택합니다.
2. 데이터베이스 서버 이름: 서버 이름을 입력합니다. [Azure Portal][Azure portal]을 사용하여 찾을 수 있습니다.

![][server_name]

1. 데이터베이스 이름: 방금 지정한 서버에서 데이터베이스의 이름을 입력합니다.
2. 서버 사용자 계정 이름: 데이터베이스에 대한 액세스 권한이 있는 계정의 사용자 이름을 입력합니다.
3. 서버 사용자 계정 암호: 지정된 사용자 계정에 대한 암호를 제공합니다.
4. 모든 서버 인증서 수락: 데이터를 읽기 전에 사이트 인증서 검토를 건너뛰려면 이 옵션을 사용합니다(보안 수준 낮음).
5. 데이터베이스 쿼리: 읽을 데이터를 설명하는 SQL 문을 입력합니다. 이 경우 다음 쿼리를 사용하여 Product 테이블에서 데이터를 읽습니다.

```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>4단계
1. 실험 캔버스에서 RUN을 클릭하여 실험을 실행합니다.
2. 실험이 완료되면 판독기 모듈에 녹색 확인 표시가 생겨 성공적으로 완료되었음을 나타냅니다. 오른쪽 위 모서리에서 실행 완료 상태도 확인됩니다.

![][run]

1. 가져온 데이터를 확인하려면 자동차 데이터 집합 아래에서 출력 포트를 클릭하고 Visualize를 선택합니다.

## <a name="create-train-and-score-a-model"></a>모델 만들기, 훈련 및 점수 매기기
이제 이 데이터 집합을 사용하여 다음을 수행할 수 있습니다.

* 모델 만들기: 데이터 처리 및 기능 정의
* 모델 교육: 학습 알고리즘 선택 및 적용
* 모델 점수 매기기 및 테스트: 새 자전거 가격 예측

![][model]

기계 학습 모델을.만들고, 훈련하고, 점수를 매기고 테스트하는 방법에 대해 알려면 [실험 만들기 자습서][Create experiment tutorial]를 사용합니다.

## <a name="write-data-to-azure-sql-data-warehouse"></a>Azure SQL 데이터 웨어하우스에 데이터 쓰기
결과 집합을 AdventureWorksDW 데이터베이스의 ProductPriceForecast 테이블에 기록합니다.

### <a name="step-1"></a>1단계
데이터 집합의 팔레트에서 기록기 모듈 및 실험 캔버스의 왼쪽에 있는 모듈을 찾습니다. 실험 캔버스에 모듈을 끌어 놓습니다.

![][drag_writer]

### <a name="step-2"></a>2단계
기록기 모듈을 선택하고 속성 창을 완성합니다.

1. Azure SQL 데이터베이스를 데이터 대상으로 선택합니다.
2. 데이터베이스 서버 이름: 서버 이름을 입력합니다. [Azure Portal][Azure portal]을 사용하여 찾을 수 있습니다.
3. 데이터베이스 이름: 방금 지정한 서버에서 데이터베이스의 이름을 입력합니다.
4. 서버 사용자 계정 이름: 데이터베이스에 대한 쓰기 권한이 있는 계정의 사용자 이름을 입력합니다.
5. 서버 사용자 계정 암호: 지정된 사용자 계정에 대한 암호를 제공합니다.
6. (안전하지 않은) 모든 서버 인증서 수락: 인증서를 보지 않으려는 경우 이 옵션을 선택합니다.
7. 저장될 열의 쉼표로 구분된 목록: 출력하려면 데이터 집합 또는 결과 열 목록을 제공합니다.
8. 데이터 테이블 이름: 데이터 테이블의 이름을 지정합니다.
9. 데이터 테이블 열의 쉼표로 구분된 목록: 새 테이블에 사용할 열 이름을 지정합니다. 열 이름은 원본 데이터 집합의 열과 다를 수 있지만, 출력 테이블에 대해 여기에서 정의한 동일한 열 수를 나열해야 합니다.
10. SQL Azure 작업당 작성된 행 수: 하나의 작업으로 SQL 데이터베이스에 기록되는 행의 수를 구성할 수 있습니다.

![][writer_properties]

### <a name="step-3"></a>3단계
1. 실험 캔버스에서 RUN을 클릭하여 실험을 실행합니다.
2. 실험이 완료되면 모든 모듈에 성공적으로 완료되었음을 나타내는 녹색 확인 표시가 표시됩니다.

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요][SQL Data Warehouse development overview]를 참조하세요.

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Create experiment tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction to machine learning on Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure portal]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

