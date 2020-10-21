---
title: 데이터 지 속성 구성-Premium Azure Cache for Redis
description: 프리미엄 계층 Azure Cache for Redis 인스턴스에 대한 데이터 지속성을 구성하고 관리하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 9927d4780ea015502151188b61c50ddbd2656819
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339546"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>프리미엄 Azure Cache for Redis에 대한 데이터 지속성을 구성하는 방법
이 문서에서는 Azure Portal를 통해 프리미엄 Azure Cache for Redis 인스턴스에 지 속성을 구성 하는 방법에 대해 알아봅니다. Azure Cache for Redis에는 클러스터링, 지속성, 가상 네트워크 지원과 같은 프리미엄 계층 기능을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제안이 있습니다. 

## <a name="what-is-data-persistence"></a>데이터 지속성이란?
[Redis 지속성](https://redis.io/topics/persistence)을 사용하면 Redis에 저장된 데이터를 유지할 수 있습니다. 또한 스냅샷을 만들고, 하드웨어 오류 시 로드할 수 있게 데이터를 백업할 수 있습니다. 기본 또는 표준 계층보다 훨씬 큰 이러한 혜택은 모든 데이터가 메모리에 저장되기 때문에 가능하며 캐시 노드 다운 시 데이터 손실 가능성이 있습니다. 

Azure Cache for Redis에서 Redis 지속성을 제공하는 데 사용하는 모델은 다음과 같습니다.

* **RDB 지속성** - RDB(Redis 데이터베이스) 지속성이 구성되면 Azure Cache for Redis에서 구성 가능한 백업 빈도에 따라 Azure Cache for Redis 스냅샷을 Redis 이진 형식으로 디스크에 유지합니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 가장 최근의 스냅샷을 사용하여 캐시를 재구성합니다. RDB 지속성의 [장점](https://redis.io/topics/persistence#rdb-advantages) 및 [단점](https://redis.io/topics/persistence#rdb-disadvantages)에 대해 자세히 알아봅니다.
* **AOF 지속성** - AOF(Append only file) 지속성이 구성되면 Azure Cache for Redis에서 모든 쓰기 작업을 Azure Storage 계정에 초당 1회 이상 저장되는 로그에 저장합니다. 중대한 이벤트가 발생하여 주 및 복제본 캐시가 모두 비활성화된 경우 저장된 쓰기 작업을 사용하여 캐시를 재구성합니다. AOF 지속성의 [장점](https://redis.io/topics/persistence#aof-advantages) 및 [단점](https://redis.io/topics/persistence#aof-disadvantages)에 대해 자세히 알아봅니다.

지 속성은 사용자가 소유 하 고 관리 하는 Azure Storage 계정에 Redis 데이터를 기록 합니다. 캐시를 만드는 동안 **새 Azure cache For Redis** 블레이드에 대해 구성 하거나 기존 프리미엄 캐시의 **리소스 메뉴** 에서 구성할 수 있습니다.

> [!NOTE]
> 
> 데이터는 지속 될 때 자동으로 암호화 Azure Storage 합니다. 암호화에 고유한 키를 사용할 수 있습니다. 자세한 내용은 [Azure Key Vault를 사용 하 여 고객 관리 키](/azure/storage/common/storage-service-encryption)를 참조 하세요.
> 
> 

1. 프리미엄 캐시를 만들려면 [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **리소스 만들기**를 선택 합니다. Azure 포털에서 캐시를 만드는 것 외에도 Resource Manager 템플릿, PowerShell 또는 Azure CLI를 사용해서도 만들 수 있습니다. Azure Cache for Redis를 만드는 방법에 대한 자세한 내용은 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)를 참조하세요.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="리소스를 만듭니다.":::
   
2. **새로 만들기** 페이지에서 **데이터베이스**를 선택한 다음, **Azure Cache for Redis**를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="리소스를 만듭니다.":::

3. **새 Redis Cache** 페이지에서 새 프리미엄 캐시에 대 한 설정을 구성 합니다.
   
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1 ~ 007e; 63 자 사이의 문자열 이어야 하며 숫자, 문자 또는 하이픈만 포함 해야 합니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름*은 *\<DNS name>.redis.cache.windows.net*입니다. | 
   | **구독** | 드롭다운을 선택 하 고 구독을 선택 합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운 하 고 리소스 그룹을 선택 하거나 **새로 만들기** 를 선택 하 고 새 리소스 그룹 이름을 입력 합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **위치** | 드롭다운 및 위치를 선택 합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **캐시 유형** | 프리미엄 기능을 구성 하려면 드롭다운 및 프리미엄 캐시를 선택 합니다. 자세한 내용은 [Redis 가격에 대 한 Azure Cache](https://azure.microsoft.com/pricing/details/cache/)를 참조 하세요. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

4. **네트워킹** 탭을 선택하거나 페이지 하단에 있는 **네트워킹** 단추를 클릭합니다.

5. **네트워킹** 탭에서 연결 방법을 선택합니다. 프리미엄 캐시 인스턴스의 경우 공용 IP 주소 또는 서비스 끝점을 통해 공개적으로 또는 개인 끝점을 사용 하 여 개인적으로 연결할 수 있습니다.

6. 페이지 맨 아래에서 **다음: 고급** 탭을 선택하거나 페이지 하단에서 **다음: 고급** 단추를 클릭합니다.

7. 프리미엄 캐시 인스턴스에 대 한 **고급** 탭에서 TLS가 아닌 포트, 클러스터링 및 데이터 지 속성에 대 한 설정을 구성 합니다. 데이터 지 속성의 경우 **RDB** 또는 **aof** 지 속성 중 하나를 선택할 수 있습니다. 

8. RDB 지 속성을 사용 하도록 설정 하려면 **rdb** 를 클릭 하 고 설정을 구성 합니다. 
   
   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **백업 빈도** | 드롭다운 및 백업 간격을 선택 합니다. 여기에는 **15 분**, **30 분**, **60 분**, **6 시간**, **12 시간**, **24 시간이**포함 됩니다. | 이 간격은 이전 백업 작업이 성공적으로 완료되어 새 백업이 시작된 시점부터 계산됩니다. | 
   | **Storage 계정** | 드롭다운 하 고 저장소 계정을 선택 합니다. | 캐시와 동일한 지역 및 구독에서 저장소 계정을 선택 해야 하며, Premium storage의 처리량이 높기 때문에 **Premium Storage** 계정을 권장 합니다.  | 
   | **스토리지 키** | 드롭다운 및 사용할 **기본 키** 또는 **보조 키** 중 하나를 선택 합니다. | 지속성 계정에 대한 스토리지 키가 다시 생성된 경우에는 **스토리지 키** 드롭다운에서 원하는 키를 다시 구성해야 합니다. | 

    첫 번째 백업은 백업 빈도 간격이 경과 되 면 시작 됩니다.

9. AOF 지 속성을 사용 하려면 **Aof** 를 클릭 하 고 설정을 구성 합니다. 
   
   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **첫 번째 저장소 계정** | 드롭다운 하 고 저장소 계정을 선택 합니다. | 이 저장소 계정은 캐시와 동일한 지역 및 구독에 있어야 하며 Premium storage의 처리량이 높기 때문에 **Premium Storage** 계정을 권장 합니다. | 
   | **첫 번째 저장소 키** | 드롭다운 및 사용할 **기본 키** 또는 **보조 키** 중 하나를 선택 합니다. | 지속성 계정에 대한 스토리지 키가 다시 생성된 경우에는 **스토리지 키** 드롭다운에서 원하는 키를 다시 구성해야 합니다. | 
   | **두 번째 저장소 계정** | 필드 드롭다운 및 보조 저장소 계정을 선택 합니다. | 필요에 따라 추가 저장소 계정을 구성할 수 있습니다. 두 번째 스토리지 계정이 구성된 경우 복제본 캐시에 대한 쓰기는 이 두 번째 스토리지 계정에 기록됩니다. | 
   | **두 번째 저장소 키** | 필드 드롭다운 및 사용할 **기본 키** 또는 **보조 키** 중 하나를 선택 합니다. | 지속성 계정에 대한 스토리지 키가 다시 생성된 경우에는 **스토리지 키** 드롭다운에서 원하는 키를 다시 구성해야 합니다. | 

    AOF 지속성을 사용하도록 설정하면 캐시에 대한 쓰기 작업이 지정된 스토리지 계정(두 번째 스토리지 계정을 구성한 경우 해당 계정도 포함)에 저장됩니다. 주 캐시와 복제본 캐시 둘 다에서 심각한 오류가 발생하면 저장된 AOF 로그가 캐시를 다시 작성하는 데 사용됩니다.

10. 페이지 맨 아래에서 **다음: 태그** 탭을 선택하거나 페이지 하단에서 **다음: 태그** 단추를 클릭합니다.

11. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다. 

12. **검토 + 만들기**를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

13. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기**를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태**가 **실행 중**으로 표시되면 캐시를 사용할 준비가 된 것입니다. 

## <a name="persistence-faq"></a>지속성 FAQ
Azure Cache for Redis 지속성에 대해 자주 묻는 질문과 대답이 나와 있는 목록은 다음과 같습니다.

* [이전에 만든 캐시에서 지속성을 사용할 수 있나요?](#can-i-enable-persistence-on-a-previously-created-cache)
* [AOF 및 RDB 지속성을 동시에 사용할 수 있나요?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [어떤 지속성 모델을 선택해야 하나요?](#which-persistence-model-should-i-choose)
* [다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [서로 다른 두 캐시에서 지 속성에 동일한 저장소 계정을 사용할 수 있나요?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)


### <a name="rdb-persistence"></a>RDB 지속성
* [캐시를 만든 후 RDB 백업 주기를 변경할 수 있나요?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [RDB 백업 주기가 60분인데 왜 백업 사이 간격이 60분 이상이 되나요?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [새 백업을 만들면 이전 RDB 백업은 어떻게 되나요?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 지속성
* [두 번째 스토리지 계정은 언제 사용해야 하나요?](#when-should-i-use-a-second-storage-account)
* [AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [두 번째 스토리지 계정은 어떻게 제거할 수 있나요?](#how-can-i-remove-the-second-storage-account)
* [다시 쓰기란 무엇이며 내 캐시에 어떤 영향을 미치나요?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [AOF가 설정된 캐시의 크기를 조정하는 경우 어떻게 되나요?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [스토리지에서 AOF 데이터는 어떤 방식으로 구성되나요?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>이전에 만든 캐시에서 지속성을 사용할 수 있나요?
예, Redis 지속성은 캐시를 만들 때와 기존 프리미엄 캐시에서 모두 구성할 수 있습니다.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>AOF 및 RDB 지속성을 동시에 사용할 수 있나요?

아니요. RDB 또는 AOF 중 하나만 사용하도록 설정할 수 있습니다.

### <a name="which-persistence-model-should-i-choose"></a>어떤 지속성 모델을 선택해야 하나요?

AOF 지속성은 모든 쓰기를 로그에 저장하므로, 구성된 백업 간격을 기준으로 백업을 저장하여 성능에 최소 영향을 미치는 RDB 지속성과 비교할 때 처리량에 영향을 미칩니다. 기본 목표가 데이터 손실을 최소화하는 것이며 캐시에 대한 처리량 감소를 처리할 수 있는 경우에는 AOF 지속성을 선택합니다. 캐시에 대해 최적의 처리량을 유지하려고 하면서도 데이터 복구 메커니즘을 계속 유지하려는 경우에는 RDB 지속성을 선택합니다.

* RDB 지속성의 [장점](https://redis.io/topics/persistence#rdb-advantages) 및 [단점](https://redis.io/topics/persistence#rdb-disadvantages)에 대해 자세히 알아봅니다.
* AOF 지속성의 [장점](https://redis.io/topics/persistence#aof-advantages) 및 [단점](https://redis.io/topics/persistence#aof-disadvantages)에 대해 자세히 알아봅니다.

AOF 지속성을 사용하는 경우 성능에 대한 자세한 내용은 [AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)를 참조하세요.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?

RDB 및 AOF 지속성:

* 더 큰 크기로 조정했다면 영향은 없습니다.
* 더 작은 크기를 조정했고 새 크기에 대한 [데이터베이스 제한](cache-configure.md#databases)보다 사용자 지정 [데이터베이스](cache-configure.md#databases) 설정이 더 크다면, 그러한 데이터베이스에 저장된 데이터는 복원되지 않습니다. 자세한 내용은 [사용자 지정 데이터베이스 설정이 크기 조정 동안 영향을 받나요?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* 더 작은 크기로 조정 했고 마지막 백업의 모든 데이터를 저장하기에 충분한 공간이 더 작은 크기에 없는 경우, 일반적으로 [allkeys-lru](https://redis.io/topics/lru-cache) 제거 정책을 사용하여 복원 프로세스 중에 키가 제거됩니다.

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>서로 다른 두 캐시에서 지 속성에 동일한 저장소 계정을 사용할 수 있나요?
예, 서로 다른 두 캐시에서 지 속성에 동일한 저장소 계정을 사용할 수 있습니다.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>캐시를 만든 후 RDB 백업 주기를 변경할 수 있나요?
예, **데이터 지 속성** 블레이드에서 RDB 지 속성의 백업 빈도를 변경할 수 있습니다. 자세한 내용은 Redis 지속성 구성을 참조하세요.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>RDB 백업 주기가 60분인데 왜 백업 사이 간격이 60분 이상이 되나요?
RDB 지속성 백업 간격의 주기는 이전 백업 프로세스가 성공적으로 완료되어야 시작됩니다. 백업 간격이 60분이고 백업 프로세스를 성공적으로 완료하는 데 15분이 걸린다면 다음 백업은 이전 백업 시작 시점에서 75분까지 시작되지 않습니다.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>새 백업을 만들면 이전 RDB 백업은 어떻게 되나요?
가장 최근 백업을 제외한 모든 RDB 지속성 백업은 자동으로 삭제됩니다. 즉시 삭제되지 않을 수 있으나 오래된 백업을 무한정 유지하지는 않습니다.


### <a name="when-should-i-use-a-second-storage-account"></a>두 번째 스토리지 계정은 언제 사용해야 하나요?

AOF 지속성이 캐시에 대해 예상된 설정 작업보다 더 높다고 판단되면 AOF 지속성에 대해 두 번째 스토리지 계정을 사용해야 합니다.  보조 스토리지 계정을 설정하면 캐시가 스토리지 대역폭 제한에 도달하지 않도록 하는 데 도움이 됩니다.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 지속성이 내 캐시의 처리량, 대기 시간 또는 성능에 영향을 미치나요?

AOF 지속성은 캐시가 최대 부하보다 낮을 때(CPU 및 서버 부하 둘 다에서 90% 미만) 처리량에 15%-20% 정도 영향을 미칩니다. 캐시가 이러한 한도 내에 있을 때 대기 시간 문제는 없습니다. 그러나 캐시는 AOF가 사용하도록 설정되면 더 빨리 이러한 한도에 도달합니다.

### <a name="how-can-i-remove-the-second-storage-account"></a>두 번째 스토리지 계정은 어떻게 제거할 수 있나요?

두 번째 스토리지 계정을 첫 번째 스토리지 계정과 동일하게 설정하여 AOF 지속성 보조 스토리지 계정을 제거할 수 있습니다. 기존 캐시의 경우 캐시의 **리소스 메뉴** 에서 **데이터 지 속성** 블레이드에 액세스 합니다. AOF 지 속성을 사용 하지 않도록 설정 하려면 **사용 안 함**을 클릭 합니다.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>다시 쓰기란 무엇이며 내 캐시에 어떤 영향을 미치나요?

AOF 파일이 충분히 커지면 다시 쓰기가 자동으로 큐에 대기됩니다. 다시 쓰기를 수행하면 현재 데이터 집합을 만드는 데 필요한 작업의 최소 집합을 사용하여 AOF 파일 크기가 다시 조정됩니다. 다시 쓰기 동안, 성능 제한에 더 빠르게 도달하며 큰 데이터 세트를 처리할 때 특히 더 그렇습니다. AOF 파일이 더 커지면 다시 쓰기는 덜 자주 발생하지만 일단 발생하면 많은 시간이 소요됩니다.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF가 설정된 캐시의 크기를 조정하는 경우 어떻게 되나요?

크기 조정이 완료된 후에 파일이 다시 로드되므로 크기 조정 시 AOF 파일이 아주 커질 경우 크기 조정 작업이 예상한 것보다 더 오래 걸립니다.

크기 조정에 대한 자세한 내용은 [다른 크기로 확장했고 크기 조정 작업 전에 만들어진 백업을 복원할 경우 어떻게 됩니까?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)를 참조하세요.

### <a name="how-is-my-aof-data-organized-in-storage"></a>스토리지에서 AOF 데이터는 어떤 방식으로 구성되나요?

AOF 파일에 저장된 데이터는 스토리지에 데이터를 저장하는 성능을 향상시키기 위해 노드당 여러 페이지 Blob으로 구분됩니다. 다음 표에는 각 가격 책정 계층에 사용되는 페이지 Blob 수가 표시됩니다.

| 프리미엄 계층 | Blob |
|--------------|-------|
| P1           | 분할당 4    |
| P2           | 분할당 8    |
| P3           | 분할당 16   |
| P4           | 분할당 20   |

클러스터링을 사용하도록 설정하면 캐시의 각 분할은 이전 표에 표시된 것처럼 자체 페이지 Blob 집합을 갖습니다. 예를 들어 분할이 3개 있는 P2 캐시는 24개 페이지 Blob에 해당 AOF 파일을 분산합니다(분할당 8 Blob, 3개 분할).

다시 쓰기 후 2개의 AOF 파일 집합이 스토리지에 존재합니다. 다시 쓰기는 백그라운드에서 발생하며 첫 번째 파일 집합에 추가되지만, 다시 쓰기 동안 캐시로 전송된 설정 작업은 두 번째 집합에 추가됩니다. 오류가 발생한 경우 백업은 다시 쓰기 동안 일시적으로 저장되지만 다시 쓰기가 끝난 후에 즉시 삭제됩니다.


## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

* [Redis Premium 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
