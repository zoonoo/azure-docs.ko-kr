---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542800"
---
## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](../articles/machine-learning/how-to-manage-workspace.md)를 참조 하세요.
- 모델. 학습 된 모델이 없는 경우 [이 자습서](https://aka.ms/azml-deploy-cloud)에 제공 된 모델 및 종속성 파일을 사용할 수 있습니다.
- [Machine Learning 서비스에 대 한 AZURE CLI (명령줄 인터페이스) 확장](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>작업 영역에 연결

Azure CLI 설명서의 지침에 따라 [구독 컨텍스트를 설정](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)합니다.

그런 다음 다음을 수행 합니다.

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

액세스 권한이 있는 작업 영역을 볼 수 있습니다.

## <a name="register-your-model"></a><a id="registermodel"></a>모델 등록

등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 작업 영역에서 단일 모델로 등록할 수 있습니다. 파일을 등록 한 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 한 모든 파일을 받을 수 있습니다.

> [!TIP]
> 모델을 등록할 때 학습 실행의 클라우드 위치 또는 로컬 디렉터리의 경로를 제공 합니다. 이 경로는 등록 프로세스의 일부로 업로드할 파일을 찾는 것입니다. 항목 스크립트에 사용 된 경로와 일치 하지 않아도 됩니다. 자세한 내용은 [항목 스크립트에서 모델 파일 찾기](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)를 참조 하세요.

기계 학습 모델은 Azure Machine Learning 작업 영역에 등록 됩니다. 모델은 Azure Machine Learning 또는 다른 위치에서 가져올 수 있습니다. 모델을 등록할 때 모델에 대 한 메타 데이터를 선택적으로 제공할 수 있습니다. `tags` `properties` 모델 등록에 적용 하는 및 사전을 사용 하 여 모델을 필터링 할 수 있습니다.

다음 예에서는 모델을 등록 하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML 학습 실행에서 모델 등록

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path`매개 변수는 모델의 클라우드 위치를 참조 합니다. 이 예제에서는 단일 파일의 경로를 사용 합니다. 모델 등록에 여러 파일을 포함 하려면를 `--asset-path` 파일을 포함 하는 폴더의 경로로 설정 합니다.

### <a name="register-a-model-from-a-local-file"></a>로컬 파일에서 모델 등록

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

모델 등록에 여러 파일을 포함 하려면를 `-p` 파일을 포함 하는 폴더의 경로로 설정 합니다.

에 대 한 자세한 내용은 `az ml model register` [참조 설명서](/cli/azure/ext/azure-cli-ml/ml/model)를 참조 하세요.

## <a name="define-an-entry-script"></a>항목 스크립트 정의

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>유추 구성 정의

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

다음 명령은 CLI를 사용 하 여 모델을 배포 하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

이 예에서는 구성에서 다음 설정을 지정 합니다.

* 모델에 Python이 필요 합니다.
* 배포 된 서비스로 전송 되는 웹 요청을 처리 하는 데 사용 되는 [항목 스크립트](#define-an-entry-script)
* 유추에 필요한 Python 패키지를 설명 하는 Conda 파일입니다.

유추 구성과 함께 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](../articles/machine-learning/how-to-deploy-custom-docker-image.md)을 참조 하세요.

## <a name="choose-a-compute-target"></a>계산 대상 선택

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>배포 구성 정의

배포 구성에 사용할 수 있는 옵션은 선택한 계산 대상에 따라 다릅니다.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

자세한 내용은 [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) 설명서를 참조 하세요.

## <a name="deploy-your-model"></a>모델 배포

이제 모델을 배포할 준비가 되었습니다.

### <a name="using-a-registered-model"></a>등록 된 모델 사용

Azure Machine Learning 작업 영역에 모델을 등록 한 경우 "mymodel: 1"을 모델 이름과 해당 버전 번호로 바꿉니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>로컬 모델 사용

모델을 등록 하지 않으려는 경우의 inferenceconfig.js에 "sourceDirectory" 매개 변수를 전달 하 여 모델을 제공할 로컬 디렉터리를 지정할 수 있습니다.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>리소스 삭제

배포 된 웹 서비스를 삭제 하려면를 사용 `az ml service <name of webservice>` 합니다.

작업 영역에서 등록 된 모델을 삭제 하려면 다음을 사용 합니다.`az ml model delete <model id>`

[Webservice를 삭제](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) 하 고 [모델을 삭제](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete) 하는 방법에 대해 자세히 알아보세요.