---
title: Azure Notebooks에 로그인
description: 신속하게 Azure Notebooks에 로그인하고 사용자 ID를 설정합니다. 그러면 저장된 프로젝트에 액세스하고 Notebook을 다른 사용자와 공유하는 기능이 제공됩니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f595fbb6571c3deaaa0644ba9d62f598181592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106314"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>빠른 시작: 로그인 및 사용자 ID 설정

언제든지 로그인하지 않고 Azure Notebooks를 볼 수 있지만, Notebook을 실행하고 저장된 프로젝트 및 Notebook에 액세스하고, 다른 사람과 Notebook을 공유하려면 로그인해야 합니다.

## <a name="sign-in"></a>로그인

1. [notebooks.azure.com](https://notebooks.azure.com/)의 오른쪽 위에서 **로그인**을 선택합니다.

    ![Azure Notebooks에서 로그인 명령의 위치](media/accounts/sign-in-command.png)

1. 메시지가 표시되면 Microsoft 계정 또는 회사 또는 학교 계정의 이메일 주소를 입력하고 **다음**을 선택합니다. [Azure Notebooks에 대한 사용자 계정](azure-notebooks-user-account.md)에 계정 유형이 설명되어 있습니다. Microsoft 계정이 없거나 Azure Notebooks에 사용할 계정을 새로 만들려면 **만들기**를 선택합니다.

    ![로그인 프롬프트의 새 Microsoft 계정 만들기 명령](media/accounts/create-new-microsoft-account.png)

1. 메시지가 표시되면 암호를 입력합니다.

1. 처음으로 로그인하면 Azure Notebooks가 계정에 대한 액세스 권한을 요청합니다. **예**를 선택하여 계속합니다.

    ![계정 권한 프롬프트](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>사용자 ID 설정

1. 처음으로 로그인하면 "anon-idrca3" 같은 임시 사용자 ID가 할당됩니다. 사용자 ID가 "anon-"으로 시작할 때마다 Azure Notebooks는 사용자 고유의 ID를 만들라는 메시지를 표시합니다. 사용자 ID는 프로젝트 및 Notebook을 공유하기 위해 획득하는 모든 URL에 사용되므로, 고유하고 의미 있는 것을 선택해야 합니다.

    ![Azure Notebooks에 대한 사용자 ID를 입력하라는 프롬프트](media/accounts/create-user-id.png)

    **아니요**를 선택하면 Azure Notebooks는 사용자가 로그인할 때마다 사용자 ID를 요구합니다. 언제든지 [사용자 프로필](azure-notebooks-user-profile.md)에서 사용자 ID를 설정할 수 있습니다.

1. 성공적으로 로그인하면 Azure Notebooks가 사용자의 공개 프로필 페이지로 이동합니다. 이 페이지에서 **프로필 정보 편집**을 선택하여 나머지 정보를 입력할 수 있습니다(자세한 내용은 [프로필 및 사용자 ID](azure-notebooks-user-profile.md) 참조).

    ![Azure Notebooks 프로필 페이지의 초기 보기](media/accounts/profile-page-new.png)

## <a name="sign-out"></a>로그아웃

로그아웃하려면 페이지의 오른쪽 위에서 사용자 이름을 선택하고 **로그아웃**을 선택합니다.

![Azure Notebooks에서 로그아웃 명령의 위치](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Notebook 만들기 및 공유](quickstart-create-share-jupyter-notebook.md)
