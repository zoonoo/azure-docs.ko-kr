---
title: 사용자 지정 데이터 및 Azure Virtual Machines
description: Azure Virtual Machines에서 사용자 지정 데이터 및 클라우드 초기화 사용에 대 한 세부 정보
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759126"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure Virtual Machines의 사용자 지정 데이터 및 클라우드 초기화

## <a name="what-is-custom-data"></a>사용자 지정 데이터 란?

고객은 프로 비전 시에 사용자가 스크립트 또는 다른 메타 데이터를 Microsoft Azure 가상 컴퓨터에 삽입할 수 있는 방법을 묻는 경우가 종종 있습니다.  다른 클라우드에서이 개념을 종종 사용자 데이터 라고 합니다.  Microsoft Azure에는 사용자 지정 데이터 라는 유사한 기능이 있습니다. 

사용자 지정 데이터는 처음 부팅/초기 설치 중에만 VM에 사용할 수 있습니다 .이 ' 프로 비전 ' 이라고 합니다. 프로 비전은 vm 만들기 매개 변수 (예: 호스트 이름, 사용자 이름, 암호, 인증서, 사용자 지정 데이터, 키 등)를 VM에서 사용할 수 있도록 하 고 프로 비전 에이전트가 [Linux 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 및 [클라우드 초기화](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)와 같은 해당 매개 변수를 처리 하는 프로세스입니다. 


## <a name="passing-custom-data-to-the-vm"></a>VM에 사용자 지정 데이터 전달
사용자 지정 데이터를 사용 하려면 AZ CLI와 같이 사용자에 대 한 변환을 수행 하는 CLI 도구를 사용 하지 않는 한, 해당 콘텐츠를 API에 전달 하기 전에 먼저 base64로 인코딩해야 합니다. 크기는 64 KB를 초과할 수 없습니다.

CLI에서 사용자 지정 데이터를 파일로 전달할 수 있으며이는 base64로 변환 됩니다.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

ARM (Azure Resource Manager)에는 [base64 함수가](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)있습니다.

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
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>사용자 지정 데이터 처리
Vm에 설치 된 프로 비전 에이전트는 플랫폼과 상호 작용 하 여 파일 시스템에 배치 합니다. 

### <a name="windows"></a>Windows
사용자 지정 데이터는 *%SYSTEMDRIVE%\AzureData\CustomData.bin* 에 이진 파일로 배치 되지만 처리 되지 않습니다. 이 파일을 처리 하려는 경우에는 사용자 지정 이미지를 빌드하고 CustomData를 처리 하는 코드를 작성 해야 합니다.

### <a name="linux"></a>Linux  
Linux OS의 경우 사용자 지정 데이터가 ovf-env 파일을 통해 VM에 전달 됩니다 .이 파일은 프로 비전 하는 동안 */dev/sv/waagent* 디렉터리에 복사 됩니다.  최신 버전의 Microsoft Azure Linux 에이전트는 편의상 base64 인코딩 데이터를 */var/lib/waagent/CustomData* 에도 복사 합니다.

Azure는 현재 두 가지 프로 비전 에이전트를 지원 합니다.
* Linux 에이전트-기본적으로 에이전트는 사용자 지정 데이터를 처리 하지 않습니다 .이를 사용 하도록 설정 된 사용자 지정 이미지를 만들어야 합니다. 관련 설정은 [설명서](https://github.com/Azure/WALinuxAgent#configuration) 에 따라 다음과 같습니다.
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

사용자 지정 데이터를 사용 하도록 설정 하 고 스크립트를 실행 하는 경우 스크립트가 완료 될 때까지 VM 보고를 지연 시키거나 프로 비전이 성공 했음을 지연 합니다. 스크립트가 총 VM 프로 비전 시간 허용치를 40 분을 초과 하면 VM 만들기가 실패 합니다. 스크립트를 실행 하는 데 실패 하거나 실행 하는 동안 오류가 발생 하는 경우에는 심각한 프로 비전 실패로 간주 되지 않습니다. 또한 알림 경로를 만들어 스크립트의 완료 상태를 확인 해야 합니다.

사용자 지정 데이터 실행 문제를 해결 하려면 */var/log/waagent.log* 를 검토 하십시오.

* 기본적으로 클라우드 초기화-기본적으로 사용자 지정 데이터를 처리 합니다. 클라우드 초기화는 클라우드 초기화 구성, 스크립트 등의 [여러 가지](https://cloudinit.readthedocs.io/en/latest/topics/format.html) 사용자 지정 데이터 형식을 허용 합니다. 클라우드 에이전트와 마찬가지로, 클라우드 초기화에서 사용자 지정 데이터를 처리 합니다. 구성 처리 또는 스크립트를 실행 하는 동안 오류가 발생 하는 경우에는 심각한 프로 비전 실패로 간주 되지 않으므로 스크립트의 완료 상태에 대 한 경고를 표시 하는 알림 경로를 만들어야 합니다. 그러나 Linux 에이전트와는 달리, 클라우드 초기화는 VM이 준비 된 플랫폼에 보고 하기 전에 사용자 지정 데이터 구성이 완료 될 때까지 기다리지 않습니다. Azure의 클라우드 초기화에 대 한 자세한 내용은 [설명서](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)를 참조 하세요.


사용자 지정 데이터 실행 문제를 해결 하려면 문제 해결 [설명서](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)를 검토 하세요.


## <a name="faq"></a>FAQ
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>VM을 만든 후 사용자 지정 데이터를 업데이트할 수 있나요?
단일 Vm의 경우 VM 모델의 사용자 지정 데이터를 업데이트할 수 없지만 VMSS의 경우 REST API를 통해 VMSS 사용자 지정 데이터를 업데이트할 수 있습니다 (PS 또는 AZ CLI 클라이언트에는 적용 되지 않음). VMSS 모델에서 사용자 지정 데이터를 업데이트 하는 경우:
* VMSS의 기존 인스턴스는 이미지로 다시 설치 될 때 까지만 업데이트 된 사용자 지정 데이터를 가져오지 않습니다.
* 업그레이드 된 VMSS의 기존 인스턴스는 업데이트 된 사용자 지정 데이터를 가져오지 않습니다.
* 새 인스턴스는 새 사용자 지정 데이터를 받게 됩니다.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>중요 한 값을 사용자 지정 데이터에 저장할 수 있나요?
중요 한 데이터를 사용자 지정 데이터에 저장 **하지 않는** 것이 좋습니다. 자세한 내용은 [Azure 보안 및 암호화 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)를 참조 하세요.


### <a name="is-custom-data-made-available-in-imds"></a>사용자 지정 데이터를 IMDS에서 사용할 수 있나요?
아니요,이 기능은 현재 사용할 수 없습니다.
