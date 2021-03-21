---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027066"
---
CRON 식과 함께 사용하는 기본 표준 시간대는 UTC(협정 세계시)입니다. 다른 표준 시간대를 기반으로 하는 CRON 식을 사용하려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 앱 설정을 만듭니다. 

이 설정의 값은 함수 앱이 실행 되는 운영 체제 및 계획에 따라 다릅니다.

|운영 체제 |계획 |값 |
|-|-|-|
| **Windows** |모두 | Windows 명령에서 제공 하는 각 쌍의 두 번째 줄에 지정 된 대로 값을 원하는 표준 시간대의 이름으로 설정 합니다. `tzutil.exe /L` |
| **Linux** |Premium<br/>전용 |[Tz 데이터베이스](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)에 표시 된 대로 값을 원하는 표준 시간대의 이름으로 설정 합니다. |

> [!NOTE]
> `WEBSITE_TIME_ZONE` 는 Linux 소비 계획에서 현재 지원 되지 않습니다.

예를 들어 미국 동부 표준시 ( `Eastern Standard Time` (Windows) 또는 `America/New_York` (Linux)로 표시)는 현재 표준 시간 동안 utc-05:00을 사용 하 고 일광 절약 시간 동안에는 utc-04:00을 사용 합니다. 매일 오전 10:00 시에 타이머 트리거를 발생 시키려면 이라는 함수 앱에 대 한 앱 설정을 만들고 `WEBSITE_TIME_ZONE` , 값을 `Eastern Standard Time` (Windows) 또는 `America/New_York` (Linux)로 설정한 후 다음 NCRONTAB 식을 사용 합니다. 

```
"0 0 10 * * *"
``` 

를 사용 하는 경우 `WEBSITE_TIME_ZONE` 일광 절약 시간을 비롯 하 여 특정 표준 시간대의 시간 변경에 대해 시간을 조정 하 고 표준 시간을 변경 합니다.
