---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155467"
---
임시 OS 디스크는 로컬 가상 시스템(VM) 저장소에 만들어지며 원격 Azure 저장소에 저장되지 않습니다. 임시 OS 디스크는 응용 프로그램이 개별 VM 오류에 내성이 있지만 VM 배포 시간 이나 개별 VM 인스턴스를 다시 이미징하는 상태 비할 데 없는 워크로드에 적합합니다. 임시 OS 디스크를 사용하면 OS 디스크에 대한 읽기/쓰기 대기 시간이 낮아지고 VM 이미지가 빨라집니다. 
 
임시 디스크의 주요 기능은 다음과 같습니다. 
- 상태 비수기 애플리케이션에 이상적입니다.
- 마켓플레이스 이미지와 사용자 지정 이미지 모두에서 사용할 수 있습니다.
- VM을 빠르게 재설정하거나 다시 이미지화하고 인스턴스를 원래 부팅 상태로 확장할 수 있습니다.  
- 임시 디스크와 유사한 대기 시간이 느려집니다. 
- 임시 OS 디스크는 무료입니다, 당신은 OS 디스크에 대한 저장 비용이 발생하지 않습니다.
- 모든 Azure 지역에서 사용할 수 있습니다. 
- 임시 OS 디스크는 공유 [이미지 갤러리에서](/azure/virtual-machines/linux/shared-image-galleries)지원됩니다. 
 

 
영구 OS 디스크와 임시 OS 디스크 간의 주요 차이점:

|                             | 영구 OS 디스크                          | 사용 후 삭제 OS 디스크                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 디스크의 크기 제한      | 2TiB                                                                                        | VM 크기 또는 2TiB 중 더 작은 캐시 크기입니다. **GiB의 캐시 크기는** [DS,](../articles/virtual-machines/linux/sizes-general.md) [ES,](../articles/virtual-machines/linux/sizes-memory.md) [M,](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)및 [GS를](/azure/virtual-machines/linux/sizes-previous-gen#gs-series) 참조하십시오.              |
| 지원되는 VM 크기          | 모두                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| 디스크 유형 지원           | 관리 및 관리되지 않는 OS 디스크                                                                | 관리되는 OS 디스크만                                                               |
| 지역 지원              | 모든 지역                                                                                  | 모든 지역                              |
| 데이터 지속성            | OS 디스크에 기록된 OS 디스크 데이터는 Azure 저장소에 저장됩니다.                                  | OS 디스크에 기록된 데이터는 로컬 VM 저장소에 저장되며 Azure 저장소에 유지되지 않습니다. |
| 거래 중지 위치      | VM 및 스케일 세트 인스턴스는 정지 할당 할당 을 설정하고 중지 할당 된 상태에서 다시 시작할 수 있습니다. | VM 및 스케일 집합 인스턴스는 중지 할당 할당할 수 없습니다.                                  |
| 특수 OS 디스크 지원 | yes                                                                                          | 예                                                                                 |
| OS 디스크 크기 조정              | VM 생성 및 VM이 정지 할당 할당 된 후 지원                                | VM 생성 중에만 지원                                                  |
| 새 VM 크기로 크기 조정   | OS 디스크 데이터가 보존됩니다.                                                                    | OS 디스크의 데이터가 삭제되고 OS가 다시 프로비전됩니다.                                      |

## <a name="size-requirements"></a>크기 요구 사항

VM 캐시 크기까지 VM 및 인스턴스 이미지를 배포할 수 있습니다. 예를 들어 마켓플레이스의 표준 Windows Server 이미지는 약 127GiB이며, 이는 127 GiB보다 큰 캐시가 있는 VM 크기가 필요하다는 것을 의미합니다. 이 경우 [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) 캐시 크기가 86 GiB이며 충분히 크지 않습니다. Standard_DS3_v2 캐시 크기가 172 GiB이며 충분히 큽니까. 이 경우 Standard_DS3_v2 이 이미지와 함께 사용할 수 있는 DSv2 계열에서 가장 작은 크기입니다. 마켓플레이스 및 Windows Server 이미지의 기본 Linux `[smallsize]` 이미지는 약 30GiB로 표시되며 사용 가능한 VM 크기의 대부분을 사용할 수 있습니다.

또한 임시 디스크에는 VM 크기가 프리미엄 저장소를 지원해야 합니다. 크기는 일반적으로 (항상은 아니지만) `s` DSv2 및 EsV3와 같은 이름에 있습니다. 자세한 내용은 프리미엄 저장소를 지원하는 크기에 대한 자세한 내용은 [Azure VM 크기를](../articles/virtual-machines/linux/sizes.md) 참조하십시오.

## <a name="powershell"></a>PowerShell

PowerShell VM 배포에 임시 디스크를 사용하려면 VM 구성에서 [Set-AzVMOSDisk를](/powershell/module/az.compute/set-azvmosdisk) 사용합니다. `-DiffDiskSetting` 을 `Local` 에 `-Caching` 및 `ReadOnly`로 설정합니다.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

스케일 세트 배포의 경우 구성에서 [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet을 사용합니다. `-DiffDiskSetting` 을 `Local` 에 `-Caching` 및 `ReadOnly`로 설정합니다.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLI VM 배포에 임시 디스크를 사용하려면 az `--ephemeral-os-disk` [vm에서](/cli/azure/vm#az-vm-create) `true` 매개 변수를 `--os-disk-caching` 로 `ReadOnly`설정하고 매개 변수를 로 설정합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

축척 집합의 경우 `--ephemeral-os-disk true` [az-vmss-create에](/cli/azure/vmss#az-vmss-create) 대해 동일한 `--os-disk-caching` 매개변수를 사용하고 매개변수를 로 설정합니다. `ReadOnly`

## <a name="portal"></a>포털   

Azure 포털에서 디스크 탭의 **고급** 섹션을 열어 VM을 배포할 때 임시 디스크를 사용하도록 선택할 수 **있습니다.** 사용 임시 **OS 디스크를** **선택합니다 .**

![임시 OS 디스크를 사용하도록 선택하기 위한 라디오 버튼을 보여주는 스크린샷](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

임시 디스크를 사용하는 옵션이 회색으로 되어 있는 경우 OS 이미지보다 큰 캐시 크기가 없거나 Premium 저장소를 지원하지 않는 VM 크기를 선택했을 수 있습니다. **기본** 페이지로 돌아가서 다른 VM 크기를 선택해 보십시오.

포털을 사용하여 임시 OS 디스크를 사용하여 배율 집합을 만들 수도 있습니다. 캐시 크기가 충분히 큰 VM 크기를 선택한 다음 **임시 OS 디스크 사용에서** **예**를 선택하십시오.

![스케일 세트에 임시 OS 디스크를 사용하도록 선택하기 위한 라디오 버튼을 보여주는 스크린샷](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>확장 설정 템플릿 배포  
임시 OS 디스크를 사용하는 축척 집합을 만드는 프로세스는 `diffDiskSettings` 템플릿의 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 리소스 유형에 속성을 추가하는 것입니다. 또한 임시 OS 디스크에 `ReadOnly` 대해 캐싱 정책을 설정해야 합니다. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM 템플릿 배포 
템플릿을 사용하여 임시 OS 디스크를 사용하여 VM을 배포할 수 있습니다. 임시 OS 디스크를 사용하는 VM을 만드는 프로세스는 템플릿의 `diffDiskSettings` Microsoft.Compute/virtualMachine 리소스 유형에 속성을 추가하는 것입니다. 또한 임시 OS 디스크에 `ReadOnly` 대해 캐싱 정책을 설정해야 합니다. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>REST를 사용하여 VM 이미지 다시 이미지
아래에 설명된 대로 REST API를 사용하고 Azure Portal을 통해 VM의 개요 창으로 이동하여 임시 OS 디스크로 가상 컴퓨터 인스턴스를 다시 이미지화할 수 있습니다. 스케일 집합의 경우 Powershell, CLI 및 포털을 통해 이미 다시 이미징을 사용할 수 있습니다.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 로컬 OS 디스크의 크기는 무엇입니까?**

A: 우리는 플랫폼 및 사용자 지정 이미지를 지원하며, VM 캐시 크기까지 OS 디스크에 대한 모든 읽기/쓰기는 가상 시스템과 동일한 노드에서 로컬로 유지됩니다. 

**Q: 임시 OS 디스크의 크기를 조정할 수 있습니까?**

A: 아니요, 임시 OS 디스크를 프로비전한 후에는 OS 디스크의 크기를 조정할 수 없습니다. 

**Q: 관리되는 디스크를 임시 VM에 연결할 수 있습니까?**

A: 예, 임시 OS 디스크를 사용하는 VM에 관리되는 데이터 디스크를 연결할 수 있습니다. 

**Q: 임시 OS 디스크에 대해 모든 VM 크기가 지원되나요?**

A: 아니요, B 시리즈, N 시리즈 및 H 시리즈 크기를 제외한 모든 프리미엄 스토리지 VM 크기(DS, ES, FS, GS 및 M)가 지원됩니다.  
 
**Q: 임시 OS 디스크를 기존 VM 및 스케일 세트에 적용할 수 있습니까?**

A: 아니요, 임시 OS 디스크는 VM 및 스케일 세트 생성 중에만 사용할 수 있습니다. 

**Q: 스케일 세트에서 임시 및 일반 OS 디스크를 혼합할 수 있습니까?**

A: 아니요, 동일한 규모 집합 내에서 임시 및 영구 OS 디스크 인스턴스를 혼합할 수 없습니다. 

**Q: Powershell 또는 CLI를 사용하여 임시 OS 디스크를 만들 수 있습니까?**

A: 예, REST, 템플릿, PowerShell 및 CLI를 사용하여 임시 OS 디스크로 VM을 만들 수 있습니다.

**Q: 임시 OS 디스크에서는 어떤 기능이 지원되지 않습니까?**

A: 임시 디스크는 다음을 지원하지 않습니다.
- VM 이미지 캡처
- 디스크 스냅샷 
- Azure 디스크 암호화 
- Azure Backup
- Azure Site Recovery  
- OS 디스크 스왑 
