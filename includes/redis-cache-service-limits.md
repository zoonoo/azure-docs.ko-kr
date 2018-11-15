---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572420"
---
| 리소스 | 제한 |
| --- | --- |
| 캐시 크기 |530GB |
| 데이터베이스 |64 |
| 연결된 최대 클라이언트 수 |40,000 |
| Redis 캐시 복제본(고가용성) |1 |
| 클러스터링을 사용하여 프리미엄 캐시 분할 |10 |

Azure Redis Cache 제한 및 크기는 각 가격 책정 계층에 따라 다릅니다. 가격 책정 계층 및 해당 크기를 확인하려면 [Azure Redis Cache 가격 책정](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.

Azure Redis Cache 구성 제한에 대한 정보는 [기본 Redis 서버 구성](../articles/redis-cache/cache-configure.md#default-redis-server-configuration)을 참조하세요.

Azure Redis Cache 인스턴스의 구성 및 관리는 Microsoft에서 수행하므로 Azure Redis Cache에서 모든 Redis 명령이 지원되지는 않습니다. 자세한 내용은 [Azure Redis Cache에서 지원되지 않는 Redis 명령](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)을 참조하세요.

