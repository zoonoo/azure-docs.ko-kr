<properties title="Using the Docker Virtual Machines Extension for Linux on Azure" pageTitle="Using the Docker VM Extension for Linux on Azure" description="Describes Docker and the Azure Virtual Machines extensions, and shows how to programmatically create Virtual Machines on Azure that are docker hosts from the command line using the azure-cli command interface." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="08/21/2014" ms.author="ralph.squillace@microsoft.com"></tags>

# Azure에서 Linux용 Docker 가상 컴퓨터 확장을 사용하는 방법

[Docker][Docker]는 공유 리소스의 데이터와 계산을 격리시키는 한 가지 방법으로 가상 컴퓨터 대신 [Linux 컨테이너][Linux 컨테이너]를 사용하는 가장 많이 사용되는 가상화 방법 중 하나입니다. [Azure Linux 에이전트][Azure Linux 에이전트]에 대한 Docker VM 확장을 사용하여 Azure에 응용 프로그램의 컨테이너를 개수에 제한없이 호스트하는 Docker VM을 만들 수 있습니다.

이 항목에서는 이 [MS Open Tech 블로그 알림][MS Open Tech 블로그 알림]을 따르고 다음을 설명합니다.

-   [Docker 및 Linux 컨테이너][Docker 및 Linux 컨테이너]
-   [Azure와 함께 Docker VM 확장을 사용하는 방법][Azure와 함께 Docker VM 확장을 사용하는 방법]
-   [Linux 및 Windows용 가상 컴퓨터 확장][Linux 및 Windows용 가상 컴퓨터 확장]
-   [Azure용 컨테이너 및 컨테이너 관리 리소스][Azure용 컨테이너 및 컨테이너 관리 리소스]

## Docker 및 Linux 컨테이너

[Docker][Docker]는 공유 리소스의 데이터와 계산을 격리시키는 한 가지 방법으로 가상 컴퓨터 대신 [Linux 컨테이너][Linux 컨테이너]를 사용하는 가장 많이 사용되는 가상화 방법 중 하나이며, 응용 프로그램을 작성 또는 어셈블하고 다른 Docker 컨테이너 간에 분산할 수 있도록 하는 기타 서비스를 제공합니다.

Docker 및 Linux 컨테이너는 [하이퍼바이저][하이퍼바이저](예: Windows Hyper-V 및 Linux의 [KVM][KVM])가 아닙니다(다른 많은 예가 있음). 하이퍼바이저는 기본 운영 체제를 가상화하여 전체 운영 체제를 응용 프로그램처럼 하이퍼바이저 내부에서 실행할 수 있도록 합니다.

하이퍼바이저 접근 방법은 "게스트" 가상 컴퓨터에 "호스트" 운영 체제에 대한 액세스 권한이 없고 하이퍼바이저의 리소스만 사용할 수 있으므로 중요한 보안 장점을 제공합니다. 그러나 보다 큰 처리 및 저장소 오버헤드, 게스트 운영 체제의 전체 복제로 인해 비교적 느린 새 가상 컴퓨터 시작 시간 등의 단점이 있습니다.

#### Docker 및 Linux 컨테이너

Docker 및 기타 *컨테이너* 접근 방법은 Linux 커널의 프로세스 및 파일 시스템 격리 기능을 통해 달리 격리된 컨테이너에 커널 기능만 노출하여 사용되는 시작 시간과 필요한 처리 및 저장소 오버헤드 둘 다를 크게 줄였습니다. 컨테이너 내부에서 파일 시스템과 커널 기능은 실행 중인 유일한 응용 프로그램인 것처럼 응용 프로그램에 표시됩니다.

또한 Docker는 커뮤니티에서 다양한 컨테이너 이미지를 로드하고 고유한 이미지를 신속하게 작성 및 로드할 수 있게 해주는 여러 컨테이너 관리 기능을 제공합니다. Docker 정의 및 실제 작동 방식에 대한 자세한 내용은 [Docker 정의][Docker 정의]를 참조하세요.

대부분의 기술과 마찬가지로 상당한 장점과 일부 단점이 있습니다. 컨테이너는 호스트 컴퓨터의 커널에 대한 액세스를 공유하므로 악성 코드가 침투할 수 있으면 호스트 컴퓨터뿐 아니라 다른 컨테이너에도 액세스할 수 있게 됩니다. 컨테이너 시스템 보안을 강화하기 위한 [Docker 권장 사항][Docker 권장 사항]에는 그룹 정책 또는 [역할 기반 보안][역할 기반 보안] 추가(예: [SELinux][SELinux] 또는 [AppArmor][AppArmor]), 컨테이너에 부여되는 커널 기능 최대한 축소 등이 있습니다. 또한 Docker와 같은 컨테이너를 사용한 보안 접근 방법을 설명하는 다른 많은 문서가 인터넷에 있습니다.

## Azure와 함께 Docker VM 확장을 사용하는 방법

Azure와 함께 Docker VM 확장을 사용하려면 [azure-cli][azure-cli] 0.8.6보다 최신 버전을 설치해야 합니다(이 문서를 작성할 당시 최신 버전은 0.8.7임). Mac과 Linux 둘 다에 azure-cli를 설치할 수 있습니다.

> [WACOM.NOTE] Microsoft Windows에 azure-cli를 설치할 수도 있습니다. 그러나 Docker는 Linux 커널 종속성으로 작성되었으므로 Windows를 Docker 클라이언트로 사용하려면 Hyper-V 또는 다른 하이퍼바이저 내부에 전체 Linux 배포를 가상 컴퓨터로 호스트해야 합니다. 작업이 완료되면 azure-cli 및 이 문서와 Docker 문서에 있는 Docker 명령을 사용할 수 있습니다. Docker 자체에 설치 프로그램 [Boot2Docker][Boot2Docker]가 있으며, 동일한 설치를 자동화하는 데 사용할 수도 있습니다.

Azure에서 Docker를 사용하는 전체 프로세스는 간단합니다.

-   Azure를 제어하려는 컴퓨터(Windows의 경우 가상 컴퓨터로 실행 중인 Linux 배포임)에 azure-cli 명령줄 도구와 해당 종속성을 설치합니다.
-   azure-cli Docker 명령을 사용하여 Azure에 VM Docker 호스트를 만듭니다.
-   로컬 Docker 명령을 사용하여 Azure의 Docker VM에서 Docker 컨테이너를 관리합니다.

> [WACOM.NOTE] azure-cli(명령줄 인터페이스)는 현재 Azure에 Docker 컨테이너를 호스트할 Docker 제어 VM을 만드는 유일한 방법입니다. 일반적인 설치 문서는 [여기][여기]에 있습니다. 아래 섹션에서는 다양한 운영 체제에 매끄럽게 설치하기 위한 추가 제안 사항을 제공합니다.

### Linux에 azure-cli 설치

Linux의 azure-cli 설치에는 nodejs가 필요한 [노드 패키지 관리자(npm)][노드 패키지 관리자(npm)]가 필요하므로 선택한 플랫폼에 따라 즐겨 찾는 패키지 관리자를 사용하여 nodejs를 설치합니다. npm이 설치되어 있을 경우 azure-cli 패키지를 가져오려면 다음을 입력합니다.

    npm install -g azure-cli

이 경우 azure-cli 패키지가 전역으로 설치됩니다. 설치를 확인하려면 명령줄에 `azure`를 입력합니다. 잠시 후에 사용 가능한 기본 명령을 나열하는 azure-cli ASCII 아트가 표시됩니다. 설치가 제대로 작동한 경우 `azure help vm`을 입력하면 나열된 명령 중 하나가 "docker"임을 확인할 수 있습니다.

> [WACOM.NOTE] Ubuntu 14.04 LTS 설치를 사용하는 경우 이미지에 약간 다른 노드 설치가 있으며 추가 작업이 필요할 수 있습니다. 효과적인 한 가지 제안 사항은 [여기][1]의 **PPA를 사용하여 설치하는 방법** 섹션에 있습니다. 이 섹션에서는 최신 버전의 nodejs를 직접 설치하는 방법을 설명하며 Ubuntu 14.04 LTS 배포에 효과적인 것 같습니다.

또는 대부분의 Linux 구성 요소처럼 소스를 복제하고 컴파일한 다음 로컬에 설치할 수도 있습니다. 관련 지침은 [][azure-cli]<https://github.com/Azure/azure-sdk-tools-xplat></a>에 있습니다.

### Mac에 azure-cli 설치

Mac에서 azure-cli를 설치하는 가장 쉬운 방법도 동일한 다음 명령과 함께 npm을 사용하는 것입니다. `npm install -g azure-cli`. 그러나 [Mac installer][Mac installer]를 사용할 수도 있습니다. Linux 및 Windows와 마찬가지로 관련 명령 프롬프트에 `azure`를 입력하여 azure-cli가 설치되었는지 확인할 수 있습니다.

### Azure 계정에 azure-cli 연결

azure-cli를 사용하려면 먼저 Azure 계정 자격 증명을 사용자 플랫폼의 azure-cli에 연결해야 합니다. [Azure 구독에 연결하는 방법][Azure 구독에 연결하는 방법] 섹션에서는 **.publishsettings** 파일을 다운로드 및 가져오거나 azure-cli 명령줄을 조직 ID에 연결하는 방법에 대해 설명합니다. 위 문서에서는 인증 및 권한 부여 방법 둘 다의 단계를 설명합니다.

> [WACOM.NOTE] 사용하는 권한 부여 방법에 따라 동작에 몇 가지 차이점이 있으므로 위 문서를 읽고 서로 다른 기능을 이해해야 합니다.

### Docker 설치 및 Azure용 Docker VM 확장 사용

이제 azure-cli가 설치되고 Azure 계정에 연결된 컴퓨터(또는 해당 컴퓨터의 가상 컴퓨터)가 있습니다. 사용자 컴퓨터에 Docker를 로컬로 설치하려면 [Docker 설치 지침][Docker 설치 지침]을 따르세요. 대부분의 운영 체제와 배포에서 `apt-get install docker.io`를 입력하면 됩니다. Docker 버전이 1.0 이상인지 확인합니다.

사용자 컴퓨터에 azure-cli 프롬프트를 설치하고 Azure 계정에 연결한 다음 Docker를 설치했습니다. Azure에 새 Docker 호스트 VM을 만들려면 [Azure Linux VM 에이전트][Azure Linux 에이전트]가 설치되어 있는 Linux VM 이미지가 필요합니다. 현재 이 에이전트가 이미 설치되어 있는 이미지는 다음 중 하나뿐입니다.

-   이미지 갤러리의 Ubuntu 이미지 또는

-   Azure Linux VM 에이전트를 설치 및 구성하여 만든 사용자 지정 Linux 이미지. Azure VM 에이전트를 사용하여 고유한 사용자 지정 Linux VM을 작성하는 방법에 대한 자세한 내용은 [Azure Linux VM 에이전트][Azure Linux 에이전트]를 참조하세요.

azure-cli 명령 프롬프트에 다음을 입력하여 VM 갤러리에서 사용할 최근 Ubuntu 이미지를 찾습니다.

`azure vm image list | grep Ubuntu-14_04`

나열된 최근 이미지 중 하나의 이름을 복사할 준비를 합니다. 명령 프롬프트에 다음을 입력합니다.

    azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20140729-alpha2-ko-kr-30GB" <username> <password>

설명:

-   *<vm-cloudservice name>*은 Azure의 Docker 컨테이너 호스트 컴퓨터가 될 VM의 이름입니다.

-   *<username>*은 VM 기본 루트 사용자의 사용자 이름입니다.

-   *<password>*는 Azure의 복잡성 표준을 충족하는 *username* 계정의 암호입니다.

> [WACOM.NOTE] 현재 암호는 8자 이상으로, 소문자, 대문자, 숫자 및 특수 문자를 각각 한 개씩 포함해야 합니다. 예: !@\#$%^&+= 앞의 문장에서 끝에 있는 마침표는 특수 문자가 아닙니다.

명령이 성공한 경우 사용한 정확한 인수와 옵션에 따라 다음과 같이 표시됩니다.

![][0]

> [WACOM.NOTE] 앞에서 언급한 것처럼 가상 컴퓨터를 만드는 데 몇 분 정도 걸릴 수 있지만 프로비전된 후에는 Docker 데몬이 시작되며 Docker 컨테이너 호스트에 연결할 수 있습니다.

Azure에 만든 Docker VM을 테스트하려면 다음을 입력합니다.

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

여기서 *<vm-name-you-used>*는 `azure vm docker create` 호출에 사용한 가상 컴퓨터의 이름입니다. 호출에 사용한 가상 컴퓨터의 이름입니다. 다시 지정한 특정 인수와 옵션에 따라 다음 응답과 같이 표시됩니다. 이 응답은 Azure에서 Docker 호스트 VM이 작동하여 실행 중이며 명령을 기다리고 있음을 나타냅니다.

![][2]

## Docker 호스트 VM 인증에 대한 참고 사항

Docker VM을 만드는 것뿐 아니라 `azure vm docker create` 명령은 Docker 클라이언트 컴퓨터가 HTTPS를 사용하여 Azure 컨테이너 호스트에 연결할 수 있도록 필요한 인증서도 자동으로 만듭니다. 인증서는 해당하는 경우 클라이언트와 호스트 컴퓨터 둘 다에 저장됩니다. 이후에 실행할 때는 기존 인증서가 다시 사용되며 새 호스트와 공유됩니다.

기본적으로 인증서는 `~/.docker`에 배치되고 Docker는 포트 **4243**에서 실행되도록 구성됩니다. 다른 포트나 디렉터리를 사용하려는 경우 다음 `azure vm docker create` 명령줄 옵션 중 하나를 사용하여 클라이언트 연결에 다른 포트나 다른 인증서를 사용하도록 Docker 컨테이너 호스트 VM을 구성할 수 있습니다.

    -dp, --docker-port [port]              Port to use for docker [4243]
    -dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]

호스트의 Docker 데몬은 `azure vm docker create` 명령에 의해 생성된 인증서를 사용하여 지정된 포트에서 클라이언트 연결을 수신 대기하고 인증하도록 구성됩니다. 클라이언트 컴퓨터가 Docker 호스트에 액세스하려면 해당 인증서가 있어야 합니다.

> [WACOM.NOTE] 반면, 해당 인증서 없이 실행되는 네트워크 호스트는 컴퓨터에 연결할 수 있는 모든 사용자에게 취약합니다. 기본 구성을 수정하기 전에 컴퓨터와 응용 프로그램에 대한 위험을 이해해야 합니다.

## 다음 단계

이제 Azure의 Docker 호스트 VM에 대해 [Docker 사용자 가이드][Docker 사용자 가이드]의 Docker 명령을 실행할 준비가 되었습니다.

## Linux 및 Windows용 가상 컴퓨터 확장

Azure용 Docker VM 확장은 특수 동작을 제공하는 여러 VM 확장 중 하나일 뿐이며 추가 VM이 개발 중입니다. 예를 들어 보안 기능, 커널 및 네트워킹 기능 등을 비롯한 [Linux VM 에이전트 확장][Azure Linux 에이전트]의 여러 기능을 사용하여 이미지를 수정하고 관리할 수 있습니다. Windows용 VMAccess 확장 이미지를 사용하면 원격 데스크톱 액세스 설정을 재설정하거나 수정하고 관리자 암호를 재설정할 수 있습니다.

전체 목록은 [Azure VM 확장][Azure VM 확장]을 참조하세요.

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [Linux 컨테이너]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux 에이전트]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-linux-agent-user-guide/
  [MS Open Tech 블로그 알림]: http://msopentech.com/blog/2014/08/15/getting_started_docker_on_microsoft_azure/
  [Docker 및 Linux 컨테이너]: #Docker-and-Linux-Containers
  [Azure와 함께 Docker VM 확장을 사용하는 방법]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Linux 및 Windows용 가상 컴퓨터 확장]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Azure용 컨테이너 및 컨테이너 관리 리소스]: #Container-and-Container-Management-Resources-for-Azure
  [하이퍼바이저]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Docker 정의]: https://www.docker.com/whatisdocker/
  [Docker 권장 사항]: https://docs.docker.com/articles/security/
  [역할 기반 보안]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [azure-cli]: https://github.com/Azure/azure-sdk-tools-xplat
  [Boot2Docker]: https://docs.docker.com/installation/windows/
  [여기]: http://azure.microsoft.com/ko-kr/documentation/articles/xplat-cli/#install
  [노드 패키지 관리자(npm)]: http://en.wikipedia.org/wiki/Npm_%28software%29
  [1]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server
  [Mac installer]: http://go.microsoft.com/fwlink/?linkid=252249&clcid=0x409
  [Azure 구독에 연결하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/xplat-cli/#configure
  [Docker 설치 지침]: https://docs.docker.com/installation/#installation
  [!@\#$%^&+=]: mailto:!@#$%^&+=
  [0]: ./media/virtual-machines-docker/dockercreateresults.png
  [2]: ./media/virtual-machines-docker/connectingtodockerhost.png
  [Docker 사용자 가이드]: https://docs.docker.com/userguide/
  [Azure VM 확장]: http://msdn.microsoft.com/ko-kr/library/azure/dn606311.aspx
