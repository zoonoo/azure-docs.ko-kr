---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027066"
---
CRON 식과 함께 사용하는 기본 표준 시간대는 UTC(협정 세계시)입니다. 다른 표준 시간대를 기반으로 하는 CRON 식을 사용하려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 앱 설정을 만듭니다. 

이 설정의 값은 함수 앱이 실행되는 운영 체제 및 플랜에 따라 다릅니다.

|운영 체제 |계획 |값 |
|-|-|-|
| **Windows** |모두 | Windows 명령 `tzutil.exe /L`에서 제공하는 각 쌍의 두 번째 줄에 지정된 원하는 표준 시간대의 이름으로 값을 설정합니다. |
| **Linux** |Premium<br/>전용 |[tz database](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)에 나온 것과 같이 원하는 표준 시간대의 이름으로 값을 설정합니다. |

> [!NOTE]
> `WEBSITE_TIME_ZONE`는 Linux 사용 플랜에서 현재 지원되지 않습니다.

예를 들어 미국 동부 표준시(`Eastern Standard Time`(Windows) 또는 `America/New_York`(Linux)로 표시)는 현재 표준 시간 동안 UTC-05:00를 사용하고 일광 절약 시간 동안에는 UTC-04:00를 사용합니다. 매일 오전 10시(미국 동부 표준시)에 타이머 트리거를 두려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 앱 설정을 만들고, 값을 `Eastern Standard Time`(Windows) 또는 `America/New_York`(Linux)로 설정하고, 다음 NCRONTAB 식을 사용합니다. 

```
"0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE`을 사용하는 경우 일광 절약 시간 및 표준 시간의 변경을 포함하여 특정 시간대의 시간 변경에 따라 조정됩니다.
