---
title: WebJobs를 사용 하 여 백그라운드 작업 실행
description: WebJobs를 사용 하 여 Azure App Service에서 백그라운드 작업을 실행 하는 방법에 대해 알아봅니다. 다양 한 스크립트 형식 중에서 선택 하 고 CRON 식을 사용 하 여 실행 합니다.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452801"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service에서 WebJobs를 사용 하 여 백그라운드 작업 실행

Azure에서 [백그라운드 작업](./webjobs-create-ieux-conceptual.md) 을 실행 하는 개념은 Azure 앱 서비스 웹 작업과 함께 제공 됩니다. 배포 방법 알아보기 <abbr title="웹 앱, API 앱 또는 모바일 앱과 동일한 인스턴스에 있는 프로그램 또는 스크립트입니다.">WebJobs</abbr> [Azure Portal](https://portal.azure.com) 를 사용 하 여 실행 파일 또는 스크립트를 업로드 합니다. 

지원 되는 세 가지 WebJobs는 다음과 같습니다.

* **연속**: 일반적으로 무한 루프에서 실행 되는 즉시 시작 합니다.
* **예약** 됨: 예약 된 트리거에서 시작 합니다.
* **수동**: 수동 트리거에서 시작 합니다.

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> 다음 단계

* [백그라운드 작업에 대 한 자세한 내용은 webjobs를 확인 하세요.](./webjobs-create-ieux-conceptual.md)
* [WebJobs의 로그 기록 보기](./webjobs-create-ieux-view-log.md)

* [WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 를 사용 하 여 많은 프로그래밍 작업 간소화

* [Visual Studio를 사용 하 여 WebJobs를 개발 하 고 배포 하는](webjobs-dotnet-deploy-vs.md) 방법을 알아봅니다.
