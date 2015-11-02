<properties
   pageTitle="Azure 기계 학습을 사용한 데이터 분석 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스와 함께 Azure 기계 학습 사용을 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="sahajs"/>

# Azure 기계 학습을 사용하여 데이터 분석
이 자습서는 Azure SQL 데이터 웨어하우스에 데이터를 사용하여 Azure 기계 학습으로 예측 기계 학습 모델을 구축하는 방법을 보여 줍니다. 이 자습서에서는 AdventureWorksDW 데이터베이스를 사용하고 대상 마케팅 캠페인을 구축하기 위해 잠재적인 자전거 구매자 여부로 Adventure Works 자전거 매장의 고객을 분류합니다.


## 필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

- AdventureWorksDW 샘플 데이터베이스로 SQL 데이터 웨어하우스.

[SQL 데이터 웨어하우스에 만들기][]는 샘플 데이터로 데이터베이스를 프로비전하는 방법을 보여 줍니다. SQL 데이터 웨어하우스 데이터베이스는 있지만 샘플 데이터가 없는 경우 [샘플 데이터를 수동으로 로드][]할 수 있습니다.


## 1단계: 데이터 가져오기 
AdventureWorksDW 데이터베이스의 dbo.vTargetMail 보기에서 데이터를 읽습니다.

1. [Azure 기계 학습 스튜디오][]에 로그인하고 내 실험을 클릭합니다.
2. **+새로 만들기**를 클릭하고 **빈 실험**을 선택합니다.
3. 실험: 대상 마케팅에 대한 이름을 입력합니다.
4. 모듈 창에서 **판독기** 모듈을 캔버스로 끌어서 놓습니다.
5. 속성 창에서 SQL 데이터 웨어하우스 데이터베이스에 대한 세부 정보를 지정합니다. 
6. 관련 데이터를 읽을 데이터베이스 쿼리를 지정합니다.

```
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
7. 실험 캔버스 아래에서 **실행**을 클릭하여 실험을 실행합니다.
8. 판독기 모듈 아래쪽에서 출력 포트를 클릭하고 **시각화**를 선택하여 가져온 데이터를 확인합니다.



## 2단계: 데이터 정리
모델에 관련되지 않은 일부 열을 삭제합니다.

1. **프로젝트 열** 모듈을 캔버스로 끌어서 놓습니다.
2. 속성 창에서 **열 선택기 시작**을 클릭하여 삭제하려는 열을 지정합니다.
3. 다음 두 열을 제외합니다. CustomerAlternateKey 및 GeographyKey



## 3단계: 모델 작성
데이터를 80-20으로 분할합니다. 80%는 기계 학습 모델을 학습하고 20%는 모델을 테스트합니다. 이진 분류 문제에 대해 "2클래스" 알고리즘을 활용합니다.

1. **분할** 모듈을 캔버스로 끌어서 놓습니다.
2. 속성 창에서 첫 번째 출력 데이터 집합의 행 분수에 대해 0.8을 입력합니다.
3. **2클래스 향상된 의사 결정 트리** 모듈을 캔버스로 끌어서 놓습니다.
4. **모델 학습** 모듈을 캔버스로 끌어서 놓고 첫 번째 입력: ML 알고리즘을 지정합니다. 두 번째 입력: 알고리즘을 학습할 데이터입니다.
5. 속성 창에서 **열 선택기 시작**을 클릭하여 모델이 예측하려는 열: BikeBuyer를 지정합니다.


## 4단계: 모델 점수 매기기
이제 모델이 테스트 데이터를 수행하는 방법을 테스트합니다. 더 잘 수행하는 알고리즘을 확인하도록 다른 알고리즘을 선택하여 비교합니다.

1. **모델 점수 매기기** 모듈을 캔버스로 끌어서 놓습니다. 첫 번째 입력: 학습된 모델 두 번째 입력: 테스트 데이터
2. **2클래스 Bayes 지점 컴퓨터**를 실험 캔버스로 끌어서 놓습니다. 2클래스 향상된 의사 결정 트리와 비교하여 이 알고리즘이 수행하는 방법을 비교합니다.
3. 캔버스에서 모델 학습 및 모델 점수 매기기 모듈을 복사하고 붙여 넣습니다.
4. **모델 평가** 모듈을 캔버스로 끌어서 놓아 두 알고리즘을 비교합니다.
5. 실험을 **실행합니다**.
6. 모델 평가 모듈의 아래쪽에서 출력 포트를 클릭하고 시각화를 클릭합니다.


제공된 메트릭은 ROC 곡선, 정밀도-리콜 다이어그램 및 리프트 곡선입니다. 이러한 메트릭을 살펴보면 첫 번째 모델이 두 번째보다 더 잘 실행하는 것을 볼 수 있습니다. 첫 번째 모델이 예측하는 것을 보려면 모델 점수 매기기의 출력 포트를 클릭하고 시각화를 클릭합니다.


테스트 데이터 집합에 두 개의 열이 추가됩니다.

- 점수가 매겨진 확률: 고객이 자전거 구매자일 가능성입니다.
- 점수가 매겨진 레이블: 모델에 의해 분류가 실행되었습니다. – 자전거 구매자(1) 혹은 아님(0) 레이블 지정에 대한 확률 임계값은 50%로 설정되고 조정할 수 있습니다.

점수가 매겨진 레이블(예측)로 열 BikeBuyer(실제) 비교를 통해 모델이 얼마나 잘 실행했는지 확인할 수 있습니다. 다음 단계로 이 모델을 사용하여 새 고객에 대한 예측을 수행하고 이 모델을 웹 서비스로 게시하거나 SQL 데이터 웨어하우스에 결과를 다시 작성할 수 있습니다.

더 자세히 알아보려면 [Azure의 기계 학습 소개][]를 참조하세요.



<!--Article references-->
[Azure 기계 학습 스튜디오]: https://studio.azureml.net/
[Azure의 기계 학습 소개]: ../machine-learning/machine-learning-what-is-machine-learning.md
[샘플 데이터를 수동으로 로드]: ./sql-data-warehouse-get-started-manually-load-samples.md
[SQL 데이터 웨어하우스에 만들기]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO4-->