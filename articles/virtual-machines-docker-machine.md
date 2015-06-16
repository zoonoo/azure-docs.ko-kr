<properties
   pageTitle="Azure에서 docker-machine을 사용하는 방법"
   description="Ubuntu의 docker-machine을 Azure에서 작동 및 실행하는 방법을 설명합니다."
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
   ms.date="05/25/2015"
   ms.author="rasquill"/>

# Azure에서 docker-machine을 사용하는 방법

이 항목에서는 [Docker](https://www.docker.com/)를 [Machine](https://github.com/docker/machine) 및 [Azure CLI](https://github.com/Azure/azure-xplat-cli)와 함께 사용하여 Ubuntu를 실행하는 컴퓨터에서 Linux 컨테이너를 빠르고 쉽게 관리하기 위한 Azure 가상 컴퓨터를 만드는 방법을 설명합니다. 이 자습서에서는 설명을 위해 [busybox Docker 허브 이미지](https://registry.hub.docker.com/_/busybox/)와 [nginx Docker 허브 이미지](https://registry.hub.docker.com/_/nginx/)를 배포하고 웹 요청을 nginx 컨테이너로 라우팅하도록 컨테이너를 구성하는 방법을 보여 줍니다. (다른 플랫폼용으로 이러한 지침을 수정하는 방법에 대한 설명은 Docker **machine** 설명서에서 확인할 수 있습니다.)

이 자습서의 작업을 완료하려면 다음의 몇 가지 필수 구성 요소를 준비해야 합니다. 즉, 다음 항목을 설치해야 합니다.

1. [npm](https://docs.npmjs.com/) 및 [Node.js](http://nodejs.org/)
2. [Azure CLI](https://github.com/Azure/azure-xplat-cli)
3. [Docker 클라이언트](https://docs.docker.com/installation/)

이러한 항목을 위의 순서대로 설치하면 Ubuntu 컴퓨터가 자습서의 작업을 수행할 수 있는 상태가 됩니다. 이 자습서의 내용은 Debian과 같은 기타 dpkg 기반 distro에서 수행하는 작업과 거의 비슷합니다. 추가 요구 사항이나 단계가 확인되는 경우 댓글로 알려 주시기 바랍니다.

## docker-machine 다운로드 또는 빌드

**docker-machine**을 사용하는 가장 빠른 방법은 [릴리스 공유](https://github.com/docker/machine/releases)에서 해당 릴리스를 직접 다운로드하는 것입니다. 이 자습서에서 사용한 클라이언트 컴퓨터는 x64 컴퓨터에서 Ubuntu를 실행하므로 **docker-machine_linux-amd64** 이미지가 사용됩니다.

[Machine 빌드](https://github.com/docker/machine#contributing)의 단계에 따라 **docker-machine**을 직접 빌드할 수도 있습니다. 빌드를 수행하려면 1GB 이상의 콘텐츠를 다운로드해야 합니다. 이렇게 하면 환경을 원하는 방식으로 정확하게 사용자 지정할 수 있습니다.

> [AZURE.NOTE]사용 중인 플랫폼 버전에 대한 [바로 가기 링크](http://en.wikipedia.org/wiki/Symbolic_link)를 만들 수도 있지만 이 자습서에서는 동작을 매우 정확하게 시연하기 위해 바이너리를 직접 사용합니다. 따라서 이 자습서에서는 **docker-machine** 자습서에 나와 있는 `docker-machine env` 등의 명령이 아닌 `docker-machine_linux-amd64 env`를 사용합니다. 바로 가기 링크를 만들어도 되고 바이너리 이름을 직접 사용해도 되지만 사용 중인 이름을 변경할 때는 아래 지침에서도 해당 이름을 수정해야 합니다.

<br />

>  어떤 방법을 선택하든 명령줄에서 바이너리를 직접 호출하거나 **/usr/local/bin**과 같은 경로에 바이너리를 저장해야 합니다. `chmod +x` &lt;*`binaryName`*&gt;을 입력하여 실행 파일로 표시되는지 확인합니다. 여기서, &lt;*`binaryName`*&gt;은 Docker Machine 실행 파일의 이름입니다. 이 자습서에서는 **docker-machine_linux-amd64**를 사용합니다.

## Docker, Machine 및 Azure용 인증서 및 키 파일 만들기

이제 Azure에서 사용자 ID와 권한을 확인하는 데 필요한 인증서 및 키 파일과 **docker-machine**에서 원격으로 컨테이너를 만들고 관리하기 위해 Azure 가상 컴퓨터와 통신하는 데 필요한 인증서 및 키 파일을 만들어야 합니다. Docker에서 사용하기 위해 이러한 파일을 디렉터리에 이미 저장한 경우에는 다시 사용해도 됩니다. 그러나 **docker-machine**을 테스트하는 모범 사례는 이러한 파일을 별도의 디렉터리에 만들고 docker-machine이 해당 파일을 가리키도록 지정하는 것입니다.

> [AZURE.NOTE]**docker-machine**를 계속 다시 입력하기를 종료한 경우 동일한 인증서와 키 파일을 다시 사용해야 합니다. **docker-machine**은 클라이언트 인증서 집합을 만들며, 만든 모든 것을 `~/.docker/machine`에서 검토할 수 있습니다. 이러한 인증서를 다른 컴퓨터로 이동하는 경우에는 **docker-machine** 인증서 폴더도 이동해야 합니다. 작동 방식만을 확인하기 위해 다른 플랫폼에서 **docker-machine**을 사용하는 등의 경우 이와 같이 폴더를 이동하면 작업을 쉽게 수행할 수 있습니다.

Linux가 배포된 환경을 사용하는 경우에는 이러한 파일이 컴퓨터의 특정 위치에서 이미 제공될 수 있습니다. [Docker HTTPS 설명서에도 해당 단계가 설명되어 있습니다](https://docs.docker.com/articles/https/). 아래에서는 이 단계를 간단하게 설명합니다.

1. 로컬 폴더를 만들고 명령줄에서 해당 폴더로 이동한 후에 다음을 입력합니다.

		openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
		openssl pkcs12 -export -out mycert.pfx -in mycert.pem -name "My Certificate"

	인증서의 내보내기 암호를 입력하고 나중에 사용하도록 캡처하도록 여기에서 준비합니다. 그런 다음 입력합니다.

		openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

2. 인증서의 .cer 파일을 Azure에 업로드합니다. [Azure 포털](https://manage.windowsazure.com)에서 아래에 나와 있는 서비스 영역 왼쪽 아래의 **설정**을 클릭합니다.

	![][portalsettingsitem]

	그런 다음 **관리 인증서**를 클릭합니다.

	![][managementcertificatesitem]

	그런 후에 ![][uploaditem]를 **업로드**(페이지 아래쪽의)하여 이전 단계에서 만든 **mycert.cer** 파일을 업로드합니다.

3. 포털의 동일한 **설정** 창에서 **구독**을 클릭하고 VM을 만들 때 사용할 구독 ID를 확인합니다. 다음 단계에서 이 ID를 사용합니다. (Azure CLI 명령 `azure account list`을 사용하여 명령줄에서 구독 ID를 찾을 수도 있습니다. 계정에 포함된 각 구독의 구독 ID가 표시됩니다.)

4. **docker-machine create** 명령을 사용하여 Azure에서 docker 호스트 VM을 만듭니다. 이 명령을 실행하려면 이전 단계에서 방금 캡처한 구독 ID와 1단계에서 만든 **.pem** 파일의 경로가 필요합니다. 이 항목에서는 Azure 클라우드 서비스 및 VM 이름으로 "machine-name"을 사용하지만 실제 작업 시에는 고유한 이름으로 바꿔야 합니다. 또한 VM 이름을 입력해야 하는 기타 모든 단계에서 클라우드 서비스 이름을 사용해야 합니다. (이 예제에서는 **docker-machine** 바로 가기 링크가 아닌 전체 바이너리 이름을 사용합니다.)

		docker-machine_linux-amd64 create \
	    -d azure \
	    --azure-subscription-id="<subscription ID acquired above>" \
	    --azure-subscription-cert="mycert.pem" \
	    machine-name

	처음 두 단계에서 새 VM을 만들고 Linux Azure 에이전트를 로드한 후 새 VM을 업데이트해야 하므로 다음과 같은 화면이 표시됩니다.

		INFO[0001] Creating Azure machine...
	    INFO[0049] Waiting for SSH...
	    modprobe: FATAL: Module aufs not found.
	    + sudo -E sh -c sleep 3; apt-get update
	    + sudo -E sh -c sleep 3; apt-get install -y -q linux-image-extra-3.13.0-36-generic
	    E: Unable to correct problems, you have held broken packages.
	    modprobe: FATAL: Module aufs not found.
	    Warning: tried to install linux-image-extra-3.13.0-36-generic (for AUFS)
	     but we still have no AUFS.  Docker may not work. Proceeding anyways!
	    + sleep 10
	    + [ https://get.docker.com/ = https://get.docker.com/ ]
	    + sudo -E sh -c apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
	    gpg: requesting key A88D21E9 from hkp server keyserver.ubuntu.com
	    gpg: key A88D21E9: public key "Docker Release Tool (releasedocker) <docker@dotcloud.com>" imported
	    gpg: Total number processed: 1
	    gpg:               imported: 1  (RSA: 1)
	    + sudo -E sh -c echo deb https://get.docker.com/ubuntu docker main > /etc/apt/sources.list.d/docker.list
	    + sudo -E sh -c sleep 3; apt-get update; apt-get install -y -q lxc-docker
	    + sudo -E sh -c docker version
	    INFO[0368] "machine-name" has been created and is now the active machine.
	    INFO[0368] To point your Docker client at it, run this in your shell: $(docker-machine_linux-amd64 env machine-name)

    > [AZURE.NOTE]VM을 만드는 중이므로 준비 상태가 되려면 몇 분 정도 걸릴 수 있습니다. 기다리는 동안 VM이 **ReadyRole** 상태로 표시될 때까지 Azure CLI를 사용하여`azure vm list`를 입력해 새 Docker 호스트의 상태를 확인할 수 있습니다.

5. 터미널 세션에 대해 Docker 및 Machine 환경 변수를 설정합니다. 피드백의 마지막 줄은 **env** 명령을 즉시 실행하여 특정 Machine에서 Docker 클라이언트를 직접 사용하는 데 필요한 환경 변수를 내보내라는 메시지를 표시합니다.

		$(docker-machine_linux-amd64 env machine-name)

	이렇게 하면 로컬 Docker 클라이언트를 사용하여 Docker **machine**에서 만든 VM에 연결하기 위해 특수한 명령을 실행할 필요가 없습니다.

	    $ docker info
	    Containers: 0
	    Images: 0
	    Storage Driver: devicemapper
	     Pool Name: docker-8:1-131736-pool
	     Pool Blocksize: 65.54 kB
	     Backing Filesystem: extfs
	     Data file: /dev/loop0
	     Metadata file: /dev/loop1
	     Data Space Used: 305.7 MB
	     Data Space Total: 107.4 GB
	     Metadata Space Used: 729.1 kB
	     Metadata Space Total: 2.147 GB
	     Udev Sync Supported: false
	     Data loop file: /var/lib/docker/devicemapper/devicemapper/data
	     Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
	     Library Version: 1.02.82-git (2013-10-04)
	    Execution Driver: native-0.2
	    Kernel Version: 3.13.0-36-generic
	    Operating System: Ubuntu 14.04.1 LTS
	    CPUs: 1
	    Total Memory: 1.639 GiB
	    Name: machine-name
	    ID: W3FZ:BCZW:UX24:GDSV:FR4N:N3JW:XOC2:RI56:IWQX:LRTZ:3G4P:6KJK
	    WARNING: No swap limit support

> [AZURE.NOTE]이 자습서에서는 VM 하나를 만드는  **docker-machine**을 보여 줍니다. 그러나 위의 단계를 반복하여 가상 컴퓨터를 원하는 수만큼 만들 수 있습니다. 이렇게 하는 경우 Docker가 설치된 VM 간을 전환하는 가장 효율적인 방법은 **env** 명령을 인라인으로 사용하여 개별 명령에 대해 **docker** 환경 변수를 설정하는 것입니다. 예를 들어, 다른 VM에서**docker info**를 사용하려면 해당 VM에서 사용할 Docker 연결 정보에 `docker $(docker-machine env <VM name>) info` 및 **env** 명령을 입력합니다.

## Docker 및 Docker 허브의 이미지를 사용하여 원격으로 응용 프로그램 실행

이제 일반적인 방식으로 Docker를 사용하여 컨테이너에 응용 프로그램을 만들 수 있습니다. [busybox](https://registry.hub.docker.com/_/busybox/)를 사용하면 이 작업을 가장 쉽게 시연할 수 있습니다.

	    $  docker run busybox echo hello world
	    Unable to find image 'busybox:latest' locally
	    511136ea3c5a: Pull complete
	    df7546f9f060: Pull complete
	    ea13149945cb: Pull complete
	    4986bf8c1536: Pull complete
	    busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
	    Status: Downloaded newer image for busybox:latest
	    hello world

그러나 [Docker 허브](https://registry.hub.docker.com/)의 [nginx](https://registry.hub.docker.com/_/nginx/)와 같이 인터넷에서 즉시 찾을 수 있는 응용 프로그램을 만들 수도 있습니다.

> [AZURE.NOTE]**docker**가 이미지에 임의 포트를 할당하도록 하려면 **-P** 옵션을 사용하고, 컨테이너가 백그라운드에서 계속 실행되도록 하려면 **-d** 옵션을 사용합니다. 이 옵션을 사용하지 않는 경우 nginx를 시작하면 즉시 종료됩니다.

	$ docker run --name machinenginx -P -d nginx
    Unable to find image 'nginx:latest' locally
    30d39e59ffe2: Pull complete
    c90d655b99b2: Pull complete
    d9ee0b8eeda7: Pull complete
    3225d58a895a: Pull complete
    224fea58b6cc: Pull complete
    ef9d79968cc6: Pull complete
    f22d05624ebc: Pull complete
    117696d1464e: Pull complete
    2ebe3e67fb76: Pull complete
    ad82b43d6595: Pull complete
    e90c322c3a1c: Pull complete
    4b5657a3d162: Pull complete
    511136ea3c5a: Already exists
    nginx:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for nginx:latest
    5883e2ff55a4ba0aa55c5c9179cebb590ad86539ea1d4d367d83dc98a4976848

인터넷에서 해당 응용 프로그램을 찾으려면 Azure VM의 포트 80에서 공용 끝점을 만든 다음 nginx 컨테이너 포트에 해당 포트를 매핑합니다. 먼저 `docker ps -a`를 사용하여 컨테이너를 찾은 다음 **docker**에서 컨테이너의 포트 80에 할당한 포트를 찾습니다. 아래에는 포트 정보만 표시하도록 편집된 정보가 나와 있습니다. 실제로는 더 많은 정보가 표시됩니다.

	$ docker ps -a
    IMAGE               PORTS
    nginx:latest        0.0.0.0:49153->80/tcp, 0.0.0.0:49154->443/tcp
    busybox:latest

Docker가 컨테이너 포트 80을 VM 포트 49153에 할당했음을 확인할 수 있습니다. 이제 Azure CLI를 사용하여 외부 클라우드 서비스의 공용 포트 80을 VM의 포트 49153에 할당할 수 있습니다. 그러면 Docker에서 VM 포트 49153의 인바운드 tcp 트래픽을 nginx 컨테이너로 라우팅합니다.

	$ azure vm endpoint create machine-name 80 49153
    info:    Executing command vm endpoint create
    + Getting virtual machines
    + Reading network configuration
    + Updating network configuration
    info:    vm endpoint create command OK

평상시에 사용하는 브라우저를 열고 nginx 컨테이너를 확인해 봅니다.

![][nginx]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[Docker 사용자 가이드](https://docs.docker.com/userguide/)로 이동하여 Microsoft Azure에서 응용 프로그램을 만들어 봅니다. 또는 Azure](virtual-machines-docker-swarm)에서 [**docker** 및 swarm](https://github.com/docker/swarm)을 사용해 보고 Docker와 Azure에서 swarm을 사용하는 방법을 살펴봅니다.

<!--Image references-->
[nginx]: ./media/virtual-machines-docker-machine/nginxondocker.png
[portalsettingsitem]: ./media/virtual-machines-docker-machine/portalsettingsitem.png
[managementcertificatesitem]: ./media/virtual-machines-docker-machine/managementcertificatesitem.png
[uploaditem]: ./media/virtual-machines-docker-machine/uploaditem.png

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=58-->