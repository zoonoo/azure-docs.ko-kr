---
title: "Azure Container Service 클러스터에 연결 | Microsoft 문서"
description: "원격 컴퓨터의 Azure Container Service에서 Kubernetes, DC/OS 또는 Docker Swarm 클러스터에 연결"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3dfa2c56dd6d3e0fe7757995d284cebe172eabc4
ms.lasthandoff: 04/03/2017


---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Kubernetes, DC/OS 또는 Docker Swarm 클러스터에 원격 연결
Azure Container Service 클러스터를 만든 후에 클러스터에 연결하여 워크로드를 배포하고 관리해야 합니다. 이 문서에서는 원격 컴퓨터에서 클러스터의 마스터 VM에 연결하는 방법을 설명합니다. 

Kubernetes, DC/OS 및 Docker Swarm 클러스터는 HTTP 끝점을 로컬로 제공합니다. Kubernetes의 경우 이 끝점은 안전하게 인터넷에 노출되고 인터넷에 연결된 모든 컴퓨터에서 `kubectl` 명령줄 도구를 실행하여 액세스할 수 있습니다. 

DC/OS 및 Docker Swarm의 경우 내부 시스템에 대한 SSH(보안 셸) 터널을 만들어야 합니다. 터널이 설정되면 HTTP 끝점을 사용하는 명령을 실행하고 로컬 시스템에서 클러스터의 웹 인터페이스를 볼 수 있습니다. 


## <a name="prerequisites"></a>필수 조건

* [Azure Container Service에 배포된](container-service-deployment.md) Kubernetes, DC/OS 또는 Swarm 클러스터
* 배포 중에 클러스터에 추가된 공개 키에 해당하는 SSH RSA 개인 키 파일. 이러한 명령은 개인 SSH 키가 사용자의 컴퓨터의 `$HOME/.ssh/id_rsa`에 있다고 가정합니다. 자세한 내용은 [OS X 및 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/linux/ssh-from-windows.md)에 대한 다음 지침을 참조하세요. SSH 연결이 작동하지 않는 경우 [SSH 키를 재설정](../virtual-machines/linux/troubleshoot-ssh-connection.md)해야 합니다.

## <a name="connect-to-a-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

다음 단계에 따라 컴퓨터에 `kubectl`을 설치하고 구성합니다.

> [!NOTE] 
> Linux 또는 OS X에서 `sudo`을 사용하여 이 섹션의 명령을 실행해야 합니다.
> 

### <a name="install-kubectl"></a>kubectl 설치
이 도구를 설치하는 한 가지 방법은 `az acs kubernetes install-cli` Azure CLI 2.0 명령을 사용하는 것입니다. 이 명령을 실행하려면 최신 Azure CLI 2.0을 [설치](/cli/azure/install-az-cli2)하고 Azure 계정(`az login`)에 로그인해야 합니다.

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

또는 [Kubernetes 릴리스 페이지](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md)에서 최신 클라이언트를 직접 다운로드할 수 있습니다. 자세한 내용은 [kubectl 설치 및 설정](https://kubernetes.io/docs/user-guide/prereqs/)을 참조하세요.

### <a name="download-cluster-credentials"></a>클러스터 자격 증명 다운로드
일단 `kubectl`을 설치하면 클러스터 자격 증명을 컴퓨터에 복사해야 합니다. 자격 증명을 가져오는 방법은 `az acs kubernetes get-credentials` 명령을 사용하는 것입니다. 리소스 그룹의 이름과 컨테이너 서비스 리소스의 이름을 전달합니다.


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

이 명령은 클러스터 자격 증명을 `$HOME/.kube/config`로 다운로드합니다. 해당 항목은 `kubectl`에 배치됩니다.

또는 `scp`을 사용하여 마스터 VM의 `$HOME/.kube/config`에서 로컬 컴퓨터로 파일을 안전하게 복사할 수 있습니다. 예:

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Windows에서 작업하는 경우 Windows, PuTTy 보안 파일 복사 클라이언트 또는 유사한 도구의 Ubuntu에서 Bash를 사용해야 합니다.



### <a name="use-kubectl"></a>kubectl 사용

`kubectl`를 구성한 경우 클러스터에 노드를 나열하여 연결을 테스트할 수 있습니다.

```console
kubectl get nodes
```

다른 `kubectl` 명령을 사용할 수 있습니다. 예를 들어, Kubernetes 대시보드를 볼 수 있습니다. 먼저 Kubernetes API 서버에 대한 프록시를 실행합니다.

```console
kubectl proxy
```

이제 Kubernetes UI는 `http://localhost:8001/ui`에서 사용 가능합니다.

자세한 내용은 [Kubernetes 빠른 시작](http://kubernetes.io/docs/user-guide/quick-start/)을 참조하세요.

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>DC/OS 또는 Swarm 클러스터에 연결

Azure Container Service에서 배포하는 DC/OS 및 Docker Swarm 클러스터를 사용하려면 다음 지침을 따라 로컬 Linux, OS X 또는 Windows 시스템에서 SSH(보안 셸) 터널을 만드세요. 

> [!NOTE]
> 이러한 지침은 SSH를 통한 TCP 트래픽 터널링에 중점을 둡니다. 또한 내부 클러스터 관리 시스템 중 하나를 사용하여 대화형 SSH 세션을 시작할 수도 있지만 이는 권장되지 않습니다. 내부 시스템에 직접 작업할 경우 실수로 구성을 변경할 위험이 있습니다.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Linux 또는 OS X에서 SSH 터널 만들기
Linux 또는 OS X에서 SSH 터널을 만들 때 먼저 수행할 작업은 부하 분산된 마스터의 공용 DNS 이름을 찾는 것입니다. 다음 단계를 수행하세요.


1. [Azure Portal](https://portal.azure.com)에서 컨테이너 서비스 클러스터를 포함하는 리소스 그룹으로 이동합니다. 리소스 그룹을 확장하여 각 리소스가 표시되도록 합니다. 

2. 컨테이너 서비스 리소스를 클릭하고 **개요**를 클릭합니다. **Essentials(필수 정보)** 아래에 클러스터의 **마스터 FQDN**이 표시됩니다. 이 이름은 나중에 사용되므로 저장합니다. 

    ![공용 DNS 이름](media/pubdns.png)

    또는 컨테이너 서비스에 `az acs show` 명령을 실행합니다. 명령 출력에서 **Master Profile:fqdn** 속성을 찾아봅니다.

3. 이제 셸을 열고 다음 값을 지정하여 `ssh` 명령을 실행합니다. 

    **LOCAL_PORT**는 연결할 터널의 서비스 측 TCP 포트입니다. Swarm의 경우 2375로 설정합니다. DC/OS의 경우 80으로 설정합니다.  
    **REMOTE_PORT**는 노출하려는 끝점의 포트입니다. Swarm의 경우 2375 포트를 사용합니다. DC/OS의 경우 포트 80을 사용합니다.  
    **USERNAME** 은 클러스터를 배포할 때 제공된 사용자 이름입니다.  
    **DNSPREFIX** 는 클러스터를 배포할 때 제공한 DNS 접두사입니다.  
    **REGION** 은 리소스 그룹이 있는 하위 지역입니다.  
    **PATH_TO_PRIVATE_KEY** [선택 사항]은 클러스터를 만들 때 제공한 공개 키에 해당하는 개인 키에 대한 경로입니다. `-i` 플래그와 함께 이 옵션을 사용합니다.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > SSH 연결 포트는 표준 포트 22가 아니라 2200입니다. 둘 이상의 마스터 VM을 포함한 클러스터에서 첫 번째 마스터 VM에 대한 연결 포트입니다.
    > 



다음 섹션에서는 DC/OS 및 Swarm에 대한 예제를 참조하세요.    

### <a name="dcos-tunnel"></a>DC/OS 터널
DC/OS 끝점에 대한 터널을 열려면 다음과 같은 명령을 실행합니다.

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> 포트 80이 아닌 다른 로컬 포트(예: 포트 8888)를 지정할 수 있습니다. 그러나 이 포트를 사용할 경우 일부 웹 UI 링크가 작동하지 않을 수 있습니다.

이제 로컬 시스템에서 다음 URL을 통해 DC/OS 끝점에 액세스할 수 있습니다(로컬 포트 80 사용 시).

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

마찬가지로, 각 응용 프로그램에 대한 rest API는 이 터널을 통해 도달할 수 있습니다.

### <a name="swarm-tunnel"></a>Swarm 터널
Swarm 끝점에 대한 터널을 열려면 다음과 같은 명령을 실행합니다.

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

이제 다음과 같이 DOCKER_HOST 환경 변수를 설정할 수 있습니다. 정상적으로 Docker CLI(명령줄 인터페이스)를 계속 사용할 수 있습니다.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Windows에서 SSH 터널 만들기
Windows에서 SSH 터널을 만드는 방법은 여러 가지가 있습니다. 이 섹션에서는 PuTTY를 사용하여 터널을 만드는 방법을 설명합니다.

1. Windows 시스템으로 [PuTTY를 다운로드합니다](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. 응용 프로그램을 실행합니다.

3. 클러스터 관리 사용자 이름 및 클러스터에서 첫 번째 마스터의 공용 DNS 이름으로 구성된 호스트 이름을 입력합니다. **호스트 이름**은 `adminuser@PublicDNSName`과 유사합니다. **포트**에 2200을 입력합니다.

    ![PuTTY 구성 1](media/putty1.png)

4. **SSH > 인증**을 선택합니다. 인증을 위한 개인 키 파일(.ppk 형식)에 경로를 추가합니다. [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)과 같은 도구를 사용하여 클러스터를 만드는 데 사용되는 SSH 키에서 이 파일을 생성할 수 있습니다.

    ![PuTTY 구성 2](media/putty2.png)

5. **SSH > 터널**을 선택하고 다음 전달된 포트를 구성합니다.

    * **원본 포트:** DC/OS의 경우 80 또는 Swarm의 경우 2375를 사용합니다.
    * **대상:** localhost:80(DC/OS) 또는 localhost:2375(Swarm)를 사용합니다.

    다음 예제에서는 DC/OS에 대해 구성되었지만 Docker Swarm에 대한 구성도 이와 유사합니다.

    > [!NOTE]
    > 이 터널을 만들 때 포트 80은 사용 중이 아니어야 합니다.
    > 

    ![PuTTY 구성 3](media/putty3.png)

6. 완료하면 **세션 > 저장**을 클릭하여 연결 구성을 저장합니다.

7. PuTTY 세션에 연결하려면 **열기**를 클릭합니다. 연결하면 PuTTY 이벤트 로그에서 포트 구성을 볼 수 있습니다.

    ![PuTTY 이벤트 로그](media/putty4.png)

DC/OS에 터널을 구성한 후에 다음에서 관련 끝점에 액세스할 수 있습니다.

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Docker Swarm에 대한 터널을 구성한 후에 Windows 설정을 열고 `DOCKER_HOST` 값을 가진 `:2375`(이)라는 시스템 환경 변수를 구성합니다. 그런 다음 Docker CLI를 통해 Swarm 클러스터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
클러스터에서 컨테이너를 배포하고 관리합니다.

* [Azure Container Service 및 Kubernetes로 작업](container-service-kubernetes-ui.md)
* [Azure 컨테이너 서비스 및 DC/OS로 작업](container-service-mesos-marathon-rest.md)
* [Azure 컨테이너 서비스 및 Docker Swarm으로 작업](container-service-docker-swarm.md)


