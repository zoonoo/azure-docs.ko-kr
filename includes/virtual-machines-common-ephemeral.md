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
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805888"
---
사용 후 삭제 OS 디스크 로컬 가상 머신 (VM) 저장소에서 만들어지고 원격 Azure Storage에 저장 되지 않습니다. 사용 후 삭제 OS 디스크 VM 배포 시의 영향을 받는 또는 개별 VM 인스턴스를 이미지로 다시 설치를 더 잘에 대 한 상태 비저장 워크 로드에는 응용 프로그램은 개별 VM 실패 허용, 하지만 작동 합니다. 사용 후 삭제 OS 디스크를 더 낮은 읽기/쓰기 대기 시간을 OS 디스크를 VM 이미지로 다시 설치를 더 빠르게 얻을 수 있습니다. 
 
사용 후 삭제 디스크의 주요 기능은 다음과 같습니다. 
- 상태 비저장 응용 프로그램에 적합 합니다.
- Marketplace 및 사용자 지정 이미지를 사용 하 여 사용할 수 있습니다.
- 빠르게 다시 복원 하거나 이미지로 다시 설치 Vm 및 확장 하는 기능을 원래 상태로 부팅 인스턴스를 설정합니다.  
- 더 낮은 대기 시간, 임시 디스크와 비슷합니다. 
- 무료 사용 후 삭제 OS 디스크를 OS 디스크에 대 한 저장소 비용 없이 발생 합니다.
- 모든 Azure 지역에서 사용할 합니다. 
- 사용 후 삭제 OS 디스크에서 사용할 수 [공유 이미지 갤러리](/azure/virtual-machines/linux/shared-image-galleries)합니다. 
 

 
영구적으로 사용 후 삭제 OS 디스크 간의 주요 차이점:

|                             | 영구 OS 디스크                          | 사용 후 삭제 OS 디스크                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 디스크의 크기 제한      | 2 TiB                                                                                        | 캐시 VM 크기 또는 2TiB에 대 한 크기 중 더 작은 값입니다. 에 대 한 합니다 **캐시 GiB 단위의 크기**를 참조 하세요 [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), 및 [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 지원되는 VM 크기          | 모두                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| 디스크 형식 지원           | 관리 및 관리 되지 않는 OS 디스크                                                                | 관리 되는 OS 디스크에만 해당                                                               |
| 지역 지원              | 모든 지역                                                                                  | 모든 지역                              |
| 데이터 지속성            | OS 디스크에 기록 된 OS 디스크 데이터는 Azure Storage에 저장 됩니다.                                  | OS 디스크에 기록 된 데이터는 로컬 VM 저장소에 저장 되 고 Azure Storage에 유지 되지 않습니다. |
| 중지-할당 해제 상태      | Vm 및 확장 집합 인스턴스 중지-할당 해제 하거나 중지-할당 취소 된 상태에서 다시 시작할 수 있습니다. | Vm 및 확장 집합 인스턴스 중지-할당 해제 될 수 없습니다.                                  |
| 특수 한 OS 디스크 지원 | 예                                                                                          | 아니요                                                                                 |
| OS 디스크 크기 조정              | 지원 되는 VM 만드는 동안 및 후 VM이 중지-할당 해제                                | VM 만들기만 하는 동안 지원                                                  |
| 새 VM 크기를 크기 조정   | OS 디스크 데이터는 보존 됨                                                                    | OS 디스크에 데이터가 삭제 되는 OS 다시 프로 비전                                      |

## <a name="size-requirements"></a>크기 요구 사항

최대 VM 캐시 크기가 VM 인스턴스와 이미지를 배포할 수 있습니다. 예를 들어, marketplace에서 표준 Windows Server 이미지는 127 GiB 보다 큰 캐시에 있는 VM 크기를 사용 해야 함을 의미 하는 약 127 GiB 합니다. 이 경우에 [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) 크기가 충분 하지 않은 86 gib 단위 캐시 크기는 합니다. 포함 한 Standard_DS2_v2 데 충분 172 gib 단위 캐시 크기를 있습니다. 이 경우에 Standard_DS3_v2는이 이미지를 사용 하 여 사용할 수 있는 DSv2 시리즈의 가장 작은 크기입니다. 로 표시 되어 있는 Marketplace 및 Windows Server 이미지의 기본 Linux 이미지 `[smallsize]` 약 30 GiB 경향이 있으며 대부분의 사용 가능한 VM 크기를 사용할 수 있습니다.

사용 후 삭제 디스크는 VM 크기를 프리미엄 저장소를 지원 해야 합니다. 크기는 일반적으로 (항상 그렇지는 않지만)가는 `s` DSv2 및 EsV3 같은 이름입니다. 자세한 내용은 참조 하세요. [Azure VM 크기](../articles/virtual-machines/linux/sizes.md) 대 한 자세한 내용은 해결 하는 크기는 Premium storage를 지원 합니다.

## <a name="powershell"></a>PowerShell

PowerShell VM 배포에는 사용 후 삭제 디스크를 사용 하려면 [집합 AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) VM 구성에서 합니다. 설정 합니다 `-DiffDiskSetting` 하 `Local` 및 `-Caching` 에 `ReadOnly`입니다.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

확장 집합 배포를 사용 합니다 [집합 AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) 구성에서 cmdlet. 설정 합니다 `-DiffDiskSetting` 하 `Local` 및 `-Caching` 에 `ReadOnly`입니다.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLI VM 배포를 위한 임시 디스크를 사용 하려면 설정 합니다 `--ephemeral-os-disk` 의 매개 변수 [az vm 만들기](/cli/azure/vm#az-vm-create) 에 `true` 및 `--os-disk-caching` 매개 변수를 `ReadOnly`입니다.

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

Scale sets를 사용 하면 동일한 `--ephemeral-os-disk true` 에 대 한 매개 변수 [az vmss 만들기](/cli/azure/vmss#az-vmss-create) 설정 합니다 `--os-disk-caching` 매개 변수를 `ReadOnly`입니다.

## <a name="portal"></a>포털   

Azure portal에서 열어 VM을 배포 하는 경우 사용 후 삭제 디스크를 사용 하도록 선택할 수 있습니다 합니다 **고급** 섹션을 **디스크** 탭 합니다. 에 대 한 **사용 하 여 사용 후 삭제 OS 디스크** 선택 **예**합니다.

![사용 후 삭제 OS 디스크를 사용 하도록 선택 라디오 단추를 보여 주는 스크린샷](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

사용 후 삭제 디스크를 사용 하기 위한 옵션은 회색으로 표시를 하는 경우 OS 이미지 보다 큰 캐시 크기를가지고 있지 않은 또는 Premium storage를 지원 하지 않는 VM 크기를 선택한 수 있습니다. 로 다시 이동 합니다 **기본 사항** 페이지 및 다른 VM 크기를 선택 합니다.

또한 포털을 사용 하 여 사용 후 삭제 OS 디스크를 사용 하 여 확장 집합을 만들 수 있습니다. 방금 충분히 큰 캐시 크기로 고 VM 크기를 선택 했는지 **사용 하 여 사용 후 삭제 OS 디스크** 선택 **예**합니다.

![크기 집합에는 사용 후 삭제 OS 디스크를 사용 하도록 선택 라디오 단추를 보여 주는 스크린샷](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>확장 집합 템플릿 배포  
사용 후 삭제 OS 디스크를 사용 하는 확장 집합을 만드는 프로세스를 추가 하는 것은 `diffDiskSettings` 속성을는 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 템플릿에서 리소스 종류입니다. 캐싱 정책으로 설정 되어 있어야 또한 `ReadOnly` 사용 후 삭제 OS 디스크에 대 한 합니다. 


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
템플릿을 사용 하는 사용 후 삭제 OS 디스크를 사용 하 여 VM을 배포할 수 있습니다. 사용 후 삭제 OS 디스크를 사용 하는 VM을 만드는 프로세스를 추가 하는 것은 `diffDiskSettings` 템플릿에서 microsoft.compute/virtualmachines 리소스 종류에는 속성입니다. 캐싱 정책으로 설정 되어 있어야 또한 `ReadOnly` 사용 후 삭제 OS 디스크에 대 한 합니다. 

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


## <a name="reimage-a-vm-using-rest"></a>REST를 사용 하 여 VM을 이미지로 다시 설치
현재 사용 후 삭제 OS 디스크를 사용 하 여 가상 머신 인스턴스를 이미지로 다시 설치 하는 유일한 방법은 REST API를 사용 하는 것입니다. 확장 집합 이미지로 다시 설치는 이미 Powershell, CLI 및 포털을 통해 사용할 수 있습니다.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 로컬 OS 디스크의 크기는?**

A: 플랫폼 및 OS 디스크에 모든 읽기/쓰기를 수 있는 가상 머신과 동일한 노드에서 로컬 VM 캐시 크기, 최대 사용자 지정 이미지를 지원 합니다. 

**Q: 사용 후 삭제 OS 디스크 크기를 조정할 수 있습니까?**

A: 아니요, 사용 후 삭제 OS 디스크를 프로 비전 되 면 OS 디스크 크기 조정할 수 없습니다. 

**Q: 임시 VM에 Managed Disks를 연결할 수 있나요?**

A: 예, 사용 후 삭제 OS 디스크를 사용 하는 VM에 관리 되는 데이터 디스크를 연결할 수 있습니다. 

**Q: 모든 VM 크기 사용 후 삭제 OS 디스크에 대 한 지원 되나요?**

A: 아니요. 모든 Premium Storage VM 크기는 지원 되는 (DS, ES, FS, GS 및 M) B-시리즈를 제외 하 고 N 시리즈 및 H 시리즈 크기입니다.  
 
**Q: 사용 후 삭제 OS 디스크에 적용할 수 기존 Vm 확장 집합 및?**

A: 아니요, 사용 후 삭제 OS 디스크는 VM 중 에서만 사용할 수 있습니다 및 확장 집합 만들기. 

**Q: 확장 집합의 임시 및 일반 OS 디스크를 혼합할 수 있습니다?**

A: 아니요, 다양 한 동일한 확장 집합 내에 임시 및 영구 OS 디스크 인스턴스 수는 없습니다. 

**Q: 사용 후 삭제 OS 디스크를 만들 수 있습니다 Powershell 또는 CLI를 사용 하 여?**

A: 예, REST, 템플릿, PowerShell 및 CLI를 사용 하 여 사용 후 삭제 OS 디스크를 사용 하 여 Vm을 만들 수 있습니다.

**Q: 기능 사용 후 삭제 OS 디스크를 사용 하 여 지원 되지 않습니다.**

A: 사용 후 삭제 디스크를 지원 하지 않습니다.
- VM 이미지 캡처
- 디스크 스냅샷 
- Azure 디스크 암호화 
- Azure Backup
- Azure Site Recovery  
- OS 디스크 교체 
