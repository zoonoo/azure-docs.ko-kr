<properties
	pageTitle="CoreOS를 사용하는 방법 | Microsoft Azure"
	description="CoreOS, 클래식 배포 모델에서 Azure에 CoreOS 가상 컴퓨터 클러스터를 만드는 방법 및 기본적인 사용 방법에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Azure에서 CoreOS를 사용하는 방법

이 항목에서는 [CoreOS]에 대해 설명하며 이 운영 체제를 빠르게 이해할 수 있도록 Azure에서 CoreOS 가상 컴퓨터 3개로 구성된 클러스터를 만드는 방법을 보여 줍니다. 여기서는 [Azure의 CoreOS], [Tim Park의 CoreOS 자습서] 및 [Patrick Chanezon의 CoreOS 자습서]에 포함된 매우 기본적인 CoreOS 배포 및 예제의 요소를 사용하여 CoreOS 배포의 기본 구조를 이해하고 정상적으로 실행되는 3개 가상 컴퓨터의 클러스터를 만들기 위한 최소 요구 사항을 제시합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).


## CoreOS, 클러스터 및 Linux 컨테이너

CoreOS는 [Docker] 컨테이너를 비롯한 Linux 컨테이너만을 패키징 메커니즘으로 사용하는 매우 커질 수 있는 VM 클러스터를 빠르게 만들 수 있도록 지원하는 경량 Linux 버전이며, 다음을 지원합니다.

+ 매우 높은 수준의 자동화
+ 보다 쉬우며 일관된 응용 프로그램 배포
+ 응용 프로그램 수준과 시스템 수준의 확장성

이러한 목표를 지원하는 CoreOS 기능은 대략적으로 다음과 같습니다.

1. 원 패키지 시스템:CoreOS는 속도, 일관성, 배포를 쉽게 하기 위해 Linux 컨테이너에서 실행되는 Linux 컨테이너 이미지만을 실행합니다.
2. 운영 체제 업데이트가 원자 방식으로 수행되므로 운영 체제를 단일 엔터티로 업데이트하고 알려진 상태로 쉽게 롤백할 수 있습니다.
3. 동적 VM 및 클러스터 통신과 관리를 위한 [etcd](https://github.com/coreos/etcd) 및 [fleet](https://github.com/coreos/fleet) 데몬(서비스)이 기본 제공됩니다.

위의 항목은 CoreOS 및 해당 기능에 대한 매우 일반적인 설명입니다. CoreOS에 대한 전체 설명은 [CoreOS 개요]를 참조하세요.

## 보안 고려 사항
현재 CoreOS는 클러스터에 대해 SSH를 실행할 수 있는 사용자에게 클러스터 관리 권한이 있다고 가정합니다. 따라서 CoreOS 클러스터는 테스트 및 개발 환경에서는 수정하지 않아도 매우 유용하지만 프로덕션 환경에서는 추가 보안 기능을 적용해야 합니다.

## Azure에서 CoreOS를 사용하는 방법

이 섹션에서는 [Azure 명령줄 인터페이스(Azure CLI)]를 사용하여 3대의 CoreOS 가상 컴퓨터로 Azure 클라우드 서비스를 만드는 방법을 설명합니다. 기본적인 단계는 다음과 같습니다.

1. CoreOS 가상 컴퓨터와의 통신을 보호하기 위한 SSH 인증서와 키를 만듭니다.
2. 상호 통신을 위한 클러스터의 etcd ID를 가져옵니다.
3. [YAML] 형식의 cloud-config 파일을 만듭니다.
4. 새로운 Azure 클라우드 서비스 및 3대의 CoreOS VM을 만들기 위해 Azure CLI를 사용합니다.
5. Azure VM에서 CoreOS 클러스터를 테스트합니다.
6. localhost에서 CoreOS 클러스터를 테스트합니다.

### 통신용 공용 키와 개인 키 만들기

[Azure에서 Linux 환경의 SSH를 사용하는 방법](virtual-machines-linux-use-ssh-key.md)의 지침에 따라 SSH용 공용 키 및 개인 키를 만듭니다. 기본 단계는 아래 지침에 나와 있습니다. 이러한 키를 사용하여 클러스터의 VM에 연결해 VM이 작동하며 서로 통신할 수 있는지를 확인합니다.

> [AZURE.NOTE] 이 항목에서는 사용자가 이러한 키를 갖고 있지 않다고 가정하며, 해당 사용자는 명확한 작업을 위해 `myPrivateKey.pem` 및 `myCert.pem` 파일을 만들어야 합니다. 이미 공용 키와 개인 키 쌍을 `~/.ssh/id_rsa`에 저장한 경우, `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem`을 입력하여 Azure에 업로드해야하는 .pem 파일을 가져올 수 있습니다.

1. 작업 디렉터리에서, `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem`을 입력하여 개인 키를 만들고 연결된 x.509 인증서를 만듭니다.

2. 개인 키의 소유자가 파일을 읽고 쓸 수 있도록 어설션 하려면 `chmod 600 myPrivateKey.key`을 입력합니다.

이제 작업 디렉터리에 `myPrivateKey.key`과 `myCert.pem` 파일이 모두 포함되어 있습니다.


### 클러스터의 etcd ID 가져오기

CoreOs의 `etcd` 데몬은 클러스터의 모든 노드를 자동으로 쿼리하기 위한 검색 ID가 필요합니다. 검색 ID를 검색하여 `etcdid` 파일에 저장하려면, 다음을 입력합니다.

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Create a cloud-config file

계속해서 같은 작업 디렉터리에서, 다음 텍스트를 포함하는 평소에 사용하는 텍스트 에디터로 파일을 만들고 `cloud-config.yaml`로 저장합니다. 파일은 원하는 이름으로 저장하면 되지만 다음 단계에서 VM을 만들 때 **azure vm create** 명령의 **--custom-data** 옵션에서 이 파일 이름을 참조해야 합니다.

> [AZURE.NOTE] `cat etcdid`을 입력하여 사용자가 위에서 만든 `etcdid` 파일에서 etcd 검색 ID를 검색한 후 다음 `cloud-config.yaml` 파일의 `<token>`을 `etcdid` 파일에서 생성된 숫자로 바꾸세요. 작업 종료 시 클러스터의 유효성을 검사할 수 없는 경우 이 단계를 건너뛰었기 때문일 수 있습니다.

```
#cloud-config

coreos:
  etcd:
    # generate a new token for each unique cluster from https://discovery.etcd.io/new
    discovery: https://discovery.etcd.io/<token>
    # deployments across multiple cloud services will need to use $public_ipv4
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
```

cloud-config 파일에 대한 전체 정보는 CoreOS 설명서의 [cloud-config 사용](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/)을 참조하세요.

### 새로운 CoreOS VM을 만들려면 Azure CLI를 사용합니다
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. [Azure 명령줄 인터페이스(Azure CLI)]를 설치하고(아직 설치하지 않은 경우) 회사 또는 학교 ID를 사용하여 로그인 하거나 .publishsettings 파일을 다운로드한 다음 사용자 계정으로 가져옵니다.
2. CoreOS 이미지를 찾습니다. 언제든 사용 가능한 이미지를 찾으려면 `azure vm image list | grep CoreOS`를 입력하고 다음과 유사한 결과 목록을 확인해야 합니다.

	데이터:2b171e93f07c4903bcad35bda10acf22\_\_CoreOS-Stable-522.6.0 Public Linux

3. `azure service create <cloud-service-name>`을 입력하여 기본 클러스터에 대해 클라우드 서비스를 만듭니다. 여기서 <*cloud-service-name*>은 CoreOS 클라우드 서비스의 이름입니다. 이 샘플에서는 이름으로 **`coreos-cluster`**을 사용합니다. 클라우드 서비스 내에서 CoreOS VM 인스턴스를 만들려면 선택하는 이름을 다시 사용해야 합니다.

	한 가지 참고 사항: [Azure 포털](https://portal.azure.com)에서 지금까지 수행한 작업을 확인하려면 다음 이미지에 나와 있는 것처럼 클라우드 서비스 이름이 리소스 그룹이자 도메인임을 확인할 수 있습니다.

	![][CloudServiceInNewPortal]

4. 클라우드 서비스에 연결한 다음 **azure vm create** 명령을 사용하여 서비스 내에 새 CoreOS VM을 만듭니다. **--ssh-cert** 옵션을 통해 X.509 인증서의 위치를 전달합니다. 다음을 입력하여 첫 번째 VM 이미지를 만듭니다. 이때 **coreos-cluster**는 앞에서 만든 클라우드 서비스 이름으로 바꿉니다.

	```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location="West US" 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. 4단계의 명령을 반복하여 두 번째 노드를 만듭니다. 이때 **--vm-name** 값은 **node-2**로 바꾸고 **--ssh** 포트 값은 2022로 바꿉니다.

6. 4단계의 명령을 반복하여 세 번째 노드를 만듭니다. 이때 **--vm-name** 값은 **node-3**으로 바꾸고 **--ssh** 포트 값은 3022로 바꿉니다.

아래 스크린샷에서 CoreOS 클러스터가 포털에 표시되는 방식을 확인할 수 있습니다.

![][EmptyCoreOSCluster]

### Azure VM에서 CoreOS 클러스터를 테스트합니다.

클러스터를 테스트하려면 현재 작업 디렉터리에 있는지 확인한 후 **ssh**를 사용하여 **node-1**에 연결합니다. 이때 다음을 입력하여 개인 키를 전달합니다.

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

연결되면 `sudo fleetctl list-machines`을 입력하며 클러스터가 클러스터 내의 모든 VM을 이미 식별했는지 여부를 확인합니다. 그러면 다음과 같은 응답이 표시됩니다.


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### localhost에서 CoreOS 클러스터를 테스트합니다.

마지막으로 로컬 Linux 클라이언트에서 CoreOS 클러스터를 테스트해보겠습니다. **npm**을 사용하여 **fleetctl**을 설치할 수 있습니다. 또는 로컬 클라이언트에서 **fleet**를 설치하고 **fleetctl**을 구축하고자 할 수 있습니다. **fleet**에는 **golang**이 필요하므로 다음을 입력하여 이 항목을 먼저 설치해야 할 수 있습니다.

`sudo apt-get install golang`

그런 후에 다음을 입력하여 github에서 **fleet** 리포지토리를 복제합니다.

`git clone https://github.com/coreos/fleet.git`

`fleet` 디렉터리로 변경하고 다음을 입력하여 **fleet**을 구축합니다.

`./build`

그리고 마지막으로 손쉽게 사용할 수 있도록 **fleet**를 배치합니다. 구성에 따라 **sudo**가 필요할 수도 있고 필요하지 않을 수도 있습니다.

`cp bin/fleetctl /usr/local/bin`

다음을 입력하여 **fleet**가 작업 디렉터리의 `myPrivateKey.key`에 액세스할 수 있는지 확인합니다.

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE] `~/.ssh/id_rsa` 키를 이미 사용하고 있으면 `ssh-add ~/.ssh/id_rsa`와 함께 이 키를 추가합니다.

이제 **node-1**에서 사용한 것과 같은 **fleetctl** 명령을 사용하되 일부 원격 인수를 전달하여 원격으로 테스트를 수행할 수 있습니다.

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

결과는 정확히 다음과 같습니다.


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## 다음 단계

지금까지 Azure에서 실행되는 3개 노드 CoreOS 클러스터를 만들었습니다. 여기에서, 더 복잡한 클러스터를 만드는 방법과 Docker를 사용하는 법 그리고 더 흥미로운 응용 프로그램을 작성하는 방법을 탐색할 수 있습니다. 몇 가지 빠른 예를 실행하려면 [Azure의 CoreOS에서 Fleet 시작]을 참조하세요.

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Azure 명령줄 인터페이스(Azure CLI)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[CoreOS 개요]: https://coreos.com/using-coreos/
[Azure의 CoreOS]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park의 CoreOS 자습서]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon의 CoreOS 자습서]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Azure의 CoreOS에서 Fleet 시작]: virtual-machines-linux-coreos-fleet-get-started.md

<!---HONumber=AcomDC_0204_2016-->