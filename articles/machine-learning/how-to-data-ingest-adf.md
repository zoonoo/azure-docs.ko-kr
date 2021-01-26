---
title: Azure Data Factory로 데이터 수집
titleSuffix: Azure Machine Learning
description: Azure Data Factory를 사용 하 여 데이터 수집 파이프라인을 구축 하는 데 사용할 수 있는 옵션과 각각의 이점을 알아보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796156"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure Data Factory로 데이터 수집

이 문서에서는 [Azure Data Factory](../data-factory/introduction.md)를 사용 하 여 데이터 수집 파이프라인을 구축 하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이 Azure Data Factory 파이프라인은 [Azure Machine Learning](overview-what-is-azure-ml.md)에 사용할 데이터를 수집 하는 데 사용 됩니다. Data Factory를 사용 하 여 ETL (추출, 변환 및 로드) 데이터를 쉽게 추출할 수 있습니다. 데이터가 변환 되 고 저장소로 로드 되 면 Azure Machine Learning에서 기계 학습 모델을 학습 하는 데 사용할 수 있습니다.

단순 데이터 변환은 [데이터 흐름과](../data-factory/control-flow-execute-data-flow-activity.md)같은 기본 Data Factory 활동 및 악기를 사용 하 여 처리할 수 있습니다. 더 복잡 한 시나리오의 경우 일부 사용자 지정 코드를 사용 하 여 데이터를 처리할 수 있습니다. 예를 들어 Python 또는 R 코드입니다.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>데이터 수집 파이프라인 Azure Data Factory 비교 
수집 하는 동안 Data Factory를 사용 하 여 데이터를 변환 하는 몇 가지 일반적인 기술이 있습니다. 각 기술은 특정 사용 사례에 적합 한지 여부를 결정 하는 데 도움이 되는 장단점이 있습니다.

| 방법 | 장점 | 단점 |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> 짧은 대기 시간, 서버 리스 계산<li>상태 저장 함수<li>재사용 가능한 함수 | 단기 실행 처리에만 적합 |
| Data Factory + 사용자 지정 구성 요소 | <li>대규모 병렬 컴퓨팅<li>과도 한 알고리즘에 적합 | <li>코드를 실행 파일로 래핑 해야 합니다.<li>종속성 및 IO 처리의 복잡성 |
| Data Factory + Azure Databricks 노트북 |<li> Apache Spark<li>네이티브 Python 환경 |<li>비용이 많이 들 수 있음<li>처음에는 클러스터를 만들고 대기 시간을 추가 합니다.

## <a name="azure-data-factory-with-azure-functions"></a>Azure 함수를 사용 하 여 Azure Data Factory

Azure Functions를 사용 하면 응용 프로그램 인프라에 대해 걱정 하지 않고 작은 코드 (함수)를 실행할 수 있습니다. 이 옵션에서는 Azure 함수로 래핑된 사용자 지정 Python 코드를 사용 하 여 데이터를 처리 합니다. 

함수는 [Azure Data Factory Azure function 활동](../data-factory/control-flow-azure-function-activity.md)을 사용 하 여 호출 됩니다. 이 방법은 간단한 데이터 변환에 유용한 옵션입니다. 

![다이어그램은 Azure Function 및 ML 파이프라인 실행, Azure Machine Learning 파이프라인, 학습 모델 및 원시 데이터와 준비 된 데이터와 상호 작용 하는 방법에 대 한 Azure Data Factory 파이프라인을 보여 줍니다.](media/how-to-data-ingest-adf/adf-function.png)



* 장점
    * 데이터가 서버 리스 계산에서 상대적으로 짧은 대기 시간으로 처리 됩니다.
    * Data Factory 파이프라인은 정교한 데이터 변환 흐름을 구현할 수 있는 [내구성이 뛰어난 Azure 함수](../azure-functions/durable/durable-functions-overview.md) 를 호출할 수 있습니다. 
    * 데이터 변환에 대 한 세부 정보는 다른 위치에서 다시 사용 하 고 호출할 수 있는 Azure 함수에서 추출 됩니다.
* 다음과
    * ADF와 함께 사용 하려면 Azure Functions를 만들어야 합니다.
    * Azure Functions은 단기 실행 데이터 처리에만 적합 합니다.

## <a name="azure-data-factory-with-custom-component-activity"></a>사용자 지정 구성 요소 작업으로 Azure Data Factory

이 옵션에서는 실행 파일에 래핑된 사용자 지정 Python 코드를 사용 하 여 데이터를 처리 합니다. [Azure Data Factory 사용자 지정 구성 요소 작업](../data-factory/transform-data-using-dotnet-custom-activity.md)을 사용 하 여 호출 됩니다. 이 방법은 이전 기술 보다 많은 데이터에 적합 합니다.

![다이어그램은 사용자 지정 구성 요소와 실행 M L 파이프라인, Azure Machine Learning 파이프라인, 학습 모델 및 원시 데이터와 준비 된 데이터와 상호 작용 하는 방법을 사용 하 여 Azure Data Factory 파이프라인을 보여 줍니다.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* 장점
    * 데이터는 대규모 병렬 및 고성능 컴퓨팅 기능을 제공 하는 [Azure Batch](../batch/batch-technical-overview.md) 풀에서 처리 됩니다.
    * 과도 한 알고리즘을 실행 하 고 많은 양의 데이터를 처리 하는 데 사용할 수 있습니다.
* 단점
    * 에서 사용 하기 전에 Azure Batch 풀을 만들어야 Data Factory
    * Python 코드를 실행 파일로 래핑하는 것과 관련 된 엔지니어링을 통해 종속성과 입/출력 매개 변수를 처리 하는 복잡성

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Python 노트북 Azure Databricks를 사용 하 여 Azure Data Factory

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 는 Microsoft 클라우드의 Apache Spark 기반 분석 플랫폼입니다.

이 방법에서는 데이터 변환이 Azure Databricks 클러스터에서 실행 되는 [Python 노트북](../data-factory/transform-data-using-databricks-notebook.md)에 의해 수행 됩니다. 이는 Azure Databricks 서비스의 전체 기능을 활용 하는 가장 일반적인 방법입니다. 대규모로 분산 된 데이터 처리를 위해 설계 되었습니다.

![다이어그램은 Python 및 실행 M L 파이프라인, Azure Machine Learning 파이프라인, 학습 모델 및 원시 데이터와 준비 된 데이터와 상호 작용 하는 방법을 사용 하 Azure Databricks 여 Azure Data Factory 파이프라인을 보여 줍니다.](media/how-to-data-ingest-adf/adf-databricks.png)

* 장점
    * 데이터는 Apache Spark 환경에서 백업 하는 가장 강력한 데이터 처리 Azure 서비스에서 변환 됩니다.
    * TensorFlow, PyTorch 및 scikit를 포함 한 데이터 과학 프레임 워크 및 라이브러리와 함께 Python의 기본 지원-배우기
    * Python 코드를 함수 또는 실행 가능한 모듈로 래핑할 필요는 없습니다. 코드는 그대로 작동 합니다.
* 단점
    * 에서 사용 하기 전에 Azure Databricks 인프라를 만들어야 Data Factory
    * Azure Databricks 구성에 따라 비용이 많이 들 수 있습니다.
    * "콜드" 모드에서 계산 클러스터를 회전 하면 솔루션에 대 한 대기 시간이 길어질 수 있는 시간이 소요 됩니다. 
    

## <a name="consume-data-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 사용 

Data Factory 파이프라인은 준비 된 데이터를 클라우드 저장소 (예: Azure Blob 또는 Azure Datalake)에 저장 합니다. <br>
Azure Machine Learning에서 준비 된 데이터를 사용 합니다. 

* Data Factory 파이프라인에서 Azure Machine Learning 파이프라인 호출<br>**OR**
* 나중에 사용할 [Azure Machine Learning 데이터 저장소](how-to-access-data.md#create-and-register-datastores) 및 [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md) 만들기

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Data Factory에서 Azure Machine Learning 파이프라인 호출

이 방법은 [MLOps (Machine Learning 작업) 워크플로에](concept-model-management-and-deployment.md#what-is-mlops)권장 됩니다. Azure Machine Learning 파이프라인을 설정 하지 않으려면 [저장소에서 직접 데이터 읽기](#read-data-directly-from-storage)를 참조 하세요.

Data Factory 파이프라인이 실행 될 때마다 

1. 데이터는 저장소의 다른 위치에 저장 됩니다. 
1. Azure Machine Learning에 위치를 전달 하기 위해 Data Factory 파이프라인은 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)을 호출 합니다. ML 파이프라인을 호출 하는 경우 데이터 위치 및 실행 ID는 매개 변수로 전송 됩니다. 
1. 그러면 ML 파이프라인이 데이터 위치를 사용 하 여 Azure Machine Learning 데이터 저장소 및 데이터 집합을 만들 수 있습니다. [Data Factory에서 Azure Machine Learning 파이프라인 실행](../data-factory/transform-data-machine-learning-service.md)에 대해 자세히 알아보세요.

![다이어그램은 Azure Data Factory 파이프라인과 Azure Machine Learning 파이프라인 및 이러한 파이프라인이 원시 데이터 및 준비 된 데이터와 상호 작용 하는 방법을 보여 줍니다. Data Factory 파이프라인은 데이터 저장소를 공급 하는 준비 된 데이터 데이터베이스로 데이터를 피드 하 여 Machine Learning 작업 영역에 데이터 집합을 피드 합니다.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> 데이터 집합은 [버전 관리를 지원](./how-to-version-track-datasets.md)하므로 ML 파이프라인은 ADF 파이프라인에서 최신 데이터를 가리키는 새 버전의 데이터 집합을 등록할 수 있습니다.

데이터 저장소 또는 데이터 집합을 통해 데이터에 액세스할 수 있게 되 면이를 사용 하 여 ML 모델을 학습 시킬 수 있습니다. 학습 프로세스는 ADF에서 호출 되는 동일한 ML 파이프라인의 일부일 수 있습니다. 또는 Jupyter 노트북의 실험과 같은 별도의 프로세스 일 수도 있습니다.

데이터 집합은 버전 관리를 지원 하 고 파이프라인에서 실행 되는 각 실행은 새 버전을 만들기 때문에 모델을 학습 하는 데 사용 된 데이터 버전을 쉽게 파악할 수 있습니다.

### <a name="read-data-directly-from-storage"></a>저장소에서 직접 데이터 읽기

ML 파이프라인을 만들지 않으려면 준비 된 데이터가 Azure Machine Learning 데이터 저장소 및 데이터 집합을 사용 하 여 저장 된 저장소 계정에서 직접 데이터에 액세스할 수 있습니다. 

다음 Python 코드는 Azure DataLake 2 세대 저장소에 연결 하는 데이터 저장소를 만드는 방법을 보여 줍니다. [데이터 저장소 및 서비스 주체 사용 권한을 찾을 수 있는 위치에 대해 자세히 알아보세요](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

그런 다음 machine learning 작업에서 사용 하려는 파일을 참조할 데이터 집합을 만듭니다. 

다음 코드는 csv 파일에서 TabularDataset을 만듭니다 `prepared-data.csv` . [데이터 집합 형식 및 허용 되는 파일 형식](how-to-create-register-datasets.md#dataset-types)에 대해 자세히 알아보세요. 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

여기에서를 사용 `prepared_dataset` 하 여 학습 스크립트와 같이 준비 된 데이터를 참조 합니다. [Azure Machine Learning에서 데이터 집합을 사용 하 여 모델을 학습](./how-to-train-with-datasets.md)하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory에서 Databricks 노트북 실행](../data-factory/transform-data-using-databricks-notebook.md)
* [Azure storage 서비스의 데이터에 액세스](./how-to-access-data.md#create-and-register-datastores)
* [Azure Machine Learning의 데이터 집합을 사용 하 여 모델을 학습](./how-to-train-with-datasets.md)합니다.
* [데이터 수집 파이프라인에 대한 DevOps](./how-to-cicd-data-ingestion.md)