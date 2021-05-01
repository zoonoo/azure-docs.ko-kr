---
title: 'Azure Virtual WAN에 대한 사용자 지정 IPsec 정책 구성: 포털 | Microsoft Docs'
description: 포털을 사용하여 Azure Virtual WAN에 대한 사용자 지정 IPsec 정책을 구성하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91851179"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>포털을 사용하여 Virtual WAN의 사용자 지정 IPsec 정책 구성

Azure Portal에서 가상 WAN VPN 연결에 대한 사용자 지정 IPsec 정책을 구성할 수 있습니다. 사용자 지정 정책은 양쪽(온-프레미스 및 Azure VPN 게이트웨이)에서 IKE 단계 1과 IKE 단계 2에 동일한 설정을 사용하려는 경우에 유용합니다.

## <a name="working-with-custom-policies"></a>다음과 같은 사용자 지정 정책에서 작동

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>정책 구성

1. **가상 허브를 찾습니다**. 브라우저에서 [Azure 포털](https://aka.ms/azurevirtualwanpreviewfeatures) 로 이동하고 Azure 계정으로 로그인합니다. Virtual WAN 리소스를 찾고 VPN 사이트가 연결된 가상 허브를 찾습니다.
2. **VPN 사이트를 선택합니다**. 허브 개요 페이지에서 **VPN(사이트 간)** 을 클릭하고 사용자 지정 IPsec 정책을 설정하려는 VPN 사이트를 선택합니다.

   ![선택](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **VPN 연결을 편집합니다**. **바로 가기 메뉴** **...** 에서 **VPN 연결 편집** 을 선택합니다.

   ![편집](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **설정을 구성합니다**. **VPN 연결 편집** 페이지에서 IPsec 설정을 기본값에서 사용자 지정으로 변경하고 IPsec 정책을 사용자 지정합니다. **저장** 을 선택하여 설정을 저장합니다.

   ![구성 및 저장](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.