---
title: "Azure 스택 운영자의 PowerShell 환경 구성 | Microsoft Docs"
description: "Azure 스택 운영자의 PowerShell 환경을 구성 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: sngun
ms.openlocfilehash: 54f0305afb94b8b19ea7fada837a73f7c6a1963c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Azure 스택 운영자의 PowerShell 환경 구성

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 연산자로 Azure 스택 개발 키트 PowerShell 환경을 구성할 수 있습니다. 를 구성한 후에 경고, 등 관리 제공, 계획, 할당량 만들기와 같은 Azure 스택 리소스를 관리할 수 PowerShell을 사용할 수 있습니다. 이 항목 적용 되는 환경, 사용자 환경에 대 한 PowerShell을 설정 하려는 경우 참조할 클라우드 연산자와 함께 사용할 범위가 [Azure 스택 사용자의 PowerShell 환경을 구성](user/azure-stack-powershell-configure-user.md) 항목입니다. 

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소에서 실행 하거나는 [개발 키트](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Windows 기반 외부 클라이언트에서 있다면 [VPN을 통해 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* 설치 [Azure 스택 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  
* 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다.  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>운영자 환경을 구성 하 고 Azure 스택에 로그인

Powershell (해야 AAD tenantName, GraphAudience 끝점 및 사용자 환경에 따라 ArmEndpoint 값 대체 Azure 스택 운영자 환경을 구성 하려면 다음 스크립트 중 하나를 실행 하는 배포 (Azure AD 또는 AD FS)의 형식에 따라 구성):

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) 기반의 배포
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience 

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) 기반의 배포
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


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

설정 하는 모든 작업을 두었습니다 이제 보겠습니다 Azure 스택 내에서 리소스를 만드는 PowerShell을 사용 합니다. 예를 들어 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 컴퓨터를 추가할 수 있습니다. 다음 명령을 사용 하 여 "MyResourceGroup" 라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>다음 단계
* [Azure Stack용 템플릿 개발](user/azure-stack-develop-templates.md)
* [PowerShell을 사용하여 템플릿 배포](user/azure-stack-deploy-template-powershell.md)
