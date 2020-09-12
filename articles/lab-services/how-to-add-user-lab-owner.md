---
title: Azure Lab Services에서 랩에 추가 소유자를 추가 하는 방법
description: 이 문서에서는 관리자가 Azure Lab Services의 랩에 사용자를 소유자로 추가 하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 4b46dd08a3b9527cdfc7bdf766ee4976e67656d0
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482672"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Azure Lab Services에서 기존 랩에 추가 소유자를 추가 하는 방법
이 문서에서는 관리자가 기존 랩에 추가 소유자를 추가할 수 있는 방법을 보여 줍니다.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>랩 계정에 대 한 읽기 권한자 역할에 사용자 추가
사용자를 기존 랩에 추가 소유자로 추가 하려면 먼저 사용자에 게 랩 계정에 대 한 **읽기** 권한을 부여 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **Lab Services**를 검색 한 다음 선택 합니다.
3. 목록에서 **랩 계정을** 선택 합니다. 
2. **랩 계정 페이지**의 왼쪽 메뉴에서 **Access Control (IAM)** 을 선택 합니다. 
2. **액세스 제어 (IAM)** 페이지의 도구 모음에서 **추가** 를 선택 하 고 **역할 할당 추가**를 선택 합니다.

    ![랩 계정에 대 한 역할 할당 ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **역할**에 대 한 **판독기** 를 선택 합니다. 
    2. 사용자를 선택합니다. 
    3. **저장**을 선택합니다. 

        ![랩 계정에 대 한 읽기 권한자 역할에 사용자 추가 ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>랩의 소유자 역할에 사용자 추가

> [!NOTE]
> 랩에 대 한 읽기 권한자 권한만 있는 사용자는 labs.azure.com에 표시 되지 않습니다.

1. **랩 계정** 페이지로 돌아가서 왼쪽 메뉴에서 **모든 실습** 을 선택 합니다.
2. 사용자를 소유자로 추가 하려는 **랩을** 선택 합니다. 
    
    ![랩 선택 ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. **랩** 페이지의 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 합니다.
4. **액세스 제어 (IAM)** 페이지의 도구 모음에서 **추가** 를 선택 하 고 **역할 할당 추가**를 선택 합니다.
5. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다. 
    1. **역할**에 대 한 **소유자** 를 선택 합니다. 
    2. 사용자를 선택합니다. 
    3. **저장**을 선택합니다. 

## <a name="next-steps"></a>다음 단계
[Lab Services 포털](https://labs.azure.com)에 로그인 할 때 사용자에 게 랩이 표시 되는지 확인 합니다.
