---
title: Azure 개인 링크를 사용 하는 azure Cache for Redis (미리 보기)
description: Azure 개인 끝점은 azure 개인 링크를 통해 Redis으로 구동 되는 Azure Cache에 대해 개인적이 고 안전 하 게 연결 하는 네트워크 인터페이스입니다. 이 문서에서는 Azure Portal를 사용 하 여 Azure Cache, Azure virtual network 및 개인 끝점을 만드는 방법에 대해 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5db756b60330cdac4e43e13bfe29d9397f87af50
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421657"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure 개인 링크를 사용 하는 azure Cache for Redis (미리 보기)
Azure 개인 끝점은 azure 개인 링크를 통해 Redis으로 구동 되는 Azure Cache에 대해 개인적이 고 안전 하 게 연결 하는 네트워크 인터페이스입니다. 

이 문서에서는 Azure Portal를 사용 하 여 Azure Cache, Azure virtual network 및 개인 끝점을 만드는 방법에 대해 알아봅니다.  

> [!IMPORTANT]
> 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요. 
> 

## <a name="prerequisites"></a>필수 구성 요소
* Azure 구독- [무료로 하나 만들기](https://azure.microsoft.com/free/)

> [!NOTE]
> 이 기능은 현재 미리 보기 상태입니다. 관심이 있는 경우 [문의해 주세요](mailto:azurecache@microsoft.com) .
>

## <a name="create-a-cache"></a>캐시 만들기
1. 캐시를 만들려면 [Azure Portal](https://portal.azure.com)에 로그인하여 **리소스 만들기**를 선택합니다. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="리소스 만들기를 선택 합니다.":::
   
1. **새로 만들기** 페이지에서 **데이터베이스**를 선택한 다음, **Azure Cache for Redis**를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Redis 용 Azure Cache를 선택 합니다.":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름은* * \<DNS name> redis.cache.windows.net*가 됩니다. | 
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기**를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **가격 책정 계층** | 드롭다운하여 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cache/)을 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
   
1. **만들기**를 선택합니다. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Redis에 대 한 Azure 캐시를 만듭니다.":::
   
   캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태**가 **실행 중**으로 표시되면 캐시를 사용할 준비가 된 것입니다.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Redis 용 Azure Cache를 만들었습니다.":::

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 가상 네트워크 및 서브넷을 만듭니다.

1. **리소스 만들기**를 선택합니다.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="리소스 만들기를 선택 합니다.":::

2. **새로 만들기** 페이지에서 **네트워킹** 을 선택한 다음 **가상 네트워크**를 선택 합니다.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="가상 네트워크를 만듭니다.":::

3. **가상 네트워크 만들기**에서 **기본 사항** 탭에 다음 정보를 입력 하거나 선택 합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | 드롭다운하여 구독을 선택합니다.                                  |
    | 리소스 그룹   | 드롭다운 하 고 리소스 그룹을 선택 합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **\<virtual-network-name>** 를 입력합니다.                                    |
    | 지역           | [**\<region-name>** |

4. **Ip 주소** 탭을 선택 하거나 페이지 맨 아래에 있는 **다음: ip 주소** 단추를 선택 합니다.

5. **IP 주소** 탭에서 다음 정보를 입력 합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **\<IPv4-address-space>** 를 입력합니다. |

6. **서브넷 이름**에서 **기본**이라는 단어를 선택 합니다.

7. **서브넷 편집**에서 다음 정보를 입력 합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **\<subnet-name>** 를 입력합니다. |
    | 서브넷 주소 범위 | **\<subnet-address-range>** 를 입력합니다.

8. **저장**을 선택합니다.

9. **검토 + 만들기** 탭을 선택 하거나 **검토 + 만들기** 단추를 선택 합니다.

10. **만들기**를 선택합니다.


## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기 

이 섹션에서는 개인 끝점을 만들어 이전에 만든 캐시에 연결 합니다.

1. **개인 링크** 를 검색 하 고 enter 키를 누르거나 검색 제안에서 선택 합니다.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="개인 링크를 검색 합니다.":::

2. 화면 왼쪽에서 **개인 끝점**을 선택 합니다.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="개인 링크를 선택 합니다.":::

3. **+ 추가** 단추를 선택 하 여 개인 끝점을 만듭니다. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="개인 링크를 추가 합니다.":::

4. **개인 끝점 만들기 페이지**에서 개인 끝점에 대 한 설정을 구성 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 드롭다운하여 구독을 선택합니다. |
    | Resource group | 드롭다운 하 고 리소스 그룹을 선택 합니다. |
    | **인스턴스 정보** |  |
    | Name |개인 끝점의 이름을 입력 합니다.  |
    | 지역 |드롭다운하여 위치를 선택합니다. |
    |||

5. 페이지 맨 아래에 있는 **다음: 리소스** 단추를 선택 합니다.

6. **리소스** 탭에서 구독을 선택 하 고 리소스 종류를 Redis/으로 선택한 다음, 이전 섹션에서 만든 캐시를 선택 합니다.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="전용 링크에 대 한 리소스입니다.":::

7. 페이지 맨 아래에 있는 **다음: 구성** 단추를 선택 합니다.

8. **구성** 탭에서 이전 섹션에서 만든 가상 네트워크 및 서브넷을 선택 합니다.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="개인 링크에 대 한 구성입니다.":::

9. 페이지 맨 아래에 있는 **다음: 태그** 단추를 선택 합니다.

10. 리소스를 분류 하려는 경우 **태그** 탭에서 이름 및 값을 입력 합니다. 이 단계는 선택 사항입니다.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="개인 링크에 대 한 태그입니다.":::

11.  **검토 + 만들기**를 선택합니다. Azure에서 구성의 유효성을 검사 하는 **검토 + 만들기**   탭으로 이동 됩니다.

12. 녹색 **유효성 검사 통과** 메시지가 표시 되 면 **만들기**를 선택 합니다.


## <a name="next-steps"></a>다음 단계

개인 링크에 대 한 자세한 내용은 [Azure 개인 링크 설명서](https://docs.microsoft.com/azure/private-link/private-link-overview)를 참조 하세요. 

