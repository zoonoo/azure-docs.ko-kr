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
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c2d14be5f27a775a14039bd63c5ccb5cd7b10f9a
ms.lasthandoff: 04/26/2017


---
# <a name="oms-virtual-machine-extension-for-linux"></a>Linux용 OMS 가상 컴퓨터 확장

## <a name="overview"></a>개요

OMS(Operations Management Suite)는 클라우드와 온-프레미스 자산에서 모니터링, 경고 및 경고 수정 기능을 제공합니다. Linux용 OMS 에이전트 가상 컴퓨터 확장은 Microsoft에서 게시 및 지원합니다. 확장 버전은 Azure Virtual Machines에 OMS 에이전트를 설치하고 기존 OMS 작업 영역에 가상 컴퓨터를 등록합니다. 이 문서에서는 지원되는 플랫폼, 구성 및 Linux용 OMS 가상 컴퓨터 확장에 대한 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

OM 에이전트 확장은 다음 Linux 배포판에 대해 실행할 수 있습니다.

| 배포 | 버전 |
|---|---|
| CentOS Linux | 5, 6 및 7 |
| Oracle Linux | 5, 6 및 7 |
| Red Hat Enterprise Linux Server | 5, 6 및 7 |
| Debian GNU/Linux | 6, 7 및 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 및 12 |

### <a name="internet-connectivity"></a>인터넷 연결

Linux용 OMS 에이전트 확장은 대상 가상 컴퓨터가 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 OMS 에이전트 확장에 대한 스키마를 보여줍니다. 이 확장은 대상 OMS 작업 영역에서 작업 영역 ID와 작업 영역 키가 필요하며, 이러한 값은 OMS 포털에서 확인할 수 있습니다. 작업 영역 키는 중요한 데이터로 처리되므로 보호되는 설정에 저장됩니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 컴퓨터에서만 해독됩니다. **workspaceId** 및 **workspaceKey**는 대/소문자를 구분합니다.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.3 |
| workspaceId(예) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey(예) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 템플릿은 OMS에 온보딩과 같이 배포 후 구성이 필요한 하나 이상의 Virtual Machines를 배포하는 경우에 이상적입니다. OMS 에이전트 VM 확장을 포함하는 샘플 Resource Manager 템플릿은 [Azure 빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)에서 찾을 수 있습니다. 

가상 컴퓨터 확장에 대한 JSON은 가상 컴퓨터 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/resource-manager-template-child-resource.md)을 참조하세요. 

다음 예제에서는 OMS 확장이 가상 컴퓨터 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 컴퓨터의 `"resources": []` 개체에 배치됩니다.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

템플릿의 루트에 JSON 확장을 배치하면 리소스 이름에 부모 가상 컴퓨터에 대한 참조가 포함되며 형식은 중첩된 구성을 반영합니다.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.3",
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
azure vm extension set myResourceGroup myVM \
  OmsAgentForLinux Microsoft.EnterpriseCloud.Monitoring 1.3 \
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

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>오류 코드 및 해당 의미

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 2 | 셸 번들에 잘못된 옵션이 제공됨 | |
| 3 | 셸 번들에 옵션이 제공되지 않음 | |
| 4 | 잘못된 패키지 형식 | |
| 5 | 셸 번들을 루트로 실행해야 함 | |
| 6 | 잘못된 패키지 아키텍처 | |
| 10 | VM이 OMS 작업 영역에 이미 연결됨 | VM을 확장 스키마에 지정된 작업 영역에 연결하려면 공용 설정에서 stopOnMultipleConnections를 false로 설정하거나 이 속성을 제거합니다. 이 VM은 연결된 각 작업 영역에 대해 한 번만 비용이 청구됩니다. |
| 11 | 확장에 잘못된 구성이 제공됨 | 이전 예제에 따라 배포에 필요한 모든 속성 값을 설정합니다. |
| 20 | SCX/OMI 설치 실패 | |
| 21 | SCX/공급자 키트 설치 실패 | |
| 22 | 번들 패키지 설치 실패 | |
| 23 | SCX 또는 OMI 패키지가 이미 설치됨 | |
| 30 | 내부 번들 오류 | |
| 51 | 이 확장이 VM의 운영 체제에서 지원되지 않음 | |
| 60 | 지원되지 않는 OpenSSL 버전 | [패키지 요구 사항](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#package-requirements)을 충족하는 OpenSSL 버전을 설치하세요. |
| 61 | Python ctypes 라이브러리 누락 | Python ctypes 라이브러리 또는 패키지(python-ctypes)를 설치합니다. |
| 62 | tar 프로그램 누락 | tar를 설치합니다. |
| 63 | sed 프로그램 누락 | sed를 설치합니다. |

추가 문제 해결 정보는 [MS-Agent-for-Linux 문제 해결 가이드](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#)에서 확인할 수 있습니다.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/en-us/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/en-us/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/en-us/support/faq/)를 참조하세요.

