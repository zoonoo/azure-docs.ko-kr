---
title: Azure의 Linux VM에서 사용자 지정 스크립트 실행 | Microsoft Docs
description: 사용자 지정 스크립트 확장을 사용하여 Linux VM 구성 작업 자동화
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 88f337df14b7e2647a76cce7ef91ec6f8950bbc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Linux 가상 머신에서 Azure 사용자 지정 스크립트 확장 사용
사용자 지정 스크립트 확장은 Azure 가상 머신에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트를 Azure Storage 또는 기타 액세스가 가능한 인터넷 위치에서 다운로드하거나 확장 런타임을 제공할 수 있습니다. 

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합됩니다. Azure CLI, PowerShell, Azure Portal 또는 Azure Virtual Machines REST API를 사용하여 실행할 수 있습니다.

이 문서에서는 Azure CLI에서 사용자 지정 스크립트 확장을 사용하는 방법 및 Azure Resource Manager 템플릿을 사용하여 확장을 실행하는 방법을 자세히 설명합니다. 또한 Linux 시스템에 대한 문제 해결 단계도 제공합니다.

## <a name="extension-configuration"></a>확장 구성
사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 이 구성을 구성 파일에 저장하거나, 명령줄에 지정하거나, Azure Resource Manager 템플릿에 지정할 수 있습니다. 

중요한 데이터는 보호된 구성에 저장하면 암호화된 후 가상 머신 내에서만 해독됩니다. 보호된 구성은 실행 명령에 암호와 같은 기밀 정보가 포함될 때 유용합니다.

### <a name="public-configuration"></a>공용 구성
공용 구성에 대한 스키마는 다음과 같습니다.

>[!NOTE]
>이러한 속성 이름은 대/소문자를 구분합니다. 배포 문제를 방지하려면 다음과 같이 이름을 사용합니다.

* **commandToExecute**: (필수, 문자열) 실행할 진입점 스크립트입니다.
* **fileUris**: (옵션, 문자열 배열) 다운로드할 파일의 URL입니다.
* **timestamp** (옵션, 정수): 스크립트의 타임스탬프입니다. 스크립트의 다시 실행을 트리거하려는 경우에만 이 필드의 값을 변경합니다.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>보호된 구성
보호된 구성에 대한 스키마는 다음과 같습니다.

>[!NOTE]
>이러한 속성 이름은 대/소문자를 구분합니다. 배포 문제를 방지하려면 다음과 같이 이름을 사용합니다.

* **commandToExecute**: (옵션, 문자열) 실행할 진입점 스크립트입니다. 명령에 암호와 같은 기밀 정보가 포함되는 경우 이 필드를 사용합니다.
* **storageAccountName**: (옵션, 문자열) 저장소 계정의 이름입니다. 저장소 자격 증명을 지정하는 경우 모든 파일 URI는 Azure Blob에 대한 URL이어야 합니다.
* **storageAccountKey**: (옵션, 문자열) 저장소 계정의 액세스 키입니다.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용하여 사용자 지정 스크립트 확장을 실행하는 경우 구성 파일 또는 파일을 만듭니다. 최소한, 구성 파일에는 파일 URI 및 스크립트 실행 명령이 포함됩니다.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

필요에 따라 명령에서 설정을 JSON 형식 문자열로 지정할 수 있습니다. 이렇게 하면 실행 중에 별도 구성 파일 없이 구성을 지정할 수 있습니다.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 예제

#### <a name="public-configuration-with-script-file"></a>스크립트 파일이 있는 공용 구성

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI 명령:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>스크립트 파일이 없는 공용 구성

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI 명령:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>공용 및 보호된 구성 파일

공용 구성 파일을 사용하여 스크립트 파일 URI를 지정합니다. 보호된 구성 파일을 사용하여 실행할 명령을 지정합니다.

공용 구성 파일:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

보호된 구성 파일:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI 명령:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure 사용자 지정 스크립트 확장은 Resource Manager 템플릿을 사용하여 가상 머신 배포 시에 실행할 수 있습니다. 이렇게 하려면 올바른 형식의 JSON을 배포 템플릿에 추가합니다.

### <a name="resource-manager-examples"></a>Resource Manager 예제

#### <a name="public-configuration"></a>공용 구성

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>보호된 구성의 실행 명령

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
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
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

전체 예제는 [.NET Music Store 데모](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결
사용자 지정 스크립트 확장이 실행되면 스크립트 생성되거나 다음 예제와 비슷한 디렉터리에 다운로드됩니다. 또한 명령 출력은 이 디렉터리의 `stdout` 및 `stderr` 파일에 저장됩니다.

```bash
/var/lib/waagent/custom-script/download/0/
```

Azure 스크립트 확장은 여기에서 찾을 수 있는 로그를 생성합니다.

```bash
/var/log/azure/custom-script/handler.log
```

Azure CLI를 사용하여 사용자 지정 스크립트 확장의 실행 상태를 검색할 수도 있습니다.

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

출력은 다음 텍스트와 비슷합니다.

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>다음 단계
다른 VM 스크립트 확장에 대한 자세한 내용은 [Linux용 Azure 스크립트 확장 개요](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

