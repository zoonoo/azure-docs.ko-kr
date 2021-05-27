---
title: 2\.0 CLI 설치, 설정 및 사용
titleSuffix: Azure Machine Learning
description: Machine Learning용 Azure CLI 확장을 설치, 설정 및 사용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 05/25/2021
ms.reviewer: laobri
ms.openlocfilehash: 19534a5b55ea666233bb1a9b66916791e934908a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110476616"
---
# <a name="install-set-up-and-use-the-20-cli-preview"></a>2\.0 CLI 설치, 설정 및 사용(미리 보기)

[Azure CLI](/cli/azure/)에 대한 `ml` 확장(미리 보기)은 Azure Machine Learning을 위한 차세대 인터페이스입니다. 이를 통해 모델 수명 주기를 추적하는 동안 데이터 과학 확장을 가속화하는 기능을 사용하여 명령줄에서 모델을 학습하고 배포할 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
- **로컬 환경** 에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

    > [!TIP]
    > [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)을 사용하는 경우 CLI는 브라우저를 통해 액세스하고 클라우드에 있습니다.

## <a name="installation"></a>설치

새 Machine Learning 확장에는 **Azure CLI 버전이 필요`>=2.15.0`** 합니다. 다음 요구 사항이 충족되는지 확인합니다.

```azurecli
az version
```

그렇지 않으면 [Azure CLI를 업그레이드](/cli/azure/update-azure-cli)합니다.

설치한 Azure CLI 확장을 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-configure-cli.sh" id="az_extension_list":::

`azure-cli-ml` 확장을 포함하여 `ml` 네임스페이스를 사용하는 충돌 확장이 설치되어 있지 않은지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-configure-cli.sh" id="az_extension_remove":::

이제 `ml` 확장을 설치합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

도움말 명령을 실행하여 설치를 확인하고 사용 가능한 하위 명령을 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-configure-cli.sh" id="az_ml_verify":::

확장을 최신 버전으로 업그레이드할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-configure-cli.sh" id="az_ml_update":::

## <a name="set-up"></a>설정

로그인:

```azurecli
az login
```

여러 Azure 구독에 대한 액세스 권한이 있는 경우 활성 구독을 설정할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-configure-cli.sh" id="az_account_set":::

Azure 리소스 그룹이 아직 없는 경우 만들 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_group_create":::

기계 학습 하위 명령에는 `--workspace/-w` 및 `--resource-group/-g` 매개 변수가 필요합니다. 반복적으로 입력하지 않으려면 기본값을 구성합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> 대부분의 코드 예제에서는 기본 작업 영역 및 리소스 그룹을 설정했다고 가정합니다. 명령줄에서 이러한 항목을 재정의할 수 있습니다.

이제 기계 학습 작업 영역을 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_workspace_create":::

## <a name="hello-world"></a>Hello World

이를 수행하려면 예제 리포지토리를 복제하고 `cli` 하위 디렉터리로 변경합니다.

```azurecli-interactive
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples/cli
```

Python을 통해 로컬로 hello world를 실행하려면 `jobs` 하위 디렉터리의 예를 참조하세요.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/hello-world.yml":::

> [!IMPORTANT]
> [Docker](https://docker.io)는 로컬에서 설치하고 실행해야 합니다.

작업을 제출하고, 로그를 콘솔 출력으로 스트리밍하고, Azure Machine Learning 스튜디오에서 실행을 엽니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-configure-cli.sh" id="hello_world":::

> [!IMPORTANT]
> Docker 이미지를 로컬로 끌어와서 Azure ML 작업이 실행되기 때문에 처음 실행하는 데 몇 분 정도 걸릴 수 있습니다. 후속 실행은 이미지를 로컬로 캐시하고 더 빠르게 완료할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Visual Studio Code Azure Machine Learning 확장 설정](how-to-setup-vs-code.md)
- [Machine Learning CLI 확장을 사용하여 모델 학습(미리 보기)](how-to-train-cli.md)
- [Azure Machine Learning Visual Studio Code 확장을 사용하여 이미지 분류 TensorFlow 모델 학습](tutorial-train-deploy-image-classification-model-vscode.md)
