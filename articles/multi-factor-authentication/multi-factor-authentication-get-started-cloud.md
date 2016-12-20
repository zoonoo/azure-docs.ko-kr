---
title: "클라우드에서 Azure MFA 시작 | Microsoft Docs"
description: "클라우드에서 Azure MFA 시작 방법을 설명하는 Microsoft Azure Multi-Factor Authentication 페이지입니다."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c6fe00b72d95a3eb40d91f6f7989b7163518c46f


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>클라우드에서 Azure Multi-Factor Authentication 시작
이 문서에서는 클라우드에서 Azure Multi-Factor Authentication을 사용하기 시작하는 방법에 대해 설명합니다.

> [!NOTE]
> 다음 문서는 **Azure 클래식 포털**사용자에 대해 사용하도록 설정하는 방법에 대해 설명합니다. O365 사용자를 위해 Azure Multi-Factor Authentication을 설정하는 방법을 찾는 경우 [Office 365용 Multi-Factor Authentication 설정](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)을 참조하세요.
> 
> 

![클라우드의 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>필수 조건
사용자가 Azure Multi-Factor Authentication을 사용할 수 있도록 하려면 다음 필수 조건을 충족해야 합니다.

1. [Azure 구독 등록](https://azure.microsoft.com/pricing/free-trial/) - 아직 Azure 구독이 없는 경우 등록해야 합니다. Azure MFA를 시작하고 사용하려면 평가판 구독을 사용할 수 있습니다.
2. [Multi-Factor Auth 공급자를 만들고](multi-factor-authentication-get-started-auth-provider.md) 디렉터리에 할당하거나 [사용자에게 라이선스를 할당](multi-factor-authentication-get-started-assign-licenses.md)합니다.

> [!NOTE]
> 라이선스는 Azure MFA, Azure AD Premium 또는 EMS(Enterprise Mobility Suite) 사용자가 사용할 수 있습니다.  MFA는 Azure AD Premium 및 EMS에 포함되어 있습니다. 라이선스가 충분한 경우 인증 공급자를 만들 필요가 없습니다.
> 
> 

## <a name="turn-on-two-step-verification-for-users"></a>사용자에 대한 2단계 확인을 설정합니다.
사용자에 대한 2단계 확인을 요구하려면 사용자의 상태를 사용 안 함에서 사용으로 변경합니다.  사용자 상태에 대한 자세한 내용은 [Azure Multi-Factor Authentication의 사용자 상태](multi-factor-authentication-get-started-user-states.md)를 참조하세요.

다음 절차를 사용하여 사용자에 대해 MFA를 사용하도록 설정합니다.

### <a name="to-turn-on-multi-factor-authentication"></a>다단계 인증을 설정하려면
1. 관리자 권한으로 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽에서 **Active Directory**를 클릭합니다.
3. 디렉터리에서 사용하도록 설정하려는 사용자에 대한 디렉터리를 선택합니다.
   ![디렉터리 클릭](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. 위쪽에서 **사용자**를 클릭합니다.
5. 페이지의 아래쪽에서 **Multi-Factor Auth 관리**를 클릭합니다. 새 브라우저 탭이 열립니다.
   ![디렉터리 클릭](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 2단계 확인을 사용하도록 설정할 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수 있습니다. 상태가 **사용 안 함** 상태인지 확인합니다.
   ![사용자 사용](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. 이름 옆의 상자에 **확인 표시** 를 합니다.
8. 오른쪽에서 **사용**을 클릭합니다.
   ![사용자 사용 설정](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. **다단계 인증 사용**을 클릭합니다.
   ![사용자 사용 설정](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. 사용자의 상태가 **사용 안 함**에서 **사용**으로 변경됩니다.
    ![사용자 사용 설정](./media/multi-factor-authentication-get-started-cloud/user.png)

사용자를 사용하도록 설정하면 전자 메일을 통해 알려야 합니다. 다음에 로그인하려는 경우 2단계 확인에 대한 해당 계정을 등록하라는 메시지가 표시됩니다. 2단계 확인을 사용하기 시작하면 비 브라우저 앱이 잠기지 않도록 방지하려면 앱 암호를 설정해야 합니다.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>PowerShell을 사용하여 2단계 확인 자동 켜기
[Azure AD PowerShell](/powershell/azureps-cmdlets-docs)을 사용하여 [상태](multi-factor-authentication-whats-next.md)를 변경하려면 다음을 사용할 수 있습니다.  `$st.State` 를 다음 중 하나로 변경할 수 있습니다.

* 사용
* 적용
* 사용 안 함  

> [!IMPORTANT]
> 사용 안 함 상태에서 강제 적용 상태로 직접 사용자를 이동하지 않는 것이 좋습니다. 사용자가 MFA 등록을 마치고 [앱 암호](multi-factor-authentication-whats-next.md#app-passwords)를 가져오지 못했기 때문에 비 브라우저 기반 앱은 작동을 중단합니다. 비 브라우저 기반 앱이 없고 앱 암호가 필요한 경우 사용 안 함 상태에서 사용 상태로 전환하는 것이 좋습니다. 그러면 사용자가 앱 암호를 등록하고 얻을 수 있습니다. 그런 다음 강제 적용 상태로 전환할 수 있습니다.
> 
> 

PowerShell을 사용하면 많은 사용자에 대해 사용을 설정할 수 있습니다. 현재 Azure 포털에는 다수 사용 기능이 없으며 각 사용자를 개별적으로 선택해야 합니다. 사용자 수가 많은 경우 태스크에 많은 시간이 소요될 수 있습니다. 다음을 사용하는 PowerShell 스크립트를 만들어서 사용자 목록을 반복하여 사용 설정할 수 있습니다.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

다음은 예제입니다.

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


자세한 내용은 [Azure Multi-Factor Authentication의 사용자 상태](multi-factor-authentication-get-started-user-states.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
클라우드에서 Azure Multi-Factor Authentication을 설정했으므로 배포를 구성 및 설정할 수 있습니다. 자세한 내용은 [Azure Multi-Factor Authentication 구성](multi-factor-authentication-whats-next.md)을 참조하세요.




<!--HONumber=Dec16_HO2-->


