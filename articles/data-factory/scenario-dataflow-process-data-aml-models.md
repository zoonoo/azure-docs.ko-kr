---
title: 자동 기계 학습 (AutoML) 모델에서 데이터 흐름을 사용 하 여 데이터 처리
description: Azure Data Factory 데이터 흐름을 사용 하 여 자동 기계 학습 (AutoML) 모델의 데이터를 처리 하는 방법을 알아봅니다.
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595382"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>데이터 흐름을 사용 하 여 자동화 된 기계 학습 모델에서 데이터 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

자동 기계 학습 (AutoML)은 분류, 회귀 및 시계열 예측에 대해 지정 하는 대상 메트릭을 사용 하 여 자동으로 최상의 모델을 학습 하 고 조정 하 고 얻을 수 있도록 기계 학습 프로젝트에서 채택 합니다.

AutoML에 대 한 한 가지 문제는 데이터 웨어하우스의 원시 데이터 나 트랜잭션 데이터베이스는 엄청난 데이터 집합 (10gb)이 될 수 있다는 것입니다. 큰 데이터 집합을 사용 하려면 모델을 학습 하는 데 시간이 더 오래 걸릴 수 있으므로 Azure Machine Learning 모델을 학습 하기 전에 데이터 처리를 최적화 하는 것이 좋습니다. 이 자습서에서는 Azure Data Factory를 사용 하 여 데이터 집합을 Machine Learning 데이터 집합의 AutoML 파일로 분할 하는 방법을 설명 합니다.

AutoML 프로젝트는 다음과 같은 세 가지 데이터 처리 시나리오를 포함 합니다.

* 모델을 학습 하기 전에 대량 데이터를 AutoML 파일로 분할 합니다.

     [Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) 은 모델을 학습 하기 전에 데이터를 처리 하는 데 주로 사용 됩니다. Pandas 데이터 프레임은 1gb 미만의 데이터 크기에 적합 하지만 데이터가 1gb 보다 큰 경우 Pandas 데이터 프레임에서 데이터를 처리 하는 속도가 느려집니다. 경우에 따라 메모리 부족 오류 메시지가 나타날 수도 있습니다. 이진 칼럼 형식 이므로 기계 학습에 [Parquet 파일](https://parquet.apache.org/) 형식을 사용 하는 것이 좋습니다.
    
     데이터 흐름에 대 한 Data Factory 매핑은 데이터 엔지니어가 코드를 작성 하는 데 사용 되는 데이터 변환을 시각적으로 디자인 합니다. 파이프라인에서 확장 된 Spark 클러스터를 사용 하기 때문에 데이터 흐름을 매핑하면 대량 데이터를 효과적으로 처리할 수 있습니다.

* 학습 데이터 집합과 테스트 데이터 집합을 분할 합니다.
    
    학습 데이터 집합은 학습 모델에 사용 됩니다. 테스트 데이터 집합은 machine learning 프로젝트에서 모델을 평가 하는 데 사용 됩니다. 데이터 흐름을 매핑하기 위한 조건부 분할 활동은 학습 데이터와 테스트 데이터를 분할 합니다.

* 정규화 되지 않은 데이터를 제거 합니다.

    행이 없는 Parquet 파일 처럼 정규화 되지 않은 데이터를 제거 하는 것이 좋습니다. 이 자습서에서는 집계 활동을 사용 하 여 행 수를 가져옵니다. 행 개수는 정규화 되지 않은 데이터를 제거 하는 조건입니다.

## <a name="preparation"></a>준비

다음 Azure SQL Database 표를 사용 합니다.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Parquet로 데이터 형식 변환

다음 데이터 흐름은 SQL Database 테이블을 Parquet 파일 형식으로 변환 합니다.

- **원본 데이터 집합**: SQL Database의 트랜잭션 테이블
- **싱크 데이터 집합**: Parquet 형식의 Blob 저장소입니다.

## <a name="remove-unqualified-data-based-on-row-count"></a>행 개수를 기준으로 정규화 되지 않은 데이터 제거

2 개 미만의 행 개수를 제거 해야 한다고 가정해 보겠습니다.

1. 집계 활동을 사용 하 여 행 수를 가져올 수 있습니다. Col2를 기준으로 **그룹화** 된를 사용 하 고  `count(1)` 행 수에 대 한 집계를 사용 합니다.

    ![행 수를 가져오는 집계 활동을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. 싱크 활동을 사용 하 여 **싱크 탭에서** **캐시로** **싱크 유형을** 선택 합니다. 그런 다음 **설정** 탭의 **키 열** 드롭다운 목록에서 원하는 열을 선택 합니다.

    ![캐시 된 싱크에 있는 행의 수를 가져오기 위해 CacheSink 활동을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. 파생 열 작업을 사용 하 여 원본 스트림에 행 개수 열을 추가 합니다. **파생 열의 설정** 탭에서 `CacheSink#lookup` 식을 사용 하 여 cachesink의 행 수를 가져옵니다.

    ![Source1의 행 개수를 추가 하도록 파생 열 작업을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. 조건부 분할 작업을 사용 하 여 정규화 되지 않은 데이터를 제거 합니다. 이 예제에서 행 수는 Col2 열을 기반으로 합니다. 두 개 미만의 행 개수를 제거 하는 것이 조건 이므로 두 행 (ID = 2 및 ID = 7)이 제거 됩니다. 데이터 관리를 위해 정규화 되지 않은 데이터를 blob 저장소에 저장 합니다.

    ![2 보다 크거나 같은 데이터를 가져오기 위해 조건부 분할 작업을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * 이후 단계에서 원본 원본에 사용 되는 행 수를 얻기 위한 새 원본을 만듭니다.
>    * 성능 관점에서 CacheSink를 사용 합니다.

## <a name="split-training-data-and-test-data"></a>학습 데이터 및 테스트 데이터 분할

각 파티션에 대 한 학습 데이터와 테스트 데이터를 분할 하려고 합니다. 이 예제에서 Col2의 동일한 값에 대해 상위 두 행을 테스트 데이터로 가져오고 나머지 행을 학습 데이터로 가져옵니다.

1. 창 활동을 사용 하 여 각 파티션에 대해 하나의 열로 된 행 번호를 추가 합니다. **초과** 탭에서 파티션에 대 한 열을 선택 합니다. 이 자습서에서는 Col2를 분할 합니다. **정렬** 탭에서 순서를 지정 합니다 .이 자습서에서는 ID를 기준으로 합니다. **창 열** 탭에서 순서를 지정 하 여 각 파티션에 대 한 행 번호로 하나의 열을 추가 합니다.

    ![행 번호가 있는 새 열 하나를 추가 하는 창 작업을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. 조건부 분할 작업을 사용 하 여 각 파티션의 상위 두 행을 테스트 데이터 집합으로 분할 하 고 나머지 행을 학습 데이터 집합으로 분할 합니다. **조건부 분할 설정** 탭에서 식을 `lesserOrEqual(RowNum,2)` 조건으로 사용 합니다.

    ![현재 데이터 집합을 학습 데이터 집합 및 테스트 데이터 집합으로 분할 하는 조건부 분할 작업을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Parquet format을 사용 하 여 학습 및 테스트 데이터 집합 분할

싱크 활동을 사용 하 여 **최적화** 탭에서 **파티션당 고유한 값** 을 사용 하 여 파티션에 대 한 열 키로 열을 설정 합니다.

![학습 데이터 집합의 파티션을 설정 하도록 싱크 작업을 구성 하는 방법을 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

전체 파이프라인 논리를 살펴보겠습니다.

![전체 파이프라인의 논리를 보여 주는 스크린샷](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>다음 단계

데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.
