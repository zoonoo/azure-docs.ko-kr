---
title: Machine Learning CLI 확장
titleSuffix: Azure Machine Learning
description: Azure CLI용 Azure Machine Learning CLI 확장에 대해 알아봅니다. Azure CLI는 Azure 클라우드의 리소스로 작업할 수 있도록 해주는 플랫폼 간 명령줄 유틸리티입니다. Machine Learning 확장을 사용 하면 Azure Machine Learning를 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 85ebcc36d32b86ec2640ce7ce02190deaab19d6b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997077"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Azure Machine Learning에 CLI 확장 사용

Azure Machine Learning CLI는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에 대한 확장입니다. 이 확장 프로그램은 Azure Machine Learning 작업을 위한 명령을 제공 합니다. 이를 통해 machine learning 작업을 자동화할 수 있습니다. 다음 목록에서는 CLI 확장으로 수행할 수 있는 몇 가지 예제 작업을 보여 줍니다.

+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ 기계 학습 모델 패키징, 배포 및 수명 주기 추적

CLI는 Azure Machine Learning SDK를 대체하는 것이 아닙니다. 자동화에 잘 맞는 매우 중요 한 작업을 처리 하도록 최적화 된 보완 도구입니다.

## <a name="prerequisites"></a>필수 구성 요소

* CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 을 사용해 보세요.

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

## <a name="full-reference-docs"></a>전체 참조 문서

[Azure CLI의 azure cli ml 확장에 대 한 전체 참조 문서](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)를 찾습니다.

## <a name="install-the-extension"></a>확장 설치

Machine Learning CLI 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 아래 명령에 사용할 수 있는 예제 파일은 [여기](https://aka.ms/azml-deploy-cloud)에서 찾을 수 있습니다.

메시지가 표시되면 `y`를 선택하여 확장을 설치합니다.

확장이 설치되었는지 확인하려면 다음 명령을 사용하여 ML 관련 하위 명령 목록을 표시합니다.

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>확장 업데이트

CLI 확장 Machine Learning를 업데이트 하려면 다음 명령을 사용 합니다.

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

+ Azure Machine Learning 작업 영역을 만듭니다.

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    자세한 내용은 [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)를 참조 하세요.

+ 작업 영역 구성을 폴더에 연결 하 여 CLI 상황별 인식을 사용 하도록 설정 합니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 .runconfig 및 `.azureml` conda environment 파일 예제를 포함 하는 하위 디렉터리를 만듭니다. 또한 Azure Machine Learning 작업 영역과 `config.json` 통신 하는 데 사용 되는 파일도 포함 되어 있습니다.

    자세한 내용은 [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)항목을 참조 하세요.

+ Azure blob 컨테이너를 데이터 저장소로 연결 합니다.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    자세한 내용은 [az ml 데이터 저장소 연결-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)을 참조 하세요.

+ 데이터 저장소에 파일을 업로드 합니다.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    자세한 내용은 [az ml 데이터 저장소 업로드](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)를 참조 하세요.

+ AKS 클러스터를 계산 대상으로 연결 합니다.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    자세한 내용은 [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) 를 참조 하세요.

+ 새 AMLcompute 대상을 만듭니다.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    자세한 내용은 [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)를 참조 하세요.

## <a id="experiments"></a>실험 실행

* 실험 실행을 시작합니다. 이 명령을 사용 하는 경우-c 매개 변수에 대해 .runconfig 파일의 이름 ( \*파일 시스템을 확인 하는 .runconfig 앞의 텍스트)을 지정 합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 명령은 .runconfig 파일의 `.azureml` 두 예제를 포함 하는 하위 디렉터리를 만듭니다. `az ml folder attach` 
    >
    > 실행 구성 개체를 프로그래밍 방식으로 만드는 Python 스크립트가 있는 경우 [.runconfig ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) 를 사용 하 여 .runconfig 파일로 저장할 수 있습니다.
    >
    > .Runconfig 파일 예제에 대 한 자세한 [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)내용은을 참조 하십시오.

    자세한 내용은 [az ml run submit-스크립트](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)를 참조 하세요.

* 실험 목록 보기:

    ```azurecli-interactive
    az ml experiment list
    ```

    자세한 내용은 [az ml 실험 list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)를 참조 하세요.

## <a name="environment-management"></a>환경 관리

다음 명령에서는 작업 영역에 대 한 Azure Machine Learning [환경을](how-to-configure-environment.md) 만들고 등록 하 고 나열 하는 방법을 보여 줍니다.

+ 환경에 대 한 스 캐 폴딩 파일 만들기:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    자세한 내용은 [az ml environment 스 캐 폴드](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)를 참조 하세요.

+ 환경 등록:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    자세한 내용은 [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)를 참조 하세요.

+ 등록 된 환경 나열:

    ```azurecli-interactive
    az ml environment list
    ```

    자세한 내용은 [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)를 참조 하세요.

+ 등록 된 환경 다운로드:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    자세한 내용은 [az ml environment 다운로드](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)를 참조 하세요.

## <a name="model-registration-profiling-deployment"></a>모델 등록, 프로 파일링, 배포

다음 명령은 학습된 모델을 등록한 후 프로덕션 서비스로 배포하는 방법을 보여 줍니다.

+ Azure Machine Learning에 모델을 등록합니다.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    자세한 내용은 [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)를 참조 하세요.

+ **선택 사항** 모델을 프로 파일링 하 여 배포를 위한 최적의 CPU 및 메모리 값을 얻습니다.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    자세한 내용은 [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)을 참조 하세요.

+ AKS에 모델 배포
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    유추 구성 파일 스키마에 대 한 자세한 내용은 [유추 구성 스키마](#inferenceconfig)를 참조 하세요.
    
    배포 구성 파일 스키마에 대 한 자세한 내용은 [배포 구성 스키마](#deploymentconfig)를 참조 하세요.

    자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)를 참조 하세요.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>유추 구성 스키마

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>배포 구성 스키마

### <a name="local-deployment-configuration-schema"></a>로컬 배포 구성 스키마

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure Container Instance 배포 구성 스키마 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service 배포 구성 스키마

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>다음 단계

* [MACHINE LEARNING CLI 확장에 대 한 명령 참조](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)입니다.

* [Azure Pipelines를 사용 하 여 기계 학습 모델 학습 및 배포](/azure/devops/pipelines/targets/azure-machine-learning)
