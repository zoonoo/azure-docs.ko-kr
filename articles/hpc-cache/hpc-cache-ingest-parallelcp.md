---
title: Azure HPC 캐시 데이터 수집-병렬 복사 스크립트
description: 병렬 복사 스크립트를 사용 하 여 Azure HPC 캐시에서 Blob 저장소 대상으로 데이터를 이동 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 0bb74dcd683145fbae22cf0b6d2827ad9e16de0e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582686"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC 캐시 데이터 수집-병렬 복사 스크립트 메서드

이 문서에서는 ``parallelcp`` 스크립트를 만들고이 스크립트를 사용 하 여 Azure HPC 캐시에서 사용할 Blob 저장소 컨테이너로 데이터를 이동 하는 방법에 대 한 지침을 제공 합니다.

Azure HPC 캐시의 Blob 저장소로 데이터를 이동 하는 방법에 대 한 자세한 내용은 azure [Hpc 캐시로 Azure blob storage로 데이터 이동](hpc-cache-ingest.md)을 참조 하세요.

## <a name="create-the-parallelcp-script"></a>Parallelcp 스크립트 만들기

아래 스크립트는 `parallelcp` 실행 파일을 추가합니다. (이 스크립트는 Ubuntu에서 사용하도록 설계되었으므로, 다른 배포판을 사용하는 경우 ``parallel``을 별도로 설치해야 합니다.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>병렬 복사 예제

이 예제에서는 병렬 복사 스크립트를 사용 하 여 Azure HPC 캐시의 원본 파일을 사용 하 ``glibc``을 컴파일합니다.

원본 파일은 Azure HPC 캐시 탑재 지점에 캐시 되 고 개체 파일은 로컬 하드 드라이브에 저장 됩니다.

이 예에서는 병렬 복사 스크립트와 ``-j`` 및 ``make`` 옵션을 사용 하 여 병렬화를 얻습니다.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
