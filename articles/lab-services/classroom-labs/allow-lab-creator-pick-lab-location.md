---
title: 랩 작성자가 Azure 랩 서비스에서 위치를 선택할 수 있도록 허용
description: 이 문서에서는 랩 계정 관리자가 랩 작성자가 랩의 위치를 선택할 수 있도록 허용하는 방법에 대해 설명합니다.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444369"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>랩 작성자가 Azure 랩 서비스에서 랩의 위치를 선택할 수 있도록 허용
Azure Lab Services에서 랩 계정 소유자는 랩 작성자(교육자)가 만든 랩의 위치를 선택할 수 있도록 허용할 수 있습니다. 이 위치는 랩 계정의 위치와 다를 수 있습니다. 위치는 Azure 지역의 그룹입니다. 예를 들어 미국 위치는 미국 동부, 미국 서부 등과 같은 지역 그룹입니다. 

랩 계정 소유자는 랩 계정을 만들 때와 랩 계정(또는 기존 랩 계정)을 만든 후 랩 위치 옵션을 선택할 수 있도록 랩 **작성자(허용** 랩 작성자)를 선택할 수 있습니다. 

## <a name="at-the-time-of-lab-account-creation"></a>랩 계정 생성 시
랩 계정을 만들 때 첫 번째**화면(기본** 탭)에 이 옵션이 표시됩니다. 

![실험실 생성 시 옵션 사용](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

**고급** 탭에서 랩 계정에 대한 피어 가상 네트워크를 선택하면 이 옵션을 사용할 수 없습니다.  

![피어 가상 네트워크가 활성화된 경우](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>랩 계정을 만든 후
랩 계정을 만든 후 다음 단계에 따라 이 옵션을 사용하거나 사용하지 않도록 설정할 수 있습니다. 

1. 랩 **계정** 페이지에서 왼쪽 메뉴에서 **랩 설정을** 선택합니다.
2. 랩 작성자가 랩의 위치를 선택할 수 있도록 허용하려면 랩 **작성자 허용을 선택하여** 랩 위치 옵션을 선택합니다. 비활성화된 경우 랩 계정이 있는 동일한 위치에 랩이 자동으로 만들어집니다. 
    
    Peer 가상 네트워크 필드에 대한 가상 네트워크를 선택하면 이 필드가 **비활성화됩니다.** 랩 계정의 랩이 피어 가상 네트워크의 리소스에 액세스하려면 랩 계정과 동일한 지역에 있어야 하기 때문입니다. 
1. 도구 모음에서 **저장**을 선택합니다. 

    ![랩 설정](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>가상 네트워크 및 위치 선택이 허용되지 않습니다.
이 시나리오에서는 **랩 작성자가 랩 위치** 옵션을 선택하도록 설정하지 않았습니다. 

![랩 위치 없음](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

그런 다음 랩 작성자(교육자)는 랩의 위치를 선택하는 옵션이 표시되지 않습니다. 그들은 그들에 게 사용할 수 있는 모든 크기 옵션에 대 한 시간 당 가격을 볼 것 이다. 랩을 만들 면 랩 계정이 있는 Azure 지역과 동일한 위치에 있는 Azure 리전에서 만들어집니다. 예를 들어 랩 계정이 **미국 서부에**있는 경우 미국 **중남부에서** 랩을 만들 수 있지만 **캐나다 동부에서는**만들지 않을 수 있습니다. 우리는 우리가 그 위치에 있는 것을 제외하고 우리가 선택하는 지역에 관하여 아무것도 보장하지 않습니다. 크기가 현재 제한되어 있는 경우 랩 작성자는 일반적으로 지원하지만 현재 사용할 수 없는 크기를 볼 수 있는 확인란을 볼 수 있습니다. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>가상 네트워크에서 위치 선택이 허용되지 않습니다.
이 시나리오에서는 **랩** 계정에 대 한 피어 가상 네트워크를 선택 했기 때문에 랩 위치 옵션을 선택 하도록 허용 랩 작성자가 사용 되지 않습니다. 그런 다음 랩 제작자는 이전 옵션과 동일한 화면을 볼 수 있습니다. 모든 VM은 가상 네트워크와 동일한 Azure 지역에 있어야 하므로 랩은 가상 네트워크가 있는 동일한 Azure 리전에서 만들어집니다. 해당 특정 영역이 크기에 대해 구속되면 크기를 사용할 수 없는 것으로 나타납니다. 

## <a name="location-selection-is-enabled"></a>위치 선택이 활성화되어 있습니다.
**랩 작성자 허용을 선택하여 랩 위치를 선택하면**랩 작성자(교육자)가 랩을 만들 때 위치를 선택하는 옵션이 표시됩니다. 

![랩 위치 선택](../media/allow-lab-creator-pick-lab-location/location-selection.png)

랩 작성자는 크기가 있는 모든 위치의 가격 범위를 확인하고 위치를 선택할 수 있습니다. 랩은 해당 위치에 매핑되는 모든 Azure 지역에서 만들어집니다.

위치가 제한된 경우 기본적으로 목록에 표시되지 않습니다. 드롭다운 목록을 확장하고 이 **크기에 사용할 수 없는 위치 표시를**선택합니다. 

![사용할 수 없는 위치 표시](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>비용
이전 가격 책정은 랩에 대해 선택한 VM 크기를 기반으로 했습니다. 이제 가격은 운영 체제 (OS), 크기 및 위치의 조합을 기반으로합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 네트워크를 피어 가상 네트워크로 연결](how-to-connect-peer-virtual-network.md)
- [랩에 공유 이미지 갤러리 첨부](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩의 방화벽 설정 보기](how-to-configure-firewall-settings.md)
- [랩에 대한 다른 설정 구성](how-to-configure-lab-accounts.md)