---
title: Express 경로 및 Azure VPN에 대해 지원 되는 Azure 경로 서버 (미리 보기) 정보
description: Azure 경로 서버가 Express 경로 및 Azure VPN 게이트웨이와 상호 작용 하는 방법에 대해 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679969"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Express 경로 및 Azure VPN에 대 한 Azure 경로 서버 (미리 보기) 지원 정보

Azure 경로 서버는 Azure에서 실행 되는 타사 네트워크 가상 어플라이언스 (NVA) 뿐만 아니라 Express 경로 및 Azure VPN 게이트웨이와 원활 하 게 통합 됩니다. 게이트웨이와 Azure 경로 서버 간에 BGP 피어 링을 구성 하거나 관리할 필요가 없습니다. 간단한 [구성 변경](quickstart-configure-route-server-powershell.md#route-exchange)으로 게이트웨이 및 Azure 경로 서버 간에 경로 교환을 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-does-it-work"></a>작동 원리

가상 네트워크에서 Express 경로 게이트웨이 및 NVA와 함께 Azure 경로 서버를 배포 하는 경우 기본적으로 Azure 경로 서버는 NVA 및 Express 경로 게이트웨이에서 받은 경로를 서로 전파 하지 않습니다. 경로 교환을 사용 하도록 설정 하 고 나면 Express 경로와 NVA가 서로의 경로를 학습 합니다.

예를 들어 다음 다이어그램에서:

* SDWAN 어플라이언스는 가상 네트워크 경로와 함께 Express 경로에 연결 된 "온-프레미스 2"의 경로를 Azure 경로 서버에서 받습니다.

* Express 경로 게이트웨이는 Azure 경로 서버에서 가상 네트워크 경로와 함께 SDWAN 어플라이언스에 연결 된 "온-프레미스 1"에서 경로를 수신 합니다.

    ![경로 서버를 사용 하 여 구성 된 Express 경로를 보여 주는 다이어그램](./media/expressroute-vpn-support/expressroute-with-route-server.png)

SDWAN 어플라이언스를 Azure VPN gateway로 바꿀 수도 있습니다. Azure VPN gateway와 Express 경로는 완전히 관리 되기 때문에 두 온-프레미스 네트워크에 대 한 경로 교환은 서로 통신할 수 있도록 설정 해야 합니다.

> [!IMPORTANT] 
> [**활성-활성**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) 모드로 Azure VPN gateway를 구성 해야 합니다.
>

![경로 서버를 사용 하 여 구성 된 Express 경로 및 VPN gateway를 보여 주는 다이어그램](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>다음 단계

- [Azure Route Server](route-server-faq.md)에 대해 자세히 알아보세요.
- [Azure Route Server를 구성](quickstart-configure-route-server-powershell.md)하는 방법에 대해 알아봅니다.
- [Azure express 경로 및 AZURE VPN 공존](../expressroute/expressroute-howto-coexist-resource-manager.md)에 대해 자세히 알아보세요.
