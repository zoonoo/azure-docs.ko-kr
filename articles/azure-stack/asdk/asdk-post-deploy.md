---
title: 게시 배포 구성에는 Azure Stack 개발 키트 ASDK () | Microsoft Docs
description: Azure Stack 개발 키트 (ASDK)를 설치한 후에 권장 되는 구성 변경 내용이 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 7e052c8d1674cc95a376de5ba6e20ca63b4dd72c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245527"
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK 설치 후 구성 작업

후 [설치 된 Azure Stack 개발 키트 ASDK ()](asdk-install.md), 몇 가지 권장 되는 설치 후 구성 ASDK 호스트 컴퓨터의 AzureStack\AzureStackAdmin로 로그인 하는 동안 변경할 수 있도록 해야 합니다. 

## <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell 설치

Azure Stack 호환 가능한 Azure PowerShell 모듈은 Azure Stack을 사용 해야 합니다.

Azure Stack에 대 한 PowerShell 명령이 PowerShell 갤러리를 통해 설치 됩니다. PSGallery 리포지토리를 등록 하려면 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 합니다.

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Azure Stack 호환 AzureRM 모듈을 지정 하려면 API 버전 프로필을 사용할 수 있습니다.  API 버전 프로필에는 Azure 및 Azure Stack의 버전 차이 관리 하는 방법을 제공 합니다. API 버전 프로필을는 특정 API 버전을 사용 하 여 AzureRM PowerShell 모듈의 집합입니다. 합니다 **AzureRM.Bootstrapper** 모듈이 PowerShell 갤러리를 통해 사용할 수 있는 API 버전 프로필을 사용 하는 데 필요한 PowerShell cmdlet을 제공 합니다.

ASDK 호스트 컴퓨터에 인터넷 연결 없이 최신 Azure Stack PowerShell 모듈을 설치할 수 있습니다.

> [!IMPORTANT]
> 필요한 버전을 설치 하기 전에 확인 하면 [기존 Azure PowerShell 모듈을 제거](../azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules)합니다.

- **인터넷에 연결 된** ASDK 호스트 컴퓨터에서. 개발 키트 설치에서 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.

  - Azure Stack 1811 이상:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - Azure Stack 1808 이상:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

  - Azure Stack 1807 또는 이전 버전:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    
    # Install Azure Stack Module Version 1.4.0.
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

  - Azure Stack 1803 또는 이전 버전:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
      Install-Module -Name AzureRm.BootStrapper

      # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
      Use-AzureRmProfile -Profile 2017-03-09-profile -Force

      # Install Azure Stack Module Version 1.2.11
      Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

  성공적으로 설치 하는 경우에 AzureRM 및 azurestack의 경우 모듈을 출력에 표시 됩니다.

- **인터넷 연결 없이** ASDK 호스트 컴퓨터에서. 연결이 끊긴 시나리오에서는 먼저 다음 PowerShell 명령을 사용 하 여 인터넷 연결 되어 있는 컴퓨터에 PowerShell 모듈을 다운로드 해야 합니다.

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  # AzureRM for 1808 requires 2.3.0, for prior versions use 1.2.11
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  # AzureStack requires 1.5.0 for version 1808, 1.4.0 for versions after 1803, and 1.2.11 for versions before 1803
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  다음으로, ASDK 컴퓨터에 다운로드 한 패키지를 복사할 위치를 기본 저장소로 등록 및이 리포지토리에서 AzureRM 및 azurestack의 경우 모듈을 설치:

    ```PowerShell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Azure Stack 도구 다운로드

[Azurestack의 경우 도구](https://github.com/Azure/AzureStack-Tools) 는 관리 및 Azure Stack에 리소스를 배포 하기 위한 PowerShell 모듈을 호스팅하는 GitHub 리포지토리입니다. 이러한 도구를 얻으려면 GitHub 리포지토리를 복제 하거나 AzureStack 도구 폴더에서 다음 스크립트를 실행 하 여 다운로드 합니다.

  ```PowerShell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>ASDK 설치 유효성 검사

ASDK 배포 성공 했는지를 확인 하려면 다음이 단계를 수행 하 여 테스트 AzureStack cmdlet을 사용할 수 있습니다.

1. ASDK 호스트 컴퓨터의 AzureStack\AzureStackAdmin로 로그인 합니다.
2. (PowerShell ISE 없습니다 ()를 관리자 권한으로 PowerShell을 엽니다.
3. 실행: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. 실행: `Test-AzureStack`

테스트를 완료 하려면 몇 분 동안. 설치에 성공한 경우 출력이 같습니다.

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

오류가 있는 경우 도움말을 보려면 문제 해결 단계를 수행 합니다.

## <a name="reset-the-password-expiration-policy"></a>암호 만료 정책 다시 설정 

개발 키트 호스트에 대 한 암호에 평가 기간이 종료 되기 전에 만료 되지 않습니다는 있는지,는 ASDK를 배포한 후 다음이 단계를 수행 합니다.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Powershell에서 암호 만료 정책을 변경 하려면

승격된 된 Powershell 콘솔에서 명령을 실행 합니다.

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>암호 만료 정책을 수동으로 변경 하려면

1. 개발 키트 호스트에서 엽니다 **그룹 정책 관리** GPMC (합니다. MMC ()로 이동한 **그룹 정책 관리** – **포리스트: azurestack.local** – **도메인** – **azurestack.local**합니다.
2. 마우스 오른쪽 단추로 클릭 **기본 도메인 정책** 누릅니다 **편집**합니다.
3. 그룹 정책 관리 편집기에서 이동할 **컴퓨터 구성** – **정책을** – **Windows 설정** – **보안 설정**– **계정 정책** – **암호 정책**합니다.
4. 오른쪽 창에서 두 번 클릭 **최대 암호 사용 기간**합니다.
5. 에 **최대 암호 사용 기간 속성** 대화 상자에서 변경 합니다 **암호 만료** 값을 **180**를 클릭 하 고 **확인**.

![그룹 정책 관리 콘솔](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>다중 테넌트 사용

Azure AD를 사용 하 여 배포 해야 [다중 테 넌 트 활성화](../azure-stack-enable-multitenancy.md#enable-multi-tenancy) ASDK 설치 합니다.

> [!NOTE]  
> Azure Stack 등록에 사용 된 도메인 이름을 포털에 추가 되어야 하는 Azure Stack 포털에 로그인 할 Azure Stack 등록을 사용 하는 것 이외의 도메인의 관리자 또는 사용자 계정이 사용 되 면 url입니다. 예를 들어 fabrikam.onmicrosoft.com Azure Stack에 등록 된 경우 사용자 계정 로그인 이며 admin@contoso.com에 사용자 포털에 로그인 하는 데 url: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com합니다.

## <a name="next-steps"></a>다음 단계

[Azure 사용 하 여 ASDK 등록](asdk-register.md)
