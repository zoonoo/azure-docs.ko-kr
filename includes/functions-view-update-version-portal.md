---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182852"
---
현재 함수 앱에서 사용하는 런타임 버전을 보고 업데이트하려면 다음 절차를 따르세요.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동합니다.

1. **구성된 기능**에서 **함수 앱 설정**을 선택합니다.

    ![함수 앱 설정을 선택합니다.](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. **함수 앱 설정** 탭에서 **런타임 버전**을 찾습니다. 특정 런타임 버전 및 요청된 주 버전을 적어둡니다. 아래 예제에서 주 버전은 `~2`로 설정됩니다.

   ![함수 앱 설정을 선택합니다.](./media/functions-view-update-version-portal/function-app-view-version.png)

1. 함수 앱을 버전 1.x 런타임으로 고정하려면 **런타임 버전** 아래에서 **~1**을 선택합니다. 앱에 함수가 있으면 이 스위치를 사용할 수 없습니다.

1. 런타임 버전을 변경한 경우 **개요** 탭으로 돌아와서 **다시 시작**을 선택해 앱을 다시 시작합니다.  버전 1.x 런타임에서 함수 앱 실행이 다시 시작되며 함수를 만들 때는 버전 1.x 템플릿이 사용됩니다.