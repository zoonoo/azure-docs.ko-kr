---
title: CLI를 사용 하 여 전용 호스트에 Linux Vm 배포
description: Azure CLI를 사용 하 여 전용 호스트에 Vm을 배포 합니다.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: f712d1be76a9bb3dc4856b9f1fa7c7b805296dea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970766"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Azure CLI를 사용 하 여 전용 호스트에 Vm 배포
 

이 문서에서는 Vm (가상 머신)을 호스트 하는 Azure [전용 호스트](dedicated-hosts.md) 를 만드는 방법을 안내 합니다. 

Azure CLI 버전 2.0.70 이상을 설치 하 고 `az login`를 사용 하 여 Azure 계정에 로그인 했는지 확인 합니다. 


## <a name="limitations"></a>제한 사항

- 가상 머신 확장 집합은 현재 전용 호스트에서 지원 되지 않습니다.
- 초기 릴리스는 다음 VM 시리즈를 지원 합니다. DSv3, ESv3, FSv2, LSv2 및 MSv2. 
 

## <a name="create-resource-group"></a>리소스 그룹 만들기 
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Az group create를 사용 하 여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 동부* 위치에 *mydhresourcegroup* 이라는 리소스 그룹을 만듭니다.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>호스트 그룹 만들기 

**호스트 그룹** 은 전용 호스트의 컬렉션을 나타내는 리소스입니다. 지역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가 합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다. 
- 여러 가용성 영역에 걸쳐 있습니다. 이 경우에는 사용 하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑되는 여러 장애 도메인에 걸쳐 있습니다. 
 
두 경우 모두 호스트 그룹의 장애 도메인 수를 제공 해야 합니다. 그룹의 장애 도메인을 확장 하지 않으려면 장애 도메인 수 1을 사용 합니다. 

가용성 영역 및 장애 도메인을 모두 사용 하도록 결정할 수도 있습니다. 

이 예제에서는 [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) 를 사용 하 여 가용성 영역 및 장애 도메인을 모두 사용 하는 호스트 그룹을 만듭니다. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>다른 예제

[Az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) 를 사용 하 여 가용성 영역 1에 호스트 그룹을 만들 수도 있습니다 (장애 도메인 없음).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
다음에서는 [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) 를 사용 하 여 장애 도메인만 사용 하 여 호스트 그룹을 만듭니다 (가용성 영역이 지원 되지 않는 지역에서 사용 됨). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>호스트 만들기 

이제 호스트 그룹에서 전용 호스트를 만들어 보겠습니다. 호스트의 이름 외에 호스트의 SKU를 제공 해야 합니다. 호스트 SKU는 지원 되는 VM 시리즈 뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.  DSv3_Type1 및 ESv3_Type1 SKU 값이 지원 됩니다.

호스트 Sku 및 가격 책정에 대 한 자세한 내용은 [Azure 전용 호스트 가격](https://aka.ms/ADHPricing)을 참조 하세요.

[Az vm host create](/cli/azure/vm/host#az-vm-host-create) 를 사용 하 여 호스트를 만듭니다. 호스트 그룹에 대 한 장애 도메인 수를 설정 하는 경우 호스트에 대 한 장애 도메인을 지정 하 라는 메시지가 표시 됩니다.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>가상 머신 만들기 
[Az vm create](/cli/azure/vm#az-vm-create)를 사용 하 여 전용 호스트 내에서 가상 머신을 만듭니다. 호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 머신을 만들 때 동일한 영역을 사용 해야 합니다.

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
> 리소스가 부족 한 호스트에서 가상 컴퓨터를 만드는 경우 가상 컴퓨터는 실패 상태로 생성 됩니다. 


## <a name="check-the-status-of-the-host"></a>호스트의 상태를 확인 합니다.

[Az vm host get instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view)를 사용 하 여 호스트 상태와 호스트에 배포할 수 있는 가상 컴퓨터 수를 확인할 수 있습니다.

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
이제 동일한 매개 변수를 사용 하 여 추가 개발 환경을 만들려고 하거나 일치 하는 프로덕션 환경에서 템플릿을 내보낼 수 있습니다. Resource Manager는 사용자 환경에 대한 모든 매개 변수를 정의하는 JSON 템플릿을 사용합니다. 이 JSON 템플릿을 참조하여 전체 환경을 빌드합니다. Json 템플릿을 수동으로 작성 하거나 기존 환경을 내보내 JSON 템플릿을 만들 수 있습니다. [Az group export](/cli/azure/group#az-group-export) 를 사용 하 여 리소스 그룹을 내보냅니다.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

이 명령을 실행하면 `myDHResourceGroup.json` 파일이 현재 작업 디렉터리에 만들어집니다. 이 템플릿에서 환경을 만들면 모든 리소스 이름을 입력하라는 메시지가 표시됩니다. `--include-parameter-default-value` 명령에 `az group export` 매개 변수를 추가하여 템플릿 파일에 이러한 이름을 입력할 수 있습니다. JSON 템플릿을 편집 하 여 리소스 이름을 지정 하거나 리소스 이름을 지정 하는 매개 변수. json 파일을 만듭니다.
 
템플릿에서 환경을 만들려면 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)를 사용 합니다.

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>정리 

가상 컴퓨터가 배포 되지 않은 경우에도 전용 호스트에 대 한 요금이 청구 됩니다. 비용을 절약 하기 위해 현재 사용 하지 않는 호스트를 모두 삭제 해야 합니다.  

호스트를 사용 하는 가상 컴퓨터가 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다. [Az vm delete](/cli/azure/vm#az-vm-delete)를 사용 하 여 vm을 삭제 합니다.

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Vm을 삭제 한 후 [az vm host delete](/cli/azure/vm/host#az-vm-host-delete)를 사용 하 여 호스트를 삭제할 수 있습니다.

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
모든 호스트를 삭제 한 후에는 [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete)를 사용 하 여 호스트 그룹을 삭제할 수 있습니다.  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 그러면 모든 Vm, 호스트 및 호스트 그룹을 포함 하 여 그룹에 생성 된 모든 리소스가 삭제 됩니다.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조 하세요.

- [Azure Portal](dedicated-hosts-portal.md)를 사용 하 여 전용 호스트를 만들 수도 있습니다.

- 영역에서 최대 복원 력을 위해 영역 및 장애 도메인을 모두 사용 하는 샘플 템플릿은 [여기](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)에 있습니다.