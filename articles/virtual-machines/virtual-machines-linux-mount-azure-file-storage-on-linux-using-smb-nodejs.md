---
title: "Azure CLI 1.0에서 SMB를 사용하여 Linux VM에 Azure File Storage 탑재 | Microsoft Docs"
description: "SMB를 사용하여 Linux VM에 Azure File Storage를 탑재하는 방법입니다."
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
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 4d547f854df5d4801779e37ac5f22fb9551bd6bb
ms.openlocfilehash: 73151dba33e2c05a66176ef6b895339bce167128
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-10"></a>Azure CLI 1.0에서 SMB를 사용하여 Linux VM에 Azure File Storage 탑재

이 문서에서는 SMB 탑재를 사용하여 Linux VM에서 Azure File Storage 서비스를 활용하는 방법을 보여 줍니다.  Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다.  요구 사항은 다음과 같습니다.

- [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 공용 및 개인 키 파일](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0(미리 보기)](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="quick-commands"></a>빠른 명령

태스크를 빠르게 완료해야 하는 경우 다음 섹션에서 필요한 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) 문서 끝까지 참조하세요.

필수 구성 요소: 리소스 그룹, VNet, SSH 인바운드가 있는 NSG, 서브넷, Azure Storage 계정, Storage 계정 키, Azure File Storage 공유 및 Linux VM 모든 예제를 고유한 설정으로 바꿉니다.

로컬 탑재를 위한 디렉터리 만들기

```bash
mkdir -p /mnt/mymountpoint
```

Azure File Storage SMB 공유를 탑재 지점에 탑재

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

다시 부팅한 후 탑재를 유지하려면 `/etc/fstab`에 줄을 추가합니다.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>자세한 연습

Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다.  File Storage 최신 릴리스를 사용하면 SMB 3.0을 지원하는 OS에서 파일 공유를 탑재할 수도 있습니다.  Linux에서 SMB 탑재를 사용하면 SLA에서 지원되는 강력한 영구 보관 저장소 위치에 쉽게 백업할 수 있습니다.  

VM의 파일을 Azure File Storage에 호스트된 SMB 탑재 지점으로 이동하는 것은 로그를 디버그하는 유용한 방법입니다. 동일한 SMB 공유가 Mac, Linux 또는 Windows 워크스테이션에 로컬로 탑재될 수 있기 때문입니다.  SMB는 SMB 프로토콜이 이와 같은 과도한 로깅 의무에 맞게 작성되지는 않았으므로 Linux 또는 응용 프로그램 로그를 실시간으로 스트리밍하기 위한 최선의 솔루션은 아닐 수 있습니다.  Linux 및 응용 프로그램 로그 출력을 수집하려는 경우 Fluentd와 같은 전용 통합 로깅 계층 도구가 SMB보다 더 적합할 것입니다.

이 자세한 연습 과정에서는 먼저 Azure File Storage 공유를 만드는 데 필요한 필수 구성 요소 만든 후 Linux VM에서 SMB를 통해 탑재합니다.

## <a name="create-the-azure-storage-account"></a>Azure Storage 계정 만들기

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>저장소 계정 키 표시

Azure Storage 계정 키는 저장소 계정이 만들어질 때 쌍으로 생성됩니다.  서비스 중단 없이 키를 순환할 수 있도록 저장소 계정 키는 쌍으로 만들어집니다.  키를 쌍의 두 번째 키로 순환하면 새 키 쌍이 만들어집니다.  새 저장소 계정 키는 항상 쌍으로 만들어지므로 항상 사용하지 않은 저장소 키를 순환할 준비가 되어 있습니다.

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Azure File Storage 공유 만들기

SMB 공유를 포함하는 File Storage 공유를 만듭니다.  할당량은 항상 GB(기가바이트) 단위입니다.

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>탑재 지점 디렉터리를 만듭니다.

Linux 파일 시스템에 SMB 공유를 탑재할 로컬 디렉터리가 필요합니다.  로컬 탑재 디렉터리에서 쓰거나 읽은 모든 내용은 Azure File Storage에 호스트된 SMB 공유로 전달됩니다.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>SMB 공유 탑재

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>재부팅을 통해 SMB 탑재 유지

Linux VM를 다시 부팅하면 탑재된 SMB 공유가 종료하는 동안 분리됩니다.  부팅 시 SMB 공유를 다시 탑재하려면 Linux에 `/etc/fstab` 줄을 추가해야 합니다.  Linux는 `fstab` 파일을 사용하여 부팅하는 동안 탑재해야 하는 파일 시스템을 나열합니다.  SMB 공유를 추가하면 Azure File Storage 공유가 Linux VM용으로 영구히 탑재된 파일 시스템이 됩니다.  `cloud-init`를 사용하여 Azure File Storage SMB 공유를 새 VM에 추가할 수 있습니다.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>다음 단계

- [cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM에 디스크 추가](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

