---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3e9885466d422a0428311ed3013e2ab34341cd25
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391288"
---
사용 후 삭제 OS 디스크는 가상 머신 (VM) 저장소에서 생성 되 고 원격 Azure Storage에 유지 되지 않습니다. 사용 후 삭제 OS 디스크는 응용 프로그램의 개별 VM 오류에 대 한 내성을 있지만 대규모 배포에 걸리는 시간 또는 개별 VM 인스턴스를 이미지로 다시 설치 하는 시간에 더 관심이 있는 상태 비저장 워크 로드에 대 한 잘 작동 합니다. Resource Manager 배포 모델로 이동 하는 클래식 배포 모델을 사용 하 여 배포 응용 프로그램에 적합 한 이기도 합니다. 사용 후 삭제되는 OS 디스크를 활용하면 OS 디스크에서 발생하는 읽기 및 쓰기 대기 시간이 줄어들고 VM의 이미지로 다시 설치 작업이 더 빨라집니다. 또한 사용 후 삭제 OS 디스크는 무료, OS 디스크에 대 한 저장소 비용 없이 발생 합니다. 
 
사용 후 삭제 디스크의 주요 기능은 다음과 같습니다. 
- Marketplace 이미지 및 사용자 지정 이미지를 사용 하 여 사용할 수 있습니다.
- 최대 VM 캐시 크기가 VM 이미지를 배포할 수 있습니다.
- 빠른 다시 설정 하거나 원래 부팅 상태로 Vm을 이미지로 다시 설치할 수 있습니다.  
- 더 낮은 실행 대기 시간 임시 디스크와 비슷합니다. 
- OS 디스크에 대 한 비용이 없습니다. 
 
 
영구적으로 사용 후 삭제 OS 디스크 간의 주요 차이점:

|                             | 영구 OS 디스크                          | 사용 후 삭제 OS 디스크                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 디스크의 크기 제한      | 2 TiB                                                                                        | 캐시 VM 크기 또는 2TiB, 크기 중 더 작은 값- [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md)합니다 [M](../articles/virtual-machines/linux/sizes-memory.md)를 [FS](../articles/virtual-machines/linux/sizes-compute.md), 및 [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| 지원되는 VM 크기          | 모두                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| 디스크 형식 지원           | 관리 및 관리 되지 않는 OS 디스크                                                                | 관리 되는 OS 디스크에만 해당                                                               |
| 지역 지원              | 모든 지역                                                                                  | 모든 지역                              |
| 데이터 지속성            | OS 디스크에 기록 된 OS 디스크 데이터는 Azure Storage에 저장 됩니다.                                  | OS 디스크에 기록 된 데이터는 로컬 VM 저장소에 저장 되 고 Azure Storage에 유지 되지 않습니다. |
| 중지-할당 해제 상태      | Vm 및 확장 집합 인스턴스 중지-할당 해제 하거나 중지-할당 취소 된 상태에서 다시 시작할 수 있습니다. | Vm 및 확장 집합 인스턴스 중지-할당 해제 될 수 없습니다.                                  |
| 특수 한 OS 디스크 지원 | 예                                                                                          | 아닙니다.                                                                                 |
| OS 디스크 크기 조정              | 지원 되는 VM 만드는 동안 및 후 VM이 중지-할당 해제                                | VM 만들기만 하는 동안 지원                                                  |
| 새 VM 크기를 크기 조정   | OS 디스크 데이터는 보존 됨                                                                    | OS 디스크에 데이터가 삭제 되는 OS 다시 프로 비전                                      |

## <a name="scale-set-deployment"></a>확장 집합 배포  
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

## <a name="vm-deployment"></a>VM 배포 
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

A: 미리 보기에 대 한 플랫폼 및/또는 OS 디스크에 모든 읽기/쓰기를 수 있는 가상 머신과 동일한 노드에서 로컬 VM 캐시 크기를 최대 이미지 지원 됩니다. 

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
