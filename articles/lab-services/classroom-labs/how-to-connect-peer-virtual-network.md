---
title: Azure Lab Services |에서 피어 네트워크에 연결 Microsoft Docs
description: 다른 네트워크와 피어로 랩 네트워크를 연결 하는 방법에 대해 알아봅니다. 예를 들어 Azure에서 랩의 가상 네트워크와 온-프레미스 school/대학 네트워크를 연결 합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701678"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>랩의 네트워크를 피어 가상 네트워크와 연결 Azure Lab Services 
이 문서에서는 랩 네트워크를 다른 네트워크로 피어 링 하는 방법에 대 한 정보를 제공 합니다. 

## <a name="overview"></a>개요
가상 네트워크 피어 링을 사용 하면 Azure 가상 네트워크를 원활 하 게 연결할 수 있습니다. 가상 네트워크가 피어링되면 연결성을 위해 하나로 표시됩니다. 피어 링 가상 네트워크의 가상 머신 간 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 즉, 트래픽은 동일한 가상 네트워크의 가상 머신 간에 개인 IP 주소를 통해서만 라우팅됩니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

다음을 포함 하 여 몇 가지 시나리오에서 랩의 네트워크를 피어 가상 네트워크에 연결 해야 할 수 있습니다.

- 랩의 가상 컴퓨터에는 라이선스를 획득 하기 위해 온-프레미스 라이선스 서버에 연결 하는 소프트웨어가 있습니다.
- 랩의 가상 머신은 대학 네트워크 공유의 데이터 집합 (또는 다른 파일)에 액세스 해야 합니다. 

특정 온-프레미스 네트워크는 [express](../../expressroute/expressroute-introduction.md) 경로 또는 [Virtual Network 게이트웨이](../../vpn-gateway/vpn-gateway-about-vpngateways.md)를 통해 Azure Virtual Network에 연결 됩니다. 이러한 서비스는 Azure Lab Services 외부에서 설정 해야 합니다. Express 경로를 사용 하 여 온-프레미스 네트워크를 Azure에 연결 하는 방법에 대해 자세히 알아보려면 [express 경로 개요](../../expressroute/expressroute-introduction.md)를 참조 하세요. Virtual Network 게이트웨이를 사용 하는 온-프레미스 연결의 경우 게이트웨이, 지정 된 가상 네트워크 및 랩 계정이 모두 동일한 지역에 있어야 합니다.

## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 생성 시 구성
새 랩 계정을 만드는 동안 **피어 가상 네트워크** 드롭다운 목록에 표시 되는 기존 가상 네트워크를 선택할 수 있습니다. 선택한 가상 네트워크는 랩 계정으로 만든 labs에 연결 (피어 링) 됩니다. 이 변경 작업을 수행한 후 생성 된 랩에서 모든 가상 머신은 피어 링 가상 네트워크의 리소스에 액세스할 수 있습니다. 

![VNet에서 피어를 선택 합니다.](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 랩 계정 만들기에 대 한 자세한 단계별 지침은 [랩 계정 설정](tutorial-setup-lab-account.md) 을 참조 하세요.


## <a name="configure-after-the-lab-is-created"></a>랩을 만든 후 구성
랩 계정 생성 시 피어 네트워크를 설정 하지 않은 경우 **랩 계정** 페이지의 랩 **구성** 탭에서 동일한 속성을 사용할 수 있습니다. 이 설정에 대 한 변경 내용은 변경 후에 생성 된 랩에서만 적용 됩니다. 이미지에서 볼 수 있듯이 랩 계정에서 랩에 대 한 **피어 가상 네트워크** 를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 

![랩을 만든 후 VNet 피어 링 사용 또는 사용 안 함](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

**피어 가상 네트워크** 필드의 가상 네트워크를 선택 하는 경우 **랩 작성자가 랩 위치를 선택할 수 있음** 옵션을 사용할 수 없습니다. 랩 계정의 랩은 피어 가상 네트워크의 리소스와 연결 하기 위해 랩 계정과 동일한 지역에 있어야 하기 때문입니다. 

> [!IMPORTANT]
> 이 설정 변경은 변경 후에 생성 된 랩에서만 적용 되며 기존 랩에는 적용 되지 않습니다. 


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)

