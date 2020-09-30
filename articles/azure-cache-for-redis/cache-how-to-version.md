---
title: Redis에 대 한 Azure Cache Redis 버전 설정 (미리 보기)
description: Redis 버전을 구성 하는 방법 알아보기
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ed0f486afe466d31388fa99b4ce5f5754210533f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571503"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Redis에 대 한 Azure Cache Redis 버전 설정 (미리 보기)
이 문서에서는 캐시 인스턴스에 사용할 Redis 소프트웨어 버전을 구성 하는 방법에 대해 알아봅니다. Redis 용 Azure Cache는 Redis의 최신 주 버전 및 하나 이상의 이전 버전을 제공 합니다. 최신 Redis 소프트웨어가 출시 됨에 따라 이러한 버전을 정기적으로 업데이트 합니다. 사용 가능한 두 버전 중에서 선택할 수 있습니다. 현재 사용 중인 버전이 더 이상 지원 되지 않는 경우 캐시가 자동으로 다음 버전으로 업그레이드 됨을 명심 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소
* Azure 구독- [무료로 하나 만들기](https://azure.microsoft.com/free/)

> [!NOTE]
> 이 기능은 현재 미리 보기 상태입니다. 관심이 있는 경우 [문의해 주세요](mailto:azurecache@microsoft.com) .
>

## <a name="create-a-cache"></a>캐시 만들기
캐시를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기**를 선택합니다.
  
1. **새로 만들기** 페이지에서 **데이터베이스**를 선택한 다음, **Azure Cache for Redis**를 선택합니다.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Redis 용 Azure Cache를 선택 합니다.":::
   
1. **기본 사항** 페이지에서 새 캐시의 설정을 구성 합니다.
   
    | 설정      | 제안 값  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **구독** | 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
    | **리소스 그룹** | 리소스 그룹을 선택 하거나 **새로 만들기** 를 선택 하 고 새 리소스 그룹 이름을 입력 합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
    | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름*은 *\<DNS name>.redis.cache.windows.net*입니다. | 
    | **위치** | 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **캐시 유형** | [캐시 계층 및 크기](https://azure.microsoft.com/pricing/details/cache/)를 선택 합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
   
1. **고급** 페이지에서 사용할 Redis 버전을 선택 합니다.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis 용 Azure Cache를 선택 합니다.":::

1. **만들기**를 클릭합니다. 
   
    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태**가 **실행 중**으로 표시되면 캐시를 사용할 준비가 된 것입니다.

    > [!NOTE]
    > 이번에는 캐시가 만들어진 후 Redis 버전을 변경할 수 없습니다.
    >

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Redis Premium 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)
