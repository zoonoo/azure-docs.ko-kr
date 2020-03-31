---
title: Azure 랩 서비스의 랩에 추가 소유자를 추가 하는 방법
description: 이 문서에서는 관리자가 Azure Lab Services의 랩에 사용자를 소유자로 추가할 수 있는 방법을 보여 주었습니다.
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
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443519"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Azure 랩 서비스의 기존 랩에 추가 소유자를 추가 하는 방법
이 문서에서는 관리자로서 기존 랩에 추가 소유자를 추가하는 방법을 보여 주며, 이를 보여 주실 수 있습니다.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>랩 계정의 판독기 역할에 사용자 추가
사용자를 기존 랩에 추가 소유자로 추가하려면 먼저 사용자에게 랩 계정에 대한 **읽기** 권한을 부여해야 합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. 랩 **서비스를**검색한 다음 선택합니다.
3. 목록에서 **랩 계정을** 선택합니다. 
2. 랩 **계정 페이지에서**왼쪽 메뉴에서 **IAM(액세스 제어)을** 선택합니다. 
2. **IAM(액세스 컨트롤)** 페이지에서 도구 모음에 **추가를** 선택하고 **역할 할당 추가를**선택합니다.

    ![랩 계정에 대한 역할 할당 ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. 역할 **할당 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **역할에**대한 **판독기를** 선택합니다. 
    2. 사용자를 선택합니다. 
    3. **저장**을 선택합니다. 

        ![랩 계정의 판독기 역할에 사용자 추가 ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>랩의 소유자 역할에 사용자 추가

1. **랩 계정** 페이지로 돌아가서 왼쪽 메뉴의 **모든 랩을 선택합니다.**
2. 사용자를 소유자로 추가할 **랩을** 선택합니다. 
    
    ![랩 선택 ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. **랩** 페이지에서 왼쪽 메뉴에서 **IAM(액세스 제어)을** 선택합니다.
4. **IAM(액세스 컨트롤)** 페이지에서 도구 모음에 **추가를** 선택하고 **역할 할당 추가를**선택합니다.
5. 역할 **할당 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **역할에**대한 **소유자를** 선택합니다. 
    2. 사용자를 선택합니다. 
    3. **저장**을 선택합니다. 

## <a name="next-steps"></a>다음 단계
사용자가 [랩 서비스 포털에](https://labs.azure.com)로그인할 때 랩을 볼 수 있도록 합니다.