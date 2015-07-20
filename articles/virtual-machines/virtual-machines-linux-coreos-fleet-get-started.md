<properties
	pageTitle="Azure의 CoreOS에서 fleet 시작"
	description="Azure의 CoreOS Linux 가상 컴퓨터에서 Fleet 및 Docker를 사용하는 방법에 대한 기본적인 예제를 제공합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="madhana"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/17/2015"
	ms.author="danlep"/>

# Azure의 CoreOS에서 fleet 시작

이 문서에서는 [CoreOS] 가상 컴퓨터 클러스터에서 [fleet](https://github.com/coreos/fleet) 및 [Docker](https://www.docker.com/)를 사용하여 응용 프로그램을 실행하는 두 가지 간단한 예제를 설명합니다.

이러한 예제를 사용하려면 먼저 [Azure에서 CoreOS를 사용하는 방법]에 설명된 대로 3노드 CoreOS 클러스터를 설정하십시오. 완료되면 CoreOS 배포의 매우 기본적인 요소를 이해하고 작동하는 클러스터 및 클라이언트 컴퓨터를 가지게 됩니다. 우리는 이러한 예제에서 정확히 똑같은 클러스터 이름을 사용할 것입니다. 또한 이 예제에서는 사용자가 로컬 Linux 호스트를 사용하여 **fleet** 명령을 사용한다고 가정합니다.




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

>[AZURE.NOTE]**-tunnel** 매개 변수 없이 원격 **fleetctl** 명령을 실행하려면 필요에 따라 FLEETCTL_TUNNEL 환경 변수를 설정하여 요청을 터널링합니다. 예제: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


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


## <a id='highavail'>예제 2: 가용성이 높은 Apache 서버</a>

CoreOS, Docker 및 **fleet** 사용 시 한 가지 이점은 가용성이 높은 방식으로 손쉽게 서비스를 실행할 수 있다는 것입니다. 이 예제에서는 Apache 웹 서버를 실행하는 동일한 컨테이너 3개로 구성된 서비스를 배포합니다. 컨테이너는 클러스터의 세 VM에서 실행됩니다. 이 예제는 [fleet로 컨테이너 시작]과 유사하며 [CoreOS Apache Docker Hub 이미지]를 사용합니다.

>[AZURE.NOTE]가용성이 높은 Apache 서버를 실행하려면 가상 컴퓨터(공용 포트 80, 개인 포트 80)에서 부하 분산된 HTTP 끝점을 구성해야 합니다. 이렇게 하려면 Azure 관리 포털 또는 **azure vm endpoint** 명령을 사용하여 CoreOS 클러스터를 만들어야 합니다. 자세한 내용은 [부하 분산된 집합 구성]을 참조하십시오.

클라이언트 컴퓨터에서 원하는 텍스트 편집기를 사용하여 apache@.service라는 이름의 **systemd** 템플릿 단위 파일을 만드십시오. 이 템플릿은 apache@1.service, apache@2.service 및 apache@3.service라고 명명된 세 가지 개별 인스턴스를 시작하는 데 사용됩니다.

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]`X-Conflicts` 특성은 CoreOS에 특정 CoreOS 호스트에서 이 컨테이너의 한 인스턴스만 실행할 수 있다고 알려줍니다. 자세한 내용은 [단위 파일]을 참조하십시오.

이제 CoreOS 클러스터에서 단위 인스턴스를 시작합니다. 3 개의 서로 다른 컴퓨터에서 실행 중인 것을 볼 수 있습니다.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
단위 중 하나에서 실행 중인 Apache 서버에 연결하기 위해 CoreOS 클러스터를 호스팅하는 클라우드 서비스에 간단한 요청을 보냅니다.

`curl http://coreos-cluster.cloudapp.net`

Apache 서버에서 다음과 비슷한 기본 텍스트가 반환됩니다.

```
<htm\l><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
```

클러스터에 있는 하나 이상의 가상 컴퓨터를 종료하면 Apache 서비스가 계속 실행되는지 확인할 수 있습니다.

완료되면 단위를 중지하고 언로드하십시오.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## 다음 단계

이렇게 하면 Azure에서 3노드 CoreOS 클러스터로 더 많은 작업을 시도할 수 있습니다. 더 복잡한 클러스터를 만들고 Docker를 사용하여 더 흥미로운 응용 프로그램을 만드는 방법을 살펴보려면 [Tim Park의 CoreOS 자습서], [Patrick Chanezon의 CoreOS 자습서], [Docker] 설명서 및 [CoreOS 개요]를 읽어보십시오.

Azure의 Linux VM에서 공개 소스 환경을 사용하는 방법에 대한 자세한 내용은 [Azure에서 Linux 및 공개 소스 컴퓨팅]을 참조하십시오.

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[CoreOS 개요]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park의 CoreOS 자습서]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon의 CoreOS 자습서]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Azure에서 CoreOS를 사용하는 방법]: virtual-machines-linux-coreos-how-to.md
[부하 분산된 집합 구성]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[fleet로 컨테이너 시작]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[단위 파일]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[BusyBox Docker Hub 이미지]: https://registry.hub.docker.com/_/busybox/
[CoreOS Apache Docker Hub 이미지]: https://registry.hub.docker.com/u/coreos/apache/
[Azure에서 Linux 및 공개 소스 컴퓨팅]: virtual-machines-linux-opensource.md
 

<!---HONumber=July15_HO2-->