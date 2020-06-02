---
title: 자습서 - Azure ML 작업 영역 만들기 - Resource Manager 템플릿
description: 이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 기계 학습용 Azure 작업 영역을 신속하게 배포합니다.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790718"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>자습서: Resource Manager 템플릿을 사용하여 Azure Machine Learning 작업 영역 배포 | Microsoft Docs
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning 작업 영역을 만드는 방법을 보여 줍니다. Azure Machine Learning 작업 영역은 기준 데이터 세트의 모든 기계 학습 자산을 배포된 모델로 구성합니다. 작업 영역은 동료와 함께 실험을 생성, 실행 및 검토하고, 컴퓨팅 리소스의 학습 및 추론을 관리하고, 배포된 모델을 모니터링하고 버전을 관리할 수 있는 단일 위치입니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://aka.ms/AMLFree)을 만듭니다.

* **로컬 환경**에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 필요합니다.

## <a name="create-a-workspace"></a>작업 영역 만들기

### <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-machine-learning-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

다음 리소스는 템플릿에 정의되어 있습니다.

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Azure ML 작업 영역을 만듭니다. 이 템플릿에서 위치와 이름은 사용자가 전달하거나 대화형으로 입력할 수 있는 매개 변수입니다.

### <a name="deploy-the-template"></a>템플릿 배포 

Azure CLI에서 템플릿을 사용하려면 로그인하고 구독을 선택합니다([Azure CLI를 사용하여 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) 참조). 다음을 실행합니다.

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

위의 명령을 실행하는 경우 다음을 입력합니다.

1. 생성된 리소스 그룹 및 Azure ML 작업 영역 이름의 기준이 될 프로젝트 이름
1. 배포하려는 Azure 위치

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure ML 작업 영역을 보려면 다음을 수행합니다.

1. [https://editor.swagger.io](https://portal.azure.com ) 으로 이동합니다. 
1. 로그인 
1. 방금 만든 작업 영역 선택

Azure Machine Learning 홈 페이지가 표시됩니다. 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Azure ML 작업 영역의 스크린샷":::

배포와 연결된 모든 리소스를 보려면 왼쪽 상단에서 해당 작업 영역 이름이 있는 링크를 클릭합니다(`my_templated_ws` 스크린샷). 이 링크를 클릭하면 Azure Portal의 리소스 그룹으로 이동합니다. 리소스 그룹 이름은 `{projectName}rg`이고 작업 영역의 이름은 `{projectName}ws`입니다.

## <a name="clean-up-resources"></a>리소스 정리

이 작업 영역을 사용하지 않으려면 삭제합니다. 작업 영역이 다른 리소스(예: 스토리지 계정)와 연결되어 있으므로 생성된 전체 리소스 그룹을 삭제하는 것이 좋습니다. "삭제" 단추를 클릭하고 확인하여 포털을 통해 리소스 그룹을 삭제할 수 있습니다. 또는 CLI에서 리소스 그룹을 삭제할 수 있습니다. 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning 작업 영역을 만들었습니다. Azure Machine Learning을 살펴보려면 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
> [자습서: Python SDK로 첫 번째 ML 실험 만들기 시작](tutorial-1st-experiment-sdk-setup.md)
