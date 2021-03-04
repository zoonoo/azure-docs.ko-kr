---
title: 패키지 관리에 대 한 사용자 지정 Conda 채널 만들기
description: 패키지 관리에 대 한 사용자 지정 Conda 채널을 만드는 방법 알아보기
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107681"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>패키지 관리에 대 한 사용자 지정 Conda 채널 만들기 
Python 패키지를 설치 하는 경우 Conda 패키지 관리자는 채널을 사용 하 여 패키지를 찾습니다. 여러 가지 이유로 사용자 지정 Conda 채널을 만들어야 할 수 있습니다. 예를 들어 다음을 확인할 수 있습니다.

- 작업 영역에는 보호 된 데이터 및 아웃 바운드 연결이 차단 됩니다.  
- 공용 리포지토리에 업로드 하지 않으려는 패키지가 있습니다.
- 작업 영역 내에서 사용자에 대 한 대체 리포지토리를 설정 하려고 합니다.

이 문서에서는 Azure Data Lake Storage 계정 내에서 사용자 지정 Conda 채널을 만드는 데 도움이 되는 단계별 가이드를 제공 합니다.

## <a name="set-up-your-local-machine"></a>로컬 컴퓨터 설정

1. 로컬 컴퓨터에 Conda를 설치 합니다. [Azure Synapse Spark 런타임을](./apache-spark-version-support.md) 참조 하 여 동일한 런타임에 사용 되는 Conda 버전을 식별할 수 있습니다.
   
2. 사용자 지정 채널을 만들려면 conda를 설치 합니다.
```
conda install conda-build
```
3. 에서 제공 하고자 하는 플랫폼에 대 한 모든 패키지를 구성 합니다. 이 예제에서는 로컬 컴퓨터에 Anaconda 보관을 설치 합니다.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>컴퓨터에 저장소 계정 탑재
다음에는 로컬 컴퓨터에 Azure Data Lake Storage Gen2 계정을 탑재 합니다. WASB 계정을 사용 하 여이 프로세스를 수행할 수도 있습니다. 그러나 ADLSg2 계정에 대 한 예제를 살펴보겠습니다. 
 
로컬 컴퓨터에 저장소 계정을 탑재 하는 방법에 대 한 자세한 내용은 [이 페이지](https://github.com/Azure/azure-storage-fuse#blobfuse )를 참조 하세요. 

1. Microsoft 제품에 대 한 Linux 소프트웨어 리포지토리에서 blobfuse를 설치할 수 있습니다.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. 탑재 지점을 만들고 ```mkdir /path/to/mount``` blobfuse를 사용 하 여 Blob 컨테이너를 탑재 합니다. 이 예제에서는 **mycontainer** 변수에 **privatechannel** 값을 사용 하겠습니다.
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>채널 만들기
다음 단계에서는 사용자 지정 Conda 채널을 만듭니다. 

1. 로컬 컴퓨터에서 사용자 지정 채널에 대 한 모든 패키지를 구성 하는 디렉터리를 만듭니다.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. ```tar.bz2```의 모든 패키지를 https://repo.anaconda.com/pkgs/main/linux-64/ 하위 디렉터리로 구성 합니다. 모든 종속 tar. bz2 패키지도 포함 해야 합니다. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

자세한 내용은 [Conda 사용자 가이드를 방문](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) 하 여 사용자 지정 채널을 만들 수도 있습니다. 

## <a name="storage-account-permissions"></a>저장소 계정 권한
이제 저장소 계정에 대 한 사용 권한의 유효성을 검사 해야 합니다. 이러한 권한을 설정 하려면 사용자 지정 채널을 만들 경로로 이동 합니다. 그런 다음 ```privatechannel``` 읽기, 나열 및 실행 권한이 있는에 대 한 SAS 토큰을 만듭니다. 

채널 이름은 이제이 프로세스에서 생성 된 blob SAS URL이 됩니다.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Sample Conda environment 구성 파일 만들기
마지막으로, 샘플 Conda 파일을 만들어 설치 프로세스를 확인 합니다 ```environment.yml``` . DEP 사용 작업 영역에 있는 경우 ``nodefaults`` 환경 파일에서 채널을 지정 해야 합니다.

다음은 예제 Conda 구성 파일입니다.
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
샘플 Conda 파일을 만든 후에는 가상 Conda 환경을 만들 수 있습니다. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
사용자 지정 채널을 확인 했으므로 [Python 풀 관리](./apache-spark-manage-python-packages.md) 프로세스를 사용 하 여 Apache Spark 풀의 라이브러리를 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- 기본 라이브러리 보기: [Apache Spark 버전 지원](apache-spark-version-support.md)
- Python 패키지 관리: [python 패키지 관리](./apache-spark-manage-python-packages.md)

