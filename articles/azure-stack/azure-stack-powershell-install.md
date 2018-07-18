---
title: Azure Stack 용 PowerShell 설치 | Microsoft Docs
description: Azure Stack 용 PowerShell을 설치 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 09d5842f349917be0e5d94d919b0e9630347284b
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035482"
---
# <a name="install-powershell-for-azure-stack"></a>Azure Stack 용 PowerShell 설치

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 호환 가능한 Azure PowerShell 모듈은 Azure Stack을 사용 해야 합니다. 이 가이드에서는 안내 Azure Stack 용 PowerShell을 설치 하는 데 필요한 단계입니다. 다음 단계를 인터넷에 연결 된 환경에 적용 됩니다. 연결이 끊어진된 환경에 대 한 페이지의 아래쪽으로 스크롤하십시오.

이 문서에서는 Azure Stack 용 PowerShell을 설치 하는 지침에 자세한 하 고 있습니다.

> [!Note]  
> 다음 단계를 필요 이상 PowerShell 5.0입니다. 버전을 확인 하려면 $PSVersionTable.PSVersion을 실행 하 고 비교 합니다 **주요** 버전입니다. PowerShell 5.0가 없는 경우에 따라 합니다 [링크](https://docs.microsoft.com/en-us/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) PowerShell 5.0으로 업그레이드 합니다.

Azure Stack에 대 한 PowerShell 명령이 PowerShell 갤러리를 통해 설치 됩니다. PSGallery 리포지토리로 등록 된 경우의 유효성을 검사 하 고 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 하려면 다음 절차를 사용할 수 있습니다.

```PowerShell
#requires -Version 5
#requires -RunAsAdministrator
#requires -Module PowerShellGet

Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop 

Get-PSRepository -Name "PSGallery"
```

리포지토리 등록 되지 않은 경우 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 합니다.

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> 이 단계는 인터넷 액세스가 필요 합니다. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>기존 버전의 Azure Stack PowerShell 모듈을 제거 합니다.

필요한 버전을 설치 하기 전에 모든 이전에 설치 된 Azure Stack AzureRM PowerShell 모듈을 제거 하는 있는지를 확인 합니다. 다음 두 가지 방법 중 하나를 사용 하 여 해당 데이터를 제거할 수 있습니다.

 - 기존 AzureRM PowerShell 모듈을 제거 하려면 모든 활성 PowerShell 세션을 닫습니다 하 고 다음 명령을 실행 합니다.

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - "Azure"로 시작 하는 모든 폴더를 삭제 합니다 `C:\Program Files\WindowsPowerShell\Modules` 고 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 폴더입니다. 이러한 폴더를 삭제 하면 모든 기존 PowerShell 모듈을 제거 합니다.

다음 섹션에서는 Azure Stack 용 PowerShell을 설치 하는 데 필요한 단계를 설명 합니다. 연결이 끊긴된 시나리오 또는 연결 된, 부분적으로 연결에서 작동 하는 Azure Stack에서 PowerShell은 설치할 수 있습니다.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>(사용 하 여 인터넷 연결) 연결 된 경우에 Azure Stack PowerShell 모듈 설치

Azure Stack 호환 AzureRM 모듈은 API 버전 프로필을 통해 설치 됩니다. Azure Stack에 필요 합니다 **2017-03-09-프로필** AzureRM.Bootstrapper 모듈을 설치 하 여 사용할 수 있는 API 버전 프로필입니다. API 버전 프로필 및 이들에 의해 제공 하는 cmdlet에 대 한 자세한 참조를 [API 버전 프로필 관리](user/azure-stack-version-profiles.md)합니다. AzureRM 모듈 외에 Azure Stack 관련 PowerShell 모듈을 설치 해야 합니다. 개발용 워크스테이션에서 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

설치를 확인 하려면 다음 명령을 실행 합니다.

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

성공적으로 설치 하는 경우에 AzureRM 및 azurestack의 경우 모듈을 출력에 표시 됩니다.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>(사용 하 여 제한 된 인터넷 연결)는 연결 되어 있지 않거나 부분적으로 연결 된 시나리오에서 Azure Stack PowerShell 모듈 설치

연결이 끊긴된 시나리오에서 먼저 인터넷에 연결 된 컴퓨터에 PowerShell 모듈을 다운로드 하 고 설치를 위한 Azure Stack 개발 키트를 전송 합니다.

> [!IMPORTANT]  
> Azure Stack 1.3.0 PowerShell 모듈의 릴리스 주요 변경 내용 목록이 포함 되어 있습니다. 1.2.11 업그레이드할 버전 참조는 [마이그레이션 가이드](https://aka.ms/azspowershellmigration)합니다.

1. 인터넷에 연결 되어 하 고 다음 스크립트는 AzureRM 다운로드 및 azurestack의 경우 패키지를 사용 하 여 로컬 컴퓨터에 있는 컴퓨터에 로그인 합니다.

   ```PowerShell 
  #requires -Version 5
  #requires -RunAsAdministrator
  #requires -Module PowerShellGet
  #requires -Module PackageManagement
  
  Import-Module -Name PowerShellGet -ErrorAction Stop
  Import-Module -Name PackageManagement -ErrorAction Stop

   $Path = "<Path that is used to save the packages>"

   Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11

   Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > 1804 이상 Azure Stack 업데이트를 사용 하 여 실행 하지 않는 경우 변경 합니다 **requiredversion** 매개 변수 값을 `1.2.11`입니다. 

2. USB 장치에 다운로드 한 패키지를 복사 합니다.

3. 워크스테이션에 로그인 하 고 워크스테이션에서 위치로 USB 장치에서 패키지를 복사 합니다.

4. 이제이 위치를 기본 저장소로 등록 하 고이 리포지토리에서 AzureRM 및 azurestack의 경우 모듈을 설치 해야 합니다.

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>프록시 서버를 사용 하도록 PowerShell 구성

프록시 서버를 인터넷에 액세스 해야 하는 시나리오에서는 먼저 기존 프록시 서버를 사용 하도록 PowerShell을 구성 해야 합니다.

1. 관리자 권한 PowerShell 프롬프트를 엽니다.
2. 다음 명령을 실행합니다.

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>다음 단계

 - [GitHub에서 Azure Stack 도구 다운로드](azure-stack-powershell-download.md)
 - [Azure Stack 사용자의 PowerShell 환경 구성](user/azure-stack-powershell-configure-user.md)  
 - [Azure Stack 운영자의 PowerShell 환경 구성](azure-stack-powershell-configure-admin.md) 
 - [Azure Stack에서 API 버전 프로필 관리](user/azure-stack-version-profiles.md)  
