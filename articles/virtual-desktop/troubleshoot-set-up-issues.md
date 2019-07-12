---
title: Windows 가상 데스크톱 테 넌 트 및 호스트 풀 만들기-Azure
description: Windows 가상 데스크톱 테 넌 트 환경을 설치 하는 동안 풀의 테 넌 트 및 호스트 문제를 해결 하는 방법을 발급 합니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 7ec4e0ffd87c0ef73a551416d8a8cc672f095483
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786723"
---
# <a name="tenant-and-host-pool-creation"></a>테넌트 및 호스트 풀 만들기

이 문서에서는 Windows 가상 데스크톱 테 넌 트와 관련 된 세션 호스트 풀 인프라의 초기 설치 중 문제를 다룹니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise 다중 세션 이미지를 가져오는 중

Windows 10 Enterprise 다중 세션 이미지를 사용 하려면 선택 Azure Marketplace로 이동 **시작** > **Microsoft Windows 10** > 및 [에 대 한 Windows 10 Enterprise 가상 데스크톱 미리 보기, 버전 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)합니다.

![Windows 10 Enterprise 가상 데스크톱 미리 보기, 버전 1809 선택 스크린샷.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows 가상 데스크톱 테 넌 트 만들기

이 섹션에서는 Windows 가상 데스크톱 테 넌 트를 만들 때 잠재적인 문제를 다룹니다.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>오류: 관리 서비스를 쿼리할 권한이 없는 사용자

![관리 서비스를 쿼리할 권한이 없는 사용자는 PowerShell의 스크린 샷 창입니다.](media/UserNotAuthorizedNewTenant.png)

원시 오류 예:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**원인:** 로그인 한 사용자 자신의 Azure Active Directory에서 TenantCreator 역할이 할당 되지 않았습니다.

**해결 방법:** 지침을 따릅니다 [Azure Active Directory 테 넌 트에서 사용자에 게 TenantCreator 응용 프로그램 역할을 할당](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant)합니다. 지시를 따라 사용자 TenantCreator 역할에 할당 해야 합니다.

![스크린 샷의 TenantCreator 역할을 할당 합니다.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows 가상 데스크톱 세션 호스트 Vm 만들기

여러 가지 방법으로 세션 호스트 Vm을 만들 수 있습니다 하지만 원격 데스크톱 서비스/Windows 가상 데스크톱 팀만 VM을 Azure Resource Manager 템플릿에 관련 된 문제를 프로 비전을 지원 합니다. Azure Resource Manager 템플릿에 사용할 수 있습니다 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 하 고 [GitHub](https://github.com/)합니다.

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Windows 가상 데스크톱 – 호스트 풀 Azure Marketplace 제품을 프로 비전을 사용 하 여 문제

Windows Virtual Desktop – 프로 비전 호스트 풀 템플릿을 Azure Marketplace에서 사용할 수 있습니다.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>오류: GitHub에서 링크를 사용 하는 경우 메시지 계정 만들기"무료" 표시

![무료 계정을 만들려면 스크린샷입니다.](media/be615904ace9832754f0669de28abd94.png)

**원인 1:** Azure에 로그인 하는 데 사용 된 계정에 활성 구독이 없습니다. 또는 사용 되는 계정에 구독을 볼 수 있는 권한이 없습니다.

**1의 해결 방법:** 세션 호스트 Vm은 어디에 배포 참가자 (최소) 구독에 액세스할 수 있는 계정으로 로그인 합니다.

**원인 2:** 사용 중인 구독에는 Microsoft 클라우드 서비스 공급자 (CSP) 테 넌 트의 일부입니다.

**2 해결 방법:** 에 대 한 GitHub 위치로 이동 **만들기 및 프로 비전 새 Windows 가상 데스크톱 호스트 풀** 이러한 지침을 따릅니다.

1. 마우스 오른쪽 단추로 클릭 **Azure에 배포** 선택한 **복사 링크 주소**합니다.
2. 오픈 **메모장** 붙여넣습니다.
3. # 문자 앞 CSP 최종 고객 테 넌 트 이름을 삽입 합니다.
4. 열린 브라우저 및 Azure portal에서 새 링크 템플릿이 로드 됩니다.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager 템플릿 및 PowerShell Desired State Configuration (DSC) 오류

Azure Resource Manager 템플릿 및 PowerShell DSC의 실패 한 배포 문제를 해결 하려면 다음이 지침을 따릅니다.

1. 사용 하 여 배포 오류를 검토 [Azure Resource Manager 배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)합니다.
2. 배포에서 오류가 없는 경우 사용 하 여 활동 로그에서 오류를 검토 [리소스에 대 한 작업을 감사 하려면 활동 로그 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)합니다.
3. 오류 식별 되 면 오류 메시지 및 리소스를 사용 하 여 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) 문제를 해결 합니다.
4. 이전 배포 및 템플릿을 다시 배포 하는 다시 시도 중에 만든 모든 리소스를 삭제 합니다.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>오류: 배포 실패<hostname>/joindomain

![배포 실패 스크린샷입니다.](media/e72df4d5c05d390620e07f0d7328d50f.png)

원시 오류 예:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인 1:** 도메인에 Vm을 조인 하는 데 제공 된 자격 증명이 올바르지 않습니다.

**1의 해결 방법:** Vm을 도메인에 가입 되지에 대 한 "잘못 된 자격 증명" 오류를 참조 하세요 [세션 호스트 VM 구성을](troubleshoot-vm-configuration.md)합니다.

**원인 2:** 도메인 이름이 확인 되지 않습니다.

**2 해결 방법:** Vm을 도메인에 가입 되지에 대해 "도메인 이름이 확인 하지 않습니다." 오류를 참조 하세요 [세션 호스트 VM 구성을](troubleshoot-vm-configuration.md)합니다.


### <a name="error-your-deployment-failedunauthorized"></a>오류: 프로그램 배포 failed...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**원인:** 사용 중인 구독에는 고객 배포를 시도 하는 지역에 필요한 기능에 액세스할 수 없는 형식입니다. 예를 들어, MSDN, 무료, 또는 교육 구독이이 오류를 표시할 수 있습니다.

**해결 방법:** 필요한 기능에 액세스할 수 있는 사용자의 구독 유형이 나 지역을 변경 합니다.

### <a name="error-vmextensionprovisioningerror"></a>오류: VMExtensionProvisioningError

![스크린 샷의 프로그램을 배포 하지 못했습니다 터미널 프로 비전 상태를 사용 하 여 실패 했습니다.](media/7aaf15615309c18a984673be73ac969a.png)

**원인 1:** Windows 가상 데스크톱 환경 사용 하 여 일시적인 오류입니다.

**원인 2:** 연결을 사용 하 여 일시적인 오류입니다.

**해결 방법:** PowerShell을 사용 하 여 로그인 하 여 Windows 가상 데스크톱 환경 정상이 확인 합니다. 수동으로 VM 등록을 완료 [PowerShell을 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)합니다.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>오류: 지정 된 관리자 사용자 이름은 허용 되지 않습니다.

![지정 된 관리자는 허용 되지 않습니다 스크린샷 배포 하지 못했습니다.](media/f2b3d3700e9517463ef88fa41875bac9.png)

원시 오류 예:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**원인:** 제공 된 암호 (관리자, 관리자, 루트) 금지 된 부분 문자열을 포함 합니다.

**해결 방법:** 사용자를 업데이트 하거나 다른 사용자를 사용 합니다.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>오류: VM 확장을 처리할 때 오류를 보고 했습니다.

![프로그램 배포 실패에서 터미널 프로 비전 상태로 완료 된 리소스 작업의 스크린샷.](media/49c4a1836a55d91cd65125cf227f411f.png)

원시 오류 예:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**원인:** PowerShell DSC 확장은 VM에서 관리자 액세스를 가져올 수 없습니다.

**해결 방법:** 가상 컴퓨터에 대 한 관리자 권한이 및 Azure Resource Manager 템플릿을 다시 실행 사용자 이름 및 암호를 확인 합니다.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>오류: DeploymentFailed – PowerShell DSC 구성 'FirstSessionHost' 완료 되었지만 오류 발생

![배포의 스크린샷 실패 하 고 PowerShell DSC 구성 'FirstSessionHost' 오류가 발생 했습니다.](media/64870370bcbe1286906f34cf0a8646ab.png)

원시 오류 예:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**원인:** PowerShell DSC 확장은 VM에서 관리자 액세스를 가져올 수 없습니다.

**해결 방법:** 사용자 이름 및 암호 제공 된 가상 컴퓨터에서 관리 액세스 권한이 확인 하 고 Azure Resource Manager 템플릿을 다시 실행 합니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: DeploymentFailed – InvalidResourceReference

원시 오류 예:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인:** 템플릿에 의해 생성 되는 특정 리소스에 대 한 리소스 그룹 이름의 일부로 사용 됩니다. 기존 리소스를 일치 하는 이름으로 인해 템플릿을 다른 그룹에서 기존 리소스를 선택할 수 있습니다.

**해결 방법:** 세션 호스트 Vm을 배포 하려면 Azure Resource Manager 템플릿을 실행 하는 경우에 처음 두 문자를 구독 리소스 그룹 이름에 대 한 고유한 확인 합니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: DeploymentFailed – InvalidResourceReference

원시 오류 예:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**원인:** 이 오류는 Azure Resource Manager 템플릿을 사용 하 여 만든 NIC에 다른 NIC와 동일한 이름을 이미 VNET 때문입니다.

**해결 방법:** 다른 호스트 접두사를 사용 합니다.

### <a name="error-deploymentfailed--error-downloading"></a>오류: DeploymentFailed – 다운로드 오류

원시 오류 예:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**원인:** 이 오류는 고정 경로, 방화벽 규칙 또는 Azure Resource Manager 템플릿에 연결 된 zip 파일의 다운로드를 차단 하는 NSG 때문입니다.

**해결 방법:** 차단 하는 고정 경로, 방화벽 규칙 또는 NSG를 제거 합니다. 필요에 따라 Azure Resource Manager 템플릿 json 파일을 텍스트 편집기에서 열고, zip 파일에 링크를 수행 및 허용 된 위치에 리소스를 다운로드 합니다.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>오류: 관리 서비스를 쿼리할 권한이 없는 사용자

원시 오류 예:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**원인:** 지정 된 Windows 가상 데스크톱 테 넌 트 관리자는 올바른 역할 할당이 없습니다.

**해결 방법:** Windows 가상 데스크톱 테 넌 트를 만든 사용자는 Windows 가상 데스크톱 PowerShell에 로그인 하 고 역할 할당을 시도한 사용자를 할당 해야 합니다. GitHub의 Azure Resource Manager 템플릿 매개 변수를 실행 하는 경우 PowerShell 명령을 사용 하 여 이러한 지침을 따릅니다.

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>오류: 사용자는 Azure Multi-factor Authentication (MFA) 필요

![Multi-factor Authentication (MFA)의 부족으로 인해 실패 한 배포의 스크린 샷](media/MFARequiredError.png)

원시 오류 예:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**원인:** 지정 된 Windows 가상 데스크톱 테 넌 트 관리자는 로그인에 Azure Multi-factor Authentication (MFA) 필요 합니다.

**해결 방법:** 서비스 주체 만들기 및 할당 역할을 Windows Virtual Desktop 테 넌 트에 대 한 단계를 수행 하 여 [자습서: 서비스 주체를 만들고 PowerShell을 사용 하 여 역할 할당](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)합니다. 서비스 주체를 사용 하 여 Windows 가상 데스크톱에 로그인 수를 확인 한 후 Azure Marketplace 제품은 또는 사용 하는 방법에 따라, GitHub Azure Resource Manager 템플릿을 다시 실행 합니다. 메서드에 대 한 올바른 매개 변수를 입력 하려면 아래 지침을 따릅니다.

Azure Marketplace 제품을 실행 하는 경우 다음 매개 변수를 Windows 가상 데스크톱에 올바르게 인증에 대 한 값을 제공 합니다.

- Windows 가상 데스크톱 RDS 소유자에 테 넌 트: 서비스 주체
- 애플리케이션 ID: 만든 새 서비스 주체의 응용 프로그램 id
- 암호/암호 확인: 서비스 주체에 대 한 생성 된 암호
- Azure AD 테 넌 트 ID: 사용자가 만든 서비스 사용자의 Azure AD 테 넌 트 ID

GitHub의 Azure Resource Manager 템플릿을 실행 하는 경우 다음 매개 변수를 Windows 가상 데스크톱에 올바르게 인증에 대 한 값을 제공 합니다.

- 테 넌 트 관리자 사용자 계정 이름 (UPN) 또는 응용 프로그램 ID: 만든 새 서비스 주체의 응용 프로그램 id
- 테 넌 트 관리자 암호: 서비스 주체에 대 한 생성 된 암호
- IsServicePrincipal: **true**
- AadTenantId: 사용자가 만든 서비스 사용자의 Azure AD 테 넌 트 ID

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 추적 문제 해결에 대 한 개요를 참조 하세요 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)합니다.
- Windows 가상 데스크톱의 가상 머신 (VM)를 구성 하는 동안 문제를 해결 하려면 참조 [세션 호스트 가상 머신 구성을](troubleshoot-vm-configuration.md)합니다.
- Windows 가상 데스크톱 클라이언트 연결을 사용 하 여 문제를 해결 하려면 참조 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)합니다.
- Windows 가상 데스크톱을 사용 하 여 PowerShell을 사용 하는 경우 문제를 해결 하려면 참조 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)합니다.
- 미리 보기 서비스에 대 한 자세한 내용은 참조 하세요 [Windows 데스크톱 미리 보기 환경](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)합니다.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.