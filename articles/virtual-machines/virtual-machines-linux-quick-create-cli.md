---
title: "Azure CLI 2.0을 사용하여 Linux VM 만들기 | Microsoft Azure"
description: "Azure CLI 2.0을 사용하여 Linux VM을 만듭니다."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Linux VM 만들기
이 문서에서는 Managed Disks 혹은 기본 저장소 계정의 디스크를 사용하는 Azure CLI 2.0에서 [az vm create](/cli/azure/vm#create) 명령을 사용하여 Azure에서 Linux 가상 컴퓨터(VM)를 신속하게 배포하는 방법을 보여 줍니다. [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 이러한 단계를 수행할 수도 있습니다.

VM을 만들려면 다음이 필요합니다. 

* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI 2.0](/cli/azure/install-az-cli2) 설치
* Azure 계정에 로그인([az login](/cli/azure/#login) 입력)

([Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 Linux VM을 배포할 수도 있습니다.)

다음 예제는 Debian VM을 배포하고 SSH(보안 셸) 키를 사용하여 Debian VM에 연결하는 방법을 보여줍니다. 인수는 다를 수 있습니다. 다른 이미지를 원하는 경우 [이미지를 검색할 수 있습니다](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Managed Disks 사용

Azure Managed Disks를 사용하려면 이것을 지원하는 지역을 사용해야 합니다. 먼저 [az group create](/cli/azure/group#create)를 입력하여 배포된 모든 리소스를 포함하는 리소스 그룹을 만듭니다.

```azurecli
 az group create -n myResourceGroup -l westus
```

출력은 다음과 같습니다(다른 형식을 보려면 다른 `--output` 옵션을 지정할 수 있음).

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>VM 만들기 
이제 VM 및 해당 환경을 만들 수 있습니다. `--public-ip-address-dns-name` 값을 고유한 값으로 대체합니다. 아래 값은 이미 사용 중일 수 있습니다.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


출력은 다음과 같이 표시됩니다. VM으로 **ssh**할 `publicIpAddress` 또는 `fqdn` 값을 기록합니다.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

출력에 나열된 FQDN(정규화된 도메인 이름) 또는 공용 IP 주소를 사용하여 VM에 로그인합니다.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

선택한 배포에 따라 다음 출력과 같이 표시될 수 있습니다.

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Managed Disks를 사용하여 새 VM으로 할 수 있는 작업을 보려면 [다음 단계](#next-steps)를 참조하세요.

## <a name="using-unmanaged-disks"></a>관리되지 않는 디스크 사용 

관리되지 않는 저장소 디스크를 사용하는 VM에는 관리되지 않는 저장소 계정이 있습니다. 먼저 [az group create](/cli/azure/group#create)를 입력하여 배포된 모든 리소스를 포함하는 리소스 그룹을 만듭니다.

```azurecli
az group create --name nativedisks --location westus
```

출력은 다음과 같이 표시됩니다(원하는 경우 다른 `--output` 옵션을 선택할 수 있음).

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>VM 만들기 

이제 VM 및 해당 환경을 만들 수 있습니다. `--use-unmanaged-disk` 플래그를 사용하여 관리되지 않는 디스크와 VM을 만듭니다. 관리되지 않는 저장소 계정도 만들어집니다. `--public-ip-address-dns-name` 값을 고유한 값으로 대체합니다. 아래 값은 이미 사용 중일 수 있습니다.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

출력은 다음과 같이 표시됩니다. VM으로 **ssh**할 `publicIpAddress` 또는 `fqdn` 값을 기록합니다.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

위의 출력에 나열되어 있는 공용 IP 주소 또는 FQDN(정규화된 도메인 이름)을 사용하여 VM에 로그인합니다.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

선택한 배포에 따라 다음 출력과 같이 표시될 수 있습니다.

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>다음 단계
`az vm create` 명령은 Bash 셸에 로그인하고 작업할 수 있도록 VM을 신속하게 배포하는 방법입니다. 그러나 `az vm create` 를 사용하면 광범위한 제어가 불가능하며 더 복잡한 환경을 만들 수도 없습니다.  이러한 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 배포할 수 있습니다.

* [Azure Resource Manager 템플릿을 사용하여 특정 배포 만들기](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

또한 [여러 명령과 함께 `docker-machine` Azure 드라이버를 사용하여 Linux VM을 신속하게 Docker host로 만들](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 수 있으며 Java를 사용하는 경우 [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) 메서드를 시도합니다.


