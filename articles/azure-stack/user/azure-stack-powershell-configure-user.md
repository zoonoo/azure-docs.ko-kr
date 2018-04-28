---
title: Azure 스택 사용자의 PowerShell 환경 구성 | Microsoft Docs
description: Azure 스택 사용자의 PowerShell 환경 구성
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/26/2017
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: e17fc85de3d11034889c39fd205b7ddc8cb344cc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Azure 스택 사용자의 PowerShell 환경 구성

Azure 스택 사용자로 Azure 스택 개발 키트 PowerShell 환경을 구성할 수 있습니다. Azure 스택 제공 서비스 리소스와 같은 구독을 관리 하려면 PowerShell을 사용할 수를 구성한 후 가상 컴퓨터를 만들고, 등 Azure 리소스 관리자 템플릿을 배포 합니다. 이 항목을 사용자 환경, 클라우드 연산자 환경에 대 한 PowerShell을 설정 하려는 경우 참조를 사용 하면 범위 지정은 [Azure 스택 운영자의 PowerShell 환경을 구성](../azure-stack-powershell-configure-admin.md) 문서. 

## <a name="prerequisites"></a>필수 조건 

다음 필수 구성 요소에서 실행 하거나는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  
* 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다. 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>사용자 환경을 구성 하 고 Azure 스택에 로그인

Azure 스택 (AAD tenantName, GraphAudience 끝점 및 환경 구성에 따라 ArmEndpoint 값을 교체할 수 있는지 확인)에 대 한 PowerShell을 구성 하려면 다음 스크립트 중 하나를 실행 하는 배포 (Azure AD 또는 AD FS) 유형을 기반으로 합니다.

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) 기반의 배포
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) 기반의 배포 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>리소스 공급자 등록

에 포털을 통해 배포 된 모든 리소스가 없는 새로 만든된 사용자 구독을 처리할 때 리소스 공급자 자동으로 등록 되어 있지 않습니다. 다음 스크립트를 사용 하 여 해당를 명시적으로 등록 해야 합니다.

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>연결 테스트

모든 했으므로 설정, 보겠습니다 PowerShell을 사용 하 여 Azure 스택 내에서 리소스를 만듭니다. 예를 들어 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 컴퓨터를 추가할 수 있습니다. 다음 명령을 사용 하 여 "MyResourceGroup" 라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>다음 단계
* [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)
* [PowerShell을 사용하여 템플릿 배포](azure-stack-deploy-template-powershell.md)
