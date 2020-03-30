---
title: Windows 가상 데스크톱 테넌트 호스트 풀 생성 - Azure
description: Windows 가상 데스크톱 테넌트 환경을 설정하는 동안 테넌트 및 호스트 풀 문제를 해결하고 해결하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371599"
---
# <a name="tenant-and-host-pool-creation"></a>테넌트 및 호스트 풀 만들기

이 문서에서는 Windows 가상 데스크톱 테넌트 및 관련 세션 호스트 풀 인프라를 처음 설정하는 동안의 문제를 다룹니다.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 엔터프라이즈 다중 세션 이미지 획득

윈도우를 사용 하려면 10 엔터프라이즈 멀티 세션 이미지, Azure 마켓 플레이스로 이동, 선택 **시작** > **마이크로소프트 윈도우 10** > 그리고 [윈도우 10 가상 데스크톱에 대 한 기업, 버전 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![가상 데스크톱용 Windows 10 엔터프라이즈, 버전 1809를 선택하는 스크린샷입니다.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows 가상 데스크톱 테넌트 만들기

이 섹션에서는 Windows 가상 데스크톱 테넌트를 만들 때 발생할 수 있는 문제를 다룹니다.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>오류: 사용자가 관리 서비스를 쿼리할 권한이 없습니다.

![사용자가 관리 서비스를 쿼리할 권한이 없는 PowerShell 창의 스크린샷입니다.](media/UserNotAuthorizedNewTenant.png)

원시 오류의 예:

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

**원인:** 로그인한 사용자는 Azure Active Directory에서 TenantCreator 역할을 할당받지 못했습니다.

**수정 사항:** Azure Active [Directory 테넌트의 사용자에게 테넌트 크리에이터 응용 프로그램 역할 할당의 지침에](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)따릅니다. 지침에 따라 테넌트크리에이터 역할에 할당된 사용자가 표시됩니다.

![테넌트 작성자 역할 할당의 스크린샷입니다.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows 가상 데스크톱 세션 호스트 VM 만들기

세션 호스트 VM은 여러 가지 방법으로 만들 수 있지만 Windows 가상 데스크톱 팀은 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 오퍼링과 관련된 VM 프로비저닝 문제만 지원합니다. 자세한 내용은 [Windows 가상 데스크톱을 사용하는 문제 - 호스트 풀 Azure 마켓플레이스 제공을 참조하십시오.](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Windows 가상 데스크톱을 사용하는 문제 - 호스트 풀 Azure 마켓플레이스 오퍼링 프로비전

Windows 가상 데스크톱 - Azure 마켓플레이스에서 호스트 풀 템플릿프로비전을 사용할 수 있습니다.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>오류: GitHub의 링크를 사용할 때 "무료 계정 만들기"라는 메시지가 나타납니다.

![스크린 샷은 무료 계정을 만들 수 있습니다.](media/be615904ace9832754f0669de28abd94.png)

**원인 1:** Azure에 로그인하는 데 사용되는 계정에 활성 구독이 없거나 구독을 볼 수 있는 권한이 없는 계정에 있습니다.

**수정 1:** 세션 호스트 VM이 배포될 구독에 최소한 액세스 권한이 있는 계정으로 로그인합니다.

**원인 2:** 사용 중인 구독은 CSP(Microsoft 클라우드 서비스 공급자) 테넌트의 일부입니다.

**수정 2:** 새 Windows 가상 데스크톱 **호스트 풀 만들기 및 프로비전용** GitHub 위치로 이동하여 다음 지침을 따릅니다.

1. **Azure에 배포를** 마우스 오른쪽 단추로 클릭하고 **링크 주소 복사를**선택합니다.
2. **메모장을** 열고 링크를 붙여 넣습니다.
3. # 문자 앞에 CSP end 고객 테넌트 이름을 삽입합니다.
4. 브라우저에서 새 링크를 열면 Azure 포털이 템플릿을 로드합니다.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>오류: "템플릿 배포가 잘못되었습니다" 오류가 나타납니다.

!["템플릿 배포의 스크린 샷 ... 유효하지 않습니다" 오류](media/troubleshooting-marketplace-validation-error-generic.png)

특정 작업을 수행하기 전에 작업 로그를 확인하여 실패한 배포 유효성 검사에 대한 자세한 오류를 확인해야 합니다.

활동 로그의 오류를 보려면 다음을 수행하십시오.

1. 현재 Azure 마켓플레이스 배포 제품을 종료합니다.
2. 상단 검색 표시줄에서 활동 로그를 검색하고 **선택합니다.**
3. **실패** 상태의 상태가 있는 **배포 유효성 검사라는** 활동을 찾아 활동을 선택합니다.
   ![개별 의 스크린샷 **실패** 상태의 배포** 활동 유효성 검사](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. JSON을 선택한 다음 "statusMessage" 필드가 표시될 때까지 화면 아래쪽으로 스크롤합니다.
   ![실패한 활동의 스크린샷, 상태 주위에 빨간색 상자JSON 텍스트의 속성 메시지.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

작업 템플릿이 할당량 제한을 초과하는 경우 다음 작업 중 하나를 수행하여 수정할 수 있습니다.

 - 처음 사용한 매개 변수를 사용하여 Azure 마켓플레이스를 실행하지만 이번에는 VM 및 VM 코어를 더 적게 사용합니다.
 - **브라우저의 statusMessage** 필드에 표시되는 링크를 열어 지정된 VM SKU에 대한 Azure 구독에 대한 할당량을 늘리는 요청을 제출합니다.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure 리소스 관리자 템플릿 및 PowerShell 원하는 상태 구성(DSC) 오류

이 지침에 따라 Azure 리소스 관리자 템플릿 및 PowerShell DSC의 실패한 배포 문제를 해결합니다.

1. [Azure 리소스 관리자를 사용하여 배포 작업 보기를](../azure-resource-manager/resource-manager-deployment-operations.md)사용하여 배포 오류를 검토합니다.
2. 배포에 오류가 없는 경우 활동 로그 보기를 사용하여 [리소스에 대한 작업을 감사하기 위해](../azure-resource-manager/resource-group-audit.md)활동 로그의 오류를 검토합니다.
3. 오류가 확인되면 오류 메시지와 [Azure 리소스 관리자를 사용하여 일반적인 Azure 배포 오류를 해결하는](../azure-resource-manager/resource-manager-common-deployment-errors.md) 리소스를 사용하여 문제를 해결합니다.
4. 이전 배포 중에 생성된 리소스를 삭제하고 템플릿 배포를 다시 시도합니다.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>오류: 배포에 실패했습니다....\<호스트 이름>/joindomain

![배포 실패 스크린샷입니다.](media/e72df4d5c05d390620e07f0d7328d50f.png)

원시 오류의 예:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인 1:** VM을 도메인에 조인하기 위해 제공된 자격 증명이 올바르지 않습니다.

**수정 1:** [세션 호스트 VM 구성의](troubleshoot-vm-configuration.md)도메인에 가입되지 않은 VM에 대한 "잘못된 자격 증명" 오류를 참조하십시오.

**원인 2:** 도메인 이름이 확인되지 않습니다.

**수정 2:** 오류 참조: [세션 호스트 VM 구성에서](troubleshoot-vm-configuration.md) [도메인 이름이 확인되지 않습니다.](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)

**원인 3:** 가상 네트워크(VNET) DNS 구성이 **기본값으로**설정됩니다.

이 문제를 해결하려면 다음 작업을 수행합니다.

1. Azure 포털을 열고 **가상 네트워크** 탭으로 이동합니다.
2. VNET을 찾은 다음 **DNS 서버를 선택합니다.**
3. DNS 서버 메뉴가 화면 오른쪽에 나타납니다. 해당 메뉴에서 **사용자 지정**을 선택합니다.
4. 사용자 지정 아래에 나열된 DNS 서버가 도메인 컨트롤러 또는 Active Directory 도메인과 일치하는지 확인합니다. DNS 서버가 표시되지 않으면 해당 값을 **DNS 서버 추가** 필드에 입력하여 추가할 수 있습니다.

### <a name="error-your-deployment-failedunauthorized"></a>오류: 배포에 실패했습니다...\무단

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**원인:** 사용 하는 구독은 고객이 배포 하려는 지역에서 필요한 기능에 액세스할 수 없는 형식입니다. 예를 들어 MSDN, 무료 또는 교육 구독에 이 오류가 표시될 수 있습니다.

**수정 사항:** 구독 유형 또는 지역을 필요한 기능에 액세스할 수 있는 것으로 변경합니다.

### <a name="error-vmextensionprovisioningerror"></a>오류: VMExtension 프로비저닝오류

![터미널 프로비저닝 상태에서 배포 실패 스크린샷이 실패했습니다.](media/7aaf15615309c18a984673be73ac969a.png)

**원인 1:** Windows 가상 데스크톱 환경의 일시적인 오류입니다.

**원인 2:** 연결시 일시적인 오류입니다.

**수정 사항:** PowerShell을 사용하여 로그인하여 Windows 가상 데스크톱 환경이 정상인지 확인합니다. 에서 수동으로 VM 등록을 완료 [PowerShell을 사용 하 여 호스트 풀 만들기에서](create-host-pools-powershell.md)

### <a name="error-the-admin-username-specified-isnt-allowed"></a>오류: 지정된 관리자 사용자 이름이 허용되지 않습니다.

![지정된 관리자가 허용되지 않는 배포 스크린샷이 실패했습니다.](media/f2b3d3700e9517463ef88fa41875bac9.png)

원시 오류의 예:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**원인:** 제공된 암호에는 금지된 하위 문자열(관리자, 관리자, 루트)이 포함되어 있습니다.

**수정 사항:** 사용자 이름을 업데이트하거나 다른 사용자를 사용합니다.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>오류: VM에서 확장을 처리할 때 오류가 보고되었습니다.

![배포 실패에서 터미널 프로비저닝 상태로 완료된 리소스 작업의 스크린샷입니다.](media/49c4a1836a55d91cd65125cf227f411f.png)

원시 오류의 예:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**원인:** PowerShell DSC 확장이 VM에서 관리자 액세스를 얻을 수 없습니다.

**수정 사항:** 사용자 이름과 암호가 가상 시스템에 대한 관리 액세스 권한이 있는지 확인하고 Azure 리소스 관리자 템플릿을 다시 실행합니다.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>오류: 배포 실패 – PowerShell DSC 구성 'FirstSessionHost' 오류완료

![PowerShell DSC 구성 'FirstSessionHost'가 오류로 완료된 경우 배포 스크린샷이 실패합니다.](media/64870370bcbe1286906f34cf0a8646ab.png)

원시 오류의 예:

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

**원인:** PowerShell DSC 확장이 VM에서 관리자 액세스를 얻을 수 없습니다.

**수정 사항:** 제공된 사용자 이름과 암호가 가상 시스템에 대한 관리 액세스 권한이 있는지 확인하고 Azure 리소스 관리자 템플릿을 다시 실행합니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: 배포 실패 – 잘못된 리소스 참조

원시 오류의 예:

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

**원인:** 리소스 그룹 이름의 일부는 템플릿에서 생성되는 특정 리소스에 사용됩니다. 기존 리소스와 일치하는 이름으로 인해 템플릿은 다른 그룹에서 기존 리소스를 선택할 수 있습니다.

**수정 사항:** 세션 호스트 VM을 배포하기 위해 Azure 리소스 관리자 템플릿을 실행할 때 구독 리소스 그룹 이름에 대해 처음 두 문자를 고유하게 만듭니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: 배포 실패 – 잘못된 리소스 참조

원시 오류의 예:

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

**원인:** 이 오류는 Azure 리소스 관리자 템플릿으로 만든 NIC의 이름이 VNET에 이미 있는 다른 NIC와 같기 때문입니다.

**수정 사항:** 다른 호스트 접두사를 사용합니다.

### <a name="error-deploymentfailed--error-downloading"></a>오류: 배포 실패 – 오류 다운로드

원시 오류의 예:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**원인:** 이 오류는 정적 경로, 방화벽 규칙 또는 NSG가 Azure 리소스 관리자 템플릿에 연결된 zip 파일의 다운로드를 차단하기 때문입니다.

**수정 사항:** 차단 정적 경로, 방화벽 규칙 또는 NSG를 제거합니다. 선택적으로 텍스트 편집기에서 Azure 리소스 관리자 템플릿 json 파일을 열고 zip 파일에 대한 링크를 가져와서 허용된 위치로 리소스를 다운로드합니다.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>오류: 사용자가 관리 서비스를 쿼리할 권한이 없습니다.

원시 오류의 예:

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

**원인:** 지정된 Windows 가상 데스크톱 테넌트 관리자에 유효한 역할 할당이 없습니다.

**수정 사항:** Windows 가상 데스크톱 테넌트를 만든 사용자는 Windows 가상 데스크톱 PowerShell에 로그인하고 시도한 사용자에게 역할 할당을 할당해야 합니다. GitHub Azure 리소스 관리자 템플릿 매개 변수를 실행 하는 경우 PowerShell 명령을 사용 하 여 다음 지침을 따르십시오.

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>오류: 사용자가 Azure 다단계 인증(MFA)을 필요로 합니다.

![MFA(다단계 인증)가 부족하여 배포 스크린샷이 실패했습니다.](media/MFARequiredError.png)

원시 오류의 예:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**원인:** 지정된 Windows 가상 데스크톱 테넌트 관리자는 MFA(Azure 다단계 인증)를 사용하여 로그인해야 합니다.

**수정 사항:** 서비스 주체를 만들고 [자습서: PowerShell을 사용하여 서비스 주체 및 역할 할당](create-service-principal-role-powershell.md)을 만들어 Windows 가상 데스크톱 테넌트에 대한 역할을 할당합니다. 서비스 주체를 사용하여 Windows 가상 데스크톱에 로그인할 수 있는지 확인한 후 사용 중인 방법에 따라 Azure Marketplace 오퍼링 또는 GitHub Azure 리소스 관리자 템플릿을 다시 실행합니다. 메서드에 대 한 올바른 매개 변수를 입력 하려면 아래 지침에 따라.

Azure Marketplace 오퍼링을 실행 하는 경우 Windows 가상 데스크톱에 제대로 인증 하려면 다음 매개 변수에 대 한 값을 제공 합니다.

- 윈도우 가상 데스크톱 테넌트 RDS 소유자: 서비스 주체
- 응용 프로그램 ID: 만든 새 서비스 주체의 응용 프로그램 식별
- 암호/암호 확인: 서비스 주체에 대해 생성한 암호 비밀
- Azure AD 테넌트 ID: 만든 서비스 주체의 Azure AD 테넌트 ID

GitHub Azure 리소스 관리자 템플릿을 실행 하는 경우 Windows 가상 데스크톱에 제대로 인증 하는 다음 매개 변수에 대 한 값을 제공 합니다.

- 테넌트 관리자 사용자 주체 이름(UPN) 또는 응용 프로그램 ID: 사용자가 만든 새 서비스 주체의 응용 프로그램 ID
- 테넌트 관리자 암호: 서비스 주체에 대해 생성한 암호 암호 암호
- IsService 교장: **true**
- AadTenantId: 만든 서비스 주체의 Azure AD 테넌트 ID

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원을](troubleshoot-set-up-overview.md)참조하십시오.
- Windows 가상 데스크톱에서 가상 시스템(VM)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 시스템 구성을](troubleshoot-vm-configuration.md)참조하십시오.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결하려면 [Windows 가상 데스크톱 서비스 연결을](troubleshoot-service-connection.md)참조하십시오.
- 원격 데스크톱 클라이언트의 문제 해결을 위해 [원격 데스크톱 클라이언트 문제 해결을](troubleshoot-client.md) 참조하세요.
- Windows 가상 데스크톱에서 PowerShell을 사용할 때 문제를 해결하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조하십시오.
- 서비스에 대한 자세한 내용은 [Windows 가상 데스크톱 환경을](environment-setup.md)참조하십시오.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포 문제를 해결 합니다.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
