
<properties
   pageTitle="Azure에서 Docker와 swarm 사용 시작"
   description="Docker VM 확장을 사용하여 VM 그룹을 만들고 swarm을 사용하여 Docker 클러스터를 만드는 방법을 설명합니다."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/24/2015"
   ms.author="rasquill"/>

# Docker 및 swarm을 사용하는 방법

이 항목에서는 [docker](https://www.docker.com/)와 [swarm](https://github.com/docker/swarm)을 사용하여 Azure에서 swarm으로 관리되는 클러스터를 만드는 매우 간단한 방법을 설명합니다. 여기서는 Azure에서 가상 컴퓨터 4대를 만드는데, 그 중 하나는 swarm 관리자로 사용되고 나머지 3대는 Docker 호스트 클러스터에 포함됩니다. 작업을 완료한 후에는 swarm을 사용하여 클러스터를 확인한 다음 클러스터에서 Docker 사용을 시작할 수 있습니다. 또한 이 항목의 Azure CLI 호출은 서비스 관리(asm) 모드를 사용합니다.

> [AZURE.NOTE]여기서 소개하는 swarm은 초기 소프트웨어 버전이므로 균형이 적절하게 조정된 제어 가능한 대규모 Docker 컨테이너 클러스터를 만들려면 Azure에서 해당 사용 방법에 대한 업데이트를 다시 확인하고, Docker swarm 설명서에서 해당 기능도 모두 확인해야 합니다. <!-- --> 또한 이 항목에서는 각 도구가 서로 독립성을 유지하면서 연동되는 방식을 보여 주기 위해 **docker-machine**을 사용하지 *않고* swarm 및 Azure CLI를 사용합니다. **docker-machine**에 포함된 **--swarm** 스위치를 사용하면 **docker-machine**을 통해 swarm에 노드를 직접 추가할 수 있습니다. 예제를 확인하려면 [docker-machine](https://github.com/docker/machine) 설명서를 참조하세요. Azure VM에서 **docker-machine**을 실행해 본 적이 없다면 [Azure에서 docker-machine을 사용하는 방법](virtual-machines-docker-machine.md)을 참조하세요.

## Azure 가상 컴퓨터를 사용하여 Docker 호스트 만들기

이 항목에서는 VM 4대를 만들지만 실제로는 원하는 수의 VM을 만들 수 있습니다. *&lt;password&gt;*를 선택한 암호로 바꿔 다음을 호출합니다.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

호출이 완료되면 **azure vm list**를 사용하여 Azure VM을 확인할 수 있습니다.

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## swarm master VM에 swarm 설치

이 항목에서는 [Docker swarm 설명서에서 제공되는 설치의 컨테이너 모델](https://github.com/docker/swarm#1---docker-image)을 사용하지만 **swarm-master**에 대해 SSH를 수행할 수도 있습니다. 이 모델에서는 swarm을 실행하는 Docker 컨테이너로 **swarm**을 다운로드합니다. 아래에서는 *Docker를 사용하여 랩톱에서 원격으로* 이 단계를 수행하여 **swarm-master** VM에 연결한 다음 해당 VM이 클러스터 ID 만들기 명령인 **swarm create**를 사용하도록 지정합니다. **swarm**은 클러스터 ID를 사용하여 swarm 그룹 멤버를 검색합니다. 리포지토리를 복제하여 직접 빌드할 수도 있습니다. 그러면 VM을 완전히 제어할 수 있으며 디버깅을 수행할 수 있습니다.

    $ docker --tls -H tcp://swarm-master.cloudapp.net:4243 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

마지막 줄에 클러스터 ID가 나와 있습니다. 이 ID는 노드 VM을 swarm master에 연결하여 "swarm"을 만들 때 다시 사용해야 하므로 다른 위치에 복사해 둡니다. 이 예제에서 클러스터 ID는 **36731c17189fd8f450c395db8437befd**입니다.

> [AZURE.NOTE]부연 설명을 하자면, 여기서는 로컬 Docker 설치를 사용하여 Azure의 **swarm-master** VM에 연결한 다음 **swarm-master** 명령을 사용하여 **create** 명령을 다운로드, 설치 및 실행합니다. 그러면 나중에 검색용으로 사용할 클러스터 ID가 반환됩니다. <!-- --> 이를 확인하려면, `docker -H tcp://`*&lt;hostname&gt;* ` images`를 실행하여 **swarm-master** 컴퓨터의 컨테이너 프로세스를 나열한 다음 비교를 위해 다른 노드의 프로세스를 나열합니다. 여기서는 **--rm** 스위치를 사용하여 이전 swarm 명령을 실행했으므로 컨테이너는 작업이 완료된 후 제거되었으며, 따라서 **docker ps -a** 사용 시 아무 항목도 반환되지 않습니다.


        $ docker --tls -H tcp://swarm-master.cloudapp.net:4243 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:4243 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
> **docker**에 대해 잘 알고 있다면 아직 이미지를 다운로드하여 실행하지 않았으므로 다른 노드에도 항목이 없음을 알 수 있습니다.

## Docker 클러스터에 노드 VM 연결

각 노드에 대해 Azure CLI를 사용하여 끝점 정보를 나열합니다. 아래에서는 **swarm-node-1** Docker에 대해 이 작업을 수행하여 노드의 Docker 포트를 가져옵니다.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       4243         4243          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


**docker** 및 `-H` 옵션을 사용하여 노드 VM의 Docker 클라이언트를 가리킨 다음, 클러스터 ID와 노드의 Docker 포트(**--addr** 사용)를 전달하여 해당 노드를 작성 중인 swarm에 연결합니다.

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:4243 run -d swarm join --addr=138.91.112.194:4243 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

노드가 정상적으로 연결되었습니다. **swarm**이 **swarm-node-1**에서 실행되는지 확인하려면 다음과 같이 입력합니다.

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:4243 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

클러스터의 다른 모든 노드에 대해 이 작업을 반복합니다. 여기서는 **swarm-node-2** 및 **swarm-node-3**에 대해 확인을 수행합니다.

## swarm 클러스터 관리 시작

    $ docker --tls -H tcp://swarm-master.cloudapp.net:4243 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

위 코드를 실행하면 클러스터의 노드 목록을 확인할 수 있습니다.

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:4243 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

swarm에서 직접 코드를 실행해 보세요. 관련 지침은 [https://github.com/docker/swarm/](https://github.com/docker/swarm/) 또는 [비디오](https://www.youtube.com/watch?v=EC25ARhZ5bI)를 참조하세요.

<!-- links -->

[docker-machine-azure]: virtual-machines-docker-machine.md

<!---HONumber=58--> 