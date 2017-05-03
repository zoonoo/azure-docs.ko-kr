---
title: "Azure DC/OS 클러스터에 ACR 사용| Microsoft Docs"
description: "Azure Container Service에서 DC/OS 클러스터에 Azure Container Registry 사용"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>DC/OS 클러스터에 ACR을 사용하여 응용 프로그램 배포

이 문서에서는 DC/OS 클러스터에 ACR(Azure Container Registry)과 같은 개인 컨테이너 레지스터를 사용하는 방법을 살펴봅니다. ACR을 사용하면 이미지를 개인적으로 저장하고 버전 및/또는 업데이트와 같은 제어를 유지할 수 있습니다.

이 예제에 따라 작업하려면 다음이 필요합니다. 
* Azure Container Service에 구성된 DC/OS 클러스터. [Azure Container Service 클러스터 배포](container-service-deployment.md)를 참조하세요.
* Azure Container Service가 배포되어 있어야 합니다. [Azure Portal을 사용하여 개인 Docker 컨테이너 레지스트리 만들기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) 또는 [Azure CLI 2.0을 사용하여 개인 Docker 컨테이너 레지스트리 만들기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)를 참조하세요.
* DC/OS 클러스터 내부에 구성된 파일 공유. [DC/OS 클러스터에 파일 공유 만들기 및 탑재](container-service-dcos-fileshare.md)를 참조하세요.
* [웹 UI](container-service-mesos-marathon-ui.md) 또는 [REST API](container-service-mesos-marathon-rest.md)를 사용하여 DC/OS 클러스터에 Docker 이미지를 배포하는 방법을 이해하려면

## <a name="manage-the-authentication-inside-your-cluster"></a>클러스터 내에서 인증 관리

개인 레지스트리에서 이미지를 밀어넣고 가져오는 편리한 방법은 먼저 레지스트리를 인증하는 것입니다. 이렇게 하려면 개인 레지스트리를 사용하는 데 필요한 docker 클라이언트 프로세스에서 `docker login` 명령줄을 사용해야 합니다.
프로덕션 환경에서는 이 경우 DC/OS를 사용하여 노드에서 이미지를 가져올 수 있는지 확인하고자 합니다. 즉, 인증 프로세스를 자동화하려고 하므로 각 컴퓨터에서 명령줄을 실행하지 마세요. 예상할 수 있듯이 클러스터 크기에 따라 문제가 발생하고 부담스러운 작업일 수 있습니다. 

[DC/OS 내에 파일 공유를 이미 설정](container-service-dcos-fileshare.md)했다고 가정하면 다음을 수행하여 이를 활용합니다.

### <a name="from-any-client-machine-recommended-method"></a>클라이언트 컴퓨터에서[권장 방법]

모든 환경에서 다음 명령을 실행할 수 있음(Windows/Mac/Linux):

1. 다음 필수 조건을 충족하고 있는지 확인:
  * TAR 도구
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [기타](https://www.docker.com/get-docker)
  * [다음 방법](container-service-dcos-fileshare.md)을 사용하여 클러스터 내부에 탑재된 파일 공유

2. 자주 사용하는 터미널에서 다음 명령을 사용하여 ACR 서비스에 대한 인증 시작: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. `USERNAME`, `PASSWORD` 및 `ACR-REGISTRY-NAME` 변수를 Azure Portal에 제공된 값으로 바꿔야 합니다.

3. `docker login` 작업을 수행할 때 값이 컴퓨터의 홈 폴더(Mac 및 Linux의 경우 `cd ~/.docker`, Windows의 경우 `cd %HOMEPATH%`) 아래에 로컬로 저장된다는 사실이 흥미롭습니다. `tar czf` 명령을 사용하여 이 폴더의 내용을 압축합니다.

4. 마지막 단계는 [필수 조건으로 만들어야 하는](container-service-dcos-fileshare.md) 파일 공유 내에 방금 만든 tar 파일을 복사하는 것입니다. 다음 명령으로 Azure-CLI를 사용하여 이 작업을 수행할 수 있습니다. `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>`

마지막으로 다음 설정을 사용하는 예입니다(windows 환경 사용).
* ACR 이름: **`demodcos`**
* 사용자 이름: **`demodcos`**
* 암호: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* 저장소 계정 이름: **`anystorageaccountname`**
* 저장소 계정 키: **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* 저장소 계정 내에서 생성된 공유 이름: **`share`**
* 업로드할 tar 아카이브 경로: **`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>마스터에서[권장하지 않는 방법]

실수 및 전체 환경에 미치는 영향을 피하기 위해 마스터에서 실행 작업은 권장되지 않습니다.

1. 우선 DC/OS 기반 클러스터의 마스터(또는 첫 번째 마스터)에 SSH를 사용합니다. 예: `ssh userName@masterFQDN –A –p 22`. 여기서 masterFQDN은 마스터 VM의 정규화된 도메인 이름입니다. [자세한 정보를 보려면 여기를 클릭](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. 다음 명령을 사용하여 ACR 서비스에 대한 인증 시작: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. `USERNAME`, `PASSWORD` 및 `ACR-REGISTRY-NAME` 변수를 Azure Portal에 제공된 값으로 바꿔야 합니다.

3. `docker login` 작업을 수행할 때 값이 컴퓨터의 홈 폴더 `~/.docker` 아래에 로컬로 저장된다는 사실이 흥미롭습니다. `tar czf` 명령을 사용하여 이 폴더의 내용을 압축합니다.

4. 마지막 단계는 공유 내에 방금 만든 tar 파일을 복사하는 것입니다. 이 작업을 통해 클러스터 내에 있는 모든 가상 컴퓨터에서 이 자격 증명을 사용하고 Azure Container Registry에서 인증 할 수 있습니다.

마지막으로 다음 설정을 사용하는 예입니다.
* ACR 이름: **`demodcos`**
* 사용자 이름: **`demodcos`**
* 암호: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* 클러스터 내 탑재 지점: **`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Marathon을 사용하여 ACR에서 이미지 배포

아마도 컨테이너 레지스트리 내부에 배포하려는 이미지를 이미 밀어넣었을 것입니다. [Docker CLI를 사용하여 개인 Docker 컨테이너 레지스트리로 이미지 밀어넣기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)를 참조하세요.

Azure (ACR)에서 호스팅되는 개인 레지스트리의 **simple-web** 이미지를 **2.1** 태그와 함께 배포한다면 다음 구성을 사용합니다.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> 여기에서 볼 수 있듯이 **uris** 옵션을 사용하여 자격 증명이 저장된 위치를 지정합니다.
>

## <a name="next-steps"></a>다음 단계
* [DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.
* [Marathon REST API](container-service-mesos-marathon-rest.md)를 통해 DC/OS 컨테이너 관리.
