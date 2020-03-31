---
title: 분리된 디스크로 인한 가상 시스템 배포 문제 해결 | 마이크로 소프트 문서
description: 분리된 디스크로 인한 가상 시스템 배포 문제 해결
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486821"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>분리된 디스크로 인한 가상 시스템 배포 문제 해결

## <a name="symptom"></a>증상

이전 데이터 디스크 분리에 실패한 가상 컴퓨터를 업데이트하려고 할 때 이 오류 코드가 발생할 수 있습니다.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>원인

이 오류는 마지막 분리 작업이 실패한 데이터 디스크를 다시 연결하려고 할 때 발생합니다. 이 상태에서 벗어나는 가장 좋은 방법은 실패한 디스크를 분리하는 것입니다.

## <a name="solution-1-powershell"></a>솔루션 1: 파워쉘

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>1단계: 가상 컴퓨터 및 디스크 세부 정보 가져오기

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2단계: 실패한 디스크에 대한 플래그를 "true"로 설정합니다.

실패한 디스크의 배열 인덱스를 얻고 실패한 디스크에 대한 **beBedetached** 플래그를 **"True"로** 설정합니다. 이 설정은 가상 컴퓨터에서 디스크를 분리하는 것을 의미합니다. 실패한 디스크 이름은 **errorMessage**.

> ! 참고: Get 및 Put 호출에 대해 지정된 API 버전은 2019-03-01 이상이어야 합니다.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

또는 아래 명령을 사용하여 이 디스크를 분리할 수도 있으며, 이는 2019년 3월 01일 이전에 API 버전을 사용하는 사용자에게 도움이 될 것입니다.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>3단계: 가상 컴퓨터 업데이트

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>솔루션 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>1단계: 가상 시스템 페이로드를 가져옵니다.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2단계: 실패한 디스크에 대한 플래그를 "true"로 설정합니다.

1단계에서 반환된 페이로드에서 실패한 디스크에 대한 **toBeDetached** 플래그를 true로 설정합니다. 참고: Get 및 Put 호출에 대해 지정된 `2019-03-01` API 버전은 더 이상이어야 합니다.

**샘플 요청 본문**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
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
  }
}
```

또는 위의 페이로드에서 실패한 데이터 디스크를 제거할 수도 있으므로 2019년 3월 01일 이전에 API 버전을 사용하는 사용자에게 유용합니다.

### <a name="step-3-update-the-virtual-machine"></a>3단계: 가상 컴퓨터 업데이트

2단계에서 설정된 요청 본문 페이로드를 사용하고 다음과 같이 가상 컴퓨터를 업데이트합니다.

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**샘플 응답:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>다음 단계

VM에 연결하는 데 문제가 있는 경우 [Azure VM에 RDP 연결 문제 해결](troubleshoot-rdp-connection.md)을 참조하세요.

VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Windows VM에서 애플리케이션 연결 문제 해결](troubleshoot-app-connection.md)을 참조하세요.
