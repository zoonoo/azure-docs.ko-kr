---
title: 분리 된 디스크로 인 한 가상 컴퓨터 배포 문제 해결 | Microsoft Docs
description: 분리 된 디스크로 인 한 가상 컴퓨터 배포 문제 해결
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75486821"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>분리 된 디스크로 인 한 가상 컴퓨터 배포 문제 해결

## <a name="symptom"></a>증상

이전 데이터 디스크 분리에 실패 한 가상 컴퓨터를 업데이트 하려고 할 때이 오류 코드가 나타날 수 있습니다.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>원인

이 오류는 마지막 분리 작업이 실패 한 데이터 디스크를 다시 사용할 때 발생 합니다. 이 상태를 확인 하는 가장 좋은 방법은 오류가 발생 한 디스크를 분리 하는 것입니다.

## <a name="solution-1-powershell"></a>해결 방법 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>1 단계: 가상 컴퓨터 및 디스크 세부 정보 가져오기

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

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2 단계: 실패 한 디스크에 대 한 플래그를 "true"로 설정 합니다.

오류가 발생 한 디스크의 배열 인덱스를 가져오고 실패 한 디스크 ( **AttachDiskWhileBeingDetached** 오류가 발생 한 경우)에 대 한 **tobedetached** 플래그를 "true"로 설정 합니다. 이 설정은 가상 머신에서 디스크를 분리 하는 것을 의미 합니다. 오류가 발생 한 디스크 이름은 **errorMessage**에서 찾을 수 있습니다.

> ! 참고: Get 및 Put 호출에 지정 된 API 버전은 2019-03-01 이상 이어야 합니다.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

또는 아래 명령을 사용 하 여이 디스크를 분리할 수도 있습니다 .이는 2019 년 3 월 01 일 이전에 API 버전을 사용 하는 사용자에 게 유용 합니다.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>3 단계: 가상 컴퓨터 업데이트

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>해결 방법 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>1 단계: 가상 컴퓨터 페이로드를 가져옵니다.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>2 단계: 실패 한 디스크에 대 한 플래그를 "true"로 설정 합니다.

1 단계에서 반환 된 페이로드에서 실패 한 디스크에 대 한 **Tobedetached** 플래그를 true로 설정 합니다. 참고: Get 및 Put 호출에 지정 된 API 버전은 `2019-03-01` 이상 이어야 합니다.

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

또는 위의 페이로드에서 실패 한 데이터 디스크를 제거할 수도 있습니다 .이는 2019 년 3 월 01 일 이전에 API 버전을 사용 하는 사용자에 게 유용 합니다.

### <a name="step-3-update-the-virtual-machine"></a>3 단계: 가상 컴퓨터 업데이트

2 단계에서 설정 된 요청 본문 페이로드를 사용 하 고 다음과 같이 가상 머신을 업데이트 합니다.

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
