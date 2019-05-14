---
title: PowerShell을 사용하여 Windows Virtual Desktop 미리 보기 서비스 주체 만들기 및 역할 할당 - Azure
description: Windows Virtual Desktop 미리 보기에서 PowerShell을 사용하여 서비스 주체를 만들고 역할을 할당하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 93725fc9d77552d779378d0c14294a5bbb11c926
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146137"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>자습서: PowerShell을 사용하여 서비스 주체 만들기 및 역할 할당

서비스 주체는 특정 목적에 대한 역할 및 권한을 할당하도록 Azure Active Directory에서 만들 수 있는 ID입니다. Windows Virtual Desktop 미리 보기에서 서비스 주체를 만들 수 있습니다.

- 특정 Windows Virtual Desktop 관리 작업 자동화
- 모든 Windows Virtual Desktop Azure Resource Manager 템플릿을 실행하는 경우 MFA 필요 사용자 대신 자격 증명으로 사용

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Active Directory에 서비스 주체 만들기
> * Windows Virtual Desktop에서 역할 할당 만들기
> * 서비스 주체를 사용하여 Windows Virtual Desktop에 로그인

## <a name="prerequisites"></a>필수 조건

서비스 주체 및 역할 할당을 만들기 전에 먼저 다음 세 가지 작업을 수행해야 합니다.

1. AzureAD 모듈을 설치합니다. 모듈을 설치하려면 관리자 권한으로 PowerShell을 실행하고 다음 cmdlet을 실행합니다.

    ```powershell
    Install-Module AzureAD
    ```

2. 세션에 관련된 값으로 대체된 따옴표 안의 값으로 다음 cmdlet을 실행합니다.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. 동일한 PowerShell 세션에서 이 문서의 모든 지침을 따릅니다. 창을 닫고 나중에 반환하는 경우 작동하지 않을 수도 있습니다.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory에 서비스 주체 만들기

PowerShell 세션에서 필수 구성 요소를 충족했으면 다음 PowerShell cmdlet을 실행하여 Azure의 다중 테넌트 보안 주체를 만듭니다.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Windows Virtual Desktop 미리 보기에서 역할 할당 만들기

서비스 주체를 만들었으므로 이제 이를 사용하여 Windows Virtual Desktop에 로그인할 수 있습니다. 역할 할당을 만들 수 있는 권한이 있는 계정으로 로그인해야 합니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

다음 PowerShell cmdlet을 실행하여 Windows Virtual Desktop에 연결하고 서비스 주체에 대한 역할 할당을 만듭니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>서비스 주체를 사용하여 로그인

서비스 주체에 대한 역할 할당을 만든 후 이제 서비스 주체가 다음 cmdlet을 실행하여 Windows Virtual Desktop에 로그인할 수 있도록 해야 합니다.

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

로그인하고 나면 서비스 주체를 사용하여 몇 가지 Windows Virtual Desktop PowerShell cmdlet을 테스트하여 제대로 작동하는지 확인합니다.

## <a name="view-your-credentials-in-powershell"></a>PowerShell에서 자격 증명 보기

PowerShell 세션을 종료하기 전에 자격 증명을 확인하고 나중에 참조할 수 있도록 적어두어야 합니다. 암호는 이 PowerShell 세션을 닫은 후에는 검색할 수 없기 때문에 특히 중요합니다.

기록해야 하는 세 가지 자격 증명과 이를 가져오기 위해 실행해야 하는 cmdlet은 다음과 같습니다.

- 암호:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- 테넌트 ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- 애플리케이션 ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 서비스 주체를 만들고 이를 사용하여 Windows Virtual Desktop에 로그인하는 방법을 알아보았습니다. Windows Virtual Desktop에 로그인하는 방법을 자세히 알아보려면 Windows Virtual Desktop 방법에 대한 연결을 계속 진행하세요.

- [Windows 10 또는 Windows 7에서 연결](connect-windows-7-and-10.md)
- [웹 브라우저에서 연결](connect-web.md)
