---
title: "Azure AD 앱 프록시를 사용하여 게시된 응용 프로그램에 대해 사용자 지정 홈 페이지 설정 | Microsoft Docs"
description: "Azure AD 응용 프로그램 프록시 커넥터에 대한 기본 사항을 제공합니다."
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
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Azure AD 앱 프록시를 사용하여 게시된 응용 프로그램에 대해 사용자 지정 홈 페이지 설정

이 문서에서는 사용자가 Azure AD 액세스 패널 및 Office 365 앱 시작 관리자에서 응용 프로그램에 액세스할 때 사용자를 사용자 홈 페이지로 안내하도록 응용 프로그램을 구성하는 방법을 설명합니다.

>[!NOTE]
>응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.
> 
 
응용 프로그램 내 특정 페이지(예: https://expenseApp-contoso.msappproxy.net/login/login.aspx)에 사용자를 리디렉션하려는 경우, 해당 인스턴스에 대해 Azure AD Powershell 모듈을 사용하여 사용자 지정 홈 페이지 URL을 정의할 수 있습니다

>[!NOTE]
>게시된 응용 프로그램에 대한 사용자 액세스를 제공하면 [Azure AD 액세스 패널](active-directory-saas-access-panel-introduction.md) 및 [Office 365 앱 시작 관리자](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)에 앱이 표시됩니다. 
>

기본적으로 사용자가 앱을 시작하면 게시된 앱의 루트 도메인 URL로 이동됩니다. 일반적으로 방문 페이지는 홈 페이지 URL로 설정됩니다. 예를 들어 이 백 엔드 응용 프로그램 http://ExpenseApp의 경우 https://expenseApp-contoso.msappproxy.net으로 게시됩니다. 기본적으로 홈 페이지 URL은 https://expenseApp-contoso.msappproxy.net으로 설정됩니다.

## <a name="determine-the-home-page-url"></a>홈 페이지 URL 확인

홈 페이지 URL을 설정하기 전에 여러 항목을 알고 있어야 합니다.

* 사용자가 지정한 경로가 루트 도메인 URL의 하위 도메인 경로인지 확인해야 합니다.

 예를 들어 게시된 앱을 홈 페이지 URL https://intranet-contoso.msappproxy.net/에서 액세스할 수 있는 경우 사용자가 구성한 홈 페이지 URL은 https://intranet-contoso.msappproxy.net/으로 시작해야 합니다. 
 
* 홈 페이지 URL이 https://apps.contoso.com/app1/이면 홈 페이지 URL은 https://apps.contoso.com/app1/로 시작해야 합니다.

* 게시된 응용 프로그램을 변경하는 경우 홈 페이지 URL 값을 다시 설정할 수 있습니다. 따라서 응용 프로그램을 업데이트하기로 한 경우 홈 페이지 URL을 다시 확인하고 잠재적으로 업데이트해야 합니다.


다음 섹션에서는 게시된 응용 프로그램에 대해 사용자 지정 홈 페이지 URL을 설정하는 방법을 안내합니다. 

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD Powershell 모듈 설치

Powershell을 사용하여 사용자 지정 홈 페이지 URL을 정의하려면 먼저 Azure AD PowerShell 모듈의 비표준 패키지를 설치해야 합니다.  GRAPH API 끝점을 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)에서 이 패키지를 다운로드할 수 있습니다. 

**Powershell을 사용하여 패키지를 설치하려면**

1. 표준 PowerShell을 엽니다.
2. 다음 명령을 실행합니다.

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 관리자가 아닌 사용자로 실행하는 경우 _-scope currentuser_ 옵션을 사용해야 합니다.
3. 설치 중에는 Nuget.org의 두 패키지를 설치하려면 "Y"를 선택합니다.  패키지를 사용하기 위해서는 둘 다 필요합니다. 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Azure AD Powershell 모듈을 사용하여 사용자 지정 홈 페이지 URL 설정

이제 Azure AD Powershell 모듈이 설치되었고 간단한 두 단계를 사용하여 홈 페이지 URL을 설정할 준비가 되었습니다.

1. 업데이트하려는 응용 프로그램을 찾습니다.
2. 응용 프로그램에 대한 홈 페이지 URL을 업데이트합니다.

###<a name="step-1--find-the-objectid-of-the-application"></a>1단계 - 응용 프로그램의 ObjectID 찾기

먼저 응용 프로그램의 ObjectID를 가져온 후 홈 페이지에서 응용 프로그램을 검색합니다.

1. PowerShell을 엽니다.
2. Azure AD 모듈을 가져옵니다.
  
 ```
 Import-Module AzureAD
 ```
3. Azure AD 모듈에 로그인합니다.  아래 cmdlet을 사용하고 화면의 지시를 따릅니다. 테넌트 관리자로 로그인해야 합니다.
 
 ```
 Connect-AzureAD
 ```
4. 아래 cmdlet은 _sharepoint-iddemo_가 포함된 홈 페이지를 기반으로 응용 프로그램을 찾습니다. 편집하려는 앱입니다. 이 값은 응용 프로그램에 대해 작동하는 값으로 바꿔야 합니다.
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. 아래 응답과 유사한 결과가 표시되어야 합니다. GUID(아래 ObjectID 값)는 복사해야 할 항목입니다.
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. GUID(ObjectID) 값을 복사합니다. 이 값은 다음 단계에서 필요합니다.


###<a name="step-2--update-the-homepage-url"></a>2단계 - 홈 페이지 URL 업데이트

응용 프로그램 ID를 찾기 위해 수행했던 것과 동일한 PowerShell 모듈을 사용하여 홈 페이지 URL을 업데이트합니다. PowerShell에 로그인한 후 아래 단계를 따릅니다.

1. 올바른 응용 프로그램이 있는지 확인하고 _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_를 위의 1단계에서 복사한 응용 프로그램의 ObjectID 값으로 바꿉니다. 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 이제 응용 프로그램을 확인했음 다음과 같이 홈 페이지를 업데이트할 수 있습니다
 
2. 변경 내용을 저장하기 위한 비어 있는 응용 프로그램 개체를 만듭니다. 업데이트할 값을 저장하기만 하므로 실제로 아무 것도 생성되지 않습니다.
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. 홈 페이지를 원하는 값으로 설정합니다. 이 값은 게시된 응용 프로그램의 하위 도메인 경로여야 합니다. 예를 들어 _https://sharepoint-iddemo.msappproxy.net/_에서 _https://sharepoint-iddemo.msappproxy.net/hybrid/_로 변경하는 경우 사용자는 사용자 지정 홈 페이지로 직접 이동합니다.
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. 마지막으로 업데이트만 수행하면 됩니다. 위의 1단계에서 복사한 GUID를 사용해야 합니다.
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. 이제 응용 프로그램을 다시 시작하여 사용자 지정 홈 페이지를 확인하여 성공적으로 변경되었는지 확인해야 합니다.
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>응용 프로그램에 대해 수행한 변경 사항으로 홈 페이지 URL이 다시 설정될 수 있습니다. 이 경우 이 프로세스를 반복해야 합니다.

##<a name="next-steps"></a>다음 단계

[Azure AD 앱 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정](application-proxy-enable-remote-access-sharepoint.md)<br>
[Azure Portal에서 응용 프로그램 프록시 사용](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


