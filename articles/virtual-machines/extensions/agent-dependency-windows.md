---
title: Windows 용 Azure Monitor 종속성 가상 머신 확장
description: 가상 컴퓨터 확장을 사용 하 여 Windows 가상 컴퓨터에 Azure Monitor 종속성 에이전트를 배포 합니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250674"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Windows 용 Azure Monitor 종속성 가상 머신 확장

VM용 Azure Monitor 맵 기능은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Windows 용 Azure VM 종속성 에이전트 가상 머신 확장은 Microsoft에서 게시 및 지원 합니다. 확장은 Azure virtual machines에 종속성 에이전트를 설치 합니다. 이 문서에서는 Windows 용 Azure VM 종속성 에이전트 가상 머신 확장에 대해 지원 되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명 합니다.

## <a name="operating-system"></a>운영 체제

Windows 용 Azure VM 종속성 에이전트 확장은 VM용 Azure Monitor 배포 문서의 [지원 되는 운영 체제](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 섹션에 나열 된 지원 되는 운영 체제에 대해 실행할 수 있습니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Azure Windows VM에서 Azure VM 종속성 에이전트 확장에 대 한 스키마를 보여 줍니다.

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

| 속성 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| 게시자 | DependencyAgent. |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용 하 여 Azure VM 확장을 배포할 수 있습니다. Azure Resource Manager 템플릿에서 이전 섹션에 자세히 설명 된 JSON 스키마를 사용 하 여 Azure Resource Manager 템플릿 배포 중에 Azure VM 종속성 에이전트 확장을 실행할 수 있습니다.

가상 머신 확장에 대 한 JSON은 가상 머신 리소스 내에 중첩 될 수 있습니다. 또는 리소스 관리자 JSON 템플릿의 루트 또는 최상위 수준에이를 추가할 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요.

다음 예에서는 종속성 에이전트 확장이 가상 머신 리소스 내에 중첩 되어 있다고 가정 합니다. 확장 리소스를 중첩할 때 JSON은 가상 컴퓨터의 `"resources": []` 개체에 배치 됩니다.


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

템플릿의 루트에 JSON 확장을 저장 하면 리소스 이름에 부모 가상 머신에 대 한 참조가 포함 됩니다. 형식은 중첩 된 구성을 반영 합니다.

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

`Set-AzVMExtension` 명령을 사용 하 여 종속성 에이전트 가상 머신 확장을 기존 가상 머신에 배포할 수 있습니다. 명령을 실행 하기 전에 공용 및 개인 구성을 PowerShell 해시 테이블에 저장 해야 합니다.

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

확장 배포 상태에 대 한 데이터는 Azure Portal 및 Azure PowerShell 모듈을 사용 하 여 검색할 수 있습니다. 지정 된 VM에 대 한 확장의 배포 상태를 확인 하려면 Azure PowerShell 모듈을 사용 하 여 다음 명령을 실행 합니다.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Support(지원)

이 문서의 어느 시점에서 든 도움이 필요한 경우 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에 게 문의할 수 있습니다. 또는 Azure 지원 인시던트를 파일에 입력할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용 하는 방법에 대 한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조 하세요.
