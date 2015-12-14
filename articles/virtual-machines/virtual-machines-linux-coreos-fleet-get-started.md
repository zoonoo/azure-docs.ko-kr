<properties
	pageTitle="CoreOS에서 Fleet 시작 | Microsoft Azure"
	description="Azure의 클래식 배포 모델을 사용하여 만든 CoreOS Linux VM 클러스터에서 Fleet 및 Docker를 사용하는 방법의 기본적인 예를 제공합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="11/16/2015"
	ms.author="danlep"/>

# Azure의 CoreOS VM 클러스터에서 fleet 시작

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).


이 문서에서는 [CoreOS] 가상 컴퓨터 클러스터에서 [fleet](https://github.com/coreos/fleet) 및 [Docker](https://www.docker.com/)를 사용하여 응용 프로그램을 실행하는 두 가지 간단한 예제를 설명합니다.

이러한 예제를 사용하려면 먼저 [Azure에서 CoreOS를 사용하는 방법]에 설명된 대로 3노드 CoreOS 클러스터를 설정하십시오. 완료되면 CoreOS 배포의 매우 기본적인 요소를 이해하고 작동하는 클러스터 및 클라이언트 컴퓨터를 가지게 됩니다. 우리는 이러한 예제에서 정확히 똑같은 클러스터 이름을 사용할 것입니다. 또한 이 예제에서는 사용자가 로컬 Linux 호스트를 사용하여 **fleetctl** 명령을 실행한다고 가정합니다. **fleetctl** 클라이언트에 대한 자세한 내용은 [클라이언트 사용](https://coreos.com/fleet/docs/latest/using-the-client.html)을 참조하세요.


## <a id='simple'>예제 1: Docker와 Hello World</a>

여기에는 단일 Docker 컨테이너에서 실행되는 간단한 "Hello World" 응용 프로그램이 있습니다. 이는 [BusyBox Docker Hub 이미지]를 사용합니다.

Linux 클라이언트 컴퓨터에서 원하는 텍스트 편집기를 사용하여 다음 **systemd** 단위 파일을 만들고 이름을 `helloworld.service`로 지정합니다. (구문에 대한 세부 정보는 [단위 파일]을 참조하십시오.)

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

이제 CoreOS 클러스터에 연결하고 다음 **fleetctl** 명령을 실행하여 단위를 시작합니다. 출력에 단위가 시작되었다는 사실과 현재 위치가 표시됩니다.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]**-tunnel** 매개 변수 없이 원격 **fleetctl** 명령을 실행하려면 필요에 따라 FLEETCTL\_TUNNEL 환경 변수를 설정하여 요청을 터널링합니다. 예제: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


컨테이너에 연결하여 서비스의 출력을 볼 수 있습니다.

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

단위 정리, 중지 및 업로드.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>예제 2: 항상 사용 가능한 nginx 서버</a>

CoreOS, Docker 및 **fleet** 사용 시 한 가지 이점은 가용성이 높은 방식으로 손쉽게 서비스를 실행할 수 있다는 것입니다. 이 예제에서는 nginx 웹 서버를 실행하는 동일한 컨테이너 3개로 구성된 서비스를 배포합니다. 컨테이너는 클러스터의 세 VM에서 실행됩니다. 이 예제는 [fleet로 컨테이너 시작]과 유사하며 [nginx Docker Hub 이미지]를 사용합니다.

>[AZURE.IMPORTANT]항상 사용 가능한 웹 서버를 실행하려면 가상 컴퓨터(공용 포트 80, 개인 포트 80)에서 부하 분산된 HTTP 끝점을 구성해야 합니다. CoreOS 클러스터를 만든 후 Azure 클래식 포털 또는 **azure vm endpoint** 명령을 사용하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [부하 분산된 집합 구성]을 참조하십시오.

클라이언트 컴퓨터에서 원하는 텍스트 편집기를 사용하여 nginx@.service라는 이름의 **systemd** 템플릿 단위 파일을 만드십시오. 이 간단한 템플릿은 nginx@1.service, nginx@2.service 및 nginx@3.service라고 명명된 세 가지 개별 인스턴스를 시작하는 데 사용됩니다.

```
[Unit]
Description=High Availability Nginx
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --rm --name nginx1 -p 80:80 nginx
ExecStop=/usr/bin/docker stop nginx1

[X-Fleet]
X-Conflicts=nginx@*.service
```

>[AZURE.NOTE]`X-Conflicts` 특성은 CoreOS에 특정 CoreOS 호스트에서 이 컨테이너의 한 인스턴스만 실행할 수 있다고 알려줍니다. 자세한 내용은 [단위 파일]을 참조하십시오.

이제 CoreOS 클러스터에서 단위 인스턴스를 시작합니다. 3 개의 서로 다른 컴퓨터에서 실행 중인 것을 볼 수 있습니다.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start nginx@{1,2,3}.service

unit nginx@3.service launched on 00c927e4.../100.79.62.16
unit nginx@1.service launched on 62f0f66e.../100.79.86.62
unit nginx@2.service launched on df85f2d1.../100.78.126.15

```
단위 중 하나에서 실행 중인 웹 서버에 연결하기 위해 CoreOS 클러스터를 호스팅하는 클라우드 서비스에 간단한 요청을 보냅니다.

`curl http://coreos-cluster.cloudapp.net`

nginx 서버에서 다음과 비슷한 기본 텍스트가 반환됩니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

클러스터에 있는 하나 이상의 가상 컴퓨터를 종료하면 웹 서비스가 계속 실행되는지 확인할 수 있습니다.

완료되면 단위를 중지하고 언로드하십시오.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop nginx@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload nginx@{1,2,3}.service

```

## 다음 단계

* 이렇게 하면 Azure에서 3노드 CoreOS 클러스터로 더 많은 작업을 시도할 수 있습니다. 더 복잡한 클러스터를 만들고 Docker를 사용하여 더 흥미로운 응용 프로그램을 만드는 방법을 살펴보려면 [Tim Park의 CoreOS 자습서], [Patrick Chanezon의 CoreOS 자습서], [Docker] 설명서 및 [CoreOS 개요]를 읽어보십시오.

* Azure 리소스 관리자에서 Fleet 및 CoreOS를 시작하려면 이 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/)을 사용합니다.

* Azure Linux VM에서 오픈 소스 환경을 사용하는 방법에 대한 자세한 내용은 [Azure의 오픈 소스 컴퓨팅 및 Linux]를 참조하세요.

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[CoreOS 개요]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park의 CoreOS 자습서]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon의 CoreOS 자습서]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Azure에서 CoreOS를 사용하는 방법]: virtual-machines-linux-coreos-how-to.md
[부하 분산된 집합 구성]: ../load-balancer/load-balancer-internet-getstarted.md
[fleet로 컨테이너 시작]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[단위 파일]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[BusyBox Docker Hub 이미지]: https://registry.hub.docker.com/_/busybox/
[nginx Docker Hub 이미지]: https://hub.docker.com/_/nginx/
[Azure의 오픈 소스 컴퓨팅 및 Linux]: virtual-machines-linux-opensource.md

<!---HONumber=AcomDC_1203_2015-->