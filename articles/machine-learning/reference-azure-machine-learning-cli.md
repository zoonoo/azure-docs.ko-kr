---
title: CLI 확장
titleSuffix: Azure Machine Learning
description: Azure CLI용 Azure Machine Learning CLI 확장에 대해 알아봅니다. Azure CLI는 Azure 클라우드의 리소스로 작업할 수 있도록 해주는 플랫폼 간 명령줄 유틸리티입니다. 기계 학습 확장을 사용하면 Azure 기계 학습을 사용할 수 있습니다. ML CLI는 작업 영역, 데이터 스토어, 데이터 집합, 파이프라인, 모델 및 배포와 같은 리소스를 만들고 관리합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618070"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Azure 기계 학습에 CLI 확장 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에 대한 확장입니다. 이 확장은 Azure 기계 학습 작업에 대한 명령을 제공합니다. 이를 통해 기계 학습 활동을 자동화할 수 있습니다. 다음 목록은 CLI 확장을 수행할 수 있는 몇 가지 예제 작업을 제공합니다.

+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ 기계 학습 모델 패키징, 배포 및 수명 주기 추적

CLI는 Azure Machine Learning SDK를 대체하는 것이 아닙니다. 자동화에 잘 맞는 고도로 매개 변수화된 작업을 처리하도록 최적화된 보완 도구입니다.

## <a name="prerequisites"></a>사전 요구 사항

* CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* **로컬 환경에서**이 문서에서 CLI 명령을 사용하려면 Azure [CLI가](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)필요합니다.

    Azure 클라우드 [셸을](https://azure.microsoft.com//features/cloud-shell/)사용하는 경우 CLI는 브라우저를 통해 액세스되고 클라우드에 있습니다.

## <a name="full-reference-docs"></a>전체 참조 문서

Azure [CLI의 azure-cli-ml 확장에 대한 전체 참조 문서를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)찾습니다.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

> [!IMPORTANT]
> Azure 클라우드 셸을 사용하는 경우 이 섹션을 건너뛸 수 있습니다. 클라우드 셸은 Azure 구독에 로그인한 계정을 사용하여 자동으로 인증합니다.

CLI에서 Azure 구독을 인증할 수 있는 방법에는 여러 가지가 있습니다. 가장 기본적인 방법은 브라우저를 사용하여 대화형으로 인증하는 것입니다. 대화식으로 인증하려면 명령줄 또는 터미널을 열고 다음 명령을 사용합니다.

```azurecli-interactive
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 그렇지 않으면 브라우저를 열고 명령줄의 지침을 따라야 합니다. 지침에는 권한 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 부여 코드를 검색하고 입력하는 것이 포함됩니다.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

다른 인증 방법에 대한 경우 [Azure CLI를 사용 하 여 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조 하십시오.

## <a name="install-the-extension"></a>확장 설치

Machine Learning CLI 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 아래 명령과 함께 사용할 수 있는 예제 [파일은 여기에서](https://aka.ms/azml-deploy-cloud)찾을 수 있습니다.

메시지가 표시되면 `y`를 선택하여 확장을 설치합니다.

확장이 설치되었는지 확인하려면 다음 명령을 사용하여 ML 관련 하위 명령 목록을 표시합니다.

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>확장 업데이트

기계 학습 CLI 확장을 업데이트하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>확장 제거

CLI 확장을 제거하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>리소스 관리

다음 명령은 CLI를 사용하여 Azure Machine Learning에서 사용하는 리소스를 관리하는 방법을 보여 줍니다.

+ 아직 없는 경우 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure 기계 학습 작업 영역 만들기:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > 이 명령은 기본 에디션 작업 영역을 만듭니다. 엔터프라이즈 작업 영역을 만들려면 `--sku enterprise` 명령과 `az ml workspace create` 함께 스위치를 사용합니다. Azure 기계 학습 버전에 대한 자세한 내용은 [Azure 기계 학습이란 무엇입니까](overview-what-is-azure-ml.md#sku)를 참조하십시오.

    자세한 내용은 [az ml 작업 영역 만들기를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)참조하십시오.

+ CLI 컨텍스트 인식을 활성화하기 위해 작업 영역 구성을 폴더에 연결합니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 `.azureml` 예제 runconfig 및 conda 환경 파일을 포함하는 하위 디렉터리를 만듭니다. 또한 Azure `config.json` 기계 학습 작업 영역과 통신하는 데 사용되는 파일도 포함되어 있습니다.

    자세한 내용은 [az ml 폴더 첨부](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)를 참조하십시오.

+ Azure Blob 컨테이너를 데이터 스토어로 연결합니다.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    자세한 내용은 [az ml 데이터스토어 첨부-Blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)을 참조하십시오.

+ 데이터를 데이터 스토어에 업로드합니다.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    자세한 내용은 [az ml 데이터스토어 업로드를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)참조하십시오.

+ AKS 클러스터를 계산 대상으로 연결합니다.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    자세한 내용은 [az ml 계산 대상 첨부 aks를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) 참조하십시오.

+ 새 AMLcompute 대상을 만듭니다.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    자세한 내용은 [az ml 계산 대상 만들기 amlcompute를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)참조하십시오.

## <a name="run-experiments"></a><a id="experiments"></a>실험 실행

* 실험 실행을 시작합니다. 이 명령을 사용하는 경우 -c 매개 변수에 대해 \*runconfig 파일의 이름(파일 시스템을 보는 경우 .runconfig 앞의 텍스트)을 지정합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 이 `az ml folder attach` 명령은 `.azureml` 두 개의 예제 runconfig 파일을 포함하는 하위 디렉터리를 만듭니다. 
    >
    > 프로그래밍 방식으로 실행 구성 개체를 만드는 Python 스크립트가 있는 경우 [RunConfig.save()를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) 사용하여 runconfig 파일로 저장할 수 있습니다.
    >
    > 전체 runconfig 스키마는 이 [JSON 파일에서](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)찾을 수 있습니다. 스키마는 각 개체의 키를 `description` 통해 자체 문서화됩니다. 또한 가능한 값에 대한 열거형과 끝에 템플릿 조각이 있습니다.

    자세한 내용은 [az ml 실행 제출 스크립트를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)참조하십시오.

* 실험 목록 보기:

    ```azurecli-interactive
    az ml experiment list
    ```

    자세한 내용은 [az ml 실험 목록을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)참조하십시오.

## <a name="dataset-management"></a>데이터 집합 관리

다음 명령은 Azure 기계 학습에서 데이터 집합을 사용하여 작업하는 방법을 보여 줍니다.

+ 데이터 집합 등록:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    데이터 집합을 정의하는 데 사용되는 JSON 파일의 형식에 대한 자세한 내용은 을 사용합니다. `az ml dataset register --show-template`

    자세한 내용은 [az ml 데이터 집합 레지스터를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)참조하십시오.

+ 활성 또는 더 이상 사용되지 않은 데이터 집합을 보관합니다.

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    자세한 내용은 [az ml 데이터 집합 아카이브를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)참조하십시오.

+ 데이터 집합을 더 이상 사용하지 않습니다.

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    자세한 내용은 [az ml 데이터 집합을 더 이상 사용하지 않습니다.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)

+ 작업 영역에 모든 데이터 집합을 나열합니다.

    ```azurecli-interactive
    az ml dataset list
    ```

    자세한 내용은 [az ml 데이터 집합 목록을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)참조하십시오.

+ 데이터 집합에 대한 세부 정보 가져오기:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    자세한 내용은 [az ml 데이터 집합 을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)참조하십시오.

+ 보관되거나 더 이상 사용되지 않은 데이터 집합을 다시 활성화합니다.

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    자세한 내용은 [az ml 데이터 집합 을 다시 활성화를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)참조하십시오.

+ 데이터 집합 등록 취소:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    자세한 내용은 [az ml 데이터 집합 등록 취소를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)참조하십시오.

## <a name="environment-management"></a>환경 관리

다음 명령은 작업 영역에 대한 Azure 기계 학습 [환경을](how-to-configure-environment.md) 만들고 등록하고 나열하는 방법을 보여 줍니다.

+ 환경에 대한 스캐폴딩 파일 만들기:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    자세한 내용은 [az ml 환경 스캐폴드를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)참조하십시오.

+ 환경 등록:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    자세한 내용은 [az ml 환경 레지스터를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)참조하십시오.

+ 등록된 환경을 나열합니다.

    ```azurecli-interactive
    az ml environment list
    ```

    자세한 내용은 [az ml 환경 목록을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)참조하십시오.

+ 등록된 환경 다운로드:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    자세한 내용은 [az ml 환경 다운로드를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)참조하십시오.

### <a name="environment-configuration-schema"></a>환경 구성 스키마

`az ml environment scaffold` 명령을 사용하는 경우 CLI를 사용하여 `azureml_environment.json` 사용자 지정 환경 구성을 만드는 데 수정하고 사용할 수 있는 템플릿 파일을 생성합니다. 최상위 개체는 Python SDK의 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) 클래스에 느슨하게 매핑됩니다. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

다음 표에서는 JSON 파일의 각 최상위 필드, 형식 및 설명에 대해 자세히 설명합니다. 개체 형식이 Python SDK의 클래스에 연결되어 있는 경우 각 JSON 필드와 Python 클래스의 공용 변수 이름 간에 느슨한 1:1 일치가 있습니다. 경우에 따라 필드는 클래스 변수가 아닌 생성자 인수에 매핑될 수 있습니다. 예를 들어 `environmentVariables` 필드는 클래스의 `environment_variables` 변수에 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) 매핑됩니다.

| JSON 필드 | Type | Description |
|---|---|---|
| `name` | `string` | 환경의 이름입니다. **Microsoft** 또는 **AzureML로**이름을 시작하지 마십시오. |
| `version` | `string` | 환경의 버전입니다. |
| `environmentVariables` | `{string: string}` | 환경 변수 이름 및 값의 해시 맵입니다. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | 대상 계산 리소스에 사용할 파이썬 환경 및 인터프리터를 정의하는 개체입니다. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | 환경 사양에 맞게 빌드된 Docker 이미지를 사용자 지정하는 설정을 정의합니다. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | 이 섹션에서는 스파크 설정을 구성합니다. 프레임워크가 PySpark로 설정된 경우에만 사용됩니다. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Databricks 라이브러리 종속성을 구성합니다. |
| `inferencingStackVersion` | `string` | 이미지에 추가된 열등 스택 버전을 지정합니다. 추론 스택을 추가하지 않으려면 이 필드를 `null`그대로 둡니다. 유효한 값: "최신". |

## <a name="ml-pipeline-management"></a>ML 파이프라인 관리

다음 명령은 기계 학습 파이프라인을 사용하여 작업하는 방법을 보여 줍니다.

+ 기계 학습 파이프라인 만들기:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    자세한 내용은 [az ml 파이프라인 만들기를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)참조하십시오.

    파이프라인 YAML 파일에 대한 자세한 내용은 [YAML의 기계 학습 파이프라인 정의를](reference-pipeline-yaml.md)참조하십시오.

+ 파이프라인 실행:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    자세한 내용은 [az ml 실행 제출 파이프라인을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)참조하십시오.

    파이프라인 YAML 파일에 대한 자세한 내용은 [YAML의 기계 학습 파이프라인 정의를](reference-pipeline-yaml.md)참조하십시오.

+ 파이프라인 예약:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    자세한 내용은 [az ml 파이프라인 만들기 일정을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)참조하십시오.

    파이프라인 일정 YAML 파일에 대한 자세한 내용은 [YAML의 기계 학습 파이프라인 정의를](reference-pipeline-yaml.md#schedules)참조하십시오.

## <a name="model-registration-profiling-deployment"></a>모델 등록, 프로파일링, 배포

다음 명령은 학습된 모델을 등록한 후 프로덕션 서비스로 배포하는 방법을 보여 줍니다.

+ Azure Machine Learning에 모델을 등록합니다.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    자세한 내용은 [az ml 모델 레지스터를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)참조하십시오.

+ **선택 사항** 모델을 프로파일로 구축하여 배포를 위한 최적의 CPU 및 메모리 값을 가져옵니다.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    자세한 내용은 [az ml 모델 프로파일을](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)참조하십시오.

+ AKS에 모델 배포
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    추론 구성 파일 스키마에 대한 자세한 내용은 [추론 구성 스키마](#inferenceconfig)를 참조하십시오.
    
    배포 구성 파일 스키마에 대한 자세한 내용은 [배포 구성 스키마](#deploymentconfig)를 참조하십시오.

    자세한 내용은 [az ml 모델 배포를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)참조하십시오.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>추론 구성 스키마

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>배포 구성 스키마

### <a name="local-deployment-configuration-schema"></a>로컬 배포 구성 스키마

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure 컨테이너 인스턴스 배포 구성 스키마 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes 서비스 배포 구성 스키마

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>다음 단계

* [기계 학습 CLI 확장에 대한 명령 참조](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Azure 파이프라인을 사용하여 기계 학습 모델 교육 및 배포](/azure/devops/pipelines/targets/azure-machine-learning)
