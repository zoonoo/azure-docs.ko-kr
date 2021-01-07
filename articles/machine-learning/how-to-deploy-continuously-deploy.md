---
title: 지속적으로 Azure Machine Learning 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning DevOps 확장을 사용 하 여 모델을 지속적으로 배포 하는 방법을 알아봅니다. 새 모델 버전을 자동으로 확인 하 고 배포 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 6043ea4e1366890033571c2ba78ecdb2e59f64e1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325207"
---
# <a name="continuously-deploy-models"></a>지속적으로 모델 배포

이 문서에서는 Azure DevOps에서 연속 배포를 사용 하 여 등록 된 모델의 새 버전을 자동으로 확인 하 고 이러한 새 모델을 프로덕션 환경으로 푸시하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 Azure Machine Learning 작업 영역에 모델을 이미 등록 했다고 가정 합니다. Scikit 모델을 학습 하 고 등록 하는 방법에 대 한 예제는 [이 자습서](how-to-train-scikit-learn.md) 를 참조 하세요.

## <a name="continuously-deploy-models"></a>지속적으로 모델 배포

[Azure DevOps](https://azure.microsoft.com/services/devops/)에 대 한 Machine Learning 확장을 사용 하 여 모델을 지속적으로 배포할 수 있습니다. 새 Machine Learning 모델이 Azure Machine Learning 작업 영역에 등록 된 경우 Azure DevOps 용 Machine Learning 확장을 사용 하 여 배포 파이프라인을 트리거할 수 있습니다.

1. 응용 프로그램을 사용 가능한 플랫폼 또는 클라우드에 지속적으로 통합 하 고 제공 하는 [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)에 등록 하세요. Azure Pipelines은 [Machine Learning 파이프라인과](concept-ml-pipelines.md#compare)동일 하지 않습니다.

1. [Azure DevOps 프로젝트를 만듭니다.](/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines에 대 한 Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)을 설치 합니다.

1. 서비스 연결을 사용 하 여 아티팩트에 액세스할 수 있도록 Azure Machine Learning 작업 영역에 대 한 서비스 사용자 연결을 설정 합니다. 프로젝트 설정으로 이동 하 여 **서비스 연결** 을 선택 하 고 **Azure Resource Manager** 를 선택 합니다.

    [![Azure Resource Manager 선택](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. **범위 수준** 목록에서 **AzureMLWorkspace** 를 선택 하 고 나머지 값을 입력 합니다.

    ![AzureMLWorkspace 선택](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Azure Pipelines를 사용 하 여 기계 학습 모델을 지속적으로 배포 하려면 파이프라인에서 **릴리스** 를 선택 합니다. 새 아티팩트를 추가한 다음 이전에 만든 **AzureML 모델** 아티팩트 및 서비스 연결을 선택 합니다. 배포를 트리거할 모델 및 버전을 선택 합니다.

    [![AzureML 모델 선택](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. 모델 아티팩트에 대해 모델 트리거를 사용 하도록 설정 합니다. 트리거를 켜면 해당 모델의 지정 된 버전 (최신 버전)이 작업 영역에 등록 될 때마다 Azure DevOps 릴리스 파이프라인이 트리거됩니다.

    [![모델 트리거를 사용 하도록 설정](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>다음 단계

ML 모델용 연속 배포에 대 한 자세한 예제는 GitHub에서 아래 프로젝트를 확인 하세요.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)