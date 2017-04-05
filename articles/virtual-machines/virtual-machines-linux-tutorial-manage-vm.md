---
title: "Azure CLI로 Linux 가상 컴퓨터 관리 | Microsoft Docs"
description: "자습서 - Azure CLI를 사용하여 Linux 가상 컴퓨터 관리"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 14d3907d9ddbe42c9f919ce4d29ba7d58c7c5296
ms.lasthandoff: 03/29/2017

---

# <a name="manage-linux-virtual-machines-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux 가상 컴퓨터 관리

이 자습서에서는 가상 컴퓨터를 만들고, 디스크 추가, 소프트웨어 설치 자동화 및 가상 컴퓨터 스냅숏 만들기 등과 같은 일반적인 관리 작업을 수행합니다. 

이 자습서를 완료하려면 최신 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치했는지 확인합니다.

## <a name="step-1--log-in-to-azure"></a>1단계 - Azure에 로그인

먼저 터미널을 열고 [az login](/cli/azure/#login) 명령을 입력하여 Azure 구독에 로그인합니다.

```azurecli
az login
```

## <a name="step-2--create-resource-group"></a>2단계 - 리소스 그룹 만들기

[az group create](https://docs.microsoft.com/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. 

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 컴퓨터보다 먼저 리소스 그룹을 만들어야 합니다. 이 예제에서는 `westeurope` 하위 지역에 `myResourceGroup`이라는 리소스 그룹이 만들어집니다. 

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="step-3---prepare-configuration"></a>3단계: 구성 준비

가상 컴퓨터를 배포할 때 **cloud-init**를 사용하여 패키지 설치, 파일 생성 및 스크립트 실행과 같은 구성을 자동화할 수 있습니다. 이 자습서에서는 다음 두 항목의 구성이 자동화됩니다.

- NGINX 웹 서버 설치
- VM의 두 번째 디스크 프로비전

**cloud-init** 구성이 VM 배포 시 발생하므로 가상 컴퓨터를 만들기 전에 **cloud-init** 구성을 정의해야 합니다.

`cloud-init.txt`라는 파일 이름을 만들고 다음 내용을 복사합니다. 이 구성은 NGINX 패키지를 설치하고 보조 디스크를 포맷한 후 탑재하는 명령을 실행합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - (echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
  - sudo mkfs -t ext4 /dev/sdc1
  - sudo mkdir /datadrive
  - sudo mount /dev/sdc1 /datadrive
```

## <a name="step-4---create-virtual-machine"></a>4단계 - 가상 컴퓨터 만들기

[az vm create](https://docs.microsoft.com/cli/azure/vm#create) 명령을 사용하여 가상 컴퓨터를 만듭니다. 

가상 컴퓨터를 만들 때 운영 체제 이미지, 디스크 크기 조정 및 관리 자격 증명 등의 몇 가지 옵션을 사용할 수 있습니다. 이 예제에서는 Ubuntu를 실행하는 `myVM`이라는 가상 컴퓨터가 만들어집니다. `--data-disk-sizes-gb` 인수를 사용하여 50GB 디스크가 만들어지고 VM에 연결됩니다. `--custom-data` 인수는 cloud-init 구성을 받아들인 후 VM에 스테이징합니다. 마지막으로 SSH 키가 존재하지 않는 경우 만들어집니다.

```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --generate-ssh-keys \
  --data-disk-sizes-gb 50 \
  --custom-data cloud-init.txt
```

VM이 만들어지면 Azure CLI에서 다음 정보를 출력합니다. 공용 IP 주소를 적어 둡니다. 이 주소는 가상 컴퓨터에 액세스할 때 사용됩니다. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

VM이 배포되는 동안 **cloud-init** 구성을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

## <a name="step-5--configure-firewall"></a>5단계 - 방화벽 구성

Azure NSG([네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md))는 하나 또는 여러 가상 컴퓨터에 대한 인바운드 및 아웃바운드 트래픽을 제어합니다. 네트워크 보안 그룹 규칙은 특정 포트 또는 포트 범위에 대한 네트워크 트래픽을 허용하거나 거부합니다. 이러한 규칙에는 미리 정의된 원본에서 시작되는 트래픽만 가상 컴퓨터와 통신할 수 있도록 원본 주소 접두사가 포함될 수도 있습니다.

이전 섹션에서 NGINX 웹 서버가 설치되었을 것입니다. 포트 80에서 인바운드 트래픽을 허용하는 네트워크 보안 그룹 규칙이 없으면 인터넷을 통해 웹 서버에 액세스할 수 없습니다. 이 단계에서는 포트 80에서 인바운드 연결을 허용하도록 NSG 규칙을 만드는 과정을 안내합니다.

### <a name="create-nsg-rule"></a>NSG 규칙 만들기

인바운드 NSG 규칙을 만들려면 [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) 명령을 사용합니다. 다음 예제에서는 가상 컴퓨터에 대해 포트 `80`을 엽니다.

```azurecli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 
```

이제 가상 컴퓨터의 공용 IP 주소로 이동합니다. NSG 규칙이 설정되면 기본 NGINX 웹 사이트가 표시됩니다.

![NGINX 기본 사이트](./media/virtual-machines-linux-tutorial-manage-vm/nginx.png)  

## <a name="step-6--snapshot-virtual-machine"></a>6단계 – 가상 컴퓨터 스냅숏 생성

디스크 스냅숏을 생성하면 디스크의 읽기 전용, 지정 시간 복사본이 만들어집니다. 이 단계에서는 VM 운영 체제 디스크의 스냅숏이 생성됩니다. OS 디스크 스냅숏를 사용하여 가상 컴퓨터를 특정 상태로 신속하게 복원하거나 스냅숏을 사용하여 동일한 상태의 새 가상 컴퓨터를 만들 수 있습니다.

### <a name="create-snapshot"></a>스냅숏 만들기

스냅숏을 만들기 전에 디스크의 ID 또는 이름이 필요합니다. [az vm show](https://docs.microsoft.com/cli/azure/vm#show) 명령을 사용하여 디스크 ID를 가져옵니다. 이 예제에서는 디스크 ID가 변수에 저장되고 이후 단계에서 사용됩니다.

```azurecli
osdiskid=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

이제 디스크의 ID를 알고 있으므로 다음 명령을 실행하여 스냅숏을 만듭니다.

```azurcli
az snapshot create -g myResourceGroup --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>스냅숏에서 디스크 만들기

그런 다음 스냅숏을 디스크로 복원하여 가상 컴퓨터를 다시 만드는 데 사용할 수 있습니다.

```azurecli
az disk create --resource-group myResourceGroup --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>스냅숏에서 가상 컴퓨터 복원

가상 컴퓨터 복구를 보여 주기 위해 기존 가상 컴퓨터를 삭제합니다. 

```azurecli
az vm delete --resource-group myResourceGroup --name myVM
```

가상 컴퓨터를 다시 만들 때 기존 네트워크 인터페이스가 다시 사용됩니다. 이렇게 하면 네트워크 보안 구성이 유지됩니다.

[az network nic list](https://docs.microsoft.com/cli/azure/network/nic#list) 명령을 사용하여 네트워크 인터페이스 이름을 가져옵니다. 이 예제에서는 `nic`라는 변수에 해당 이름을 추가합니다. 이 변수는 다음 단계에서 사용됩니다.

```azurecli
nic=$(az network nic list --resource-group myResourceGroup --query "[].[name]" -o tsv)
```

스냅숏 디스크에서 새 가상 컴퓨터를 만듭니다.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --attach-os-disk mySnapshotDisk --os-type linux --nics $nic
```

새 공용 IP 주소를 적어둔 후 인터넷 브라우저를 사용하여 이 주소로 이동합니다. NGINX가 복원된 가상 컴퓨터에서 실행되고 있음을 확인할 수 있습니다. 

### <a name="reconfigure-data-disk"></a>데이터 디스크 다시 구성

이제 데이터 디스크를 가상 컴퓨터에 다시 연결할 수 있습니다. 

먼저 [az disk list](https://docs.microsoft.com/cli/azure/disk#list) 명령을 사용하여 데이터 디스크 이름을 찾습니다. 이 예제에서는 `datadisk`라는 변수에 디스크의 이름을 추가합니다. 이 변수는 다음 단계에서 사용됩니다.

```azurecli
datadisk=$(az disk list -g myResourceGroup --query "[?contains(name,'myVM')].[name]" -o tsv)
```

[az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) 명령을 사용하여 디스크를 연결합니다.

```azurecli
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk $datadisk
```

또한 이 디스크를 운영 체제에 탑재해야 합니다. 디스크를 탑재하려면 가상 컴퓨터에 연결하고 `sudo mount /dev/sdc1 /datadrive` 또는 기본 설정 디스크 탑재 작업을 실행합니다. 

## <a name="step-7--management-tasks"></a>7단계 - 관리 작업

가상 컴퓨터의 수명 주기 동안 가상 컴퓨터 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 스크립트를 만들 수도 있습니다. Azure CLI를 사용하여 명령줄이나 스크립트에서 여러 가지 일반적인 관리 작업을 실행할 수 있습니다. 

### <a name="get-ip-address"></a>IP 주소 가져오기

이 명령은 가상 컴퓨터의 개인 및 공용 IP 주소를 반환합니다.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myVM
```

### <a name="resize-virtual-machine"></a>가상 컴퓨터 크기 조정

Azure Virtual Machine의 크기를 조정하려면 선택한 Azure 지역에서 사용할 수 있는 크기의 이름을 알고 있어야 합니다. 이러한 크기는 [az vm list-sizes](https://docs.microsoft.com/cli/azure/vm#list-sizes) 명령을 사용하여 확인할 수 있습니다.

```azurecli
az vm list-sizes --location westeurope --output table
```

가상 컴퓨터 크기는 [az vm resize](https://docs.microsoft.com/cli/azure/vm#resize) 명령을 사용하여 조정할 수 있습니다. 

```azurecli
az vm resize -g myResourceGroup -n myVM --size Standard_F4s
```

### <a name="stop-virtual-machine"></a>가상 컴퓨터 중지

```azurecli
az vm stop --resource-group myResourceGroup --name myVM
```

### <a name="start-virtual-machine"></a>가상 컴퓨터 시작

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

### <a name="delete-resource-group"></a>리소스 그룹 삭제

리소스 그룹을 삭제하면 그 안에 포함된 리소스도 모두 삭제됩니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

자습서 - [부하 분산된 가상 컴퓨터 만들기](./virtual-machines-linux-tutorial-load-balance-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

샘플 - [Azure CLI 샘플 스크립트](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
