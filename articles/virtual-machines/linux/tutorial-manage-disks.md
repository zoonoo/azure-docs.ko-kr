---
title: 자습서 - Azure CLI를 사용하여 Azure 디스크 관리 | Microsoft Docs
description: 이 자습서에서는 Azure CLI를 사용하여 가상 머신을 위한 Azure 디스크를 만들고 관리하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: aff00978178f63b7324168e9aca765b77decf2b4
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922905"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>자습서 - Azure CLI를 사용하여 Azure 디스크 관리

Azure VM(가상 머신)은 디스크를 사용하여 운영 체제, 애플리케이션 및 데이터를 저장합니다. VM을 만들 때 예상되는 워크로드에 적합한 디스크 크기와 구성을 선택하는 것이 중요합니다. 이 자습서에서는 VM 디스크를 배포하고 관리하는 방법을 보여줍니다. 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비
> * 디스크 크기 조정
> * 디스크 스냅샷


## <a name="default-azure-disks"></a>기본 Azure 디스크

Azure Virtual Machine을 만들면 두 개의 디스크가 자동으로 가상 머신에 연결됩니다.

**운영 체제 디스크** - 운영 체제 디스크는 최대 2TB까지 크기를 조정할 수 있고 VM 운영 체제를 호스트합니다. OS 디스크는 기본적으로 */dev/sda*로 레이블이 지정됩니다. OS 디스크의 디스크 캐싱 구성은 OS 성능에 맞게 최적화됩니다. 이 구성으로 인해 OS 디스크는 애플리케이션 또는 데이터에 사용되지 **않아야 합니다**. 애플리케이션 및 데이터는 데이터 디스크를 사용하며 여기에 대해서는 이 자습서의 뒷부분에서 자세히 설명합니다.

**임시 디스크** - 임시 디스크는 VM과 같은 Azure 호스트에 있는 반도체 드라이브를 사용합니다. 임시 디스크는 성능이 높고 임시 데이터 처리 등의 작업에 사용할 수 있습니다. 그러나 VM이 새 호스트로 이동되면 임시 디스크에 저장된 모든 데이터는 제거됩니다. 임시 디스크의 크기는 VM 크기에 따라 결정됩니다. 임시 디스크는 */dev/sdb*로 레이블이 지정되고 탑재 지점은 */mnt*입니다.

## <a name="azure-data-disks"></a>Azure 데이터 디스크

애플리케이션을 설치하고 데이터를 저장하기 위해 추가 데이터 디스크를 추가할 수 있습니다. 데이터 디스크는 지속형 및 반응형 데이터 저장소가 필요한 상황에 사용해야 합니다. 가상 컴퓨터의 크기에 따라 VM에 연결할 수 있는 데이터 디스크 수가 결정됩니다. 각 VM vCPU에 대해 네 개의 데이터 디스크를 연결할 수 있습니다.

## <a name="vm-disk-types"></a>VM 디스크 유형

Azure는 표준과 프리미엄의 두 가지 디스크를 제공합니다.

### <a name="standard-disk"></a>표준 디스크

Standard Storage는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 스토리지를 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.

### <a name="premium-disk"></a>프리미엄 디스크

프리미엄 디스크는 SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 프로덕션 워크로드를 실행하는 VM에 완벽한 디스크입니다. Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 FS 시리즈 VM을 지원합니다. 디스크 크기를 선택하면 값이 다음 형식으로 반올림됩니다. 예를 들어 디스크 크기가 128GB 미만인 경우 디스크 유형은 P10입니다. 디스크 크기가 129GB에서 512GB 사이이면 크기는 P20입니다. 512GB 초과이면 크기는 P30입니다.

### <a name="premium-disk-performance"></a>프리미엄 디스크 성능
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

위의 표에 디스크당 최대 IOPS가 나와 있지만 여러 데이터 디스크를 스트라이프하여 더 높은 수준의 성능을 구현할 수 있습니다. 예를 들어 Standard_GS5 VM은 최대 80,000 IOPS를 얻을 수 있습니다. VM당 최대 IOPS에 대한 자세한 내용은 [Linux VM 크기](sizes.md)를 참조하세요.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 문서의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택합니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-and-attach-disks"></a>디스크 만들기 및 연결

VM을 만들 때 또는 기존 VM에 데이터 디스크를 만들고 연결할 수 있습니다.

### <a name="attach-disk-at-vm-creation"></a>VM을 만들 때 디스크 연결

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

[az vm create](/cli/azure/vm#az-vm-create) 명령을 사용하여 VM을 만듭니다. 다음 예제에서는 *myVM*이라는 VM을 만들고, *azureuser*라는 사용자 계정을 추가하고, 아직 SSH 키가 없으면 새로 생성합니다. `--datadisk-sizes-gb` 인수를 사용하여 가상 머신에 추가 디스크를 만들고 연결하도록 지정할 수 있습니다. 둘 이상의 디스크를 만들고 연결하려면 공백으로 구분된 디스크 크기 값 목록을 사용합니다. 다음 예제에서는 128GB 데이터 디스크 두 개가 있는 VM을 만듭니다. 디스크 크기가 128GB이므로 이러한 디스크는 모두 디스크당 최대 500 IOPS를 제공하는 P10으로 구성됩니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>기존 VM에 디스크 연결

기존 가상 머신에 새 디스크를 만들고 연결하려면 [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) 명령을 사용합니다. 다음 예제에서는 크기가 128GB인 프리미엄 디스크를 만들고 마지막 단계에서 만든 VM에 연결합니다.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>데이터 디스크 준비

디스크가 가상 머신에 연결된 후 디스크를 사용하도록 운영 체제를 구성해야 합니다. 다음 예제에는 디스크를 수동으로 구성하는 방법을 보여 줍니다. 이 프로세스는 cloud-init를 사용하여 자동화할 수도 있으며 여기에 대해서는 [이후의 자습서](./tutorial-automate-vm-deployment.md)에서 다룹니다.


가상 머신과 SSH 연결 만들기 예제 IP 주소를 가상 머신의 공용 IP로 바꿉니다.

```azurecli-interactive
ssh 10.101.10.10
```

`fdisk`를 사용하여 디스크를 분할합니다.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

`mkfs` 명령을 사용하여 파티션에 파일 시스템을 씁니다.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

운영 체제에서 액세스할 수 있도록 새 디스크를 탑재합니다.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

이제 *datadrive* 탑재 지점을 통해 디스크에 액세스할 수 있으며, `df -h` 명령을 실행하여 확인할 수 있습니다.

```bash
df -h
```

*/datadrive*에 탑재된 새 드라이브가 출력에 표시됩니다.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

다시 부팅 후 드라이브가 다시 탑재되도록 하려면 */etc/fstab* 파일에 추가해야 합니다. 이렇게 하려면 `blkid` 유틸리티를 사용하여 디스크의 UUID를 가져옵니다.

```bash
sudo -i blkid
```

출력에 드라이브의 UUID가 표시됩니다(이 경우 `/dev/sdc1`).

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

다음과 유사한 줄을 */etc/fstab* 파일에 추가합니다.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

이제 디스크가 구성되었으니 SSH 세션을 닫습니다.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>디스크 스냅샷 만들기

디스크 스냅샷을 생성하면 Azure에서는 디스크의 읽기 전용, 지정 시간 복사본을 만듭니다. Azure VM 스냅샷은 구성을 변경하기 전에 VM의 상태를 신속하게 저장하는 데 유용합니다. 문제 또는 오류 발생 시 스냅샷을 사용하여 VM을 복원할 수 있습니다. VM에 둘 이상의 디스크가 있는 경우 각 디스크의 스냅샷은 다른 디스크의 스냅샷과 독립적으로 생성됩니다. 애플리케이션 일치 백업을 만들려면 디스크 스냅샷을 만들기 전에 VM을 중지하는 것이 좋습니다. 또는 [Azure Backup 서비스](/azure/backup/)를 사용하면 VM을 실행하는 동안 자동화된 백업을 수행할 수 있습니다.

### <a name="create-snapshot"></a>스냅샷 만들기

가상 머신 디스크 스냅샷을 만들려면 디스크의 ID 또는 이름이 필요합니다. [az vm show](/cli/azure/vm#az-vm-show) 명령을 사용하여 디스크 ID를 가져옵니다. 이 예제에서는 디스크 ID가 변수에 저장되고 이후 단계에서 사용될 수 있습니다.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

이제 가상 머신의 ID를 알고 있으므로 다음 명령을 실행하여 디스크 스냅샷을 만듭니다.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>스냅샷에서 디스크 만들기

그런 다음, 스냅샷을 디스크로 복원하여 가상 머신을 다시 만드는 데 사용할 수 있습니다.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>스냅샷에서 가상 머신 복원

가상 머신 복구를 보여 주기 위해 기존 가상 머신을 삭제합니다.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

스냅샷 디스크에서 새 가상 머신을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>데이터 디스크 다시 연결

모든 데이터 디스크를 가상 머신에 다시 연결해야 합니다.

먼저 [az disk list](/cli/azure/disk#az-disk-list) 명령을 사용하여 데이터 디스크 이름을 찾습니다. 이 예제에서는 *datadisk*라는 변수에 디스크의 이름을 추가합니다. 이 변수는 다음 단계에서 사용됩니다.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

[az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) 명령을 사용하여 디스크를 연결합니다.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --disk $datadisk
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 VM 디스크 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비
> * 디스크 크기 조정
> * 디스크 스냅샷

VM 구성 자동화에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM 구성 자동화](./tutorial-automate-vm-deployment.md)
