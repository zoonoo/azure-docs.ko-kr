---
title: CI/CD에 대 한 GitHub 작업
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 모델을 학습 하는 GitHub 작업 워크플로를 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 4336827dc7f8cb45f04e4cef94d79d1e6409d5c0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795212"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Azure Machine Learning GitHub 작업 사용

Azure Machine Learning에서 모델을 학습 하려면 [GitHub 작업](https://help.github.com/en/articles/about-github-actions) 을 시작 하세요. 

> [!NOTE]
> Azure Machine Learning에 대 한 GitHub 작업은 있는 그대로 제공 되며 Microsoft에서 완벽 하 게 지원 되지 않습니다. 특정 작업에 문제가 발생 하는 경우 작업에 대 한 리포지토리에서 문제를 엽니다. 예를 들어 aml-배포 작업에 문제가 발생 하는 경우 리포지토리의 문제를 보고 [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) 합니다.

## <a name="prerequisites"></a>필수 구성 요소 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [무료로](https://github.com/join)등록 하세요.  

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

파일에는 다음 4 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**연결** | 1. machine learning 작업 영역에 연결 합니다. <br /> 2. 계산 대상에 연결 합니다. |
|**실행** | 1. 학습 실행을 제출 합니다. |
|**배포** | 1. Azure Machine Learning 레지스트리에 모델을 등록 합니다. 1. 모델을 배포합니다. |

## <a name="create-repository"></a>리포지토리 만들기

[GitHub 작업 및 Azure Machine Learning 템플릿을 사용 하 여 ML Ops](https://github.com/machine-learning-apps/ml-template-azure)에서 새 리포지토리를 만듭니다. 

1. GitHub에서 [템플릿을](https://github.com/machine-learning-apps/ml-template-azure) 엽니다. 
2. **이 템플릿 사용** 을 선택합니다. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="이 템플릿 사용을 선택 합니다.":::
3. 템플릿에서 새 리포지토리를 만듭니다. 리포지토리 이름을 `ml-learning` 또는 원하는 이름으로 설정 합니다. 


## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 아래와 같이 App Service 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명을 포함 하는 JSON 개체입니다. 나중에이 JSON 개체를 복사 합니다.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

1. [GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가** 합니다.

2. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

## <a name="connect-to-the-workspace"></a>작업 영역에 연결

[Azure Machine Learning 작업 영역 작업](https://github.com/marketplace/actions/azure-machine-learning-workspace) 을 사용 하 여 Azure Machine Learning 작업 영역에 연결 합니다. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

기본적으로 작업에는 파일이 필요 `workspace.json` 합니다. JSON 파일에 다른 이름이 있는 경우 입력 매개 변수를 사용 하 여 지정할 수 있습니다 `parameters_file` . 파일이 없는 경우 리포지토리 이름으로 새 파일이 생성 됩니다.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
작업은 구성 파일에 작업 영역 Azure Resource Manager (ARM) 속성을 기록 합니다 .이 파일은 이후의 모든 Azure Machine Learning GitHub 작업에서 선택할 수 있습니다. 파일은에 저장 됩니다 `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Azure Machine Learning에서 계산 대상에 연결

[Azure Machine Learning compute 작업](https://github.com/Azure/aml-compute) 을 사용 하 여 Azure Machine Learning에서 계산 대상에 연결 합니다.  계산 대상이 있는 경우 작업이 연결 됩니다. 그렇지 않으면 동작에서 새 계산 대상이 생성 됩니다. [AML 계산 작업](https://github.com/Azure/aml-compute) 은 azure ML 계산 클러스터 및 Azure Kubernetes 서비스 (AKS)만 지원 합니다. 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>교육 실행 제출

[Azure Machine Learning 학습 작업](https://github.com/Azure/aml-run) 을 사용 하 여 Azure Machine Learning에 대 한 평가기 또는 파이프라인을 제출 합니다. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>레지스트리에 모델 등록

[모델 등록 작업](https://github.com/Azure/aml-registermodel) 을 사용 하 여 Azure Machine Learning에 모델을 등록할 Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>ACI로 Azure Machine Learning에 모델 배포

[Azure Machine Learning 배포 작업](https://github.com/Azure/aml-deploy) 을 사용 하 여 모델을 배포 하 고 모델의 끝점을 만들 수 있습니다. Azure Machine Learning 배포를 사용 하 여 Azure Kubernetes Service에 배포할 수도 있습니다. Azure Kubernetes Service에 배포 하는 모델에 대 한 [이 샘플 워크플로](https://github.com/Azure-Samples/mlops-enterprise-template) 를 참조 하세요.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>전체 예제

모델을 학습 하 고 Azure Machine Learning에 배포 합니다. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹 및 리포지토리가 더 이상 필요 하지 않은 경우 리소스 그룹 및 GitHub 리포지토리를 삭제 하 여 배포 된 리소스를 정리 합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md)
