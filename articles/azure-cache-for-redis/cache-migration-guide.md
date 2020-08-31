---
title: Azure Cache for Redis로 마이그레이션
description: Redis 용 Azure Cache에 기존 캐시를 마이그레이션하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 2a95aa9e9fccdb7047c2c0901f4349fecfbab672
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009582"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Azure Cache for Redis로 마이그레이션
이 문서에서는 온-프레미스 또는 다른 클라우드 서비스에서 실행 되는 기존 Redis cache를 Redis 용 Azure Cache로 마이그레이션하는 다양 한 방법을 설명 합니다.

## <a name="migration-scenarios"></a>마이그레이션 시나리오
오픈 소스 Redis는 많은 계산 환경에서 실행할 수 있습니다. 일반적인 예제는 다음을 포함합니다.

- **온-프레미스** -Redis 캐시가 전용 데이터 센터에서 실행 되 고 있습니다.
- **클라우드 기반 vm** -Azure VM, AWS EC2 등에서 실행 되는 Redis 캐시
- **호스팅 서비스** -AWS ElastiCache와 같은 관리 되는 Redis services
- 다른 **지역** -Redis 캐시가 다른 Azure 지역에 있습니다.

이러한 캐시가 있는 경우 최소 중단 또는 가동 중지 시간으로 Redis을 위해 Azure 캐시로 이동할 수 있습니다.

## <a name="migration-options"></a>마이그레이션 옵션

한 캐시에서 다른 캐시로 전환할 수 있는 여러 가지 방법이 있습니다. 캐시의 위치와 응용 프로그램이 상호 작용 하는 방식에 따라 한 가지 방법이 다른 방법 보다 더 유용 합니다. 자주 사용 하는 마이그레이션 전략 중 일부는 아래에 자세히 설명 되어 있습니다.

   | 옵션       | 장점 | 단점 |
   | ------------ | ---------- | ------------- |
   | 새 캐시 만들기 | 구현 하는 것이 가장 간단 합니다. | 데이터를 새 캐시로 다시 채워야 하므로 많은 응용 프로그램에서 작동 하지 않을 수 있습니다. |
   | RDB 파일을 통해 데이터 내보내기 및 가져오기 | 일반적으로 모든 Redis 캐시와 호환 됩니다. | RDB 파일이 생성 된 후 기존 캐시에 기록 되 면 일부 데이터가 손실 될 수 있습니다. | 
   | 두 개의 캐시로 데이터 이중 쓰기 | 데이터 손실이 나 downtown 없습니다. 기존 캐시의 중단 없는 작업 새 캐시를 쉽게 테스트할 수 있습니다. | 오랜 시간 동안 두 개의 캐시가 필요 합니다. | 
   | 프로그래밍 방식으로 데이터 마이그레이션 | 데이터 이동 방법에 대 한 모든 권한 | 사용자 지정 코드가 필요 합니다. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Redis에 대 한 새 Azure 캐시 만들기

기술적으로이 방법은 마이그레이션하지 않습니다. 데이터 손실이 걱정 되지 않는 경우 Redis 용 Azure Cache로 이동 하는 가장 쉬운 방법은 캐시 인스턴스를 만들고 응용 프로그램을 연결 하는 것입니다. 예를 들어 Redis를 데이터베이스 레코드에 대 한 조회 캐시로 사용 하는 경우 캐시를 처음부터 쉽게 다시 작성할 수 있습니다.

이 옵션을 구현 하는 일반적인 단계는 다음과 같습니다.

1. Redis 인스턴스에 대 한 새 Azure 캐시를 만듭니다.

2. 새 인스턴스를 사용 하도록 응용 프로그램을 업데이트 합니다.

3. 이전 Redis 인스턴스를 삭제 합니다.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>데이터를 RDB 파일로 내보내고 Redis 용 Azure Cache로 가져오기

오픈 소스 Redis는 캐시의 메모리 내 데이터 집합에 대 한 스냅숏을 만들고이를 파일에 저장 하는 표준 메커니즘을 정의 합니다. RDB 라는이 파일은 다른 Redis cache에서 읽을 수 있습니다. [Redis premium 계층 용 Azure cache](cache-overview.md#service-tiers) 는 RDB 파일을 통해 캐시 인스턴스로 데이터를 가져오는 것을 지원 합니다. RDB 파일을 사용 하 여 기존 캐시에서 Redis 용 Azure 캐시로 데이터를 전송할 수 있습니다.

> [!IMPORTANT]
> RDB 파일 형식은 Redis 버전 간에 변경 될 수 있으며 이전 버전과의 호환성을 유지 하지 않을 수 있습니다. 내보내는 캐시의 Redis 버전은 Redis 용 Azure Cache에서 제공 되는 버전과 같거나 작아야 합니다.
>

이 옵션을 구현 하는 일반적인 단계는 다음과 같습니다.

1. 프리미엄 계층에서 기존 캐시와 크기가 같거나 큰 새 Azure Cache for Redis 인스턴스를 만듭니다.

2. 기존 Redis cache의 스냅숏을 저장 합니다. [Redis를 구성 하 여 스냅숏을](https://redis.io/topics/persistence) 주기적으로 저장 하거나 [save](https://redis.io/commands/save) 또는 [BGSAVE](https://redis.io/commands/bgsave) 명령을 사용 하 여 수동으로 프로세스를 실행할 수 있습니다. RDB 파일의 이름은 기본적으로 "redis" 이며,이 파일은 *redis.conf* 구성 파일에 지정 된 경로에 배치 됩니다.

    > [!NOTE]
    > Redis에 대 한 Azure 캐시 내에서 데이터를 마이그레이션하는 경우 RDB 파일을 내보내는 방법 또는 [PowerShell 내보내기 cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) 을 대신 사용 하 [는 방법에 대 한 다음 지침](cache-how-to-import-export-data.md) 을 참조 하세요.
    >

3. 새 캐시가 있는 지역의 Azure storage 계정에 RDB 파일을 복사 합니다. 이 작업에는 AzCopy를 사용할 수 있습니다.

4. 이러한 [가져오기 명령](cache-how-to-import-export-data.md) 또는 [PowerShell import cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0)을 사용 하 여 새 캐시로 RDB 파일을 가져옵니다.

5. 새 캐시 인스턴스를 사용 하도록 응용 프로그램을 업데이트 합니다.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>마이그레이션 기간 동안 동시에 두 개의 Redis 캐시에 쓰기

캐시 간에 직접 데이터를 이동 하는 대신 응용 프로그램을 사용 하 여 기존 캐시 및 설정 중인 새 캐시에 데이터를 쓸 수 있습니다. 응용 프로그램은 원래 기존 캐시에서 데이터를 계속 읽습니다. 새 캐시에 필요한 데이터가 있으면 응용 프로그램을 해당 캐시로 전환 하 고 이전 응용 프로그램을 사용 중지 합니다. 예를 들어 Redis를 세션 저장소로 사용 하 고 응용 프로그램 세션은 7 일 동안 유효 하다 고 가정해 보겠습니다. 주에 대 한 두 개의 캐시에 쓴 후에는 새 캐시에 만료 되지 않은 모든 세션 정보가 포함 됩니다. 데이터 손실에 대해 걱정 하지 않고 해당 시점부터 안전 하 게 사용할 수 있습니다.

이 옵션을 구현 하는 일반적인 단계는 다음과 같습니다.

1. 프리미엄 계층에서 기존 캐시와 크기가 같거나 큰 새 Azure Cache for Redis 인스턴스를 만듭니다.

2. 새 인스턴스와 원래 인스턴스 모두에 쓰도록 응용 프로그램 코드를 수정 합니다.

3. 새 인스턴스가 데이터로 충분히 채워질 때까지 원래 인스턴스에서 데이터를 계속 읽습니다.

4. 응용 프로그램 코드를 새 인스턴스만 읽고 쓰도록 업데이트 합니다.

5. 원본 인스턴스를 삭제 합니다.

### <a name="migrate-programmatically"></a>프로그래밍 방식으로 마이그레이션

기존 캐시에서 프로그래밍 방식으로 데이터를 읽고 Redis 용 Azure Cache에 기록 하 여 사용자 지정 마이그레이션 프로세스를 만들 수 있습니다. 이 [오픈 소스 도구](https://github.com/deepakverma/redis-copy) 를 사용 하 여 Redis 인스턴스의 한 Azure 캐시에서 다른 인스턴스로 데이터를 복사할 수 있습니다. 이 도구는 서로 다른 Azure 캐시 지역의 캐시 인스턴스 간에 데이터를 이동 하는 데 유용 합니다. [컴파일된 버전](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) 도 사용할 수 있습니다. 또한 고유한 마이그레이션 도구를 작성 하는 데 도움이 되는 소스 코드를 찾을 수 있습니다.

> [!NOTE]
> 이 도구는 Microsoft에서 공식적으로 지원 되지 않습니다. 
>

이 옵션을 구현 하는 일반적인 단계는 다음과 같습니다.

1. 기존 캐시가 있는 지역에서 VM을 만듭니다. 데이터 집합이 크면 비교적 강력한 VM을 선택 하 여 복사 시간을 줄입니다.

2. Redis 인스턴스에 대 한 새 Azure 캐시를 만듭니다.

3. 새 캐시에서 데이터를 플러시하고 비어 있는지 확인 합니다. 복사 도구 자체가 대상 캐시의 기존 키를 덮어쓰지 않기 때문에이 단계가 필요 합니다.

    > [!IMPORTANT]
    > 원본 캐시에서 플러시하지 않도록 해야 합니다.
    >

4. 위의 오픈 소스 도구와 같은 응용 프로그램을 사용 하 여 원본 캐시에서 대상으로 데이터를 복사 하는 기능을 자동화 합니다. 데이터 집합의 크기에 따라 복사 프로세스를 완료 하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

* [Redis 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)
* [데이터 가져오기](cache-how-to-import-export-data.md#import)
