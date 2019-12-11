---
title: 포털에서 Azure Machine Learning 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure Portal에서 Azure Machine Learning 작업 영역을 만들고, 확인 하 고, 삭제 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 80b554b7dd4f37e1a215892962d8ec5622d8ae5c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974039"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure Portal에서 Azure Machine Learning 작업 영역 만들기 및 관리
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure Machine Learning](overview-what-is-azure-ml.md)에 대 한 Azure Portal에서 [**Azure Machine Learning 작업 영역**](concept-workspace.md) 을 만들고, 확인 하 고, 삭제 합니다.  포털은 작업 영역을 시작 하는 가장 쉬운 방법 이지만 변경 내용 또는 automation 증가 요구 사항에 따라 [CLI를 사용 하 여](reference-azure-machine-learning-cli.md)작업 영역을 만들고 삭제할 수도 있습니다 (예를 들어, [Python 코드를](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 사용 하거나 [VS Code 확장을 통해](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)).

## <a name="create-a-workspace"></a>Munkaterületek létrehozása

작업 영역을 만들려면 Azure 구독이 필요 합니다. Azure 구독이 없는 경우 시작 하기 전에 무료 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 을 사용해 보세요.

1. Azure 구독에 대 한 자격 증명을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다. 

1. Azure Portal의 왼쪽 위 모퉁이에서 **+ 리소스 만들기**를 선택 합니다.

      ![Új erőforrás létrehozása](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. 검색 표시줄을 사용 하 여 **Machine Learning**를 찾을 수 있습니다.

1. **Machine Learning**를 선택 합니다.

1. **Machine Learning** 창에서 **만들기** 를 선택 하 여 시작 합니다.

1. 새 작업 영역을 구성 하려면 다음 정보를 제공 합니다.

   Mező|Leírás 
   ---|---
   Munkaterület neve |작업 영역을 식별 하는 고유한 이름을 입력 합니다. 이 예제에서는 **문서 ws**를 사용 합니다. 이름은 리소스 그룹에서 고유 해야 합니다. 쉽게 회수할 수 있는 이름을 사용 하 고 다른 사용자가 만든 작업 영역을 구분 합니다.  
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Az előfizetés valamelyik meglévő erőforráscsoportját használja, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. 리소스 그룹은 Azure 솔루션에 관련 된 리소스를 보유 합니다. 이 예제에서는 **문서-aml**을 사용 합니다. 
   Földrajzi egység | 사용자에 게 가장 가까운 위치를 선택 하 고 데이터 리소스를 선택 하 여 작업 영역을 만듭니다.
   작업 영역 버전 | **기본** 또는 **엔터프라이즈**를 선택 합니다.  이 작업 영역 버전은 액세스 및 가격 책정을 사용할 수 있는 기능을 결정 합니다. [Basic 및 Enterprise edition 제품](overview-what-is-azure-ml.md#sku)에 대해 자세히 알아보세요. 

    ![작업 영역 구성](media/how-to-manage-workspace/select-edition.png)

1. 작업 영역 구성을 완료 한 후 **만들기**를 선택 합니다. 

   > [!Warning] 
   > 클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다.

   프로세스가 완료 되 면 배포 성공 메시지가 표시 됩니다. 
 
 1. 새 작업 영역을 보려면 **리소스로 이동**을 선택 합니다.

### <a name="download-a-configuration-file"></a>구성 파일 다운로드

1. [노트북 VM](tutorial-1st-experiment-sdk-setup.md#azure)을 만들 경우에는이 단계를 건너뜁니다.

1. 로컬 환경에서이 작업 영역을 참조 하는 코드를 사용할 계획인 경우 작업 영역의 **개요** 섹션에서 **config.xml 다운로드** 를 선택 합니다.  

   ![Web.config를 다운로드 합니다.](./media/how-to-manage-workspace/configure.png)
   
   Python 스크립트 또는 Jupyter 노트북을 사용 하 여 파일을 디렉터리 구조에 저장 합니다. 같은 디렉터리, 이름이 *azureml*인 하위 디렉터리 또는 부모 디렉터리에 있을 수 있습니다. 노트북 VM을 만들 때이 파일은 VM의 올바른 디렉터리에 추가 됩니다.

## <a name="upgrade"></a>Enterprise edition으로 업그레이드

작업 영역을 Basic edition에서 Enterprise edition으로 업그레이드 하 여 낮은 코드 환경 및 향상 된 보안 기능 등의 향상 된 기능을 활용할 수 있습니다.

1. [Azure Machine Learning studio](https://ml.azure.com)에 로그인 합니다.

1. 업그레이드 하려는 작업 영역을 선택 합니다.

1. 페이지의 오른쪽 위에서 자세히 **알아보기** 를 선택 합니다.

   [작업 영역을 업그레이드 ![](media/how-to-manage-workspace/upgrade.png)](media/how-to-manage-workspace/upgrade.png#lightbox)

1. 표시 되는 창에서 **업그레이드** 를 선택 합니다.


> [!IMPORTANT]
> Enterprise edition 작업 영역을 기본 버전 작업 영역으로 다운 그레이드할 수 없습니다. 

## <a name="view"></a>작업 영역 찾기

1. 위쪽 검색 필드에 **Machine Learning**을 입력 합니다.  

1. **Machine Learning**를 선택 합니다.

   ![Azure Machine Learning 작업 영역 검색](media/how-to-manage-workspace/find-workspaces.png)

1. 찾은 작업 영역 목록을 살펴봅니다. 구독, 리소스 그룹 및 위치를 기준으로 필터링 할 수 있습니다.  

1. 해당 속성을 표시할 작업 영역을 선택 합니다.

## <a name="delete-a-workspace"></a>Munkaterület törlése

삭제 하려는 작업 영역의 맨 위에 있는 삭제 단추를 사용 합니다.

  ![삭제 단추](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

전체 길이 자습서에 따라 작업 영역을 사용 하 여 Azure Machine Learning으로 모델을 작성, 학습 및 배포 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 모델 학습](tutorial-train-models-with-aml.md)
