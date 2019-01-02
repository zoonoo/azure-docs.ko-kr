---
title: SMB를 사용하여 Linux VM에 Azure File Storage 탑재 | Microsoft Docs
description: Azure CLI에서 SMB를 사용하여 Linux VM에 Azure File 저장소를 탑재하는 방법
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: fde8892f7a32d7b5405eef6661bbf29098325178
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958681"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>SMB를 사용하여 Linux VM에 Azure File Storage 탑재

이 문서에서는 Azure CLI에서 SMB 탑재를 사용하여 Linux VM에서 Azure File 저장소 서비스를 사용하는 방법을 보여 줍니다. Azure 파일 저장소는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. 

File Storage는 표준 SMB 프로토콜을 사용하는 클라우드에서 파일 공유를 제공합니다. SMB 3.0을 지원하는 모든 OS에서 파일 공유를 탑재할 수 있습니다. Linux에서 SMB 탑재를 사용하면 SLA에서 지원되는 강력한 영구 보관 저장소 위치에 쉽게 백업할 수 있습니다.

VM에서 File Storage에서 호스팅되는 SMB 탑재로 파일을 이동하는 것은 로그를 디버깅하는 유용한 방법입니다. 동일한 SMB 공유를 Mac, Linux 또는 Windows 워크스테이션에 로컬로 탑재할 수 있습니다. SMB 프로토콜이 이러한 과도한 로깅 작업을 다루도록 빌드되지 않았기 때문에 SMB는 Linux 또는 애플리케이션 로그를 실시간으로 스트리밍하기 위한 최선의 솔루션은 아닐 수 있습니다. Linux 및 애플리케이션 로그 출력을 수집하려는 경우 Fluentd와 같은 전용 통합 로깅 계층 도구가 SMB보다 더 적합할 것입니다.

이 가이드에서는 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 찾으려면 **az --version**을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

*미국 동부* 위치에 *myResourceGroup*이라는 이름의 리소스 그룹을 만듭니다.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기

[az storage account create](/cli/azure/storage/account#create)를 사용하여 만든 리소스 그룹 내에 새 저장소 계정을 만듭니다. 다음 예제에서는 *mySTORAGEACCT<random number>* 라는 저장소 계정을 만들고, 해당 저장소 계정의 이름을 **STORAGEACCT** 변수에 배치합니다. 저장소 계정 이름은 고유해야 하며, 사용하는 `$RANDOM` 끝에 숫자를 추가하여 고유하게 만듭니다.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>저장소 키 가져오기

저장소 계정을 만드는 경우 서비스 중단 없이 키를 순환할 수 있도록 저장소 계정 키는 쌍으로 만들어집니다. 쌍에서 두 번째 키로 전환하는 경우 새 키 쌍이 만들어집니다. 새 저장소 계정 키는 항상 쌍으로 만들어지므로 전환할 준비가 된 사용하지 않은 저장소 계정 키가 하나 이상 항상 있어야 합니다.

[az storage account keys list](/cli/azure/storage/account/keys#list)를 사용하여 저장소 계정 키를 봅니다. 다음 예제에서는 키 1의 값을 **STORAGEKEY** 변수에 저장합니다.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>파일 공유 만들기

[az storage share create](/cli/azure/storage/share#create)를 사용하여 파일 저장소 공유를 만듭니다. 

공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만, 하이픈으로 시작할 수는 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

다음 예제에서는 10GiB 할당량이 있는 *myshare*라는 공유를 만듭니다. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>탑재 지점 만들기

Linux 컴퓨터에 Azure 파일 공유를 탑재하려면 **cifs-utils** 패키지가 설치되어 있어야 합니다. 설치 지침은 [Linux 배포용 cifs-utils 패키지 설치](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils)를 참조하세요.

Azure Files는 445 TCP 포트를 통해 통신하는 SMB 프로토콜을 사용합니다.  Azure 파일 공유를 탑재하는 데 문제가 있으면 방화벽이 445 TCP 포트를 차단하지 않는지 확인합니다.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>공유 탑재

Azure 파일 공유를 로컬 디렉터리에 탑재합니다. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

위의 명령은 [mount](https://linux.die.net/man/8/mount) 명령을 사용하여 Azure 파일 공유 및 [cifs](https://linux.die.net/man/8/mount.cifs) 전용 옵션을 탑재합니다. 특별히 file_mode 및 dir_mode 옵션은 파일 및 디렉터리를 `0777` 사용 권한에 설정합니다. `0777` 사용 권한은 읽기, 쓰기 및 실행 권한을 모든 사용자에게 부여합니다. 값을 다른 [chmod 사용 권한](https://en.wikipedia.org/wiki/Chmod)으로 바꾸면 이러한 권한을 변경할 수 있습니다. 또한 gid 또는 uid와 같은 다른 [cifs](https://linux.die.net/man/8/mount.cifs) 옵션을 사용할 수도 있습니다. 


## <a name="persist-the-mount"></a>탑재 유지

Linux VM를 다시 부팅하면 탑재된 SMB 공유가 종료하는 동안 분리됩니다. 부팅 시 SMB 공유를 다시 탑재하려면 Linux /etc/fstab에 줄을 추가합니다. Linux는 부팅 프로세스 중에 탑재해야 하는 파일 시스템을 나열하기 위해 /etc/fstab 파일을 사용합니다. SMB 공유를 추가하면 File Storage 공유가 Linux VM용으로 영구히 탑재된 파일 시스템이 됩니다. cloud-init를 사용하는 경우 File Storage SMB 공유를 새 VM에 추가할 수 있습니다.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
프로덕션 환경에서 보안을 강화하려면 자격 증명을 fstab 외부에 저장해야 합니다.

## <a name="next-steps"></a>다음 단계

- [cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정](using-cloud-init.md)
- [Linux VM에 디스크 추가](add-disk.md)
- [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md)

