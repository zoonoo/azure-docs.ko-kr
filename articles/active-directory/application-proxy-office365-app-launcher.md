---
title: "Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시 커넥터에 대한 기본 사항 제공"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 31e8e39580ed83f13fd3ffb9981221765063a0b7
ms.lasthandoff: 04/21/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정

이 문서에서는 사용자가 Azure AD(Azure Active Directory) 액세스 패널 및 Office 365 앱 시작 관리자에서 앱에 액세스할 때 사용자를 사용자 지정 홈 페이지로 이동시키도록 앱을 구성하는 방법을 설명합니다.

사용자가 앱을 시작하면 기본적으로 게시된 앱의 루트 도메인 URL로 이동됩니다. 방문 페이지는 일반적으로 홈페이지 URL로 설정됩니다. 예를 들어, 백 엔드 앱인 http://ExpenseApp의 경우 URL은 *https://expenseApp-contoso.msappproxy.net*으로 게시됩니다. 기본적으로 홈페이지 URL은 *https://expenseApp-contoso.msappproxy.net*으로 설정됩니다.

앱 내 특정 페이지(*https://expenseApp-contoso.msappproxy.net/login/login.aspx*)에 사용자를 이동하려는 경우 Azure AD PowerShell 모듈을 사용하여 해당 인스턴스에 대한 사용자 지정 홈페이지 URL을 정의할 수 있습니다.

>[!NOTE]
>사용자에게 게시된 응용 프로그램에 대한 액세스 권한을 제공하면 [Azure AD 액세스 패널](active-directory-saas-access-panel-introduction.md) 및 [Office 365 앱 시작 관리자](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)에 앱이 표시됩니다.

## <a name="before-you-start"></a>시작하기 전에

### <a name="determine-the-home-page-url"></a>홈 페이지 URL 확인

홈페이지 URL을 설정하기 전에 다음 사항에 유의하세요.

* 사용자가 지정한 경로가 루트 도메인 URL의 하위 도메인 경로인지 확인합니다.

  예를 들어, 루트 도메인 URL이 https://apps.contoso.com/app1/이면 구성한 홈페이지 URL은 https://apps.contoso.com/app1/로 시작해야 합니다.

* 게시된 앱을 변경하는 경우 이로 인해 홈페이지 URL 값을 다시 설정할 수 있습니다. 나중에 앱을 업데이트할 경우 홈페이지 URL을 다시 확인하고 필요한 경우 업데이트해야 합니다.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

PowerShell을 사용하여 사용자 지정 홈페이지 URL을 정의하기 전에 Azure AD PowerShell 모듈의 비표준 패키지를 설치합니다. Graph API 끝점을 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)에서 패키지를 다운로드할 수 있습니다. 

패키지를 설치하려면 다음 단계를 따르세요.

1. 표준 PowerShell 창을 열고 다음 명령을 실행합니다.

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    관리자가 아닌 사용자로 명령을 실행하는 경우 `-scope currentuser` 옵션을 사용합니다.
2. 설치 도중 Nuget.org에서 두 개의 패키지를 설치하려면 **Y**를 선택합니다. 두 개의 패키지가 모두 필요합니다. 

## <a name="step-1-find-the-objectid-of-the-app"></a>1단계: 앱의 ObjectID 찾기

앱의 ObjectID를 받은 다음 해당 홈 페이지에서 앱을 검색합니다.

1. PowerShell을 열고 Azure AD 모듈을 가져옵니다.

    ```
    Import-Module AzureAD
    ```

2. 테넌트 관리자로 Azure AD 모듈에 로그인합니다.

    ```
    Connect-AzureAD
    ```
3. 홈페이지 URL을 기준으로 앱을 찾습니다. **Azure Active Directory** > **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**으로 이동하여 포털에서 URL을 찾을 수 있습니다. 이 예제에서는 *sharepoint-iddemo*를 사용합니다.

    ```
    Get-AzureADApplications | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. 여기에 표시된 것과 비슷한 결과가 표시되어야 합니다. 다음 섹션에서 사용할 ObjectID GUID를 복사합니다.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

## <a name="step-2-update-the-home-page-url"></a>2단계: 홈페이지 URL 업데이트

1단계에서 사용한 것과 동일한 PowerShell 모듈에서 다음을 수행합니다.

1. 맞는 앱이 있는지 확인하고 *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4*를 앞의 단계에서 복사한 GUID(ObjectID)로 바꿉니다.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 이제 앱을 확인했으므로 다음과 같이 홈 페이지를 업데이트할 준비가 되었습니다.

2. 변경 내용을 저장하기 위해 비어 있는 응용 프로그램 개체를 만듭니다.  

 >[!NOTE]
 >업데이트할 값을 저장하기만 하므로 실제로 아무 것도 생성되지 않습니다.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. 홈페이지 URL을 원하는 값으로 설정합니다. 값은 게시된 앱의 하위 도메인 경로여야 합니다. 예를 들어, 홈페이지 URL을 *https://sharepoint-iddemo.msappproxy.net/*에서 *https://sharepoint-iddemo.msappproxy.net/hybrid/*로 변경하는 경우 앱 사용자는 사용자 지정 홈페이지로 직접 이동합니다.

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. "1단계: 앱의 ObjectID 찾기"에서 복사한 GUID(ObjectID)를 사용하여 업데이트합니다.

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. 변경 내용이 성공했는지를 확인하려면 앱을 다시 시작합니다.

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>앱에 대한 변경 내용으로 인해 홈페이지 URL을 다시 설정할 수도 있습니다. 이 경우 2단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 응용 프로그램 프록시를 사용하여 SharePoint에 원격 액세스 사용하도록 설정](application-proxy-enable-remote-access-sharepoint.md)
- [Azure Portal에서 응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md)

