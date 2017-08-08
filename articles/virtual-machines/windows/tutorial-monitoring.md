---
title: "Azure 모니터링 및 Windows Virtual Machines | Microsoft Docs"
description: "자습서 - Azure PowerShell을 사용하여 Windows 가상 컴퓨터 모니터링"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>Azure PowerShell을 사용하여 Windows 가상 컴퓨터 모니터링

Azure 모니터링은 Azure VM에서 부팅 및 성능 데이터를 수집하고, Azure Storage에 이 데이터를 저장하고, 포털, Azure PowerShell 모듈 및 Azure CLI를 통해 액세스할 수 있도록 하는 에이전트를 사용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * VM 호스트 메트릭 보기
> * 진단 확장 설치
> * VM 메트릭 보기
> * 경고 만들기
> * 고급 모니터링 설정

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

이 자습서의 예제를 완료하려면 기존 가상 컴퓨터가 있어야 합니다. 필요한 경우 이 [스크립트 샘플](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)을 사용하여 가상 컴퓨터를 만들 수 있습니다. 자습서를 진행할 때 필요한 경우 리소스 그룹, VM 이름 및 위치를 바꿉니다.

## <a name="view-boot-diagnostics"></a>부트 진단 보기

Windows 가상 컴퓨터를 부팅하면 부트 진단 에이전트가 문제 해결 목적에 사용할 수 있는 화면 출력을 캡처합니다. 이 기능은 기본적으로 사용하도록 설정되어 있습니다. 캡처한 스크린샷은 기본적으로 생성되는 Azure Storage 계정에 저장됩니다. 

[Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) 명령으로 부트 진단 데이터를 가져올 수 있습니다. 다음 예제에서는 부트 진단이 *c:\* 드라이브의 루트에 다운로드됩니다. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>호스트 메트릭 보기

Windows VM에는 Azure에서 상호 작용하는 전용 호스트 VM이 있습니다. 이 호스트에 대한 메트릭이 자동으로 수집되며, Azure Portal에서 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. 호스트 VM이 어떻게 수행되는지 확인하려면 VM 블레이드에서 **메트릭**을 클릭한 다음 **사용 가능한 메트릭**에서 호스트 메트릭 중 하나를 선택합니다.

    ![호스트 메트릭 보기](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>진단 확장 설치

기본 호스트 메트릭을 사용할 수 있지만, 더 세분화된 VM 관련 메트릭을 보려면 VM에 Azure 진단 확장을 설치해야 합니다. Azure 진단 확장을 사용하면 VM에서 추가 모니터링 및 진단 데이터를 검색할 수 있습니다. 이러한 성능 메트릭을 보고 VM 성능에 따라 경고를 만들 수 있습니다. 진단 확장은 다음과 같이 Azure Portal을 통해 설치됩니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. **진단 설정**을 클릭합니다. 목록에서는 *부트 진단*이 이전 섹션에서 이미 사용하도록 설정되었음을 보여 줍니다. *기본 메트릭*에 대한 확인란을 클릭합니다.
3. **게스트 수준 모니터링을 사용하도록 설정** 단추를 클릭합니다.

    ![진단 메트릭 보기](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>VM 메트릭 보기

호스트 VM 메트릭을 본 것과 동일한 방법으로 VM 메트릭을 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. VM의 성능을 보려면 VM 블레이드에서 **메트릭**을 클릭한 다음 **사용 가능한 메트릭**에서 진단 메트릭 중 하나를 선택합니다.

    ![VM 메트릭 보기](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>경고 만들기

특정 성능 메트릭을 기반으로 하는 경고를 만들 수 있습니다. 예를 들어 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지면 경고를 사용하여 사용자에게 알릴 수 있습니다. 경고는 Azure Portal에 표시되거나 전자 메일을 통해 보낼 수 있습니다. 또한 생성되는 경고에 대한 응답으로 Azure Automation Runbook 또는 Azure Logic Apps를 트리거할 수도 있습니다.

다음 예제에서는 평균 CPU 사용량에 대한 경고를 만듭니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. VM 블레이드에서 **경고 규칙**을 클릭한 다음, 경고 블레이드 위쪽에 있는 **메트릭 경고 추가**를 클릭합니다.
4. *myAlertRule*과 같이 경고에 대한 **이름**을 입력합니다.
5. CPU 사용률이 5분 동안 1.0을 초과할 때 경고를 트리거하려면 다른 모든 기본값을 선택한 상태로 둡니다.
6. 필요한 경우 전자 메일 알림을 보내도록 *전자 메일 소유자, 참가자 및 읽기 권한자*의 확인란을 선택합니다. 기본 작업은 포털에서 알림을 제공하는 것입니다.
7. **확인** 단추를 클릭합니다.

## <a name="advanced-monitoring"></a>고급 모니터링 

[Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)를 사용하여 VM에 대한 고급 모니터링을 수행할 수 있습니다. 아직 사용해 보지 않았으면 Operations Management Suite의 [평가판](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)에 등록할 수 있습니다.

OMS 포털에 액세스할 수 있으면 설정 블레이드에서 작업 영역 키와 작업 영역 식별자를 찾을 수 있습니다. [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) 명령을 사용하여 OMS 확장을 VM에 추가합니다. 아래 샘플에서는 변수 값을 업데이트하여 OMS 작업 영역 키 및 작업 영역 ID를 표시합니다.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

몇 분 후 새 VM이 OMS 작업 영역에 표시됩니다. 

![OMS 블레이드](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Security Center를 사용하여 VM을 구성하고 검토했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 리소스 그룹 및 VM 만들기 
> * VM에서 부트 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * 진단 확장 설치
> * VM 메트릭 보기
> * 경고 만들기
> * 고급 모니터링 설정

Azure Security Center에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VM 보안 관리](./tutorial-azure-security.md)
