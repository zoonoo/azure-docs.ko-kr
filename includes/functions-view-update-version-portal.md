---
title: 파일 포함
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83343357"
---
현재 함수 앱에서 사용하는 런타임 버전을 보고 업데이트하려면 다음 절차를 따르세요.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동합니다.

1. **설정**아래에서 **구성**을 선택 합니다. **함수 런타임 설정** 탭에서 **런타임 버전**을 찾습니다. 특정 런타임 버전을 확인 합니다. 아래 예제에서 주 버전은 `~3`로 설정됩니다.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="런타임 버전을 봅니다." border="true":::

1. 함수 앱을 버전 1.x 런타임으로 고정하려면 **런타임 버전** 아래에서 **~1**을 선택합니다. 앱에 함수가 있으면 이 스위치를 사용할 수 없습니다.

1. 런타임 버전을 변경한 경우 **개요** 탭으로 돌아와서 **다시 시작**을 선택해 앱을 다시 시작합니다.  버전 1.x 런타임에서 함수 앱 실행이 다시 시작되며 함수를 만들 때는 버전 1.x 템플릿이 사용됩니다.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="함수 앱을 다시 시작 합니다." border="true":::
