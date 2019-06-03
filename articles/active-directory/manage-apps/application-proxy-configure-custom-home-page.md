---
title: Azure AD 애플리케이션 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정 | Microsoft Docs
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
ms.openlocfilehash: 0f4e71bd7fd7e0ed9a220619995ba108fdccabe4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66233758"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정

이 문서에는 사용자 안내 하는 사용자 지정 홈 페이지에 앱을 구성 하는 방법을 설명 합니다. 응용 프로그램 프록시를 사용 하 여 앱을 게시할 때 내부 URL을 설정 하면 사용할 수 있지만 때로는 사용자가 처음 보게 되는 페이지입니다. 사용자 지정 홈페이지 설정 사용자 앱에 액세스할 때 오른쪽 페이지를 가져옵니다. 사용자는 사용자 지정 홈 페이지를 보려면 설정 하는, Azure Active Directory 액세스 패널 또는 Office 365 앱 시작 관리자에서 앱에 액세스 하는지 여부에 관계 없이 합니다.

사용자 앱이 시작 될 때 기본적으로 게시 된 앱의 루트 도메인 URL로 이동 됩니다. 방문 페이지는 일반적으로 홈페이지 URL로 설정됩니다. 앱 내 특정 페이지에 앱 사용자를 하려는 경우 사용자 지정 홈페이지 URL을 정의 하려면 Azure AD PowerShell 모듈을 사용 합니다.

회사는 사용자 지정 홈 페이지를 설정 하는 이유를 설명 하는 한 가지 시나리오는 다음과 같습니다.

- 사용자가 회사 네트워크 내부에 `https://ExpenseApp/login/login.aspx` 에 로그인 하 여 앱에 액세스 합니다.
- 사용 하 여 앱을 게시 하면 응용 프로그램 프록시가 폴더 구조의 최상위 수준에서 액세스 해야 하는 기타 자산 (예: 이미지) 했으므로 `https://ExpenseApp` 내부 URL로 합니다.
- 기본 외부 URL은 `https://ExpenseApp-contoso.msappproxy.net`에 로그인 페이지에 외부 사용자를 사용 하지 않습니다.
- 설정 하려는 `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` 홈페이지 URL로 대신 따라서 외부 사용자 페이지가 표시 됩니다 로그인 먼저 합니다.

>[!NOTE]
>사용자에게 게시된 응용 프로그램에 대한 액세스 권한을 제공하면 [Azure AD 액세스 패널](../user-help/my-apps-portal-end-user-access.md) 및 [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)에 앱이 표시됩니다.

## <a name="before-you-start"></a>시작하기 전에

홈페이지 URL을 설정하기 전에 다음 요구 사항에 유의하세요.

- 지정 된 경로 루트 도메인 URL의 하위 도메인 경로 여야 합니다.

  예를 들어, 루트 도메인 URL이 `https://apps.contoso.com/app1/`에서 구성한 홈 페이지 URL을 시작 해야 `https://apps.contoso.com/app1/`합니다.

- 게시된 앱을 변경하는 경우 이로 인해 홈페이지 URL 값을 다시 설정할 수 있습니다. 나중에 앱을 업데이트할 경우 홈페이지 URL을 다시 확인하고 필요한 경우 업데이트해야 합니다.

Azure portal을 통해 또는 PowerShell을 사용 하 여 홈 페이지 URL을 설정할 수 있습니다.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure Portal에서 홈페이지 변경

Azure AD 포털을 통해 앱의 홈 페이지 URL을 변경 하려면 다음이 단계를 수행 합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 선택 **Azure Active Directory**를 차례로 **앱 등록**합니다. 등록 된 앱 목록이 표시 됩니다.
3. 목록에서 앱을 선택 합니다. 등록된 된 앱의 세부 정보를 보여 주는 페이지가 나타납니다.
4. 아래 **관리**를 선택 **브랜드**합니다.
5. 업데이트를 **홈페이지 URL** 새 경로 사용 하 여 합니다.

   ![홈페이지 URL 필드를 표시 하는 등록 된 앱에 대 한 브랜딩 페이지](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)
 
6. **저장**을 선택합니다.

## <a name="change-the-home-page-with-powershell"></a>PowerShell을 사용하여 홈페이지 변경

PowerShell을 사용 하 여 앱의 홈 페이지를 구성 하려면:

1. Azure AD PowerShell 모듈을 설치합니다.
2. 앱의 ObjectId 값을 찾습니다.
3. PowerShell 명령을 사용 하 여 앱의 홈 페이지 URL을 업데이트 합니다.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

PowerShell을 사용하여 사용자 지정 홈페이지 URL을 정의하기 전에 Azure AD PowerShell 모듈을 설치합니다. Graph API 엔드포인트를 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)에서 패키지를 다운로드할 수 있습니다.

패키지를 설치하려면 다음 단계를 따르세요.

1. 표준 PowerShell 창을 열고 다음 명령을 실행합니다.

   ```powershell
   Install-Module -Name AzureAD
   ```

    관리자가 아닌 사용자로 명령을 실행하는 경우 `-scope currentuser` 옵션을 사용합니다.

2. 설치 도중 Nuget.org에서 두 개의 패키지를 설치하려면 **Y**를 선택합니다. 두 개의 패키지가 모두 필요합니다.

### <a name="find-the-objectid-of-the-app"></a>앱의 ObjectId 찾기

해당 표시 이름 또는 홈 페이지에서 앱을 검색 하 여 앱의 ObjectId를 가져옵니다.

1. 동일한 PowerShell 창에서 Azure AD 모듈을 가져옵니다.

   ```powershell
   Import-Module AzureAD
   ```

2. 테넌트 관리자로 Azure AD 모듈에 로그인합니다.

   ```powershell
   Connect-AzureAD
   ```

3. 앱을 찾습니다. 이 예제에서는 PowerShell을 사용 하 여 앱의 표시 이름으로 검색 하 여 ObjectId를 찾으려고 `SharePoint`합니다.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   여기에 표시된 것과 비슷한 결과가 표시되어야 합니다. 다음 섹션에서 사용할 ObjectId GUID를 복사 합니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   또는 수만 모든 앱 목록을 가져올, 특정 표시 이름 또는 홈 페이지를 사용 하 여 앱의 목록을 검색 및 앱이 발견 되 면 앱의 ObjectId를 복사 합니다.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>홈페이지 URL 업데이트

홈 페이지 URL을 만들고 해당 값을 사용 하 여 앱을 업데이트 합니다. 동일한 PowerShell 창에서 사용 하 여 계속 하거나 새 PowerShell 창을 사용 하는 경우 다시 사용 하 여 Azure AD 모듈에 로그인 `Connect-AzureAD`합니다. 그러고 나서 다음 단계를 수행합니다.

1. 이전 섹션에서 복사한 ObjectId 값을 보유할 변수를 만듭니다. (앱의 ObjectId 값이 SharePoint 예제에서 사용 되는 ObjectId 값을 바꿉니다.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. 다음 명령을 실행 하 여 올바른 앱 했는지 확인 합니다. 출력을 이전 섹션에서 살펴본 출력 동일 해야 합니다. ([앱의 ObjectId 찾기](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. 변경 내용을 저장하기 위해 비어 있는 애플리케이션 개체를 만듭니다.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. 홈페이지 URL을 원하는 값으로 설정합니다. 값은 게시된 앱의 하위 도메인 경로여야 합니다. 예를 들어 홈페이지 URL을 `https://sharepoint-iddemo.msappproxy.net/`에서 `https://sharepoint-iddemo.msappproxy.net/hybrid/`로 변경하면 앱 사용자가 사용자 지정 홈페이지로 바로 이동합니다.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. 홈 페이지의 업데이트를 확인 합니다.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. 변경 내용이 성공 했는지를 확인 하려면 2 단계에서 다음 명령의 다시 실행 합니다.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   예를 들어 출력 이제 같습니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. 예상 대로 홈 페이지에서 첫 번째 화면으로 표시 되는지 확인 하려면 앱을 다시 시작 합니다.

>[!NOTE]
>앱에 대한 변경 내용으로 인해 홈페이지 URL을 다시 설정할 수도 있습니다. 홈 페이지 URL을 다시 설정하는 경우 이 섹션의 단계를 반복하여 재설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시를 사용하여 SharePoint에 원격 액세스 사용하도록 설정](application-proxy-integrate-with-sharepoint-server.md)
- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](application-proxy-add-on-premises-application.md)