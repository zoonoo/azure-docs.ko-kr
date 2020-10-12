---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569284"
---
CRON 식과 함께 사용하는 기본 표준 시간대는 UTC(협정 세계시)입니다. 다른 표준 시간대를 기반으로 하는 CRON 식을 사용하려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 앱 설정을 만듭니다. 

이 설정의 값은 함수 앱이 실행 되는 운영 체제 및 계획에 따라 다릅니다.

|운영 체제 |계획 |값 |
|-|-|-|
| **Windows** |모두 | [Microsoft 표준 시간대 색인](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10))에 나온 대로 값을 원하는 표준 시간대의 이름으로 설정합니다. |
| **Linux** |Premium<br/>전용 |[Tz 데이터베이스](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)에 표시 된 대로 값을 원하는 표준 시간대의 이름으로 설정 합니다. |

> [!NOTE]
> `WEBSITE_TIME_ZONE` 는 Linux 소비 계획에서 현재 지원 되지 않습니다.

예를 들어 *동부 표준시* (Windows) 또는 *아메리카/New_York* (Linux)는 UTC-05:00입니다. 매일 오전 10:00 시에 타이머 트리거가 발생 하도록 하려면 UTC 표준 시간대에 대 한 계정을 나타내는 다음 NCRONTAB 식을 사용 합니다.

```
"0 0 15 * * *"
``` 

또는 이라는 함수 앱에 대 한 앱 설정을 만들고 `WEBSITE_TIME_ZONE` , 값을 `Eastern Standard Time` (Windows) 또는 `America/New_York` (Linux)로 설정한 후 다음 NCRONTAB 식을 사용 합니다. 

```
"0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE`을 사용하는 경우 일광 절약 시간제와 같은 특정 표준 시간대에서 시간 변경에 대해 시간이 조정됩니다. 
