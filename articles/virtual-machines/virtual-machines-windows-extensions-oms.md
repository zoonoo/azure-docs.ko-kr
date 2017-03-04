---
title: "Windows용 OMS Azure Virtual Machine 확장 | Microsoft Docs"
description: "가상 컴퓨터 확장을 사용하여 Windows 가상 컴퓨터에 OMS 에이전트를 배포합니다."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/08/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 251d7b973426afb50206c428873021144b8bffdf
ms.openlocfilehash: 63e2509b92b4d97bfdc98629cc356816839b03b5
ms.lasthandoff: 01/11/2017


---
# <a name="oms-virtual-machine-extension-for-windows"></a>Windows용 OMS 가상 컴퓨터 확장

OMS(Operations Management Suite)는 클라우드와 온-프레미스 자산에서 모니터링, 경고 및 경고 수정 기능을 제공합니다. Windows용 OMS 에이전트 가상 컴퓨터 확장은 Microsoft에서 게시 및 지원합니다. 확장 버전은 Azure Virtual Machines에 OMS 에이전트를 설치하고 기존 OMS 작업 영역에 Virtual Machines를 등록합니다. 이 문서에서는 지원되는 플랫폼, 구성 및 Windows용 OMS 가상 컴퓨터 확장에 대한 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제
Windows용 OMS 에이전트 확장은 Windows Server 2008 R2, 2012, 2012 R2 및 2016 릴리스에 대해 실행할 수 있습니다.

### <a name="internet-connectivity"></a>인터넷 연결
Windows용 OMS 에이전트 확장은 대상 가상 컴퓨터가 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 OMS 에이전트 확장에 대한 스키마를 보여줍니다. 이 확장은 대상 OMS 작업 영역에서 작업 영역 ID와 작업 영역 키가 필요하며, OMS 포털에서 확인할 수 있습니다. 작업 영역 키는 중요한 데이터로 처리되므로 보호되는 설정에 저장됩니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 컴퓨터에서만 해독됩니다.

```json
{
    "type": "extensions",
    "name": "Microsoft.EnterpriseCloud.Monitoring",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId(예) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey(예) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명되어 있는 JSON 스키마는 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 OMS 에이전트 확장을 실행할 수 있습니다. OMS 에이전트 VM 확장을 포함하는 샘플 템플릿은 [Azure 빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)에서 찾을 수 있습니다. 

## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzureRmVMExtension` 명령을 사용하여 OMS 에이전트 가상 컴퓨터 확장을 기존 가상 컴퓨터에 배포할 수 있습니다. 명령을 실행하기 전에 공용 및 개인 구성을 PowerShell 해시 테이블에 저장해야 합니다. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 모듈을 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell 모듈을 사용하여 다음 명령을 실행합니다.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/en-us/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/en-us/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/en-us/support/faq/)를 참조하세요.

