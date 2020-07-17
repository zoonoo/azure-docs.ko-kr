---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "68286262"
---
.NET 애플리케이션은 **StackExchange.Redis** 캐시 클라이언트를 사용할 수 있습니다. 이는 캐시 클라이언트 애플리케이션의 구성을 단순화하는 NuGet 패키지를 사용하여 Visual Studio에서 구성할 수 있습니다. 

> [!NOTE]
> 자세한 내용은 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) GitHub 페이지 및 [StackExchange.Azure Cache for Redis 클라이언트 설명서](https://github.com/StackExchange/StackExchange.Redis#documentation)를 참조하세요.
>
>

StackExchange.Redis NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **솔루션 탐색기**에서 클라이언트 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 

![NuGet 패키지 관리](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

검색 텍스트 상자에 **StackExchange.Redis** 또는 **StackExchange.Redis.StrongName**을 입력하고 결과에서 원하는 버전을 선택한 후 **설치**를 클릭합니다.

> [!NOTE]
> 강력한 이름 버전의 **StackExchange.Redis** 클라이언트 라이브러리를 사용하려면 **StackExchange.Redis.StrongName**을 선택하고 그렇지 않으면 **StackExchange.Redis**를 선택합니다.
>
>

![StackExchange.Redis NuGet 패키지](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet 패키지는 클라이언트 애플리케이션이 StackExchange.Azure Cache for Redis 클라이언트를 사용하여 Azure Cache for Redis에 액세스하는 데 필요한 어셈블리 참조를 다운로드하고 추가합니다.

> [!NOTE]
> 이전에 StackExchange.Redis를 사용하도록 프로젝트를 구성한 경우 **NuGet 패키지 관리자**에서 패키지에 대한 업데이트가 있는지 확인할 수 있습니다. 업데이트된 버전의 StackExchange.Redis NuGet 패키지가 있는지 확인하고 설치하려면 **NuGet 패키지 관리자** 창에서 **업데이트**를 클릭합니다. StackExchange.Redis NuGet 패키지에 대한 업데이트를 사용할 수 있는 경우 업데이트된 버전을 사용하도록 프로젝트를 업데이트할 수 있습니다.
>
>

**도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 클릭하고 **패키지 관리자 콘솔** 창에서 다음 명령을 실행하여 StackExchange.Redis NuGet 패키지를 설치할 수도 있습니다.

```
Install-Package StackExchange.Redis
```
