---
title: Azure Machine Learning 작업 영역 만들기 및 관리
description: Azure Portal에서 Azure Machine Learning 작업 영역을 만들고, 살펴보고, 삭제하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 7d01a2e3ebd46315966c82a43a17ffc5b329b829
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954350"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역 만들기 및 관리

이 문서에서는 [Azure Machine Learning 서비스](overview-what-is-azure-ml.md)에 대한 Azure Portal에서 [**Azure Machine Learning 작업 영역**](concept-azure-machine-learning-architecture.md#workspace)을 만들고, 살펴보고, 삭제할 것입니다.  [CLI를 사용하여](reference-azure-machine-learning-cli.md) 또는 [Python 코드를 사용하여](http://aka.ms/aml-sdk) 작업 영역을 만들고 삭제할 수도 있습니다.

작업 영역을 만들려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-workspace"></a>작업 영역 만들기 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>작업 영역 보기

1. 포털의 왼쪽 위 모서리에서 **모든 서비스**를 선택합니다. 

1. **모든 서비스** 필터 필드에 **Machine Learning 작업 영역**을 입력합니다.  

   ![Azure Machine Learning 작업 영역 검색](media/how-to-manage-workspace/allservices-search1.png)

1. 필터 결과에서 **Machine Learning 작업 영역**을 선택하여 작업 영역 목록을 표시합니다. 

   ![Azure Machine Learning 작업 영역 검색](media/how-to-manage-workspace/allservices-search.PNG)

1. 검색된 작업 영역 목록을 살펴봅니다. 구독, 리소스 그룹 및 위치를 기준으로 필터링 할 수 있습니다.  

   ![Azure Machine Learning 작업 영역 목록](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. 방금 만든 작업 영역을 선택하여 속성을 표시합니다.

   ![png](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>작업 영역 삭제

삭제하려는 작업 영역 맨 위에 있는 [삭제] 단추를 사용합니다.

  ![png](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>리소스 정리 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

전체 자습서의 단계에 따라 작업 영역을 사용하여 Azure Machine Learning 서비스로 모델을 빌드, 교육 및 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: 모델 교육](tutorial-train-models-with-aml.md)
