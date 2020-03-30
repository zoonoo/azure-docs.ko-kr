---
title: Azure AD에서 엔터프라이즈 앱에 사용자 또는 그룹 할당
description: Azure Active Directory에서 사용자 또는 그룹을 할당할 엔터프라이즈 앱을 선택하는 방법
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409195"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당

이 문서에서는 Azure 포털 내에서 또는 PowerShell을 사용하여 Azure Active Directory(Azure AD)의 엔터프라이즈 응용 프로그램에 사용자 또는 그룹을 할당하는 방법을 보여 주며, 이 문서에서는 응용 프로그램에 사용자를 할당하면 쉽게 액세스할 수 있도록 응용 프로그램이 사용자의 [내 앱 액세스 패널에](https://myapps.microsoft.com/) 나타납니다. 응용 프로그램에서 역할을 노출하는 경우 사용자에게 특정 역할을 할당할 수도 있습니다.

제어를 강화하려면 특정 유형의 엔터프라이즈 응용 프로그램을 [사용자 할당을 요구하도록](#configure-an-application-to-require-user-assignment)구성할 수 있습니다. 

[엔터프라이즈 앱에 사용자 또는 그룹을 할당하려면](#assign-users-or-groups-to-an-app-via-the-azure-portal)글로벌 관리자, 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자 또는 엔터프라이즈 앱의 할당된 소유자로 로그인해야 합니다.

> [!NOTE]
> 그룹 기반 할당에는 Azure Active 디렉터리 프리미엄 P1 또는 P2 버전이 필요합니다. 그룹 기반 할당은 보안 그룹에대해서만 지원됩니다. 중첩된 그룹 구성원 및 Office 365 그룹은 현재 지원되지 않습니다. 이 문서에서 설명하는 기능에 대한 라이선스 요구 사항에 대한 자세한 내용은 [Azure Active Directory 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/active-directory)참조하십시오. 

## <a name="configure-an-application-to-require-user-assignment"></a>사용자 할당이 필요한 응용 프로그램 구성

다음 유형의 응용 프로그램을 사용하면 사용자가 응용 프로그램에 액세스하기 전에 응용 프로그램에 할당하도록 요구할 수 있습니다.

- SAML 기반 인증을 통해 페더레이션된 단일 사인온(SSO)을 위해 구성된 응용 프로그램
- Azure Active Directory 사전 인증을 사용하는 응용 프로그램 프록시 응용 프로그램
- 사용자 또는 관리자가 해당 응용 프로그램에 동의한 후 OAuth 2.0 / OpenID 연결 인증을 사용하는 Azure AD 응용 프로그램 플랫폼에 빌드된 응용 프로그램입니다.

사용자 할당이 필요한 경우 응용 프로그램에 명시적으로 할당한 사용자만 로그인할 수 있습니다. 내 앱 페이지에서 또는 직접 링크를 사용하여 앱에 액세스할 수 있습니다. 

할당이 *필요하지 않은*경우 이 옵션을 **아니요로** 설정했거나 응용 프로그램이 다른 SSO 모드를 사용하기 때문에 응용 프로그램의 **속성** 페이지에 응용 프로그램에 대한 직접 링크 또는 사용자 **액세스 URL이** 있는 경우 모든 사용자가 응용 프로그램에 액세스할 수 있습니다. 

이 설정은 내 앱 액세스 패널에 응용 프로그램이 표시되는지 여부에 영향을 주지 않습니다. 응용 프로그램에 사용자 또는 그룹을 할당하면 사용자의 내 앱 액세스 패널에 응용 프로그램이 표시됩니다. 백그라운드는 [앱에 대한 액세스 관리를](what-is-access-management.md)참조하십시오.


응용 프로그램에 대한 사용자 할당을 요구하려면 다음을 수행합니다.

1. 관리자 계정또는 응용 프로그램의 소유자로 [Azure 포털에](https://portal.azure.com) 로그인합니다.

2. **Azure 활성 디렉터리**를 선택합니다. 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램을 선택합니다.**

3. 목록에서 응용 프로그램을 선택합니다. 응용 프로그램이 표시되지 않으면 검색 상자에 이름을 입력하십시오. 또는 필터 컨트롤을 사용하여 응용 프로그램 유형, 상태 또는 표시 를 선택한 다음 **적용을**선택합니다.

4. 왼쪽 탐색 메뉴에서 **속성**을 선택합니다.

5. **사용자 할당이 필요한지** 확인하려면 전환이 예로 설정되어 **있습니다.**

   > [!NOTE]
   > 사용자 **할당이 필요한** 경우 전환글을 사용할 수 없는 경우 PowerShell을 사용하여 서비스 주체에서 appRoleAssignmentRequired 속성을 설정할 수 있습니다.

6. 화면 상단의 **저장** 버튼을 선택합니다.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Azure 포털을 통해 앱에 사용자 또는 그룹 할당

1. 글로벌 관리자, 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자 계정또는 엔터프라이즈 앱의 할당된 소유자로 [Azure 포털에](https://portal.azure.com) 로그인합니다.
2. **Azure 활성 디렉터리**를 선택합니다. 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램을 선택합니다.**
3. 목록에서 응용 프로그램을 선택합니다. 응용 프로그램이 표시되지 않으면 검색 상자에 이름을 입력하십시오. 또는 필터 컨트롤을 사용하여 응용 프로그램 유형, 상태 또는 표시 를 선택한 다음 **적용을**선택합니다.
4. 왼쪽 탐색 메뉴에서 **사용자 및 그룹을**선택합니다.
   > [!NOTE]
   > Office 365 앱과 같은 Microsoft 응용 프로그램에 사용자를 할당하려는 경우 이러한 앱 중 일부는 PowerShell을 사용합니다. 
5. **사용자 추가** 단추를 선택합니다.
6. 과제 **추가** 창에서 **사용자 및 그룹을**선택합니다.
7. 응용 프로그램에 할당할 사용자 또는 그룹을 선택하거나 검색 상자에 사용자 또는 그룹의 이름을 입력합니다. 여러 사용자 및 그룹을 선택할 수 있으며 선택 항목이 **선택항목**아래에 표시됩니다.
8. 완료되면 을 **선택(을)을 클릭합니다.**

   ![앱에 사용자 또는 그룹 할당](./media/assign-user-or-group-access-portal/assign-users.png)

9. 사용자 **및 그룹** 창에서 목록에서 하나 이상의 사용자 또는 그룹을 선택한 다음 창 하단의 **선택** 단추를 선택합니다.
10. 응용 프로그램이 지원하는 경우 사용자 또는 그룹에 역할을 할당할 수 있습니다. 과제 **추가** 창에서 **역할 선택을**선택합니다. 그런 다음 **역할 선택** 창에서 선택한 사용자 또는 그룹에 적용할 역할을 선택한 다음 창 하단에서 **확인을** 선택합니다. 

    > [!NOTE]
    > 응용 프로그램이 역할 선택을 지원하지 않는 경우 기본 액세스 역할이 할당됩니다. 이 경우 응용 프로그램은 사용자가 가진 액세스 수준을 관리합니다.

2. 할당 **추가** 창에서 창 하단에 **있는 할당** 단추를 선택합니다.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>PowerShell을 통해 앱에 사용자 또는 그룹 할당

1. 관리자 권한 Windows PowerShell 명령 프롬프트를 엽니다.

   > [!NOTE]
   > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

1. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.
1. 다음 스크립트를 사용하여 애플리케이션에 사용자 및 역할을 할당합니다.

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

애플리케이션 역할에 사용자를 할당하는 방법에 대한 자세한 내용은 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)에 대한 설명서를 참조하세요.

그룹을 엔터프라이즈 앱에 할당하려면 `Get-AzureADUser`를 `Get-AzureADGroup`으로 바꿔야 합니다.

### <a name="example"></a>예제

이 예제에서는 PowerShell을 사용하여 사용자 Britta Simon을 [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) 애플리케이션에 할당합니다.

1. PowerShell에서 변수 $username, app_name $ 및 $app_role_name에 해당 값을 할당합니다.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. 이 예제에서는 Britta Simon에 할당하려는 애플리케이션 역할의 정확한 이름을 모릅니다. 다음 명령을 실행하여 사용자 UPN 및 서비스 주체 표시 이름으로 사용자($user) 및 서비스 주체($sp)를 가져옵니다.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. 명령 `$sp.AppRoles`를 실행하여 Workplace Analytics 애플리케이션에 대해 사용할 수 있는 역할을 표시합니다. 이 예제에서는 Britta Simon을 Analyst(제한된 액세스) 역할에 할당하려고 합니다.

   ![Workplace 분석 역할을 사용하여 사용자가 사용할 수 있는 역할 표시](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. 역할 이름을 `$app_role_name` 변수에 할당합니다.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. 다음 명령을 실행하여 앱 역할에 사용자를 할당합니다.

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>관련 문서

- [응용 프로그램에 대한 최종 사용자 액세스에 대해 자세히 알아보기](end-user-experiences.md)
- [Azure AD 액세스 패널 배포 계획](access-panel-deployment-plan.md)
- [앱에 대한 액세스 관리](what-is-access-management.md)
 
## <a name="next-steps"></a>다음 단계

- [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
- [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
- [엔터프라이즈 앱의 이름 또는 로고 변경](change-name-or-logo-portal.md)
