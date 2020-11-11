---
title: Azure 개인 링크를 사용 하는 azure Cache for Redis (미리 보기)
description: Azure 개인 끝점은 azure 개인 링크를 통해 Redis으로 구동 되는 Azure Cache에 대해 개인적이 고 안전 하 게 연결 하는 네트워크 인터페이스입니다. 이 문서에서는 Azure Portal를 사용 하 여 Azure 캐시, Azure Virtual Network 및 개인 끝점을 만드는 방법에 대해 설명 합니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 1a9d5fe69cd9d853d0bf8ec971f31518bbf47c9a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504699"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure 개인 링크를 사용 하는 azure Cache for Redis (공개 미리 보기)
이 문서에서는 Azure Portal를 사용 하 여 개인 끝점이 있는 Redis 인스턴스에 대 한 가상 네트워크 및 Azure Cache를 만드는 방법에 대해 알아봅니다. Redis 인스턴스에 대 한 기존 Azure 캐시에 개인 끝점을 추가 하는 방법에 대해서도 알아봅니다.

Azure 개인 끝점은 azure 개인 링크를 통해 Redis으로 구동 되는 Azure Cache에 대해 개인적이 고 안전 하 게 연결 하는 네트워크 인터페이스입니다. 

## <a name="prerequisites"></a>필수 구성 요소
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> 개인 끝점을 사용 하려면 2020 년 7 월 28 일 이후에 Azure Cache for Redis 인스턴스를 만들어야 합니다.
> 현재 지역에서 복제, 방화벽 규칙, 포털 콘솔 지원, 클러스터 된 캐시에 대 한 다중 끝점, 방화벽과 삽입 된 캐시에 대 한 지 속성은 지원 되지 않습니다. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Redis 인스턴스에 대 한 새 Azure Cache를 사용 하 여 개인 끝점 만들기 

이 섹션에서는 개인 끝점을 사용 하 여 Redis 인스턴스에 대 한 새 Azure Cache를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기 

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="리소스 만들기를 선택 합니다.":::

2. **새로 만들기** 페이지에서 **네트워킹** 을 선택한 다음 **가상 네트워크** 를 선택 합니다.

3. **추가** 를 선택 하 여 가상 네트워크를 만듭니다.

4. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 가상 네트워크를 만들 구독입니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 가상 네트워크 및 기타 리소스를 만들 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **이름** | 가상 네트워크 이름을 입력 하십시오. | 이름은 문자 또는 숫자로 시작 하 고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. | 
   | **지역** | 드롭다운을 선택 하 고 지역을 선택 합니다. | 가상 네트워크를 사용 하는 다른 서비스 근처의 [지역을](https://azure.microsoft.com/regions/) 선택 합니다. |

5. **Ip 주소** 탭을 선택 하거나 페이지 맨 아래에 있는 **다음: ip 주소** 단추를 클릭 합니다.

6. **IP 주소** 탭에서 **IPv4 주소 공간** 을 CIDR 표기법 (예: 192.168.1.0/24)에서 하나 이상의 주소 접두사로 지정 합니다.

7. **서브넷 이름** 에서 **기본값** 을 클릭 하 여 서브넷의 속성을 편집 합니다.

8. 서브넷 **편집** 창에서 서브넷 **이름** 및 **서브넷 주소 범위** 를 지정 합니다. 서브넷의 주소 범위는 CIDR 표기법 (예: 192.168.1.0/24) 이어야 합니다. 가상 네트워크의 주소 공간에 포함되어야 합니다.

9. **저장** 을 선택합니다.

10. **검토 + 만들기** 탭을 선택 하거나 **검토 + 만들기** 단추를 클릭 합니다.

11. 모든 정보가 올바른지 확인 하 고 **만들기** 를 클릭 하 여 가상 네트워크를 프로 비전 합니다.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 Redis 인스턴스에 대 한 Azure 캐시 만들기
캐시 인스턴스를 만들려면 다음 단계를 수행 합니다.

1. Azure Portal 홈페이지로 돌아가서 사이드바 메뉴를 열고 **리소스 만들기** 를 선택 합니다. 
   
1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Azure Cache for Redis를 선택합니다.":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. | 
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **가격 책정 계층** | 드롭다운하여 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cache/)을 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

1. **네트워킹** 탭을 선택하거나 페이지 하단에 있는 **네트워킹** 단추를 클릭합니다.

1. **네트워킹** 탭에서 연결 방법에 대 한 **개인 끝점** 을 선택 합니다.

1. **추가** 단추를 클릭 하 여 개인 끝점을 만듭니다.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="네트워킹에서 개인 끝점을 추가 합니다.":::

1. **개인 끝점 만들기** 페이지에서, 마지막 섹션에서 만든 가상 네트워크 및 서브넷을 사용 하 여 개인 끝점에 대 한 설정을 구성 하 고 **확인** 을 선택 합니다. 

1. 페이지 맨 아래에서 **다음: 고급** 탭을 선택하거나 페이지 하단에서 **다음: 고급** 단추를 클릭합니다.

1. 기본 또는 표준 캐시 인스턴스의 **고급** 탭에서 TLS 포트가 아닌 다른 포트를 사용하도록 설정하려면 사용 토글을 선택합니다.

1. 프리미엄 캐시 인스턴스의 **고급** 탭에서 TLS가 아닌 다른 포트, 클러스터링 및 데이터 지속성에 대한 설정을 구성합니다.

1. 페이지 맨 아래에서 **다음: 태그** 탭을 선택하거나 페이지 하단에서 **다음: 태그** 단추를 클릭합니다.

1. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다. 

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다. 
    
> [!IMPORTANT]
> 
> `publicNetworkAccess` `Enabled` 기본적으로 플래그가 있습니다. 
> 이 플래그는가로 설정 된 경우 선택적으로 공용 및 개인 끝점 액세스를 모두 허용할 수 있도록 하기 위한 것입니다 `Enabled` . 로 설정 되 면 `Disabled` 개인 끝점 액세스만 허용 됩니다. 다음 PATCH 요청을 사용 하 여 값을로 설정할 수 있습니다 `Disabled` .
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> 클러스터형 캐시에 연결 하려면를 `publicNetworkAccess` 로 설정 해야 `Disabled` 하며 개인 끝점 연결은 하나만 있을 수 있습니다. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Redis 인스턴스에 대 한 기존 Azure Cache를 사용 하 여 개인 끝점 만들기 

이 섹션에서는 Redis 인스턴스에 대 한 기존 Azure 캐시에 개인 끝점을 추가 합니다. 

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기 
가상 네트워크를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기** 를 선택합니다.

2. **새로 만들기** 페이지에서 **네트워킹** 을 선택한 다음 **가상 네트워크** 를 선택 합니다.

3. **추가** 를 선택 하 여 가상 네트워크를 만듭니다.

4. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 가상 네트워크를 만들 구독입니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 가상 네트워크 및 기타 리소스를 만들 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **이름** | 가상 네트워크 이름을 입력 하십시오. | 이름은 문자 또는 숫자로 시작 하 고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. | 
   | **지역** | 드롭다운을 선택 하 고 지역을 선택 합니다. | 가상 네트워크를 사용 하는 다른 서비스 근처의 [지역을](https://azure.microsoft.com/regions/) 선택 합니다. |

5. **Ip 주소** 탭을 선택 하거나 페이지 맨 아래에 있는 **다음: ip 주소** 단추를 클릭 합니다.

6. **IP 주소** 탭에서 **IPv4 주소 공간** 을 CIDR 표기법 (예: 192.168.1.0/24)에서 하나 이상의 주소 접두사로 지정 합니다.

7. **서브넷 이름** 에서 **기본값** 을 클릭 하 여 서브넷의 속성을 편집 합니다.

8. 서브넷 **편집** 창에서 서브넷 **이름** 및 **서브넷 주소 범위** 를 지정 합니다. 서브넷의 주소 범위는 CIDR 표기법 (예: 192.168.1.0/24) 이어야 합니다. 가상 네트워크의 주소 공간에 포함되어야 합니다.

9. **저장** 을 선택합니다.

10. **검토 + 만들기** 탭을 선택 하거나 **검토 + 만들기** 단추를 클릭 합니다.

11. 모든 정보가 올바른지 확인 하 고 **만들기** 를 클릭 하 여 가상 네트워크를 프로 비전 합니다.

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기 

개인 끝점을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **Azure Cache For Redis** 를 검색 하 고 enter 키를 누르거나 검색 제안에서 선택 합니다.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Redis에 대 한 Azure 캐시를 검색 합니다.":::

2. 개인 끝점을 추가 하려는 캐시 인스턴스를 선택 합니다.

3. 화면 왼쪽에서 **(미리 보기) 개인 끝점** 을 선택 합니다.

4. 개인 **끝점** 단추를 클릭 하 여 개인 끝점을 만듭니다.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="개인 끝점을 추가 합니다.":::

5. **개인 끝점 만들기 페이지** 에서 개인 끝점에 대 한 설정을 구성 합니다.

   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 개인 끝점을 만들 구독입니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 개인 끝점 및 기타 리소스를 만들 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **이름** | 개인 끝점 이름을 입력 합니다. | 이름은 문자 또는 숫자로 시작 하 고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. | 
   | **지역** | 드롭다운을 선택 하 고 지역을 선택 합니다. | 개인 끝점을 사용 하는 다른 서비스 근처의 [지역을](https://azure.microsoft.com/regions/) 선택 합니다. |

6. 페이지 맨 아래에 있는 **다음: 리소스** 단추를 클릭 합니다.

7. **리소스** 탭에서 구독을 선택 하 고, 리소스 종류를 선택 하 `Microsoft.Cache/Redis` 고, 개인 끝점을 연결 하려는 캐시를 선택 합니다.

8. 페이지 맨 아래에 있는 **다음: 구성** 단추를 클릭 합니다.

9. **구성** 탭에서 이전 섹션에서 만든 가상 네트워크 및 서브넷을 선택 합니다.

10. 페이지 맨 아래에 있는 **다음: 태그** 단추를 클릭 합니다.

11. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다.

12. **검토 + 만들기** 를 선택합니다. Azure에서 구성의 유효성을 검사 하는 **검토 + 만들기** 탭으로 이동 됩니다.

13. 녹색 **유효성 검사 통과** 메시지가 표시 되 면 **만들기** 를 선택 합니다.

## <a name="faq"></a>FAQ

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>개인 끝점에 연결할 수 없는 이유는 무엇입니까?
캐시가 이미 VNet 삽입 캐시 인 경우 개인 끝점은 캐시 인스턴스와 함께 사용할 수 없습니다. 캐시 인스턴스가 지원 되지 않는 기능을 사용 하 고 있는 경우 (아래에 나열 됨) 개인 끝점 인스턴스에 연결할 수 없습니다. 또한 개인 끝점을 사용 하려면 7 월 27 일 이후 캐시 인스턴스를 만들어야 합니다.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>개인 끝점에서 지원 되지 않는 기능은 무엇입니까?
지역에서 복제, 방화벽 규칙, 포털 콘솔 지원, 클러스터 된 캐시 당 여러 끝점, 방화벽 규칙 및 영역 중복성에 대 한 지 속성 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-from-public-network-access"></a>내 개인 끝점을 공용 네트워크 액세스에서 사용 하지 않도록 변경 하려면 어떻게 해야 하나요?
`publicNetworkAccess` `Enabled` 기본적으로 플래그가 있습니다. 이 플래그는가로 설정 된 경우 선택적으로 공용 및 개인 끝점 액세스를 모두 허용할 수 있도록 하기 위한 것입니다 `Enabled` . 로 설정 되 면 `Disabled` 개인 끝점 액세스만 허용 됩니다. 다음 PATCH 요청을 사용 하 여 값을로 설정할 수 있습니다 `Disabled` .
```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>개인 끝점에 대해 NSG (네트워크 보안 그룹)를 사용할 수 있나요?
아니요. 전용 끝점에 대해서는 사용할 수 없습니다. 그러나 서브넷에 다른 리소스가 있는 경우 NSG 적용이 해당 리소스에 적용 됩니다.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>어떻게 클러스터형 캐시에 연결할 수 있나요?
`publicNetworkAccess` 는로 설정 해야 `Disabled` 하며 개인 끝점 연결은 하나만 있을 수 있습니다.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>내 개인 끝점 인스턴스는 내 VNet에 있지 않으므로 VNet에 연결 하는 방법은 무엇 인가요?
VNet에만 연결 됩니다. VNet에 있지 않으므로 종속 끝점에 대 한 NSG 규칙을 수정할 필요가 없습니다.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>VNet 삽입 된 캐시를 개인 끝점 캐시로 마이그레이션하려면 어떻게 해야 하나요?
VNet 삽입 캐시를 삭제 하 고 개인 끝점을 사용 하 여 새 캐시 인스턴스를 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure 개인 링크에 대 한 자세한 내용은 [Azure 개인 링크 설명서](../private-link/private-link-overview.md)를 참조 하세요.
* 캐시 인스턴스에 대 한 다양 한 네트워크 격리 옵션을 비교 하려면 [Azure cache For Redis 네트워크 격리 옵션 설명서](cache-network-isolation.md)를 참조 하세요.