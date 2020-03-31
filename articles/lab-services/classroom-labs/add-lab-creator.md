---
title: Azure 랩 서비스에서 사용자를 랩 작성자로 추가
description: 이 문서에서는 Azure Lab 서비스의 랩 계정에 대한 랩 작성자 역할에 사용자를 추가하는 방법을 보여 주며 있습니다. 랩 작성자는 이 랩 계정 내에서 랩을 만들 수 있습니다.
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
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444772"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Azure 랩 서비스의 랩 계정에 랩 작성자 추가
이 문서에서는 Azure Lab Services의 랩 계정에 사용자를 랩 작성자로 추가하는 방법을 보여 주며, 이 문서에서는 사용자를 랩 작성자로 추가하는 방법을 보여 주며, 이 문서에서는 사용자를 랩 작성자로 추가하는 방법을 보여 주시면 됩니다. 그런 다음 이러한 용도로 랩 계정에서 강의실 랩을 만들 수 있습니다. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>랩 작성자 역할에 Microsoft 사용자 계정 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 역할 할당 추가**를 클릭합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Microsoft 이외의 계정 사용자를 랩 작성자로 추가하는 경우 [Microsoft 가 아닌 계정 사용자 추가](#add-a-non-microsoft-account-user-as-a-lab-creator) 섹션을 참조하세요. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Microsoft 이외의 계정 사용자를 랩 작성자로 추가
사용자를 랩 작성자로 추가하려면 해당 전자 메일 계정을 사용합니다. 다음과 같은 유형의 전자 메일 계정을 사용할 수 있습니다.

- 대학 의 Office 365 Azure Active Directory(AAD)에서 제공하는 전자 메일 계정입니다. 
- 와 `@outlook.com`같은 `@hotmail.com` `@msn.com`Microsoft 전자 메일 계정 `@live.com`입니다.
- 야후 나 구글에서 제공하는 것과 같은 비 마이크로 소프트 이메일 계정. 그러나 이러한 유형의 계정은 Microsoft 계정과 연결되어야 합니다.
- GitHub 계정. 이 계정은 Microsoft 계정과 연결되어 있어야 합니다.

### <a name="using-a-non-microsoft-email-account"></a>Microsoft 이외의 전자 메일 계정 사용
랩 작성자/교수자는 Microsoft 이외의 전자 메일 계정을 사용하여 강의실 랩에 등록하고 로그인할 수 있습니다.  그러나 랩 서비스 포털에 로그인하려면 교수자가 먼저 Microsoft 이외의 전자 메일 주소에 연결된 Microsoft 계정을 만들어야 합니다.

많은 교수자는 이미 Microsoft 이외의 전자 메일 주소에 연결된 Microsoft 계정을 가지고 있을 수 있습니다. 예를 들어, 교수자는 Office, Skype, OneDrive 또는 Windows와 같은 Microsoft의 다른 제품 또는 서비스와 함께 전자 메일 주소를 사용한 경우 이미 Microsoft 계정을 가지고 있습니다.  

교수자는 랩 서비스 포털에 로그인할 때 이메일 주소와 암호를 묻는 메시지가 표시됩니다. 교수자가 Microsoft 계정이 연결되어 있지 않은 Microsoft 이외의 계정으로 로그인을 시도하면 교수자는 다음과 같은 오류 메시지를 받게 됩니다. 

![오류 메시지](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft 계정에 등록하려면 교수자는 로 [http://signup.live.com](http://signup.live.com)이동해야 합니다.  


### <a name="using-a-github-account"></a>GitHub 계정 사용
교수자는 기존 GitHub 계정을 사용하여 강의실 랩에 등록하고 로그인할 수도 있습니다. 교수자가 이미 GitHub 계정에 연결된 Microsoft 계정이 있는 경우 이전 섹션과 같이 로그인하여 암호를 제공할 수 있습니다. GitHub 계정을 Microsoft 계정에 아직 연결하지 않은 경우 **로그인 옵션을**선택해야 합니다.

![로그인 옵션 링크](../media/how-to-configure-student-usage/signin-options.png)

로그인 **옵션** 페이지에서 **GitHub를 사용 하 고 로그인을**선택합니다.

![GitHub 링크로 로그인](../media/how-to-configure-student-usage/signin-github.png)

마지막으로 GitHub 계정에 연결된 Microsoft 계정을 만들라는 메시지가 표시됩니다. 교수자가 **다음**을 선택하면 자동으로 수행됩니다.  그런 다음 강사가 즉시 로그인되어 교실 랩에 연결됩니다.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
