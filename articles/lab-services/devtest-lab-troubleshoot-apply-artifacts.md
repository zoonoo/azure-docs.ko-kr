---
title: Azure DevTest 랩의 아티팩트 문제 해결 | 마이크로 소프트 문서
description: Azure DevTest Labs 가상 컴퓨터에서 아티팩트를 적용할 때 발생하는 문제를 해결하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456979"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Azure DevTest Labs 가상 컴퓨터에서 아티팩트를 적용할 때 문제 해결
가상 시스템에 아티팩트를 적용하는 것은 여러 가지 이유로 실패할 수 있습니다. 이 문서에서는 가능한 원인을 식별하는 데 도움이 되는 몇 가지 방법을 안내합니다.

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의](https://azure.microsoft.com/support/forums/)DTL(Azure DevTest Labs) 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 지원 받기를 선택합니다.   

> [!NOTE]
> 이 문서는 Windows 및 비Windows 가상 컴퓨터에 모두 적용됩니다. 몇 가지 차이점이 있지만 이 문서에서 명시적으로 호출됩니다.

## <a name="quick-troubleshooting-steps"></a>빠른 문제 해결 단계
VM이 실행 중이면 확인합니다. DevTest 연구소에서 VM을 실행 해야 하 고 [Microsoft Azure 가상 시스템 에이전트 (VM 에이전트)를](../virtual-machines/extensions/agent-windows.md) 설치 하 고 준비.

> [!TIP]
> Azure **포털에서**VM의 **아티팩트 관리** 페이지로 이동하여 VM이 아티팩트를 적용할 준비가 되었는지 확인합니다. 해당 페이지의 맨 위에 메시지가 표시됩니다. 
> 
> **Azure PowerShell을**사용하여 **플래그를 검사할 수 있습니다GET**작업을 확장할 때만 반환되는 아티팩트를 적용합니다. 다음 예제 명령을 참조하십시오.

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
다음 방법 중 하나를 사용하여 DevTest Labs 및 리소스 관리자 배포 모델을 사용하여 만든 VM 문제를 해결할 수 있습니다.

- **Azure 포털** - 문제의 원인을 시각적으로 빠르게 확인해야 하는 경우 적합합니다.
- **Azure PowerShell** - PowerShell 프롬프트에 익숙한 경우 Azure PowerShell cmdlet을 사용하여 DevTest Labs 리소스를 빠르게 쿼리합니다.

> [!TIP]
> VM 내에서 아티팩트 실행을 검토하는 방법에 대한 자세한 내용은 [랩의 아티팩트 오류 진단을](devtest-lab-troubleshoot-artifact-failure.md)참조하십시오.

## <a name="symptoms-causes-and-potential-resolutions"></a>증상, 원인 및 잠재적 해결 방법 

### <a name="artifact-appears-to-hang"></a>아티팩트가 중단된 것처럼 보입니다.   
미리 정의된 시간 지정 기간이 만료되고 아티팩트가 **실패로**표시될 때까지 아티팩트가 중단된 것처럼 보입니다.

아티팩트가 중단된 것처럼 나타나면 먼저 아티팩트가 멈췄던 위치를 결정합니다. 아티팩트는 실행 하는 동안 다음 단계 중 어느 단계에서 차단될 수 있습니다.

- **초기 요청 중에**. DevTest 연구소는 CSE(사용자 지정 스크립트 확장)의 사용을 요청하는 Azure 리소스 관리자 템플릿을 만듭니다. 따라서 뒤에서 리소스 그룹 배포가 트리거됩니다. 이 수준에서 오류가 발생하면 해당 VM에 대한 리소스 그룹의 **활동 로그에** 대한 세부 정보를 얻을 수 있습니다.  
    - 랩 VM 페이지 탐색 모음에서 활동 로그에 액세스할 수 있습니다. 이 옵션을 선택하면 **가상 시스템에 아티팩트를 적용하거나(아티팩트** 적용 작업이 직접 트리거된 경우) 또는 **가상 시스템 추가 또는 수정(적용** 아티팩트 작업이 VM 생성 프로세스의 일부인 경우) 항목이 표시됩니다.
    - 이러한 항목에서 오류를 찾습니다. 경우에 따라 오류에 따라 태그가 지정되지 않으며 각 항목을 조사해야 합니다.
    - 각 항목의 세부 정보를 조사할 때 JSON 페이로드의 내용을 검토해야 합니다. 해당 문서의 맨 아래에 오류가 표시될 수 있습니다.
- **아티팩트를 실행하려고 할 때**. 네트워킹 또는 저장소 문제 때문일 수 있습니다. 자세한 내용은 이 문서의 각 섹션을 참조하십시오. 스크립트를 작성하는 방식 때문에 발생할 수도 있습니다. 예를 들어:
    - PowerShell 스크립트에는 **필수 매개 변수가**있지만 사용자가 비워 두도록 허용하거나 artifactfile.json 정의 파일에 속성에 대한 기본값이 없기 때문에 값을 전달하지 못합니다. 스크립트는 사용자 입력을 기다리고 있기 때문에 중단됩니다.
    - PowerShell 스크립트는 실행의 일부로 **사용자 입력이 필요합니다.** 스크립트는 사용자 개입 없이 자동으로 작동하도록 작성해야 합니다.
- **VM 에이전트는 준비하는 데 오래 걸립니다.** VM이 처음 시작되거나 아티팩트 적용 요청을 제공하기 위해 사용자 지정 스크립트 확장이 처음 설치된 경우 VM은 VM 에이전트를 업그레이드하거나 VM 에이전트가 초기화될 때까지 기다려야 할 수 있습니다. 초기화하는 데 시간이 오래 걸리는 VM 에이전트가 종속되는 서비스가 있을 수 있습니다. 이러한 경우 추가 문제 해결에 대한 [Azure 가상 시스템 에이전트 개요를](../virtual-machines/extensions/agent-windows.md) 참조하세요.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>스크립트로 인해 아티팩트가 중단되는지 확인하려면

1. 문제의 가상 시스템에 로그인합니다.
2. 가상 컴퓨터에서 로컬로 스크립트를 복사하거나 아래의 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`가상 컴퓨터에서 스크립트를 찾습니다. 아티팩트 스크립트가 다운로드되는 위치입니다.
3. 상승된 명령 프롬프트를 사용하여 스크립트를 로컬로 실행하여 문제를 일으키는 데 사용되는 것과 동일한 매개 변수 값을 제공합니다.
4. 스크립트가 원치 않는 동작으로 인해 발생하는지 확인합니다. 그렇다면 아티팩트에 대한 업데이트를 요청합니다(공용 리포지토리의 경우). 또는 개인 리포지토리에서 수정할 수 있습니다.

> [!TIP]
> [공개 리포지토리에서](https://github.com/Azure/azure-devtestlab) 호스팅되는 아티팩트의 문제를 수정하고 검토 및 승인을 위해 변경 사항을 제출할 수 있습니다. [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) 문서의 **기여** 섹션을 참조하십시오.
> 
> 사용자 고유의 아티팩트 작성에 대한 자세한 내용은 [문서를 AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) 참조하세요.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>VM 에이전트로 인해 아티팩트가 중단되는 지 확인하려면 다음을 수행하십시오.
1. 문제의 가상 시스템에 로그인합니다.
2. 파일 탐색기를 사용하여 **C:\WindowsAzure\로그로 이동합니다.**
3. 파일 **WaAppAgent.log를**찾아 엽니다.
4. VM 에이전트가 언제 시작되고 초기화가 완료될 때(즉, 첫 번째 하트비트가 전송되는경우)가 표시됩니다. 최신 항목 또는 특히 문제가 발생한 기간 주변의 항목을 선호합니다.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    이 예제에서는 하트비트가 전송되었기 때문에 VM 에이전트 시작 시간이 10분 20초가 걸린 것을 확인할 수 있습니다. 이 경우 OOBE 서비스를 시작하는 데 시간이 오래 걸리는 원인이 되었습니다.

> [!TIP]
> Azure 확장에 대한 일반 정보는 [Azure 가상 컴퓨터 확장 및 기능을](../virtual-machines/extensions/overview.md)참조하십시오.

## <a name="storage-errors"></a>저장소 오류
DevTest 연구소에서는 아티팩트를 캐시하기 위해 만들어진 랩의 저장소 계정에 액세스해야 합니다. DevTest 연구소에서 아티팩트를 적용하면 구성된 리포지토리에서 아티팩트 구성 및 해당 파일을 읽습니다. 기본적으로 DevTest 연구소는 **공용 아티팩트 리포지토리에**대한 액세스를 구성합니다.

VM을 구성하는 방법에 따라 이 리포지토리에 직접 액세스하지 못할 수 있습니다. 따라서 DevTest Labs는 랩이 처음 초기화될 때 생성된 저장소 계정에 아티팩트를 캐시합니다.

VM에서 Azure Storage 서비스로 트래픽이 차단된 경우와 같이 이 저장소 계정에 대한 액세스가 어떤 식으로든 차단되면 다음과 유사한 오류가 표시될 수 있습니다.

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

위의 오류는 **아티팩트 관리**아래의 **아티팩트 결과** 페이지의 배포 **메시지** 섹션에 나타납니다. 문제의 가상 시스템의 리소스 그룹 아래의 **활동 로그에도** 표시됩니다.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Azure Storage 서비스에 대한 통신이 차단되지 않도록 하려면 다음을 수행하십시오.

- **NSG(네트워크 보안 그룹)가 추가된지 확인합니다.** NSG가 모든 가상 네트워크에서 자동으로 구성되는 구독 정책이 추가되었을 수 있습니다. 또한 VM 을 만드는 데 사용되는 랩의 기본 가상 네트워크 또는 랩에 구성된 다른 가상 네트워크에도 영향을 미칩니다.
- **기본 랩의 저장소** 계정(즉, 랩을 만들 때 생성된 첫 번째 저장소 계정, 일반적으로 이름은 문자 "a"로 시작하여\<랩 이름\>#인 다자리 숫자로 끝나는)을 확인합니다.
    1. 랩의 리소스 그룹으로 이동합니다.
    2. 이름이 규칙과 일치하는 형식 **저장소 계정의**리소스를 찾습니다.
    3. 방화벽 및 가상 네트워크라는 저장소 계정 페이지로 **이동합니다.**
    4. **모든 네트워크로**설정되어 있는지 확인합니다. 선택한 **네트워크** 옵션을 선택한 경우 VM을 만드는 데 사용되는 랩의 가상 네트워크가 목록에 추가되었는지 확인합니다.

보다 자세한 문제 해결은 [Azure 저장소 방화벽 및 가상 네트워크 구성을](../storage/common/storage-network-security.md)참조하십시오.

> [!TIP]
> **네트워크 보안 그룹 규칙을 확인합니다.** [IP 흐름 확인을](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) 사용하여 네트워크 보안 그룹의 규칙이 가상 컴퓨터와의 트래픽을 차단하고 있는지 확인합니다. 효과적인 보안 그룹 규칙을 검토하여 인바운드 **허용** NSG 규칙이 있는지 확인할 수도 있습니다. 자세한 내용은 [효과적인 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결](../virtual-network/diagnose-network-traffic-filter-problem.md)을 참조하세요.

## <a name="other-sources-of-error"></a>기타 오류 원인
다른 덜 자주 가능한 오류 소스가 있습니다. 각 서비스 케이스에 적용되는지 확인하기 위해 각 을 평가하십시오. 다음은 그 중 하나입니다. 

- **개인 리포지토리에 대한 개인 액세스 토큰이 만료되었습니다.** 만료되면 아티팩트가 나열되지 않으며 만료된 개인 액세스 토큰이 있는 리포지토리의 아티팩트를 참조하는 스크립트는 그에 따라 실패합니다.

## <a name="next-steps"></a>다음 단계
이러한 오류가 발생하지 않았고 아티팩트를 적용할 수 없는 경우 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.

