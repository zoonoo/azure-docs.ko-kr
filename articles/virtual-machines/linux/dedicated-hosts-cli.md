---
title: CLI를 사용하여 전용 호스트에 Linux VM 배포
description: Azure CLI를 사용하여 전용 호스트에 VM을 배포합니다.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127686"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Azure CLI를 사용하여 전용 호스트에 VM 배포
 

이 문서에서는 가상 시스템(VM)을 호스트하는 Azure [전용 호스트를](dedicated-hosts.md) 만드는 방법을 설명합니다. 

Azure CLI 버전 2.0.70 이상을 설치하고 을 사용하여 `az login`Azure 계정에 로그인했는지 확인합니다. 


## <a name="limitations"></a>제한 사항

- 가상 시스템 크기 집합은 현재 전용 호스트에서 지원되지 않습니다.
- 전용 호스트에 사용할 수 있는 크기와 하드웨어 유형은 지역에 따라 다릅니다. 자세한 내용은 호스트 [가격 페이지를](https://aka.ms/ADHPricing) 참조하십시오.
 

## <a name="create-resource-group"></a>리소스 그룹 만들기 
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. az group create을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 동부* 위치에 *myDHResourceGroup이라는* 리소스 그룹을 만듭니다.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>호스트 그룹 만들기 

**호스트 그룹은** 전용 호스트의 컬렉션을 나타내는 리소스입니다. 리전 및 가용성 영역에서 호스트 그룹을 만들고 호스트를 추가합니다. 고가용성을 계획할 때 추가 옵션이 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다사용할 수 있습니다. 
- 여러 가용성 영역에 걸쳐 있습니다. 이 경우 사용하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 물리적 랙에 매핑되는 여러 오류 도메인에 걸쳐 스팬합니다. 
 
두 경우 모두 호스트 그룹에 대한 장애 도메인 수를 제공해야 합니다. 그룹의 장애 도메인을 포괄하지 않으려면 오류 도메인 수를 1로 사용합니다. 

가용성 영역과 장애 도메인을 모두 사용하도록 결정할 수도 있습니다. 

이 예제에서는 az [vm 호스트 그룹 create를](/cli/azure/vm/host/group#az-vm-host-group-create) 사용하여 가용성 영역과 장애 도메인을 모두 사용하여 호스트 그룹을 만듭니다. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>다른 예제

[az vm 호스트 그룹 만들기를](/cli/azure/vm/host/group#az-vm-host-group-create) 사용하여 가용성 영역 1(오류 도메인 없음)에서 호스트 그룹을 만들 수도 있습니다.

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
다음은 [az vm 호스트 그룹 만들기를](/cli/azure/vm/host/group#az-vm-host-group-create) 사용하여 오류 도메인만 사용하여 호스트 그룹을 만듭니다(가용성 영역이 지원되지 않는 지역에서 사용). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>호스트 만들기 

이제 호스트 그룹에 전용 호스트를 만들어 보겠습니다. 호스트의 이름 외에도 호스트에 대한 SKU를 제공해야 합니다. Host SKU는 지원되는 VM 시리즈와 전용 호스트의 하드웨어 생성을 캡처합니다.  

호스트 SCO 및 가격 책정에 대한 자세한 내용은 [Azure 전용 호스트 가격 책정을](https://aka.ms/ADHPricing)참조하십시오.

[az vm 호스트 만들기를](/cli/azure/vm/host#az-vm-host-create) 사용하여 호스트를 만듭니다. 호스트 그룹에 대한 오류 도메인 수를 설정하면 호스트에 대한 오류 도메인을 지정하라는 메시지가 표시됩니다.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>가상 머신 만들기 
[az vm create를](/cli/azure/vm#az-vm-create)사용하여 전용 호스트 내에서 가상 컴퓨터를 만듭니다. 호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 컴퓨터를 만들 때 동일한 영역을 사용해야 합니다.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> 리소스가 충분하지 않은 호스트에 가상 컴퓨터를 만들면 가상 시스템이 FAILED 상태로 만들어집니다. 


## <a name="check-the-status-of-the-host"></a>호스트의 상태 확인

az vm 호스트 [get-instance-view를](/cli/azure/vm/host#az-vm-host-get-instance-view)사용하여 호스트 상태 와 호스트에 배포할 수 있는 가상 컴퓨터 수를 확인할 수 있습니다.

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 출력은 다음과 유사합니다.
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>템플릿으로 내보내기 
이제 동일한 매개 변수를 사용하여 추가 개발 환경 또는 템플릿과 일치하는 프로덕션 환경을 만들려는 경우 템플릿을 내보낼 수 있습니다. Resource Manager는 사용자 환경에 대한 모든 매개 변수를 정의하는 JSON 템플릿을 사용합니다. 이 JSON 템플릿을 참조하여 전체 환경을 빌드합니다. JSON 템플릿을 수동으로 빌드하거나 기존 환경을 내보내 JSON 템플릿을 만들 수 있습니다. [az 그룹 내보내기를](/cli/azure/group#az-group-export) 사용하여 리소스 그룹을 내보냅니다.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

이 명령을 실행하면 `myDHResourceGroup.json` 파일이 현재 작업 디렉터리에 만들어집니다. 이 템플릿에서 환경을 만들면 모든 리소스 이름을 입력하라는 메시지가 표시됩니다. `az group export` 명령에 `--include-parameter-default-value` 매개 변수를 추가하여 템플릿 파일에 이러한 이름을 입력할 수 있습니다. JSON 템플릿을 편집하여 리소스 이름을 지정하거나 리소스 이름을 지정하는 parameters.json 파일을 만듭니다 .
 
템플릿에서 환경을 만들려면 [az 그룹 배포 만들기를](/cli/azure/group/deployment#az-group-deployment-create)사용합니다.

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>정리 

가상 컴퓨터를 배포하지 않은 경우에도 전용 호스트에 대한 요금이 부과됩니다. 비용을 절감하기 위해 현재 사용하지 않는 호스트를 삭제해야 합니다.  

호스트를 사용하는 가상 시스템이 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다. [az vm 삭제를 사용하여 VM을 삭제합니다.](/cli/azure/vm#az-vm-delete)

```bash
az vm delete -n myVM -g myDHResourceGroup
```

VM을 삭제한 후 [az vm 호스트 삭제를](/cli/azure/vm/host#az-vm-host-delete)사용하여 호스트를 삭제할 수 있습니다.

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
모든 호스트를 삭제한 후에는 [az vm 호스트 그룹 삭제를](/cli/azure/vm/host/group#az-vm-host-group-delete)사용하여 호스트 그룹을 삭제할 수 있습니다.  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 이렇게 하면 모든 VM, 호스트 및 호스트 그룹을 포함하여 그룹에서 만든 모든 리소스가 삭제됩니다.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 전용 [호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 포털을](dedicated-hosts-portal.md)사용하여 전용 호스트를 만들 수도 있습니다.

- 영역과 오류 도메인을 모두 사용하여 영역의 복원력을 극대화하는 샘플 템플릿이 [있습니다.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)