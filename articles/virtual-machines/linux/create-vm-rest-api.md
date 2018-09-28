---
title: Azure REST API를 통해 Linux 가상 머신 만들기 | Microsoft Docs
description: Azure에서 Azure REST API를 통해 관리 디스크 및 SSH 인증을 사용하는 Linux 가상 머신을 만드는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 11d9f5efb452d46e5ca30169861582f6f2bbbd1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969396"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>REST API를 통해 SSH 인증을 사용하는 Linux 가상 머신 만들기

Azure에서 VM(가상 머신)은 위치, 하드웨어 크기, 운영 체제 이미지 및 로그온 자격 증명과 같은 다양한 매개 변수에 의해 정의됩니다. 이 문서에서는 REST API를 통해 SSH 인증을 사용하는 Linux 가상 머신을 만드는 방법을 보여 줍니다.

가상 머신을 만들거나 업데이트하려면 다음 *PUT* 작업을 사용합니다.

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>요청 만들기

*PUT* 요청을 만들려면 `{subscription-id}` 매개 변수는 필수입니다. 구독이 여러 개인 경우 [여러 구독으로 작업](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)을 참조합니다. 리소스에 대해 `{resourceGroupName}` 및 `{vmName}`과 함께 `api-version` 매개 변수를 정의합니다. 이 문서에서는 `api-version=2017-12-01`을 사용합니다.

다음과 같은 헤더가 필요합니다.

| 요청 헤더   | 설명 |
|------------------|-----------------|
| *Content-Type:*  | 필수 사항입니다. `application/json`로 설정합니다. |
| *권한 부여* | 필수 사항입니다. 유효한 `Bearer` [액세스 토큰](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |

요청을 만드는 방법에 대한 자세한 내용은 [REST API 요청/응답의 구성 요소](/rest/api/azure/#components-of-a-rest-api-requestresponse)를 참조하세요.

## <a name="create-the-request-body"></a>요청 본문 만들기

다음과 같은 일반적인 정의가 요청 본문을 빌드하는 데 사용됩니다.

| 이름                       | 필수 | 형식                                                                                | 설명  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | string                                                                              | 리소스 위치. |
| 이름                       |          | string                                                                              | 가상 머신의 이름. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 가상 머신에 대한 하드웨어 설정을 지정합니다. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 가상 머신 디스크에 대한 저장소 설정을 지정합니다. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 가상 머신에 대한 운영 체제 설정을 지정합니다. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 가상 머신의 네트워크 인터페이스를 지정합니다. |

요청 본문에 사용할 수 있는 정의의 전체 목록은 [가상 머신 요청 본문 정의 만들기 또는 업데이트](/rest/api/compute/virtualmachines/createorupdate#definitions)를 참조하세요.

### <a name="example-request-body"></a>요청 본문 예제

다음 요청 본문 예제는 프리미엄 관리 디스크를 사용하는 Ubuntu 18.04-LTS 이미지를 정의합니다. SSH 공개 키 인증이 사용되며, VM은 사용자가 [이전에 만든](../../virtual-network/virtual-network-network-interface.md) 기존 가상 NIC(네트워크 인터페이스 카드)를 사용합니다. *osProfile.linuxConfiguration.ssh.publicKeys.keyData* 필드에 SSH 공개 키를 입력합니다. 필요한 경우 [SSH 키 쌍을 생성](mac-create-ssh-keys.md)할 수 있습니다.

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>응답

가상 머신 만들기 또는 업데이트하는 작업에 대한 성공적인 응답에는 두 가지가 있습니다.

| 이름        | type                                                                              | 설명 |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 정상      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 확인          |
| 201 생성됨 | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 생성일     |

REST API 응답에 대한 자세한 내용은 [응답 메시지 처리](/rest/api/azure/#process-the-response-message)를 참조하세요.

### <a name="example-response"></a>예제 응답

VM을 만든 이전 요청 본문 예제에서 압축된 *201 생성됨* 응답은 *vmId*가 할당되었으며 *provisioningState*가 *만들어지고 있음*을 보여 줍니다.

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>다음 단계

Azure REST API 또는 Azure CLI, Azure PowerShell 등의 다른 관리 도구에 대한 자세한 내용은 다음 항목을 참조하세요.

- [Azure Compute 공급자 REST API](/rest/api/compute/)
- [Azure REST API 시작하기](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell 모듈](/powershell/azure/overview)
