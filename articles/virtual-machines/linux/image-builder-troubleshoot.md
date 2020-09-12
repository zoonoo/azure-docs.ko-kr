---
title: Azure 이미지 작성기 서비스 문제 해결
description: Azure VM 이미지 작성기 서비스를 사용할 때 발생 하는 일반적인 문제 해결
author: cynthn
ms.author: danis
ms.date: 09/03/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: ee65cd1605e23dfd5699f92a900bdb5e7952fe13
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459932"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Azure 이미지 작성기 서비스 문제 해결

이 문서는 Azure 이미지 작성기 서비스를 사용 하는 경우 발생할 수 있는 일반적인 문제를 해결 하는 데 도움이 됩니다.

AIB 오류는 다음과 같은 두 영역에서 발생할 수 있습니다.
- 이미지 템플릿 전송
- 이미지 빌드

## <a name="troubleshoot-image-template-submission-errors"></a>이미지 템플릿 전송 오류 문제 해결

전송 시에만 이미지 템플릿 전송 오류가 반환 됩니다. 이미지 템플릿 전송 오류에 대 한 오류 로그가 없습니다. 제출 하는 동안 오류가 발생 한 경우 템플릿 상태를 확인 하 고 특히 및를 검토 하 여 오류를 반환할 수 `ProvisioningState` 있습니다 `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> PowerShell의 경우 [Azure 이미지 작성기 PowerShell 모듈](../windows/image-builder-powershell.md#prerequisites)을 설치 해야 합니다.

다음 섹션에는 일반적인 이미지 템플릿 전송 오류에 대 한 문제 해결 지침이 포함 되어 있습니다.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>이미지 템플릿의 업데이트/업그레이드는 현재 지원 되지 않습니다.

#### <a name="error"></a>Error

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>원인

템플릿이 이미 있습니다.

#### <a name="solution"></a>솔루션

이미지 구성 템플릿을 제출 하 고 전송에 실패 하면 실패 한 템플릿 아티팩트가 여전히 존재 합니다. 실패 한 템플릿을 삭제 합니다.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>리소스 작업이 ' 실패 ' 터미널 프로 비전 상태로 완료 되었습니다.

#### <a name="error"></a>Error

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>원인

대부분의 경우 권한 누락으로 인해 리소스 배포 실패 오류가 발생 합니다.

#### <a name="solution"></a>솔루션

시나리오에 따라 Azure 이미지 작성기에는 다음에 대 한 권한이 필요할 수 있습니다.
- 원본 이미지 또는 공유 이미지 갤러리 리소스 그룹
- 배포 이미지 또는 공유 이미지 갤러리 리소스
- 파일 사용자 지정자가 액세스 하는 저장소 계정, 컨테이너 또는 blob입니다. 

권한 구성에 대 한 자세한 내용은 PowerShell을 사용 하 [여 Azure 이미지 작성기 서비스 권한 구성 Azure CLI](image-builder-permissions-cli.md) 또는 [Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-powershell.md) 을 참조 하세요.

### <a name="error-getting-managed-image"></a>관리 되는 이미지를 가져오는 동안 오류 발생

#### <a name="error"></a>Error

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>원인

권한이 없습니다.

#### <a name="solution"></a>솔루션

시나리오에 따라 Azure 이미지 작성기에는 다음에 대 한 권한이 필요할 수 있습니다.
* 원본 이미지 또는 공유 이미지 갤러리 리소스 그룹
* 배포 이미지 또는 공유 이미지 갤러리 리소스
* 파일 사용자 지정자가 액세스 하는 저장소 계정, 컨테이너 또는 blob입니다. 

권한 구성에 대 한 자세한 내용은 PowerShell을 사용 하 [여 Azure 이미지 작성기 서비스 권한 구성 Azure CLI](image-builder-permissions-cli.md) 또는 [Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-powershell.md) 을 참조 하세요.

### <a name="build--step-failed-for-image-version"></a>이미지 버전에 대 한 빌드 단계가 실패 했습니다.

#### <a name="error"></a>Error
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>원인

Azure 이미지 작성기에서 원본 이미지를 찾을 수 없습니다.

#### <a name="solution"></a>솔루션

원본 이미지가 올바르고 Azure 이미지 작성기 서비스의 위치에 있는지 확인 하세요.

### <a name="downloading-external-file-to-local-file"></a>로컬 파일에 외부 파일을 다운로드 하는 중

#### <a name="error"></a>Error

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>원인

파일 이름 또는 위치가 잘못 되었거나 위치에 연결할 수 없습니다.

#### <a name="solution"></a>솔루션

파일에 연결할 수 있는지 확인 합니다. 이름 및 위치가 올바른지 확인 합니다.

## <a name="troubleshoot-build-failures"></a>빌드 오류 문제 해결

이미지 빌드에 실패 하는 경우에서 오류를 가져온 `lastrunstatus` 다음 사용자 지정 .log에서 세부 정보를 검토할 수 있습니다.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>사용자 지정 로그

이미지 빌드를 실행 하면 로그가 만들어지고 저장소 계정에 저장 됩니다. Azure 이미지 작성기는 이미지 템플릿 아티팩트를 만들 때 임시 리소스 그룹에 저장소 계정을 만듭니다.

저장소 계정 이름은 다음 패턴을 사용 합니다. **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\> **

예를 들어 *IT_aibmdi_helloImageTemplateLinux01*합니다.

**저장소**계정  >  **blob**을 선택 하 여 리소스 그룹의 저장소 계정에서 사용자 지정 .log를 볼 수  >  있습니다. `packerlogs`  그런 다음 **디렉터리 > 사용자 지정 .log**를 선택 합니다.


### <a name="understanding-the-customization-log"></a>사용자 지정 로그 이해

로그는 자세한 정보를 표시 합니다. 공유 이미지 갤러리 복제와 같은 이미지 배포 문제를 포함 하 여 이미지 빌드에 대해 설명 합니다. 이러한 오류는 이미지 템플릿 상태의 오류 메시지에 표시 됩니다.

사용자 지정 .log에는 다음 단계가 포함 됩니다.

1. ARM 템플릿을 사용 하 여 빌드 VM 및 종속성을 IT_ 준비 리소스 그룹 단계에 배포 합니다. 이 단계에는 Azure 이미지 작성기 리소스 공급자에 대 한 여러 게시물이 포함 되어 있습니다.
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. 배포 단계의 상태입니다. 이 단계에는 각 리소스 배포의 상태가 포함 됩니다.
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. 빌드 VM 단계에 연결 합니다.

    Windows 인 경우 Azure 이미지 작성기 서비스는 WinRM을 사용 하 여 연결 합니다.
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Linux 인 경우 Azure 이미지 작성기 서비스는 SSH를 사용 하 여 연결 합니다.
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. 사용자 지정 단계를 실행 합니다. 사용자 지정을 실행 하면 사용자 지정 .log를 검토 하 여 사용자 지정을 식별할 수 있습니다. *(원격 분석)* 를 검색 합니다.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. 프로 비전 해제 단계입니다. Azure 이미지 작성기는 숨겨진 사용자 지정자를 추가 합니다. 이 프로 비전 해제 단계는 프로 비전 해제를 위해 VM을 준비 하는 일을 담당 합니다. Windows Sysprep (c:\DeprovisioningScript.ps1 사용) 또는 Linux waagent 프로 비전 해제 (/tmp/DeprovisioningScript.sh 사용)를 실행 합니다. 

    다음은 그 예입니다. 
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. 정리 단계입니다. 빌드가 완료 되 면 Azure 이미지 작성기 리소스가 삭제 됩니다.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>스크립트/인라인 사용자 지정 문제 해결을 위한 팁
- 이미지 작성기에 제공 하기 전에 코드 테스트
- Azure Policy의 및 방화벽이 원격 리소스에 대 한 연결을 허용 하는지 확인 합니다.
- 또는를 사용 하 여 콘솔에 주석을 출력 합니다. `Write-Host` `echo` 이렇게 하면 사용자 지정 .log를 검색할 수 있습니다.

## <a name="troubleshoot-common-build-errors"></a>일반적인 빌드 오류 문제 해결

### <a name="packer-build-command-failure"></a>팩 er 빌드 명령 실패

#### <a name="error"></a>Error

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>원인

사용자 지정 오류입니다.

#### <a name="solution"></a>솔루션

로그를 검토 하 여 오류 지정자를 찾습니다. *(원격 분석)* 를 검색 합니다. 

다음은 그 예입니다. 
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>제한 시간 초과

#### <a name="error"></a>Error

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>원인

빌드가 빌드 시간 제한을 초과 했습니다. 이 오류는 ' lastrunstatus '에 표시 됩니다.

#### <a name="solution"></a>솔루션

1. 사용자 지정 .log를 검토 합니다. 실행할 마지막 사용자 지정을 식별 합니다. `(telemetry)`로그 아래쪽부터 검색 합니다. 

2. 스크립트 사용자 지정을 선택 합니다. 사용자 지정은 옵션과 같은 명령에 대 한 사용자 상호 작용을 억제 하지 않을 수 있습니다 `quiet` . 예를 들어를 사용 하면 `apt-get install -y` 사용자 상호 작용을 기다리는 스크립트 실행이 발생 합니다.

3. 사용자 지정자를 사용 하 여 `File` 20mb 보다 큰 아티팩트를 다운로드 하는 경우 해결 방법 섹션을 참조 하세요.

4. 스크립트를 대기 시킬 수 있는 스크립트의 오류 및 종속성을 검토 합니다.

5. 사용자 지정에 시간이 더 필요한 경우 [buildTimeoutInMinutes](image-builder-json.md)를 늘립니다. 기본값은 4 시간입니다.

6. Gb 파일 다운로드와 같이 리소스를 많이 사용 하는 작업이 있는 경우 기본 빌드 VM 크기를 고려 합니다. 서비스는 Standard_D1_v2 VM을 사용 합니다. VM에는 1 개의 vCPU 및 3.5 GB의 메모리가 있습니다. 50 GB를 다운로드 하는 경우 VM 리소스가 고갈 되 고 Azure 이미지 작성기 서비스와 빌드 VM 간에 통신 오류가 발생할 수 있습니다. [VM_Size](image-builder-json.md#vmprofile)를 설정 하 여 더 큰 메모리 VM으로 빌드를 다시 시도 합니다.

### <a name="long-file-download-time"></a>긴 파일 다운로드 시간

#### <a name="error"></a>Error
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>원인 

파일 사용자 지정자에서 많은 파일을 다운로드 하 고 있습니다.

#### <a name="solution"></a>솔루션

파일 사용자 지정자는 20mb 미만의 작은 파일 다운로드에만 적합 합니다. 더 큰 파일을 다운로드 하려면 스크립트나 인라인 명령을 사용 합니다. 예를 들어 Linux에서는 또는을 사용할 수 있습니다 `wget` `curl` . Windows에서는를 사용할 수 있습니다 `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>공유 이미지 갤러리에서 대기 하는 동안 오류 발생

#### <a name="error"></a>Error

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>원인

이미지를 추가 하 여 SIG (공유 이미지 갤러리)에 복제할 때까지 기다리는 동안 이미지 작성기 시간이 초과 되었습니다. 이미지가 SIG에 삽입 되는 경우 이미지 빌드에 성공 했다고 간주할 수 있습니다. 그러나 이미지 작성기가 공유 이미지 갤러리에서 복제를 완료 하기를 기다리고 있기 때문에 전체 프로세스가 실패 했습니다. 빌드가 실패 하더라도 복제는 계속 됩니다. 배포 *Runoutput*을 확인 하 여 이미지 버전의 속성을 가져올 수 있습니다.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>솔루션

**BuildTimeoutInMinutes**를 늘립니다.
 
### <a name="low-windows-resource-information-events"></a>낮은 Windows 리소스 정보 이벤트

#### <a name="error"></a>Error

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>원인

리소스 소모. 이 문제는 일반적으로 기본 빌드 VM 크기 D1_V2를 사용 하 여 실행 되는 Windows 업데이트에 표시 됩니다.

#### <a name="solution"></a>솔루션

빌드 VM 크기를 늘립니다.

### <a name="builds-finished-but-no-artifacts-were-created"></a>빌드가 완료 되었지만 생성 된 아티팩트가 없습니다.

#### <a name="error"></a>Error

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>원인

필요한 Azure 리소스가 생성 될 때까지 대기 하 여 발생 하는 제한 시간입니다.

#### <a name="solution"></a>솔루션

빌드를 다시 실행 하 여 다시 시도 합니다.

### <a name="resource-not-found"></a>리소스를 찾을 수 없음

#### <a name="error"></a>Error

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>원인

권한이 없습니다.

#### <a name="solution"></a>솔루션

Azure 이미지 작성기 다시 확인은 필요한 모든 권한을가지고 있습니다. 

권한 구성에 대 한 자세한 내용은 PowerShell을 사용 하 [여 Azure 이미지 작성기 서비스 권한 구성 Azure CLI](image-builder-permissions-cli.md) 또는 [Azure 이미지 작성기 서비스 권한 구성](image-builder-permissions-powershell.md) 을 참조 하세요.

### <a name="sysprep-timing"></a>Sysprep 타이밍

#### <a name="error"></a>Error

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>원인

D1_V2 VM 크기로 인해 타이밍 문제가 발생할 수 있습니다. 사용자 지정이 제한 되 고 3 초 미만으로 실행 되는 경우 Azure 이미지 작성기에서 sysprep 명령을 실행 하 여 프로 비전을 해제 합니다. Azure 이미지 작성기를 프로 비전 할 때 sysprep 명령은 *WindowsAzureGuestAgent*를 확인 합니다 .이는 전체 설치 되지 않아 타이밍 문제가 발생 하지 않을 수 있습니다. 

#### <a name="solution"></a>솔루션

VM 크기를 늘립니다. 또는 타이밍 문제를 방지 하기 위해 60 초의 PowerShell 절전 모드 사용자 지정을 추가할 수 있습니다.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>컨텍스트 취소 컨텍스트가 취소 된 후 작성기를 취소 하는 중

#### <a name="error"></a>Error
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>원인
이미지 작성기 서비스는 포트 22 (Linux) 또는 5986 (Windows)를 사용 하 여 빌드 VM에 연결 합니다 .이는 이미지를 빌드하는 동안 빌드 VM에서 서비스의 연결이 끊어질 때 발생 합니다. 연결 끊기의 이유는 다양할 수 있지만 스크립트에서 방화벽을 사용 하도록 설정 하거나 구성 하면 위의 포트를 차단할 수 있습니다.

#### <a name="solution"></a>솔루션
방화벽 변경/사용 또는 SSH 또는 WinRM 변경에 대 한 스크립트를 검토 하 고 위의 포트에서 서비스와 VM을 연결 하는 일정을 변경 해야 합니다. 이미지 작성기 네트워킹에 대 한 자세한 내용은 [요구 사항](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking)을 참조 하세요.

## <a name="devops-task"></a>DevOps 작업 

### <a name="troubleshooting-the-task"></a>작업 문제 해결
사용자 지정 중에 오류가 발생 하는 경우에만 작업이 실패 합니다 .이 경우 작업에서 오류를 보고 하 고 준비 리소스 그룹을 로그와 함께 유지 하 여 문제를 식별할 수 있습니다. 

로그를 찾으려면 템플릿 이름을 알고 있어야 합니다. 파이프라인 > 빌드를 실패 한 빌드 > AIB DevOps 작업을 드릴 다운 합니다. 그러면 로그 및 템플릿 이름이 표시 됩니다.
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

포털로 이동 하 여 리소스 그룹에서 템플릿 이름을 검색 한 다음 IT_ *를 사용 하 여 리소스 그룹을 찾습니다.
저장소 계정 > blob > 컨테이너 > 로그로 이동 합니다.

### <a name="troubleshooting-successful-builds"></a>성공한 빌드 문제 해결
성공적인 빌드를 조사 하 고 로그를 검토 해야 하는 경우도 있습니다. 앞서 언급 했 듯이, 이미지 빌드에 성공 하면 로그가 포함 된 준비 리소스 그룹이 정리의 일부로 삭제 됩니다. 그러나 수행할 수 있는 작업은 인라인 명령 후에 절전 모드를 도입 하 고 빌드가 일시 중지 될 때 로그를 가져옵니다. 이렇게 하려면 다음 단계를 수행 합니다.
 
1. 인라인 명령을 업데이트 하 고: Write Host/Echo "Sleep"을 추가 합니다. 그러면 로그에서 검색할 수 있습니다.
2. 절전 모드를 10 분 이상 추가 합니다. [절전 모드 또는](/powershell/module/microsoft.powershell.utility/start-sleep)Linux 명령을 사용할 수 있습니다 `Sleep` .
3. 위의 방법을 사용 하 여 로그 위치를 확인 한 다음, 절전 모드로 전환 될 때까지 로그를 다운로드 하 고 확인 합니다.


### <a name="operation-was-canceled"></a>작업이 취소되었습니다.

#### <a name="error"></a>Error

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>원인

사용자가 빌드를 취소 하지 않은 경우 Azure DevOps 사용자 에이전트에서 빌드를 취소 했습니다. Azure DevOps 기능으로 인해 1 시간 제한이 발생 했을 가능성이 높습니다. 개인 프로젝트 및 에이전트를 사용 하는 경우 60 분의 빌드 시간을 얻을 수 있습니다. 빌드가 시간 제한을 초과 하는 경우 DevOps는 실행 중인 작업을 취소 합니다.

Azure DevOps 기능 및 제한 사항에 대 한 자세한 내용은 [Microsoft 호스팅 에이전트](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops#capabilities-and-limitations) 를 참조 하세요.
 
#### <a name="solution"></a>솔루션

사용자 고유의 DevOps 에이전트를 호스팅하거나 빌드 시간을 줄일 수 있습니다. 예를 들어 공유 이미지 갤러리에 배포 하는 경우 한 지역에 복제 합니다. 비동기식으로 복제 하려는 경우 
 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>AIB 이미지에서 만든 Vm을 만들지 못했습니다.

기본적으로 Azure 이미지 작성기는 각 이미지 사용자 지정 단계 끝에서 *프로 비전* 해제 코드를 실행 하 여 이미지를 *일반화* 합니다. 일반화는 여러 Vm을 만드는 데 이미지를 다시 사용 하도록 설정 하 고 호스트 이름, 사용자 이름 등의 VM 설정을 전달할 수 있는 프로세스입니다. Windows의 경우 Azure 이미지 작성기는 *Sysprep*을 실행 하 고 Linux Azure 이미지 작성기를 실행 `waagent -deprovision` 합니다. 

Windows의 경우 Azure 이미지 작성기는 일반 Sysprep 명령을 사용 합니다. 그러나이는 모든 성공적인 Windows 일반화에 적합 하지 않을 수 있습니다. Azure 이미지 작성기를 사용 하 여 Sysprep 명령을 사용자 지정할 수 있습니다. 참고 Azure 이미지 작성기는 이미지 자동화 도구입니다. Sysprep 명령을 성공적으로 실행 해야 합니다. 그러나 이미지를 다시 사용할 수 있도록 하려면 다른 Sysprep 명령이 필요할 수 있습니다. Linux의 경우 Azure 이미지 작성기는 일반 `waagent -deprovision+user` 명령을 사용 합니다. 자세한 내용은 [Microsoft Azure Linux Agent 설명서](https://github.com/Azure/WALinuxAgent#command-line-options)를 참조 하세요.

기존 사용자 지정을 마이그레이션하는 경우 다른 Sysprep/waagent 명령을 사용 하면 이미지 작성기 일반 명령을 사용해 볼 수 있습니다. VM을 만들 때 오류가 발생 하면 이전 Sysprep/waagent 명령을 사용 합니다.

> [!NOTE]
> AIB에서 Windows 사용자 지정 이미지를 만들고이 이미지에서 VM을 만든 후 vm을 만들지 않는 경우 (예: VM 만들기 명령이 완료 되지 않았거나 시간이 초과 되지 않는 경우) Windows Server Sysprep 설명서를 검토 해야 합니다. 또는 올바른 Sysprep 명령에 대 한 문제를 해결 하 고 advise 할 수 있는 Windows Server Sysprep 고객 서비스 지원 팀과 함께 지원 요청을 발생 시킬 수 있습니다.

### <a name="command-locations-and-filenames"></a>명령 위치 및 파일 이름

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep 명령: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>프로 비전 해제 명령: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>명령 재정의

명령을 재정의 하려면 PowerShell 또는 shell 스크립트 provisioners를 사용 하 여 정확한 파일 이름으로 명령 파일을 만들고 앞에 나열 된 디렉터리에 배치 합니다. Azure 이미지 작성기는 이러한 명령을 읽고 출력은 *사용자 지정 .log*에 기록 됩니다.

## <a name="getting-support"></a>지원 받기
이 지침을 참조 했지만 여전히 문제를 해결할 수 없는 경우 지원 사례를 열 수 있습니다. 이 작업을 수행 하는 경우 적절 한 제품 및 지원 항목을 선택 하세요. 이렇게 하면 Azure VM 이미지 작성기 지원 팀에 참여 하 게 됩니다.

사례 제품 선택:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 이미지 작성기 개요](image-builder-overview.md)를 참조 하세요.
