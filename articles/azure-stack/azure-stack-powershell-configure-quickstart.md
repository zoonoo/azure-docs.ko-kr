---
title: PowerShell 설치 및 구성에 대 한 Azure Stack 빠른 시작 | Microsoft Docs
description: 설치 하 고 Azure Stack 용 PowerShell 구성에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: mabrigg
ms.openlocfilehash: db253c921354ea132dc6b043dcb6f0b96cdbfd88
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368174"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Azure Stack에서 PowerShell을 사용 하 여 즉시 가동 및 실행

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 빠른 시작 설치 및 PowerShell을 사용 하 여 Azure Stack 환경을 구성할 수 있습니다. 이 문서에서 제공 하는 스크립트 범위를 지정 합니다 **Azure Stack 운영자** 만 합니다.

이 문서는에 설명 된 단계를 간략하게 합니다 [PowerShell 설치]( azure-stack-powershell-install.md), [도구를 다운로드]( azure-stack-powershell-download.md), 및 [AzureStack운영자의PowerShell환경구성]( azure-stack-powershell-configure-admin.md) 문서. 이 문서의 스크립트를 사용 하면 Azure Active Directory 또는 Active Directory Federation Services (AD FS)를 사용 하 여 배포 된 Azure Stack 환경에 대 한 PowerShell을 설정할 수 있습니다.  

## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Azure Active Directory 기반 배포에 대 한 PowerShell 설정  

VPN을 통해 연결 되어 있는 경우 Azure Stack Development Kit 또는 Windows 기반 외부 클라이언트에 로그인 합니다. 관리자 권한 PowerShell ISE 세션을 열고 다음 스크립트를 실행 합니다.

업데이트 해야 합니다 **TenantName**를 **ArmEndpoint**, 및 **GraphAudience** 환경 구성에 대해 필요에 따라 변수:

```PowerShell  
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following commands:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module
Get-Module Azs.* -ListAvailable | Uninstall-Module -force

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Azure Stack의 버전에 대 한 API 프로필과 관리자 모듈을 로드 합니다.

  - Azure Stack 1808 이상입니다.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
  ```

  - Azure Stack 1807 또는 이전 버전입니다.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

  - Azure Stack 1804 또는 이전 버전입니다.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11
  ```

Azure Stack 도구를 다운로드 하 고 연결 합니다.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId -AADTenantName $TenantName -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>AD FS 기반 배포에 대 한 PowerShell 설정

인터넷에 연결 하는 경우 Azure Stack을 작동 하는 경우 다음 스크립트를 사용할 수 있습니다. 그러나 인터넷 연결 없이 Azure Stack을 운영 하는 경우 사용 하 여 합니다 [PowerShell을 설치 하는 방식으로 연결이 끊긴](azure-stack-powershell-install.md) PowerShell을 구성 하는 cmdlet이이 스크립트에 표시 된 것과 동일 하 게 유지 됩니다. VPN을 통해 연결 되어 있는 경우 Azure Stack Development Kit 또는 Windows 기반 외부 클라이언트에 로그인 합니다. 관리자 권한 PowerShell ISE 세션을 열고 다음 스크립트를 실행 합니다. 업데이트 해야 합니다 **ArmEndpoint** 하 고 **GraphAudience** 환경 구성에 대해 필요에 따라 변수:

```PowerShell  

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Azure Stack의 버전에 대 한 API 프로필과 관리자 모듈을 로드 합니다.

  - Azure Stack 1808 이상입니다.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - Azure Stack 1807 또는 이전 버전입니다.

    > [!Note]  
    1.2.11 업그레이드할 버전 참조는 [마이그레이션 가이드](https://aka.ms/azspowershellmigration)합니다.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - Azure Stack 1804 또는 이전 버전입니다.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

Azure Stack 도구를 다운로드 하 고 연결 합니다.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>연결 테스트

PowerShell을 사용 하도록 구성한 했으므로 리소스 그룹을 만들어 구성을 테스트할 수 있습니다.

```PowerShell  
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> 리소스 그룹을 지정 하려면 구독의 리소스 그룹을 하도록 해야 합니다. 구독에 대 한 자세한 내용은 참조 하세요. [계획, 제안, 할당량 및 구독 개요](azure-stack-plan-offer-quota-overview.md)

리소스 그룹을 만든 후 합니다 **프로 비전 상태** 속성이 **Succeeded**합니다.

## <a name="next-steps"></a>다음 단계

 - [CLI 설치 및 구성](azure-stack-connect-cli.md)
 - [템플릿 개발](user/azure-stack-develop-templates.md)
