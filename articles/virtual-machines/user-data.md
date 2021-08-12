---
title: Azure Virtual Machine에 대한 사용자 데이터
description: 고객이 프로비저닝 시 Azure Virtual Machine에 스크립트 또는 기타 메타데이터를 삽입할 수 있도록 허용합니다.
services: virtual-machines-linux
author: ningk
manager: rogardle
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/30/2021
ms.author: ningk
ms.reviewer: azmetadata
ms.openlocfilehash: d149ead5cd45b55c846852f92342e6bbc56ba3cf
ms.sourcegitcommit: 19dfdfa85e92c6a34933bdd54a7c94e8b00eacfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109665326"
---
# <a name="user-data-for-azure-virtual-machine"></a>Azure Virtual Machine에 대한 사용자 데이터 

사용자 데이터를 사용하면 사용자 고유의 스크립트 또는 메타데이터를 가상 머신에 전달할 수 있습니다.

## <a name="what-is-user-data"></a>"사용자 데이터"란?

사용자 데이터는 프로비저닝 시 Azure Virtual Machine에 삽입되는 스크립트 또는 기타 메타데이터 세트입니다. 가상 머신의 모든 애플리케이션은 프로비저닝 후 IMDS(Azure Instance Metadata Service)에서 사용자 데이터에 액세스할 수 있습니다. 

사용자 데이터는 새 버전의 [사용자 지정 데이터](./custom-data.md)이며 다음과 같은 추가적인 이점을 제공합니다.

* 프로비저닝 후 Azure IMDS(Instance Metadata Service)에서 사용자 데이터를 검색할 수 있습니다.

* 사용자 데이터는 영구적입니다. VM의 수명 동안 사용할 수 있습니다.

* VM을 중지하거나 다시 부팅하지 않고 VM 외부에서 사용자 데이터를 업데이트할 수 있습니다.

* $expand 옵션이 있는 GET VM/VMSS API를 통해 사용자 데이터를 쿼리할 수 있습니다.

 또한 프로비저닝 시 사용자 데이터가 추가되지 않은 경우 프로비저닝 후에 추가할 수 있습니다.

**보안 경고**

> [!WARNING]
> 사용자 데이터는 암호화되지 않으며 VM의 모든 프로세스에서 이 데이터를 쿼리할 수 있습니다. 사용자 데이터에 기밀 정보를 저장하면 안 됩니다.

새 사용자 데이터 기능을 사용하기 위한 최신 Azure Resource Manager API가 있는지 확인합니다. 콘텐츠는 API에 전달되기 전에 base64로 인코딩되어야 합니다. 파일 크기는 64KB를 초과할 수 없습니다.

## <a name="create-user-data-for-azure-vmvmss"></a>Azure VM/VMSS에 대한 사용자 데이터 만들기

**새 VM을 만들 때 사용자 데이터 추가**

[이 Azure Resource Manager 템플릿](https://aka.ms/ImdsUserDataArmTemplate)을 사용하여 사용자 데이터로 새 VM을 만듭니다.
Rest API를 사용하는 경우, 단일 VM의 경우 PUT 요청을 사용하여 '속성' 섹션에 'UserData'를 추가하여 VM을 만듭니다.

```json
{
  "name": "testVM",
  "location": "West US",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_A1"
    },
    "storageProfile": {
      "osDisk": {
        "osType": "Windows",
        "name": "osDisk",
        "createOption": "Attach",
        "vhd": {
          "uri": "http://myaccount.blob.core.windows.net/container/directory/blob.vhd"
        }
      }
    },
    "userData": "c2FtcGxlIHVzZXJEYXRh",
    "networkProfile": { "networkInterfaces" : [ { "name" : "nic1" } ] },
  }
}
```

**새 가상 머신 스케일링 집합을 만들 때 사용자 데이터 추가**

Rest API를 사용하면 가상 머신 스케일링 집합을 만들 때 PUT 요청을 사용하여 "virtualMachineProfile" 섹션에 'UserData'를 추가합니다.
```json
{
  "location": "West US",
  "sku": {
    "name": "Standard_A1",
    "capacity": 1
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "userData": "VXNlckRhdGE=",
      "osProfile": {
        "computerNamePrefix": "TestVM",
        "adminUsername": "TestUserName",
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "timeZone": "Dateline Standard Time"
        }
      },
      "storageProfile": {
        "osDisk": {
          "createOption": "FromImage",
          "caching": "ReadOnly"
        },
        "imageReference": {
          "publisher": "publisher",
          "offer": "offer",
          "sku": "sku",
          "version": "1.2.3"
        }
      },
      "networkProfile": {"networkInterfaceConfigurations":[{"name":"nicconfig1","properties":{"ipConfigurations":[{"name":"ip1","properties":{"subnet":{"id":"vmssSubnet0"}}}]}}]},
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "https://crputest.blob.core.windows.net"
        }
      }
    },
    "provisioningState": 0,
    "overprovision": false,
    "uniqueId": "00000000-0000-0000-0000-000000000000"
  }
}
```


## <a name="retrieving-user-data"></a>사용자 데이터 검색

VM 내에서 실행 중인 애플리케이션은 IMDS 엔드포인트에서 사용자 데이터를 검색할 수 있습니다. 자세한 내용은 여기에서 [IMDS 샘플 코드](./linux/instance-metadata-service.md?tabs=linux#get-user-data)를 참조하세요.

고객은 \$expand=userData 엔드포인트를 사용하여 Rest API를 통해 사용자 데이터의 기존 값을 검색할 수 있습니다(요청 본문은 비워 둘 수 있음).

단일 VM

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachines/{VMName}?$expand=userData"`

가상 머신 스케일링 집합

`GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}?$expand=userData"`

가상 머신 스케일링 집합 VM:

` GET "/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMSSName}/virtualmachines/{vmss instance id}?$expand=userData" `

## <a name="updating-user-data"></a>사용자 흐름 업데이트

Rest API를 사용하면 일반 PUT 또는 PATCH 요청을 사용하여 사용자 데이터를 업데이트할 수 있습니다. VM을 중지 하거나 다시 부팅할 필요 없이 사용자 데이터가 업데이트됩니다.

`PUT
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

`PATCH
"/subscriptions/{guid}/resourceGroups/{RGName}/providers/Microsoft.Compute/ virtualMachines/{VMName}
`

이러한 요청의 VM.Properties는 다음과 같이 원하는 UserData 필드를 포함해야 합니다.

```json
"properties": {
        "hardwareProfile": {
          "vmSize": "Standard_D1_v2"
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
            "name": "vmOSdisk",
            "createOption": "FromImage"
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
        },
        "osProfile": {
          "adminUsername": "{your-username}",
          "computerName": "{vm-name}",
          "adminPassword": "{your-password}"
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
            "storageUri": "http://{existing-storage-account-name}.blob.core.windows.net",
            "enabled": true
          }
        },
        "userData": "U29tZSBDdXN0b20gRGF0YQ=="
      } 
```
> [!NOTE]
> 이 경우 "userData"에 대해 빈 문자열을 전달하면 사용자 데이터가 삭제됩니다.

## <a name="user-data-and-custom-data"></a>사용자 데이터 및 사용자 지정 데이터

사용자 지정 데이터는 현재와 같은 방식으로 계속 작동합니다. IMDS에서 사용자 지정 데이터를 검색할 수 없습니다. 

## <a name="adding-user-data-to-an-existing-vm"></a>기존 VM에 사용자 데이터 추가 

사용자 데이터가 없는 기존 VM/VMSS가 있는 경우 ["사용자 데이터 업데이트"](#updating-user-data) 섹션에 설명된 대로 업데이트 명령을 사용하여 이 VM에 사용자 데이터를 추가할 수 있습니다. 최신 버전의 Azure Resource Manger API로 업그레이드해야 합니다.

## <a name="next-steps"></a>다음 단계 
 
[Azure Instance Metadata Service](./linux/instance-metadata-service.md)를 사용해 보고 엔드포인트에서 VM 인스턴스 메타데이터 및 사용자 데이터를 얻는 방법을 알아봅니다. 
