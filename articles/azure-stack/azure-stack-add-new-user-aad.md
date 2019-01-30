---
title: Azure Active Directory에서 새 Azure Stack 테 넌 트 계정 추가 | Microsoft Docs
description: Microsoft Azure Stack 개발 키트를 배포한 후 테 넌 트 포털을 탐색할 수 있도록 하나 이상의 테 넌 트 사용자 계정을 만들려면 해야 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: c0f457955bd8df7fe07e2a96469fde0a751417bc
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240322"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Azure Active Directory에서 새 Azure Stack 테넌트 계정 추가

이후에 [Azure Stack 개발 키트 배포](azure-stack-run-powershell-script.md), 테 넌 트 포털을 탐색 하 고 본인의 제품 및 계획을 테스트할 수 있도록 테 넌 트 사용자 계정이 필요 합니다. 테 넌 트 계정을 만들 수 있습니다 [Azure portal을 사용 하 여](#create-an-azure-stack-tenant-account-using-the-azure-portal) 하거나 [PowerShell을 사용 하 여](#create-an-azure-stack-tenant-account-using-powershell)입니다.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Stack 테넌트 계정 만들기

Azure 포털을 사용하려면 Azure 구독이 있어야 합니다.

1. 에 로그인 [Azure](https://portal.azure.com)합니다.
2. 왼쪽된 탐색 모음에서 선택 **Active Directory** 및 Azure Stack에 대해 사용 하려는 디렉터리로 전환 하거나 새로 만듭니다.
3. 선택 **Azure Active Directory** > **사용자가** > **새 사용자**합니다.

    ![사용자 - 새 사용자가 강조 표시된 모든 사용자 페이지](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. **사용자** 페이지에서 필요한 정보를 입력합니다.

    ![새 사용자 추가, 사용자 정보가 있는 사용자 페이지](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **이름(필수).** 새 사용자의 이름 및 성입니다. 예를 들어, Mary Parker입니다.
    - **사용자 이름(필수).** 새 사용자의 사용자 이름입니다. 예: mary@contoso.com
        사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 <_yourdomainname_>.onmicrosoft.com이거나 contoso.com과 같은 사용자 지정 도메인 이름을 사용해야 합니다. 사용자 지정 도메인 이름 작성 방법에 대한 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름을 추가하는 방법](../active-directory/fundamentals/add-custom-domain.md)을 참조하세요.
    - **프로필.** 원할 경우 사용자에 대한 자세한 정보를 추가할 수 있습니다. 사용자 정보를 나중에 추가할 수도 있습니다. 사용자 정보 추가에 대한 자세한 내용은 [사용자 프로필 정보를 추가하거나 변경하는 방법](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)을 참조하세요.
    - **디렉터리 역할.**  선택할 **사용자**합니다.

5. 확인 **암호 표시** 에 제공 된 자동 생성 된 암호를 복사 합니다 **암호** 상자입니다. 초기 로그인 프로세스에 대 한이 암호가 필요 합니다.

6. **만들기**를 선택합니다.

    사용자가 만들어지고 Azure AD 테넌트에 추가됩니다.

7. 새 계정으로 Microsoft Azure 포털에 로그인 합니다. 메시지가 표시되면 암호를 변경합니다.
8. 에 로그인 `https://portal.local.azurestack.external` 테 넌 트 포털이 새 계정으로 합니다.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>PowerShell을 사용하여 Azure Stack 테넌트 계정 만들기

Azure 구독이 없으면 테 넌 트 사용자 계정을 추가 하려면 Azure portal을 사용할 수 없습니다. 이 경우에 Azure Active Directory 모듈에 대 한 Windows PowerShell을 대신 사용할 수 있습니다.

> [!NOTE]
> Microsoft 계정 (Live ID)를 Azure Stack 개발 키트 배포를 사용 하는 경우에 테 넌 트 계정을 만들려면 AAD PowerShell을 사용할 수 없습니다. 
> 
> 

1. [IT 전문가를 위한 Microsoft Online Services 로그인 도우미 RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950)를 설치합니다
2. 설치 합니다 [Azure Active Directory에 대 한 Windows PowerShell 모듈 (64 비트 버전)](https://go.microsoft.com/fwlink/p/?linkid=236297) 엽니다.
3. 다음 cmdlet을 실행합니다.

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 새 계정으로 Microsoft Azure에 로그인 합니다. 메시지가 표시되면 암호를 변경합니다.
2. 에 로그인 `https://portal.local.azurestack.external` 테 넌 트 포털이 새 계정으로 합니다.

