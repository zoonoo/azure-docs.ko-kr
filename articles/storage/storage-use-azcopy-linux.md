---
title: "Linux에서 AzCopy 사용하여 Azure Storage로 데이터 복사 또는 이동 | Microsoft Docs"
description: "Linux에서 AzCopy 유틸리티를 사용하여 Blob 및 파일 콘텐츠에서 데이터를 이동하거나 복사합니다. 로컬 파일에서 Azure 저장소로 데이터를 복사하거나, 저장소 계정 내에서 데이터를 복사하거나, 저장소 계정 간에 데이터를 복사합니다. 데이터를 Azure 저장소로 손쉽게 마이그레이션할 수 있습니다."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: seguler
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: d17f63dcee590529756d48d699f78b3fb30f973c
ms.contentlocale: ko-kr
ms.lasthandoff: 06/03/2017


---
# <a name="transfer-data-with-azcopy-on-linux"></a>Linux에서 AzCopy를 사용하여 데이터 전송
Linux에서 AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Microsoft Azure Blob 및 File Storage에(로부터) 복사하도록 디자인된 명령줄 유틸리티입니다. 저장소 계정 내에서나 저장소 계정 사이에서 개체 간에 데이터 복사할 수 있습니다.

두 가지 버전의 AzCopy를 다운로드할 수 있습니다. Linux에서 AzCopy는 POSIX 스타일 명령줄 옵션을 제공하는 Linux 플랫폼을 대상으로 하는 .NET Core Framework를 기반으로 합니다. [Windows에서 AzCopy](storage-use-azcopy.md)는 .NET Framework를 기반으로 하며 Windows 스타일 명령줄 옵션을 제공합니다. 이 문서에서는 Linux에서 AzCopy를 설명합니다.

## <a name="download-and-install-azcopy"></a>AzCopy 다운로드 및 설치
### <a name="installation-on-linux"></a>Linux에서 설치

Linux에서 AzCopy를 사용하려면 플랫폼에 .NET Core framework가 있어야 합니다. [.NET Core](https://www.microsoft.com/net/core#linuxubuntu) 페이지에서 설치 지침을 참조하세요.

예를 들어, Ubuntu 16.10에 .NET Core를 설치해 보겠습니다. 최신 설치 가이드를 보려면 [Linux에서.NET Core](https://www.microsoft.com/net/core#linuxubuntu) 설치 페이지를 확인하세요.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.3
```

.NET Core를 설치했으면 AzCopy를 다운로드 및 설치합니다.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Linux에서 AzCopy가 설치되면 추출한 파일을 제거할 수 있습니다. 또는 superuser 권한이 없는 경우 추출된 폴더에서 셸 스크립트 'azcopy'를 사용하여 AzCopy를 실행할 수도 있습니다. 

### <a name="alternative-installation-on-ubuntu"></a>Ubuntu에 대체 설치

**Ubuntu 14.04**

.Net Core에 대한 apt 원본 추가:

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

Microsoft Linux 제품 리포지토리에 대한 apt 원본 추가 및 AzCopy 설치:

```bash
curl https://packages.microsoft.com/config/ubuntu/14.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

.Net Core에 대한 apt 원본 추가:

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

Microsoft Linux 제품 리포지토리에 대한 apt 원본 추가 및 AzCopy 설치:

```bash
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.10**

.Net Core에 대한 apt 원본 추가:

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

Microsoft Linux 제품 리포지토리에 대한 apt 원본 추가 및 AzCopy 설치:

```bash
curl https://packages.microsoft.com/config/ubuntu/16.10/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>첫 번째 AzCopy 명령 작성
AzCopy 명령의 기본 구문은 다음과 같습니다.

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

다음 예제는 Microsoft Azure Blob 및 파일에(로부터) 데이터를 복사하는 여러 시나리오를 보여줍니다. 각 샘플에 사용된 매개 변수에 대한 자세한 설명은 `azcopy --help` 메뉴를 참조하세요.

## <a name="blob-download"></a>Blob: 다운로드
### <a name="download-single-blob"></a>단일 blob 다운로드

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

`/mnt/myfiles` 폴더가 없으면 AzCopy는 이 폴더를 만든 후 새 폴더에 `abc.txt `를 다운로드합니다.

### <a name="download-single-blob-from-secondary-region"></a>보조 지역에서 단일 Blob 다운로드

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

지역 중복 저장소가 사용된 읽기 액세스가 있어야 합니다.

### <a name="download-all-blobs"></a>모든 Blob 다운로드

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다.  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

다운로드 작업 후에 `/mnt/myfiles` 디렉터리에는 다음 파일이 포함됩니다.

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

`--recursive` 옵션을 지정하지 않으면 Blob이 다운로드되지 않습니다.

### <a name="download-blobs-with-specified-prefix"></a>지정된 접두사가 있는 Blob 다운로드

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다. 접두사 `a`로 시작하는 모든 Blob이 다운로드됩니다.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

다운로드 작업 후에 `/mnt/myfiles` 폴더에는 다음 파일이 포함됩니다.

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

접두사는 Blob 이름의 처음 부분을 구성하는 가상 디렉터리에 적용됩니다. 위에 표시된 예에서는 가상 디렉터리가 지정된 접두사와 일치하지 않으므로 Blob이 다운로드되지 않습니다. 또한 옵션 `--recursive`가 지정되지 않으면 AzCopy는 어떤 Blob도 다운로드하지 않습니다.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>내보낸 파일의 마지막으로 수정한 시간을 소스 Blob과 동일하게 설정

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

또한 마지막으로 수정한 시간에 따라 다운로드 작업에서 Blob을 제외할 수도 있습니다. 예를 들어 마지막으로 수정된 시간이 대상 파일과 동일하거나 더 최신인 Blob을 제외하려는 경우 `--exclude-newer` 옵션을 추가합니다.

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

또는 마지막으로 수정된 시간이 대상 파일과 동일하거나 더 이전인 Blob을 제외하려는 경우 `--exclude-older` 옵션을 추가합니다.

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: 업로드
### <a name="upload-single-file"></a>단일 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

지정된 대상 컨테이너가 존재하지 않을 경우 AzCopy는 컨테이너를 만든 후 여기에 파일을 업로드합니다.

### <a name="upload-single-file-to-virtual-directory"></a>가상 디렉터리에 단일 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

지정한 가상 디렉터리가 없으면 AzCopy는 Blob 이름에 가상 디렉터리를 포함하여 파일을 업로드합니다(*예*: 위 예의 `vd/abc.txt`).

### <a name="upload-all-files"></a>모든 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

`--recursive` 옵션을 지정하면 지정된 디렉터리의 내용이 Blob Storage에 재귀적으로 업로드됩니다. 즉, 모든 하위 폴더 및 해당 파일도 업로드됩니다. 예를 들어 다음 파일이 `/mnt/myfiles` 폴더에 있는 경우를 생각해 보겠습니다.

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

업로드 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

`--recursive` 옵션을 지정하지 않으면 다음 3개 파일이 업로드됩니다.

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>지정된 패턴과 일치하는 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

다음 파일이 `/mnt/myfiles`폴더에 있는 경우를 생각해 보겠습니다.

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

업로드 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

`--recursive` 옵션을 지정하지 않으면 AzCopy는 하위 디렉터리에 있는 파일을 건너뜁니다.

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>대상 Blob의 MIME 콘텐츠 형식을 지정합니다.
기본적으로 AzCopy에서는 대상 Blob의 콘텐츠 형식을 `application/octet-stream`으로 설정합니다. 하지만 `--set-content-type [content-type]` 옵션을 통해 콘텐츠 형식을 명시적으로 지정할 수 있습니다. 이 구문은 업로드 작업에서 모든 Blob의 콘텐츠 형식을 설정합니다.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

값 없이 `--set-content-type` 옵션을 지정하면 AzCopy에서 각 Blob 또는 파일의 콘텐츠 형식을 파일 확장명에 따라 설정합니다.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Blob: 복사
### <a name="copy-single-blob-within-storage-account"></a>저장소 계정 내 단일 Blob 복사

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key> \
    --dest-key <key> \
    --include "abc.txt"
```

--sync-copy 옵션 없이 Blob을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### <a name="copy-single-blob-across-storage-accounts"></a>저장소 계정 간 단일 Blob 복사

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

--sync-copy 옵션 없이 Blob을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>보조 지역에서 주 지역으로 단일 Blob 복사

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

지역 중복 저장소가 사용된 읽기 액세스가 있어야 합니다.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>저장소 계정 간 단일 Blob 및 스냅숏 복사

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

복사 작업 후에 대상 컨테이너에는 Blob 및 해당 스냅숏이 포함됩니다. 컨테이너에는 다음 Blob과 스냅숏이 포함됩니다.

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>저장소 계정 간 Blob 비동기 복사
기본적으로 AzCopy는 두 저장소 끝점 간에 데이터를 비동기적으로 복사합니다. 따라서 복사 작업은 Blob이 복사되는 속도와 관련하여 SLA가 없는 여분의 대역폭 용량을 사용하여 백그라운드로 실행됩니다. 

`--sync-copy` 옵션을 사용하면 복사 작업이 일관된 속도를 유지할 수 있습니다. AzCopy는 지정된 소스에서 로컬 메모리로 복사할 Blob을 다운로드한 후 대상 Blob 저장소에 업로드하여 동기 복사를 수행합니다.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy`는 비동기 복사에 비해 추가적인 송신 비용이 발생할 수 있습니다. 원본 저장소 계정과 동일한 지역에 있는 Azure VM에서 이 옵션을 사용하여 송신 비용이 발생하지 않도록 하는 것이 좋습니다.

## <a name="file-download"></a>파일: 다운로드
### <a name="download-single-file"></a>단일 파일 다운로드

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

지정된 소스가 Azure 파일 공유이면 단일 파일을 다운로드할 정확한 파일 이름(*예:* `abc.txt`)을 지정하거나 `--recursive` 옵션을 지정하여 공유의 모든 파일을 재귀 방식으로 다운로드해야 합니다. 파일 패턴과 `--recursive` 옵션을 함께 지정하려고 하면 오류가 발생합니다.

### <a name="download-all-files"></a>모든 파일 다운로드

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

빈 폴더는 다운로드되지 않습니다.

## <a name="file-upload"></a>파일: 업로드
### <a name="upload-single-file"></a>단일 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include abc.txt
```

### <a name="upload-all-files"></a>모든 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

빈 폴더는 업로드되지 않습니다.

### <a name="upload-files-matching-specified-pattern"></a>지정된 패턴과 일치하는 파일 업로드

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>파일: 복사
### <a name="copy-across-file-shares"></a>파일 공유 간 복사

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
파일 공유에 파일을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### <a name="copy-from-file-share-to-blob"></a>파일 공유에서 Blob으로 복사

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
파일 공유에서 Blob으로 파일을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### <a name="copy-from-blob-to-file-share"></a>Blob에서 파일 공유로 복사

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Blob에서 파일 공유로 파일을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### <a name="synchronously-copy-files"></a>동기적으로 파일 복사
`--sync-copy` 옵션을 지정하여 File Storage 간에, File Storage에서 Blob Storage로, Blob Storage에서 File Storage로 동기적으로 데이터를 복사할 수 있습니다. AzCopy는 로컬 메모리에 원본 데이터를 다운로드한 후 대상에 다시 업로드하여 이 작업을 수행합니다. 이 경우 표준 송신 비용이 적용됩니다.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

파일 저장소에서 Blob 저장소로 복사할 경우 기본 Blob 형식은 블록 Blob입니다. `/BlobType:page` 옵션을 지정하면 사용자가 대상 Blob 유형을 변경할 수 있습니다.

`--sync-copy`는 비동기 복사에 비해 추가적인 송신 비용이 발생할 수 있습니다. 원본 저장소 계정과 동일한 지역에 있는 Azure VM에서 이 옵션을 사용하여 송신 비용이 발생하지 않도록 하는 것이 좋습니다.

## <a name="other-azcopy-features"></a>기타 AzCopy 기능
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>대상에 없는 데이터만 복사
`--exclude-older` 및 `--exclude-newer` 매개 변수를 통해 이전 또는 최신 소스 리소스를 각각 복사 작업에서 제외할 수 있습니다. 대상에 없는 소스 리소스만 복사하려는 경우 AzCopy 명령에 두 매개 변수를 지정할 수 있습니다.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>구성 파일을 사용하여 명령줄 매개 변수 지정

```azcopy
azcopy --config-file "azcopy-config.ini"
```

구성 파일에 AzCopy 명령줄 매개 변수를 포함할 수 있습니다. AzCopy는 파일의 매개 변수가 마치 명령줄에 지정된 것처럼 처리하고 파일 내용을 직접적으로 대체합니다.

다음 줄을 포함하는 `copyoperation`라는 구성 파일이 있고 한 줄 또는 여러 줄에 각 AzCopy 매개 변수를

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

지정할 수 있습니다.

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

`--source-key` 매개 변수에 대해 여기에 표시된 것처럼 매개 변수를 두 줄로 분할하면 AzCopy는 실패합니다.

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>SAS(공유 액세스 서명) 지정

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-sas <SAS1> \
    --dest-sas <SAS2> \
    --include abc.txt
```

또한 컨테이너 URI에서 SAS를 지정할 수도 있습니다.

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

AzCopy는 현재 [계정 SAS](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1)만 지원합니다.

### <a name="journal-file-folder"></a>저널 파일 폴더
AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.

저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 AzCopy에서 사용자에게 저널 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다.

저널 파일에 대해 기본 위치를 사용하는 경우

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

`--resume` 옵션을 생략하거나 위에 표시된 것처럼 폴더 경로 없이 `--resume`를 지정하면 AzCopy는 기본 위치인 `~\Microsoft\Azure\AzCopy`에 저널 파일을 만듭니다. 저널 파일이 이미 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

저널 파일의 사용자 지정 위치를 지정하려는 경우

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

이 예에서는 저널 파일이 없는 경우 파일을 만듭니다. 저널 파일이 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

AzCopy 작업을 다시 시작하려는 경우 같은 명령을 반복합니다. Linux에서 AzCopy에 확인 메시지가 표시됩니다.

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>자세한 정보 표시 로그 출력

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>시작할 동시 작업 수 지정
`--parallel-level` 옵션은 동시 복사 작업의 수를 지정합니다. AzCopy는 데이터 전송 처리량을 높이기 위해 기본적으로 특정 수의 동시 작업을 시작합니다. 동시 작업 수는 가지고 있는 프로세서의 수의 8배입니다. 저대역폭 네트워크에서 AzCopy를 실행하는 경우에는 리소스 경쟁으로 인한 실패를 방지하기 위해 --parallel-level을 더 낮게 지정할 수 있습니다.

[!TIP]
>AzCopy 매개 변수의 전체 목록을 보려면, 'azcopy --help' 메뉴를 확인하세요.

## <a name="known-issues-and-best-practices"></a>알려진 문제 및 모범 사례
### <a name="error-net-core-is-not-found-in-the-system"></a>오류: 시스템에서 .NET Core를 찾을 수 없습니다.
시스템에 .NET Core가 설치되지 않았다는 오류가 발생하는 경우 .NET Core 이진 `dotnet`에 대한 경로가 누락될 수 있습니다.

이 문제를 해결하기 위해 시스템에서 .NET Core 이진을 찾습니다.
```bash
sudo find / -name dotnet
```

그러면 dotnet 이진에 대한 경로가 반환됩니다. 

    /opt/rh/rh-dotnetcore11/root/usr/bin/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/shared/Microsoft.NETCore.App/1.1.2/dotnet

이제 이 경로를 경로 변수에 추가합니다. sudo의 경우 dotnet 이진에 대한 경로를 포함하도록 secure_path를 편집합니다.
```bash 
sudo visudo
### Append the path found in the preceding example to 'secure_path' variable
```

이 예제에서 secure_path 변수 내용은 다음과 같습니다.

```
secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/rh/rh-dotnetcore11/root/usr/bin/
```

현재 사용자에 대해 PATH 변수에 dotnet 이진에 대한 경로를 포함하도록 .bash_profile/.profile을 편집합니다. 
```bash
vi ~/.bash_profile
### Append the path found in the preceding example to 'PATH' variable
```

이제 .NET Core가 경로에 있는지 확인합니다.
```bash
which dotnet
sudo which dotnet
```

### <a name="error-installing-azcopy"></a>AzCopy 설치 오류
AzCopy 설치 관련 문제가 발생하는 경우 추출된 `azcopy` 폴더에서 bash 스크립트를 사용하여 AzCopy를 실행해볼 수 있습니다.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>데이터를 복사하는 동안 동시 쓰기 제한
AzCopy를 사용하여 Blob 또는 파일을 복사할 때는 복사하는 동안 다른 응용 프로그램이 데이터를 수정할 수 있다는 사실을 유의해야 합니다. 가능한 경우 복사 중인 데이터가 복사 작업 중에 수정되지 않도록 합니다. 예를 들어 Azure 가상 컴퓨터와 연결된 VHD를 복사할 때는 다른 응용 프로그램이 현재 VHD에 쓰고 있지 않은지 확인합니다. 이렇게 하려면 복사할 리소스를 임대하는 것이 좋습니다. 또는 먼저 VHD의 스냅샷을 만든 후 스냅샷을 복사할 수 있습니다.

다른 응용 프로그램이 복사 중인 Blob 또는 파일에 쓰지 못하게 할 수 없으면 작업이 완료될 때까지 복사된 리소스가 소스 리소스와 더 이상 완전히 동일하지 않을 수 있습니다.

### <a name="run-one-azcopy-instance-on-one-machine"></a>한 컴퓨터에서 AzCopy 인스턴스 하나를 실행합니다.
AzCopy는 데이터 전송 속도를 높이기 위해 컴퓨터 리소스를 최대한 활용할 수 있도록 설계되었습니다. 따라서 컴퓨터 한 대에서 AzCopy 인스턴스를 하나만 실행하는 것이 좋으며 더 많은 동시 작업을 수행해야 하는 경우에는 `--parallel-level` 옵션을 지정해야 합니다. 자세한 내용을 확인하려면 명령줄에 `AzCopy --help parallel-level` 를 입력하세요.

## <a name="next-steps"></a>다음 단계
Azure Storage 및 AzCopy에 대한 자세한 내용은 다음 리소스를 참조하세요.

### <a name="azure-storage-documentation"></a>Azure 저장소 설명서
* [Azure 저장소 소개](storage-introduction.md)
* [저장소 계정을 만드는](storage-create-storage-account.md)
* [저장소 탐색기를 사용하여 Blob 관리](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs)
* [Azure Storage에서 Azure CLI 2.0 사용](storage-azure-cli.md)
* [C++에서 Blob 저장소를 사용하는 방법](storage-c-plus-plus-how-to-use-blobs.md)
* [Java에서 Blob 저장소를 사용하는 방법](storage-java-how-to-use-blob-storage.md)
* [Node.js에서 Blob 저장소를 사용하는 방법](storage-nodejs-how-to-use-blob-storage.md)
* [Python에서 Blob 저장소를 사용하는 방법](storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure 저장소 블로그 게시물:
* [Linux 미리 보기에서 AzCopy 발표](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Azure 저장소 데이터 이동 라이브러리 미리 보기 소개](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: 동기 복사본 및 사용자 지정 콘텐츠 형식 소개(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: AzCopy 3.0의 일반 공급 및 테이블 및 파일을 지원하는 AzCopy 4.0의 미리 보기 릴리스 발표(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: 대량 복사 시나리오에 맞게 최적화(영문)](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: 읽기 액세스 지역 중복 저장소 지원(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: 다시 시작 가능 모드 및 SAS 토큰으로 데이터 전송(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: 크로스 계정 Blob 복사 사용(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Azure Blob 파일 업로드/다운로드(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


