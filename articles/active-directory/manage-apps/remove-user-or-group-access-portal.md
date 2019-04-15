---
title: Azure Active Directory에서 엔터프라이즈 앱의 사용자 또는 그룹 할당 제거 | Microsoft Docs
description: Azure Active Directory에서 엔터프라이즈 앱의 사용자 또는 그룹의 액세스 할당을 제거하는 방법
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97759ae992ebe38aa85e9b4724edeebb5285db4b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565702"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱의 사용자 또는 그룹 할당 제거
Azure Active Directory (Azure AD)에서 엔터프라이즈 응용 프로그램 중 하나에 할당 된 액세스에서 사용자 또는 그룹을 제거 하는 것이 쉽습니다. 엔터프라이즈 앱을 관리 하는 적절 한 권한이 필요 합니다. 및 디렉터리에 대 한 전역 관리자 여야 합니다.

> [!NOTE]
> Microsoft 애플리케이션(예: Office 365 앱)의 경우 PowerShell을 사용하여 엔터프라이즈 앱에 대한 사용자를 제거합니다.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 사용자 또는 그룹 할당을 어떻게 제거하나요?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
1. 에 **Azure Active Directory- *directoryname***  페이지 (즉, Azure AD 페이지를 관리 하는 디렉터리에 대 한), 선택 **엔터프라이즈 응용 프로그램**합니다.
1. 에 **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지를 관리할 수 있는 앱 목록이 표시 됩니다. 앱을 선택 합니다.
1. 에 ***appname*** 개요 페이지 (즉, 페이지 제목에서 선택된 된 앱의 이름)을 선택 **사용자 및 그룹**합니다.
1. ***appname*** **- 사용자 및 그룹 할당** 페이지에서 하나 이상의 사용자 또는 그룹을 선택한 다음, **제거** 명령을 선택합니다. 프롬프트에서 의사 결정을 확인합니다.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>PowerShell을 사용하여 엔터프라이즈 앱에 대한 사용자 또는 그룹 할당을 어떻게 제거하나요?
1. 관리자 권한 Windows PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE] 
    > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

1. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.
1. 다음 스크립트를 사용 하 여 응용 프로그램에서 사용자 및 역할을 제거 합니다.

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
   ## <a name="next-steps"></a>다음 단계

- [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
- [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](disable-user-sign-in-portal.md)
- [엔터프라이즈 앱의 이름 또는 로고 변경](change-name-or-logo-portal.md)
