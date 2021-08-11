---
title: Windows VM 확장 오류 문제 해결
description: Azure Windows VM 확장 오류 문제 해결에 대해 자세히 알아봅니다.
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: b79bea4cda89254620244d85f1b7daf8ec2e128e
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108738164"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 확장 오류 문제 해결
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>확장 상태 보기
Azure Powershell에서 Azure Resource Manager 템플릿을 실행할 수 있습니다. 템플릿을 실행하면 Azure Resource Explorer 또는 명령줄 도구에서 확장 상태를 볼 수 있습니다.

다음은 예제입니다.

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

샘플 출력은 다음과 같습니다.

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>확장 오류 문제 해결

### <a name="rerun-the-extension-on-the-vm"></a>VM에서 확장 다시 실행
사용자 지정 스크립트 확장을 사용하여 VM에서 스크립트를 실행하는 경우 때때로 VM이 성공적으로 만들어졌지만 스크립트가 실패하는 오류가 발생할 수 있습니다. 이러한 조건에서 이 오류를 복구하는 권장 방법은 확장을 제거하고 템플릿을 다시 실행하는 것입니다.
참고: 이후에는 확장을 제거할 필요가 없도록 이 기능이 향상될 예정입니다.

#### <a name="remove-the-extension-from-azure-powershell"></a>Azure Powershell에서 확장 제거
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

확장이 제거되면 템플릿을 다시 실행하여 VM에서 스크립트를 실행할 수 있습니다.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>VM에 새 GoalState 트리거
"Windows Azure CRP 인증서 생성기"가 누락되어 확장이 실행되지 않았거나 실행에 실패하는 것을 알 수 있습니다(해당 인증서는 확장의 보호된 설정 전송을 보호하는 데 사용됨).
해당 인증서는 Virtual Machine 내에서 Windows 게스트 에이전트를 다시 시작하여 자동으로 다시 생성됩니다.
- 태스크 관리자 열기
- 세부 정보 탭으로 이동
- WindowsAzureGuestAgent.exe 프로세스 찾기
- 마우스 오른쪽 단추로 클릭하고 "작업 종료"를 선택합니다. 프로세스는 자동으로 다시 시작됩니다.


"VM 다시 적용"을 실행하여 새 GoalState를 VM에 트리거할 수도 있습니다. VM [다시 적용](/rest/api/compute/virtualmachines/reapply)은 VM 상태를 다시 적용하기 위해 2020년에 도입된 API입니다. 짧은 VM 가동 중지 시간을 허용하려면 이 작업을 한 번에 수행하는 것이 좋습니다. 다시 적용 자체는 VM 재부팅을 발생시키지 않으며 Reapply를 호출하는 대부분의 경우 VM을 다시 부팅하지 않지만, 다시 적용이 새 목표 상태를 트리거할 때 VM 모델에 보류 중인 다른 업데이트가 적용되고 다른 변경에 다시 시작이 필요할 수 있는 위험이 매우 적습니다. 

Azure Portal:

포털에서 VM을 선택하고 왼쪽 창의 **지원 + 문제 해결** 아래에서 **다시 배포 + 다시 적용** 을 선택한 다음, **다시 적용** 을 선택합니다.


Azure PowerShell *(RG 이름 및 VM 이름을 사용자의 값으로 대체)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(RG 이름 및 VM 이름을 사용자의 값으로 대체)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

"VM 다시 적용"이 작동하지 않는 경우 Azure 관리 포털의 VM에 비어 있는 새 데이터 디스크를 추가한 다음, 나중에 인증서가 다시 추가되면 제거할 수 있습니다.