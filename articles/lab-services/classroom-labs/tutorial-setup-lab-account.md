---
title: Azure Lab Services로 랩 계정 설정 | Microsoft Docs
description: 이 자습서에서는 Azure Lab Services로 랩 계정을 설정합니다.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2019
ms.author: spelluru
ms.openlocfilehash: 0977c4537e409b59be7f9031c488b3317f9f2f0f
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415788"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>자습서: Azure Lab Services로 랩 계정 설정
Azure Lab Services에서 랩 계정은 조직의 랩을 관리하는 중앙 계정 역할을 합니다. 랩 계정에서 랩을 만들고 랩 계정의 모든 랩에 적용되는 정책을 설정할 수 있는 권한을 다른 사람에게 부여하세요. 이 자습서에서는 랩 관리자 권한으로 랩 계정을 만드는 방법을 알아봅니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩 계정 만들기
> * 랩 작성자 역할에 사용자 추가
> * 랩 소유자에 사용할 수 있는 Marketplace 이미지 지정

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-a-lab-account"></a>랩 계정 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs로 랩 계정을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **DEVOPS** 섹션에서 **랩 서비스**를 선택합니다. **랩 서비스** 옆에 있는 별표(`*`)를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스**를 선택합니다.

    ![모든 서비스 -> 랩 서비스](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **랩 서비스** 페이지에서 도구 모음에 있는 **추가**를 선택합니다. 

    ![랩 계정 페이지에서 추가 선택](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **랩 계정** 페이지에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름**에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독**을 선택합니다.
    3. **리소스 그룹**의 경우 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치**에서 랩 계정을 만들 위치/지역을 선택합니다. 
    5. 기존 **공유 이미지 갤러리**를 선택하거나 새로 만듭니다. 다른 사용자가 다시 사용할 수 있도록 템플릿 VM을 공유 이미지 갤러리에 저장할 수 있습니다. 공유 이미지 갤러리에 대한 자세한 내용은 [Azure Lab Services에서 공유 이미지 갤러리 사용](how-to-use-shared-image-gallery.md)을 참조하세요. 
    6. **가상 네트워크 피어 지정**에서 랩 네트워크에 대한 VNet(가상 네트워크) 피어 지정을 선택합니다. 이 계정에서 생성된 랩은 선택한 VNet에 연결되어 있으며 선택한 VNet의 리소스에 액세스할 수 있습니다. 
    7. 랩의 VM에 대해 **주소 범위**를 지정합니다. 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법을 따라야 합니다(예: 10.20.0.0/23). 랩의 가상 머신은 이 주소 범위에서 만들어집니다. 자세한 내용은 [랩의 VM에 대해 주소 범위 지정](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)을 참조하세요.
    8. **랩 작성자가 랩 위치를 선택하도록 허용** 필드에서 랩 작성자가 랩의 위치를 선택할 수 있도록 할지 여부를 지정합니다. 이 옵션은 기본적으로 '사용 안 함'으로 설정되어 있습니다. 이 기능이 '사용 안 함'으로 설정되면 랩 작성자가 만들고 있는 랩의 위치를 지정할 수 없습니다. 랩은 랩 계정과 가장 가까운 지리적 위치에 만들어집니다. 이 기능이 '사용'으로 설정되면 랩 작성자가 랩을 만들 때 위치를 선택할 수 있습니다. 
    9. **만들기**를 선택합니다. 

        ![랩 계정 만들기 창](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 도구 모음의 **종 모양 아이콘**(**알림**)을 선택하고 배포가 성공했는지 확인한 후 **리소스로 이동**을 선택합니다. 

    또는 **랩 계정** 페이지에서 **새로 고침**을 선택하고 생성된 랩 계정을 선택합니다. 

    ![랩 계정 만들기 창](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 다음과 같은 **랩 계정** 페이지가 표시됩니다.

    ![랩 계정 페이지](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 추가**, **+ 역할 할당 추가**를 차례로 선택합니다. 

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
이 자습서에서는 랩 계정을 만들었습니다. 직업으로 클래스룸 랩을 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)

