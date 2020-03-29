---
title: 클라우드-init을 사용하여 Linux VM에서 스왑 파티션을 구성합니다.
description: Azure CLI를 사용하여 만드는 동안 Linux VM에서 스왑 파티션을 구성하기 위해 클라우드 init을 사용하는 방법
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969196"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>클라우드-init을 사용하여 Linux VM에서 스왑 파티션을 구성합니다.
이 문서에서는 클라우드 [init을](https://cloudinit.readthedocs.io) 사용하여 다양한 Linux 배포판에서 스왑 파티션을 구성하는 방법을 보여 주며 있습니다. 스왑 파티션은 전통적으로 필요한 배포판에 따라 LINUX 에이전트(WALA)에 의해 구성되었습니다.  이 문서에서는 클라우드 init을 사용하여 프로비저닝 하는 동안 필요에 따라 스왑 파티션을 빌드하는 프로세스를 간략하게 설명합니다.  Azure 및 지원되는 Linux 배포판에서 클라우드 init이 기본적으로 작동하는 방식에 대한 자세한 내용은 [클라우드 init 개요를](using-cloud-init.md) 참조하세요.

## <a name="create-swap-partition-for-ubuntu-based-images"></a>우분투 기반 이미지에 대 한 스왑 파티션 만들기
기본적으로 Azure에서 우분투 갤러리 이미지는 스왑 파티션을 만들지 않습니다. 클라우드-init을 사용하여 VM 프로비저닝 시간 동안 스왑 파티션 구성을 사용하려면 우분투 위키의 [AzureSwapPartitions 문서를](https://wiki.ubuntu.com/AzureSwapPartitions) 참조하십시오.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Red Hat 및 CentOS 기반 이미지에 대한 스왑 파티션 만들기

*cloud_init_swappart.txt라는* 현재 셸에 파일을 만들고 다음 구성을 붙여넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud_init_swappart.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

이 이미지를 배포하기 전에 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *동쪽* 위치에 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_swappart.txt`로 cloud-init 파일을 지정합니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>스왑 파티션이 만들어졌는지 확인
이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 **publicIpAddress**를 다음과 같이 입력합니다.

```bash
ssh <publicIpAddress>
```

SSH'ed를 vm에 넣은 후 스왑 파티션이 만들어졌는지 확인하십시오.

```bash
swapon -s
```

이 명령의 출력은 다음과 같아야 합니다.

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> 스왑 파티션이 구성된 기존 Azure 이미지가 있고 새 이미지의 스왑 파티션 구성을 변경하려면 기존 스왑 파티션을 제거해야 합니다. 자세한 내용은 'cloud-init를 사용하여 프로비전할 이미지 사용자 지정' 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
