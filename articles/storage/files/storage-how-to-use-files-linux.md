---
title: Linux에서 Azure Files 사용 | Microsoft Docs
description: Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다. 사전 요구 사항 목록을 참조하세요. Linux 클라이언트에서 SMB 보안 고려 사항을 검토하세요.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4ace5620bf98b06956c294a12b6b08881422e718
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952340"
---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [SMB 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

Linux에서 Azure 파일 공유를 탑재하는 권장 방법은 SMB 3.0을 사용하는 것입니다. 기본적으로 Azure Files는 전송 중에 암호화가 필요하며 이는 SMB 3.0에서만 지원됩니다. Azure Files는 전송 중에 암호화를 지원하지 않는 SMB 2.1도 지원합니다. 하지만 보안상의 이유로 다른 Azure 지역 또는 온-프레미스에서 SMB 2.1을 사용하여 Azure 파일 공유를 탑재하지 않을 수 있습니다. 애플리케이션에서 특별히 SMB 2.1을 요구하지 않는 한 가장 널리 사용되는 최근에 릴리스된 Linux 배포판에서 SMB 3.0을 지원하기 때문에 2.1을 사용해야 하는 이유가 거의 없습니다.  

| Linux 배포 | SMB 2.1 <br>(동일한 Azure 지역 내에서 VM에 탑재) | SMB 3.0 <br>(온-프레미스 및 지역 간 탑재) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| RHEL(Red Hat Enterprise Linux) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10개 이상 |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP2+ |

위의 표에 나열되지 않은 Linux 배포판을 사용하는 경우 Linux 커널 버전을 확인하여 Linux 배포판에서 암호화가 포함된 SMB 3.0을 지원하는지 여부를 확인할 수 있습니다. 암호화가 포함된 SMB 3.0이 Linux 커널 버전 4.11에 추가되었습니다. `uname` 명령은 사용 중인 Linux 커널 버전을 반환합니다.

```bash
uname -r
```

## <a name="prerequisites"></a>필수 구성 요소
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**cifs-utils 패키지가 설치되어 잇는 확인합니다.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt` 패키지 관리자를 사용합니다.

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Fedora**, **Red Hat Enterprise Linux 8 이상** 및 **CentOS 8 이상** 에서는 `dnf` 패키지 관리자를 사용합니다.

    ```bash
    sudo dnf install cifs-utils
    ```

    **Red Hat Enterprise Linux** 및 **CentOS** 의 이전 버전에서는 `yum` 패키지 관리자를 사용합니다.

    ```bash
    sudo yum install cifs-utils 
    ```

    **openSUSE** 에서는 `zypper` 패키지 관리자를 사용합니다.

    ```bash
    sudo zypper install cifs-utils
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **최신 버전의 Azure CLI(Azure 명령줄 인터페이스)를 사용해야 합니다.** Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell CLI 설치](/cli/azure/install-azure-cli)를 참조하고 해당 운영 체제를 선택합니다. PowerShell 6 이상에서 Azure PowerShell 모듈을 사용하려는 경우 Azure CLI에 대한 아래 지침이 표시될 수 있습니다.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.  `<your-resource-group>` 및 `<your-storage-account>`를 바꾸고 다음 스크립트를 실행합니다.
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    연결되면 다음 출력과 유사한 내용이 표시됩니다.

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    회사 네트워크에서 포트 445를 열 수 없거나 ISP에 의해 이러한 작업이 차단된 경우 VPN 연결 또는 ExpressRoute를 사용하여 포트 445를 처리할 수 있습니다. 자세한 내용은 [직접 Azure 파일 공유 액세스를 위한 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

## <a name="mounting-azure-file-share"></a>Azure 파일 공유 탑재
Linux 배포판에 Azure 파일 공유를 사용하려면 Azure 파일 공유에 대한 탑재 지점으로 사용할 디렉터리를 만들어야 합니다. 탑재 지점은 Linux 시스템의 어디에나 만들 수 있지만 /mount 아래에 만드는 것이 일반적인 규칙입니다. 탑재 지점 후 `mount` 명령을 사용하여 Azure 파일 공유에 액세스합니다.

원하는 경우 동일한 Azure 파일 공유를 여러 탑재 지점에 탑재할 수 있습니다.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재
1. **탑재 지점에 대한 폴더 만들기**: `<your-resource-group>`, `<your-storage-account>` 및 `<your-file-share>`를 사용자 환경에 적합한 정보로 바꿉니다.

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **탑재 명령을 사용하여 Azure 파일 공유를 탑재합니다**. 아래 예제에서 로컬 Linux 파일 및 폴더 권한 기본 0755는 소유자에 대한 읽기, 쓰기 및 실행(파일/디렉터리 Linux 소유자 기반), 소유자 그룹의 사용자에 대한 읽기 및 실행, 시스템의 다른 사용자에 대한 읽기 및 실행을 의미합니다. `uid` 및 `gid` 탑재 옵션을 사용하여 탑재할 사용자 ID 및 그룹 ID를 설정합니다. `dir_mode` 및 `file_mode`를 사용하여 원하는 대로 사용자 지정 권한을 설정할 수도 있습니다. 권한 설정 방법에 대한 자세한 내용은 위키백과에서 [UNIX 숫자 표기법](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)을 참조하세요. 

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > 위의 탑재 명령은 SMB 3.0을 사용하여 탑재합니다. Linux 배포판이 암호화를 포함하는 SMB 3.0을 지원하지 않거나 SMB 2.1만 지원하는 경우, 스토리지 계정과 동일한 지역 내의 Azure VM에서만 탑재할 수 있습니다. 암호화를 포함하는 SMB 3.0을 지원하지 않는 Linux 배포판에서 Azure 파일 공유를 탑재하려면 [스토리지 계정에 대해 전송 중인 암호화를 사용하지 않도록 설정](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)해야 합니다.

Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount $mntPath`를 사용하여 공유를 탑재 해제할 수 있습니다.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기
1. **탑재 지점용 폴더 만들기**: 탑재 지점용 폴더는 파일 시스템의 어디에나 만들 수 있지만 /mount 아래 만드는 것이 일반 규칙입니다. 예를 들어, 다음 명령은 새 디렉터리를 만들어 `<your-resource-group>`, `<your-storage-account>` 및 `<your-file-share>`를 사용자 환경에 적합한 정보로 바꿉니다.

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **파일 공유를 위한 사용자 이름(스토리지 계정 이름) 및 암호(스토리지 계정 키)를 저장할 자격 증명 파일을 만듭니다.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **하나의 루트만 암호 파일을 읽거나 수정할 수 있도록 자격 증명 파일의 권한을 변경합니다.** 스토리지 계정 키는 기본적으로 스토리지 계정에 대한 상위 관리자 암호이므로, 루트만 액세스할 수 있는 파일에서 사용 권한을 설정하는 것은 더 낮은 권한 사용자가 스토리지 계정 키를 검색할 수 없도록 하는 데 중요합니다.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **다음 명령을 사용하여 다음 줄을 `/etc/fstab`** 에 추가: 아래 예제에서 로컬 Linux 파일 및 폴더 권한 기본 0755는 소유자에 대한 읽기, 쓰기 및 실행(파일/디렉터리 Linux 소유자 기반), 소유자 그룹의 사용자에 대한 읽기 및 실행, 시스템의 다른 사용자에 대한 읽기 및 실행을 의미합니다. `uid` 및 `gid` 탑재 옵션을 사용하여 탑재할 사용자 ID 및 그룹 ID를 설정합니다. `dir_mode` 및 `file_mode`를 사용하여 원하는 대로 사용자 지정 권한을 설정할 수도 있습니다. 권한 설정 방법에 대한 자세한 내용은 위키백과에서 [UNIX 숫자 표기법](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)을 참조하세요.

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > 위의 탑재 명령은 SMB 3.0을 사용하여 탑재합니다. Linux 배포판이 암호화를 포함하는 SMB 3.0을 지원하지 않거나 SMB 2.1만 지원하는 경우, 스토리지 계정과 동일한 지역 내의 Azure VM에서만 탑재할 수 있습니다. 암호화를 포함하는 SMB 3.0을 지원하지 않는 Linux 배포판에서 Azure 파일 공유를 탑재하려면 [스토리지 계정에 대해 전송 중인 암호화를 사용하지 않도록 설정](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)해야 합니다.

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>autofs를 사용하여 Azure 파일 공유 자동 탑재

1. **autofs 패키지가 설치되어 있는지 확인합니다.**  

    autofs 패키지는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt` 패키지 관리자를 사용합니다.
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    **Fedora**, **Red Hat Enterprise Linux 8 이상** 및 **CentOS 8 이상** 에서는 `dnf` 패키지 관리자를 사용합니다.
    ```bash
    sudo dnf install autofs
    ```
    **Red Hat Enterprise Linux** 및 **CentOS** 의 이전 버전에서는 `yum` 패키지 관리자를 사용합니다.
    ```bash
    sudo yum install autofs 
    ```
    **openSUSE** 에서는 `zypper` 패키지 관리자를 사용합니다.
    ```bash
    sudo zypper install autofs
    ```
2. **공유에 대한 탑재 지점 만들기**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **새로운 사용자 지정 autofs 구성 파일 만들기**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **/etc/auto.fileshares에 다음 항목 추가**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **/etc/auto.master에 다음 항목 추가**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **autofs 다시 시작**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **공유에 지정된 폴더에 액세스**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Linux 보안
Linux에서 Azure 파일 공유를 탑재하려면 포트 445에 액세스할 수 있어야 합니다. 많은 조직에서 SMB 1에 내재된 보안 위험 때문에 포트 445를 차단합니다. CIFS(Common Internet File System)라고도 하는 SMB 1은 많은 Linux 배포판에 포함된 레거시 파일 시스템 프로토콜입니다. SMB 1은 구식 프로토콜로 비효율적이며 무엇보다도 보안성이 떨어집니다. 좋은 소식은 Azure Files가 SMB 1을 지원하지 않으며, Linux 커널 버전 4.18부터는 Linux를 통해 SMB 1을 비활성화할 수 있다는 것입니다. 프로덕션 환경에서 SMB 파일 공유를 사용하기 전에 Linux 클라이언트에서 SMB 1을 사용하지 않도록 설정하는 것이 [좋습니다](https://aka.ms/stopusingsmb1).

Linux 커널 4.18부터 레거시 용도로 `cifs`라는 SMB 커널 모듈이 `disable_legacy_dialects`라는 새 모듈 매개 변수(다양한 외부 문서에서는 *parm* 이라고도 함)를 노출합니다. Linux 커널 4.18에 도입되었지만 일부 공급업체는 이 변경 내용을 지원되는 이전 커널로 백포팅했습니다. 편의를 위해 다음 표에서는 일반적인 Linux 배포판에서 이 모듈 매개 변수의 가용성을 자세히 설명합니다.

| 배포 | SMB 1 해제 가능 여부 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 예 |
| Ubuntu 18.04 | 예 |
| Ubuntu 19.04 이상 | 예 |
| Debian 8-9 | 예 |
| Debian 10 이상 | 예 |
| Fedora 31 이상 | 예 |
| CentOS 7 | 예 | 
| CentOS 8 이상 | 예 |
| Red Hat Enterprise Linux 6.x-7.x | 예 |
| Red Hat Enterprise Linux 8 이상 | 예 |
| openSUSE Leap 15.0 | 예 |
| openSUSE Leap 15.1 이상 | 예 |
| openSUSE Tumbleweed | 예 |
| SUSE Linux Enterprise 11.x-12.x | 예 |
| SUSE Linux Enterprise 15 | 예 |
| SUSE Linux Enterprise 15.1 | 예 |

Linux 배포판에서 다음 명령을 통해 `disable_legacy_dialects` 모듈 매개 변수를 지원하는지 확인할 수 있습니다.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

이 명령은 다음 메시지를 출력해야 합니다.

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

SMB 1을 사용하지 않도록 설정하기 전에 SMB 모듈이 현재 시스템에 로드되어 있지 않은지 확인해야 합니다(SMB 공유를 탑재한 경우 자동으로 발생). 다음 명령을 사용하여 이 작업을 수행할 수 있습니다. SMB가 로드되지 않은 경우 아무 것도 출력되지 않습니다.

```bash
lsmod | grep cifs
```

모듈을 언로드하려면 먼저 모든 SMB 공유(위에 설명된 대로 `umount` 명령 사용)를 탑재 해제합니다. 다음 명령을 사용하여 사용하는 시스템에서 탑재된 모든 SMB 공유를 식별할 수 있습니다.

```bash
mount | grep cifs
```

모든 SMB 파일 공유를 탑재 해제한 후에는 모듈을 언로드하는 것이 안전합니다. 이렇게 하려면 `modprobe` 명령을 사용합니다.

```bash
sudo modprobe -r cifs
```

`modprobe` 명령을 사용하여 SMB 1이 언로드된 모듈을 수동으로 로드할 수 있습니다.

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

마지막으로 `/sys/module/cifs/parameters`에서 로드된 매개 변수를 살펴보면 SMB 모듈이 매개 변수와 함께 로드되었는지 확인할 수 있습니다.

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu 및 Debian 기반 배포판에서 SMB 1을 영구적으로 사용하지 않도록 설정하려면 설정으로 `/etc/modprobe.d/local.conf`라는 새 파일(다른 모듈에 대한 사용자 지정 옵션이 없는 경우)을 만들어야 합니다. 다음 명령을 사용하여 수행할 수 있습니다.

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB 모듈을 로드하여 이 작업의 수행 여부를 확인할 수 있습니다.

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [FAQ](./storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)