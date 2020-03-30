---
title: 게시된 앱에 대한 사용자 지정 홈 페이지 - Azure AD 응용 프로그램 프록시
description: Azure AD 애플리케이션 프록시 커넥터에 대한 기본 사항 제공
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275608"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정

이 문서에서는 사용자를 사용자 지정 홈 페이지로 안내하도록 앱을 구성하는 방법에 대해 설명합니다. 응용 프로그램 프록시를 사용 하 고 응용 프로그램을 게시할 때 내부 URL을 설정 하지만 때로는 사용자가 먼저 볼 수 있는 페이지 가 아닙니다. 사용자가 앱에 액세스할 때 올바른 페이지를 얻을 수 있도록 사용자 지정 홈 페이지를 설정합니다. 사용자는 Azure Active Directory 액세스 패널 또는 Office 365 앱 실행 프로그램에서 앱에 액세스하는지 여부에 관계없이 사용자가 설정한 사용자 지정 홈 페이지를 볼 수 있습니다.

사용자가 앱을 실행하면 게시된 앱의 루트 도메인 URL로 기본적으로 이동됩니다. 방문 페이지는 일반적으로 홈페이지 URL로 설정됩니다. Azure AD PowerShell 모듈을 사용하여 앱 사용자가 앱 내의 특정 페이지에 착륙하도록 할 때 사용자 지정 홈 페이지 URL을 정의합니다.

다음은 회사에서 사용자 지정 홈 페이지를 설정하는 이유를 설명하는 한 가지 시나리오입니다.

- 회사 네트워크 내에서 사용자가 로그인하여 `https://ExpenseApp/login/login.aspx` 앱에 액세스합니다.
- 응용 프로그램 프록시가 폴더 구조의 최상위 수준에서 액세스해야 하는 다른 자산(예: 이미지)이 있으므로 내부 URL로 `https://ExpenseApp` 앱을 게시합니다.
- 기본 외부 URL은 `https://ExpenseApp-contoso.msappproxy.net`외부 사용자를 로그인 페이지로 이동하지 않는 것입니다.
- 대신 홈 `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` 페이지 URL로 설정하려고 하므로 외부 사용자가 로그인 페이지를 먼저 볼 수 있습니다.

> [!NOTE]
> 사용자에게 게시된 응용 프로그램에 대한 액세스 권한을 제공하면 [Azure AD 액세스 패널](../user-help/my-apps-portal-end-user-access.md) 및 [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)에 앱이 표시됩니다.

## <a name="before-you-start"></a>시작하기 전에

홈페이지 URL을 설정하기 전에 다음 요구 사항에 유의하세요.

- 지정하는 경로는 루트 도메인 URL의 하위 도메인 경로여야 합니다.

  예를 들어 루트 도메인 URL이 `https://apps.contoso.com/app1/`있는 경우 구성한 홈 페이지 `https://apps.contoso.com/app1/`URL을 으로 시작해야 합니다.

- 게시된 앱을 변경하는 경우 이로 인해 홈페이지 URL 값을 다시 설정할 수 있습니다. 나중에 앱을 업데이트할 경우 홈페이지 URL을 다시 확인하고 필요한 경우 업데이트해야 합니다.

Azure 포털을 통해 또는 PowerShell을 사용하여 홈 페이지 URL을 설정할 수 있습니다.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure Portal에서 홈페이지 변경

Azure AD 포털을 통해 앱의 홈 페이지 URL을 변경하려면 다음 단계를 따르세요.

1. 관리자로 [Azure 포털에](https://portal.azure.com/) 로그인합니다.
1. **Azure Active Directory를**선택한 다음 **앱 등록을 선택합니다.** 등록된 앱 목록이 나타납니다.
1. 목록에서 앱을 선택합니다. 등록된 앱의 세부 정보를 보여주는 페이지가 나타납니다.
1. **관리**에서 **브랜딩을 선택합니다.**
1. 새 경로로 **홈 페이지 URL을** 업데이트합니다.

   ![홈 페이지 URL 필드를 보여주는 등록된 앱의 브랜딩 페이지](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. **저장**을 선택합니다.

## <a name="change-the-home-page-with-powershell"></a>PowerShell을 사용하여 홈페이지 변경

PowerShell을 사용하여 앱의 홈 페이지를 구성하려면 다음을 수행해야 합니다.

1. Azure AD PowerShell 모듈을 설치합니다.
1. 앱의 ObjectId 값을 찾습니다.
1. PowerShell 명령을 사용하여 앱의 홈 페이지 URL을 업데이트합니다.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

PowerShell을 사용하여 사용자 지정 홈페이지 URL을 정의하기 전에 Azure AD PowerShell 모듈을 설치합니다.Graph API 엔드포인트를 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)에서 패키지를 다운로드할 수 있습니다.

패키지를 설치하려면 다음 단계를 따르세요.

1. 표준 PowerShell 창을 열고 다음 명령을 실행합니다.

   ```powershell
   Install-Module -Name AzureAD
   ```

    관리자가 아닌 사용자로 명령을 실행하는 경우 `-scope currentuser` 옵션을 사용합니다.

1. 설치하는 동안 **Y를** 선택하여 Nuget.org 두 개의 패키지를 설치합니다. 두 패키지가 모두 필요합니다.

### <a name="find-the-objectid-of-the-app"></a>앱의 ObjectId 찾기

표시 이름 또는 홈 페이지로 앱을 검색하여 앱의 ObjectId를 가져옵니다.

1. 동일한 PowerShell 창에서 Azure AD 모듈을 가져옵니다.

   ```powershell
   Import-Module AzureAD
   ```

1. 테넌트 관리자로 Azure AD 모듈에 로그인합니다.

   ```powershell
   Connect-AzureAD
   ```

1. 앱을 찾습니다. 이 예제에서는 PowerShell을 사용하여 `SharePoint`의 표시 이름을 가진 앱을 검색하여 ObjectId를 찾습니다.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   여기에 표시된 것과 비슷한 결과가 표시되어야 합니다. 다음 섹션에서 사용할 ObjectId GUID를 복사합니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   또는 모든 앱 목록을 가져오고, 특정 표시 이름 또는 홈 페이지가 있는 앱 목록을 검색하고, 앱이 발견되면 앱의 ObjectId를 복사할 수 있습니다.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>홈페이지 URL 업데이트

홈 페이지 URL을 만들고 해당 값으로 앱을 업데이트합니다. 동일한 PowerShell 창을 계속 사용하거나 새 PowerShell 창을 사용하는 경우 을 사용하여 `Connect-AzureAD`Azure AD 모듈에 다시 로그인합니다. 그러고 나서 다음 단계를 수행합니다.

1. 이전 섹션에서 복사한 ObjectId 값을 보유하는 변수를 만듭니다. (이 SharePoint 예제에서 사용되는 ObjectId 값을 앱의 ObjectId 값으로 바꿉니다.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 다음 명령을 실행하여 올바른 앱이 있는지 확인합니다. 출력은 이전[섹션(앱의 ObjectId 찾기)에서](#find-the-objectid-of-the-app)본 출력과 동일해야 합니다.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. 변경 내용을 저장하기 위해 비어 있는 애플리케이션 개체를 만듭니다.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. 홈페이지 URL을 원하는 값으로 설정합니다. 값은 게시된 앱의 하위 도메인 경로여야 합니다. 예를 들어 홈페이지 URL을 `https://sharepoint-iddemo.msappproxy.net/`에서 `https://sharepoint-iddemo.msappproxy.net/hybrid/`로 변경하면 앱 사용자가 사용자 지정 홈페이지로 바로 이동합니다.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. 홈 페이지를 업데이트합니다.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 변경이 성공적임을 확인하려면 2단계에서 다음 명령을 다시 실행합니다.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   이 예제에서는 출력이 다음과 같이 표시됩니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. 앱을 다시 시작하여 홈 페이지가 예상대로 첫 번째 화면으로 표시되는지 확인합니다.

> [!NOTE]
> 앱에 대한 변경 내용으로 인해 홈페이지 URL을 다시 설정할 수도 있습니다. 홈 페이지 URL을 다시 설정하는 경우 이 섹션의 단계를 반복하여 재설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정](application-proxy-integrate-with-sharepoint-server.md)
- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md)