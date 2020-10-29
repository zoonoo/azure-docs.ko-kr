---
title: 사용 후 삭제 OS 디스크
description: Azure Vm의 임시 OS 디스크에 대해 자세히 알아보세요.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f915652110524aac06d641d636155bc6a5fcd256
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927926"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Azure Vm 용 삭제 OS 디스크

삭제 된 OS 디스크는 로컬 VM (가상 컴퓨터) 저장소에 생성 되며 원격 Azure Storage에 저장 되지 않습니다. 사용 후 삭제 OS 디스크는 응용 프로그램이 개별 VM 오류를 허용 하는 상태 비저장 워크 로드에서 잘 작동 하지만 VM 배포 시간 또는 개별 VM 인스턴스 이미지로 다시 설치에 더 많은 영향을 줍니다. 사용 후 삭제 OS 디스크를 사용 하는 경우 OS 디스크에 대 한 읽기/쓰기 대기 시간 및 더 빠른 VM 이미지로 다시 설치 됩니다. 
 
임시 디스크의 핵심 기능은 다음과 같습니다. 
- 상태 비저장 응용 프로그램에 적합 합니다.
- Marketplace 및 사용자 지정 이미지에서 모두 사용할 수 있습니다.
- Vm 및 확장 집합 인스턴스를 원래 부팅 상태로 신속 하 게 다시 설정 하거나 이미지로 다시 설치 하는 기능.  
- 임시 디스크와 비슷한 대기 시간을 줄입니다. 
- 사용 후 삭제 OS 디스크는 OS 디스크에 대 한 저장소 비용이 들지 않습니다.
- 모든 Azure 지역에서 사용할 수 있습니다. 
- 삭제 된 OS 디스크는 [공유 이미지 갤러리](./linux/shared-image-galleries.md)에서 지원 됩니다. 
 

 
영구 및 임시 OS 디스크의 주요 차이점:

|                             | 영구적 OS 디스크                          | 사용 후 삭제 OS 디스크                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **OS 디스크의 크기 제한**      | 2TiB                                                                                        | VM 크기나 2TiB의 캐시 크기 중 더 작은 쪽입니다. **GiB의 캐시 크기** 에 대해서는 [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)및 [GS](sizes-previous-gen.md#gs-series) 를 참조 하세요.              |
| **지원 되는 VM 크기**          | 모두                                                                                          | Premium storage를 지 원하는 VM 크기 (예: DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M)                                               |
| **디스크 유형 지원**           | 관리 및 관리 되지 않는 OS 디스크                                                                | 관리 되는 OS 디스크만                                                               |
| **지역 지원**              | 모든 지역                                                                                  | 모든 지역                              |
| **데이터 지속성**            | Os 디스크에 작성 된 OS 디스크 데이터는 Azure Storage에 저장 됩니다.                                  | OS 디스크에 기록 되는 데이터는 로컬 VM 저장소에 저장 되 고 Azure Storage에 유지 되지 않습니다. |
| **중지-할당 취소 됨 상태**      | Vm 및 확장 집합 인스턴스는 중지-할당 취소 되 고 중지-할당 취소 됨 상태에서 다시 시작 될 수 있습니다. | Vm 및 확장 집합 인스턴스는 중지-할당 취소할 수 없습니다.                                  |
| **특수 OS 디스크 지원** | 예                                                                                          | 예                                                                                 |
| **OS 디스크 크기 조정**              | VM을 만드는 동안 및 VM이 중지 되 고 할당이 취소 된 후에 지원 됨                                | VM을 만드는 동안에만 지원 됨                                                  |
| **새 VM 크기로 크기 조정**   | OS 디스크 데이터가 보존 됩니다.                                                                    | Os 디스크의 데이터가 삭제 되 고 OS가 다시 프로 비전 됩니다.       
| **페이지 파일 배치**   | Windows의 경우 페이지 파일이 리소스 디스크에 저장 됩니다.                                              | Windows의 경우 페이지 파일은 OS 디스크에 저장 됩니다.   |

## <a name="size-requirements"></a>크기 요구 사항

Vm 캐시 크기까지 VM 및 인스턴스 이미지를 배포할 수 있습니다. 예를 들어 marketplace의 표준 Windows Server 이미지는 약 127 GiB, 127 GiB 보다 큰 캐시가 있는 VM 크기가 필요 함을 의미 합니다. 이 경우 [Standard_DS2_v2](dv2-dsv2-series.md) 의 캐시 크기가 86 GiB이 고 크기가 크지 않습니다. Standard_DS3_v2의 캐시 크기가 172 GiB입니다 .이는 크기가 충분 합니다. 이 경우 Standard_DS3_v2는이 이미지에서 사용할 수 있는 DSv2 시리즈의 가장 작은 크기입니다. Marketplace의 기본 Linux 이미지와로 표시 되는 Windows Server 이미지는 `[smallsize]` 가장 GiB 30 개의 사용 가능한 VM 크기를 사용할 수 있습니다.

또한 임시 디스크는 VM 크기가 Premium storage를 지원 해야 합니다. 크기 (항상 그렇지는 않음) `s` 에는 이름에 DSv2 및 EsV3와 같은가 있습니다. 자세한 내용은 프리미엄 저장소를 지 원하는 크기에 대 한 자세한 내용은 [AZURE VM 크기](sizes.md) 를 참조 하세요.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>미리 보기-이제 임시 디스크에 임시 OS 디스크를 저장할 수 있습니다.
이제 삭제 된 OS 디스크를 vm 캐시 외에도 VM 임시/리소스 디스크에 저장할 수 있습니다. 따라서 이제 캐시가 없거나 캐시 공간이 부족 하지만 Dav3, Dav4, Eav4 및 Eav3와 같은 사용 후 삭제 OS 디스크를 저장 하기 위한 temp/resource 디스크가 있는 VM에서 사용 후 삭제 OS 디스크를 사용할 수 있습니다. VM에 캐시 및 임시 공간이 충분 한 경우 이제는 [Diffdiskplacement](/rest/api/compute/virtualmachines/list#diffdiskplacement)라는 새 속성을 사용 하 여 임시 OS 디스크를 저장 하려는 위치도 지정할 수 있습니다. 이 기능을 사용 하 여 Windows VM이 프로 비전 되 면 OS 디스크에 있는 페이지 파일을 구성 합니다. 이 기능은 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 시작 하려면 액세스를 [요청](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)하세요.

## <a name="powershell"></a>PowerShell

PowerShell VM 배포에 임시 디스크를 사용 하려면 VM 구성에서 [AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) 를 사용 합니다. 을로 설정 하 고를로 설정 `-DiffDiskSetting` `Local` `-Caching` `ReadOnly` 합니다.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

확장 집합 배포의 경우 구성에서 [AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet을 사용 합니다. 을로 설정 하 고를로 설정 `-DiffDiskSetting` `Local` `-Caching` `ReadOnly` 합니다.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLI VM 배포에 임시 디스크를 사용 하려면 `--ephemeral-os-disk` [az VM create](/cli/azure/vm#az-vm-create) 에서 매개 변수를로 설정 하 `true` 고 `--os-disk-caching` 매개 변수를로 설정 `ReadOnly` 합니다.

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

확장 집합의 경우 `--ephemeral-os-disk true` [az-vmss-create](/cli/azure/vmss#az-vmss-create) 에 동일한 매개 변수를 사용 하 고 `--os-disk-caching` 매개 변수를로 설정 `ReadOnly` 합니다.

## <a name="portal"></a>포털   

Azure Portal에서 **디스크** 탭의 **고급** 섹션을 열어 VM을 배포할 때 임시 디스크를 사용 하도록 선택할 수 있습니다. **사용 후 삭제 OS 디스크 사용** **예** 를 선택 합니다.

![사용 후 삭제 OS 디스크를 사용 하도록 선택 하는 라디오 단추를 보여 주는 스크린샷](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

사용 후 삭제 디스크를 사용 하는 옵션이 회색으로 표시 된 경우 OS 이미지 보다 큰 캐시 크기가 없거나 Premium storage를 지원 하지 않는 VM 크기를 선택할 수 있습니다. **기본** 페이지로 돌아가서 다른 VM 크기를 선택 해 봅니다.

포털을 사용 하 여 사용 후 삭제 OS 디스크로 확장 집합을 만들 수도 있습니다. 충분 한 캐시 크기를 가진 VM 크기를 선택 하 고 **사용 후 삭제 OS 디스크 사용** 에서 **예** 를 선택 합니다.

![확장 집합에 대 한 사용 후 삭제 OS 디스크를 사용 하도록 선택 하는 라디오 단추를 보여 주는 스크린샷](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>확장 집합 템플릿 배포  
사용 후 삭제 OS 디스크를 사용 하는 확장 집합을 만드는 프로세스는 `diffDiskSettings` 템플릿의 리소스 유형에 속성을 추가 하는 것입니다 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` . 또한 `ReadOnly` 임시 OS 디스크에 대해 캐싱 정책을로 설정 해야 합니다. 


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
템플릿을 사용 하 여 임시 OS 디스크를 사용 하는 VM을 배포할 수 있습니다. 사용 후 삭제 OS 디스크를 사용 하는 VM을 만드는 프로세스는 `diffDiskSettings` 템플릿의 virtualMachines/리소스 유형에 속성을 추가 하는 것입니다. 또한 `ReadOnly` 임시 OS 디스크에 대해 캐싱 정책을로 설정 해야 합니다. 

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


## <a name="reimage-a-vm-using-rest"></a>REST를 사용 하 여 VM 이미지로 다시 설치
아래에 설명 된 대로 REST API를 사용 하 고 VM의 개요 창으로 이동 하 여 Azure Portal을 통해 사용 후 삭제 OS 디스크를 사용 하 여 가상 머신 인스턴스를 이미지로 다시 설치할 수 있습니다. 확장 집합의 경우 Powershell, CLI 및 포털을 통해 이미지로 다시 설치를 이미 사용할 수 있습니다.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 로컬 OS 디스크의 크기는 어떻게 되나요?**

A: OS 디스크에 대 한 모든 읽기/쓰기는 가상 머신과 동일한 노드에서 로컬이 되는 플랫폼 및 사용자 지정 이미지를 지원 합니다. 

**Q: 삭제 된 OS 디스크의 크기를 조정할 수 있나요?**

A: 아니요, 임시 OS 디스크를 프로 비전 한 후에는 OS 디스크의 크기를 조정할 수 없습니다. 

**Q: Managed Disks을 임시 VM에 연결할 수 있나요?**

A: 예, 사용 후 삭제 OS 디스크를 사용 하는 VM에 관리 되는 데이터 디스크를 연결할 수 있습니다. 

**Q: 모든 VM 크기는 임시 OS 디스크에 대해 지원 되나요?**

A: 아니요, 대부분의 Premium Storage VM 크기 (DS, ES, FS, GS, M 등)가 지원 됩니다. 특정 VM 크기에서 임시 OS 디스크를 지원 하는지 여부를 확인 하려면 다음을 수행할 수 있습니다.

PowerShell cmdlet을 호출 합니다. `Get-AzComputeResourceSku`
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**Q: 삭제 된 OS 디스크를 기존 Vm 및 확장 집합에 적용할 수 있나요?**

A: 아니요, 사용 후 삭제 OS 디스크는 VM 및 확장 집합을 만드는 동안에만 사용할 수 있습니다. 

**Q: 확장 집합에서 임시 및 일반 OS 디스크를 혼합할 수 있나요?**

A: 아니요, 동일한 확장 집합 내에서 임시 및 영구 OS 디스크 인스턴스를 혼합할 수 없습니다. 

**Q: Powershell 또는 CLI를 사용 하 여 사용 후 삭제 OS 디스크를 만들 수 있나요?**

A: 예, REST, 템플릿, PowerShell 및 CLI를 사용 하 여 사용 후 삭제 OS 디스크로 Vm을 만들 수 있습니다.

**Q: 사용 후 삭제 OS 디스크에서 지원 되지 않는 기능은 무엇입니까?**

A: 임시 디스크는 다음을 지원 하지 않습니다.
- VM 이미지 캡처
- 디스크 스냅샷 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OS 디스크 교환 
 
## <a name="next-steps"></a>다음 단계
[Azure CLI](/cli/azure/vm#az-vm-create)를 사용 하 여 사용 후 삭제 OS 디스크로 VM을 만들 수 있습니다.
