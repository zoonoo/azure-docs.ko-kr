---
title: 포함 파일
description: 포함 파일
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: 07a80fcd1ec62b051a660bdac696f89e3b7c42b7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93353894"
---
1. 캐시를 만들려면 [Azure Portal](https://portal.azure.com)에 로그인하여 **리소스 만들기** 를 선택합니다.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Azure Cache for Redis를 선택합니다.":::

   
1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Azure Cache for Redis를 선택합니다.":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. | 
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **가격 책정 계층** | 드롭다운하여 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cache/)을 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](../articles/azure-cache-for-redis/cache-overview.md)를 참조하세요. |

1. **네트워킹** 탭을 선택하거나 페이지 하단에 있는 **네트워킹** 단추를 클릭합니다.

1. **네트워킹** 탭에서 연결 방법을 선택합니다.

1. 페이지 맨 아래에서 **다음: 고급** 탭을 선택하거나 페이지 하단에서 **다음: 고급** 단추를 클릭합니다.

1. 기본 또는 표준 캐시 인스턴스의 **고급** 탭에서 TLS 포트가 아닌 다른 포트를 사용하도록 설정하려면 사용 토글을 선택합니다. 4 또는 (미리 보기) 6 중에서 사용하려는 Redis 버전을 선택할 수도 있습니다.

    :::image type="content" source="media/redis-cache-create/redis-version.png" alt-text="Redis 버전 4 또는 6.":::

1. 프리미엄 캐시 인스턴스의 **고급** 탭에서 TLS가 아닌 다른 포트, 클러스터링 및 데이터 지속성에 대한 설정을 구성합니다. 4 또는 (미리 보기) 6 중에서 사용하려는 Redis 버전을 선택할 수도 있습니다. 

1. 페이지 맨 아래에서 **다음: 태그** 탭을 선택하거나 페이지 하단에서 **다음: 태그** 단추를 클릭합니다.

1. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다. 

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다. 