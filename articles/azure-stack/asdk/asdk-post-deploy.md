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
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e057d7a649397083240e9f67080808a3057c7f50
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139327"
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK 설치 후 구성 작업

이후에 [는 Azure Stack 개발 키트 ASDK ()를 설치](asdk-install.md), 몇 가지 권장 되는 설치 후 구성을 변경 해야 합니다.

## <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell 설치

Azure Stack 호환 가능한 Azure PowerShell 모듈은 Azure Stack을 사용 해야 합니다.

Azure Stack에 대 한 PowerShell 명령이 PowerShell 갤러리를 통해 설치 됩니다. PSGallery 리포지토리를 등록 하려면 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 합니다.

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

Azure Stack 호환 AzureRM 모듈을 지정 하려면 API 버전 프로필을 사용할 수 있습니다.  API 버전 프로필에는 Azure 및 Azure Stack의 버전 차이 관리 하는 방법을 제공 합니다. API 버전 프로필을는 특정 API 버전을 사용 하 여 AzureRM PowerShell 모듈의 집합입니다. 합니다 **AzureRM.Bootstrapper** 모듈이 PowerShell 갤러리를 통해 사용할 수 있는 API 버전 프로필을 사용 하는 데 필요한 PowerShell cmdlet을 제공 합니다.

ASDK 호스트 컴퓨터에 인터넷 연결 없이 최신 Azure Stack PowerShell 모듈을 설치할 수 있습니다.

> [!IMPORTANT]
> 필요한 버전을 설치 하기 전에 확인 하면 [기존 Azure PowerShell 모듈을 제거](.\.\azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules)합니다.

- **인터넷에 연결 된** ASDK 호스트 컴퓨터에서. 개발 키트 설치에서 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  # Install Azure Stack Module Version 1.4.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.
  Install-Module -Name AzureStack -RequiredVersion 1.4.0 

  ```

  성공적으로 설치 하는 경우에 AzureRM 및 azurestack의 경우 모듈을 출력에 표시 됩니다.

- **인터넷 연결 없이** ASDK 호스트 컴퓨터에서. 연결이 끊긴 시나리오에서는 먼저 다음 PowerShell 명령을 사용 하 여 인터넷 연결 되어 있는 컴퓨터에 PowerShell 모듈을 다운로드 해야 합니다.

  ```PowerShell
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
  # Install Azure Stack Module Version 1.4.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.  
    -RequiredVersion 1.4.0
  ```

  다음으로, ASDK 컴퓨터에 다운로드 한 패키지를 복사할 위치를 기본 저장소로 등록 및이 리포지토리에서 AzureRM 및 azurestack의 경우 모듈을 설치:

    ```PowerShell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository `
      -Name $RepoName `
      -SourceLocation $SourceLocation `
      -InstallationPolicy Trusted

    Install-Module AzureRM `
      -Repository $RepoName

    Install-Module AzureStack `
      -Repository $RepoName
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
  expand-archive master.zip `
    -DestinationPath . `
    -Force

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

## <a name="activate-the-administrator-and-tenant-portals"></a>관리자 및 테 넌 트 포털 활성화
Azure AD를 사용 하는 배포 후 Azure Stack 관리자 및 테 넌 트 포털 모두 활성화 해야 합니다. 이 활성화 된 Azure Stack 포털과 Azure Resource Manager 정확한 권한을 제공 (동의 페이지에 나열 된) 모든 사용자에 대 한 디렉터리의에 동의 합니다.

- 관리자 포털을 이동 https://adminportal.local.azurestack.external/guest/signup정보를 읽고 클릭 **Accept**합니다. 를 수락 하면 디렉터리 테 넌 트 관리자가 아닌 서비스 관리자를 추가할 수 있습니다.

- 테 넌 트 포털에 대 한 이동 https://portal.local.azurestack.external/guest/signup정보를 읽고 클릭 **Accept**합니다. 를 수락 하면 디렉터리의 사용자는 테 넌 트 포털에 로그인 수 있습니다. 

> [!NOTE] 
> 포털 활성화 되지 않으면, 디렉터리 관리자만 로그인 하 고 포털을 사용할 수 있습니다. 다른 사용자가 로그인 하는 경우는 관리자 권한을 부여 받지 다른 사용자에 게 알리는 오류가 표시 됩니다. 관리자는 기본적으로 Azure Stack에 등록 하는 디렉터리에 속하지 않습니다, Azure Stack 디렉터리 활성화 URL에 추가 해야 합니다. Azure Stack은 fabrikam.onmicrosoft.com 관리 사용자를 등록 하는 경우는 예를 들어 admin@contoso.com, 이동할 https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com 포털을 활성화 하려면. 

## <a name="reset-the-password-expiration-policy"></a>암호 만료 정책 다시 설정 
개발 키트 호스트에 대 한 암호에 평가 기간이 종료 되기 전에 만료 되지 않습니다는 있는지,는 ASDK를 배포한 후 다음이 단계를 수행 합니다.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Powershell에서 암호 만료 정책 변경:
승격된 된 Powershell 콘솔에서 명령을 실행 합니다.

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>암호 만료 정책을 수동으로 변경 합니다.
1. 개발 키트 호스트에서 엽니다 **그룹 정책 관리** GPMC (합니다. MMC ()로 이동한 **그룹 정책 관리** – **포리스트: azurestack.local** – **도메인** – **azurestack.local**합니다.
2. 마우스 오른쪽 단추로 클릭 **기본 도메인 정책** 누릅니다 **편집**합니다.
3. 그룹 정책 관리 편집기에서 이동할 **컴퓨터 구성** – **정책을** – **Windows 설정** – **보안 설정**– **계정 정책** – **암호 정책**합니다.
4. 오른쪽 창에서 두 번 클릭 **최대 암호 사용 기간**합니다.
5. 에 **최대 암호 사용 기간 속성** 대화 상자에서 변경 합니다 **암호 만료** 값을 **180**를 클릭 하 고 **확인**.

![그룹 정책 관리 콘솔](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>다음 단계
[Azure 사용 하 여 ASDK 등록](asdk-register.md)
