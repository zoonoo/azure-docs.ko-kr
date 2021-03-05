---
title: Apache Spark 풀 (미리 보기)을 사용 하 여 데이터 준비
titleSuffix: Azure Machine Learning
description: Azure Synapse Analytics 및 Azure Machine Learning를 사용 하 여 데이터 준비를 위해 Apache Spark 풀을 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 7eeb7b82d9c3bfe21019d5d68f82c2e6d7a2bf68
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171516"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-preparation-preview"></a>데이터 준비를 위해 (Azure Synapse Analytics에서 구동) Apache Spark 풀 연결 (미리 보기)

이 문서에서는 데이터 준비를 위해 [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) 에서 제공 하는 Apache Spark 풀을 연결 하 고 시작 하는 방법에 대해 알아봅니다. 

>[!IMPORTANT]
> Azure Machine Learning 및 Azure Synapse Analytics 통합은 미리 보기 상태입니다. 이 문서에 제공 된 기능에서는 언제 `azureml-synapse` 든 지 변경 될 수 있는 [실험적](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) 미리 보기 기능을 포함 하는 패키지를 사용 합니다.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning 및 Azure Synapse Analytics 통합 (미리 보기)

Azure Synapse Analytics와 Azure Machine Learning (미리 보기)를 통합 하면 Azure Synapse에서 지원 되는 Apache Spark 풀을 연결 하 여 대화형 데이터 탐색 및 준비를 수행할 수 있습니다. 이 통합을 사용 하면 기계 학습 모델을 학습 하는 데 사용 하는 것과 동일한 Python 노트북 내에서 대규모로 데이터 준비를 위한 전용 계산을 수행할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md?tabs=python).

* [Azure Portal에서 Azure Synapse Analytics 작업 영역을 만듭니다](../synapse-analytics/quickstart-create-workspace.md).

* [Azure Portal, 웹 도구 또는 Synapse Studio를 사용 하 여 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* 패키지 (미리 보기)를 포함 하는 [Azure Machine Learning PYTHON SDK를 설치](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) 합니다 `azureml-synapse` . 
    * 직접 설치할 수도 있지만 SDK 버전 1.20 이상 에서만 호환 됩니다. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-analytics-assets"></a>Machine learning 작업 영역 및 Synapse 분석 자산 연결

데이터 준비를 위해 Apache Synapse Spark 풀을 연결 하려면 먼저 Azure Machine Learning 작업 영역을 Azure Synapse Analytics 작업 영역에 연결 해야 합니다. 

[PYTHON SDK](#link-sdk) 또는 [Azure Machine Learning studio](#link-studio)를 통해 Machine Learning 작업 영역 및 Synapse Analytics 작업 영역을 연결할 수 있습니다. 

> [!IMPORTANT]
> Azure Synapse Analytics 작업 영역에 성공적으로 연결 하려면 Azure Synapse Analytics 작업 영역의 **소유자** 역할을 부여 받아야 합니다. [Azure Portal](https://ms.portal.azure.com/)에서 액세스를 확인 합니다.
>
> Azure Synapse Analytics 작업 영역의 **소유자** 가 아니지만 기존 연결 된 서비스를 사용 하려는 경우 [기존 연결 된 서비스 가져오기](#get-an-existing-linked-service)를 참조 하세요.


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Python SDK를 사용 하 여 작업 영역 연결

다음 코드는 [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) 및 클래스를에 채택 합니다. [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) 

* Azure Synapse Analytics 작업 영역을 사용 하 여 Azure Machine Learning 작업 영역을 연결 `ws` 합니다. 
* Azure Machine Learning를 사용 하 여 Azure Synapse Analytics 작업 영역을 연결 된 서비스로 등록 합니다.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> `system_assigned_identity_principal_id`연결 된 각 서비스에 대해 관리 id가 만들어집니다. Apache Spark 세션을 시작 하기 전에이 관리 되는 id에 Azure Synapse Analytics 작업 영역의 **Synapse Apache Spark 관리자** 역할을 부여 해야 합니다. [Synapse Studio에서 관리 되는 id에 Synapse Apache Spark Administrator 역할을 할당](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)합니다.
>
> `system_assigned_identity_principal_id`특정 연결 된 서비스의를 찾으려면를 사용 `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` 합니다.

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Studio를 통해 작업 영역 연결

다음 단계를 사용 하 여 Azure Machine Learning studio를 통해 Azure Machine Learning 작업 영역 및 Azure Synapse Analytics 작업 영역을 연결 합니다. 

1. [Azure Machine Learning studio](https://ml.azure.com/)에 로그인 합니다.
1. 왼쪽 창의 **관리** 섹션에서 **연결 된 서비스** 를 선택 합니다.
1. **통합 추가** 를 선택 합니다.
1. **링크 작업 영역** 폼에서 필드를 채웁니다.

   |필드| 설명    
   |---|---
   |Name| 연결 된 서비스의 이름을 제공 합니다. 이 이름은이 특정 연결 된 서비스를 참조 하는 데 사용 됩니다.
   |구독 이름 | Machine learning 작업 영역에 연결 된 구독의 이름을 선택 합니다. 
   |Synapse 작업 영역 | 연결 하려는 Synapse 작업 영역을 선택 합니다. 
   
1. **다음** 을 선택 하 여 **Spark 풀 선택 (선택 사항)** 양식을 엽니다. 이 양식에서 작업 영역에 연결할 Synapse Apache Spark 풀을 선택 합니다.

1. **다음** 을 선택 하 여 **검토** 양식을 열고 선택 내용을 확인 합니다. 
1. **만들기** 를 선택 하 여 연결 된 서비스 만들기 프로세스를 완료 합니다.

## <a name="get-an-existing-linked-service"></a>기존 연결 된 서비스 가져오기

기존 연결 된 서비스를 검색 하 고 사용 하려면 Azure Synapse Analytics 작업 영역에 대 한 **사용자 또는 참가자** 권한이 필요 합니다.

이 예제에서는 `synapselink1` 메서드를 사용 하 여 작업 영역에서 연결 된 기존 서비스를 검색 합니다 `ws` [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) .
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>연결 된 서비스 관리

작업 영역 연결을 해제 하려면 메서드를 사용 합니다. `unregister()`

``` python
linked_service.unregister()
```

Machine learning 작업 영역과 연결 된 모든 연결 된 서비스를 확인 합니다. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark 풀을 계산으로 연결

작업 영역이 연결 되 면 데이터 준비 태스크에 대 한 전용 계산 리소스로 Synapse Apache Spark 풀을 연결 합니다. 

를 통해 Apache Spark 풀을 연결할 수 있습니다.
* Azure Machine Learning Studio
* [ARM(Azure Resource Manager) 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

Studio를 사용 하 여 Apache Spark 풀을 연결 하려면 다음 단계를 수행 합니다. 

1. [Azure Machine Learning studio](https://ml.azure.com/)에 로그인 합니다.
1. 왼쪽 창의 **관리** 섹션에서 **연결 된 서비스** 를 선택 합니다.
1. Synapse 작업 영역을 선택 합니다.
1. 왼쪽 위에서 **연결 된 Spark 풀** 을 선택 합니다. 
1. **연결** 을 선택합니다. 
1. 목록에서 Apache Spark 풀을 선택 하 고 이름을 입력 합니다.  
    1. 이 목록은 계산에 연결할 수 있는 사용 가능한 Synapse Spark 풀을 식별 합니다. 
    1. 새 Synapse Spark 풀을 만들려면 [Synapse Studio를 사용 하 여 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 를 참조 하세요.
1. **선택한 연결** 을 선택 합니다. 


**PYTHON SDK** 를 사용 하 여 Apache Spark 풀을 연결할 수도 있습니다. 

다음 코드 
1. 다음을 사용 하 여 SynapseCompute를 구성 합니다.

   1. `linked_service`이전 단계에서 만들었거나 검색 한 LinkedService입니다. 
   1. 연결 하려는 계산 대상의 유형입니다. `SynapseSpark`
   1. Apache Spark 풀의 이름입니다. 이는 Azure Synapse Analytics 작업 영역에 있는 기존 Apache Spark 풀과 일치 해야 합니다.
   
1. 를 전달 하 여 machine learning ComputeTarget를 만듭니다. 
   1. 사용 하려는 machine learning 작업 영역 `ws`
   1. Azure Machine Learning 작업 영역 내에서 계산을 참조 하고자 하는 이름입니다. 
   1. Synapse 계산을 구성할 때 지정한 attach_configuration입니다.
       1. ComputeTarget ()에 대 한 호출은 비동기 이므로 샘플은 호출이 완료 될 때까지 차단 됩니다.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Apache Spark 풀이 연결 되어 있는지 확인 합니다.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>데이터 준비 태스크에 대 한 Synapse Spark 풀 시작

Apache Spark 세션 중에 사용할 [Azure Machine Learning 환경을](concept-environments.md) 지정할 수 있습니다. 환경에 지정 된 Conda 종속성만 적용 됩니다. Docker 이미지는 지원 되지 않습니다.

>[!WARNING]
>  환경 Conda 종속성에 지정 된 Python 종속성은 Apache Spark 풀에서 지원 되지 않습니다. 현재는 고정 Python 버전만 지원 됩니다. 스크립트에를 포함 하 여 Python 버전을 확인  `sys.version_info` 합니다.

다음 코드에서는 `myenv` `azureml-core` 세션이 시작 되기 전에 버전 1.20.0 및 버전 1.17.0을 설치 하는 환경을 만듭니다 `numpy` . 그런 다음 Apache Spark session 문에이 환경을 포함할 수 있습니다 `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Apache Spark Spark 풀을 사용 하 여 데이터 준비를 시작 하려면 Apache Spark 풀 이름을 지정 하 고 구독 ID, machine learning 작업 영역 리소스 그룹, machine learning 작업 영역 이름 및 Apache Spark 세션 동안 사용할 환경을 제공 합니다. 

> [!IMPORTANT]
> Apache Spark 풀을 계속 사용 하려면 `%synapse` 단일 코드 줄 및 여러 줄에 대 한 데이터 준비 작업 전체에서 사용할 계산 리소스를 지정 해야 합니다 `%%synapse` . 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

세션이 시작 된 후 세션의 메타 데이터를 확인할 수 있습니다.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>저장소에서 데이터 로드

Apache Spark 세션이 시작 되 면 준비 하려는 데이터를 읽습니다. 데이터 로드는 Azure Blob storage 및 Azure Data Lake Storage 세대 1 및 2에 대해 지원 됩니다.

이러한 저장소 서비스에서 데이터를 로드 하는 방법에는 두 가지가 있습니다. 

* HDFS (Hadoop Distributed Files System) 경로를 사용 하 여 저장소에서 데이터를 직접 로드 합니다.

* 기존 [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md)에서 데이터를 읽습니다.

이러한 저장소 서비스에 액세스 하려면 **저장소 Blob 데이터 판독기** 권한이 필요 합니다. 이러한 저장소 서비스에 데이터를 다시 쓰려면 **저장소 Blob 데이터 참가자** 권한이 필요 합니다. [저장소 사용 권한 및 역할에 대해 자세히 알아보세요](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>HDFS (Hadoop Distributed Files System) 경로를 사용 하 여 데이터 로드

해당 HDFS 경로를 사용 하 여 저장소에서 데이터를 로드 하 고 읽으려면 데이터 액세스 인증 자격 증명을 쉽게 사용할 수 있어야 합니다. 이러한 자격 증명은 저장소 유형에 따라 달라 집니다.  

다음 코드에서는 SAS (공유 액세스 서명) 토큰 또는 액세스 키를 사용 하 여 **Azure Blob 저장소** 에서 Spark 데이터 프레임 데이터를 읽는 방법을 보여 줍니다. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

다음 코드에서는 서비스 주체 자격 증명을 사용 하 여 **Azure Data Lake Storage 1 세대 (ADLS Gen 1)** 에서 데이터를 읽는 방법을 보여 줍니다. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

다음 코드에서는 서비스 주체 자격 증명을 사용 하 여 **Azure Data Lake Storage 2 세대 (ADLS Gen 2)** 에서 데이터를 읽는 방법을 보여 줍니다. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>등록 된 데이터 집합에서 데이터 읽기

작업 영역에서 등록 된 기존 데이터 집합을 가져와서 spark 데이터 프레임 변환 하 여 데이터 준비를 수행할 수도 있습니다.  

다음 예제에서는 `blob_dset` blob 저장소의 파일을 참조 하 고 spark 데이터 프레임로 변환 하는 등록 된 TabularDataset를 가져옵니다. 데이터 집합을 spark 데이터 프레임 변환 하는 경우 `pyspark` 데이터 탐색 및 준비 라이브러리를 활용할 수 있습니다.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>데이터 준비 태스크 수행

데이터를 검색 하 고 탐색 한 후에는 데이터 준비 작업을 수행할 수 있습니다.

다음 코드는 이전 섹션의 HDFS 예제를 확장 하 고 Survivor 열을 기반으로 하 여 spark 데이터 프레임에서 데이터를 필터링 하 고 `df` **Age** 로 나열 된 그룹  을 필터링 합니다.

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>저장소에 데이터 저장 및 spark 세션 중지

데이터 탐색 및 준비가 완료 되 면 Azure의 저장소 계정에서 나중에 사용할 수 있도록 준비 된 데이터를 저장 합니다.

다음 예제에서 준비 된 데이터는 Azure Blob storage에 다시 기록 되 고 `Titanic.csv` 디렉터리의 원본 파일을 덮어씁니다 `training_data` . 저장소에 다시 쓰려면 **저장소 Blob 데이터 참가자** 권한이 필요 합니다. [저장소 사용 권한 및 역할에 대해 자세히 알아보세요](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

데이터 준비를 완료 하 고 준비 된 데이터를 저장소에 저장 한 경우 다음 명령을 사용 하 여 Apache Spark 풀 사용을 중지 합니다.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>준비 된 데이터를 나타내는 데이터 집합 만들기

모델 학습을 위해 준비 된 데이터를 사용할 준비가 되 면 [Azure Machine Learning 데이터 저장소](how-to-access-data.md)를 사용 하 여 저장소에 연결 하 고 [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md)에 사용할 파일을 지정 합니다.

다음 코드 예제

* 준비 된 데이터를 저장 한 저장소 서비스에 연결 하는 데이터 저장소를 이미 만들었다고 가정 합니다.  
* `mydatastore`작업 영역에서 `ws` get () 메서드를 사용 하 여 기존 데이터 저장소를 가져옵니다.
* [](how-to-create-register-datasets.md#filedataset) `train_ds` 의 디렉터리에 있는 준비 된 데이터 파일을 참조 하는 filedataset을 만듭니다 `training_data` `mydatastore` .  
* `input1`나중에 계산 대상에서 데이터 집합의 데이터 파일을 사용할 수 있도록 하는 데 사용할 수 있는 변수를 만듭니다 `train_ds` .

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>예제 Notebook

Azure Synapse Analytics 및 Azure Machine Learning를 사용 하 여 단일 노트북에서 데이터 준비 및 모델 학습을 수행 하는 방법에 대 한 자세한 코드 예제는이 [종단 간 노트북](../synapse-analytics/overview-what-is.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [모델을 학습](how-to-set-up-training-targets.md)합니다.
* [Azure Machine Learning 데이터 집합으로 학습](how-to-train-with-datasets.md)
* [Azure machine learning 데이터 집합을 만듭니다](how-to-create-register-datasets.md).