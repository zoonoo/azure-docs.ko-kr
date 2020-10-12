---
title: Azure Cache for Redis 계획 Faq
description: Redis 용 Azure Cache를 계획 하는 데 도움이 되는 일반적인 질문에 대 한 답변을 알아보세요.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 13ba529dd3067ae16167f0d9c14c8f72b982f52c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010842"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Azure Cache for Redis 계획 Faq

이 문서에서는 Azure Cache for Redis를 계획 하는 방법에 대 한 일반적인 질문에 대 한 답변을 제공 합니다.

## <a name="common-questions-and-answers"></a>일반적인 질문 및 답변
이 섹션에서는 다음 Faq를 다룹니다.

* [Azure Cache for Redis 성능](#azure-cache-for-redis-performance)
* [어떤 영역에 내 캐시를 배치해야 하나요?](#in-what-region-should-i-locate-my-cache)
* [내 캐시 된 데이터는 어디에 있습니까?](#where-do-my-cached-data-reside)
* [Azure Cache for Redis에 대한 요금은 어떻게 청구되나요?](#how-am-i-billed-for-azure-cache-for-redis)
* [Azure Government 클라우드, Azure 중국 21Vianet 클라우드 또는 Microsoft Azure 독일와 함께 Redis 용 Azure Cache를 사용할 수 있나요?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure Cache for Redis 성능
다음 표에는 Azure Cache for Redis 엔드포인트에 대해 Iaas VM에서 `redis-benchmark.exe`를 사용하여 다양한 크기의 표준 및 프리미엄 캐시를 테스트하는 동안 관찰되는 최대 대역폭 값이 나와 있습니다. TLS 처리량의 경우 redis-benchmark를 stunnel과 함께 사용하여 Azure Cache for Redis 엔드포인트에 연결합니다.

>[!NOTE] 
>이러한 값은 보장되지 않으며 해당 수치에 대한 SLA는 없지만 일반적인 수치입니다. 애플리케이션에 적합한 캐시 크기를 확인하려면 사용자 고유의 애플리케이션을 부하 테스트해야 합니다.
>이러한 수치는 새 결과가 주기적으로 게시됨에 따라 변경될 수 있습니다.
>

이 테이블에서 다음과 같은 결론을 내릴 수 있습니다.

* 동일한 크기의 캐시 처리량은 표준 계층과 비교할 때 프리미엄 계층에서 더 높습니다. 예를 들어 6GB 캐시를 사용할 경우 C3의 처리량은 100,000RPS(초당 요청 수)인 데 비해 P1의 처리량은 180,000RPS입니다.
* Redis 클러스터를 사용하여 클러스터에서 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 예를 들어 10개의 분할된 데이터베이스에 P4 클러스터를 만드는 경우 가능한 처리량은 400,000 * 10 = 4백만 RPS입니다.
* 큰 크기의 키에 대한 처리량이 표준 계층에 비해 프리미엄 계층에서 더 높습니다.

| 가격 책정 계층 | 크기 | CPU 코어 | 사용 가능한 대역폭 | 1KB 값 크기 | 1KB 값 크기 |
| --- | --- | --- | --- | --- | --- |
| **표준 캐시 크기** | | |**Mb/s(초당 메가비트) / MB/s(초당 메가바이트)** |**RPS(초당 요청 수) 비 SSL** |**RPS(초당 요청 수) SSL** |
| C0 | 250MB | 공유됨 | 100/12.5  |  15,000 |   7,500 |
| C1 |   1 GB | 1      | 500/62.5  |  38,000 |  20,720 |
| C2 | 2.5GB | 2      | 500/62.5  |  41,000 |  37,000 |
| C3 |   6GB | 4      | 1000/125  | 100,000 |  90,000 |
| C4 |  13GB | 2      | 500/62.5  |  60,000 |  55,000 |
| C5 |  26GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **프리미엄 캐시 크기** | |**분할 영역당 CPU 코어** | **Mb/s(초당 메가비트) / MB/s(초당 메가바이트)** |**RPS(초당 요청 수) 비 SSL, 분할당** |**RPS(초당 요청 수) SSL, 분할당** |
| P1 |   6GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

Stunnel를 설정 하거나 Redis 도구를 다운로드 하는 방법에 대 한 지침은 `redis-benchmark.exe` [Redis commands를 실행 하는 방법](cache-development-faq.md#how-can-i-run-redis-commands)을 참조 하세요.

### <a name="in-what-region-should-i-locate-my-cache"></a>어떤 영역에 내 캐시를 배치해야 하나요?
성능을 최적화하고 대기 시간을 최소화하려면 Azure Cache for Redis를 캐시 클라이언트 애플리케이션과 동일한 지역에 배치합니다.

### <a name="where-do-my-cached-data-reside"></a>내 캐시 된 데이터는 어디에 있습니까?
Redis 용 Azure Cache는 캐시를 호스팅하는 계층에 따라 VM 또는 vm의 RAM에 응용 프로그램 데이터를 저장 합니다. 사용자의 데이터는 기본적으로 선택한 Azure 지역에만 저장 됩니다. 데이터가 영역을 벗어날 수 있는 두 가지 경우는 다음과 같습니다.
* 캐시에서 지 속성을 사용 하도록 설정 하는 경우 Redis에 대 한 Azure Cache는 사용자가 소유한 Azure Storage 계정으로 데이터를 백업 합니다. 제공 하는 저장소 계정이 다른 지역에 있는 경우 데이터 복사본이 여기에 표시 됩니다.
* 지역에서 복제를 설정 하 고 보조 캐시가 다른 지역에 있는 경우 (일반적으로 해당 하는 경우) 데이터가 해당 지역에 복제 됩니다.

이러한 기능을 사용 하려면 Redis에 대 한 Azure 캐시를 명시적으로 구성 해야 합니다. 저장소 계정 또는 보조 캐시가 있는 지역을 완전히 제어할 수도 있습니다.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 요금은 어떻게 청구되나요?
Azure Cache for Redis 가격은 [여기](https://azure.microsoft.com/pricing/details/cache/)서 책정합니다. 가격 책정 페이지에는 시간별 및 월별 요금이 표시 됩니다. 캐시는 캐시가 만들어지는 시간부터 삭제되는 시간까지 분 단위로 요금이 청구됩니다. 캐시 요금 청구를 중지 또는 일시 중지하는 옵션은 없습니다.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Azure Government 클라우드, Azure 중국 21Vianet 클라우드 또는 Microsoft Azure 독일와 함께 Redis 용 Azure Cache를 사용할 수 있나요?
예, Azure Cache for Redis는 Azure Government 클라우드, Azure 중국 21Vianet 클라우드 및 Microsoft Azure 독일에서 사용할 수 있습니다. Azure 퍼블릭 클라우드와 비교할 때 이러한 클라우드에서 Azure Cache for Redis에 액세스하고 관리하기 위한 URL은 다릅니다.

| 클라우드   | Redis에 대한 Dns 접미사            |
|---------|---------------------------------|
| 공용  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| 독일 | *.redis.cache.cloudapi.de       |
| 중국   | *.redis.cache.chinacloudapi.cn  |

다른 클라우드에서 Azure Cache for Redis를 사용할 때 고려해야 할 사항에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure Government 데이터베이스 - Azure Cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure 중국 21Vianet 클라우드 - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Azure Government 클라우드, Azure 중국 21Vianet 클라우드 및 Microsoft Azure 독일에서 PowerShell을 통해 Azure Cache for Redis를 사용하는 방법에 대한 자세한 내용은 [다른 클라우드에 연결하는 방법 - Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Redis faq 용 다른 Azure Cache](cache-faq.md)에 대해 알아봅니다.
