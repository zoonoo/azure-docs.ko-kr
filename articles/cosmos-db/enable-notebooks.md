---
title: Azure Cosmos DB 계정에서 노트북 사용 (미리 보기)
description: Azure Cosmos DB의 기본 제공 노트북을 사용 하 여 포털 내에서 데이터를 분석 하 고 시각화할 수 있습니다. 이 문서에서는 Cosmos 계정에 대해이 기능을 사용 하도록 설정 하는 방법을 설명 합니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/09/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: b8ce745a0a0db96e7f6b9e3a0140d46364246e4a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379610"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB 계정에 대해 노트북 사용 (미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 기본 제공 노트북은 현재 [29 개 지역](#supported-regions)에서 사용할 수 있습니다. 노트북을 사용 하려면 [새 Cosmos 계정을 만들거나](#create-a-new-cosmos-account) 이러한 지역 중 하나에 있는 [기존 계정에서 노트북을 사용 하도록 설정](#enable-notebooks-in-an-existing-cosmos-account) 합니다. 

Azure Cosmos DB의 기본 제공 Jupyter Notebook을 사용하여 Azure Portal에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 Azure Cosmos DB 계정에 대해 이 기능을 사용하도록 설정하는 방법을 설명합니다.

## <a name="create-a-new-cosmos-account"></a>새 Cosmos 계정 만들기
2021 년 2 월 10 일부 터 [지원 되는 지역](#supported-regions) 중 하나에서 만들어진 새 Azure Cosmos 계정은 자동으로 노트북을 사용 하도록 설정 됩니다. 노트북을 사용 하도록 설정 하는 데 필요한 추가 구성은 없습니다. 새 계정을 만들려면 다음 지침을 따르십시오.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **리소스 만들기** > **데이터베이스** > **Azure Cosmos DB** 를 선택합니다.
1. 계정에 대 한 기본 설정을 입력 합니다. 
 
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Azure Cosmos DB에 대한 새 계정 페이지":::

1. **검토 + 만들기** 를 선택합니다. **네트워크** 및 **태그** 옵션을 건너뛸 수 있습니다. 
1. 계정 설정을 검토한 다음, **만들기** 를 선택합니다. 계정을 만드는 데 몇 분이 걸립니다. 포털 페이지에 **배포가 완료됨** 이 표시되기를 기다립니다. 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure Portal 알림 창":::

1. **리소스로 이동** 을 선택하여 Azure Cosmos DB 계정 페이지로 이동합니다.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB 계정 페이지":::

1. **데이터 탐색기** 창으로 이동 합니다. 이제 전자 필기장 작업 영역이 표시 됩니다.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="새 Azure Cosmos DB 노트북 작업 영역":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>기존 Cosmos 계정에서 노트북을 사용 하도록 설정

기존 계정에서 노트북을 사용 하도록 설정할 수도 있습니다. 이 단계는 계정 마다 한 번만 수행 해야 합니다.

1. Cosmos 계정의 **데이터 탐색기** 창으로 이동 합니다.
1. **전자 필기장 사용** 을 선택 합니다.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="데이터 탐색기에서 새 노트북 작업 영역 만들기":::

1. 그러면 새 전자 필기장 작업 영역을 만들라는 메시지가 표시 됩니다. **설정 완료를 선택 합니다.**
1. 이제 계정이 노트북을 사용 하도록 설정 되었습니다.

## <a name="create-and-run-your-first-notebook"></a>첫 번째 노트북 만들기 및 실행

노트북을 사용할 수 있는지 확인 하려면 샘플 노트북에서 노트북 중 하나를 선택 합니다. 그러면 노트의 복사본이 작업 영역에 저장 되 고 열립니다.

이 예제에서는 **Gettingstarted를 사용 합니다. ipa**. 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Geta 뷰를 시작 했습니다. ipynb 노트북":::

노트북을 실행 하려면:
1. Python 코드를 포함 하는 첫 번째 코드 셀을 선택 합니다. 
1. **실행** 을 선택 하 여 셀을 실행 합니다. **Shift + Enter** 를 사용 하 여 셀을 실행할 수도 있습니다.
1. 리소스 창을 새로 고쳐 만들어진 데이터베이스 및 컨테이너를 확인 합니다.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="시작 노트북 실행":::

**내 전자** 필기장 메뉴에서 **파일 업로드** **를 선택 하 여** 새 노트북을 만들거나 기존 노트북 (.ipokb) 파일을 업로드할 수도 있습니다. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="새 노트북 만들기 또는 업로드":::

## <a name="supported-regions"></a>지원되는 지역
Azure Cosmos DB에 대 한 기본 제공 노트북은 현재 29 개 Azure 지역에서 사용할 수 있습니다. 이러한 지역에서 만든 새 Azure Cosmos 계정은 노트북을 자동으로 사용 하도록 설정 됩니다. 노트북은 사용자 계정에서 무료로 제공 됩니다. 

- 오스트레일리아 중부
- 오스트레일리아 중부 2
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 브라질 남부
- 캐나다 중부
- 캐나다 동부
- 인도 중부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 프랑스 중부
- 프랑스 남부
- 독일 북부
- 독일 중서부
- 일본 서부
- 한국 남부
- 미국 중 북부
- 북유럽
- 미국 중 남부
- 동남 아시아
- 스위스 북부
- 아랍에미리트 중부
- 영국 남부
- 영국 서부
- 미국 서 부 중부
- 서유럽
- 인도 서부
- 미국 서부 2

## <a name="next-steps"></a>다음 단계

* [Jupyter 노트북 Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) 의 이점에 대해 알아봅니다.
* [노트북 샘플 갤러리 탐색](https://cosmos.azure.com/gallery.html)
* [Python Notebook 기능 및 명령 사용](use-python-notebook-features-and-commands.md)
* [C# Notebook 기능 및 명령 사용](use-csharp-notebook-features-and-commands.md)
* [GitHub 리포지토리에서 노트북 가져오기](import-github-notebooks.md)


