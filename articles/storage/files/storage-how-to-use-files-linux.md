---
title: Linux에서 Azure Files 사용 | Microsoft Docs
description: Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72264755d5f0379f0ffb07852f48885126a36898
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411596"
---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [SMB 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

Linux에서 Azure 파일 공유를 탑재하는 권장 방법은 SMB 3.0을 사용하는 것입니다. 기본적으로 Azure Files는 SMB 3.0에서만 지원되는 전송 중인 암호화가 필요합니다. Azure Files는 전송 중 암호화를 지원하지 않는 SMB 2.1도 지원하지만 보안상의 이유로 다른 Azure 지역 또는 온-프레미스에서 SMB 2.1을 탑재할 수 없습니다. 응용 프로그램에 SMB 2.1이 특별히 필요하지 않는 한, 가장 인기 있고 최근에 릴리스된 Linux 배포판이 SMB 3.0을 지원하기 때문에 이 응용 프로그램을 사용할 이유가 거의 없습니다.  

| | SMB 2.1 <br>(동일한 Azure 지역 내에서 VM에 탑재) | SMB 3.0 <br>(온-프레미스 및 지역 간 탑재) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| RHEL(Red Hat Enterprise Linux) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10개 이상 |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

위의 표에 나열 되지 않은 리눅스 배포판을 사용 하는 경우 리눅스 배포판 지원 여부를 확인할 수 있습니다 SMB 3.0 리눅스 커널 버전을 확인 하 여 암호화. 암호화와 SMB 3.0 리눅스 커널 버전 4.11에 추가되었습니다. 명령은 `uname` 사용 중 Linux 커널 의 버전을 반환합니다.

```bash
uname -r
```

## <a name="prerequisites"></a>사전 요구 사항
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**cifs-utils 패키지가 설치되어 있는지 확인합니다.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt` 패키지 관리자를 사용합니다.

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **페도라에서,** **레드 햇 엔터프라이즈 리눅스 8 +** 및 **CentOS 8 +** 패키지 관리자를 `dnf` 사용:

    ```bash
    sudo dnf install cifs-utils
    ```

    이전 버전의 **Red Hat 엔터프라이즈 Linux** 및 `yum` **CentOS에서는**패키지 관리자를 사용합니다.

    ```bash
    sudo yum install cifs-utils 
    ```

    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.

    ```bash
    sudo zypper install cifs-utils
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **AZURE 명령줄 인터페이스(CLI)의 최신 버전입니다.** Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 및 운영 체제 선택을 참조하세요. PowerShell 6+에서 Azure PowerShell 모듈을 사용하려는 경우 Azure CLI에 대한 아래 지침이 표시될 수 있습니다.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.  **리소스 그룹><** 교체하고 스토리지 계정>**<**
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

    연결이 성공하면 다음 출력과 비슷한 것이 표시됩니다.

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    회사 네트워크에서 포트 445를 열 수 없거나 ISP에 의해 포트 445를 열 수 없거나 ISP에 의해 차단된 경우 VPN 연결 또는 ExpressRoute를 사용하여 포트 445를 해결할 수 있습니다. 자세한 내용은 [직접 Azure 파일 공유 액세스에 대한 네트워킹 고려 사항을](storage-files-networking-overview.md)참조하십시오.

## <a name="mounting-azure-file-share"></a>Azure 파일 공유 장착
Linux 배포판에서 Azure 파일 공유를 사용하려면 Azure 파일 공유의 탑재 지점역할을 하는 디렉터리를 만들어야 합니다. 마운트 지점은 Linux 시스템의 아무 곳이나 만들 수 있지만 /mnt 에서 이 것을 만드는 것이 일반적인 규칙입니다. 탑재 지점 이후에는 명령을 `mount` 사용하여 Azure 파일 공유에 액세스합니다.

원하는 경우 동일한 Azure 파일 공유를 여러 마운트 지점에 탑재할 수 있습니다.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재
1. **마운트 지점에 대한 폴더**만들기 `<your-resource-group>` `<your-storage-account>`: `<your-file-share>` 을 바꾸고 환경에 적합한 정보로 만듭니다.

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **마운트 명령을 사용하여 Azure 파일 공유를 마운트합니다.** 아래 예제에서는 로컬 Linux 파일 및 폴더 사용 권한 기본0755를 의미하며, 이는 소유자에 대해 읽기, 쓰기 및 실행(파일/디렉터리 Linux 소유자 기준), 소유자 그룹의 사용자를 읽고 실행하고 시스템의 다른 사용자를 위해 읽고 실행한다는 것을 의미합니다. `uid` 및 `gid` 마운트 옵션을 사용하여 마운트에 대한 사용자 ID 및 그룹 ID를 설정할 수 있습니다. 사용자 지정 `dir_mode` 권한을 `file_mode` 원하는 대로 사용하고 설정할 수도 있습니다. 사용 권한을 설정하는 방법에 대한 자세한 내용은 위키백과에서 [UNIX 숫자 표기법을](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) 참조하십시오. 

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
    > 위의 마운트 명령은 SMB 3.0이 있는 마운트입니다. Linux 배포판에서 암호화를 통해 SMB 3.0을 지원하지 않거나 SMB 2.1만 지원하는 경우 저장소 계정과 동일한 지역 내에서만 Azure VM에서 마운트할 수 있습니다. 암호화를 사용하여 SMB 3.0을 지원하지 않는 Linux 배포판에 Azure 파일 공유를 탑재하려면 [저장소 계정에 대해 전송 중에 암호화를 사용하지 않도록 설정해야](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)합니다.

Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount $mntPath`를 사용하여 공유를 탑재 해제할 수 있습니다.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기
1. **마운트 지점에 대한 폴더 만들기**: 마운트 지점에 대한 폴더는 파일 시스템의 아무 곳이나 만들 수 있지만 /mnt 에서 이 폴더를 만드는 것이 일반적입니다. 예를 들어 다음 명령은 새 디렉터리, `<your-resource-group>` `<your-storage-account>`대체 `<your-file-share>` 을 만들고 환경에 적합한 정보를 만듭니다.

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

1. **다음 명령을 사용하여 다음 줄을 다음 `/etc/fstab`에 연결합니다: **아래 예제에서는 로컬 Linux 파일 및 폴더 사용 권한 기본0755를 사용하여 소유자에 대해 읽기, 쓰기 및 실행(파일/디렉터리 Linux 소유자 기준)을 읽고 실행하고, 소유자 그룹의 사용자를 읽고 실행하고, 시스템의 다른 사용자를 위해 읽고 실행합니다. `uid` 및 `gid` 마운트 옵션을 사용하여 마운트에 대한 사용자 ID 및 그룹 ID를 설정할 수 있습니다. 사용자 지정 `dir_mode` 권한을 `file_mode` 원하는 대로 사용하고 설정할 수도 있습니다. 사용 권한을 설정하는 방법에 대한 자세한 내용은 위키백과에서 [UNIX 숫자 표기법을](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) 참조하십시오.

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
    > 위의 마운트 명령은 SMB 3.0이 있는 마운트입니다. Linux 배포판에서 암호화를 통해 SMB 3.0을 지원하지 않거나 SMB 2.1만 지원하는 경우 저장소 계정과 동일한 지역 내에서만 Azure VM에서 마운트할 수 있습니다. 암호화를 사용하여 SMB 3.0을 지원하지 않는 Linux 배포판에 Azure 파일 공유를 탑재하려면 [저장소 계정에 대해 전송 중에 암호화를 사용하지 않도록 설정해야](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)합니다.

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>자동 fs를 사용하여 Azure 파일 공유를 자동으로 마운트합니다.

1. **자동 패키지가 설치되어 있는지 확인합니다.**  

    autofs 패키지는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt` 패키지 관리자를 사용합니다.
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    **페도라에서,** **레드 햇 엔터프라이즈 리눅스 8 +** 및 **CentOS 8 +** 패키지 관리자를 `dnf` 사용:
    ```bash
    sudo dnf install autofs
    ```
    이전 버전의 **Red Hat 엔터프라이즈 Linux** 및 `yum` **CentOS에서는**패키지 관리자를 사용합니다.
    ```bash
    sudo yum install autofs 
    ```
    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.
    ```bash
    sudo zypper install autofs
    ```
2. **공유에 대한 마운트 지점 만들기**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **크레타 새로운 사용자 정의 자동 자동 구성 파일**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **/etc/auto.fileshares에 다음 항목을 추가합니다.**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **/etc/auto.master에 다음 항목을 추가합니다.**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **자동 자동 시작**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **공유에 대해 지정된 폴더에 액세스**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Linux 보안
Linux에서 Azure 파일 공유를 탑재하려면 포트 445에 액세스할 수 있어야 합니다. 많은 조직에서 SMB 1에 내재된 보안 위험 때문에 포트 445를 차단합니다. CIFS(일반 인터넷 파일 시스템)라고도 하는 SMB 1은 많은 Linux 배포판에 포함된 레거시 파일 시스템 프로토콜입니다. SMB 1은 구식 프로토콜로 비효율적이며 무엇보다도 보안성이 떨어집니다. 좋은 소식은 Azure Files가 SMB 1을 지원하지 않으며 Linux 커널 버전 4.18부터 시작하여 Linux에서 SMB 1을 비활성화할 수 있다는 것입니다. 프로덕션 환경에서 SMB 파일 공유를 사용하기 전에 Linux 클라이언트에서 SMB 1을 사용하지 않도록 설정하는 [것이 좋습니다.](https://aka.ms/stopusingsmb1)

Linux 커널 4.18부터 시작하여 레거시 이유로 호출되는 `cifs` SMB 커널 모듈은 새 모듈 매개 변수(다양한 외부 설명서에서 *parm라고도* 함)를 `disable_legacy_dialects`노출합니다. Linux 커널 4.18에 도입되었지만 일부 공급업체에서는 이러한 변경 내용을 지원하는 이전 커널로 백업했습니다. 편의를 위해 다음 표에서는 일반적인 Linux 배포판에서 이 모듈 매개 변수의 가용성을 자세히 설명합니다.

| 배포 | SMB 1을 비활성화할 수 있습니다. |
|--------------|-------------------|
| 우분투 14.04-16.04 | 예 |
| Ubuntu 18.04 | 예 |
| 우분투 19.04+ | 예 |
| 데비안 8-9 | 예 |
| 데비안 10+ | 예 |
| 페도라 29+ | 예 |
| CentOS 7 | 예 | 
| 센트로스 8+ | 예 |
| 레드 햇 엔터프라이즈 리눅스 6.x-7.x | 예 |
| 레드 햇 엔터프라이즈 리눅스 8+ | 예 |
| 오픈수스 도약 15.0 | 예 |
| 오픈수스 도약 15.1+ | 예 |
| 오픈수즈 텀블위드 | 예 |
| 수지 리눅스 엔터프라이즈 11.x-12.x | 예 |
| 수지 리눅스 엔터프라이즈 15 | 예 |
| 수지 리눅스 엔터프라이즈 15.1 | 예 |

Linux 배포판에서 다음 명령을 통해 `disable_legacy_dialects` 모듈 매개 변수를 지원하는지 확인할 수 있습니다.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

이 명령은 다음 메시지를 출력해야 합니다.

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

SMB 1을 사용하지 않도록 설정하기 전에 SMB 모듈이 현재 시스템에 로드되지 않았는지 확인해야 합니다(SMB 공유를 탑재한 경우 자동으로 발생). SMB가 로드되지 않은 경우 아무 것도 출력하지 않아야 하는 다음 명령으로 이 작업을 수행할 수 있습니다.

```bash
lsmod | grep cifs
```

모듈을 언로드하려면 먼저 모든 SMB 공유를 `umount` 해제합니다(위에서 설명한 명령 사용). 다음 명령을 통해 시스템에 탑재된 모든 SMB 공유를 식별할 수 있습니다.

```bash
mount | grep cifs
```

모든 SMB 파일 공유를 해제하면 모듈을 언로드해도 됩니다. 이렇게 하려면 `modprobe` 명령을 사용합니다.

```bash
sudo modprobe -r cifs
```

다음 명령을 사용하여 SMB 1을 언로드하여 `modprobe` 모듈을 수동으로 로드할 수 있습니다.

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

마지막으로, SMB 모듈이 매개 `/sys/module/cifs/parameters`변수와 함께 로드되었는지 확인할 수 있습니다.

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

우분투 및 데비안 기반 배포판에서 SMB 1을 지속적으로 사용하지 않으려면 설정으로 호출된 `/etc/modprobe.d/local.conf` 새 파일(다른 모듈에 대한 사용자 지정 옵션이 없는 경우)을 만들어야 합니다. 다음 명령으로 이 작업을 수행할 수 있습니다.

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB 모듈을 로드하여 이것이 효과가 있었는지 확인할 수 있습니다.

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>사용자 의견
Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure Files는 Linux에서 File Storage를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure Files Linux 사용자](mailto:azurefiles@microsoft.com)에게 메일을 보내세요.

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure Files 배포에 대한 계획](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
