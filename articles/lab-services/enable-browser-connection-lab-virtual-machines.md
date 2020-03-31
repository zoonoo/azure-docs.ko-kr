---
title: Azure DevTest Labs 가상 컴퓨터에서 브라우저 연결 활성화 | 마이크로 소프트 문서
description: 이제 DevTest Labs는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스하도록 설정할 수 있는 랩 소유자로서 Azure Bastion과 통합됩니다.
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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970787"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>랩 가상 컴퓨터에서 브라우저 연결 사용 
DevTest Labs는 브라우저를 통해 가상 컴퓨터에 연결할 수 있는 [Azure Bastion과](https://docs.microsoft.com/azure/bastion/)통합됩니다. 먼저 랩 가상 컴퓨터에서 브라우저 연결을 사용하도록 설정해야 합니다.

랩의 소유자는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스하도록 설정할 수 있습니다. 다른 클라이언트, 에이전트 또는 소프트웨어가 필요하지 않습니다. Azure Bastion은 SSL을 통해 Azure 포털에서 직접 가상 시스템에 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion을 통해 연결할 때 가상 컴퓨터에 공용 IP 주소가 필요하지 않습니다. 자세한 내용은 [Azure 요새란 무엇입니까?](../bastion/bastion-overview.md)


이 문서에서는 랩 가상 컴퓨터에서 브라우저 연결을 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항 
기존 랩의 가상 **네트워크(OR)에** Bastion 호스트를 배포하면 Bastion이 구성된 가상 네트워크와 랩을 연결합니다. 

가상 네트워크에서 Bastion 호스트를 배포하는 방법에 대해 알아보려면 [Azure 요새 호스트 만들기를](../bastion/bastion-create-host-portal.md)참조하십시오. Bastion 호스트를 만들 때 랩의 가상 네트워크를 선택합니다. 

첫째, AzureBastionSubnet에서 비 바스티온 리소스를 만들 수 없기 때문에 Bastion 가상 네트워크에서 두 번째 서브넷을 만들어야 합니다. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Bastion 가상 네트워크에서 두 번째 하위 그물 만들기
Azure 요새 서브넷에서는 랩 VM을 만들 수 없습니다. Bastion 가상 네트워크 내에서 다음 이미지와 같이 다른 서브넷을 만듭니다.

![Azure 요새 가상 네트워크의 두 번째 서브넷](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>서브넷에서 VM 생성 사용
이제 다음 단계를 수행하여 이 서브넷에서 VM을 만들 수 있습니다. 

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스를** 선택합니다. 
1. 목록에서 **개발자 테스트 랩을 선택합니다.** 
1. 랩 목록에서 *랩을 선택합니다.* 

    > [!NOTE]
    > Azure 요새는 이제 미국 서부, 미국 동부, 유럽 서부, 미국 중남부, 오스트레일리아 동부 및 일본 동부 지역에서 일반적으로 사용할 수 있습니다. 따라서 랩이 해당 지역 중 하나에 없는 경우 이러한 지역 중 하나에 랩을 만듭니다. 
    
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책을** 선택합니다. 
1. **가상 네트워크를**선택합니다.
1. 도구 모음에서 **추가를** 선택합니다. 
1. Bastion 호스트가 배포된 **가상 네트워크를** 선택합니다. 
1. 이전에 만든 다른 **AzureBastionSubnet이**아닌 VM에 대한 서브넷을 선택합니다. 하단에 있는 목록에 서브넷이 표시되지 않으면 페이지를 닫고 다시 엽니다. 

    ![서브넷에서 VM 생성 사용](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. **가상 컴퓨터 생성 옵션에서 사용을** 선택합니다. 
1. 도구 모음에서 **저장**을 선택합니다. 
1. 랩에 대한 오래된 가상 네트워크가 있는 경우 * 을 선택하여*제거하십시오.*  및 **제거합니다.** 

## <a name="enable-browser-connection"></a>브라우저 연결 사용 

실험실 소유자로서 Bastion이 구성된 가상 네트워크가 있으면 랩 가상 컴퓨터에서 브라우저 연결을 활성화할 수 있습니다.

랩 가상 컴퓨터에서 브라우저 연결을 사용하려면 다음 단계를 따르십시오.

1. Azure 포털에서 *랩으로 이동합니다.*
1. **구성 및 정책**을 선택합니다.
1. **설정에서** **브라우저 연결을 선택합니다.** 이 옵션이 표시되지 않으면 **구성 정책** 페이지를 닫고 다시 엽니다. 

    ![브라우저 연결 사용](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>다음 단계
브라우저를 사용하여 VM에 연결하는 방법을 알아보려면 다음 문서를 참조하세요: [브라우저를 통해 가상 컴퓨터에 연결](connect-virtual-machine-through-browser.md)
