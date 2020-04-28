---
title: Azure DevTest Labs의 아티팩트 문제 해결 | Microsoft Docs
description: Azure DevTest Labs 가상 머신에서 아티팩트를 적용할 때 발생 하는 문제를 해결 하는 방법을 알아봅니다.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75456979"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Azure DevTest Labs 가상 컴퓨터에서 아티팩트를 적용할 때 발생 하는 문제 해결
가상 컴퓨터에 아티팩트를 적용 하는 것은 다양 한 이유로 실패할 수 있습니다. 이 문서에서는 가능한 원인을 식별 하는 데 도움이 되는 몇 가지 방법을 안내 합니다.

이 문서의 어느 시점에서 든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 AZURE DEVTEST LABS (DTL) 전문가에 게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 지원 받기를 선택 합니다.   

> [!NOTE]
> 이 문서는 Windows 및 Windows가 아닌 가상 컴퓨터에 모두 적용 됩니다. 약간의 차이가 있지만이 문서에서는이를 명시적으로 호출 합니다.

## <a name="quick-troubleshooting-steps"></a>빠른 문제 해결 단계
VM이 실행 중인지 확인 합니다. DevTest Labs를 사용 하려면 VM이 실행 중이 고 [Microsoft Azure 가상 컴퓨터 에이전트 (Vm 에이전트)](../virtual-machines/extensions/agent-windows.md) 가 설치 되어 있고 준비 되어 있어야 합니다.

> [!TIP]
> **Azure Portal**에서 vm에 대 한 **아티팩트 관리** 페이지로 이동 하 여 vm이 아티팩트를 적용할 준비가 되었는지 확인 합니다. 해당 페이지의 맨 위에 메시지가 표시 됩니다. 
> 
> **Azure PowerShell**를 사용 하 여 GET 작업을 확장할 때만 반환 되는 플래그 **canapplyartifacts**를 검사 합니다. 다음 예제 명령을 참조 하세요.

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>문제 해결 방법 
다음 방법 중 하나를 사용 하 여 DevTest Labs 및 리소스 관리자 배포 모델을 사용 하 여 만든 Vm 문제를 해결할 수 있습니다.

- **Azure Portal** -문제의 원인이 될 수 있는 요소에 대 한 시각적 힌트를 신속 하 게 받아야 하는 경우에 유용 합니다.
- **Azure PowerShell** -PowerShell 프롬프트에 익숙한 경우 Azure PowerShell cmdlet을 사용 하 여 DevTest Labs 리소스를 신속 하 게 쿼리 합니다.

> [!TIP]
> VM 내에서 아티팩트 실행을 검토 하는 방법에 대 한 자세한 내용은 [랩에서 아티팩트 실패 진단](devtest-lab-troubleshoot-artifact-failure.md)을 참조 하세요.

## <a name="symptoms-causes-and-potential-resolutions"></a>증상, 원인 및 잠재적 해결 방법 

### <a name="artifact-appears-to-hang"></a>아티팩트가 멈춘 것 처럼 보입니다.   
미리 정의 된 시간 제한 기간이 만료 될 때까지 아티팩트가 중단 된 것 처럼 보이고 아티팩트가 **실패로**표시 됩니다.

아티팩트가 중단 된 것으로 표시 되 면 먼저 중지 된 위치를 확인 합니다. 실행 중에 다음 단계 중 하나에서 아티팩트를 차단할 수 있습니다.

- **초기 요청 중**입니다. DevTest Labs는 CSE (사용자 지정 스크립트 확장) 사용을 요청 하는 Azure Resource Manager 템플릿을 만듭니다. 따라서 내부적으로 리소스 그룹 배포가 트리거됩니다. 이 수준에서 오류가 발생 하는 경우 해당 VM에 대 한 리소스 그룹의 **활동 로그** 에서 세부 정보를 볼 수 있습니다.  
    - 활동 로그는 랩 VM 페이지 탐색 모음에서 액세스할 수 있습니다. 이 항목을 선택 하면 **가상 컴퓨터에 아티팩트를 적용 하** 는 항목 (아티팩트 적용 작업이 직접 트리거된 경우) 또는 **가상 컴퓨터 추가 또는 수정** (아티팩트 적용 작업이 VM 생성 프로세스의 일부인 경우)에 대 한 항목이 표시 됩니다.
    - 이러한 항목에서 오류를 찾습니다. 경우에 따라 오류의 태그가 지정 되지 않으므로 각 항목을 조사 해야 합니다.
    - 각 항목에 대 한 세부 정보를 조사할 때 JSON 페이로드의 내용을 검토 해야 합니다. 해당 문서의 맨 아래에 오류가 표시 될 수 있습니다.
- **아티팩트를 실행 하려고**합니다. 네트워킹 또는 저장소 문제로 인 한 것일 수 있습니다. 자세한 내용은이 문서의 뒷부분에 있는 해당 섹션을 참조 하세요. 스크립트를 작성 하는 방식으로 인해 발생할 수도 있습니다. 다음은 그 예입니다.
    - PowerShell 스크립트에는 **필수 매개 변수가**있지만 그 중 하나는 값을 비워 둘 수 있습니다. 그렇지 않으면 사용자가 값을 비워 둘 수 있습니다. 그렇지 않으면 artifactfile. json 정의 파일에 속성에 대 한 기본값이 없기 때문입니다. 사용자 입력을 대기 하는 중 이므로 스크립트가 중지 됩니다.
    - PowerShell 스크립트를 실행 하는 동안 **사용자 입력이 필요** 합니다. 사용자 개입 없이도 자동으로 작동 하도록 스크립트를 작성 해야 합니다.
- **VM 에이전트가 준비 되는 데 시간이 오래 걸립니다**. VM을 처음 시작 하거나 아티팩트 적용 요청을 제공 하기 위해 사용자 지정 스크립트 확장을 처음 설치 하는 경우 vm에서 vm 에이전트를 업그레이드 하거나 VM 에이전트가 초기화 될 때까지 기다려야 할 수 있습니다. VM 에이전트가 초기화 하는 데 시간이 오래 걸리는 서비스가 있을 수 있습니다. 이러한 경우 추가 문제 해결을 위해 [Azure Virtual Machine 에이전트 개요](../virtual-machines/extensions/agent-windows.md) 를 참조 하세요.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>스크립트로 인해 아티팩트가 중단 된 것으로 표시 되는지 확인 하려면

1. 문제가 있는 가상 컴퓨터에 로그인 합니다.
2. 가상 컴퓨터에서 로컬로 스크립트를 복사 하거나 아래의 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`가상 컴퓨터에서 스크립트를 찾습니다. 아티팩트 스크립트를 다운로드 하는 위치입니다.
3. 관리자 권한 명령 프롬프트를 사용 하 여 스크립트를 로컬로 실행 하 고 문제를 발생 시키는 데 사용 되는 것과 동일한 매개 변수 값을 제공 합니다.
4. 스크립트가 원치 않는 동작에서 발생 하는지 여부를 확인 합니다. 이 경우 아티팩트에 대 한 업데이트를 요청 합니다 (공용 리포지토리의 경우). 또는 직접 수정 합니다 (개인 리포지토리의 경우).

> [!TIP]
> [공개](https://github.com/Azure/azure-devtestlab) 리포지토리에서 호스트 되는 아티팩트와 관련 된 문제를 해결 하 고 검토 및 승인에 대 한 변경 내용을 제출할 수 있습니다. [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) 문서의 **기여** 섹션을 참조 하세요.
> 
> 사용자 고유의 아티팩트를 작성 하는 방법에 대 한 자세한 내용은 [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) 문서를 참조 하세요.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>VM 에이전트로 인해 아티팩트가 중단 된 것으로 표시 되는지 확인 하려면 다음을 수행 합니다.
1. 문제가 있는 가상 컴퓨터에 로그인 합니다.
2. 파일 탐색기를 사용 하 여 **C:\windowsazure\logs**로 이동 합니다.
3. **Waappagent.exe**파일을 찾아 엽니다.
4. VM 에이전트가 시작 될 때와 초기화가 완료 되는 시점을 표시 하는 항목을 찾습니다 (즉, 첫 번째 하트 비트가 전송 됨). 최신 항목을 선호 하거나 문제가 발생 한 기간을 기준으로 합니다.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    이 예에서는 하트 비트를 전송 했기 때문에 VM 에이전트 시작 시간이 10 분 20 초 소요 된 것을 볼 수 있습니다. 이 경우의 원인은 OOBE 서비스를 시작 하는 데 오랜 시간이 걸립니다.

> [!TIP]
> Azure 확장에 대 한 일반 정보는 [azure virtual machine 확장 및 기능](../virtual-machines/extensions/overview.md)을 참조 하세요.

## <a name="storage-errors"></a>저장소 오류
DevTest Labs를 사용 하려면 아티팩트를 캐시 하기 위해 만든 랩의 저장소 계정에 액세스 해야 합니다. DevTest Labs에서 아티팩트를 적용 하는 경우 구성 된 리포지토리에서 아티팩트 구성과 해당 파일을 읽습니다. 기본적으로 DevTest Labs는 **공용 아티팩트**리포지토리에 대 한 액세스를 구성 합니다.

VM이 구성 된 방법에 따라이 리포지토리에 직접 액세스 하지 못할 수 있습니다. 따라서 DevTest Labs는 랩을 처음 초기화할 때 생성 된 저장소 계정에서 아티팩트를 캐시 합니다.

VM에서 Azure Storage 서비스로의 트래픽이 차단 되는 경우와 같이이 저장소 계정에 대 한 액세스가 차단 되 면 다음과 유사한 오류가 표시 될 수 있습니다.

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

위의 오류는 아티팩트 **관리**에서 **아티팩트 결과** 페이지의 **배포 메시지** 섹션에 표시 됩니다. 또한 문제가 있는 가상 컴퓨터의 리소스 그룹 아래에 있는 **활동 로그** 에도 표시 됩니다.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Azure Storage 서비스에 대 한 통신이 차단 되지 않도록 하려면 다음을 수행 합니다.

- **추가 된 NSG (네트워크 보안 그룹)를 확인**합니다. 모든 가상 네트워크에서 NSGs가 자동으로 구성 되는 구독 정책이 추가 된 것일 수 있습니다. 랩의 기본 가상 네트워크 (사용 되는 경우) 또는 랩에서 구성 된 다른 가상 네트워크에도 영향을 줍니다 .이는 Vm을 만드는 데 사용 됩니다.
- **기본 랩의 저장소 계정** (랩을 만들 때 만든 첫 번째 저장소 계정)을 확인 합니다. 이름은 일반적으로 문자 "a"로 시작 하 고 여러 자리 숫자 (예,\<연구원 이름\>#)로 끝납니다.
    1. 랩의 리소스 그룹으로 이동 합니다.
    2. 이름이 규칙과 일치 하는 **저장소 계정**유형의 리소스를 찾습니다.
    3. **방화벽 및 가상 네트워크**라는 저장소 계정 페이지로 이동 합니다.
    4. **모든 네트워크**로 설정 되었는지 확인 합니다. **선택한 네트워크** 옵션을 선택한 경우에는 vm을 만드는 데 사용 된 랩의 가상 네트워크가 목록에 추가 되어 있는지 확인 합니다.

자세한 문제 해결에 대 한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md)을 참조 하세요.

> [!TIP]
> **네트워크 보안 그룹 규칙을 확인**합니다. [IP 흐름 확인](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) 을 사용 하 여 네트워크 보안 그룹의 규칙이 가상 머신에서 들어오고 나가는 트래픽을 차단 하는지 확인 합니다. 유효한 보안 그룹 규칙을 검토 하 여 인바운드 **허용** nsg 규칙이 있는지 확인할 수도 있습니다. 자세한 내용은 [효과적인 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결](../virtual-network/diagnose-network-traffic-filter-problem.md)을 참조하세요.

## <a name="other-sources-of-error"></a>기타 오류 원인
오류가 발생할 수 있는 원인의 원인에는 제한이 없습니다. 각 항목을 평가 하 여 사용자의 사례에 적용 되는지 확인 해야 합니다. 다음은 그 중 하나입니다. 

- **개인 리포지토리의 개인 액세스 토큰이 만료 되었습니다**. 만료 되 면 아티팩트가 나열 되지 않으며, 저장소에서의 아티팩트를 참조 하는 모든 스크립트 (전용 액세스 토큰이 만료 됨)가 그에 따라 실패 합니다.

## <a name="next-steps"></a>다음 단계
이러한 오류가 발생 하지 않고 아티팩트를 계속 적용할 수 없는 경우 Azure 지원 인시던트를 파일에 추가할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 **지원 받기**를 선택 합니다.

