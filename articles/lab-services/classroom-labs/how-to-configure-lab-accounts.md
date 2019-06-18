---
title: Azure Lab Services의 랩 계정 구성 | Microsoft Docs
description: 만든 후 랩 계정을 구성 하는 방법에 알아봅니다.
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
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65414048"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Azure Lab Services의 랩 계정 구성 
Azure Lab Services의 랩 계정은 클래스 룸 랩과 같은 관리 되는 랩 형식에 대 한 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 이 문서는 랩 계정을 만들거나, 모든 랩 계정을 보거나, 랩 계정을 삭제하는 방법을 설명합니다.

## <a name="connect-with-a-peer-virtual-network"></a>피어 가상 네트워크를 사용 하 여 연결
랩의 가상 네트워크에 피어 네트워크로 가상 네트워크에 연결 하려면 다음이 단계를 수행 합니다.

1. 에 **랩 계정을** 페이지에서 **Labs 구성** 왼쪽된 메뉴에서.

    ![랩 구성 페이지](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 에 대 한 **피어 가상 네트워크**를 선택 **Enabled** 또는 **비활성**합니다. 기본값은 **사용 안 함**합니다. 피어 가상 네트워크를 사용 하려면 다음 단계를 수행 합니다. 
    1. **사용**을 선택합니다.
    2. 선택 된 **VNet** 드롭 다운 목록에서. 
3. 도구 모음에서 **저장**을 선택합니다. 

이 계정에 만든 랩 선택한 가상 네트워크에 연결 됩니다. 선택한 가상 네트워크의 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Lab Services의 피어 가상 네트워크를 사용 하 여 랩의 네트워크 연결](how-to-connect-peer-virtual-network.md)합니다.

에 대 한 가상 네트워크를 선택 하는 경우는 **피어 가상 네트워크** 필드를 **랩 위치를 선택 하도록 허용 랩 작성자** 옵션이 비활성화 됩니다. 실습 랩 계정에는 피어 가상 네트워크의 리소스를 사용 하 여 연결 하려면 랩 계정으로 동일한 지역에 있어야 합니다. 때문입니다. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>랩 작성자 랩에 대 한 위치를 선택 하도록 허용
다음이 단계를 수행 하 여 랩 계정 위치와 다른 위치에서 랩을 만들고 랩 작성자를 허용할 수 있습니다. 

1. 에 **랩 계정을** 페이지에서 **Labs 구성** 왼쪽된 메뉴에서.
2. 에 대 한는 **랩 위치를 선택 하도록 허용 랩 작성자**를 선택 **Enabled** 랩 작성자 랩에 대 한 위치를 선택할 수 있게 되기를 원하는 경우. 비활성화 된 실습 랩 계정을 존재 하는 동일한 위치에 자동으로 만들어집니다. 
    
    에 대 한 가상 네트워크를 선택 하면이 필드가 비활성화 됩니다 합니다 **피어 가상 네트워크** 필드입니다. 실습 랩 계정에는 피어 가상 네트워크의 리소스에 액세스 하기 위해 랩 계정으로 동일한 지역에 있어야 합니다. 때문입니다. 
1. 도구 모음에서 **저장**을 선택합니다. 

    ![랩 위치 설정 구성](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>랩의 Vm에 대 한 주소 범위를 지정
다음 절차는 랩의 Vm에 대 한 주소 범위를 지정 하는 단계가 있습니다. 이전에 지정한 범위를 업데이트 하는 경우 수정 된 주소 범위를 변경한 후 만들어지는 Vm에만 적용 됩니다. 

유념 해야 하는 주소 범위를 지정 하는 경우 몇 가지 제한 사항은 다음과 같습니다. 

- 보다 작거나 23 접두사 여야 합니다. 
- 랩 계정에 가상 네트워크 피어 링은 제공 된 주소 범위는 피어 링 된 가상 네트워크에서 주소 범위와 겹칠 수 없습니다.

1. 에 **랩 계정을** 페이지에서 **Labs 구성** 왼쪽된 메뉴에서.
2. 에 대 한 합니다 **주소 범위** 필드에서 랩에 만들어지는 Vm에 대 한 주소 범위를 지정 합니다. 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법을 따라야 합니다(예: 10.20.0.0/23). 랩의 가상 머신은 이 주소 범위에서 만들어집니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![주소 범위를 구성 합니다.](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 역할 할당 추가**를 클릭합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>랩 작성자에 사용할 수 있는 Marketplace 이미지 지정
랩 계정 소유자는 랩 작성자가 랩 계정에서 랩을 만들 때 사용할 수 있는 Marketplace 이미지를 지정할 수 있습니다. 

1. 왼쪽 메뉴에서 **Marketplace 이미지**를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(사용 및 사용 안 함으로 설정된 이미지 모두 포함). 맨 위의 드롭다운 목록에서 **Enabled only**(사용으로 설정된 이미지만)/**Disabled only**(사용 안 함으로 설정된 이미지만) 옵션을 선택하여 사용/사용 안 함으로 설정된 이미지만 표시하도록 목록을 필터링할 수 있습니다. 
    
    ![Marketplace 이미지 페이지](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    다음 조건을 만족하는 Marketplace 이미지만 목록에 표시됩니다.
        
    - 단일 VM을 만듭니다.
    - Azure Resource Manager를 사용하여 VM을 프로비전합니다.
    - 추가 라이선스 플랜을 구입하지 않아도 됩니다.
2. 사용하도록 설정된 Marketplace 이미지를 **사용하지 않도록 설정**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 사용 안 함**을 선택합니다. 

        ![하나의 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 사용 안 함**을 선택합니다. 

        ![여러 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 마찬가지로 Marketplace 이미지를 **사용하도록 설정**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 사용**을 선택합니다. 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 사용**을 선택합니다. 




## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
