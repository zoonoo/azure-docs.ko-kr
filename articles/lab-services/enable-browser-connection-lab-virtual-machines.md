---
title: Azure DevTest Labs virtual machines에서 브라우저 연결 사용 | Microsoft Docs
description: DevTest Labs는 이제 Azure 방호와 통합 됩니다. 랩의 소유자는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스 하도록 설정할 수 있습니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 080dd91b2ab6792debfae3a3ccc97b0927015de4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580144"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>랩 가상 컴퓨터에서 브라우저 연결 사용 

DevTest Labs는 브라우저를 통해 가상 컴퓨터에 연결할 수 있도록 하는 [Azure 방호](https://docs.microsoft.com/azure/bastion/)와 통합 됩니다. 먼저 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 해야 합니다.

랩의 소유자는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스 하도록 설정할 수 있습니다. 다른 클라이언트, 에이전트 또는 소프트웨어가 필요하지 않습니다. Azure Bastion은 SSL을 통해 Azure Portal에서 가상 머신에 대한 안전하고 원활한 RDP/SSH 연결을 직접 제공합니다. Azure 방호를 통해 연결 하는 경우 가상 머신에 공용 IP 주소가 필요 하지 않습니다. 자세한 내용은 [Azure 방호 이란?](../bastion/bastion-overview.md) 을 참조 하세요.


이 문서에서는 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건 
기존 랩의 가상 네트워크에 요새 호스트를 배포 **하거나,** 해당 랩에 방호 구성 VNet을 연결 합니다. 

VNet에 요새 호스트를 배포 하는 방법에 대 한 자세한 내용은 [Azure 방호 호스트 만들기 (미리 보기)](../bastion/bastion-create-host-portal.md)를 참조 하세요. 요새 호스트를 만들 때 랩의 가상 네트워크를 선택 합니다. 

랩을 구성 된 VNet에 연결 하는 방법을 알아보려면 [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)을 참조 하세요. 요새 호스트를 배포한 VNet 및 **AzureBastionSubnet** 를 선택 합니다. 세부 단계는 다음과 같습니다. 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택 합니다. 
1. 목록에서 **DevTest Labs** 를 선택 합니다. 
1. 랩 목록에서 *랩을*선택 합니다. 

    > [!NOTE]
    > Azure 방호는 현재 미국 서 부, 미국 동부, 유럽 서부, 미국 중부 중부, 오스트레일리아 동부 및 일본 동부 지역에서 일반적으로 사용할 수 있습니다. 따라서 랩을 이러한 지역 중 하나에 없는 경우 이러한 지역 중 하나에서 랩을 만듭니다. 
    
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택 합니다. 
1. **가상 네트워크**를 선택 합니다.
1. 도구 모음에서 **추가** 를 선택 합니다. 
1. 요새 호스트가 배포 된 **VNet** 을 선택 합니다. 
1. 서브넷: **AzureBastionSubnet**을 선택 합니다. 

    ![서브넷](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. **가상 컴퓨터 만들기 옵션에서 사용을** 선택 합니다. 
1. 도구 모음에서 **저장**을 선택합니다. 
1. 랩에 대 한 이전 VNet이 있는 경우 * *...* 를 선택 하 여 제거 합니다.  **제거**합니다. 

## <a name="enable-browser-connection"></a>브라우저 연결 사용 

랩 내에서 요새를 구성 하는 경우 랩 소유자는 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정할 수 있습니다.

랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 *랩*으로 이동 합니다.
1. **구성 및 정책**을 선택합니다.
1. **설정**에서 **브라우저 연결 (미리 보기)** 을 선택 합니다.

    ![브라우저 연결 사용](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>다음 단계
브라우저를 사용 하 여 Vm에 연결 하는 방법을 알아보려면 브라우저를 [통해 가상 머신에](connect-virtual-machine-through-browser.md) 연결 하는 방법을 알아보려면 다음 문서를 참조 하세요.
