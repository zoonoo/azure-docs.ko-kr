---
title: Azure Cosmos DB 노트북 갤러리에 노트북 게시
description: 공개 갤러리에서 노트북을 다운로드 하 고, 편집 하 고, 자신의 노트북을 갤러리에 게시 하는 방법에 대해 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039328"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Azure Cosmos DB 노트북 갤러리에 노트북 게시
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 기본 제공 Jupyter 노트북은 Azure Portal의 Azure Cosmos DB 계정에 직접 통합 됩니다. 이러한 노트북을 사용 하 여 Azure Portal에서 데이터를 분석 하 고 시각화할 수 있습니다. Azure Cosmos DB에 대 한 기본 제공 노트북은 현재 [많은 지역](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)에서 사용할 수 있습니다. 노트북을 사용 하려면 [새 Cosmos 계정을 만들거나](create-cosmosdb-resources-portal.md) 이러한 지역 중 하나에 있는 [기존 계정에서 노트북을 사용 하도록 설정](enable-notebooks.md) 합니다.

Azure Portal의 노트북 환경에는 Azure Cosmos DB 팀에서 게시 한 몇 가지 샘플이 있습니다. 또한 사용자 고유의 노트북을 게시 하 고 공유할 수 있는 공용 갤러리가 있습니다. 갤러리에 노트북을 게시 한 후에는 모든 Azure Cosmos DB 사용자가 보고 사용할 수 있습니다. 이 문서에서는 공용 갤러리에서 노트북을 사용 하 고 갤러리에 노트북을 게시 하는 방법을 알아봅니다.

## <a name="download-a-notebook-from-the-gallery"></a>갤러리에서 노트북 다운로드

다음 단계를 사용 하 여 갤러리에서 자신의 노트북 작업 영역으로 전자 필기장을 보고 다운로드 합니다.

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 노트북 환경에서 사용 하도록 설정 된 Azure Cosmos DB 계정으로 이동 합니다.

1. **데이터 탐색기**  >  **노트북**  >  **갤러리**  >  **공용 갤러리** 탭으로 이동 합니다.

1. 갤러리에서 노트북을 보거나 게시 하기 전에 [준수 코드](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  를 적용 합니다. 사용자는 구독 수준에 따라 사용자 단위로 기록 됩니다. 다른 구독으로 전환 하는 경우 갤러리에 액세스 하기 전에 다시 수락 해야 합니다. 준수 코드를 적용 하려면 확인란을 선택 하 고 **계속** 합니다.

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="전자 필기장 공용 갤러리로 이동 하 여 수행 하는 코드를 적용 합니다.":::

1. 다음으로, 특정 노트북을 즐겨찾기 탭에 추가 하거나 다운로드할 수 있습니다. 노트북을 다운로드 하면 노트북 작업 영역에 복사 되어 실행 하거나 편집할 수 있습니다.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="갤러리에서 작업 영역으로 노트북을 다운로드 합니다.":::

## <a name="publish-a-notebook-to-the-gallery"></a>갤러리에 노트북 게시

고유한 노트북을 빌드하거나 다른 시나리오에 맞게 기존 노트북을 편집 하는 경우 갤러리에 게시 하는 것이 좋습니다. 갤러리에 노트북을 게시 한 후에는 다른 사용자가 해당 노트북에 액세스할 수 있습니다. [노트북 샘플 갤러리를 탐색](https://cosmos.azure.com/gallery.html) 하 여 현재 사용할 수 있는 노트북을 볼 수 있습니다.

다음 단계를 사용 하 여 노트북을 게시 합니다.

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 노트북 환경에서 사용 하도록 설정 된 Azure Cosmos DB 계정으로 이동 합니다.

1. **데이터 탐색기**  >  **노트북**  >  **내 노트북** 탭으로 이동 합니다.

1. 게시 하려는 노트북으로 이동 합니다. 명령 모음에서 **저장** 단추를 선택 하 고 **갤러리에 게시를** 선택 합니다.

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="갤러리에 게시할 노트북을 선택 합니다.":::

   ...를 선택 하 여 **갤러리에 게시** 옵션을 찾을 수도  있습니다. 노트북 이름 옆에 있는 단추:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="갤러리에 게시할 노트북을 선택 하는 또 다른 방법입니다.":::

1. 다음 세부 정보를 사용 하 여 **갤러리에 게시 폼을** 채웁니다.

   * **이름:** 노트북을 식별 하는 친숙 한 이름입니다.
   * **설명:**  노트북의 용도에 대 한 간단한 설명입니다.
   * **태그:** 태그는 선택 사항이 며 키워드로 검색할 때 결과를 필터링 하는 데 사용 됩니다.
   * **커버 이미지:** 노트북을 게시할 때 표지 페이지에서 사용 되는 이미지입니다. 다음 옵션 중 하나를 선택할 수 있습니다.
   * **사용자 지정 이미지** -컴퓨터에서 이미지를 업로드할 수 있습니다. 가로 세로 비율이 256x144 인 이미지 파일을 선택 합니다.
   * **Url** -이미지가 위치한 공개적으로 액세스할 수 있는 url을 제공 합니다.
   * **스크린샷** 만들기-열려 있는 노트북의 스크린샷에 자동으로 이동 하 여 미리 보기에 업로드 합니다.
   * 표시 출력을 포함 하는 첫 번째 셀의 출력 출력 출력을 **사용** 합니다. Markdown/text만 표시 하는 셀은 표시 출력으로 계산 되지 않습니다.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="갤러리에 게시 양식을 작성 합니다.":::

   > [!NOTE]
   > **갤러리에 게시** **옵션을 사용** 하는 경우 단추를 클릭 하면 모든 **커버 이미지** 옵션이 표시 되지 않습니다. 이는 노트북이 열리지 않을 수 있으며 Azure Cosmos DB는 스크린샷을 사용 하거나 첫 번째 표시 출력에 액세스할 수 있는 권한이 없습니다.

1. 미리 보기가 적절 한지 확인 하 고 **게시** 를 선택 합니다. 이 노트북은 게시 될 때 Azure Cosmos DB 노트북 공용 갤러리에 표시 됩니다. 게시 하기 전에 중요 한 데이터 또는 출력을 제거 했는지 확인 합니다. 게시 하기 전에 Microsoft 정책 위반에 대해 노트북 콘텐츠를 검색 합니다. 이 프로세스는 일반적으로 완료 하는 데 몇 초 정도 걸립니다. 위반이 발견 되 면 알림 탭에 메시지가 표시 됩니다. 위반을 발견 하면 노트북이 게시 되지 않으며 위반 된 텍스트를 제거 하 고 다시 게시 합니다.

   > [!NOTE]
   > 전자 필기장이 공개 갤러리에 게시 된 후에는 모든 Azure Cosmos DB 사용자가 볼 수 있습니다. 액세스는 구독 또는 조직 수준에 따라 제한 되지 않습니다.

1. 갤러리에 노트북을 게시 한 후 **내 게시 된 작업** 탭에서 볼 수 있습니다. 또한 공개 갤러리에서 게시 한 노트북을 제거 하거나 삭제할 수 있습니다.

1. 또한 수행 하는 코드를 위반 하는 노트북을 보고할 수도 있습니다. 위반이 발견 되 면 Cosmos DB가 갤러리에서 노트북을 자동으로 제거 합니다. 노트북을 제거 하는 경우 사용자는 **내 게시 된 작업** 탭 아래에서 제거 된 메모를 볼 수 있습니다. 노트북을 보고 하려면 **데이터 탐색기**  >  **노트북**  >  **갤러리**  >  **공용 갤러리** 탭으로 이동 합니다. 보고할 노트북을 열고 오른쪽 모서리의 **도움말** 단추를 가리킨 다음 **신고** 하기를 선택 합니다.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="준수 코드를 위반 하는 노트북을 보고 합니다.":::

## <a name="next-steps"></a>다음 단계

* [Jupyter 노트북 Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) 의 이점에 대해 알아봅니다.
* [Python Notebook 기능 및 명령 사용](use-python-notebook-features-and-commands.md)
* [C# Notebook 기능 및 명령 사용](use-csharp-notebook-features-and-commands.md)
* [GitHub 리포지토리에서 Notebooks 가져오기](import-github-notebooks.md)
