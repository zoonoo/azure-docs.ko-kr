---
title: 지속적인 Azure Machine Learning 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning DevOps 확장을 사용하여 모델을 지속적으로 배포하는 방법을 알아봅니다. 새 모델 버전을 자동으로 확인하고 배포합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: tracking-python, deploy
ms.openlocfilehash: aae0194269d79371189998268bd87b9ea4a6b4d4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885545"
---
# <a name="continuously-deploy-models"></a>지속적으로 모델 배포

이 문서에서는 Azure DevOps에서 지속적인 배포를 사용하여 등록된 모델의 새 버전을 자동으로 확인하고 새 모델을 프로덕션으로 푸시하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Machine Learning 작업 영역에 모델을 이미 등록했다고 가정합니다. scikit-learn 모델을 학습하고 등록하는 방법에 대한 예제는 [이 자습서](how-to-train-scikit-learn.md)를 참조하세요.

## <a name="continuously-deploy-models"></a>지속적으로 모델 배포

[Azure DevOps](https://azure.microsoft.com/services/devops/)에 대한 Machine Learning 확장을 사용하여 모델을 지속적으로 배포할 수 있습니다. 새 기계 학습 모델이 Azure Machine Learning 작업 영역에 등록된 경우 Azure DevOps용 Machine Learning 확장을 사용하여 배포 파이프라인을 트리거할 수 있습니다.

1. 애플리케이션을 사용 가능한 플랫폼 또는 클라우드에 연속적으로 통합하고 제공하는 [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up)에 가입합니다. (Azure Pipelines는 [Machine Learning 파이프라인과](concept-ml-pipelines.md#compare) 동일하지 않습니다.)

1. [Azure DevOps 프로젝트를 만듭니다.](/azure/devops/organizations/projects/create-project)

1. [Azure Pipelines용 Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)을 설치합니다.

1. 아티팩트에 액세스할 수 있도록 서비스 연결을 사용하여 Azure Machine Learning 작업 영역에 대한 서비스 주체 연결을 설정합니다. 프로젝트 설정으로 이동하여 **서비스 연결** 을 선택한 다음 **Azure Resource Manager** 를 선택합니다.

    [Azure Resource Manager 선택![](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. **범위 수준** 목록에서 **AzureMLWorkspace** 를 선택하고 나머지 값을 입력합니다.

    ![AzureMLWorkspace 선택](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Azure Pipelines를 사용하여 기계 학습 모델을 지속적으로 배포하려면 파이프라인에서 **릴리스** 를 선택합니다. 새 아티팩트를 추가한 다음 이전에 만든 **AzureML 모델** 아티팩트 및 서비스 연결을 선택합니다. 배포를 트리거할 모델 및 버전을 선택합니다.

    [![AzureML 모델 선택](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. 모델 아티팩트에 모델 트리거를 사용합니다. 트리거를 켜면 해당 모델의 지정된 버전(최신 버전)이 작업 영역에 등록될 때마다 Azure DevOps 릴리스 파이프라인이 트리거됩니다.

    [![모델 트리거를 사용](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>다음 단계

ML 모델용 지속적인 배포에 대한 자세한 예제는 GitHub에서 아래 프로젝트를 확인하세요.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)