---
title: Linux용 가상 머신 확장 및 기능 | Microsoft Docs
description: 확장이 제공하거나 개선하는 기능별로 그룹화하여 Azure 가상 머신에 사용할 수 있는 확장을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 47cc812f9dac606cf4f69df9eff6d48095eb6ef8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32191178"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Linux용 가상 머신 확장 및 기능

Azure 가상 머신 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 응용 프로그램입니다. 예를 들어 가상 머신에서 소프트웨어가 설치되도록 요구하거나, 바이러스 백신 보호 또는 Docker 구성을 요구하는 경우 VM 확장을 사용하여 이러한 작업을 완료할 수 있습니다. Azure CLI, PowerShell, Azure Resource Manager 템플릿 및 Azure Portal을 사용하여 Azure VM 확장을 실행할 수 있습니다. 확장을 새 가상 머신 배포와 번들로 제공하거나 기존 시스템에 대해 실행할 수 있습니다.

이 문서에서는 VM 확장의 개요, Azure VM 확장 사용을 위한 필수 구성 요소, VM 확장을 검색, 관리 및 제거하는 방법에 대한 지침을 제공합니다. 많은 VM 확장 각각을 고유한 구성으로 사용할 수 있으므로 여기서는 일반적인 정보를 제공합니다. 확장 관련 세부 정보는 개별 확장과 관련된 각 문서에서 찾을 수 있습니다.

## <a name="use-cases-and-samples"></a>사용 사례 및 샘플

각각 특정 사용 사례가 있는 몇 가지 다른 Azure VM 확장을 사용할 수 있습니다. 일부 사례:

- Linux용 DSC 확장을 사용하여 가상 컴퓨터에 PowerShell의 필요한 상태 구성을 적용합니다. 자세한 내용은 [Azure 필요한 상태 구성 확장](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)을 참조하세요.
- Microsoft Monitoring Agent VM 확장을 사용하여 가상 머신의 모니터링을 구성합니다. 자세한 내용은 [Linux VM을 모니터링하는 방법](tutorial-monitoring.md)을 참조하세요.
- Datadog 확장으로 Azure 인프라의 모니터링을 구성합니다. 자세한 내용은 [Datadog 블로그](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)를 참조하세요.
- Docker VM 확장을 사용하여 Azure Virtual Machine에서 Docker 호스트를 구성합니다. 자세한 내용은 [Docker VM 확장](dockerextension.md)을 참조하세요.

프로세스 관련 확장 외에도 Windows 및 Linux 가상 머신에 대해 사용자 지정 스크립트 확장을 사용할 수 있습니다. Linux용 사용자 지정 스크립트 확장을 사용하면 Bash 스크립트를 가상 컴퓨터에서 실행할 수 있습니다. 사용자 지정 스크립트는 네이티브 Azure 도구로 제공할 수 있는 것 이상의 구성이 필요한 Azure 배포를 디자인할 때 유용합니다. 자세한 내용은 [Linux VM 사용자 지정 스크립트 확장](extensions-customscript.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

각 가상 머신 확장에는 고유한 필수 구성 요소 집합이 있을 수 있습니다. 예를 들어 Docker VM 확장에는 지원되는 Linux 배포판의 필수 구성 요소가 있습니다. 개별 확장의 요구 사항에 대해서는 확장 관련 설명서에 자세히 설명되어 있습니다.

### <a name="azure-vm-agent"></a>Azure VM 에이전트

Azure VM 에이전트는 Azure Virtual Machine과 Azure 패브릭 컨트롤러 간 상호 작용을 관리합니다. VM 에이전트는 VM 확장 실행을 포함하여 Azure Virtual Machine 배포 및 관리의 다양한 기능적 측면을 담당합니다. Azure VM 에이전트는 Azure Marketplace 이미지에 미리 설치되며 지원되는 운영 체제에 수동으로 설치될 수 있습니다.

지원되는 운영 체제 및 설치 지침에 대한 자세한 내용은 [Azure Virtual Machines 에이전트](agent-user-guide.md)를 참조하세요.

## <a name="discover-vm-extensions"></a>VM 확장 검색

Azure Virtual Machine와 함께 여러 다양한 VM 확장을 사용할 수 있습니다. 전체 목록을 보려면 Azure CLI와 함께 다음 명령을 실행하고 해당 예제 위치를 선택한 위치로 바꿉니다.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>VM 확장 실행

Azure 가상 머신 확장은 기존 가상 머신에서 실행할 수 있습니다. 이러한 기능은 이미 배포한 VM의 구성을 변경하거나 연결을 복구해야 할 때 유용합니다. 또한 VM 확장을 Azure Resource Manager 템플릿 배포와 번들로 묶을 수도 있습니다. Resource Manager 템플릿에서 확장을 사용하면 Azure Virtual Machine을 배포하고, 배포 후 개입 없이 구성할 수 있습니다.

다음 방법을 사용하여 기존 가상 머신에 대해 확장을 실행할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

ph x="1" /> 명령을 사용하여 기존 가상 머신에 대해 Azure Virtual Machine 확장을 실행할 수 있습니다. 이 예제에서는 가상 머신에 대한 사용자 지정 스크립트 확장을 실행합니다.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

이 스크립트는 다음 텍스트와 유사한 출력을 생성합니다.

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 기존 가상 컴퓨터에 VM 확장을 적용할 수 있습니다. 이렇게 하려면 가상 머신을 선택하고 **확장**을 선택한 후 **추가**를 클릭합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지시를 따릅니다.

다음 이미지는 Azure Portal에서 Linux 사용자 지정 스크립트 확장을 설치하는 경우를 보여 줍니다.

![Install custom script extension](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Resource Manager 템플릿에 VM 확장을 추가하고 템플릿 배포를 통해 실행할 수 있습니다. 템플릿을 사용하여 확장을 배포할 때 완전히 구성된 Azure 배포를 만들 수 있습니다. 예를 들어 다음 JSON은 Resource Manager 템플릿에서 가져옵니다. 이 템플릿은 부하 분산된 가상 머신 집합 및 Azure SQL Database를 배포한 후 각 VM에 .NET Core 응용 프로그램을 설치합니다. VM 확장은 소프트웨어 설치를 관리합니다.

자세한 내용은 전체 [Resource Manager 템플릿](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)을 참조하세요.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

자세한 내용은 [Azure Resource Manager 템플릿 작성](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions)을 참조하세요.

## <a name="secure-vm-extension-data"></a>VM 확장 데이터 보호

VM 확장을 실행하는 경우 자격 증명, 저장소 계정 이름 및 저장소 계정 액세스 키와 같은 중요한 정보를 포함해야 할 수도 있습니다. 많은 VM 확장에는 데이터를 암호화하고 대상 가상 머신 내에서만 해독하는 보호된 구성이 포함되어 있습니다. 각 확장에는 보호되는 특정 구성 스키마가 있으며 각각은 확장 관련 설명서에 자세히 나와 있습니다.

다음 예제에서는 Linux용 사용자 지정 스크립트 확장의 인스턴스를 보여 줍니다. 실행할 명령에는 자격 증명 집합이 포함됩니다. 이 예제에서는 실행할 명령이 암호화되지 않습니다.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

**실행할 명령** 속성을 **보호됨** 구성으로 이동하면 실행 문자열이 보호됩니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>VM 확장 문제 해결

각 VM 확장에는 확장 관련 문제 해결 단계가 있을 수 있습니다. 예를 들어 사용자 지정 스크립트 확장을 사용하는 경우 확장이 실행된 가상 머신에서 로컬로 스크립트 실행 세부 정보를 확인할 수 있습니다. 확장별 문제 해결 단계는 확장 관련 설명서에 자세히 나와 있습니다.

다음 문제 해결 단계는 모든 가상 머신 확장에 적용됩니다.

### <a name="view-extension-status"></a>확장 상태 보기

가상 머신에 대해 가상 머신 확장을 실행한 후에 다음 Azure CLI 명령을 사용하여 확장 상태를 반환합니다. 매개 변수 이름을 고유한 값으로 바꿉니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

출력은 다음 텍스트와 비슷합니다.

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Azure Portal에서 확장 실행 상태를 찾을 수도 있습니다. 확장의 상태를 확인하려면 가상 컴퓨터를 선택하고 **확장**을 선택한 후 원하는 확장을 선택합니다.

### <a name="rerun-a-vm-extension"></a>VM 확장 다시 실행

가상 머신 확장을 다시 실행해야 하는 경우가 있을 수 있습니다. 확장을 다시 실행하려면 확장을 제거한 다음 원하는 실행 방법으로 확장을 다시 실행하면 됩니다. 확장을 제거하려면 Azure CLI를 사용하여 다음 명령을 실행합니다. 매개 변수 이름을 고유한 값으로 바꿉니다.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Azure Portal에서 다음 단계에 사용하여 확장을 제거할 수 있습니다.

1. 가상 머신을 선택합니다.
2. **확장**을 선택합니다.
3. 원하는 확장을 선택합니다.
4. **제거**를 선택합니다.

## <a name="common-vm-extension-reference"></a>일반적인 VM 확장 참조
| 확장 이름 | 설명 | 자세한 정보 |
| --- | --- | --- |
| Linux용 사용자 지정 스크립트 확장 |Azure Virtual Machine에 대해 스크립트 실행 |[Linux용 사용자 지정 스크립트 확장](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Docker 확장 |원격 Docker 명령을 지원하기 위해 Docker 데몬을 설치합니다. |[Docker VM 확장](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| VM 액세스 확장 |Azure Virtual Machine에 대한 액세스 권한 복구 |[VM 액세스 확장](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure 진단 확장 |Azure 진단 관리 |[Azure 진단 확장](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 액세스 확장 |사용자 및 자격 증명 관리 |[Linux용 VM 액세스 확장](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
