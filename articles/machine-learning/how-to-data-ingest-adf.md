---
title: Azure 데이터 팩터리의 데이터 수집
titleSuffix: Azure Machine Learning
description: Azure 데이터 팩터리에서 데이터 수집 파이프라인을 빌드하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274789"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure 데이터 팩터리의 데이터 수집

이 문서에서는 ADF(Azure Data Factory)를 사용하여 데이터 수집 파이프라인을 빌드하는 방법을 알아봅니다. 이 파이프라인은 Azure 기계 학습에 사용할 데이터를 수집하는 데 사용됩니다. Azure 데이터 팩터리에서는 ETL(데이터)을 쉽게 추출, 변환 및 로드할 수 있습니다. 데이터가 변환되고 저장소에 로드되면 기계 학습 모델을 학습하는 데 사용할 수 있습니다.

간단한 데이터 변환은 네이티브 ADF 활동 및 [데이터 흐름과](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)같은 계측기로 처리할 수 있습니다. 더 복잡한 시나리오의 경우 일부 사용자 지정 코드로 데이터를 처리할 수 있습니다. 예를 들어, 파이썬 또는 R 코드.

Azure Data Factory를 사용하여 수집 하는 동안 데이터를 변환 하는 몇 가지 일반적인 기술이 있습니다. 각 기술에는 특정 사용 사례에 적합한지 여부를 결정하는 장단점이 있습니다.

| 방법 | 장점 | 단점 |
| ----- | ----- | ----- |
| ADF + Azure 함수 | 낮은 대기 시간, 서버리스 컴퓨팅</br>상태 풀 함수</br>재사용 가능한 기능 | 짧은 실행 처리에만 적합 |
| ADF + 사용자 지정 구성 요소 | 대규모 병렬 컴퓨팅</br>무거운 알고리즘에 적합 | 코드 래핑을 실행 수로</br>종속성 및 IO 처리의 복잡성 |
| ADF + Azure 데이터브릭스 노트북 | Apache Spark</br>네이티브 파이썬 환경 | 비쌀 수 있습니다.</br>클러스터를 처음 만드는 데는 시간이 걸리고 대기 시간이 추가됩니다.

## <a name="adf-with-azure-functions"></a>Azure 기능이 있는 ADF

![adf 기능](media/how-to-data-ingest-adf/adf-function.png)

Azure 함수를 사용하면 응용 프로그램 인프라에 대해 걱정할 필요가 없이 작은 코드(함수)를 실행할 수 있습니다. 이 옵션에서 데이터는 Azure Function에 래핑된 사용자 지정 파이썬 코드로 처리됩니다. 

함수는 [ADF Azure Function 활동으로](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)호출됩니다. 이 방법은 간단한 데이터 변환에 적합합니다. 

* 장점:
    * 데이터는 비교적 짧은 대기 시간으로 서버리스 컴퓨팅에서 처리됩니다.
    * ADF 파이프라인은 정교한 데이터 변환 흐름을 구현할 수 있는 [지속성 Azure 함수를](/azure/azure-functions/durable/durable-functions-overview) 호출할 수 있습니다. 
    * 데이터 변환의 세부 정보는 다른 위치에서 다시 사용하고 호출할 수 있는 Azure Function에 의해 추상화됩니다.
* 단점:
    * ADF와 함께 사용하기 전에 Azure 함수를 만들어야 합니다.
    * Azure 함수는 짧은 실행 데이터 처리에만 적합합니다.

## <a name="adf-with-custom-component-activity"></a>사용자 지정 구성 요소 활동이 있는 ADF

![adf-사용자 정의 구성 요소](media/how-to-data-ingest-adf/adf-customcomponent.png)

이 옵션에서 데이터는 실행 수로 래핑 된 사용자 정의 파이썬 코드로 처리됩니다. [ADF 사용자 지정 구성 요소 활동으로](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)호출됩니다. 이 방법은 이전 기술보다 큰 데이터에 더 적합합니다.

* 장점:
    * 데이터는 대규모 병렬 및 고성능 컴퓨팅을 제공하는 [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) 풀에서 처리됩니다.
    * 무거운 알고리즘을 실행하고 상당한 양의 데이터를 처리하는 데 사용할 수 있습니다.
* 단점:
    * ADF와 함께 사용하기 전에 Azure 일괄 처리 풀을 만들어야 합니다.
    * 파이썬 코드를 실행 형으로 래핑하는 데 관련된 엔지니어링을 통해. 종속성 및 입력/출력 매개 변수 처리의 복잡성

## <a name="adf-with-azure-databricks-python-notebook"></a>Azure 데이터브릭스 파이썬 노트북을 갖춘 ADF

![adf-데이터 브릭](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks는](https://azure.microsoft.com/services/databricks/) Microsoft 클라우드의 아파치 스파크 기반 분석 플랫폼입니다.

이 기술에서는 Azure Databricks 클러스터에서 실행되는 [Python 노트북에서](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)데이터 변환을 수행합니다. Azure Databricks 서비스의 모든 권한을 활용하는 가장 일반적인 접근 방식일 수 있습니다. 대규모 분산 데이터 처리를 위해 설계되었습니다.

* 장점:
    * 데이터는 아파치 스파크 환경에 의해 백업되는 가장 강력한 데이터 처리 Azure 서비스에 변환됩니다.
    * 텐서플로우, 파이토치, 사이킷 학습을 포함한 데이터 과학 프레임워크 및 라이브러리와 함께 파이썬의 기본 지원
    * 파이썬 코드를 함수 또는 실행 모듈로 래핑할 필요가 없습니다. 코드는 있는 대로 작동합니다.
* 단점:
    * ADF와 함께 사용하기 전에 Azure Databricks 인프라를 만들어야 합니다.
    * Azure 데이터 브릭 구성에 따라 비용이 많이 들 수 있습니다.
    * "콜드" 모드에서 계산 클러스터를 회전하는 데 는 시간이 걸리며 솔루션에 높은 대기 시간이 필요합니다. 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Azure 기계 학습 파이프라인에서 데이터 사용

![aml 데이터 집합](media/how-to-data-ingest-adf/aml-dataset.png)

ADF 파이프라인에서 변환된 데이터는 데이터 저장소(예: Azure Blob)에 저장됩니다. Azure 기계 학습은 [데이터 스토어](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) 및 데이터 집합을 사용하여 이 데이터에 액세스할 수 [있습니다.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

ADF 파이프라인이 실행될 때마다 데이터는 저장소의 다른 위치에 저장됩니다. Azure 기계 학습에 위치를 전달하기 위해 ADF 파이프라인은 Azure 기계 학습 파이프라인을 호출합니다. ML 파이프라인을 호출할 때 데이터 위치 및 실행 ID는 매개 변수로 전송됩니다. 그런 다음 ML 파이프라인은 데이터 위치를 사용하여 데이터 스토어/데이터 집합을 만들 수 있습니다. 

> [!TIP]
> 데이터 [집합은 버전 전환을 지원하므로](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)ML 파이프라인은 ADF 파이프라인의 최신 데이터를 가리키는 새 버전의 데이터 집합을 등록할 수 있습니다.

데이터 스토어 또는 데이터 집합을 통해 데이터에 액세스할 수 있게 되면 ML 모델을 학습하는 데 사용할 수 있습니다. 교육 프로세스는 ADF에서 호출되는 동일한 ML 파이프라인의 일부일 수 있습니다. 또는 Jupyter 노트북에서 실험과 같은 별도의 프로세스일 수 있습니다.

데이터 집합은 버전 버전을 지원하고 파이프라인에서 각 실행은 새 버전을 생성하므로 모델을 학습하는 데 사용된 데이터의 버전을 쉽게 이해할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 팩터리에서 데이터 브릭 노트북 실행](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Azure 저장소 서비스의 데이터 액세스](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Azure 기계 학습에서 데이터 집합을 사용하여 모델 학습](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [데이터 수집 파이프라인에 대한 데브옵스](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

