---
title: Windows용 Azure 모니터 종속성 가상 컴퓨터 확장
description: 가상 시스템 확장을 사용하여 Windows 가상 컴퓨터에 Azure 모니터 종속성 에이전트를 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250674"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Windows용 Azure 모니터 종속성 가상 컴퓨터 확장

VM용 Azure Monitor 맵 기능은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Windows용 Azure VM 종속성 에이전트 가상 컴퓨터 확장은 Microsoft에서 게시하고 지원합니다. 확장Azure 가상 시스템에 종속성 에이전트를 설치합니다. 이 문서에서는 Windows용 Azure VM 종속성 에이전트 가상 컴퓨터 확장에 대해 지원되는 플랫폼, 구성 및 배포 옵션을 자세히 설명합니다.

## <a name="operating-system"></a>운영 체제

Windows용 Azure VM 종속성 에이전트 확장은 VM 배포 문서의 [지원되는 운영 체제](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 섹션에 나열된 지원되는 운영 체제에 대해 실행할 수 있습니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Azure Windows VM에서 Azure VM 종속성 에이전트 확장에 대한 스키마를 보여 주며 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.모니터링.종속성 에이전트 |
| type | 종속성에이전트윈도우 |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>템플릿 배포

Azure 리소스 관리자 템플릿을 사용 하 고 Azure VM 확장을 배포할 수 있습니다. Azure 리소스 관리자 템플릿의 이전 섹션에 자세히 설명된 JSON 스키마를 사용하여 Azure 리소스 관리자 템플릿 배포 중에 Azure VM 종속성 에이전트 확장을 실행할 수 있습니다.

가상 시스템 확장에 대한 JSON은 가상 시스템 리소스 내에 중첩될 수 있습니다. 또는 리소스 관리자 JSON 템플릿의 루트 또는 최상위 수준에 배치할 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요.

다음 예제는 종속성 에이전트 확장이 가상 시스템 리소스 내에 중첩된 것으로 가정합니다. 확장 리소스를 중첩하면 JSON이 가상 `"resources": []` 시스템의 개체에 배치됩니다.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

템플릿의 루트에 확장 JSON을 배치하면 리소스 이름에 상위 가상 시스템에 대한 참조가 포함됩니다. 형식은 중첩된 구성을 반영합니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 배포

이 `Set-AzVMExtension` 명령을 사용하여 기존 가상 시스템에 종속성 에이전트 가상 컴퓨터 확장을 배포할 수 있습니다. 명령을 실행하기 전에 공용 및 개인 구성을 PowerShell 해시 테이블에 저장해야 합니다.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure 포털및 Azure PowerShell 모듈을 사용하여 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell 모듈을 사용하여 다음 명령을 실행합니다.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>고객 지원팀

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의](https://azure.microsoft.com/support/forums/)Azure 전문가에게 문의할 수 있습니다. 또는 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다. Azure 지원 사용 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ를](https://azure.microsoft.com/support/faq/)참조하십시오.
