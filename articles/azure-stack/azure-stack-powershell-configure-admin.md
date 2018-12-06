---
title: Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결 | Microsoft Docs
description: Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결 하는 방법 알아보기
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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b961fac00ba43eb1b44acc46c6f60fa0f3a10877
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957081"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack PowerShell을 사용 하 여 제안, 계획, 할당량 및 경고 만들기와 같은 리소스 관리를 구성할 수 있습니다. 이 항목에서는 운영자 환경을 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소를 실행 합니다 [개발 키트](./asdk/asdk-connect.md#connect-with-rdp) 또는 Windows 기반 외부 클라이언트의 경우 [VPN 통해 ASDK 연결할](./asdk/asdk-connect.md#connect-with-vpn)합니다. 

 - 설치할 [Azure Stack 호환 Azure PowerShell 모듈](azure-stack-powershell-install.md)합니다.  
 - 일관 된 [azure storage: 차이점 및 고려 사항](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>운영자 환경을 구성 하 고 Azure Stack에 로그인

PowerShell을 사용 하 여 Azure Stack 운영자 환경을 구성 합니다. 다음 스크립트 중 하나를 실행 합니다: 사용자 고유의 환경 구성을 사용 하 여 Azure AD tenantName, GraphAudience 끝점 및 ArmEndpoint 값을 바꿔야 합니다.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>연결 테스트

모든 항목을 가져온 했으므로 설정 PowerShell을 사용 하 여 Azure Stack에서 리소스를 만듭니다. 예를 들어, 응용 프로그램에 대 한 리소스 그룹을 만들고 가상 머신 추가 수 있습니다. 다음 명령을 사용 하 여 명명 된 리소스 그룹을 만듭니다 **MyResourceGroup**합니다.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>다음 단계

 - [Azure Stack용 템플릿 개발](user/azure-stack-develop-templates.md)
 - [PowerShell을 사용하여 템플릿 배포](user/azure-stack-deploy-template-powershell.md)