---
title: Windows 가상 데스크톱 환경 호스트 풀 만들기-Azure
description: Windows 가상 데스크톱 환경을 설치 하는 동안 테 넌 트 및 호스트 풀 문제를 해결 하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 126a8e48a8db1c41299a7cb7a34f172342110667
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023125"
---
# <a name="host-pool-creation"></a>호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md)를 참조하세요.

이 문서에서는 Windows 가상 데스크톱 테 넌 트 및 관련 세션 호스트 풀 인프라의 초기 설정 중에 발생 하는 문제에 대해 설명 합니다.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise 다중 세션 이미지 가져오기

Windows 10 Enterprise 다중 세션 이미지를 사용 하려면 Azure Marketplace으로 이동 하 여 **시작**  >  **Microsoft windows 10** > 및 [Windows 10 Enterprise 다중 세션 버전 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)을 선택 합니다.

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Azure Portal를 사용 하 여 호스트 풀을 만들 때 발생 하는 문제

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>오류: 서비스에 액세스할 때 "무료 계정 만들기"가 표시 됩니다.

> [!div class="mx-imgBorder"]
> !["무료 계정 만들기" 메시지를 표시 하 Azure Portal를 보여 주는 이미지](media/create-new-account.png)

**원인**: Azure에 로그인 한 계정에 활성 구독이 없거나, 계정에 구독을 볼 수 있는 권한이 없습니다.

**수정**: 참가자 수준 액세스 권한이 있는 계정으로 세션 호스트 vm (가상 머신)을 배포 하는 구독에 로그인 합니다.

### <a name="error-exceeding-quota-limit"></a>오류: "할당량 한도 초과"

작업이 할당량 한도를 초과 하는 경우 다음 작업 중 하나를 수행할 수 있습니다.

- 동일한 매개 변수를 사용 하지만 vm 및 VM 코어 수가 작은 새 호스트 풀을 만듭니다.

- 브라우저의 statusMessage 필드에 표시 되는 링크를 열어 지정 된 VM SKU에 대 한 Azure 구독의 할당량을 늘리는 요청을 제출 합니다.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>오류: 앱 그룹의 사용자 할당을 볼 수 없습니다.

원인:이 오류는 일반적으로 구독을 AD (Azure Active Directory) 테 넌 트에서 다른 테 넌 트에서 이동한 후에 발생 합니다. 이전 할당이 여전히 이전 Azure AD 테 넌 트에 연결 된 경우에는 Azure Portal에서 추적을 잃게 됩니다.

Fix: 사용자를 앱 그룹에 다시 할당 해야 합니다.

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager 템플릿 오류

다음 지침에 따라 Azure Resource Manager 템플릿 및 PowerShell DSC의 실패 한 배포 문제를 해결 합니다.

1. [Azure Resource Manager에서 배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 사용 하 여 배포 오류를 검토 합니다.
2. 배포에 오류가 없으면 활동 로그 보기를 사용 하 여 활동 로그의 오류를 검토 하 여 [리소스에 대 한 작업을 감사](../azure-resource-manager/management/view-activity-logs.md)합니다.
3. 오류가 식별 되 면 오류 메시지 및 문제 해결을 위해 [Azure Resource Manager 일반적인 Azure 배포 오류 해결](../azure-resource-manager/templates/common-deployment-errors.md) 의 리소스를 사용 합니다.
4. 이전 배포 중에 만들어진 모든 리소스를 삭제 하 고 템플릿을 다시 배포 하는 작업을 다시 시도 합니다.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>오류: 배포 하지 못했습니다. \<hostname> /joindomain

> [!div class="mx-imgBorder"]
> ![배포에 실패 했습니다.](media/failure-joindomain.png)

원시 오류의 예:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인 1:** Vm을 도메인에 가입 시키기 위해 제공 된 자격 증명이 잘못 되었습니다.

**수정 1:** Vm에 대 한 "잘못 된 자격 증명" 오류는 [세션 호스트 VM 구성](troubleshoot-vm-configuration.md)에서 도메인에 가입 되지 않음을 참조 하세요.

**원인 2:** 도메인 이름이 확인 되지 않습니다.

**수정 2:** [오류: 도메인 이름이](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) [세션 호스트 VM 구성](troubleshoot-vm-configuration.md)에서 확인 되지 않음을 참조 하세요.

**원인 3:** 가상 네트워크 (VNET) DNS 구성이 **기본값으로** 설정 되어 있습니다.

이 문제를 해결 하려면 다음 작업을 수행 합니다.

1. Azure Portal를 열고 **가상 네트워크** 탭으로 이동 합니다.
2. VNET을 찾은 후 **DNS 서버** 를 선택 합니다.
3. DNS 서버 메뉴가 화면 오른쪽에 표시 됩니다. 해당 메뉴에서 **사용자 지정** 을 선택 합니다.
4. 사용자 지정 아래에 나열 된 DNS 서버가 도메인 컨트롤러 또는 Active Directory 도메인과 일치 하는지 확인 합니다. DNS 서버가 표시 되지 않으면 **dns 서버 추가** 필드에 해당 값을 입력 하 여 추가할 수 있습니다.

### <a name="error-your-deployment-failedunauthorized"></a>오류: 배포하지 못했습니다...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**원인:** 사용 중인 구독은 고객이 배포를 시도 하는 지역의 필수 기능에 액세스할 수 없는 유형입니다. 예를 들어 MSDN, 무료 또는 교육 구독에이 오류가 표시 될 수 있습니다.

**해결 방법:** 구독 유형 또는 지역을 필요한 기능에 액세스할 수 있는 것으로 변경 합니다.

### <a name="error-vmextensionprovisioningerror"></a>오류: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![터미널 프로 비전 상태가 실패 하 여 배포의 스크린샷에 실패 했습니다.](media/failure-vmextensionprovisioning.png)

**원인 1:** Windows 가상 데스크톱 환경에서 일시적 오류가 발생 했습니다.

**원인 2:** 연결에 일시적인 오류가 발생 했습니다.

**해결 방법:** PowerShell을 사용 하 여 로그인 하면 Windows 가상 데스크톱 환경이 정상 상태 인지 확인 합니다. [PowerShell을 사용 하 여 호스트 풀 만들기](create-host-pools-powershell.md)에서 수동으로 VM 등록을 완료 합니다.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>오류: 지정된 관리자 사용자 이름이 허용되지 않습니다.

> [!div class="mx-imgBorder"]
> ![지정 된 관리자가 허용 되지 않는 배포의 스크린샷](media/failure-username.png)

원시 오류의 예:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**원인:** 제공 된 암호에 금지 된 부분 문자열 (admin, administrator, root)이 포함 되어 있습니다.

**해결 방법:** 사용자 이름을 업데이트 하거나 다른 사용자를 사용 합니다.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>오류: 확장을 처리하는 동안 VM이 오류를 보고했습니다.

> [!div class="mx-imgBorder"]
> ![배포에서 터미널 프로 비전 상태를 사용 하 여 완료 된 리소스 작업의 스크린샷에 실패 했습니다.](media/failure-processing.png)

원시 오류의 예:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**원인:** PowerShell DSC 확장에서 VM에 대 한 관리자 액세스 권한을 얻을 수 없습니다.

**해결 방법:** 사용자 이름 및 암호에 가상 컴퓨터에 대 한 관리자 권한이 있는지 확인 하 고 Azure Resource Manager 템플릿을 다시 실행 하십시오.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>오류: DeploymentFailed – PowerShell DSC 구성 ' FirstSessionHost '이 (가) 완료 되었으나 오류가 발생 했습니다.

> [!div class="mx-imgBorder"]
> ![오류로 인해 PowerShell DSC 구성 ' FirstSessionHost '를 완료 하 여 배포 실패의 스크린샷](media/failure-dsc.png)

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

**원인:** PowerShell DSC 확장에서 VM에 대 한 관리자 액세스 권한을 얻을 수 없습니다.

**해결 방법:** 입력 한 사용자 이름 및 암호에 가상 머신에 대 한 관리 권한이 있는지 확인 하 고 Azure Resource Manager 템플릿을 다시 실행 합니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: DeploymentFailed – InvalidResourceReference

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

**원인:** 리소스 그룹 이름의 일부가 템플릿에서 만드는 특정 리소스에 사용 됩니다. 기존 리소스와 일치 하는 이름 때문에 템플릿은 다른 그룹에서 기존 리소스를 선택할 수 있습니다.

**해결 방법:** Azure Resource Manager 템플릿을 실행 하 여 세션 호스트 Vm을 배포 하는 경우 구독 리소스 그룹 이름에 대해 처음 두 문자를 고유 하 게 만듭니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: DeploymentFailed – InvalidResourceReference

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

**원인:** 이 오류는 Azure Resource Manager 템플릿으로 만든 NIC가 VNET에 이미 있는 다른 NIC와 동일한 이름을 갖고 있기 때문입니다.

**해결 방법:** 다른 호스트 접두사를 사용 하십시오.

### <a name="error-deploymentfailed--error-downloading"></a>오류: DeploymentFailed – 다운로드 오류

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

**원인:** 이 오류는 Azure Resource Manager 템플릿에 연결 된 zip 파일의 다운로드를 차단 하는 고정 경로, 방화벽 규칙 또는 NSG 때문입니다.

**해결 방법:** 차단 고정 경로, 방화벽 규칙 또는 NSG를 제거 합니다. 필요에 따라 텍스트 편집기에서 Azure Resource Manager 템플릿 json 파일을 열고 zip 파일에 대 한 링크를 가져온 다음 허용 된 위치에 리소스를 다운로드 합니다.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>오류: VM을 삭제 한 후에는 호스트 풀에서 세션 호스트를 삭제할 수 없습니다.

**원인:** VM을 삭제 하기 전에 세션 호스트를 삭제 해야 합니다.

**해결 방법:** 세션 호스트를 드레이닝 모드로 전환 하 고, 세션 호스트에서 모든 사용자를 로그 아웃 한 다음, 호스트를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

- Windows Virtual Desktop 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Windows Virtual Desktop에서 VM(가상 머신)을 구성하면서 생기는 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)을 참조 하세요.
- 원격 데스크톱 클라이언트와 관련 된 문제를 해결 하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md) 을 참조 하세요.
- Windows Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.