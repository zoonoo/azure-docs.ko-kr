---
title: Linux용 Azure Monitor 종속성 가상 머신 확장
description: 가상 머신 확장을 사용하여 Linux 가상 머신에 Azure Monitor 종속성 에이전트를 배포합니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/01/2021
ms.openlocfilehash: 97f557ec45530de3f42dd61ee1cded57fd7c33a0
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793749"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Linux용 Azure Monitor 종속성 가상 머신 확장

VM용 Azure Monitor 맵 기능은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Linux용 Azure VM 종속성 에이전트 가상 머신 확장은 Microsoft에서 게시 및 지원합니다. 이 확장은 Azure 가상 머신에 종속성 에이전트를 설치합니다. 이 문서에서는 Linux용 Azure VM 종속성 에이전트 가상 머신 확장에 대한 지원되는 플랫폼, 구성 및 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="operating-system"></a>운영 체제

Linux용 Azure VM 종속성 에이전트 확장은 VM용 Azure Monitor 배포 문서의 [지원되는 운영 체제](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 섹션에 나열된 지원되는 운영 체제에 실행할 수 있습니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Azure Linux VM의 Azure VM 종속성 에이전트 확장에 대한 스키마를 보여 줍니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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

| Name | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명한 JSON 스키마를 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 Azure VM 종속성 에이전트 확장을 실행할 수 있습니다.

가상 머신 확장용 JSON은 가상 머신 리소스 내에 중첩할 수 있습니다. 또는 Resource Manager JSON 템플릿의 루트 또는 최상위 수준에 배치할 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요.

다음 예제에서는 종속성 에이전트 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.


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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

템플릿의 루트에 JSON 확장을 저장하면 리소스 이름에 부모 가상 머신에 대한 참조가 포함됩니다. 형식은 중첩된 구성을 반영합니다. 

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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 종속성 에이전트 VM 확장을 기존 가상 머신에 배포할 수 있습니다.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="automatic-upgrade-preview"></a>자동 업그레이드 프로세스(미리 보기)
종속성 확장의 부 버전을 자동으로 업그레이드하는 새로운 기능을 이제 공개 미리 보기에서 사용할 수 있습니다. 이 기능을 사용하도록 설정하려면 다음 구성 변경을 수행해야 합니다.

-   [미리 보기 액세스 사용](../automatic-extension-upgrade.md#enabling-preview-access)의 방법 중 하나를 사용하여 구독 기능을 사용하도록 설정합니다.
- 템플릿에 `enableAutomaticUpgrade` 특성을 추가합니다.

종속성 에이전트 확장 버전 관리 체계는 다음 형식을 따릅니다.

```
<MM.mm.bb.rr> where M = Major version number, m = minor version number, b = bug number, r = revision number.
```

`enableAutomaticUpgrade` 및 `autoUpgradeMinorVersion` 특성은 함께 작동하여 구독의 가상 머신에 대해 업그레이드가 처리되는 방식을 결정합니다.

| enableAutomaticUpgrade | autoUpgradeMinorVersion | 영향 |
|:---|:---|:---|
| true | false | 최신 버전의 bb.rr이 있는 경우 종속성 에이전트를 업그레이드합니다. 예를 들어 9.6.0.1355를 실행 중이고 최신 버전이 9.6.2.1366인 경우 사용하도록 설정된 구독의 가상 머신은 9.6.2.1366으로 업그레이드됩니다. |
| true | true |  mm.bb.rr 또는 bb.rr의 최신 버전이 있는 경우 종속성 에이전트를 업그레이드합니다. 예를 들어 9.6.0.1355를 실행 중이고 최신 버전이 9.7.1.1416인 경우 사용하도록 설정된 구독의 가상 머신은 9.7.1.1416으로 업그레이드됩니다. 또한 9.6.0.1355를 실행 중이고 최신 버전이 9.6.2.1366인 경우 사용하도록 설정된 구독의 가상 머신은 9.6.2.1366으로 업그레이드됩니다. |
| false | true 또는 false | 자동 업그레이드를 사용할 수 없습니다.

> [!IMPORTANT]
> 템플릿에 `enableAutomaticUpgrade`를 추가하는 경우 최소 API 버전 2019-12-01을 사용해야 합니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>지원

이 문서의 어디서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의하세요. 또는 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기** 를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
