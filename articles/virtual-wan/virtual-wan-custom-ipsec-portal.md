---
title: 'Azure 가상 WAN에 대 한 사용자 지정 IPsec 정책 구성: 포털 | Microsoft Docs'
description: 포털을 사용 하 여 Azure 가상 WAN에 대 한 사용자 지정 IPsec 정책을 구성 하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515747"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>포털을 사용 하 여 가상 WAN에 대 한 사용자 지정 IPsec 정책 구성

Azure Portal에서 가상 WAN에 대 한 사용자 지정 IPsec 정책을 구성할 수 있습니다. 사용자 지정 정책은 양쪽 (온-프레미스 및 Azure VPN gateway)에서 IKE 단계 1 및 IKE 단계 2에 동일한 설정을 사용 하려는 경우에 유용 합니다.

## <a name="working-with-custom-policies"></a>사용자 지정 정책 사용

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>정책 구성

1. **가상 허브를 찾습니다**. 브라우저에서 [Azure 포털](https://aka.ms/azurevirtualwanpreviewfeatures) 로 이동하고 Azure 계정으로 로그인합니다. 사이트에 대 한 가상 허브를 찾습니다.
2. **VPN 사이트를 선택**합니다. 허브 페이지에서 사용자 지정 정책을 설정 하려는 VPN 사이트를 선택 합니다.

   ![선택](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **VPN 연결을 편집**합니다. 상황에 **맞는 메뉴** **...에서** **VPN 연결 편집**을 선택 합니다.

   ![편집할](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **설정을 구성**합니다. **VPN 연결 편집** 페이지에서 설정을 구성 합니다. **저장**을 선택하여 설정을 저장합니다.

   ![구성 및 저장](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.