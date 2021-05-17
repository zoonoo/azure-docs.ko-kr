---
title: 공용 IP 만들기 - Azure Portal
description: Azure Portal에서 공용 IP를 만드는 방법 알아보기
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: c1ac3f2fa1ef3f1a24077064ad3ad3f3c30c5f3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048360"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Azure Portal을 사용하여 공용 IP 주소 만들기

이 문서에서는 Azure Portal을 사용하여 공용 IP 주소 리소스를 만드는 방법을 소개합니다. 

이 공용 IP와 연결할 수 있는 리소스와 기본 및 표준 SKU 간의 차이점에 대한 자세한 내용은 [공용 IP 주소](./public-ip-addresses.md)를 참조하세요. 

이 문서에서는 IPv4 주소를 집중적으로 설명합니다. IPv6 주소에 대한 자세한 내용은 [Azure VNet용 IPv6](./ipv6-overview.md)를 참조하세요.

# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-create-public-ip-standard-zones)

다음 단계를 사용하여 **myStandardZRPublicIP** 라는 표준 영역 중복 공용 IP 주소를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다. 
3. 검색 상자에서 **공용 IP 주소** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.
4. **공용 IP 주소** 페이지에서 **만들기** 를 선택합니다.
5. **공용 IP 주소 만들기** 페이지에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.                 |    
    | SKU                     | **표준** 선택         |
    | 계층*                   | **지역** 선택         |
    | 이름                    | **myStandardZRPublicIP** 를 입력합니다.          |
    | IP 주소 할당   | 이 선택 항목은 "정적"으로 잠겨 있습니다.                                        |
    | 라우팅 기본 설정      | **Microsoft 네트워크** 의 기본값을 그대로 둡니다. </br> 라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정(미리 보기)이란?](./routing-preference-overview.md)을 참조하세요. |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.        |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독을 선택합니다.   |
    | Resource group          | **새로 만들기** 를 선택하고, **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **미국 동부 2** 선택      |
    | 가용성 영역       | **영역 중복**, 영역 없음을 선택하거나 특정 영역을 선택합니다(아래 참고 참조). |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Azure Portal의 표준 IP 주소 만들기" border="false":::

> [!NOTE]
> 이러한 선택 항목은 [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)있는 지역에서 유효합니다. </br>
이러한 지역에서 특정 영역을 선택할 수 있지만 영역 오류에 대한 복원력이 없습니다. </br> 가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

\* = 계층은 현재 미리 보기로 제공되는 [지역 간 부하 분산 장치](../load-balancer/cross-region-overview.md) 기능과 관련이 있습니다.

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-create-public-ip-basic)

이 섹션에서는 **myBasicPublicIP** 라는 기본 공용 IP 주소를 만듭니다. 

> [!NOTE]
> 기본 공용 IP는 가용성 영역을 지원하지 않습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** 를 선택합니다. 
3. 검색 상자에서 **공용 IP 주소** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.
4. **공용 IP 주소** 페이지에서 **만들기** 를 선택합니다.
5. **공용 IP 주소 만들기** 페이지에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                       |
    | ---                     | ---                         |
    | IP 버전              | IPv4를 선택합니다.                 |    
    | SKU                     | **기본** 을 선택합니다.         |
    | 이름                    | *myBasicPublicIP* 입력          |
    | IP 주소 할당   | **정적** 을 선택합니다(아래 참고 참조).                                     |
    | 유휴 제한 시간(분)  | 기본값 **4** 를 그대로 둡니다.       |
    | DNS 이름 레이블          | 이 값은 빈 상태로 둡니다.    |
    | Subscription            | 구독을 선택합니다.   |
    | Resource group          | **새로 만들기** 를 선택하고, **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | Location                | **미국 동부 2** 선택      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Azure Portal의 표준 IP 주소 만들기" border="false":::

시간에 따라 IP 주소를 변경하는 것이 허용되는 경우 **동적** IP 할당을 선택할 수 있습니다.

---

## <a name="additional-information"></a>추가 정보 

위에 나열된 개별 필드에 대한 자세한 내용은 [공용 IP 주소 관리](./virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [공용 IP 주소를 가상 머신에](./associate-public-ip-address-vm.md#azure-portal) 연결
- Azure에서 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.