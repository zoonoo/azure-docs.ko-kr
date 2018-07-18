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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 6039ea482b0968d48fc21ff3dfec82a2ff0db43d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715329"
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
강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 랩 작성자 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고 도구 모음에서 **+ 추가**를 클릭합니다. 

    ![랩 계정 페이지](../media/tutorial-setup-lab-account/access-control.png)
2. **권한 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 역할에 사용자 추가](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


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

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 랩 설정 시작합니다.

- [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)
- [랩 설정](../tutorial-create-custom-lab.md)
