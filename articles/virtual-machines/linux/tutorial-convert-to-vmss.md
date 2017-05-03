---
title: "Azure VM을 확장 집합으로 변환 | Microsoft Docs"
description: "Azure CLI를 사용하여 Linux Azure 가상 컴퓨터 확장 집합을 만들고 배포합니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>기존 Azure 가상 컴퓨터를 확장 집합으로 변환

이 자습서는 Azure CLI 2.0을 사용하여 가상 컴퓨터를 가상 컴퓨터 확장 집합으로 변환하는 방법을 보여줍니다. 확장 집합에서 가상 컴퓨터의 구성을 자동화하는 방법도 알아봅니다. Azure CLI 2.0을 설치하는 방법에 대한 자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli.md)을 참조하세요. 규모 집합에 대한 자세한 내용은 [가상 컴퓨터 규모 집합](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 참조하세요.

## <a name="step-1---deprovision-the-vm"></a>1단계 - VM 프로비전 해제

SSH를 사용하여 VM에 연결합니다.

Azure VM 에이전트를 사용하여 VM 프로비전을 해제하여 파일과 데이터를 삭제합니다. 프로비전 해제에 대한 세부적인 개요는 [Linux 가상 컴퓨터 캡처](capture-image.md)를 참조하세요.

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>2단계 - VM 이미지 캡처

캡처에 대한 세부적인 개요는 [Linux 가상 컴퓨터 캡처](capture-image.md)를 참조하세요.

[az vm deallocate](/cli/azure/vm#deallocate)로 VM의 할당을 취소합니다.

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm generalize](/cli/azure/vm#generalize)로 VM을 일반화합니다.

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

[az image create](/cli/azure/image#create)로 VM 리소스에서 이미지를 만듭니다.

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>3단계 - 확장 집합 만들기

이미지의 **id**를 구합니다.

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

[az vm create](/cli/azure/vmss#create)를 사용하여 이미지 리소스에서 VM을 만듭니다.

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

이 명령은 10GB 데이터 디스크도 연결합니다. 선택한 VM 크기(**Standard_DS1_v2**가 사용됨)에 따라 허용되는 데이터 디스크 수가 다릅니다. 자세한 내용은 [가상 컴퓨터 크기](sizes.md)를 검토하세요.

확장 집합이 완료되면 확장 집합에 연결합니다. [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info)를 사용하여 SSH를 위한 인스턴스의 IP 주소 목록을 가져옵니다.

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

이제 가상 컴퓨터 인스턴스에 연결하여 데이터 디스크를 초기화할 수 있습니다.

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>4단계 - 데이터 디스크 초기화

가상 컴퓨터에 연결되어 있는 동안 `fdisk`를 사용하여 디스크를 파티션합니다.

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

`mkfs` 명령을 사용하여 파티션에 파일 시스템을 씁니다.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

운영 체제에서 액세스할 수 있도록 새 디스크를 탑재합니다.

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

이제 데이터 드라이브 탑재 지점을 통해 디스크에 액세스할 수 있으며 `ls /datadrive/`를 사용하여 확인할 수 있습니다.

SSH 세션을 종료합니다.


## <a name="step-5---configure-firewall"></a>5단계 - 방화벽 구성

방화벽을 통해 확장 집합이 호스트하는 웹 서버에 구멍을 냅니다. 확장 집합이 생성될 때 부하 분산 장치도 만들어지며 개별적인 가상 컴퓨터에 **SSH**를 사용합니다. 포트를 열려면 두 가지 정보가 필요하며 Azure CLI를 사용하여 해당 정보를 구할 수 있습니다.

* **프런트 엔드 IP 주소 풀**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **백 엔드 IP 주소 풀**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

이러한 두 가지 이름을 사용하여 포트 **80**을 열 수 있습니다.

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>6단계 – 구성 자동화

데이터 디스크는 각각의 가상 컴퓨터 인스턴스에서 구성되어야 합니다. **CustomScript** 확장을 사용하여 가상 컴퓨터 구성을 자동화할 수 있습니다.

먼저 디스크 포맷 명령을 포함하는 *.sh* 스크립트를 만듭니다.

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

다음으로 이 스크립트 파일을 **CustomScript** 확장이 액세스할 수 있는 위치에 업로드합니다. 복사본은 [여기](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4)에 있습니다.

**settings.json**이라는 이름의 로컬 파일을 만들고 다음 JSON 블록을 파일에 넣습니다. `flieUris` 속성은 스크립트 파일을 업로드한 위치로 설정해야 합니다.

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

방금 만든 **settings.json** 파일을 참조하는 **CustomScript** 확장을 사용하여 확장 집합에 이 명령을 배포합니다.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

이 확장은 현재의 모든 인스턴스와 나중에 크기 조정에 의해 생성되는 모든 인스턴스에서 자동으로 실행됩니다.

## <a name="step-7---configure-autoscale-rules"></a>7단계 - 자동 크기 조정 규칙 구성

현재 자동 크기 조정 규칙은 Azure CLI에서 설정할 수 없습니다. 자동 크기 조정을 구성하려면 [Azure Portal](https://portal.azure.com)을 사용하세요.

## <a name="step-8---management-tasks"></a>8단계 - 관리 작업

확장 집합의 수명 주기 내내 하나 이상의 관리 작업을 실행해야 합니다. 또한 다양한 수명 주기 작업을 자동화하는 스크립트를 만들어야 하는 경우도 있습니다. Azure CLI는 이러한 작업을 수행할 수 있는 빠른 방법을 제공합니다. 다음은 몇 가지 일반적인 작업입니다.

### <a name="get-connection-info"></a>연결 정보 가져오기

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>인스턴스 수 설정(수동 크기 조정)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>리소스 그룹 삭제

리소스 그룹을 삭제하면 그 안에 포함된 리소스도 모두 삭제됩니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계
이 자습서에 소개된 일부 가상 컴퓨터 확장 집합 기능에 대한 자세한 내용은 다음 정보를 참조하세요.

- [Azure Virtual Machine Scale Sets 개요](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure Load Balancer개요](../../load-balancer/load-balancer-overview.md)
- [네트워크 보안 그룹을 사용하여 네트워크 트래픽 흐름 제어](../../virtual-network/virtual-networks-nsg.md)
