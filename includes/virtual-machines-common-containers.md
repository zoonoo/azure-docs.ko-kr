Azure 클라우드 솔루션은 가상 컴퓨터(물리적 컴퓨터 하드웨어의 에뮬레이션)에 구축되어 물리적 하드웨어보다 소프트웨어 배포를 빠르게 패키징하고 리소스 통합을 훨씬 원활하게 수행할 수 있습니다. [Docker](https://www.docker.com) 컨테이너 및 docker 에코시스템은 분산된 소프트웨어를 개발, 제공 및 관리할 수 있는 방법을 크게 확장시켰습니다. 컨테이너의 응용 프로그램 코드는 호스트 VM 및 동일한 VM의 기타 컨테이너에서 격리됩니다. 이 격리로 인해 개발 및 배포 유연성이 증가합니다.

Azure에서는 다음 Docker 값을 제공합니다.

* [수많은](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [다양한](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 방법들로 컨테이너용 Docker 호스트를 상황에 맞게 생성
* [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/)는 **marathon** 및 **swarm** 같은 orchestrator를 사용하여 컨테이너 호스트의 클러스터를 만듭니다.
* [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) 및 [리소스 그룹 템플릿](../articles/resource-group-authoring-templates.md)으로 복잡한 분산 응용 프로그램을 간단하게 배포하고 업데이트
* 여러 독점 및 공개 소스 구성 관리 도구와 통합

Azure에서는 VM 및 Linux 컨테이너를 프로그래밍하여 생성할 수 있습니다. 즉, VM 및 컨테이너 *오케스트레이션* 도구를 사용하여 Virtual Machines(VM) 그룹을 생성하고 Linux 컨테이너 및 현재 [Windows 컨테이너](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) 내에 응용 프로그램을 배포할 수 있습니다.

이 문서는 상위 수준에서 이러한 개념들을 논의할 뿐만 아니라, Azure상의 컨테이너 및 클러스터 사용에 대한 수많은 상세 정보, 자습서, 제품 링크를 담고 있습니다. 개념에 대해 모두 알고 있고 링크만 필요한 경우 [컨테이너 작업 도구](#tools-for-working-with-azure-vms-and-containers)에서 확인할 수 있습니다.

## <a name="the-difference-between-virtual-machines-and-containers"></a>가상 컴퓨터와 컨테이너의 차이
가상 컴퓨터는 [하이퍼바이저](http://en.wikipedia.org/wiki/Hypervisor)가 제공하는 독립된 하드웨어 가상화 환경 내에서 실행됩니다. Azure에서는 [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) 서비스가 모든 작업을 처리합니다. 운영 체제를 선택하여 &mdash;를 구성하거나 사용자 지정 VM 이미지를 업로드하여 Virtual Machines를 생성합니다. Virtual Machines는 오랫동안 테스트되고 "많은 시행착오를 겪은" 기술이기에 OS와 포함된 앱을 관리하고 사용할 수 있는 도구가 많이 있습니다.  VM의 앱은 호스트 OS에 숨겨져 있습니다. VM의 응용 프로그램 또는 사용자의 관점에서 보면 VM은 자치 물리적 컴퓨터로 보입니다.

[Linux 컨테이너](http://en.wikipedia.org/wiki/LXC)와 Docker 도구를 사용하여 생성되고 호스트된 컨테이너는 하이퍼바이저를 사용하여 격리하지 않습니다. 컨테이너를 사용하면, 컨테이너 호스트가 Linux 커널의 프로세스 및 파일 시스템 격리 기능을 사용하여 컨테이너, 해당 앱, 특정 커널 기능 및 자체 격리 파일 시스템에 노출시킵니다. 컨테이너 내부에서 실행되는 앱의 관점에서 보면 해당 컨테이너는 독자적인 OS 인스턴스로 보입니다. 컨테이너 내 앱에서는 해당 컨테이너 외부에 있는 프로세스나 기타 리소스를 볼 수 없습니다.

Docker 컨테이너에 사용되는 리소스는 VM에서 사용하는 것보다 훨씬 적습니다. Docker 컨테이너는 Docker 호스트의 커널을 공유하지 않는 응용 프로그램 격리 및 실행 모델을 사용합니다. 컨테이너는 전체 OS에 포함되지 않기 때문에 디스크 공간이 훨씬 적습니다. 시작 시간과 필수 디스크 공간은 VM에 비해 훨씬 낮습니다.
Windows 컨테이너는 Windows에서 실행되는 앱의 Linux 컨테이너와 같은 동일한 이점을 제공합니다. Windows 컨테이너는 Docker 이미지 형식과 Docker API를 지원하지만 PowerShell을 사용하여 관리할 수도 있습니다. 두 개의 컨테이너 런타임을 Windows 컨테이너, Windows Server 컨테이너 및 Hyper-V 컨테이너에서 사용할 수 있습니다. Hyper-V 컨테이너는 매우 최적화된 VM에서 각 컨테이너를 호스트하여 추가 격리 계층을 제공합니다. Windows 컨테이너에 대한 자세한 내용은 [Windows 컨테이너 정보](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)를 참조하세요. Azure에서 Windows Containers를 시작하려면 [Azure Container Service 클러스터를 배포](/articles/container-service/container-service-deployment.md)하는 방법을 알아봅니다.

## <a name="what-are-containers-good-for"></a>컨테이너의 이점

컨테이너는 다음을 향상시킬 수 있습니다.

* 응용 프로그램 코드의 개발과 광범위한 공유 속도
* 앱을 테스트할 수 있는 속도 및 신뢰도
* 앱을 배포할 수 있는 속도 및 신뢰도

컨테이너는 컨테이너 호스트&mdash;운영 체제, 그리고 Azure(Azure Virtual Machine)에서 실행됩니다. 컨테이너가 아무리 우수해도 호스팅하려면 VM 인프라가 여전히 필요합니다. 그래도 좋은 점은 컨테이너가 어떤 VM에서나 실행된다는 것입니다. 물론 컨테이너가 Linux나 Windows 중 어떤 실행 환경을 선호하는지는 중요합니다.


## <a name="what-are-containers-good-for"></a>컨테이너의 이점
컨테이너는 다양한 작업에서 유용하지만&mdash;[Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/)나 [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)과&mdash;마찬가지로 단일 서비스 및 마이크로 서비스 지향의 분산형 응용 프로그램을 개발할 때 좋습니다. 이러한 응용 프로그램은 크고 강력하게 연결된 구성 요소가 아닌 작고 구성 가능한 요소를 기반으로 설계되기 때문입니다.

특히 Azure와 같은 공용 클라우드 환경에서는 VM이 필요할 때 언제 어디서나 가져다 쓸 수 있기 때문에 더욱 그 진가를 발합니다. 격리, 신속한 배포, 오케스트레이션 도구를 쉽게 가져와 사용할 수 있을 뿐만 아니라 더욱 효율적으로 응용 프로그램 인프라를 결정할 수 있습니다.

예를 들어, 접속 규모가 대단히 큰 분산형 응용 프로그램 배포를 위해 9개의 대규모 Azure VM을 구성한다고 가정해 보겠습니다. 이때 이 응용 프로그램의 구성 요소들을 컨테이너들에 배포할 수 있다면 중복 및 로드 밸런싱을 위해 단 4개의 VM만 사용하고 응용 프로그램 구성 요소를 20개의 컨테이너에 배포하면 됩니다.

물론 이것은 예시에 불과하지만 실제 작업에 적용할 경우 데이터 트래픽이 급증할 때 Azure VM이 아닌 컨테이너를 증대하여 해결할 수 있고 전반적인 CPU 잔여 로드를 전보다 훨씬 효율적으로 사용할 수 있습니다.

이 외에도 마이크로 서비스 접근 방식에 적합하지 않은 다양한 시나리오가 있겠지만 마이크로 서비스와 컨테이너가 언제 도움이 될지에 대해서는 사용자가 잘 판단할 수 있을 것입니다.

### <a name="container-benefits-for-developers"></a>컨테이너가 개발자에게 주는 이점
컨테이너 기술이 모두에게 유용하다는 것은 자명하지만 특히 더 혜택을 볼 수 있는 사람들이 있습니다. Docker 컨테이너의 예를 들어 보겠습니다. 여기에서 Docker에 대해 깊게 논의하지 않더라도(자세한 내용은 [Docker란?](https://www.docker.com/whatisdocker/) 또는 [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)를 참고) Docker 및 Docker 환경은 개발자와 IT 전문가 모두에게 큰 이점을 제공합니다.

개발자가 Docker 컨테이너를 빠르게 받아들인 이유는 무엇보다도 Linux 및 Windows 컨테이너를 쉽게 사용할 수 있기 때문입니다.

* 간단한 증분 명령을 사용하여 배포하기 쉬운 고정 이미지를 생성할 수 있고 이 이미지들을 Docker 파일을 이용해 자동으로 구축할 수 있습니다
* 이 이미지들은 간단한 [Git](https://git-scm.com/) 스타일의 푸시 앤 풀 명령을 사용하여 [공개](https://registry.hub.docker.com/) 또는 [비공개 Docker 레지스트리](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 쉽게 공유할 수 있습니다.
* 또한 컴퓨터가 아닌 격리된 응용 프로그램 구성 요소를 활용하고
* Docker 컨테이너와 다양한 기본 이미지들을 잘 다룰 수 있는 수많은 도구를 사용할 수 있습니다

### <a name="container-benefits-for-operations-and-it-professionals"></a>컨테이너가 운영 및 IT 전문가에게 주는 이점
IT 및 운영 전문가 역시 컨테이너 및 가상 컴퓨터를 조합하여 이점을 얻을 수 있습니다.

* 컨테이너에서 실행되는 서비스는 VM 호스트 실행 환경에서 격리됩니다.
* 컨테이너 내 코드는 동일하게 인증됩니다
* 컨테이너 내 서비스는 개발, 테스트, 프로덕션 환경을 가리지 않고 신속하게 시작, 중지, 이동할 수 있습니다

이 외에도&mdash;대기업에 큰&mdash;도움이 되는 많은 기능들이 있습니다. 대기업은 시장 경쟁력을 유지하고 고객 만족도 및 도달 범위를 증가시키기 위해 전문 정보 기술 조직이&mdash;처리 성능&mdash;및 리소스 최적화 작업을 수행합니다. 소기업, ISV, 신생 기업도 상황이 조금 다르기는 하겠지만 그와 같은 작업이 필요한 것은 마찬가지입니다.

## <a name="what-are-virtual-machines-good-for"></a>가상 컴퓨터의 이점
가상 컴퓨터가 클라우드 컴퓨팅의 토대라는 사실은 변함이 없습니다. 가상 컴퓨터가 점점 느리게 시동되고 디스크 공간에서 차지하는 공간이 늘어나며 마이크로 서비스 아키텍처로 직접 매핑할 수 없어도 여전히 다음과 같은 이점을 제공한다는 점에서 매우 중요합니다.

1. 기본적으로 호스트 컴퓨터에 훨씬 더 강력한 기본 보안 기능 제공
2. 주요 OS 및 응용 프로그램 구성 지원
3. 명령 및 제어에 장기간 사용할 수 있는 도구 환경 제공
4. 호스트 컨테이너의 실행 환경 제공

특히 마지막 항목이 중요합니다. 컨테이너 내 응용 프로그램이 어떤 호출을 보내는가에 따라 특정 운영체제와 CPU 유형이 필요하기 때문입니다. 컨테이너는 VM에 설치된다는 점을 잊지 마십시오. 배포하려는 응용 프로그램은 컨테이너에 들어가지만 컨테이너가 VM이나 운영체제를 대체할 수 있는 것은 아닙니다.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>VM과 컨테이너의 고급 기능 비교
다음 표에서는 VM과 Linux 컨테이너 간&mdash;고급 기능의&mdash;차이에 대해 매우 간략하게 설명합니다. 응용 프로그램 요구 사항에 따라 보다 적합한 기능도 있고 그렇지 않은 기능도 있습니다. 또한 모든 소프트웨어와 마찬가지로 약간 작업을 하면 지원되는 기능을 향상시킬 수 있으며 특히 보안 분야의 기능 지원에서 효과를 볼 수 있습니다.

| 기능 | VM | 컨테이너 |
|:--- | --- | --- |
| “기본” 보안 지원 |높은 수준으로 지원 |약간 낮은 수준으로 지원 |
| 디스크에 필요한 메모리 |전체 OS + 앱 |앱 요구 사항만 적용됨 |
| 시작 소요 시간 |매우 김: OS 부팅 및 앱 로딩 |매우 짧음: 커널이 이미 실행 중이므로 앱만 시작하면 됨 |
| 이식성 |적절한 준비로 이식 가능 |이미지 형식으로 이식 가능. 일반적으로 더 작음 |
| 이미지 자동화 |OS와 앱에 따라 크게 다름 |[Docker 레지스트리](https://registry.hub.docker.com/)및 기타 |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>VM 및 컨테이너 그룹 생성 및 관리
이 시점에서 설계자, 개발자, 또는 IT 운영 전문가라면 "이걸 다 자동화할 수 있다니 진정한 DCaaS(Data-Center-As-A-Service)야!"라고 할지도 모르겠습니다.

맞습니다. 가능합니다. 수많은 시스템이 있고 그 중 상당수를 이미 사용하고 계실지도 모르며 [Windows용 CustomScriptingExtension](https://msdn.microsoft.com/library/azure/dn781373.aspx) 또는 [Linux용 CustomScriptingExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)을 주로 사용해서 Azure VM의 그룹들을 관리하고 사용자 지정 코드를 스크립트로 주입할 수도 있습니다. 이미&mdash;사용하고 있겠지만&mdash;PowerShell 또는 Azure CLI 스크립트를 사용하여 Azure를 배포할 수 있습니다.

이런 기능들은 [Puppet](https://puppetlabs.com/) 및 [Chef](https://www.chef.io/) 같은 도구들로 마이그레이션하여 VM 생성 및 구성을 규모에 맞추어 자동화하는 데 사용되기도 합니다. ( [Azure에서 이러한 도구를 사용하는 방법](#tools-for-working-with-containers)에 대한 몇 가지 링크는 여기에서 확인하실 수 있습니다.)

### <a name="azure-resource-group-templates"></a>Azure 리소스 그룹 템플릿
최근 Azure는 [Azure 리소스 관리](../articles/resource-manager-deployment-model.md) REST API와 업데이트된 PowerShell 및 Azure CLI 도구를 배포하여 사용이 더욱 간편해졌습니다. 다음을 사용하면 Azure 리소스 관리 API와 함께 [Azure 리소스 관리자 템플릿](../articles/resource-group-authoring-templates.md) 으로 전체 응용 프로그램 토폴로지를 배포, 수정 또는 재배포할 수 있습니다.

* [Azure Portal의 템플릿](https://github.com/Azure/azure-quickstart-templates)&mdash;(힌트: "DeployToAzure" 버튼을 사용하세요.)
* [Azure CLI](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure PowerShell 모듈](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>전체 Azure VM 및 컨테이너 그룹의 배포 및 관리
몇몇 인기 시스템에서는 전체 Azure VM 그룹을 배포하고 거기에 Docker 또는 기타 Linux 컨테이너 호스트 시스템을 자동화 가능한 그룹으로 설치할 수 있습니다. 바로 가기 링크는 아래의 [컨테이너 및 도구](#containers-and-vm-technologies) 섹션에서 확인하시기 바랍니다. 이러한 작업이 가능한 시스템은 성능 수준 면에서 다양하며 여기에 소개된 목록이 전부가 아닙니다. 또한 이 시스템들은 사용자의 기술과 시나리오에 따라 유용할 수도, 유용하지 않을 수도 있습니다.

Docker는 그 자체에 VM 생성 도구([Docker 컴퓨터](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))와 로드 밸런싱 Docker 컨테이너 클러스터 관리 도구([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))가 있습니다. 또한 [Azure Docker VM Extension](https://github.com/Azure/azure-docker-extension/blob/master/README.md)이 [`docker-compose`](https://docs.docker.com/compose/)를 위한 기본 지원에 포함되기 때문에 구성된 응용 프로그램 컨테이너를 여러 컨테이너에 배포할 수 있습니다.

[Mesosphere의 DCOS(데이터 센터 운영체제)](http://docs.mesosphere.com/install/azurecluster/)도 사용해 볼만 합니다. DCOS는 "분산형 시스템 커널"인 공개 소스 [Mesos](http://mesos.apache.org/) 에 기반하여 데이터 센터를 주소 지정이 가능한 단일 서비스로 사용할 수 있습니다. DCOS에는 [Spark](http://spark.apache.org/), [Kafka](http://kafka.apache.org/) 등의 여러 중요한 시스템에 기본 제공되는 패키지뿐만 아니라 [Marathon](https://mesosphere.github.io/marathon/)(컨테이너 제어 시스템) 및 [Chronos](https://mesos.github.io/chronos/)(분산형 스케줄러) 같은 기본 제공 서비스가 있습니다. Mesos는 Twitter, AirBnb, 기타 큰 웹 비즈니스의 경험을 바탕으로 탄생했습니다. **swarm** 을 오케스트레이션 엔진으로 사용할 수도 있습니다.

[kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) 역시 Google의 경험에 기반하여 탄생한 VM 및 컨테이너 그룹 관리용 공개 소스 시스템입니다. [kubernetes는 Weave와 함께 사용하여 네트워킹 지원](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)에 사용할 수도 있습니다.

[Deis](http://deis.io/overview/) 는 공개 소스 "PaaS"(Platform-as-a-Service)로, 사용자 서버에서 응용 프로그램을 손쉽게 배포하고 관리하는 데 사용됩니다. Deis는 Docker 및 CoreOS에 구축되어 Heroku 스타일의 워크플로우와 함께 가벼운 PaaS를 제공합니다. [3개 노드의 Azure VM 그룹을 간단하게 만들어 Deis를](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Azure에 설치한 후 [Hello World Go 응용 프로그램을 설치](../articles/virtual-machines/linux/deis-cluster.md#deploy-and-scale-a-hello-world-application)할 수 있습니다.

Linux에서 배포한 [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)는 최적화된 공간을 차지하고 Docker를 지원하며 [rkt](https://github.com/coreos/rkt)라 불리는 자체 컨테이너 시스템을 가지고 있으며 [fleet](https://coreos.com/using-coreos/clustering/)라 불리는 컨테이너 그룹 관리 도구도 제공합니다.

또 다른 인기 Linux 제품인 Ubuntu는 Docker를 매우 적극적으로 지원하며 [Linux(LXC 스타일) 클러스터](https://help.ubuntu.com/lts/serverguide/lxc.html)도 지원합니다.

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Azure VM 및 컨테이너와 호환되는 도구
컨테이너와 Azure VM으로 작업 시 도구가 필요합니다. 이 섹션에서는 컨테이너, 그룹, 더 큰 규모의 구성 및 오케스트레이션 도구에 관련된 가장 유용하거나 중요한 개념과 도구를 일부만 제시하겠습니다.

> [!NOTE]
> 이 영역은 매우 급속하게 변화하고 있습니다. 이 항목과 그 링크들을 늘 최신으로 유지하기 위해 노력하겠지만, 이는 사실 불가능할 수도 있습니다. 최신 정보를 놓치지 않기 위해서 꼭 검색을 하기 바랍니다!
>
>

### <a name="containers-and-vm-technologies"></a>컨테이너 및 VM 기술
일부 Linux 컨테이너 기술:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS 및 rkt](https://github.com/coreos/rkt)
* [공개 컨테이너 프로젝트](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Windows 컨테이너 링크:

* [Windows 컨테이너](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Visual Studio Docker 링크:

* [Visual Studio 2015 RC Tools for Docker - 미리 보기](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker 도구:

* [Docker 데몬](https://docs.docker.com/installation/#installation)
* Docker 클라이언트
  * [Chocolatey의 Windows Docker 클라이언트](https://chocolatey.org/packages/docker)
  * [Docker 설치 지침](https://docs.docker.com/installation/#installation)

Microsoft Azure의 Docker:

* [Azure의 Linux용 Docker VM 확장](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Docker VM 확장 프로그램 사용자 가이드](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Azure 명령줄 인터페이스(Azure CLI)에서 Docker VM 확장 사용](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure 포털에서 Docker VM 확장 사용](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure에서 docker-machine을 사용하는 방법](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure에서 swarm과 함께 Docker를 사용하는 방법](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 가상 컴퓨터에서 Docker 및 Compose 시작](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 리소스 그룹 템플릿을 사용하여 Azure에서 신속하게 Docker 호스트 생성](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* 컨테이너 내 응용 프로그램에 대해 [를 기본적으로 지원`compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys)
* [Azure에서 Docker 개인 레지스트리 구현](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Linux 배포 도구 및 Azure 예시:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

구성, 클러스터 관리, 컨테이너 오케스트레이션:

* [CoreOS의 Fleet](https://coreos.com/using-coreos/clustering/)
* Deis

  * [3개 노드의 Azure VM 그룹 생성, Azure에 Deis 설치, Hello World Go 응용 프로그램 설치 시작](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* kubernetes

  * [CoreOS 및 Weave로 자동화된 Kubernetes 클러스터에 대한 완벽한 가이드](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Mesosphere의 데이터 센터 운영체제(DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) 및 [Hudson](http://hudson-ci.org/)

  * [블로그: Azure용 Jenkins Slave 플러그인](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [GitHub 리포지토리: Azure용 Jenkins 저장소 플러그인](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [타사: Azure용 Hudson 슬레이브 플러그인](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [타사: Azure용 Hudson Storage 플러그인](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Azure Automation](https://azure.microsoft.com/services/automation/)

  * [비디오: Linux VM에서 Azure 자동화를 사용하는 방법](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* Linux용 Powershell DSC

  * [블로그: Linux용 Powershell DSC 작업 방법](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: Docker 클라이언트 DSC](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>다음 단계
[Docker](https://www.docker.com) 및 [Windows 컨테이너](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)를 확인하세요.

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
