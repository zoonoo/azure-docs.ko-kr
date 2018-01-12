---
title: "PowerShell 설치 및 구성에 대 한 Azure 스택 퀵 스타트 | Microsoft Docs"
description: "설치 하 고 Azure 스택에 대 한 PowerShell 구성에 대해 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: mabrigg
ms.openlocfilehash: ca61562607da274f0e0c7f504d1a24723210f2f7
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Azure 스택에서 PowerShell과 함께 시작 및 실행

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이 빠른 시작을 사용 하면 설치 하 고 PowerShell과 함께 Azure 스택 환경을 구성할 수 있습니다. 이 문서에서 제공 하는 스크립트는으로 범위가 제한 된 **Azure 스택 연산자** 만 합니다.

이 문서는에 설명 된 단계를 간략하게는 [PowerShell을 설치]( azure-stack-powershell-install.md), [도구 다운로드]( azure-stack-powershell-download.md), 및 [Azure 스택 운영자의 PowerShell 환경을구성]( azure-stack-powershell-configure-admin.md) 문서입니다. 이 항목의 스크립트를 사용 하 여 Azure Active Directory 또는 Active Directory Federation Services (AD FS)을 함께 배포 되는 Azure 스택 환경에 대 한 PowerShell을 설정할 수 있습니다.  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Azure Active Directory 기반 배포에 대 한 PowerShell을 설정 합니다.

VPN을 통해 연결 되어 있는 경우 Azure 스택 개발 키트 또는 Windows 기반 외부 클라이언트에 로그인 합니다. 관리자 권한 PowerShell ISE 세션을 열고 다음 스크립트를 실행 합니다. 업데이트 해야는 **TenantName**, **ArmEndpoint**, 및 **GraphAudience** 환경 구성에 대 한 필요에 따라 변수:

> [!IMPORTANT]
> 릴리스 1.2.11 AzureRM PowerShell 모듈의 주요 변경 내용 목록이 포함 되어 있습니다. 1.2.10에서 업그레이드 하 버전에서는 참조는 [마이그레이션 가이드](https://aka.ms/azspowershellmigration)합니다.

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>AD FS 기반 배포에 대 한 PowerShell을 설정 합니다.

Azure 스택 인터넷에 연결 되어 있을 때 작동 하는 경우 다음 스크립트를 사용할 수 있습니다. 그러나 Azure 스택 인터넷 연결 없이 작동 하는 경우 사용 하 여는 [PowerShell 설치 방법은 연결이 끊어진](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) PowerShell을 구성 하기 위한 cmdlet이이 스크립트에 표시 된 것 처럼 동일한 상태로 유지 됩니다. VPN을 통해 연결 되어 있는 경우 Azure 스택 개발 키트 또는 Windows 기반 외부 클라이언트에 로그인 합니다. 관리자 권한 PowerShell ISE 세션을 열고 다음 스크립트를 실행 합니다. 업데이트 해야는 **ArmEndpoint** 및 **GraphAudience** 환경 구성에 대 한 필요에 따라 변수:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>연결 테스트

PowerShell을 구성 했으므로 리소스 그룹을 만들어 구성을 테스트할 수 있습니다.

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

리소스 그룹을 만든 후의 **의 프로비저닝 상태** 속성이 **Succeeded**합니다.

## <a name="next-steps"></a>다음 단계

* [설치 및 CLI를 구성 합니다.](azure-stack-connect-cli.md)

* [템플릿 개발](user/azure-stack-develop-templates.md)







