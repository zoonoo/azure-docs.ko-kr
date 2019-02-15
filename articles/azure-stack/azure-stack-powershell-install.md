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
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 89e75afd3b9001f7a0b8a027744ef71c8bb69690
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299567"
---
# <a name="install-powershell-for-azure-stack"></a>Azure Stack 용 PowerShell 설치

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

클라우드를 사용 하려면 Azure Stack 호환 PowerShell 모듈을 설치 해야 합니다. 호환성 라는 기능을 통해 사용 가능 *API 프로필*합니다.

API 프로필에는 Azure 및 Azure Stack의 버전 차이 관리 하는 방법을 제공 합니다. API 버전 프로필을는 특정 API 버전을 사용 하 여 Azure Resource Manager PowerShell 모듈의 집합입니다. 각 클라우드 플랫폼에 지원 되는 API 버전 프로필 집합이 있습니다. 예를 들어, Azure Stack 지 원하는 특정 프로필 버전을 같은 **2018-03-01-하이브리드**합니다. 프로필을 설치할 때 지정된 된 프로필에 해당 하는 Azure Resource Manager PowerShell 모듈 설치 됩니다.

Azure Stack 호환 PowerShell 모듈 인터넷에 연결 된, 부분적으로 연결 됨 또는 연결이 끊긴 시나리오를 설치할 수 있습니다. 이 문서에서는 이러한 시나리오에 대 한 Azure Stack 용 PowerShell을 설치 하는 자세한 지침을 안내 합니다.

## <a name="1-verify-your-prerequisites"></a>1. 프로그램 필수 구성 요소 확인

Azure Stack 및 PowerShell을 사용 하 여 시작 하기 전에 다음 필수 조건이 있어야 합니다.

- **PowerShell 버전 5.0** 버전을 확인 하려면 **$PSVersionTable.PSVersion** 비교 합니다 **주요** 버전입니다. PowerShell 5.0가 없는 경우에 따라 합니다 [Windows PowerShell 설치](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)합니다.

  > [!Note]
  > PowerShell 5.0에는 Windows 컴퓨터에 필요합니다.

- **관리자 권한 명령 프롬프트에서 Powershell을 실행**합니다.
  관리자 권한으로 PowerShell을 실행 해야 합니다.

- **PowerShell 갤러리 액세스** 에 액세스 해야 합니다 [PowerShell 갤러리](https://www.powershellgallery.com)합니다. 갤러리는 PowerShell 콘텐츠에 대 한 중앙 리포지토리입니다. 합니다 **PowerShellGet** 모듈 검색, 설치, 업데이트 및 모듈, DSC 리소스, 역할 기능 및 PowerShell 갤러리 및 다른 개인에서 스크립트와 같은 PowerShell 아티팩트를 게시에 대 한 cmdlet 포함 되어 있습니다. 리포지토리입니다. 연결이 끊긴된 시나리오의 PowerShell을 사용 하는 경우에 인터넷에 연결 된 컴퓨터에서 리소스를 검색 하 고 연결이 끊어진된 컴퓨터에 액세스할 수 있는 위치에 저장 해야 합니다.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. PowerShell 갤러리 내게 필요한 옵션 유효성 검사

PSGallery 리포지토리로 등록 된 경우의 유효성을 검사 합니다.

> [!Note]  
> 이 단계는 인터넷 액세스가 필요 합니다.

관리자 권한 PowerShell 프롬프트를 열고 다음 cmdlet을 실행 합니다.

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

리포지토리 등록 되지 않은 경우 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 합니다.

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. 기존 버전의 Azure Stack PowerShell 모듈을 제거 합니다.

필요한 버전을 설치 하기 전에 모든 이전에 설치 된 Azure Stack AzureRM PowerShell 모듈을 제거 하는 있는지를 확인 합니다. 다음 두 가지 방법 중 하나를 사용 하 여 해당 데이터를 제거할 수 있습니다.

1. 기존 AzureRM PowerShell 모듈을 제거 하려면 모든 활성 PowerShell 세션을 닫고 다음 cmdlet을 실행 합니다.

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    오류가 발생 하는 경우와 같은 '모듈을 이미 사용 중인', 모듈을 사용 하는 위의 스크립트를 다시 실행 하 여 PowerShell 세션을 닫으십시오.

2. 로 시작 하는 모든 폴더를 삭제 `Azure` 나 `Azs.` 에서 합니다 `C:\Program Files\WindowsPowerShell\Modules` 및 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 폴더입니다. 이러한 폴더를 삭제 하면 모든 기존 PowerShell 모듈을 제거 합니다.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. 연결: 인터넷에 연결 된 Azure Stack 용 PowerShell 설치

Azure Stack에 필요 합니다 **2018-03-01-하이브리드** Azure Stack 1808 이후 버전에 대 한 API 버전 프로필입니다. 설치 하 여 프로필을 사용할 수는 **AzureRM.Bootstrapper** 모듈입니다. 또한 AzureRM 모듈에도 설치 해야 Azure Stack 관련 PowerShell 모듈. API 버전 프로필 및 필요한 Azure Stack PowerShell 모듈에 따라 달라 집니다 Azure Stack의 버전에는 실행 합니다.

설치에는 세 가지 단계가 있습니다.

1. Azure Stack의 버전에 따라 Azure Stack PowerShell 설치
2. 추가 저장소 기능을 사용 하도록 설정
3. PowerShell의 설치를 확인 합니다.

### <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell 설치

개발용 워크스테이션에서 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.

- Azure Stack 1901 이상:

    ```PowerShell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > Azure Stack 모듈 버전 1.7.0는 주요 변경 내용입니다. Azure Stack에서 마이그레이션하도록 1.6.0 하세요 합니다 [마이그레이션 가이드](https://aka.ms/azspshmigration170)합니다.

- Azure Stack 1811:

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Azure Stack 1810 또는 이전 버전:

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Azure PowerShell에서 업그레이드 하는 **2017-03-09-프로필** 를 **2018-03-01-하이브리드**를 참조 하세요 합니다 [마이그레이션 가이드](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md)합니다.

### <a name="enable-additional-storage-features"></a>추가 저장소 기능을 사용 하도록 설정

사용 하 여 추가 저장소 기능 (연결 된 섹션에서 설명)을 다운로드 하 고 다음 패키지도 설치를 확인 합니다.

```PowerShell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>PowerShell의 설치를 확인 합니다.

다음 명령을 실행 하 여 설치를 확인 합니다.

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

성공적으로 설치 하는 경우에 AzureRM 및 azurestack의 경우 모듈을 출력에 표시 됩니다.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. 연결 끊김. 인터넷 연결 없이 PowerShell을 설치 합니다.

연결이 끊긴된 시나리오에서 먼저 인터넷에 연결 된 컴퓨터에 PowerShell 모듈을 다운로드 하 고 설치를 위한 Azure Stack 개발 키트를 전송 합니다.

인터넷에 연결 된 컴퓨터에 로그인 한 다음 스크립트를 사용 하 여 Azure Stack의 버전에 따라 Azure Resource Manager 및 azurestack의 경우 패키지를 다운로드 합니다.

설치에는 네 가지 단계가 있습니다.

1. 연결 된 컴퓨터에 Azure Stack PowerShell 설치
2. 추가 저장소 기능을 사용 하도록 설정
3. 연결이 끊어진된 워크스테이션에 PowerShell 패키지를 전송 합니다.
4. PowerShell의 설치를 확인 합니다.


### <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell 설치

- Azure Stack 1901 이상입니다.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > Azure Stack 모듈 버전 1.7.0는 주요 변경 내용입니다. AzureStack 마이그레이션할 1.6.0 하세요 합니다 [마이그레이션 가이드](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)합니다.


  - Azure Stack 1811 또는 이전 버전입니다.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 또는 이전 버전입니다.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

### <a name="enable-additional-storage-features"></a>추가 저장소 기능을 사용 하도록 설정

사용 하 여 추가 저장소 기능 (연결 된 섹션에서 설명)을 다운로드 하 고 다음 패키지도 설치를 확인 합니다.

```PowerShell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>사용자의 워크스테이션에 패키지 추가

1. USB 장치에 다운로드 한 패키지를 복사 합니다.

2. 연결이 끊어진된 워크스테이션에 로그인 하 고 워크스테이션에서 위치로 USB 장치에서 패키지를 복사 합니다.

3. 이제이 위치를 기본 저장소로 등록 하 고이 리포지토리에서 AzureRM 및 azurestack의 경우 모듈을 설치 합니다.

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>PowerShell의 설치를 확인 합니다.

다음 명령을 실행 하 여 설치를 확인 합니다.

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. 프록시 서버를 사용 하도록 PowerShell 구성

프록시 서버를 인터넷에 액세스 해야 하는 시나리오에서는 먼저 기존 프록시 서버를 사용 하는 PowerShell을 구성 해야 합니다.

1. 관리자 권한 PowerShell 프롬프트를 엽니다.
2. 다음 명령을 실행합니다.

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>다음 단계

 - [GitHub에서 Azure Stack 도구 다운로드](azure-stack-powershell-download.md)
 - [Azure Stack 사용자의 PowerShell 환경 구성](user/azure-stack-powershell-configure-user.md)
 - [Azure Stack 운영자의 PowerShell 환경 구성](azure-stack-powershell-configure-admin.md)
 - [Azure Stack에서 API 버전 프로필 관리](user/azure-stack-version-profiles.md)