---
title: 관리형 온라인 엔드포인트를 통해 ML 모델 배포
titleSuffix: Azure Machine Learning
description: 기계 학습 모델을 Azure에서 자동으로 관리하는 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/13/2021
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: 8c14523d1d566086eff73693d6500947ccda7ba4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382796"
---
# <a name="deploy-and-score-a-machine-learning-model-with-a-managed-online-endpoint-preview"></a>관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점

관리형 온라인 엔드포인트(미리 보기)는 기본 인프라를 만들고 관리할 필요 없이 모델을 배포하는 기능을 제공합니다. 이 문서에서는 먼저 로컬 머신에 모델을 배포하여 오류를 디버그한 다음, Azure에서 배포하고 테스트합니다. 로그를 보고 SLA(서비스 수준 약정)를 모니터링하는 방법도 알아봅니다. 모델로 시작하여 온라인/실시간 채점에 사용할 수 있는 확장 가능한 HTTPS/REST 엔드포인트로 끝납니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 기계 학습을 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* Azure CLI 및 ML 확장을 설치하고 구성해야 합니다. 자세한 내용은 [2.0 CLI(미리 보기) 설치, 설정 및 사용](how-to-configure-cli.md)을 참조하세요. 

* Azure 리소스 그룹이 있고, 사용자(또는 사용하는 서비스 주체)에게 이에 대한 `Contributor` 액세스 권한이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 리소스 그룹이 있을 것입니다. 

* Azure Machine Learning 작업 영역이 있어야 합니다. 위의 문서에 따라 ML 확장을 구성한 경우 이러한 작업 영역이 있을 것입니다.

* Azure CLI의 기본값을 아직 설정하지 않은 경우 기본 설정을 저장해야 합니다. 값을 반복해서 전달하지 않으려면 다음을 실행합니다.

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* [Optional] To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Prepare your system

To follow along with the article, clone the samples repository, and navigate to the right directory by running the following commands:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

엔드포인트 이름을 설정합니다(아래의 `YOUR_ENDPOINT_NAME`을 고유한 이름으로 변경). 아래의 명령은 Unix 환경에 해당합니다.

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

Windows 운영 체제를 사용하는 경우 `set ENDPOINT_NAME=YOUR_ENDPOINT_NAME` 대신 이 명령을 사용합니다.

> [!NOTE]
> 엔드포인트 이름은 Azure 지역 수준에서 고유해야 합니다. 예를 들어 `westus2`에는 이름이 `my-endpoint`인 엔드포인트가 하나만 있을 수 있습니다. 

## <a name="define-the-endpoint-configuration"></a>엔드포인트 구성 정의

온라인 엔드포인트에 모델을 배포하는 데 필요한 입력은 다음과 같습니다.

- 모델 파일(또는 작업 영역에 이미 등록된 모델의 이름 및 버전). 이 예에는 회귀를 수행하는 `scikit-learn` 모델이 있습니다.
- 모델을 채점하는 데 필요한 코드. 이 경우 `score.py` 파일이 있습니다.
- 모델이 실행되는 환경(여기서 볼 수 있듯이, conda 종속성이 있는 Docker 이미지 또는 Dockerfile이 환경이 될 수 있음).
- 인스턴스 유형 및 크기 조정 용량을 지정하는 설정.

다음 코드 조각은 위의 모든 정보를 캡처하는 `endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml` 파일을 보여 줍니다. 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!Note]
> 관리형 온라인 엔드포인트에 완전히 지정된 샘플 YAML을 [참조](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)용으로 사용할 수 있습니다.

엔드포인트 YAML 형식에 대한 참조는 아래와 같습니다. 이러한 특성을 지정하는 방법을 이해하려면 이 문서의 YAML 예제를 참조하거나 앞의 참고 사항에서 언급한 완전히 지정된 YAML 샘플을 참조하세요. 관리형 엔드포인트와 관련된 제한에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 리소스의 할당량 관리 및 증대](how-to-manage-quotas.md)를 참조하세요.

| 키 | Description |
| --- | --- |
| $schema    | [선택 사항] YAML 스키마입니다. 위 예제의 스키마를 브라우저에서 보면 YAML 파일에서 사용할 수 있는 모든 옵션을 볼 수 있습니다.|
| name       | 엔드포인트의 이름입니다. Azure 지역 수준에서 고유해야 합니다.|
| traffic | 엔드포인트에서 각 배포로 전환되는 트래픽의 비율입니다. 트래픽 값의 총 합은 100이 되어야 합니다. |
| auth_mode | 키 기반 인증의 경우 `key`를 사용하고, Azure 기계 학습 토큰 기반 인증의 경우 `aml_token`을 사용합니다. `key`는 만료되지 않지만 `aml_token`은 만료됩니다. `az ml endpoint list-keys` 명령을 사용하여 최신 토큰을 가져옵니다. |
| 배포 | 엔드포인트에서 만들 배포의 목록을 포함합니다. 이 경우 `blue`라는 하나의 배포만 있습니다. 여러 배포에 대한 자세한 내용은 [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)을 참조하세요.|

`deployment`의 특성:

| 키 | Description |
| --- | --- |
| name  | 배포의 이름 |
| model | 이 예에서는 `name`, `version` 및 `local_path` 모델 속성을 인라인으로 지정합니다. 모델 파일은 자동으로 업로드되고 등록됩니다. 인라인 사양의 단점은 모델 파일을 업데이트하려는 경우 버전을 수동으로 증분시켜야 한다는 것입니다. 관련 모범 사례는 아래 섹션의 **팁** 을 참조하세요. |
| code_configuration.code.local_path | 모델 채점을 위한 모든 Python 소스 코드가 포함된 디렉터리입니다. 중첩 디렉터리/패키지가 지원됩니다. |
| code_configuration.scoring_script | 위의 채점 디렉터리에 있는 Python 파일입니다. 이 Python 코드에는 `init()` 함수와 `run()` 함수가 있어야 합니다. `init()` 함수는 모델을 만들거나 업데이트한 후에 호출됩니다(모델을 메모리에 캐시하는 등의 용도로 사용할 수 있음). `run()` 함수는 실제 채점/예측을 수행하도록 엔드포인트를 호출할 때마다 호출됩니다. |
| environment | 모델 및 코드를 호스트할 환경의 세부 정보를 포함합니다. 이 예제에는 `name`, `version` 및 `path`를 포함하는 인라인 정의가 있습니다. 이 예제에서는 `environment.docker.image`가 이미지로 사용되며, 그 위에 `conda_file` 종속성이 설치됩니다. 자세한 내용은 아래 섹션의 **팁** 을 참조하세요. |
| instance_type | 배포 인스턴스를 호스트할 VM SKU입니다. 자세한 내용은 [관리형 온라인 엔드포인트에서 지원되는 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)를 참조하세요. |
| scale_settings.scale_type | 현재 이 값은 `manual`이 되어야 합니다. 엔드포인트 및 배포를 만든 후 스케일 업하거나 스케일 다운하려면 YAML에서 `instance_count`를 업데이트하고 `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>` 명령을 실행합니다.|
| scale_settings.instance_count | 배포의 인스턴스 수입니다. 예상되는 워크로드 값을 기준으로 합니다. 고가용성을 위해 최소한 `3` 이상으로 설정하는 것이 좋습니다. |

> [!Note]
> AKS(Azure Kubernetes Service)를 관리형 엔드포인트 대신 컴퓨팅 대상으로 사용하려면 다음을 수행합니다.
> 1. [Azure ML Studio를 사용](how-to-create-attach-compute-studio.md#whats-a-compute-target)하여 AKS 클러스터를 만들고 Azure Machine Learning 작업 영역에 컴퓨팅 대상으로 연결
> 2. 위의 관리형 엔드포인트 YAML 대신 이 [엔드포인트 YAML](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml)을 대상 AKS에 사용합니다. `target`의 값을 등록된 컴퓨팅 대상의 이름으로 변경하려면 YAML을 편집해야 합니다.
> 이 문서의 명령은 선택적 SLA 모니터링 및 Log Analytics 통합을 제외하고, 관리형 엔드포인트와 AKS 엔드포인트 간에 상호 교환이 가능합니다.

### <a name="registering-your-model-and-environment-separately"></a>모델 및 환경을 별도로 등록

 이 예제에서는 모델과 파일을 업로드할 환경 속성 `name`, `version` 및 `local_path`를 인라인으로 지정합니다. 내부적으로 CLI가 파일을 업로드하고 모델 및 환경을 자동으로 등록합니다. 프로덕션 환경에서는 모델 및 환경을 별도로 등록하고 YAML에 등록된 이름 및 버전을 지정하는 것이 모범 사례입니다. 형식은 `model:azureml:my-model:1` 또는 `environment:azureml:my-env:1`입니다.

 등록을 수행하려면 `model` 및 `environment`의 YAML 정의를 별도의 YAML 파일에 추출하고 `az ml model create` 및 `az ml environment create` 명령을 사용할 수 있습니다. 이러한 명령에 대해 자세히 알아보려면 `az ml model create -h` 및 `az ml environment create -h`를 실행합니다.

### <a name="using-different-cpu--gpu-instance-types"></a>여러 가지 CPU 및 GPU 인스턴스 유형 사용

위의 YAML은 범용 유형(`Standard_F2s_v2`) 및 GPU가 아닌 Docker 이미지를 사용합니다(YAML에서 `image` 특성 참조). GPU 컴퓨팅의 경우 GPU 컴퓨팅 유형 SKU 및 GPU Docker 이미지를 선택해야 합니다.

[관리형 온라인 엔드포인트에서 지원되는 VM SKU](reference-managed-online-endpoints-vm-sku-list.md)에서 지원되는 범용 및 GPU 인스턴스 유형을 볼 수 있습니다. Azure ML CPU 및 GPU 기본 이미지 목록은 [Azure Machine Learning 기본 이미지](https://github.com/Azure/AzureML-Containers)에서 확인할 수 있습니다.

### <a name="using-more-than-one-model"></a>두 개 이상의 모델 사용

현재 YAML에는 배포당 하나의 모델만 지정할 수 있습니다. 모델이 두 개 이상 있는 경우 이 제한을 해결하려면 모델을 등록할 때 모든 모델(파일 또는 하위 디렉터리)을 등록에 사용할 폴더에 복사합니다. 채점 스크립트에서 환경 변수 `AZUREML_MODEL_DIR`을 사용하여 모델 루트 폴더의 경로를 가져올 수 있습니다. 기본 디렉터리 구조는 유지됩니다.

## <a name="understand-the-scoring-script"></a>채점 스크립트 이해

> [!Tip]
> 관리형 온라인 엔드포인트의 채점 스크립트 형식은 이전 버전의 CLI 및 Python SDK에서 사용된 것과 동일한 형식입니다.

위의 YAML에 참조된 것처럼, `code_configuration.scoring_script`에는 `init()` 함수와 `run()` 함수가 있어야 합니다. 이 예제에서는 이 [score.py 파일](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)을 사용합니다. 컨테이너가 초기화/시작되면 `init()` 함수가 호출됩니다. 이 초기화는 일반적으로 배포를 만들거나 업데이트한 직후에 발생합니다. 이 예제에서와 같이 메모리에 모델을 캐시하는 것과 같은 글로벌 초기화 작업을 수행하는 논리를 여기에 작성합니다. `run()` 함수는 엔드포인트를 호출할 때마다 호출되며, 실제 채점/예측을 수행해야 합니다. 이 예제에서는 JSON 입력에서 데이터를 추출하고, `scikit-learn` 모델의 `predict()` 메서드를 호출하고, 결과를 반환합니다.

## <a name="deploy-and-debug-locally-using-local-endpoints"></a>로컬 엔드포인트를 사용하여 로컬로 배포 및 디버그

디버깅 시간을 절약하려면 로컬에서 엔드포인트를 테스트 실행하는 것이 **좋습니다**.

> [!Note]
> * 로컬에서 배포하려면 [Docker 엔진](https://docs.docker.com/engine/install/)이 설치되어 있어야 합니다.
> * Docker 엔진이 실행 중이어야 합니다. 일반적으로 엔진은 시작 시 실행됩니다. 그렇지 않은 경우 [여기에서 문제를 해결](https://docs.docker.com/config/daemon/#start-the-daemon-manually)할 수 있습니다.

> [!Important]
> 로컬 엔드포인트 배포의 목표는 Azure에 배포하기 전에 코드 및 구성의 유효성을 검사하고 디버그하는 것입니다. 로컬 배포에는 다음과 같은 제한 사항이 있습니다.
> - 로컬 엔드포인트는 트래픽 규칙, 인증, 스케일링 설정 또는 프로브 설정을 지원하지 **않습니다**. 
> - 로컬 엔드포인트는 엔드포인트당 하나의 배포만 지원합니다.
> - 현재 로컬 배포에는 예제 YAML과 같은 모델 및 환경의 인라인 사양이 필요합니다. 즉, 로컬 배포에서는 Azure 기계 학습 작업 영역에 등록된 모델 또는 환경에 대한 참조를 사용할 수 없습니다. 

### <a name="deploy-the-model-locally"></a>로컬에서 모델 배포

로컬에서 모델을 배포하려면 다음 명령을 실행합니다.

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

`--local` 플래그는 Docker 환경에 엔드포인트를 배포하도록 CLI에 지시합니다.

>[!NOTE]
>Windows 운영 체제를 사용하는 경우 이 명령과 이후 명령에서 `$ENDPOINT_NAME` 대신 `%ENDPOINT_NAME%`을 사용합니다.

### <a name="check-if-the-local-deployment-succeeded"></a>로컬 배포에 성공했는지 확인

로그를 확인하여 모델이 오류 없이 배포되었는지 확인합니다.

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-with-your-model"></a>로컬 엔드포인트를 호출하여 모델로 데이터 채점

엔드포인트를 호출하여 편리한 명령 `invoke`를 사용하고 JSON 파일에 저장된 쿼리 매개 변수를 전달하여 모델을 채점합니다.

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

curl 같은 REST 클라이언트를 사용하려는 경우 채점 URI가 필요합니다. 이는 `az ml endpoint show --local -n $ENDPOINT_NAME` 명령을 사용하여 가져올 수 있습니다. 반환된 데이터에서 `scoring_uri`라는 특성을 찾을 수 있습니다. 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>호출 작업의 출력에 대한 로그를 검토합니다.

`score.py` 예제에서 `run()` 메서드는 콘솔에 일부 출력을 로그합니다. `get-logs` 명령을 다시 사용하여 이 출력을 볼 수 있습니다.

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Azure에 관리형 온라인 엔드포인트 배포 

### <a name="deploy-to-azure"></a>Deploy to Azure

YAML 구성을 클라우드에 배포하려면 다음 명령을 실행합니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="deploy" :::

이 배포는 기본 환경/이미지가 처음으로 빌드되는지 여부에 따라 약 8-14분이 걸릴 수 있습니다. 동일한 환경을 사용하는 후속 배포는 더 빨라집니다.

> [!Tip]
> CLI 콘솔을 차단하지 않으려는 경우 명령에 `--no-wait` 플래그를 추가할 수 있습니다. 그러나 이렇게 하면 배포 상태의 대화형 표시가 중지됩니다.

> [!Tip]
> [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)을 사용하여 오류를 디버그합니다.

### <a name="check-the-status-of-the-deployment"></a>배포 상태 확인

`show` 명령에는 엔드포인트와 배포 모두에 대한 `provisioning_status`가 포함되어 있습니다.

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="get_status" :::

`list` 명령을 사용하여 작업 영역의 모든 엔드포인트를 테이블 형식으로 나열할 수 있습니다.

```azurecli
az ml endpoint list --output table
```

### <a name="check-if-the-cloud-deployment-succeeded"></a>클라우드 배포에 성공했는지 확인

로그를 확인하여 모델이 오류 없이 배포되었는지 확인합니다.

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

기본적으로 로그는 inference-server에서 풀합니다. 모델 및 코드와 같은 자산을 컨테이너에 탑재하는 storage-initializer의 로그를 보려면 `--container storage-initializer` 플래그를 추가합니다.

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>엔드포인트를 호출하여 모델로 데이터 채점

선택한 `invoke` 명령 또는 REST 클라이언트를 사용하여 엔드포인트를 호출하고 일부 데이터를 채점할 수 있습니다. 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

이전에 표시된 `get-logs` 명령을 다시 사용하여 호출 로그를 볼 수 있습니다.

REST 클라이언트를 사용하려면 `scoring_uri` 및 인증 키/토큰이 필요합니다. `scoring_uri`는 `show` 명령의 출력에서 볼 수 있습니다.
 
::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

`--query`를 사용하여 필요한 특성만 표시되도록 특성을 필터링하는 방법을 참고합니다. `--query`에 대한 자세한 내용은 [Azure CLI 명령 출력 쿼리](/cli/azure/query-azure-cli)에서 확인할 수 있습니다.

`get-credentials` 명령을 사용하여 필요한 자격 증명을 검색합니다.

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>[선택 사항] 배포 업데이트

코드, 모델, 환경 또는 스케일링 설정을 업데이트하려면 YAML 파일을 업데이트하고 `az ml endpoint update` 명령을 실행합니다. 

>[!IMPORTANT]
> 단일 `update` 명령에서 **하나의** 양상(트래픽, 스케일링 설정, 코드, 모델 또는 환경)만 수정할 수 있습니다. 

`update`의 작동 방식을 이해하려면 다음을 수행합니다.

1. `online/model-1/onlinescoring/score.py`파일을 엽니다.
1. `init()` 함수의 마지막 줄을 변경합니다. `logging.info("Init complete")` 다음에 `logging.info("Updated successfully")`를 추가합니다. 
1. 파일 저장
1. 명령 실행:
```azurecli
az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!IMPORTANT]
> YAML을 사용한 업데이트는 선언적입니다. 즉, YAML의 변경 내용이 기본 Azure Resource Manager 리소스(엔드포인트 및 배포)에 반영됩니다. 이 접근법을 사용하면 [GitOps](https://www.atlassian.com/git/tutorials/gitops)가 용이해집니다. 엔드포인트/배포에 대한 *모든* 변경 내용이 YAML을 거칩니다(`instance_count`도 해당). 부작용은 YAML에서 배포를 제거하고 파일을 사용하여 `az ml endpoint update`를 실행할 경우 해당 배포가 삭제된다는 것입니다. 다음 팁에 설명된 것처럼, YAML을 사용하지 않고도 `--set ` 플래그를 사용하여 업데이트를 수행할 수 있습니다.

5. 엔드포인트가 생성되거나 업데이트될 때 실행되는 `init()` 함수를 수정했으므로 로그에 `Updated successfully` 메시지가 표시됩니다. 다음을 실행하여 the0 로그를 검색합니다.
```azurecli
az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
```

드문 경우이지만 해결할 수 없는 문제로 인해 배포를 삭제하고 다시 만들려면 다음을 사용합니다.

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

이 `update` 명령은 로컬 엔드포인트에서도 작동합니다. 동일한 `az ml endpoint update` 명령에 `--local` 플래그를 사용합니다.

> [!Tip]
> `az ml endpoint update` 명령을 통해 [Azure CLI에서 사용할 수 있는 `--set` 매개 변수](/cli/azure/use-cli-effectively#generic-update-arguments)를 사용하여 YAML의 특성을 **재정의하거나** YAML 파일을 전달하지 않고 특정 특성을 설정할 수 있습니다. 단일 특성에 `--set`를 사용하는 것은 개발/테스트 시나리오에서 특히 유용합니다. 예를 들어 첫 번째 배포의 `instance_count`를 스케일 업하려면 `--set deployments[0].scale_settings.instance_count=2` 플래그를 사용하면 됩니다. 그러나 YAML이 업데이트되지 않으므로 이 기법을 사용하면 [GitOps](https://www.atlassian.com/git/tutorials/gitops)가 용이하지 않습니다.

### <a name="optional-monitor-sla-using-azure-monitor"></a>[선택 사항] Azure Monitor를 사용하여 SLA 모니터링

[관리형 온라인 엔드포인트 모니터링](how-to-monitor-online-endpoints.md)의 지침에 따라 SLA를 기준으로 메트릭을 확인하고 경고를 설정할 수 있습니다.

### <a name="optional-integrate-with-log-analytics"></a>[선택 사항] Log Analytics와 통합

`get-logs` 명령은 자동으로 선택된 인스턴스에서 마지막 몇 백 줄의 로그만 제공합니다. 그러나 Log Analytics는 지속적으로 로그를 저장하고 분석하는 방법을 제공합니다. 먼저 Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](/azure/azure-monitor/logs/quick-create-workspace#create-a-workspace)의 단계를 따르세요.

그런 다음, Azure Portal에서 다음을 수행합니다.

1. 리소스 그룹으로 이동
1. 엔드포인트 선택
1. **ARM 리소스 페이지** 선택
1. **진단 설정** 선택
1. **설정 추가** 선택: 로그 분석 작업 영역에 콘솔 로그 전송 사용

로그를 연결하는 데 최대 1시간이 걸릴 수 있습니다. 이 기간 이후에 일부 채점 요청을 보낸 후 다음 단계에 따라 로그를 확인합니다.

1. Log Analytics 작업 영역 열기 
1. 왼쪽 탐색 영역에서 **로그** 선택
1. 자동으로 열리는 **쿼리** 팝업 닫기
1. **AmlOnlineEndpointConsoleLog** 두 번 클릭
1. *실행* 을 선택합니다.

## <a name="delete-the-endpoint-and-deployment"></a>엔드포인트 및 배포 삭제

배포를 사용하지 않을 경우 아래 명령을 사용하여 배포를 삭제해야 합니다(엔드포인트 및 모든 기본 배포 삭제).

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>다음 단계

- [온라인 엔드포인트에 대한 안전한 롤아웃(미리 보기)](how-to-safely-rollout-managed-endpoints.md)|
- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)