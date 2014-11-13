<properties title="Azure의 Linux용 Docker 가상 컴퓨터 확장" pageTitle="Azure의 Linux용 Docker 가상 컴퓨터 확장" description="Docker와 컨테이너 및 Azure 가상 컴퓨터 확장에 대해 설명하고 xplat-cli와 포털에서 Docker 컨테이너를 만드는 방법을 설명하는 추가 리소스 링크를 제공합니다." metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />

# Azure의 Linux용 Docker 가상 컴퓨터 확장

[Docker][Docker]는 공유 리소스의 데이터와 계산을 격리시키는 한 가지 방법으로 가상 컴퓨터 대신 [Linux 컨테이너][Linux 컨테이너]를 사용하는 가장 많이 사용되는 가상화 방법 중 하나입니다. [Azure Linux 에이전트][Azure Linux 에이전트]에 대한 Docker VM 확장을 사용하여 Azure에 응용 프로그램의 컨테이너를 개수에 제한없이 호스트하는 Docker VM을 만들 수 있습니다.

이 항목에서는 다음에 대해 설명합니다.

-   [Docker 및 Linux 컨테이너][Docker 및 Linux 컨테이너]
-   [Azure와 함께 Docker VM 확장을 사용하는 방법][Azure와 함께 Docker VM 확장을 사용하는 방법]
-   [Linux 및 Windows용 가상 컴퓨터 확장][Linux 및 Windows용 가상 컴퓨터 확장]

Docker 사용 가능 VM을 즉시 만들려면 다음 항목을 참조하세요.

-   [Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법][Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법]
-   [Azure 포털에서 Docker VM 확장을 사용하는 방법][Azure 포털에서 Docker VM 확장을 사용하는 방법]

## <span id="Docker and Linux Containers"></span>Docker 및 Linux 컨테이너</a>

[Docker][Docker]는 공유 리소스의 데이터와 계산을 격리시키는 한 가지 방법으로 가상 컴퓨터 대신 [Linux 컨테이너][Linux 컨테이너]를 사용하는 가장 많이 사용되는 가상화 방법 중 하나이며, 응용 프로그램을 작성 또는 어셈블하고 다른 Docker 컨테이너 간에 분산할 수 있도록 하는 기타 서비스를 제공합니다.

Docker 및 Linux 컨테이너는 [하이퍼바이저][하이퍼바이저](예: Windows Hyper-V 및 Linux의 [KVM][KVM])가 아닙니다(다른 많은 예가 있음). 하이퍼바이저는 기본 운영 체제를 가상화하여 전체 운영 체제를 응용 프로그램처럼 하이퍼바이저 내부에서 실행할 수 있도록 합니다.

Docker 및 기타 *컨테이너* 접근 방법은 Linux 커널의 프로세스 및 파일 시스템 격리 기능을 통해 달리 격리된 컨테이너에 커널 기능만 노출하여 사용되는 시작 시간과 필요한 처리 및 저장소 오버헤드 둘 다를 크게 줄였습니다.

다음 표에서는 하이퍼바이저와 Linux 컨테이너 간의 기능 차이에 대해 매우 간략하게 설명합니다. 개별 응용 프로그램 요구 사항에 따라 보다 적합한 기능도 있고 그렇지 않은 기능도 있습니다.

| 기능                   | 하이퍼바이저                         | 컨테이너                                                  |
|------------------------|--------------------------------------|-----------------------------------------------------------|
| 프로세스 격리          | 어느 정도 완전함                     | 루트를 가져오는 경우 컨테이너 호스트가 손상될 수 있음     |
| 디스크에 필요한 메모리 | 전체 OS + 앱                         | 앱 요구 사항만 적용됨                                     |
| 시작 소요 시간         | 매우 김: OS 부팅 시간 + 앱 로딩 시간 | 매우 짧음: 커널이 이미 실행되고 있으므로 앱만 시작되면 됨 |
| 컨테이너 자동화        | OS와 앱에 따라 크게 다름             | [Docker 이미지 갤러리][Docker 이미지 갤러리]/기타                             |

컨테이너와 해당 이점에 대한 간략한 설명을 확인하려면 [Docker 요약 화이트보드][Docker 요약 화이트보드]를 참조하세요.

Docker 정의 및 실제 작동 방식에 대한 자세한 내용은 [Docker 정의][Docker 정의]를 참조하세요.

#### Docker 및 Linux 컨테이너 보안 모범 사례

컨테이너는 호스트 컴퓨터의 커널에 대한 액세스를 공유하므로 악성 코드가 침투할 수 있으면 호스트 컴퓨터뿐 아니라 다른 컨테이너에도 액세스할 수 있게 됩니다. 컨테이너 시스템 보안을 기본 구성보다 강화하기 위한 [Docker 권장 사항][Docker 권장 사항]에는 그룹 정책 또는 [역할 기반 보안][역할 기반 보안](예: [SELinux][SELinux] 또는 [AppArmor][AppArmor])을 추가로 사용하거나 컨테이너에 부여되는 커널 기능을 최대한 축소하는 방법 등이 있습니다. 또한 Docker와 같은 컨테이너를 사용한 보안 접근 방법을 설명하는 다른 많은 문서가 인터넷에 있습니다.

## <span id="How to use the Docker VM Extension with Azure"></span>Azure와 함께 Docker VM 확장을 사용하는 방법</a>

Docker VM 확장은 사용자가 만드는 VM 인스턴스에 설치되는 구성 요소로, 자체적으로 Docker 엔진을 설치하고 VM과의 원격 통신을 관리합니다. 두 가지 방법으로 VM 확장을 설치할 수 있습니다. 그 중 하나는 관리 포털을 통해 VM을 만드는 것이고, 다른 하나는 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)에서 이미지를 만드는 것입니다.

포털을 사용하면 호환되는 모든 Linux VM에 Docker VM 확장을 추가할 수 있습니다. 현재 확장을 지원하는 이미지는 7월 이후의 Ubuntu 14.04 LTS 이미지뿐입니다. 그러나 xplat-cli 명령줄을 사용하는 경우에는 VM 인스턴스를 만들 때 Docker VM 확장 설치 및 Docker 통신 인증서 업로드를 모두 동시에 수행할 수 있습니다.

Docker 사용 가능 VM을 즉시 만들려면 다음 항목을 참조하세요.

-   [Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법][Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법]
-   [Azure 포털에서 Docker VM 확장을 사용하는 방법][Azure 포털에서 Docker VM 확장을 사용하는 방법]

## <span id="Virtual Machine Extensions for Linux and Windows"></span>Linux 및 Windows용 가상 컴퓨터 확장</a>

Azure용 Docker VM 확장은 특수 동작을 제공하는 여러 VM 확장 중 하나일 뿐이며 추가 VM이 개발 중입니다. 예를 들어 보안 기능, 커널 및 네트워킹 기능 등을 비롯한 [Linux VM 에이전트 확장][Azure Linux 에이전트]의 여러 기능을 사용하여 이미지를 수정하고 관리할 수 있습니다. Windows용 VMAccess 확장 이미지를 사용하면 원격 데스크톱 액세스 설정을 재설정하거나 수정하고 관리자 암호를 재설정할 수 있습니다.

전체 목록은 [Azure VM 확장][Azure VM 확장]을 참조하세요.

<!--Anchors-->

  [Docker]: https://www.docker.com/
  [Linux 컨테이너]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux 에이전트]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-linux-agent-user-guide/
  [Docker 및 Linux 컨테이너]: #Docker-and-Linux-Containers
  [Azure와 함께 Docker VM 확장을 사용하는 방법]: #How-to-use-the-Docker-VM-Extension-with-Azure
  [Linux 및 Windows용 가상 컴퓨터 확장]: #Virtual-Machine-Extensions-For-Linux-and-Windows
  [Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)에서 Docker VM 확장을 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Azure 포털에서 Docker VM 확장을 사용하는 방법]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-docker-with-portal/
  [하이퍼바이저]: http://en.wikipedia.org/wiki/Hypervisor
  [KVM]: http://www.linux-kvm.org/page/Main_Page
  [Docker 이미지 갤러리]: https://registry.hub.docker.com/
  [Docker 요약 화이트보드]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Docker 정의]: https://www.docker.com/whatisdocker/
  [Docker 권장 사항]: https://docs.docker.com/articles/security/
  [역할 기반 보안]: http://en.wikipedia.org/wiki/Role-based_access_control
  [SELinux]: http://selinuxproject.org/page/Main_Page
  [AppArmor]: http://wiki.apparmor.net/index.php/Main_Page
  [Azure VM 확장]: http://msdn.microsoft.com/ko-kr/library/azure/dn606311.aspx
