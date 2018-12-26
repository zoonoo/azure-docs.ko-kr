---
title: Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당 | Microsoft Docs
description: Azure Active Directory에서 사용자 또는 그룹을 할당할 엔터프라이즈 앱을 선택하는 방법
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: b6b7408b4efe4c3271ea2ddeb63a499bee670976
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711313"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당
엔터프라이즈 앱에 사용자 또는 그룹을 할당하려면 엔터프라이즈 앱을 관리하기 위한 적절한 권한이 있어야 하고 해당 디렉터리에 대한 전역 관리자여야 합니다.

> [!NOTE]
> 이 문서에서 설명하는 기능에 대한 라이선스 요구 사항은 [Azure Active Directory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory)를 참조하세요.

> [!NOTE]
> Microsoft 응용 프로그램(예: Office 365 앱)의 경우 PowerShell을 사용하여 엔터프라이즈 앱에 사용자를 할당합니다.


## <a name="assign-a-user-to-an-app---portal"></a>앱에 사용자 할당 - 포털
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택하고 텍스트 상자에 Azure Active Directory를 입력한 다음, **입력**을 선택합니다.
3. **Enterprise 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 앱 열기](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. **엔터프라이즈 응용 프로그램** 블레이드에서 **모든 응용 프로그램**을 선택합니다. 그러면 관리할 수 있는 앱이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 블레이드에서 앱을 선택합니다.
6. ***appname*** 블레이드(즉, 제목에서 선택된 앱의 이름을 가진 블레이드)에서 **사용자 및 그룹**을 선택합니다.

    ![모든 애플리케이션 명령 선택](./media/assign-user-or-group-access-portal/select-app-users.png)
7. ***appname*** **- 사용자 및 그룹 할당** 블레이드에서 **추가** 명령을 선택합니다.
8. **할당 추가** 블레이드에서 **사용자 및 그룹**을 선택합니다.

    ![앱에 사용자 또는 그룹 할당](./media/assign-user-or-group-access-portal/assign-users.png)
9. **사용자 및 그룹** 블레이드의 목록에서 하나 이상의 사용자 또는 그룹을 선택한 다음 블레이드 맨 아래의 **선택** 단추를 선택합니다.
10. **할당 추가** 블레이드에서 **역할**을 선택합니다. 그런 다음 **역할 선택** 블레이드에서 선택한 사용자 또는 그룹에 적용할 역할을 선택한 다음 블레이드 맨 아래의 **확인** 단추를 선택합니다.
11. **할당 추가** 블레이드에서 블레이드 맨 아래의 **할당** 단추를 선택합니다. 할당된 사용자 또는 그룹은 이 엔터프라이즈 앱에 대한 선택된 역할로 정의된 사용 권한을 갖습니다.

## <a name="allow-all-users-to-access-an-app---portal"></a>모든 사용자가 앱에 액세스하도록 허용 - 포털
모든 사용자가 애플리케이션에 액세스하도록 허용하려면:

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택하고 텍스트 상자에 Azure Active Directory를 입력한 다음, **입력**을 선택합니다.
3. **Enterprise 응용 프로그램**을 선택합니다.
4. **엔터프라이즈 응용 프로그램** 블레이드에서 **모든 응용 프로그램**을 선택합니다. 그러면 관리할 수 있는 앱이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 블레이드에서 앱을 선택합니다.
6. ***appname*** 블레이드에서 **속성**을 선택합니다.
7. ***appname* - 속성** 블레이드에서 **사용자 할당 필요?** 를 **아니요**로 설정합니다. 

**사용자 할당 필요?** 옵션:

- 애플리케이션 액세스 패널에 애플리케이션이 표시되는지 여부에 영향을 주지 않습니다. 애플리케이션을 액세스 패널에 표시하려면 애플리케이션에 적절한 사용자 또는 그룹을 할당해야 합니다.
- SAML Single Sign-On에 대해 구성된 클라우드 애플리케이션 및 애플리케이션 프록시로 구성된 온-프레미스 애플리케이션으로만 작동합니다. [애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)을 참조하세요.
- 사용자는 애플리케이션에 동의해야 합니다. 관리자는 모든 사용자에 대한 동의를 부여할 수 있습니다.  [최종 사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)을 참조하세요.


## <a name="assign-a-user-to-an-app---powershell"></a>앱에 사용자 할당 - PowerShell

1. 관리자 권한 Windows PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE] 
    > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

2. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.
3. 다음 스크립트를 사용하여 응용 프로그램에 사용자 및 역할을 할당합니다.

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

응용 프로그램 역할에 사용자를 할당하는 방법에 대한 자세한 내용은 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)에 대한 설명서를 참조하세요.

그룹을 엔터프라이즈 앱에 할당하려면 `Get-AzureADUser`를 `Get-AzureADGroup`으로 바꿔야 합니다.

### <a name="example"></a>예

이 예제에서는 PowerShell을 사용하여 사용자 Britta Simon을 [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) 응용 프로그램에 할당합니다.

1. PowerShell에서 변수 $username, app_name $ 및 $app_role_name에 해당 값을 할당합니다. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. 이 예제에서는 Britta Simon에 할당하려는 응용 프로그램 역할의 정확한 이름을 모릅니다. 다음 명령을 실행하여 사용자 UPN 및 서비스 주체 표시 이름으로 사용자($user) 및 서비스 주체($sp)를 가져옵니다.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. 명령 `$sp.AppRoles`를 실행하여 Workplace Analytics 응용 프로그램에 대해 사용할 수 있는 역할을 표시합니다. 이 예제에서는 Britta Simon을 Analyst(제한된 액세스) 역할에 할당하려고 합니다.
    
    ![Workplace Analytics 역할](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. 역할 이름을 `$app_role_name` 변수에 할당합니다.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. 다음 명령을 실행하여 앱 역할에 사용자를 할당합니다.

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](change-name-or-logo-portal.md)
