---
title: 자동 기계 학습 (AutoML) 모델의 데이터를 처리 하기 위해 데이터 흐름 사용
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
ms.openlocfilehash: e8352b687a3cdfac7ea2a819e1217906598a6837
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563269"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>자동 기계 학습 (AutoML) 모델에서 데이터 흐름을 사용 하 여 데이터 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

자동 기계 학습 (AutoML)은 분류, 회귀 및 시계열 예측에 대해 지정 하는 대상 메트릭을 사용 하 여 자동으로 최상의 모델을 학습 하 고 조정 하 고 얻을 수 있도록 기계 학습 프로젝트에서 채택 합니다. 

한 가지 문제는 데이터 웨어하우스의 원시 데이터 또는 트랜잭션 데이터베이스는 10GB와 같은 방대한 데이터 집합이 고, 큰 데이터 집합은 모델을 학습 하는 데 시간이 더 오래 걸릴 수 있기 때문에 Azure Machine Learning 모델을 학습 하기 전에 데이터 처리 최적화를 사용 하는 것이 좋습니다. 이 자습서에서는 ADF를 사용 하 여 데이터 집합을 Azure Machine Learning 데이터 집합에 대 한 parquet 파일에 분할 하는 방법을 설명 합니다. 

AutoML (자동화 된 기계 학습) 프로젝트에서 다음 세 가지 데이터 처리 시나리오를 적용 합니다.

* 모델 학습 전에 대량 데이터를 parquet 파일로 분할 합니다. 

     [Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) 은 일반적으로 모델 학습 전에 데이터를 처리 하는 데 사용 됩니다. Pandas data 프레임은 1GB 미만의 데이터 크기에 적합 하지만 데이터가 1GB 보다 크면 데이터 프레임 속도가 Pandas 데이터를 처리 하는 속도가 느려집니다 .이 경우에도 메모리 부족 오류가 발생 합니다. [Parquet 파일](https://parquet.apache.org/) 형식은 이진 칼럼 형식 이므로 기계 학습에 권장 됩니다.
    
    Azure 데이터 팩터리 매핑 데이터 흐름은 코드 없는 데이터 엔지니어에 게 제공 되는 데이터 변환을 시각적으로 디자인 합니다. 파이프라인이 확장 된 Spark 클러스터를 사용 하기 때문에 대량 데이터를 처리 하는 것이 강력 합니다.

* 학습 데이터 집합 및 테스트 데이터 집합을 분할 합니다.
    
    학습 데이터 집합은 학습 모델에 사용 되며, 테스트 데이터 집합은 machine learning 프로젝트에서 모델을 평가 하는 데 사용 됩니다. 데이터 흐름 조건부 분할 활동은 학습 데이터와 테스트 데이터를 분할 합니다. 

* 정규화 되지 않은 데이터를 제거 합니다.

    행이 0 개 있는 parquet 파일 등의 정규화 되지 않은 데이터를 제거 하려고 할 수 있습니다. 이 자습서에서는 집계 활동을 사용 하 여 행 수를 가져오며, 행 개수는 정규화 되지 않은 데이터를 제거 하는 조건입니다. 


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

데이터 흐름은 Azure SQL Database 테이블을 parquet 파일 형식으로 변환 합니다. 

**원본 데이터 집합**: Azure SQL Database 트랜잭션 테이블

**싱크 데이터 집합**: Parquet 형식의 Blob storage


## <a name="remove-unqualified-data-based-on-row-count"></a>행 개수를 기준으로 정규화 되지 않은 데이터 제거

행 개수를 2 개 미만으로 제거 한다고 가정해 보겠습니다. 

1. 집계 활동을 사용 하 여 행 수에 대 한 개수 (1)를 사용 하 여 Col2 및 **집계** 를 기준으로 **그룹화 하 여** 행 수를 가져옵니다. 

    ![행 수를 가져오도록 집계 작업 구성](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. 싱크 작업을 사용 하 고 **싱크 탭에서** 캐시로 **싱크 유형을** 선택한 다음 **설정** 탭의 **키 열** 드롭다운 목록에서 원하는 열을 선택 합니다. 

    ![캐시 된 싱크에 있는 행 수를 가져오도록 CacheSink 활동을 구성 합니다.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. 파생 열 작업을 사용 하 여 원본 스트림에 행 개수 열을 추가 합니다. **파생 열의 설정** 탭에서 CacheSink # lookup 식을 사용 하 여 SinkCache에서 행 개수를 가져옵니다.
    ![파생 열 작업을 구성 하 여 원본 1의 행 수를 추가 합니다.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. 조건부 분할 작업을 사용 하 여 정규화 되지 않은 데이터를 제거 합니다. 이 예에서는 Col2 열을 기반으로 하는 행 수를 계산 하 고, 조건은 2 보다 작은 행 개수를 제거 하는 것 이므로 두 행 (ID = 2 및 ID = 7)이 제거 됩니다. 데이터 관리를 위해 정규화 되지 않은 데이터를 blob 저장소에 저장 합니다. 

    ![조건부 분할 작업을 구성 하 여 2 보다 크거나 같은 데이터 가져오기](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    이후 단계에서 원본 원본에 사용 될 수 있는 행 수를 가져오기 위한 새 원본을 만듭니다. 
>    *    성능 관점에서 CacheSink를 사용 합니다. 

## <a name="split-training-data-and-test-data"></a>학습 데이터 및 테스트 데이터 분할 

1. 각 파티션에 대 한 학습 데이터와 테스트 데이터를 분할 하려고 합니다. 이 예제에서 Col2의 동일한 값에 대해 상위 2 개 행을 테스트 데이터로, rest 행을 학습 데이터로 가져옵니다. 

    창 활동을 사용 하 여 각 파티션에 대해 하나의 열 행 번호를 추가 합니다. **초과** 탭에서 파티션 (이 자습서에서는 Col2에 대해 파티션)을 선택 하 고, 정렬 탭 (이 자습서에서는 ID를 기준으로 **정렬** )을 지정 하 고, **창 열** 탭에서 각 파티션에 대 한 행 번호로 하나의 열을 추가 합니다. 
    ![행 번호가 있는 새 열 하나를 추가 하도록 창 작업 구성](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. 조건부 분할 작업을 사용 하 여 각 파티션의 상위 2 개 행을 테스트 데이터 집합으로 분할 하 고 나머지 행을 학습 데이터 집합으로 분할 합니다. **조건부 분할 설정** 탭에서 LesserOrEqual (RowNum, 2) 식을 조건으로 사용 합니다. 

    ![현재 데이터 집합을 학습 데이터 집합 및 테스트 데이터 집합으로 분할 하는 조건부 분할 작업 구성](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Parquet format을 사용 하 여 학습 데이터 집합 및 테스트 데이터 집합 분할

1. **최적화** 탭에서 싱크 활동을 사용 하 여 파티션당 **고유한 값** 을 사용 하 여 파티션에 대 한 열 키로 열을 설정 합니다. 
    ![학습 데이터 집합의 파티션을 설정 하도록 싱크 작업 구성](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    전체 파이프라인 논리를 살펴보겠습니다.
    ![전체 파이프라인의 논리](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.
