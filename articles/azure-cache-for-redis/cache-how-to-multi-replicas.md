---
title: Redis 용 Azure Cache에 복제본 추가 (미리 보기)
description: Redis 인스턴스에 대 한 프리미엄 계층 Azure Cache에 복제본을 추가 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a747cf8e1713eb905aee02af95c568a448b47f05
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347027"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Redis 용 Azure Cache에 복제본 추가 (미리 보기)
이 문서에서는 Azure Portal를 사용 하 여 추가 복제본을 사용 하 여 Azure Cache 인스턴스를 설정 하는 방법을 알아봅니다.

Redis Standard 및 Premium 계층 용 Azure Cache는 두 개의 전용 Vm (가상 머신)에서 각 캐시를 호스팅하여 중복성을 제공 합니다. 이러한 Vm은 주 복제본과 복제본으로 구성 됩니다. 주 VM을 사용할 수 없게 되 면 복제본은이를 검색 하 여 새 주 복제본으로 자동으로 가져옵니다. 이제 프리미엄 캐시의 복제본 수를 최대 3 개까지 늘릴 수 있으므로 캐시를 지 원하는 Vm이 총 4 개씩 제공 됩니다. 복제본이 여러 개 있으면 단일 복제본이 제공할 수 있는 것 보다 더 높은 복원 력이 발생 합니다.

> [!IMPORTANT]
> 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요. 
> 

## <a name="prerequisites"></a>사전 요구 사항
* Azure 구독- [무료로 하나 만들기](https://azure.microsoft.com/free/)

> [!NOTE]
> 이 기능은 현재 미리 보기 상태입니다. 관심이 있는 경우 [문의해 주세요](mailto:azurecache@microsoft.com) .
>

## <a name="create-a-cache"></a>캐시 만들기
캐시를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기**를 선택합니다.
  
1. **새로 만들기** 페이지에서 **데이터베이스**를 선택한 다음, **Azure Cache for Redis**를 선택합니다.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Redis 용 Azure Cache를 선택 합니다.":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
    | 설정      | 제안 값  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름*은 *\<DNS name>.redis.cache.windows.net*입니다. | 
    | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
    | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기**를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
    | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **가격 책정 계층** | 드롭다운 및 [프리미엄 계층](https://azure.microsoft.com/pricing/details/cache/) 캐시를 선택 합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
    | **복제본 수** | 슬라이드를 선택 하 여 복제본 수를 선택 합니다. | 기본값은 1입니다. |
   
1. 프리미엄 계층 캐시를 선택 하면 Redis 클러스터링을 사용 하도록 설정할지 여부를 묻는 메시지가 표시 됩니다. **클러스터링** 을 *사용 안 함*으로 유지 합니다. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Redis 클러스터를 구성 합니다.":::

    > [!NOTE]
    > 다중 복제본 지원은 현재 비클러스터형 캐시 에서만 작동 합니다.
    >

1. **만들기**를 클릭합니다. 
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Redis에 대 한 Azure 캐시를 만듭니다.":::
   
    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태**가 **실행 중**으로 표시되면 캐시를 사용할 준비가 된 것입니다.

    > [!NOTE]
    > 캐시의 복제본 수를 만든 후에는 변경할 수 없습니다.
    >

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Redis Premium 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)
