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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: d4bfd684792e5ec13b2a4a020fa21249f1888657
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226354"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>자습서: Azure Lab Services로 랩 계정 설정
Azure Lab Services에서 랩 계정은 조직의 랩을 관리하는 중앙 계정 역할을 합니다. 랩 계정에서 랩을 만들고 랩 계정의 모든 랩에 적용되는 정책을 설정할 수 있는 권한을 다른 사람에게 부여하세요. 이 자습서에서는 랩 관리자 권한으로 랩 계정을 만드는 방법을 알아봅니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩 계정 만들기
> * 랩 작성자 역할에 사용자 추가
> * 랩 소유자에 사용할 수 있는 Marketplace 이미지 지정

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-a-lab-account"></a>랩 계정 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs로 랩 계정을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에 있는 주 메뉴에서 **리소스 만들기**를 선택합니다.
3. Azure Marketplace에서 **Lab Services**를 검색하고 드롭다운 목록에서 **Lab Services**를 선택합니다. 
4. 필터링된 서비스 목록에서 **Lab Services(미리 보기)** 를 선택합니다. 
1. **랩 계정 만들기** 창에서 **만들기**를 선택합니다.
2. **랩 계정** 창에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름**에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독**을 선택합니다.
    3. **리소스 그룹**의 경우 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치**에서 랩 계정을 만들 위치/지역을 선택합니다. 
    5. **만들기**를 선택합니다. 

        ![랩 계정 만들기 창](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 랩 계정에 대한 페이지가 표시되지 않으면 **알림** 단추를 선택한 다음, 알림에서 **리소스로 이동** 단추를 클릭합니다. 

    ![랩 계정 만들기 창](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. 다음과 같은 **랩 계정** 페이지가 표시됩니다.

    ![랩 계정 페이지](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고 도구 모음에서 **+ 추가**를 클릭합니다. 

    ![랩 계정 페이지](../media/tutorial-setup-lab-account/access-control.png)
2. **권한 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 역할에 사용자 추가](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>랩 소유자에 사용할 수 있는 Marketplace 이미지 지정
이 세션에서는 랩 소유자가 클래스룸 랩을 만드는 데 사용할 수 있는 Marketplace 이미지를 지정합니다. 

1. 왼쪽 메뉴에서 **Marketplace 이미지**를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(둘 다 활성화 및 비활성화됨). 맨 위의 드롭다운 목록에서 **활성화만**/**비활성화만** 옵션을 선택하여 활성화된/비활성화된 이미지만 표시하도록 목록을 필터링할 수 있습니다. 

    ![Marketplace 이미지 페이지](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. 활성화된 Marketplace 이미지를 **비활성화**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 비활성화**를 선택합니다. 

        ![하나의 이미지 비활성화](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 비활성화**를 선택합니다. 

        ![여러 이미지 비활성화](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 유사하게, Marketplace 이미지를 **활성화**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 활성화**를 선택합니다. 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 활성화**를 선택합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 랩 계정을 만들었습니다. 직업으로 클래스룸 랩을 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)

