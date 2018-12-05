---
title: Azure Lab Services의 랩 계정 관리 | Microsoft Docs
description: Azure 구독에서 랩 계정을 만들거나 랩 계정을 모두 보거나 랩 계정을 삭제하는 방법을 알아봅니다.
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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: c672634e79cbc0850edfe7f7fbdb0a880d5cf0d8
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707132"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Azure Lab Services의 랩 계정 관리 
Azure Lab Services에서 랩 계정은 강의실 랩과 같은 관리되는 랩의 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 이 문서는 랩 계정을 만들거나, 모든 랩 계정을 보거나, 랩 계정을 삭제하는 방법을 설명합니다.

## <a name="create-a-lab-account"></a>랩 계정 만들기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에 있는 주 메뉴에서 **리소스 만들기**를 선택합니다.
3. Azure Marketplace에서 **Lab Services**를 검색하고 드롭다운 목록에서 **Lab Services**를 선택합니다. 
4. 필터링된 서비스 목록에서 **Lab Services(미리 보기)** 를 선택합니다. 
5. **랩 계정 만들기** 창에서 **만들기**를 선택합니다.
7. **랩 계정** 창에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름**에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독**을 선택합니다.
    3. **리소스 그룹**의 경우 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치**에서 랩 계정을 만들 위치/지역을 선택합니다. 
    5. **만들기**를 선택합니다. 

        ![랩 계정 만들기 창](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. 랩 계정에 대한 페이지가 표시되지 않으면 **알림** 단추를 선택한 다음, 알림에서 **리소스로 이동** 단추를 클릭합니다. 

    ![랩 계정 만들기 창](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. 다음과 같은 **랩 계정** 페이지가 표시됩니다.

    ![랩 계정 페이지](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고 도구 모음에서 **+ 추가**를 클릭합니다. 

    ![랩 계정 페이지](../media/tutorial-setup-lab-account/access-control.png)
2. **권한 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 역할에 사용자 추가](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>랩 소유자에 사용할 수 있는 Marketplace 이미지 지정
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

## <a name="view-lab-accounts"></a>랩 계정 보기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 메뉴에서 **모든 리소스**를 선택합니다. 
3. **형식**에 대한 **랩 서비스**를 선택합니다. 
    구독, 리소스 그룹, 위치 및 태그별로 필터링할 수도 있습니다. 

## <a name="delete-a-lab-account"></a>랩 계정 삭제
랩 계정을 목록으로 표시하는 이전 섹션의 지침을 따릅니다. 다음 지침에 따라 랩 계정을 삭제합니다. 

1. 삭제하려는 **랩 계정**을 선택합니다. 
2. 도구 모음에서 **삭제**를 선택합니다. 
3. 확인을 위해 **예**를 입력합니다.
4. **삭제**를 선택합니다. 

## <a name="view-and-manage-labs-in-the-lab-account"></a>랩 계정의 랩 보기 및 관리

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩**을 선택합니다.

    ![계정의 랩](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. 계정에는 다음 정보가 있는 **랩** 목록이 표시됩니다. 
    1. 랩의 이름
    2. 랩을 만든 날짜 
    3. 랩을 만든 사용자의 메일 주소 
    4. 랩에 허용되는 최대 사용자 수 
    5. 랩 상태 

## <a name="delete-a-lab-in-the-lab-account"></a>랩 계정에서 랩 삭제
이전 섹션의 지침에 따라 랩 계정의 랩 목록을 표시합니다.

1. **...(줄임표)** 를 선택하고 **삭제**를 선택합니다. 

    ![랩 삭제 - 단추](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. 경고 메시지에서 **예**를 선택합니다. 



## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)