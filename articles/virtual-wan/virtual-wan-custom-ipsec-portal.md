---
title: 'Azure 가상 WAN: 포털에 대한 사용자 지정 IPsec 정책 구성 | 마이크로 소프트 문서'
description: 포털을 사용하여 Azure Virtual WAN에 대한 사용자 지정 IPsec 정책을 구성하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515747"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>포털을 사용하여 가상 WAN에 대한 사용자 지정 IPsec 정책 구성

Azure 포털에서 가상 WAN에 대한 사용자 지정 IPsec 정책을 구성할 수 있습니다. 사용자 지정 정책은 양면(온-프레미스 및 Azure VPN 게이트웨이)이 IKE 1단계 및 IKE 2 단계에 대해 동일한 설정을 사용하려는 경우에 유용합니다.

## <a name="working-with-custom-policies"></a>사용자 지정 정책 작업

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>정책 구성

1. **가상 허브를 찾습니다.** 브라우저에서 [Azure 포털로](https://aka.ms/azurevirtualwanpreviewfeatures) 이동하여 Azure 계정으로 로그인합니다. 사이트의 가상 허브를 찾습니다.
2. **VPN 사이트를 선택합니다.** 허브 페이지에서 사용자 지정 정책을 설정할 VPN 사이트를 선택합니다.

   ![선택](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **VPN 연결을 편집합니다.** 컨텍스트 **메뉴에서** **...** VPN **연결 편집을**선택합니다.

   ![편집](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **설정을 구성합니다.** VPN **연결 편집** 페이지에서 설정을 구성합니다. 설정을 저장하려면 **저장을** 선택합니다.

   ![구성 및 저장](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.