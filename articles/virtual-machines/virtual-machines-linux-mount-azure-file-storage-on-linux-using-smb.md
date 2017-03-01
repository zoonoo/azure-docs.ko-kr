---
title: "SMB를 사용하여 Linux VM에 Azure File Storage 탑재 | Microsoft Docs"
description: "Azure CLI 2.0(미리 보기)에서 SMB를 사용하여 Linux VM에 Azure File Storage를 탑재하는 방법"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)에서 SMB를 사용하여 Linux VM에 Azure File Storage 탑재

이 문서에서는 SMB 탑재를 사용하여 Linux VM에서 Azure File Storage 서비스를 활용하는 방법을 보여 줍니다. Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다.  요구 사항은 다음과 같습니다.

- [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 공용 및 개인 키 파일](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](#quick-commands) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)


## <a name="quick-commands"></a>빠른 명령

태스크를 빠르게 완료해야 하는 경우 다음 섹션에서 필요한 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) 문서 끝까지 참조하세요.

필수 구성 요소: 리소스 그룹, VNet, SSH 인바운드가 있는 NSG, 서브넷, Azure Storage 계정, Storage 계정 키, Azure File Storage 공유 및 Linux VM 모든 예제를 고유한 값으로 바꿉니다.

다음과 같이 로컬 탑재를 위한 디렉터리를 만듭니다.

```bash
mkdir -p /mnt/mymountpoint
```

다음과 같이 Azure File Storage SMB 공유를 탑재 지점에 탑재합니다.

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

다시 부팅한 후 탑재를 유지하려면 다음과 같이 `/etc/fstab`에 줄을 추가합니다.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>자세한 연습

Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. File Storage 최신 릴리스를 사용하면 SMB 3.0을 지원하는 OS에서 파일 공유를 탑재할 수도 있습니다. Linux에서 SMB 탑재를 사용하면 SLA에서 지원되는 강력한 영구 보관 저장소 위치에 쉽게 백업할 수 있습니다.  

VM의 파일을 Azure File Storage에 호스트된 SMB 탑재 지점으로 이동하는 것은 로그를 디버그하는 유용한 방법입니다. 동일한 SMB 공유가 Mac, Linux 또는 Windows 워크스테이션에 로컬로 탑재될 수 있기 때문입니다. SMB는 SMB 프로토콜이 이와 같은 과도한 로깅 의무에 맞게 작성되지는 않았으므로 Linux 또는 응용 프로그램 로그를 실시간으로 스트리밍하기 위한 최선의 솔루션은 아닐 수 있습니다. Linux 및 응용 프로그램 로그 출력을 수집하려는 경우 Fluentd와 같은 전용 통합 로깅 계층 도구가 SMB보다 더 적합할 것입니다.

이 자세한 연습 과정에서는 먼저 Azure File Storage 공유를 만드는 데 필요한 필수 구성 요소 만든 후 Linux VM에서 SMB를 통해 탑재합니다.

먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만들어서 파일 공유를 저장합니다. 다음 예제에서는 `West US` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>Azure Storage 계정 만들기
다음으로 [az storage account create](/cli/azure/storage/account#create)를 사용하여 저장소 계정을 만들어 실제 파일을 저장합니다. 다음 예제에서는 `Standard_LRS` 저장소 SKU를 사용하여 `mystorageaccount`라는 저장소 계정을 만듭니다.

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>저장소 계정 키 표시

Azure Storage 계정 키는 저장소 계정이 만들어질 때 쌍으로 생성됩니다. 서비스 중단 없이 키를 순환할 수 있도록 저장소 계정 키는 쌍으로 만들어집니다. 키를 쌍의 두 번째 키로 순환하면 새 키 쌍이 만들어집니다. 새 저장소 계정 키는 항상 쌍으로 만들어지므로 항상 사용하지 않은 저장소 키를 순환할 준비가 되어 있습니다.

[az storage account keys list](/cli/azure/storage/account/keys#list)를 사용하여 저장소 계정 키를 봅니다. 다음 예제에서는 `mystorageaccount`라는 저장소 계정에 저장소 계정 키를 나열합니다.

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount
```

단일 키를 추출하려면 `--query` 플래그를 사용합니다. 다음 예제에서는 첫 번째 키(`[0]`)를 추출합니다.

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query '[0].{Key:value}' --output tsv
```


## <a name="create-the-azure-file-storage-share"></a>Azure File Storage 공유 만들기

[az storage share create](/cli/azure/storage/share#create)를 사용하여 SMB 공유를 포함하는 File Storage 공유를 만듭니다. 할당량은 항상 GB(기가바이트) 단위입니다. 앞의 **az storage account keys list** 명령에서 키 중 하나에 전달합니다. 다음 예제에서는 `10`GB 할당량을 가진 `mystorageshare`라는 공유를 만듭니다.

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>탑재 지점 디렉터리를 만듭니다.

Linux 파일 시스템에 SMB 공유를 탑재할 로컬 디렉터리가 필요합니다. 로컬 탑재 디렉터리에서 쓰거나 읽은 모든 내용은 Azure File Storage에 호스트된 SMB 공유로 전달됩니다. 다음 예제는 `/mnt/mymountdirectory`에서 로컬 디렉터리를 만듭니다.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>SMB 공유 탑재
다음과 같이 만들 로컬 디렉터리에 SMB 공유를 탑재합니다. 다음과 같이 고유한 저장소 계정 사용자 이름 및 탑재 자격 증명에 대한 저장소 계정 키를 제공합니다.

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>재부팅을 통해 SMB 탑재 유지

Linux VM를 다시 부팅하면 탑재된 SMB 공유가 종료하는 동안 분리됩니다. 부팅 시 SMB 공유를 다시 탑재하려면 Linux `/etc/fstab`에 줄을 추가합니다. Linux는 `fstab` 파일을 사용하여 부팅하는 동안 탑재해야 하는 파일 시스템을 나열합니다. SMB 공유를 추가하면 Azure File Storage 공유가 Linux VM용으로 영구히 탑재된 파일 시스템이 됩니다. `cloud-init`를 사용하여 Azure File Storage SMB 공유를 새 VM에 추가할 수 있습니다.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>다음 단계

- [cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM에 디스크 추가](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

