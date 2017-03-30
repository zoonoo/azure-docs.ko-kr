---
title: "Azure DC/OS 클러스터용 파일 공유| Microsoft Docs"
description: "Azure Container Service에서 DC/OS 클러스터에 파일 공유 만들기 및 탑재"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>DC/OS 클러스터에 파일 공유 만들기 및 탑재
이 문서에서는 Azure에서 파일 공유를 만들어서 DC/OS 클러스터의 각 에이전트 및 마스터에 탑재하는 방법을 알아봅니다. 파일 공유를 설정하면 구성, 액세스, 로그 등과 같은 클러스터 전반에서 파일을 간편하게 공유할 수 있습니다.

이 예제에 따라 작업하려면 Azure Container Service에 구성된 DC/OS 클러스터가 필요합니다. [Azure Container Service 클러스터 배포](container-service-deployment.md)를 참조하세요.

## <a name="create-a-file-share-on-microsoft-azure"></a>Microsoft Azure에서 파일 공유 만들기
### <a name="using-the-portal"></a>포털 사용

1. 포털에 로그인합니다.
2. 저장소 계정을 만듭니다.
   
  ![Azure Container Service에서 저장소 계정 만들기](media/container-service-dcos-fileshare/createSA.png)

3. 만들어지면 **서비스** 섹션에서 **파일**을 클릭합니다.
   
  ![Azure Container Service 파일 선택](media/container-service-dcos-fileshare/filesServices.png)

4. **+ 파일 공유**를 클릭하고 새 공유에 대한 이름을 입력합니다(**할당량**은 필수가 아님).
   
  ![Azure Container Service + 파일 공유](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Azure CLI 2.0 사용

필요한 경우 [Azure CLI를 설치하고 설정](/cli/azure/install-azure-cli.md)합니다.

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>클러스터에 공유 탑재

다음으로 cifs 도구/프로토콜을 사용하여 클러스터 내의 모든 가상 컴퓨터에 이 공유를 탑재해야 합니다. 이 작업은 다음 명령줄을 사용하여 수행합니다. `mount -t cifs`.

사용 예는 다음과 같습니다.
* 저장소 계정 이름 **`anystorageaccountname`**
* 가상의 계정 키 **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`** 
* 탑재 지점 **`/mnt/share/demoshare`**

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

이 명령을 클러스터의 각 가상 컴퓨터(마스터 및 에이전트 노드)에서 실행합니다. 에이전트 수가 많은 경우 스크립트를 만들어서 이 프로세스를 자동화하는 것이 좋습니다.  

### <a name="set-up-scripts"></a>스크립트 설정

1. 우선 DC/OS 기반 클러스터의 마스터(또는 첫 번째 마스터)에 SSH를 사용합니다. 예: `ssh userName@masterFQDN –A –p 22`. 여기서 masterFQDN은 마스터 VM의 정규화된 도메인 이름입니다.

2. 개인 키를 마스터의 작업 디렉터리(~)에 복사합니다.

3. 다음 명령을 사용하여 그에 대한 사용 권한을 변경합니다. `chmod 600 yourPrivateKeyFile`.

4. `ssh-add yourPrivateKeyFile` 명령을 사용하여 개인 키를 가져옵니다. 처음에 작동하지 않으면 `eval ssh-agent -s`를 실행해야 할 수 있습니다.

5. 마스터에서 vi, nano 또는 vim 등 원하는 편집기를 사용하여 두 개의 파일을 만듭니다. 
  
  * 하나는 각 VM에서 실행할 스크립트가 포함된 **cifsMount.sh** 파일 
  * 다른 하나는 첫 번째 스크립트를 호출하는 모든 ssh 연결을 실행할 **mountShares.sh** 파일


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> **'mount'** 명령을 저장소 계정 이름 및 암호와 같은 자신만의 설정으로 변경해야 합니다.
>  

이전 스크립트를 만든 폴더에 이제 파일이 3개 있습니다.  

* **cifsMount.sh**
* **mountShares.sh**
* **yourPrivateKeyFile** 

### <a name="run-the-scripts"></a>스크립트 실행

다음 명령을 사용하여 **mountShares.sh** 파일을 실행합니다. `sh mountShares.sh`.

터미널에 결과가 출력되는 것을 확인합니다. 스크립트가 완료된 후 클러스터에서 파일 공유를 사용할 수 있습니다.

스크립트를 최적화할 수 있지만 이 예제는 지침을 제공하기 위한 용도라서 간단합니다.

> [!NOTE] 
> 이 방법은 높은 IOPS가 필요한 시나리오에는 권장되지 않지만 클러스터 전반에서 문서와 정보를 공유하기에는 매우 유용합니다.
>

## <a name="next-steps"></a>다음 단계
* [DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.
* [Marathon REST API](container-service-mesos-marathon-rest.md)를 통해 DC/OS 컨테이너 관리.
