---
title: Azure 기계 학습 파이프라인 실행
description: Azure 데이터 팩터리 파이프라인에서 Azure 기계 학습 파이프라인을 실행하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419003"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure 데이터 팩터리에서 Azure 기계 학습 파이프라인 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 데이터 팩터리 파이프라인의 단계로 Azure 기계 학습 파이프라인을 실행합니다. 기계 학습 실행 파이프라인 활동을 사용하면 가능한 대출 불이행을 식별하고, 감정을 결정하고, 고객 행동 패턴을 분석하는 등의 일괄 처리 예측 시나리오를 사용할 수 있습니다.

아래 비디오는 이 기능에 대한 6분 짜리 소개 및 데모를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>구문

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | 파이프라인의 작업 이름입니다. | String | 예
type | 활동 유형은 'AzureML실행 파이프라인'입니다. | String | 예
linkedServiceName | Azure 기계 학습에 연결된 서비스 | 연결된 서비스 참조 | 예
ml파이프라인Id | 게시된 Azure 기계 학습 파이프라인의 ID | 문자열(또는 resultType 문자열이 있는 식) | 예
실험 이름 | 기계 학습 파이프라인 실행의 기록 실험 이름 실행 | 문자열(또는 resultType 문자열이 있는 식) | 예
ml파이프라인매개 변수 | 키, 값 쌍게시된 Azure 기계 학습 파이프라인 끝점에 전달 합니다. 키는 게시된 기계 학습 파이프라인에 정의된 파이프라인 매개 변수의 이름과 일치해야 합니다. | 키 값 쌍이 있는 개체(또는 resultType 개체가 있는 식) | 예
ml부모RunId | 상위 Azure 기계 학습 파이프라인 실행 ID | 문자열(또는 resultType 문자열이 있는 식) | 예
continueOnStep 실패 | 단계가 실패할 경우 기계 학습 파이프라인에서 다른 단계의 실행을 계속할지 여부 | boolean | 예

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요.

* [데이터 흐름 활동 실행](control-flow-execute-data-flow-activity.md)
* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [돼지 활동](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [하두롭 스트리밍 활동](transform-data-using-hadoop-streaming.md)
* [스파크 액티비티](transform-data-using-spark.md)
* [.NET 사용자 지정 활동](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 활동](transform-data-using-stored-procedure.md)
