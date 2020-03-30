---
title: 포함 파일
description: 포함 파일
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424175"
---
> [!NOTE]
> 웹앱이 20분 동안 작동하지 않으면 시간이 초과될 수 있습니다. 실제 웹앱에 대한 요청만 타이머를 다시 설정합니다. Azure 포털에서 앱의 구성을 보거나 고급 도구 사이트()`https://<app_name>.scm.azurewebsites.net`에 대한 요청을 하는 것은 타이머를 재설정하지 않습니다. 앱이 연속 또는 예약된(타이머 트리거) WebJobs를 실행하는 경우 **Always On을** 사용하여 WebJobs가 안정적으로 실행되도록 합니다. 이 기능은 기본, 표준 및 프리미엄 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)에서만 사용할 수 있습니다.
