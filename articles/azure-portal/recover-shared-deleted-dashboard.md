---
title: Azure 포털에서 삭제된 대시보드 복구 | 마이크로 소프트 문서
description: Azure 포털에서 게시된 대시보드를 삭제하면 대시보드를 복구할 수 있습니다.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133286"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Azure 포털에서 삭제된 대시보드 복구

공용 Azure 클라우드에 있고 Azure Portal에서 _게시된_ 대시보드를 삭제한 경우 삭제 후 14일 이내에 해당 대시보드를 복구할 수 있습니다. Azure 정부 클라우드에 있거나 대시보드가 게시되지 않은 경우 복구할 수 없으므로 다시 빌드해야 합니다. 대시보드 게시에 대한 자세한 내용은 [대시보드 게시](azure-portal-dashboard-share-access.md#publish-dashboard)를 참조하십시오. 게시된 대시보드를 복구하려면 다음 단계를 따르십시오.

1. Azure 포털 메뉴에서 **리소스 그룹을**선택한 다음 대시보드를 게시한 리소스 그룹을 **선택합니다(기본적으로 대시보드라는**이름).

1. **활동 로그에서** **대시보드 삭제** 작업을 확장합니다. 기록 **변경** 탭을 선택한 다음 ** \<삭제된\>리소스를**선택합니다.

    ![변경 기록 탭의 스크린샷](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 왼쪽 창의 내용을 선택하고 복사한 다음 _.json_ 파일 확장명이 있는 텍스트 파일에 저장합니다. 포털은 JSON 파일을 사용하여 대시보드를 다시 만듭니다.

    ![변경 기록 diff의 스크린샷](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Azure 포털 메뉴에서 **대시보드를**선택한 다음 **업로드**를 선택합니다.

    ![대시보드 업로드 스크린샷](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 저장한 JSON 파일을 선택합니다. 포털은 삭제된 대시보드와 동일한 이름과 요소를 가진 대시보드를 다시 만듭니다.

1. **공유를** 선택하여 대시보드를 게시하고 적절한 액세스 제어를 다시 설정합니다.

    ![대시보드 공유의 스크린샷](media/recover-shared-deleted-dashboard/dashboard-share.png)
