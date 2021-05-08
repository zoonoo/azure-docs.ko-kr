---
title: WebJobs로 백그라운드 작업 실행
description: WebJobs를 사용하여 Azure App Service에서 백그라운드 작업을 실행하는 방법에 대해 알아봅니다. 다양한 스크립트 형식 중에서 선택하고 CRON 식을 사용하여 실행합니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452801"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service에서 WebJobs로 백그라운드 작업 실행

Azure에서 [백그라운드 작업](./webjobs-create-ieux-conceptual.md)을 실행하는 개념은 Azure App Service 웹 작업과 함께 제공됩니다. 배포하는 방법 알아보기 <abbr title="웹앱, API 앱 또는 모바일 앱과 동일한 인스턴스에 있는 프로그램 또는 스크립트입니다.">WebJobs</abbr> [Azure Portal](https://portal.azure.com)을 사용하여 실행 파일 또는 스크립트를 업로드합니다. 

지원되는 세 가지 WebJobs는 다음과 같습니다.

* **연속**: 즉시 시작되며, 일반적으로 무한 루프에서 실행됩니다.
* **예약됨**: 예약된 트리거에서 시작됩니다.
* **수동**: 수동 트리거에서 시작됩니다.

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

* [WebJobs로서의 백그라운드 작업에 대해 자세히 알아보기](./webjobs-create-ieux-conceptual.md)
* [WebJobs의 로그 기록 보기](./webjobs-create-ieux-view-log.md)

* [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)를 사용하여 많은 프로그래밍 작업 간소화

* [Visual Studio를 사용하여 WebJobs를 개발하고 배포하는 방법](webjobs-dotnet-deploy-vs.md) 알아보기
