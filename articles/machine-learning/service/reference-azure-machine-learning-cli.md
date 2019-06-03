---
title: Machine Learning CLI 확장
titleSuffix: Azure Machine Learning service
description: Azure CLI용 Azure Machine Learning CLI 확장에 대해 알아봅니다. Azure CLI는 Azure 클라우드의 리소스로 작업할 수 있도록 해주는 플랫폼 간 명령줄 유틸리티입니다. Machine Learning 확장을 사용하면 Azure Machine Learning 서비스로 작업할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 541ffe70ae5198e631568584a58d02ac283e89d3
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298251"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Azure Machine Learning 서비스용 CLI 확장 사용

Azure Machine Learning CLI는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에 대한 확장입니다. 이 확장은 Azure Machine Learning 서비스를 사용 하 여 작업에 대 한 명령을 제공 합니다. 기계 학습 작업을 자동화할 수 있습니다. 다음은 CLI 확장을 사용 하 여 수행할 수 있는 몇 가지 예제 작업을 제공 합니다.

+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ 기계 학습 모델 패키징, 배포 및 수명 주기 추적

CLI는 Azure Machine Learning SDK를 대체하는 것이 아닙니다. 자동화에 잘 맞게 자체는 항상 매개 변수가 있는 작업을 처리 하도록 최적화 된 보완적인 도구입니다.

## <a name="prerequisites"></a>필수 조건

* CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

## <a name="full-reference-docs"></a>전체 참조 문서

찾을 합니다 [Azure CLI의 azure cli-기계 학습 확장에 대 한 참조 문서를 전체](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)합니다.

## <a name="install-the-extension"></a>확장 설치

Machine Learning CLI 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 아래 명령을 사용할 수 있는 예제 파일을 찾을 수 있습니다 [여기](https://aka.ms/azml-deploy-cloud)합니다.

메시지가 표시되면 `y`를 선택하여 확장을 설치합니다.

확장이 설치되었는지 확인하려면 다음 명령을 사용하여 ML 관련 하위 명령 목록을 표시합니다.

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>확장 제거

CLI 확장을 제거하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>리소스 관리

다음 명령은 CLI를 사용하여 Azure Machine Learning에서 사용하는 리소스를 관리하는 방법을 보여 줍니다.

+ 수 없는 하나, 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning 서비스 작업 영역을 만듭니다.

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    자세한 내용은 [az ml 작업 영역 만들기](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)합니다.

+ 작업 영역 구성 컨텍스트 인식 CLI를 사용 하도록 설정 하려면 폴더에 연결 합니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 만듭니다는 `.azureml` 예제 runconfig 및 conda 환경 파일을 포함 하는 하위 디렉터리입니다. 또한 포함을 `config.json` Azure Machine Learning 작업 영역을 사용 하 여 통신 하는 데 사용 되는 파일입니다.

    자세한 내용은 [az ml 폴더 연결](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)합니다.

+ 데이터 저장소로 Azure blob 컨테이너를 연결 합니다.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    자세한 내용은 [az ml 데이터 저장소 연결-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)합니다.

    
+ 계산 대상으로 AKS 클러스터를 연결 합니다.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    자세한 내용은 참조 하세요. [az ml computetarget aks 연결](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 새 AMLcompute 대상을 만듭니다.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    자세한 내용은 [az ml computetarget 만들 amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)합니다.

## <a id="experiments"></a>실험 실행

* 실험 실행을 시작합니다. 이 명령을 사용할 때는 runconfig 파일의 이름을 지정 (앞에 있는 텍스트 \*.runconfig 파일 시스템에서 찾으려는 경우)-c 매개 변수에 대 한 합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 명령은 만듭니다는 `.azureml` 두 예제 runconfig 파일을 포함 하는 하위 디렉터리입니다. 
    >
    > Python 스크립트 실행된 구성 개체를 프로그래밍 방식으로 만드는 경우 사용할 수 [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) runconfig 파일으로 저장 합니다.
    >
    > 자세한 예제 runconfig 파일을 참조 하세요 [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)합니다.

    자세한 내용은 [제출 스크립트를 실행 하는 az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)합니다.

* 실험 목록 보기:

    ```azurecli-interactive
    az ml experiment list
    ```

    자세한 내용은 [az ml 실험 목록](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)합니다.

## <a name="model-registration-profiling-deployment"></a>모델 등록, 프로 파일링, 배포

다음 명령은 학습된 모델을 등록한 후 프로덕션 서비스로 배포하는 방법을 보여 줍니다.

+ Azure Machine Learning에 모델을 등록합니다.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    자세한 내용은 [az ml 모델 등록](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)합니다.

+ **선택적** 모델 배포를 위한 최적의 CPU 및 메모리 값을 얻으려면을 프로 파일링 합니다.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    자세한 내용은 [az ml 모델 프로필](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)합니다.

+ AKS에 모델 배포
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    다음은 예제 `inferenceconfig.json` 문서:
    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```
    다음은 'deploymentconfig.json' 문서의 예입니다.
    ```json
    {
    "computeType": "aks",
    "ComputeTarget": "akscomputetarget"
    }
    ```

    자세한 내용은 [az ml 모델 배포](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)합니다.


## <a name="next-steps"></a>다음 단계

* [Machine Learning CLI 확장에 대 한 참조를 명령](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)입니다.

* [학습 및 Azure 파이프라인을 사용 하 여 기계 학습 모델 배포](/azure/devops/pipelines/targets/azure-machine-learning)