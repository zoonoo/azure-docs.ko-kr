---
title: Azure Stack의 다중 테 넌 트
description: Azure Stack에서 여러 Azure Active directory를 지 원하는 방법을 알아봅니다
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/6/2018
ms.author: patricka
ms.reviewer: bryanr
ms.openlocfilehash: 2861b0d1b7ac24a8e881ff052b865ca0384a55d6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464811"
---
# <a name="multi-tenancy-in-azure-stack"></a>Azure Stack의 다중 테 넌 트

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에서 서비스를 사용 하도록 여러 Azure Active Directory (Azure AD) 테 넌 트에서 사용자를 지 원하는 Azure Stack을 구성할 수 있습니다. 예를 들어 다음 시나리오를 고려할 수 있습니다.

- Azure Stack이 설치 되어 있는 서비스 관리자 contoso.onmicrosoft.com 것입니다.
- Mary가 게스트 사용자가 있는 fabrikam.onmicrosoft.com, 디렉터리 관리자입니다.
- Mary의 회사 회사에서 IaaS 및 PaaS 서비스를 받고 게스트 디렉터리 (fabrikam.onmicrosoft.com)에서 사용자가 로그인 하 고 contoso.onmicrosoft.com에서 Azure Stack 리소스를 사용할 수 있도록 해야 합니다.

이 가이드에서는 필요한이 시나리오의 컨텍스트에서 Azure Stack에서 다중 테 넌 트를 구성 하는 단계를 제공 합니다. 이 시나리오에서는 메리에 로그인 하 여 Contoso의 Azure Stack 배포에서 서비스를 사용 하는 Fabrikam의 사용자를 사용 하도록 설정 하는 단계를 완료 해야 합니다.  

## <a name="enable-multi-tenancy"></a>다중 테넌트 사용

필수 구성 요소에 맞게 Azure Stack에서 다중 테 넌 트를 구성 하기 전에 몇 가지 있습니다.
  
 - Azure Stack (Contoso)에 설치 된 디렉터리 및 게스트 디렉터리 (Fabrikam)과 메리 관리 단계를 조정 해야 합니다.  
 - 했는지 [설치](azure-stack-powershell-install.md) 하 고 [구성](azure-stack-powershell-configure-admin.md) Azure Stack 용 PowerShell.
 - [Azure Stack Tools를 다운로드할](azure-stack-powershell-download.md), 연결 및 Id 모듈을 가져옵니다.

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

### <a name="configure-azure-stack-directory"></a>Azure Stack 디렉터리 구성

이 섹션에서는 Azure Stack Fabrikam Azure AD 디렉터리 테 넌 트의 로그인을 허용 하도록 구성할 수 있습니다.

온 보 딩 게스트 디렉터리 테 넌 트 (Fabrikam)에 사용자를 허용 하 고 서비스 주체 게스트 디렉터리 테 넌 트에서 Azure Resource Manager를 구성 하 여 Azure Stack에 있습니다.

Contoso.onmicrosoft.com의 서비스 관리자는 다음 명령을 실행합니다.

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>게스트 디렉터리를 구성 합니다.

Azure Stack 관리자가 한 번 / 연산자가 Azure Stack과 함께 사용할 Fabrikam 디렉터리를 사용 하도록 설정, Mary Fabrikam의 디렉터리 테 넌 트를 사용 하 여 Azure Stack을 등록 해야 합니다.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>게스트 디렉터리를 사용 하 여 Azure Stack 등록

Mary는 디렉터리 관리자에 게 Fabrikam의 게스트 디렉터리 fabrikam.onmicrosoft.com에서 다음 명령을 실행 합니다.

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> Azure Stack 관리자에 게 나중에 새 서비스 또는 업데이트 설치를 하는 경우이 스크립트를 다시 실행 해야 합니다.
>
> 언제 든 지 디렉터리에 Azure Stack 응용 프로그램의 상태를 확인 하려면 다시이 스크립트를 실행 합니다.
>
> 새 (1808 업데이트에서 도입 된) Managed Disks에 Vm을 만드는 문제가 나타난 경우 **디스크 리소스 공급자** 이 스크립트를 다시 실행할 수 필요한 추가 되었습니다.

### <a name="direct-users-to-sign-in"></a>직접 사용자가에 로그인

과 메리 온 보 딩 Mary의 디렉터리에 단계를 완료 했으므로 Mary Fabrikam 사용자가 로그인을 보낼 수 있습니다.  Fabrikam 사용자 (즉, fabrikam.onmicrosoft.com 접미사를 사용 하 여 사용자)가 방문 하 여 로그인 https://portal.local.azurestack.external합니다.  

Mary 모든 안내 [외래 주체](../role-based-access-control/rbac-and-directory-admin-roles.md) Fabrikam 디렉터리 (즉, fabrikam.onmicrosoft.com의 접미사를 붙이지 말고 Fabrikam 디렉터리의 사용자)에 사용 하 여 로그인 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com합니다.  이 URL을 사용 하지 않는 경우 해당 기본 디렉터리 (Fabrikam)에 전송 되는 이러한 및 관리자 동의 하지 않은 되었다는 오류가 발생 합니다.

## <a name="disable-multi-tenancy"></a>다중 테 넌 트를 사용 하지 않도록 설정

Azure Stack에서 여러 테 넌 트를 하지 않으려는 경우 다음 단계를 순서 대로 수행 하 여 다중 테 넌 트를 비활성화할 수 없습니다.

1. 실행 (이 시나리오에서 Mary) 게스트 디렉터리의 관리자로 *등록 취소 AzsWithMyDirectoryTenant*합니다. Cmdlet는 새 디렉터리에서 모든 Azure Stack 응용 프로그램을 제거합니다.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. 실행 (사용자이 시나리오에서), Azure Stack의 서비스 관리자로 *등록 취소 AzSGuestDirectoryTenant*합니다. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > 사용 안 함 다중 테 넌 트 단계를 순서 대로 수행 되어야 합니다. 단계 #1 단계 # 2를 먼저 완료 하는 경우 실패 합니다.

## <a name="next-steps"></a>다음 단계

- [위임 된 공급자 관리](azure-stack-delegated-provider.md)
- [Azure Stack의 주요 개념](azure-stack-key-features.md)
- [클라우드 서비스 공급자로서 Azure Stack의 사용량 및 청구 관리](azure-stack-add-manage-billing-as-a-csp.md)
- [사용량 및 청구에 대한 테넌트를 Azure Stack에 추가](azure-stack-csp-howto-register-tenants.md)
