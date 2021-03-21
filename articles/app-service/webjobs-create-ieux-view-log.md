---
title: WebJobs의 로그 기록 보기
description: 실패 한 작업과 성공한 작업에 대 한 로그 기록을 봅니다.
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745676"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Azure Portal에서 WebJob 기록 보기

실패 한 작업과 성공한 작업에 대 한 로그 기록을 봅니다.

1. 기록을 확인하려는 WebJob을 선택하고 **로그** 단추를 선택합니다.

    ![로그 단추](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. **WebJob 세부 정보** 페이지에서 하나를 실행하는 세부 내용을 확인하려면 시간을 선택합니다.

    ![WebJob 세부 정보](./media/web-sites-create-web-jobs/webjobdetails.png)

1. **WebJob 실행 세부 정보** 페이지에서 **토글 출력** 을 선택하여 로그 내용의 텍스트를 확인합니다.

    ![WebJob 실행 세부 작업](./media/web-sites-create-web-jobs/webjobrundetails.png)

    별도의 브라우저 창에서 출력 텍스트를 보려면 **다운로드** 를 선택합니다. 텍스트 자체를 다운로드하려면 **다운로드** 를 마우스 오른쪽 단추로 클릭하고 브라우저 옵션을 사용하여 파일 내용을 저장합니다.

1. 페이지 맨 위에 있는 **WebJobs** 이동 경로 탐색 링크를 선택하여 WebJobs의 목록으로 이동합니다.

    ![WebJob 이동 경로 탐색](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![기록 대시보드의 작업 목록](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>다음 단계

* [WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 를 사용 하 여 많은 프로그래밍 작업 간소화

* [Visual Studio를 사용 하 여 WebJobs를 개발 하 고 배포 하는](webjobs-dotnet-deploy-vs.md) 방법을 알아봅니다.