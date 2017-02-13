---
title: "Linux용 OMS Azure Virtual Machine 확장 | Microsoft Docs"
description: "가상 컴퓨터 확장을 사용하여 Linux 가상 컴퓨터에 OMS 에이전트를 배포합니다."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 9f27890e52cb7a9a0d46f2bb84bfe92f7c6fff37
ms.openlocfilehash: 9864314956d79317785c1c2a4bc87621bc6a3e2d


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Linux용 OMS 가상 컴퓨터 확장

## <a name="overview"></a>개요

OMS(Operations Management Suite)는 클라우드와 온-프레미스 자산에서 모니터링, 경고 및 경고 수정 기능을 제공합니다. Linux용 OMS 에이전트 가상 컴퓨터 확장은 Microsoft에서 게시 및 지원합니다. 확장 버전은 Azure Virtual Machines에 OMS 에이전트를 설치하고 기존 OMS 작업 영역에 가상 컴퓨터를 등록합니다. 이 문서에서는 지원되는 플랫폼, 구성 및 Linux용 OMS 가상 컴퓨터 확장에 대한 배포 옵션을 설명합니다.

Azure Virtual Machine 확장에 대한 일반적인 내용은 [가상 컴퓨터 확장 개요](./virtual-machines-linux-extensions-features.md)를 참조하세요.

Operations Management Suite에 대한 자세한 내용은 [Operations Management Suite 개요](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

OM 에이전트 확장은 다음 Linux 배포판에 대해 실행할 수 있습니다.

| 배포 | 버전 |
|---|---|
| CentOS Linux | 5,6 및 7 |
| Oracle Linux | 5,6 및 7 |
| Red Hat Enterprise Linux Server | 5,6 및 7 |
| Debian GNU/Linux | 6, 7 및 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04 |
| SUSE Linux Enterprise Server | 11 및 12 |

### <a name="connectivity"></a>연결

Linux용 OMS 에이전트 확장은 대상 가상 컴퓨터가 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-configuration"></a>확장 구성

Linux용 OMS 에이전트가 가상 컴퓨터 확장에는 대상 OMS 작업 영역에서 작업 영역 ID와 작업 영역 키가 필요합니다. 작업 영역 키는 중요한 데이터로 처리되므로 보호되는 구성에 저장됩니다. Azure VM 확장으로 보호되는 구성 데이터는 암호화되어 대상 가상 컴퓨터에서만 해독됩니다. 공용 및 개인 구성은 배포 시점에 지정되며 이 문서의 후속 섹션에서 자세히 설명합니다.

### <a name="public-configuration"></a>공용 구성

공용 구성의 스키마:

- workspaceId: (필수, 문자열) 가상 컴퓨터를 등록할 OMS 작업 영역 id입니다.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>개인 구성

공용 구성의 스키마:

- workspaceKey: (필수, 문자열) 작업 영역의 주/보조 공유 키입니다.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 템플릿은 OMS에 등록과 같이 배포 후 구성이 필요한 하나 이상의 가상 컴퓨터를 배포하는 경우에 이상적입니다. OMS 에이전트 VM 확장을 포함하는 샘플 Resource Manager 템플릿은 [Azure 빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)에서 찾을 수 있습니다. 

이 샘플은 이 단추를 사용하여 이 문서에서 배포할 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-ubuntu-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

OMS 에이전트 VM 확장을 배포하는 데 사용되는 JSON은 다음 JSON 예제와 같습니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<extension-deployment-name>",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 OMS 에이전트 VM 확장을 기존 가상 컴퓨터에 배포할 수 있습니다. OM 에이전트 확장을 배포하기 전에 public.json 및 protected.json 파일을 만듭니다. 이러한 파일에 대한 스키마는 이 문서의 앞부분에 자세히 설명되어 있습니다.

```azurecli
azure vm extension set <resource-group> <vm-name> \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.0 \
  --public-config-path public.json  \
  --private-config-path protected.json
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
azure vm extension get myResourceGroup myVM
```

확장 실행 출력은 다음 파일에 기록됩니다.

`
/opt/microsoft/omsagent/bin/stdout
`

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/en-us/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/en-us/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/en-us/support/faq/)를 참조하세요.



<!--HONumber=Dec16_HO1-->


