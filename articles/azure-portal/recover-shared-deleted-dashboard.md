---
title: Azure Portal에서 삭제된 대시보드 복구
description: Azure Portal에서 게시된 대시보드를 삭제한 경우 대시보드를 복구할 수 있습니다.
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 96d330872327f8719e7cc4287415d86fd0ae5fd4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559719"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Azure Portal에서 삭제된 대시보드 복구

전체 Azure 클라우드에 있는 경우 Azure Portal에서 _게시된_ 대시보드를 삭제하면 삭제 후 14일 내에 해당 대시보드를 복구할 수 있습니다. Azure Government 클라우드에 있거나 대시보드가 게시되지 않은 경우에는 복구할 수 없으며, 다시 빌드해야 합니다. 대시보드를 게시하는 방법에 대한 자세한 내용은 [대시보드 게시](azure-portal-dashboard-share-access.md#publish-a-dashboard)를 참조하세요. 게시된 대시보드를 복구하려면 다음 단계를 따르세요.

1. Azure Portal 메뉴에서 **리소스 그룹** 을 선택한 다음 대시보드를 게시한 리소스 그룹을 선택합니다(기본적으로 **대시보드** 이름이 지정됨).

1. **활동 로그** 에서 **대시보드 삭제** 작업을 확장합니다. **변경 내용** 탭을 선택하고 **\<deleted resource\>** 를 선택합니다.

    ![변경 내용 탭의 스크린샷](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 왼쪽 창의 내용을 선택하여 복사한 다음 파일 확장명이 _.json_ 인 텍스트 파일에 저장합니다. 포털에서는 JSON 파일을 사용하여 대시보드를 다시 만듭니다.

    ![변경 내용 차이의 스크린샷](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Azure Portal 메뉴에서 **대시보드** 를 선택한 다음 **업로드** 를 선택합니다.

    ![대시보드 업로드의 스크린샷](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 저장한 JSON 파일을 선택합니다. 포털에서 삭제된 대시보드와 이름 및 요소가 동일한 대시보드를 다시 만듭니다.

1. **공유** 를 선택하여 대시보드를 게시하고 적절한 액세스 제어를 다시 설정합니다.

    ![대시보드 공유의 스크린샷](media/recover-shared-deleted-dashboard/dashboard-share.png)
