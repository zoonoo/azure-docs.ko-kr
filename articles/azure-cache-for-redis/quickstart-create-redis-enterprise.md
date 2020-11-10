---
title: '빠른 시작: Enterprise 계층 캐시 만들기'
description: 이 빠른 시작에서는 Azure Cache for Redis Enterprise 계층의 인스턴스를 만드는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bd5e05f38d34199d9012c52ca3fdad33af231aad
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127988"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>빠른 시작: Enterprise 계층 캐시 만들기(미리 보기)

Azure Cache for Redis Enterprise 계층은 Azure에서 완전히 통합되고 관리되는 [Redis Enterprise](https://redislabs.com/redis-enterprise/)를 제공합니다. 현재는 미리 보기로 제공됩니다. 이 미리 보기에는 두 개의 새로운 계층이 있습니다.
* Enterprise는 가상 머신에서 휘발성 메모리(DRAM)를 사용하여 데이터를 저장합니다.
* Enterprise Flash는 휘발성 및 비휘발성 메모리(NVMe 또는 SSD)를 사용하여 데이터를 저장합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 Azure 구독이 필요합니다. 아직 없는 경우 먼저 [체험 계정](https://azure.microsoft.com/free/)을 만드세요.

## <a name="create-a-cache"></a>캐시 만들기
1. 캐시를 만들려면 미리 보기 초대 링크를 사용하여 Azure Portal에 로그인하여 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Azure Cache for Redis 선택":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.<Azure region>.redisenterprise.cache.azure.net* 입니다. | 
   | **위치** | 드롭다운하여 위치를 선택합니다. | 엔터프라이즈 계층은 미리 보기 중에 제한된 Azure 지역에서 사용할 수 있습니다. |
   | **캐시 유형** | *Enterprise* 또는 *Enterprise Flash* 계층 및 크기를 드롭다운하여 선택합니다. |  계층에 따라 캐시에 사용 가능한 크기, 성능 및 기능이 결정됩니다. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Enterprise 계층 기본 사항":::

   > [!NOTE] 
   > 계속하기 전에 "약관" 아래의 확인란을 선택해야 합니다.
   >

1. 완료되면 **다음: 네트워킹** 을 선택하고 건너뜁니다.

   > [!NOTE] 
   > 프라이빗 링크 옵션은 롤아웃 중이며 해당 지역에서 즉시 사용하지 못할 수 있습니다.
   >

1. 완료되면 **다음: 고급** 을 선택하고 **클러스터링 정책** 을 **Enterprise** 로 설정합니다.
   
   기본 설정을 유지하거나 적절하게 변경할 수 있습니다. **TLS를 통해서만 액세스 허용** 을 설정하는 경우 애플리케이션에서 새 캐시에 액세스하려면 TLS를 사용해야 합니다.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Enterprise 계층 고급 정보":::

   > [!NOTE] 
   > Redis 모듈은 아직 Enterprise Flash 계층에서 지원되지 않습니다. Redis 모듈을 사용하려는 경우 Enterprise 계층 캐시를 선택해야 합니다.
   >
   
1. 완료되면 **다음: 태그** 를 선택하고 건너뜁니다.

1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Enterprise 계층 요약":::

1. 설정을 검토하고 **만들기** 를 클릭합니다.
   
   캐시를 만드는 데는 약간의 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cache for Redis의 Enterprise 계층 인스턴스를 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)

