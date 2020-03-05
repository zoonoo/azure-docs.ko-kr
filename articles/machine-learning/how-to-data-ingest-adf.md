---
title: Azure Data Factory를 사용 하 여 데이터 수집
titleSuffix: Azure Machine Learning
description: Azure Data Factory를 사용 하 여 데이터 수집 파이프라인을 빌드하는 방법을 알아봅니다.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274789"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure Data Factory를 사용 하 여 데이터 수집

이 문서에서는 Azure Data Factory (ADF)를 사용 하 여 데이터 수집 파이프라인을 빌드하는 방법에 대해 알아봅니다. 이 파이프라인은 Azure Machine Learning에 사용할 데이터를 수집 하는 데 사용 됩니다. Azure Data Factory를 사용 하 여 ETL (추출, 변환 및 로드) 데이터를 쉽게 추출할 수 있습니다. 데이터가 변환 되 고 저장소로 로드 되 면 machine learning 모델을 학습 하는 데 사용할 수 있습니다.

단순 데이터 변환은 [데이터 흐름과](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)같은 기본 ADF 작업 및 악기를 사용 하 여 처리할 수 있습니다. 더 복잡 한 시나리오의 경우 일부 사용자 지정 코드를 사용 하 여 데이터를 처리할 수 있습니다. 예를 들어 Python 또는 R 코드입니다.

수집 하는 동안 Azure Data Factory를 사용 하 여 데이터를 변환 하는 몇 가지 일반적인 기술이 있습니다. 각 기술에는 특정 사용 사례에 적합 한지 여부를 결정 하는 장단점이 있습니다.

| 방법 | 장점 | 단점 |
| ----- | ----- | ----- |
| ADF + Azure Functions | 짧은 대기 시간, 서버 리스 계산</br>상태 저장 함수</br>재사용 가능한 함수 | 단기 실행 처리에만 적합 |
| ADF + 사용자 지정 구성 요소 | 대규모 병렬 컴퓨팅</br>과도 한 알고리즘에 적합 | 코드를 실행 파일로 래핑</br>종속성 및 IO 처리의 복잡성 |
| ADF + Azure Databricks 노트북 | Apache Spark</br>네이티브 Python 환경 | 비용이 많이 들 수 있음</br>처음에는 클러스터를 만들고 대기 시간을 추가 합니다.

## <a name="adf-with-azure-functions"></a>Azure 함수를 사용 하는 ADF

![adf-함수](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions를 사용 하면 응용 프로그램 인프라에 대해 걱정 하지 않고 작은 코드 (함수)를 실행할 수 있습니다. 이 옵션에서는 Azure 함수로 래핑된 사용자 지정 Python 코드를 사용 하 여 데이터를 처리 합니다. 

함수는 [ADF Azure function 활동](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)을 사용 하 여 호출 됩니다. 이 방법은 간단한 데이터 변환에 유용한 옵션입니다. 

* 장점:
    * 데이터가 서버 리스 계산에서 상대적으로 짧은 대기 시간으로 처리 됩니다.
    * ADF 파이프라인은 정교한 데이터 변환 흐름을 구현할 수 있는 [내구성이 뛰어난 Azure 함수](/azure/azure-functions/durable/durable-functions-overview) 를 호출할 수 있습니다. 
    * 데이터 변환에 대 한 세부 정보는 다른 위치에서 다시 사용 하 고 호출할 수 있는 Azure 함수에서 추출 됩니다.
* 단점:
    * ADF와 함께 사용 하려면 Azure Functions를 만들어야 합니다.
    * Azure Functions은 단기 실행 데이터 처리에만 적합 합니다.

## <a name="adf-with-custom-component-activity"></a>사용자 지정 구성 요소 작업이 포함 된 ADF

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

이 옵션에서는 실행 파일에 래핑된 사용자 지정 Python 코드를 사용 하 여 데이터를 처리 합니다. [ADF 사용자 지정 구성 요소 작업](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)을 사용 하 여 호출 됩니다. 이 방법은 이전 기술 보다 많은 데이터에 적합 합니다.

* 장점:
    * 데이터는 대규모 병렬 및 고성능 컴퓨팅 기능을 제공 하는 [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) 풀에서 처리 됩니다.
    * 과도 한 알고리즘을 실행 하 고 많은 양의 데이터를 처리 하는 데 사용할 수 있습니다.
* 단점:
    * ADF와 함께 사용 하려면 Azure Batch 풀을 만들어야 합니다.
    * Python 코드를 실행 파일로 래핑하는 것과 관련 된 엔지니어링을 통해 종속성과 입/출력 매개 변수를 처리 하는 복잡성

## <a name="adf-with-azure-databricks-python-notebook"></a>Azure Databricks Python 노트북을 사용 하는 ADF

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 는 Microsoft 클라우드의 Apache Spark 기반 분석 플랫폼입니다.

이 방법에서는 데이터 변환이 Azure Databricks 클러스터에서 실행 되는 [Python 노트북](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)에 의해 수행 됩니다. 이는 Azure Databricks 서비스의 전체 기능을 활용 하는 가장 일반적인 방법입니다. 대규모로 분산 된 데이터 처리를 위해 설계 되었습니다.

* 장점:
    * 데이터는 Apache Spark 환경에서 백업 하는 가장 강력한 데이터 처리 Azure 서비스에서 변환 됩니다.
    * TensorFlow, PyTorch 및 scikit를 포함 한 데이터 과학 프레임 워크 및 라이브러리와 함께 Python의 기본 지원-배우기
    * Python 코드를 함수 또는 실행 가능한 모듈로 래핑할 필요는 없습니다. 코드는 그대로 작동 합니다.
* 단점:
    * ADF와 함께 사용 하려면 Azure Databricks 인프라를 만들어야 합니다.
    * Azure Databricks 구성에 따라 비용이 많이 들 수 있습니다.
    * "콜드" 모드에서 계산 클러스터를 회전 하면 솔루션에 대 한 대기 시간이 길어질 수 있는 시간이 소요 됩니다. 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Azure Machine Learning 파이프라인에서 데이터 사용

![aml-데이터 집합](media/how-to-data-ingest-adf/aml-dataset.png)

ADF 파이프라인에서 변환 된 데이터는 데이터 저장소 (예: Azure Blob)에 저장 됩니다. 데이터 [저장소](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) 및 데이터 [집합](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)을 사용 하 여이 데이터에 액세스할 수 Azure Machine Learning.

ADF 파이프라인이 실행 될 때마다 데이터가 저장소의 다른 위치에 저장 됩니다. Azure Machine Learning에 위치를 전달 하기 위해 ADF 파이프라인은 Azure Machine Learning 파이프라인을 호출 합니다. ML 파이프라인을 호출 하는 경우 데이터 위치 및 실행 ID는 매개 변수로 전송 됩니다. 그런 다음 ML 파이프라인은 데이터 위치를 사용 하 여 데이터 저장소/데이터 집합을 만들 수 있습니다. 

> [!TIP]
> 데이터 집합은 [버전 관리를 지원](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)하므로 ML 파이프라인은 ADF 파이프라인에서 최신 데이터를 가리키는 새 버전의 데이터 집합을 등록할 수 있습니다.

데이터 저장소 또는 데이터 집합을 통해 데이터에 액세스할 수 있게 되 면이를 사용 하 여 ML 모델을 학습 시킬 수 있습니다. 학습 프로세스는 ADF에서 호출 되는 동일한 ML 파이프라인의 일부일 수 있습니다. 또는 Jupyter 노트북의 실험과 같은 별도의 프로세스 일 수도 있습니다.

데이터 집합은 버전 관리를 지원 하 고 파이프라인에서 실행 되는 각 실행은 새 버전을 만들기 때문에 모델을 학습 하는 데 사용 된 데이터 버전을 쉽게 파악할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory에서 Databricks 노트북 실행](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Azure Storage 서비스에서 데이터 액세스](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Azure Machine Learning의 데이터 집합을 사용 하 여 모델 학습](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [데이터 수집 파이프라인에 대 한 DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

