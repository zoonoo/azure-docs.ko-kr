---
title: Azure 스택 용 PowerShell 설치 | Microsoft Docs
description: Azure 스택에 대 한 PowerShell을 설치 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F8D99A91-15B5-4073-BE07-A43514A6D2CF
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: mabrigg
ms.openlocfilehash: 70a2118ef0e26043f9f6a9cceb9d4a533d343556
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="install-powershell-for-azure-stack"></a>Azure 스택에 대 한 PowerShell을 설치 합니다.  

Azure 스택 호환 Azure PowerShell 모듈은 Azure 스택이 작동 해야 합니다. 이 가이드에서는 과정을 단계별로 Azure 스택에 대 한 PowerShell을 설치 하는 데 필요한 단계. VPN을 통해 연결 되어 있는 경우 Azure 스택 개발 키트 또는 Windows 기반 외부 클라이언트에서이 문서에 설명 된 단계를 사용할 수 있습니다.

이 문서에 Azure 스택에 대 한 PowerShell을 설치 하는 지침을 설명 합니다. 그러나 신속 하 게 설치 하 고 PowerShell을 구성 하려는 경우 "시작 하 고 실행할 PowerShell과 함께" 문서에서 제공 되는 스크립트를 사용할 수 있습니다. 

> [!NOTE]
> 다음 단계에는 PowerShell 5.0 있어야합니다. 사용 중인 버전을 확인 하려면 $PSVersionTable.PSVersion 실행 하 고 "주" 버전을 비교 합니다.

Azure 스택에 대 한 PowerShell 명령이 PowerShell 갤러리를 통해 설치 됩니다. PSGallery 리포지토리를 등록 하려면 관리자 권한 PowerShell 세션에서에서 열거나 개발 키트 Windows 기반 외부 클라이언트에서 VPN을 통해 연결 되 고 다음 명령을 실행 하는 경우:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>기존 버전의 PowerShell 제거 합니다.

필요한 버전을 설치 하기 전에 모든 기존 Azure PowerShell 모듈을 제거 해야 합니다. 다음 두 가지 방법 중 하나를 사용 하 여 해당 데이터를 제거할 수 있습니다.

* 기존 PowerShell 모듈을 제거 하려면 또는에 로그인 개발 키트를 통해 외부 액세스에 VPN 연결을 설정 하려는 경우. 모든 활성 PowerShell 세션을 닫은 다음 명령을 실행 합니다. 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* 또는에 로그인 개발 키트를 통해 외부 액세스에 VPN 연결을 설정 하려는 경우. "Azure"로 시작 하는 모든 폴더에서 삭제 된 `C:\Program Files (x86)\WindowsPowerShell\Modules` 및 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 폴더입니다. 이 폴더를 삭제 "AzureStackAdmin" 및 "전역" 사용자 범위에서 기존 PowerShell 모듈을 제거 합니다. 

다음 섹션에서는 Azure 스택에 대 한 PowerShell을 설치 하는 데 필요한 단계를 설명 합니다. 연결 된, 부분적으로 연결에서 운영 하는 Azure 스택 또는 연결이 끊긴된 시나리오에서 PowerShell은 설치할 수 있습니다. 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>인터넷 연결) (에 연결 된 시나리오에서 PowerShell 설치

Azure 스택 호환 AzureRM 모듈 API 버전 프로필을 통해 설치 됩니다. Azure 스택 필요는 **2017-03-09-프로필** AzureRM.Bootstrapper 모듈을 설치 하 여 액세스할 수 있는 API 버전 프로필입니다. API 버전 프로필 및 이들 프로그램에서 제공 하는 cmdlet에 대 한 자세한 내용은 참조는 [API 버전 프로필을 관리](azure-stack-version-profiles-powershell.md)합니다. AzureRM 모듈 외에 Azure 스택 관련 PowerShell 모듈을 설치 해야 합니다. 개발 워크스테이션에 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.



  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

설치를 확인 하려면 다음 명령을 실행 합니다.

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  설치에 성공한 경우 AzureRM 및 Azure 스택 모듈 출력에 표시 됩니다.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>제한 된 인터넷 연결) (에 연결 되어 있지 않거나 부분적으로 연결 된 시나리오에서 PowerShell을 설치

연결 되어 있지 않거나 부분적으로 연결 된 시나리오에서는 먼저 인터넷 연결 되어 있는 컴퓨터에 PowerShell 모듈을 다운로드 하 고 설치를 위한 Azure 스택 개발 키트를 전송 해야 합니다.

1. 인터넷에 연결 하 고 AzureRM, 다운로드 및 AzureStack 패키지를 위한 다음 스크립트를 사용 하 여 로컬 컴퓨터에 있는 컴퓨터에 로그인 합니다.

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11 
   ```

2. USB 장치를 통해 다운로드 한 패키지를 복사 합니다.

3. 개발 키트에 로그인 하 고 개발 키트의 위치로 USB 장치에서 패키지를 복사 합니다. 

4. 이제이 위치를 기본 저장소로 등록 하 고이 저장소에서 AzureRM 및 AzureStack 모듈을 설치 해야 합니다.

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>다음 단계

* [GitHub에서 Azure 스택 도구 다운로드](azure-stack-powershell-download.md)  
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
* [Azure 스택에서 API 버전 프로필을 관리](azure-stack-version-profiles-powershell.md)  
