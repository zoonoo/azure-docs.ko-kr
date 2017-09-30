---
title: "Linux에서 Azure Files 사용 | Microsoft Docs"
description: "Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6c80c6814dacf90a0a50dab691899c3a8cc2bf82
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [Samba 프로젝트](https://www.samba.org/)의 [cifs-utils 패키지](https://wiki.samba.org/index.php/LinuxCIFS_utils)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

> [!NOTE]  
> 온-프레미스 또는 다른 Azure 지역과 같이 에서 호스팅되는 Azure 지역 외부에 Azure 파일 공유를 탑재하려면 OS에서 SMB 3.0 암호화 기능을 지원해야 합니다. Linux용 SMB 3.0에 대한 암호화 기능이 4.11 커널에 도입되었습니다. 이 기능을 사용하면 온-프레미스 또는 다른 Azure 지역에서 Azure 파일 공유를 탑재할 수 있습니다. 게시 시점에서 이 기능은 Ubuntu 16.04 이상으로 백포팅되었습니다.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Linux 및 cifs-utils 패키지와 함께 Azure 파일 공유를 탑재하기 위한 필수 조건
* **cifs-utils 패키지를 설치할 수 있는 Linux 배포판을 선택합니다**. Azure 이미지 갤러리에서 권장되는 Linux 배포판은 다음과 같습니다.

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**cifs-utils 패키지가 설치됩니다**. cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt-get` 패키지 관리자를 사용합니다.

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    **RHEL** 및 **CentOS**에서는 `yum` 패키지 관리자를 사용합니다.

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.

    ```
    sudo zypper install samba*
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **탑재된 공유의 디렉터리/파일 권한을 결정합니다**. 아래 예제에서는 모든 사용자에게 읽기, 쓰기 및 실행 권한을 부여하기 위해 0777을 사용합니다. 다른 [chmod 권한](https://en.wikipedia.org/wiki/Chmod)으로 바꿀 수 있습니다. 

* **저장소 계정 이름**: Azure 파일 공유를 탑재하려면 저장소 계정의 이름이 필요합니다.

* **저장소 계정 키**: Azure 파일 공유를 탑재하려면 기본(또는 보조) 저장소 키가 필요합니다. SAS 키는 현재 탑재를 지원하지 않습니다.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재
1. **[Linux 배포판에 cifs-utils 패키지를 설치합니다](#install-cifs-utils)**.

2. **탑재 지점에 대한 폴더를 만듭니다**. 이 작업은 파일 시스템의 어느 위치에서나 수행할 수 있습니다.

    ```
    mkdir mymountpoint
    ```

3. **mount 명령을 사용하여 Azure 파일 공유를 탑재합니다**. `<storage-account-name>`, `<share-name>` 및 `<storage-account-key>`를 적절한 정보로 바꿉니다.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount ./mymountpoint`를 사용하여 공유를 탑재 해제할 수 있습니다.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기
1. **[Linux 배포판에 cifs-utils 패키지를 설치합니다](#install-cifs-utils)**.

2. **탑재 지점에 대한 폴더를 만듭니다**. 이 작업은 파일 시스템의 어느 위치에서나 수행할 수 있지만 폴더의 절대 경로를 기록해 두어야 합니다. 다음 예제에서는 루트 아래에 폴더를 만듭니다.

    ```
    sudo mkdir /mymountpoint
    ```

3. **다음 명령을 사용하여 `/etc/fstab`에 다음 줄을 추가합니다**. `<storage-account-name>`, `<share-name>` 및 `<storage-account-key>`를 적절한 정보로 바꿉니다.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> 다시 부팅하는 대신 `/etc/fstab`을 편집한 후에 `sudo mount -a`를 사용하여 Azure 파일 공유를 탑재할 수 있습니다.

## <a name="feedback"></a>사용자 의견
Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure Files는 Linux에서 파일 저장소를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure Files Linux 사용자](mailto:azurefileslinuxusers@microsoft.com)에게 메일을 보내세요.

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.
* [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage에서 Azure CLI 사용](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [FAQ](../storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)

