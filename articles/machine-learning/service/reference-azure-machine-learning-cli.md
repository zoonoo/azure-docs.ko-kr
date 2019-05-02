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
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 2992ec9f43aac9e0d80c5e42873d26ac3a9c3fd1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916986"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Azure Machine Learning 서비스용 CLI 확장 사용

Azure Machine Learning CLI는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에 대한 확장입니다. 이 확장은 명령줄에서 Azure Machine Learning 서비스로 작업하는 데 필요한 명령을 제공합니다. 기계 학습 워크플로 자동화할 수 있습니다. 예를 들어, 다음 작업을 수행할 수 있습니다.

+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ 기계 학습 모델 패키징, 배포 및 수명 주기 추적

CLI는 Azure Machine Learning SDK를 대체하는 것이 아닙니다. 자동화에 잘 맞게 자체는 항상 매개 변수가 있는 작업을 처리 하도록 최적화 된 보완적인 도구입니다.

## <a name="prerequisites"></a>필수 조건

* CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

## <a name="install-the-extension"></a>확장 설치

Machine Learning CLI 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 아래 명령을 사용할 수 있는 예제 파일을 찾을 수 있습니다 [여기](http://aka.ms/azml-deploy-cloud)합니다.

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

+ 작업 영역 구성 컨텍스트 인식 CLI를 사용 하도록 설정 하려면 폴더에 연결 합니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ 데이터 저장소로 Azure blob 컨테이너를 연결 합니다.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

+ 계산 대상으로 AKS 클러스터를 연결 합니다.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

+ 새 AMLcompute 대상 만들기

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```
    
## <a id="experiments"></a>실험 실행

+ 작업 영역 구성 컨텍스트 인식 CLI를 사용 하도록 설정 하려면 폴더에 연결 합니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

* 실험 실행을 시작합니다. 이 명령을 사용할 때는 runconfig 파일의 이름을 지정 (앞에 있는 텍스트 \*.runconfig 파일 시스템에서 찾으려는 경우)-c 매개 변수에 대 한 합니다.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* 실험 목록 보기:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling-deployment"></a>모델 등록, 프로 파일링, 배포

다음 명령은 학습된 모델을 등록한 후 프로덕션 서비스로 배포하는 방법을 보여 줍니다.

+ Azure Machine Learning에 모델을 등록합니다.

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ AKS에 모델 배포

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
