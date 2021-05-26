---
title: Azure Cache for Redis에 대한 영역 중복 사용
description: Premium 및 Enterprise 계층 Azure Cache for Redis 인스턴스에 영역 중복을 설정하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: c355939191e6da9a9408edde02deac97d69c9bbf
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110084433"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 영역 중복 사용
이 문서에서는 Azure Portal을 사용하여 영역 중복 Azure Cache 인스턴스를 구성하는 방법을 알아봅니다.

Azure Cache for Redis Standard, Premium, Enterprise 계층은 두 개의 전용 가상 머신(VM)에서 각 캐시를 호스트하여 중복을 기본 제공합니다. 이와 같은 VM은 별도의 [Azure 장애 및 업데이트 도메인](../virtual-machines/availability.md)에 있고 가용성이 높지만 데이터 센터 수준의 오류에 취약합니다. 또한 Azure Cache for Redis는 Premium 및 Enterprise 계층에서 영역 중복을 지원합니다. 영역 중복 캐시는 여러 [가용성 영역](../availability-zones/az-overview.md)에 걸쳐 분산된 VM에서 실행됩니다. 이는 더 높은 복원력 및 가용성을 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>캐시 만들기
캐시를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기** 를 선택합니다.
  
1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Azure Cache for Redis를 선택합니다.":::
   
1. **기본 사항** 페이지에서 새 캐시의 설정을 구성합니다.
   
    | 설정      | 제안 값  | 설명 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **구독** | 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
    | **리소스 그룹** | 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
    | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. | 
    | **위치** | 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **캐시 유형** | [Premium 또는 Enterprise 계층](https://azure.microsoft.com/pricing/details/cache/) 캐시를 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
   
1. **고급** 페이지에서 Premium 계층 캐시에 대해 **복제본 수** 를 선택합니다.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="복제본 수":::

1. **가용성 영역** 을 선택합니다. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="가용성 영역":::

1. 다른 옵션은 기본 설정으로 그대로 둡니다. 

    > [!NOTE]
    > 영역 중복 지원은 현재 비 클러스터 및 지역 복제되지 않은 캐시에만 사용할 수 있습니다. 또한 프라이빗 링크, 스케일링, 데이터 지속성 또는 가져오기/내보내기를 지원하지 않습니다.
    >

1. **만들기** 를 클릭합니다. 
   
    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.
   
    > [!NOTE]
    > 캐시를 만든 후에는 가용성 영역을 변경할 수 없습니다.
    >

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Cache for Redis 프리미엄 서비스 계층](cache-overview.md#service-tiers)