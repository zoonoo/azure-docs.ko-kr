---
title: Azure Cosmos DB 계정에서 노트북 사용(미리 보기)
description: Azure Cosmos DB의 기본 제공 노트북을 사용하면 포털 내에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 Cosmos 계정에 대해 이 기능을 사용하도록 설정하는 방법에 대해 설명합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768012"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB 계정에 대한 전자 필기장 사용(미리 보기)

> [!IMPORTANT]
> Azure Cosmos DB용 기본 제공 노트북은 현재 호주 동부, 미국 동부, 미국 동부 2, 북유럽, 미국 중남부, 동남아시아, 영국 남부, 서유럽 및 미국 서부 2의 Azure 지역에서 사용할 수 있습니다. 전자 필기장을 사용하려면 [전자 필기장이 있는 새 계정을 만들거나](#enable-notebooks-in-a-new-cosmos-account) 이러한 지역 중 하나에서 [기존 계정에서 전자 필기장을 사용하도록 설정합니다.](#enable-notebooks-in-an-existing-cosmos-account)

Azure Cosmos DB의 기본 제공 Jupyter 노트북을 사용하면 Azure 포털에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 Azure Cosmos DB 계정에 대해 이 기능을 사용하도록 설정하는 방법을 설명합니다.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>새로운 코스모스 계정에서 노트북 사용
1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
1. **리소스** > 만들기**를** > 선택 데이터베이스**Azure 코스모스 DB**.
1. Azure **Cosmos DB 계정 만들기** 페이지에서 **전자 필기장을 선택합니다.** 
 
    ![Azure 코스모스 DB 블레이드 만들기에서 노트북 옵션 선택](media/enable-notebooks/create-new-account-with-notebooks.png)
1. **검토 + 만들기를**선택합니다. **네트워크** 및 태그 옵션을 건너뛸 수 **있습니다.** 
1. 계정 설정을 검토한 다음, **만들기**를 선택합니다. 계정을 만드는 데 몇 분이 걸립니다. 포털 페이지에 **배포가 완료됨**이 표시되기를 기다립니다. 

    ![Azure Portal 알림 창](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. **리소스로 이동**을 선택하여 Azure Cosmos DB 계정 페이지로 이동합니다. 

    ![Azure Cosmos DB 계정 페이지](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. **데이터 탐색기** 창으로 이동합니다. 이제 노트북 작업 영역이 표시됩니다.

    ![새로운 Azure 코스모스 DB 노트북 작업 공간](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>기존 Cosmos 계정에서 노트북 사용
기존 계정에서 전자 필기장을 사용하도록 설정할 수도 있습니다. 이 단계는 계정당 한 번만 수행해야 합니다.

1. 코스모스 계정의 **데이터 탐색기** 창으로 이동합니다.
1. **노트북 활성화를 선택합니다.**

    ![데이터 탐색기에서 새 전자 필기장 작업 영역 만들기](media/enable-notebooks/enable-notebooks-workspace.png)
1. 그러면 새 전자 필기장 작업 영역을 만들라는 메시지가 표시됩니다. **설정 완료를 선택합니다.**
1. 이제 계정에서 노트북을 사용할 수 있습니다!

## <a name="create-and-run-your-first-notebook"></a>첫 번째 전자 필기장 만들기 및 실행

전자 필기장을 사용할 수 있는지 확인하려면 샘플 전자 필기장 에서 전자 필기장 중 하나를 선택합니다. 이렇게 하면 전자 필기장의 복사본이 작업 영역에 저장되고 열립니다.

이 예제에서는 **GettingStarted.ipynb**을 사용합니다. 

![점점 시작.ipynb 노트북 보기](media/enable-notebooks/select-getting-started-notebook.png)

전자 필기장을 실행하려면 다음을 수행합니다.
1. 파이썬 코드가 포함된 첫 번째 코드 셀을 선택합니다. 
1. 셀을 실행하려면 **실행을** 선택합니다. Shift + **Enter를** 사용하여 셀을 실행할 수도 있습니다.
1. 리소스 창을 새로 고쳐 서 만든 데이터베이스 및 컨테이너를 확인 합니다.

    ![실행 시작 노트북](media/enable-notebooks/run-first-notebook-cell.png)

내 전자 **필기장** 메뉴에서 **파일 업로드를** 선택하여 새 전자 필기장을 선택하여 새 전자 필기장을 선택하거나 기존 전자 필기장(.ipynb) 파일을 업로드할 수도 **있습니다.** 

![새 전자 필기장 만들기 또는 업로드](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 주피터 노트북의](cosmosdb-jupyter-notebooks.md) 이점에 대해 알아보기
