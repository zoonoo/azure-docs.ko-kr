---
title: Office 365 그룹에 대한 명명 정책 추가 빠른 시작 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 새 사용자를 추가하거나 기존 사용자를 삭제하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4105fa17041c7cefd1387d1ee50c177b8c55fc9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651289"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>빠른 시작: Azure Active Directory의 그룹에 대한 명명 정책

이 빠른 시작에서는 사용자가 만든 Office 365 그룹의 Azure AD(Azure Active Directory) 테넌트에서 명명 정책을 설정할 것이며, 이렇게 하면 테넌트의 그룹을 정렬하고 검색할 수 있습니다. 예를 들어 명명 정책을 사용하여 다음과 같은 일을 할 수 있습니다.

* 그룹의 기능, 멤버 자격, 지리적 영역 또는 그룹을 만든 사람 정보를 전달합니다.
* 주소록의 그룹을 범주화합니다.
* 그룹 이름 및 별칭에 특정 단어를 사용하지 않도록 차단합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="install-powershell-cmdlets"></a>PowerShell cmdlet 설치

PowerShell 명령을 실행하기 전에 Windows PowerShell용 그래프 모듈에 대한 Azure Active Directory PowerShell의 이전 버전을 제거하고 [그래프용 Azure Active Directory PowerShell - 공용 미리 보기 릴리스 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)을 설치해야 합니다. 

1. 관리자로 Windows PowerShell 앱을 엽니다.
2. AzureADPreview의 이전 버전을 제거합니다.
  

   ```powershell
   Uninstall-Module AzureADPreview
   ```

3. AzureADPreview의 최신 버전을 설치합니다.
  

   ```powershell
   Install-Module AzureADPreview
   ```

   신뢰할 수 없는 리포지토리에 액세스하라는 메시지가 표시되면 **Y**를 입력합니다. 새 모듈을 설치하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="set-up-naming-policy"></a>명명 정책 설정

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>1단계: PowerShell cmdlet을 사용하여 로그인

1. Windows PowerShell 앱을 엽니다. 상승된 권한이 필요 없습니다.

2. 다음 명령을 실행하여 cmdlet 실행을 준비합니다.
  

   ```powershell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   **계정에 로그인** 화면이 열리면 관리자 계정 및 암호를 입력하여 서비스에 연결하고 **로그인**을 선택합니다.

3. [그룹 설정을 구성하는 Azure Active Directory cmdlet](groups-settings-cmdlets.md)의 단계에 따라 이 테넌트에 대한 그룹 설정을 만듭니다.

### <a name="step-2-view-the-current-settings"></a>2단계: 현재 설정 보기

1. 현재 명명 정책 설정을 봅니다.
  

   ```powershell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id
   ```

  
2. 현재 그룹 설정을 표시합니다.
  

   ```powershell
   $Setting.Values
   ```

  

### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>3단계: 명명 정책 및 사용자 지정 차단 단어 설정

1. Azure AD PowerShell에서 그룹 이름 접두사 및 접미사를 설정합니다. 기능이 제대로 작동하려면 [GroupName]이 설정에 포함되어야 합니다.
  

   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```

  
2. 제한하려는 사용자 정의 차단 단어를 설정합니다. 다음 예제는 고유한 사용자 정의 단어를 추가하는 방법을 보여줍니다.
  

   ```powershell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```

  
3. 다음 예제와 같이 새 정책을 적용하기 위한 설정을 저장합니다.
  

   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

  
이것으로 끝입니다. 명명 정책을 설정하고 사용자 지정 차단 단어를 추가했습니다.

## <a name="clean-up-resources"></a>리소스 정리

1. Azure AD PowerShell에서 그룹 이름 접두사 및 접미사를 비웁니다.
  

   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```

  
2. 사용자 지정 차단 단어를 비웁니다.
  

   ```powershell
   $Setting["CustomBlockedWordsList"]=""
   ```

  
3. 설정을 저장합니다.
  

   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 PowerShell cmdlet을 사용하여 Azure AD 테넌트에 대한 명명 정책을 설정하는 방법을 알아보았습니다.

기술 제약 조건, 사용자 지정 차단 단어를 추가하는 방법 또는 Office 365 앱의 최종 사용자 환경에 대한 자세히 알아보려면 명명 정책에 대해 자세히 다루는 그 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [명명 정책 모든 세부 정보](groups-naming-policy.md)