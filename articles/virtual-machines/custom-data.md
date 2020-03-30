---
title: 사용자 지정 데이터 및 Azure 가상 시스템
description: Azure 가상 컴퓨터에서 사용자 지정 데이터 및 클라우드 Init 사용에 대한 세부 정보
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109629"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 가상 머신에서 사용자 지정 데이터 및 클라우드 Init

## <a name="what-is-custom-data"></a>사용자 지정 데이터란?

고객은 종종 프로비저닝 시 Microsoft Azure 가상 컴퓨터에 스크립트 또는 기타 메타데이터를 삽입하는 방법을 묻습니다.  다른 클라우드에서는 이 개념을 사용자 데이터라고도 합니다.  Microsoft Azure에는 사용자 지정 데이터라는 유사한 기능이 있습니다. 

사용자 지정 데이터는 첫 번째 부팅/초기 설정 중에만 VM에서 사용할 수 있으며 이를 '프로비저닝'이라고 합니다. 프로비저닝은 VM Create 매개 변수(예: 호스트 이름, 사용자 이름, 암호, 인증서, 사용자 지정 데이터, 키 등)를 VM에서 사용할 수 있게 되고 프로비저닝 에이전트가 [Linux 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 및 [클라우드 init과](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)같은 매개 변수를 처리하는 프로세스입니다. 


## <a name="passing-custom-data-to-the-vm"></a>사용자 지정 데이터를 VM에 전달
사용자 지정 데이터를 사용하려면 AZ CLI와 같이 변환을 수행하는 CLI 도구를 사용하지 않는 한 API에 전달하기 전에 먼저 내용을 인코딩해야 합니다. 크기는 64KB를 초과할 수 없습니다.

CLI에서는 사용자 지정 데이터를 파일로 전달할 수 있으며 base64로 변환됩니다.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

AZURE 리소스 관리자(ARM)에는 [base64 함수가](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)있습니다.

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>사용자 지정 데이터 처리
VM에 설치된 프로비저닝 에이전트는 플랫폼과의 인터페이싱을 처리하고 파일 시스템에 배치합니다. 

### <a name="windows"></a>Windows
사용자 지정 데이터는 이진 파일로 *%SYSTEMDRIVE%\AzureData\CustomData.bin에* 배치되지만 처리되지 는 않습니다. 이 파일을 처리하려면 사용자 지정 이미지를 빌드하고 CustomData.bin을 처리하기 위해 코드를 작성해야 합니다.

### <a name="linux"></a>Linux  
Linux OS에서는 사용자 지정 데이터가 ovf-env.xml 파일을 통해 VM에 전달되며, 이 파일은 프로비저닝 중에 */var/lib/waagent* 디렉토리에 복사됩니다.  Microsoft Azure Linux 에이전트의 최신 버전도 편의를 위해 base64 인코딩된 데이터를 */var/lib/waagent/CustomData에* 복사합니다.

Azure는 현재 두 가지 프로비저닝 에이전트를 지원합니다.
* Linux 에이전트 - 기본적으로 에이전트는 사용자 지정 데이터를 처리하지 않으며 사용하도록 설정된 사용자 지정 이미지를 빌드해야 합니다. [설명서에](https://github.com/Azure/WALinuxAgent#configuration) 따라 관련 설정은 다음과 같습니다.
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

사용자 지정 데이터를 사용하도록 설정하고 스크립트를 실행하면 스크립트가 완료될 때까지 준비중이거나 프로비저닝이 성공한 VM 보고가 지연됩니다. 스크립트가 총 VM 프로비저닝 시간 허용량을 40분 초과하면 VM 만들기가 실패합니다. 스크립트가 실행되지 않거나 실행 중에 오류가 치명적인 프로비저닝 실패로 간주되지 않는 경우 스크립트의 완료 상태를 알리는 알림 경로를 만들어야 합니다.

사용자 지정 데이터 실행 문제를 해결하려면 */var/log/waagent.log를 검토합니다.*

* 클라우드-init - 기본적으로 사용자 지정 데이터를 기본적으로 처리하며, 클라우드 init은 클라우드 init 구성, 스크립트 등과 같은 [여러 형식의](https://cloudinit.readthedocs.io/en/latest/topics/format.html) 사용자 지정 데이터를 허용합니다. Linux 에이전트와 마찬가지로 클라우드 init이 사용자 지정 데이터를 처리합니다. 구성 처리 또는 스크립트를 실행하는 동안 오류가 있는 경우 치명적인 프로비저닝 실패로 간주되지 않으며 스크립트의 완료 상태를 알리는 알림 경로를 만들어야 합니다. 그러나 Linux 에이전트와 달리 클라우드 init은 VM이 준비된 플랫폼에 보고하기 전에 사용자 사용자 지정 데이터 구성이 완료될 때까지 기다리지 않습니다. Azure의 클라우드 init에 대한 자세한 내용은 [설명서를](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)참조하십시오.


사용자 지정 데이터 실행 문제를 해결하려면 문제 해결 [설명서를](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)검토합니다.


## <a name="faq"></a>FAQ
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>VM을 만든 후 사용자 지정 데이터를 업데이트할 수 있습니까?
단일 VM의 경우 VM 모델의 사용자 지정 데이터를 업데이트할 수 없지만 VMSS의 경우 REST API를 통해 VMSS 사용자 지정 데이터를 업데이트할 수 있습니다(PS 또는 AZ CLI 클라이언트에는 적용되지 않음). VMSS 모델에서 사용자 지정 데이터를 업데이트할 때:
* VMSS의 기존 인스턴스는 업데이트된 사용자 지정 데이터를 받지 못하며 이미지가 다시 지정될 때까지만 가져옵니다.
* 업그레이드된 VMSS의 기존 인스턴스는 업데이트된 사용자 지정 데이터를 얻지 않습니다.
* 새 인스턴스는 새 사용자 지정 데이터를 수신합니다.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>사용자 지정 데이터에 중요한 값을 배치할 수 있습니까?
중요한 데이터는 사용자 지정 데이터에 **저장하지 않는** 것이 좋습니다. 자세한 내용은 [Azure 보안 및 암호화 모범 사례를](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)참조하십시오.


### <a name="is-custom-data-made-available-in-imds"></a>사용자 지정 데이터는 IMDS에서 사용할 수 있습니까?
아니요, 이 기능은 현재 사용할 수 없습니다.
