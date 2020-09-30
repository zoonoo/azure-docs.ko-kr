---
title: Azure Cache for Redis (미리 보기)에 대 한 영역 중복성 사용
description: Redis 인스턴스에 대 한 프리미엄 계층 Azure 캐시에 대 한 영역 중복성을 설정 하는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 33c346fa2e4572799ad6341bd5115cdd6e3b9ec9
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569983"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Azure Cache for Redis (미리 보기)에 대 한 영역 중복성 사용
이 문서에서는 Azure Portal를 사용 하 여 영역 중복 Azure 캐시 인스턴스를 구성 하는 방법을 알아봅니다.

Redis Standard 및 Premium 계층 용 Azure Cache는 두 개의 전용 Vm (가상 머신)에서 각 캐시를 호스팅하여 중복성을 제공 합니다. 이러한 Vm은 별도의 [Azure 장애 및 업데이트 도메인](/azure/virtual-machines/windows/manage-availability) 에 있고 항상 사용 가능 하더라도 데이터 센터 수준 오류에 취약 합니다. 또한 Azure Cache for Redis는 프리미엄 계층에서 영역 중복성을 지원 합니다. 영역 중복 캐시는 여러 [가용성 영역](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures)에 걸쳐 분산 된 vm에서 실행 됩니다. 더 높은 복원 력 및 가용성을 제공 합니다.

> [!IMPORTANT]
> 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요. 
> 

## <a name="prerequisites"></a>사전 준비 사항
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
    | **캐시 유형** | [프리미엄 계층](https://azure.microsoft.com/pricing/details/cache/) 캐시를 선택 합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
   
1. **고급** 페이지에서 **복제본 수**를 선택 합니다.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Redis 용 Azure Cache를 선택 합니다.":::

1. **가용성 영역**을 선택 합니다. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Redis 용 Azure Cache를 선택 합니다.":::

1. 다른 옵션은 기본 설정으로 그대로 둡니다. 

    > [!NOTE]
    > 영역 중복성 지원은 현재 비클러스터형 및 지역에서 복제 되지 않은 캐시에만 사용할 수 있습니다. 또한 개인 링크, 크기 조정, 데이터 지 속성 또는 가져오기/내보내기를 지원 하지 않습니다.
    >

1. **만들기**를 클릭합니다. 
   
    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태**가 **실행 중**으로 표시되면 캐시를 사용할 준비가 된 것입니다.
   
    > [!NOTE]
    > 캐시를 만든 후에는 가용성 영역을 변경할 수 없습니다.
    >

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Redis Premium 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)
