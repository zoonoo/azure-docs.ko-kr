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
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642488"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>랩 가상 컴퓨터에서 브라우저 연결 사용 

DevTest Labs는 브라우저를 통해 가상 컴퓨터에 연결할 수 있도록 하는 [Azure 방호](https://docs.microsoft.com/azure/bastion/)와 통합 됩니다. 먼저 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 해야 합니다.

랩의 소유자는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스 하도록 설정할 수 있습니다. 다른 클라이언트, 에이전트 또는 소프트웨어가 필요하지 않습니다. Azure 방호는 SSL을 통해 Azure Portal에서 직접 가상 머신에 안전 하 고 원활한 RDP/SSH 연결을 제공 합니다. Azure 방호를 통해 연결 하는 경우 가상 머신에 공용 IP 주소가 필요 하지 않습니다. 자세한 내용은 [Azure 방호 이란?](../bastion/bastion-overview.md) 을 참조 하세요.

> [!NOTE]
> 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 하는 것이 미리 보기 상태입니다.

이 문서에서는 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소 
기존 랩의 가상 네트워크에 요새 호스트를 배포 **하거나,** 해당 랩에 방호 구성 VNet을 연결 합니다. 

VNet에 요새 호스트를 배포 하는 방법에 대 한 자세한 내용은 [Azure 방호 호스트 만들기 (미리 보기)](../bastion/bastion-create-host-portal.md)를 참조 하세요. 요새 호스트를 만들 때 랩의 가상 네트워크를 선택 합니다. 

랩을 구성 된 VNet에 연결 하는 방법을 알아보려면 [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)을 참조 하세요. 요새 호스트를 배포한 VNet 및 **AzureBastionSubnet** 를 선택 합니다. 세부 단계는 다음과 같습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택 합니다. 
1. 목록에서 **DevTest Labs** 를 선택 합니다. 
1. 랩 목록에서 *랩을*선택 합니다. 

    > [!NOTE]
    > Azure 방호는 현재 미리 보기 상태입니다. 다음 지역으로 제한 됩니다. 미국 서 부, 미국 동부, 유럽 서부, 남부 중부 미국, 오스트레일리아 동부 및 일본 동부. 따라서 랩을 이러한 지역 중 하나에 없는 경우 이러한 지역 중 하나에서 랩을 만듭니다. 
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택 합니다. 
1. **가상 네트워크**를 선택 합니다.
1. 도구 모음에서 **추가** 를 선택 합니다. 
1. 요새 호스트가 배포 된 **VNet** 을 선택 합니다. 
1. 서브넷 선택: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
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
브라우저를 사용 하 여 Vm에 연결 하는 방법을 알아보려면 다음 문서를 참조 하세요. [브라우저를 통해 가상 컴퓨터에 연결](connect-virtual-machine-through-browser.md)