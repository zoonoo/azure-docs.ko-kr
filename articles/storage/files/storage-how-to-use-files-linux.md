---
title: Linux에서 Azure Files 사용 | Microsoft Docs
description: Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3d8d7c6d3c4e752480310c122bcb7db237b3022b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209409"
---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [SMB 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

Linux에서 Azure 파일 공유를 탑재 하는 권장 방법은 SMB 3.0을 사용 하는 것입니다. 기본적으로 Azure Files SMB 3.0 에서만 지원 되는 전송 암호화가 필요 합니다. Azure Files은 전송 중인 암호화를 지원 하지 않는 SMB 2.1도 지원 하지만 보안상의 이유로 다른 Azure 지역 또는 온-프레미스에서 SMB 2.1을 사용 하 여 Azure 파일 공유를 탑재 하지 않을 수 있습니다. 응용 프로그램에서 특별히 SMB 2.1을 요구 하지 않는 한 가장 널리 사용 되는 Linux 배포판에서 SMB 3.0을 지원 하기 때문에 특별히 사용 해야 하는 이유가 거의 없습니다.  

| | SMB 2.1 <br>(동일한 Azure 지역 내에서 VM에 탑재) | SMB 3.0 <br>(온-프레미스 및 지역 간 탑재) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| RHEL(Red Hat Enterprise Linux) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

위의 표에 나열 되지 않은 Linux 배포를 사용 하는 경우 linux 배포판에서 Linux 커널 버전을 확인 하 여 암호화를 사용 하 여 SMB 3.0을 지원 하는지 확인할 수 있습니다. 암호화가 포함 된 SMB 3.0이 Linux 커널 버전 4.11에 추가 되었습니다. `uname` 명령은 사용 중인 Linux 커널의 버전을 반환 합니다.

```bash
uname -r
```

## <a name="prerequisites"></a>선행 조건
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Cifs-유틸리티 패키지가 설치 되어 있는지 확인 합니다.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt` 패키지 관리자를 사용합니다.

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Fedora**, **Red Hat Enterprise Linux 8 +** 및 **CentOS 8 +** 에서 `dnf` 패키지 관리자를 사용 합니다.

    ```bash
    sudo dnf install cifs-utils
    ```

    이전 버전의 **Red Hat Enterprise Linux** 및 **CentOS**에서 `yum` 패키지 관리자를 사용 합니다.

    ```bash
    sudo yum install cifs-utils 
    ```

    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.

    ```bash
    sudo zypper install cifs-utils
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **최신 버전의 Azure CLI (명령줄 인터페이스)입니다.** Azure CLI를 설치 하는 방법에 대 한 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 를 참조 하 고 운영 체제를 선택 하십시오. PowerShell 6 +에서 Azure PowerShell 모듈을 사용 하려는 경우에는 Azure CLI에 대 한 지침이 제공 될 수 있습니다.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.  **-리소스 그룹 > <** 을 바꾸고 **저장소 계정 < >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    연결에 성공 하면 다음 출력과 유사한 내용이 표시 됩니다.

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    회사 네트워크에서 포트 445을 열 수 없거나 ISP에 의해 차단 된 경우 VPN 연결 또는 Express 경로를 사용 하 여 포트 445를 해결할 수 있습니다. 자세한 내용은 [Azure 파일 공유에 대 한 직접 액세스에 대 한 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조 하세요.

## <a name="mounting-azure-file-share"></a>Azure 파일 공유 탑재
Linux 배포에 Azure 파일 공유를 사용 하려면 Azure 파일 공유의 탑재 지점으로 사용할 디렉터리를 만들어야 합니다. 탑재 지점은 Linux 시스템의 어디에 나 만들 수 있지만/mnt.에서이를 만드는 일반적인 규칙입니다. 탑재 지점 후에 `mount` 명령을 사용 하 여 Azure 파일 공유에 액세스 합니다.

원하는 경우 동일한 Azure 파일 공유를 여러 탑재 위치에 탑재할 수 있습니다.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재
1. **탑재 지점에 대 한 폴더 만들기**: `<your-resource-group>`, `<your-storage-account>`및 `<your-file-share>`을 사용자 환경에 맞는 적절 한 정보로 바꿉니다.

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Mount 명령을 사용 하 여 Azure 파일 공유를 탑재**합니다. 아래 예제에서는 로컬 Linux 파일 및 폴더 사용 권한 기본 0755를 사용 합니다 .이는 소유자 (파일/디렉터리 Linux 소유자 기반)에 대 한 읽기, 쓰기 및 실행, 소유자 그룹의 사용자에 대 한 읽기 및 실행, 시스템에서 다른 사용자에 대 한 읽기 및 실행을 의미 합니다. `uid`를 사용 하 고 탑재 옵션을 `gid` 하 여 탑재의 사용자 ID 및 그룹 ID를 설정할 수 있습니다. `dir_mode` 및 `file_mode`를 사용 하 여 원하는 대로 사용자 지정 권한을 설정할 수도 있습니다. 사용 권한을 설정 하는 방법에 대 한 자세한 내용은 위키백과의 [UNIX 숫자 표기법](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) 을 참조 하세요. 

    ```bash
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
    > 위의 탑재 명령은 SMB 3.0를 사용 하 여 탑재 합니다. Linux 배포판에서 암호화를 사용 하는 SMB 3.0을 지원 하지 않거나 SMB 2.1만 지 원하는 경우에는 저장소 계정과 동일한 지역 내에 있는 Azure VM 에서만 탑재할 수 있습니다. 암호화를 사용 하 여 SMB 3.0을 지원 하지 않는 Linux 배포판에 Azure 파일 공유를 탑재 하려면 [저장소 계정에 대 한 전송에서 암호화를 사용 하지 않도록 설정](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)해야 합니다.

Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount $mntPath`를 사용하여 공유를 탑재 해제할 수 있습니다.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기
1. **탑재 지점에 대 한 폴더 만들기**: 탑재 지점에 대 한 폴더는 파일 시스템의 어느 위치에 나 만들 수 있지만이를/mnt. 아래에 만드는 것이 일반적인 규칙입니다. 예를 들어 다음 명령은 새 디렉터리를 만들고 `<your-resource-group>`, `<your-storage-account>`및 `<your-file-share>`을 사용자 환경에 맞는 적절 한 정보로 바꿉니다.

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

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

1. **다음 명령을 사용 하 여 `/etc/fstab`에 다음 줄을 추가** 합니다. 아래 예제에서는 파일/디렉터리 Linux 소유자에 따라 소유자에 대 한 읽기, 쓰기 및 실행, 소유자 그룹의 사용자에 대 한 읽기 및 실행, 시스템의 다른 사용자에 대 한 읽기 및 실행을 의미 하는 로컬 Linux 파일 및 폴더 권한을 기본 0755으로 설정 합니다. `uid`를 사용 하 고 탑재 옵션을 `gid` 하 여 탑재의 사용자 ID 및 그룹 ID를 설정할 수 있습니다. `dir_mode` 및 `file_mode`를 사용 하 여 원하는 대로 사용자 지정 권한을 설정할 수도 있습니다. 사용 권한을 설정 하는 방법에 대 한 자세한 내용은 위키백과의 [UNIX 숫자 표기법](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) 을 참조 하세요.

    ```bash
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
    > 위의 탑재 명령은 SMB 3.0를 사용 하 여 탑재 합니다. Linux 배포판에서 암호화를 사용 하는 SMB 3.0을 지원 하지 않거나 SMB 2.1만 지 원하는 경우에는 저장소 계정과 동일한 지역 내에 있는 Azure VM 에서만 탑재할 수 있습니다. 암호화를 사용 하 여 SMB 3.0을 지원 하지 않는 Linux 배포판에 Azure 파일 공유를 탑재 하려면 [저장소 계정에 대 한 전송에서 암호화를 사용 하지 않도록 설정](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)해야 합니다.

## <a name="securing-linux"></a>Linux 보안
Linux에서 Azure 파일 공유를 탑재 하려면 포트 445에 액세스할 수 있어야 합니다. 많은 조직에서 SMB 1에 내재된 보안 위험 때문에 포트 445를 차단합니다. CIFS (Common Internet File System) 라고도 하는 SMB 1은 많은 Linux 배포판에 포함 된 레거시 파일 시스템 프로토콜입니다. SMB 1은 구식 프로토콜로 비효율적이며 무엇보다도 보안성이 떨어집니다. Azure Files SMB 1을 지원 하지 않으며 Linux 커널 버전 4.18부터 Linux를 사용 하지 않도록 설정 하는 것이 좋습니다. 프로덕션 환경에서 SMB 파일 공유를 사용 하기 전에 Linux 클라이언트에서 항상 SMB 1을 사용 하지 않도록 설정 하는 [것이 좋습니다](https://aka.ms/stopusingsmb1) .

Linux 커널 4.18부터 레거시 이유로 `cifs` 이라고 하는 SMB 커널 모듈은 `disable_legacy_dialects`라는 새 모듈 매개 변수 (종종 다양 한 외부 문서에서 *parm* 라고도 함)를 노출 합니다. Linux 커널 4.18에 도입 되었지만 일부 공급 업체는이 변경 내용을 지원 되는 이전 커널로 변경 했습니다. 편의를 위해 다음 표에서는 일반적인 Linux 배포판에서이 모듈 매개 변수의 가용성을 자세히 설명 합니다.

| 배포 | SMB를 사용 하지 않도록 설정할 수 있음 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 아니오 |
| Ubuntu 18.04 | 예 |
| Ubuntu 19.04 + | 예 |
| Debian 8-9 | 아니오 |
| Debian 10 이상 | 예 |
| Fedora 29 이상 | 예 |
| CentOS 7 | 아니오 | 
| CentOS 8 이상 | 예 |
| Red Hat Enterprise Linux 6.x-7.x | 아니오 |
| Red Hat Enterprise Linux 8 이상 | 예 |
| openSUSE Leap 15.0 | 아니오 |
| openSUSE Leap 15.1 + | 예 |
| openSUSE Tumbleweed | 예 |
| SUSE Linux Enterprise 11.x-12. x | 아니오 |
| SUSE Linux Enterprise 15 | 아니오 |
| SUSE Linux Enterprise 15.1 | 아니오 |

다음 명령을 통해 Linux 배포판에서 `disable_legacy_dialects` module 매개 변수를 지원 하는지 확인할 수 있습니다.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

이 명령은 다음 메시지를 출력 해야 합니다.

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Smb 1을 사용 하지 않도록 설정 하기 전에 smb 모듈이 현재 시스템에 로드 되어 있지 않은지 확인 해야 합니다 (SMB 공유를 탑재 한 경우 자동으로 발생). 다음 명령을 사용 하 여이 작업을 수행할 수 있습니다. SMB가 로드 되지 않은 경우 아무 것도 출력 되지 않습니다.

```bash
lsmod | grep cifs
```

모듈을 언로드하려면 먼저 모든 SMB 공유 (위에 설명 된 대로 `umount` 명령을 사용 하 여)를 분리 합니다. 다음 명령을 사용 하 여 시스템에서 탑재 된 모든 SMB 공유를 식별할 수 있습니다.

```bash
mount | grep cifs
```

모든 SMB 파일 공유를 분리 한 후에는 모듈을 언로드하는 것이 안전 합니다. 이렇게 하려면 `modprobe` 명령을 사용합니다.

```bash
sudo modprobe -r cifs
```

`modprobe` 명령을 사용 하 여 SMB 1로 언로드된 모듈을 수동으로 로드할 수 있습니다.

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

마지막으로 `/sys/module/cifs/parameters`에서 로드 된 매개 변수를 살펴보면 SMB 모듈이 매개 변수와 함께 로드 되었는지 확인할 수 있습니다.

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu 및 Debian 기반 배포에서 SMB 1을 영구적으로 사용 하지 않도록 설정 하려면 설정을 사용 하 여 `/etc/modprobe.d/local.conf` 라고 하는 새 파일 (다른 모듈에 대 한 사용자 지정 옵션을 아직 사용 하지 않은 경우)을 만들어야 합니다. 다음 명령을 사용 하 여이 작업을 수행할 수 있습니다.

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB 모듈을 로드 하 여이 작업의 수행 여부를 확인할 수 있습니다.

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>사용자 의견
Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure Files는 Linux에서 File Storage를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure Files Linux 사용자](mailto:azurefileslinuxusers@microsoft.com)에게 메일을 보내세요.

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
