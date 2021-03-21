---
title: Redis 인스턴스에 대 한 엔터프라이즈 Azure 캐시에 대 한 활성 지역 복제 구성
description: Azure 지역에서 Redis Enterprise 인스턴스에 대 한 Azure 캐시를 복제 하는 방법을 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: fe777c3aa7b314dc56a42cc64712d18281a6ea7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121170"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Redis 인스턴스에 대 한 엔터프라이즈 Azure 캐시에 대 한 활성 지역 복제 구성 (미리 보기)

이 문서에서는 Azure Portal를 사용 하 여 활성 지역 복제 Azure 캐시를 구성 하는 방법을 알아봅니다.

활성 지역 복제는 Redis 인스턴스에 대 한 둘 이상의 Enterprise Azure 캐시를 Azure 지역에 걸쳐 있는 단일 캐시로 그룹화 합니다. 모든 인스턴스는 로컬 기본 역할을 합니다. 응용 프로그램은 읽기 및 쓰기 요청에 사용할 인스턴스를 결정 합니다.

## <a name="create-or-join-an-active-geo-replication-group"></a>활성 지역 복제 그룹 만들기 또는 조인

> [!IMPORTANT]
> 활성 지역 복제는 Azure Cache for Redis를 만들 때 사용 하도록 설정 되어야 합니다.
>
>

1. **새 Redis Cache** 만들기 UI에서 **구성** 을 클릭 하 여 **고급** 탭에서 **활성 지역 복제** 를 설정 합니다.

    ![활성 지역 복제 구성](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. 첫 번째 캐시 인스턴스에 대해 새 복제 그룹을 만들거나 목록에서 기존 복제 그룹을 선택 합니다.

    ![캐시 연결](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. **구성** 을 클릭 하 여 완료 합니다.

    ![활성 지역 복제 구성 됨](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. 지역에서 복제 그룹의 각 추가 캐시 인스턴스에 대해 위의 단계를 반복 합니다.

## <a name="remove-from-an-active-geo-replication-group"></a>활성 지역 복제 그룹에서 제거

활성 지역 복제 그룹에서 캐시 인스턴스를 제거 하려면 인스턴스를 삭제 하기만 하면 됩니다. 나머지 인스턴스는 자동으로 다시 구성 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 기능에 대해 자세히 알아보세요.

* [Redis 서비스 계층에 대 한 Azure 캐시](cache-overview.md#service-tiers)
* [Redis 용 Azure Cache의 고가용성](cache-high-availability.md)
