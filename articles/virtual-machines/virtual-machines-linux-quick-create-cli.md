---
title: "Azure CLI 2.0(미리 보기)을 사용하여 Linux VM 만들기 | Microsoft Azure"
description: "Azure CLI 2.0(미리 보기)을 사용하여 Linux VM 만들기"
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Azure CLI 2.0 미리 보기(az.py)를 사용하여 Linux VM 만들기
이 문서에서는 Azure CLI 2.0(미리 보기)을 사용하는 [az vm create](/cli/azure/vm#create) 명령을 사용하여 Azure에서 Linux 가상 컴퓨터(VM)를 신속하게 배포하는 방법을 보여 줍니다. 

> [!NOTE] 
> Azure CLI 2.0 미리 보기는 차세대 다중 플랫폼 CLI입니다. [사용해 보세요!](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> 이 문서의 나머지는 기존 Azure CLI를 사용합니다. CLI 2.0 미리 보기가 아니라 기존 Azure CLI 1.0을 사용하여 VM을 만들려면 [Azure CLI를 사용하여 VM 만들기](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

VM을 만들려면 다음이 필요합니다. 

* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))
* [Azure CLI v. 2.0(미리 보기)](/cli/azure/install-az-cli2)가 설치됨
* Azure 계정에 로그인([az login](/cli/azure/#login) 입력)

([Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 Linux VM을 신속히 배포할 수도 있습니다.)

다음 예제에서는 Debian VM을 배포하고 SSH(Secure Shell) 키를 연결하는 방법을 보여줍니다. (사용자의 인수는 다를 수 있습니다. 다른 이미지를 원하는 경우 [검색할 수 있습니다](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

먼저 [az group create](/cli/azure/group#create)를 입력하여 배포된 모든 리소스를 포함하는 리소스 그룹을 만듭니다.

```azurecli
az group create -n myResourceGroup -l westus
```

출력은 다음과 같이 표시됩니다(원하는 경우 다른 `--output` 옵션을 선택할 수 있음).

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>최신 Debian 이미지를 사용하여 VM 만들기

이제 VM 및 해당 환경을 만들 수 있습니다. `----public-ip-address-dns-name` 값을 고유한 값으로 대체합니다. 아래 값은 이미 사용 중일 수 있습니다.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


출력은 다음과 같이 표시됩니다. VM으로 **ssh**할 `publicIpAddress` 또는 `fqdn` 값을 기록합니다.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

출력에 나열된 공용 IP 주소를 사용하여 VM에 로그인합니다. 나열된 정규화된 도메인 이름(FQDN)을 사용할 수도 있습니다.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

선택한 배포에 따라 다음 출력과 같이 표시될 수 있습니다.

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>다음 단계
`az vm create` 명령은 Bash 셸에 로그인하고 작업할 수 있도록 VM을 신속하게 배포하는 방법입니다. 그러나 `az vm create` 를 사용하면 광범위한 제어가 불가능하며 더 복잡한 환경을 만들 수도 없습니다.  이러한 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 배포할 수 있습니다.

* [Azure Resource Manager 템플릿을 사용하여 특정 배포 만들기](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

또한 [여러 명령과 함께 `docker-machine` Azure 드라이버를 사용하여 Linux VM을 신속하게 Docker host로 만들](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 수 있으며 Java를 사용하는 경우 [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) 메서드를 시도합니다.




<!--HONumber=Jan17_HO1-->


