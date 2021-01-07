---
title: Virtual WAN 허브에 Azure Firewall 설치
titleSuffix: Azure Virtual WAN
description: Virtual WAN 허브에서 Azure Firewall을 구성하는 단계
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983676"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Virtual WAN 허브에 Azure Firewall 구성

**보안 허브**는 Azure Firewall을 사용하는 Azure Virtual WAN 허브입니다. 이 문서에서는 Azure Virtual WAN 포털 페이지에서 직접 Azure Firewall을 설치하여 Virtual WAN 허브를 보안 허브로 변환하는 단계를 안내합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계에서는 하나 이상의 허브가 있는 Virtual WAN을 이미 배포했다고 가정합니다.

새 Virtual WAN 및 새 허브를 만들려면 다음 문서의 단계를 수행합니다.

* [Virtual WAN 만들기](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 만들기](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>가상 허브 보기

Virtual WAN의 **개요** 페이지에는 가상 허브 및 보안 허브 목록이 표시됩니다. 다음 그림은 보안 허브가 없는 Virtual WAN을 보여 줍니다.

[가상 ![ 허브 목록이 포함 된 가상 WAN의 개요 페이지를 보여 주는 스크린샷](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>보안 허브로 변환

1. Virtual WAN의 **개요** 페이지에서 보안 허브로 변환할 허브를 선택합니다. 가상 허브 페이지에 Azure Firewall을 이 허브에 배포하는 두 가지 옵션이 표시됩니다. 옵션 중 하나를 선택합니다.

   [![보안 허브 또는 Azure 방화벽으로 변환을 선택할 수 있는 가상 WAN의 개요 페이지가 스크린샷에 표시 됩니다.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. 옵션 중 하나를 선택하면 **보안 허브로 변환** 페이지가 표시됩니다. 변환할 허브를 선택하고 페이지 하단의 **다음: Azure Firewall**을 선택합니다.

   [ ![허브 선택](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. 워크플로를 완료한 후 **확인**을 선택합니다.

   [![스크린샷 확인을 선택 하 여 보안 허브로 변환 창을 보여 줍니다.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. 허브가 보안 허브로 변환되면 Virtual WAN **개요** 페이지에서 볼 수 있습니다.

   [ ![보안 허브 보기](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>허브 리소스 보기

Virtual WAN **개요** 페이지에서 보안 허브를 선택합니다. 허브 페이지에서 Azure Firewall을 비롯한 모든 가상 허브 리소스를 볼 수 있습니다.

[ ![허브 리소스 보기](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

보안 허브에서 Azure Firewall 설정을 보려면 **보안**에서 **보안 가상 허브 설정**을 선택합니다.
[ ![허브 설정 보기](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>추가 설정 구성

가상 허브에 대한 추가 Azure Firewall 설정을 구성하려면 **Azure Firewall Manager**의 링크를 선택합니다. 방화벽 정책에 대한 자세한 내용은 [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub)를 참조하세요.

[ ![추가 설정](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

허브 **개요** 페이지로 돌아가려면 다음 그림에 표시된 화살표와 같이 경로를 클릭하여 다시 탐색할 수 있습니다.

[ ![개요로 돌아가기](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
