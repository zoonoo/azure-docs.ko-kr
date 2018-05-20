---
title: Azure Lab Services로 랩 계정 설정 | Microsoft Docs
description: 이 자습서에서는 Azure Lab Services(이전의 DevTest Labs)로 랩 계정을 설정합니다.
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
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 5af348bfdccb9392948af962cf582e33ebd40872
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>자습서: Azure Lab Services(이전 Azure DevTest Labs)로 랩 계정 설정
이 자습서에서는 랩 관리자 역할을 하여 Azure Lab Services로 랩 계정을 만듭니다. 그런 다음 교사에게 이 랩 계정에서 클래스를 위한 랩을 만들 수 있는 권한을 제공합니다. 교육자는 [Azure Lab Services 웹 사이트](https://labs.azure.com)를 사용하여 클래스를 위해 랩을 설정할 수 있습니다.   

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩 계정 만들기
> * 랩 작성자 역할에 사용자 추가

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure Lab Services는 현재 제어된 미리 보기 상태입니다. 랩 계정을 만들려면 [미리 보기에 등록](https://aka.ms/azlabspreviewsignup)하세요.

## <a name="create-a-lab-account"></a>랩 계정 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs로 랩 계정을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽의 주 메뉴에서 **리소스 만들기**(목록 맨 위에 있음)를 선택한 후 **개발자 도구**를 가리키고 **랩 서비스(미리 보기)** 를 클릭합니다.
1. **랩 계정 만들기** 창에서 **만들기**를 선택합니다.
2. **랩 계정** 창에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름**에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독**을 선택합니다.
    3. **리소스 그룹**의 경우 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치**에서 랩 계정을 만들 위치/지역을 선택합니다. 
    5. **만들기**를 선택합니다. 

        ![랩 계정 만들기 창](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. 랩 계정에 대한 페이지가 표시되지 않으면 **알림** 단추를 선택한 다음, 알림에서 **리소스로 이동** 단추를 클릭합니다. 

    ![랩 계정 만들기 창](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. 다음과 같은 **랩 계정** 페이지가 표시됩니다.

    ![랩 계정 페이지](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 랩 작성자 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고 도구 모음에서 **+ 추가**를 클릭합니다. 

    ![랩 계정 페이지](./media/tutorial-setup-lab-account/access-control.png)
2. **권한 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 역할에 사용자 추가](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 랩 계정을 만들었습니다. 직업으로 클래스룸 랩을 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)

