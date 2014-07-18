<properties  linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="When to Choose Azure Web Sites, Cloud Services, or Virtual Machines" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

# Azure 웹 사이트, 클라우드 서비스 및 가상 컴퓨터 비교

Azure는 [Azure 웹 사이트][1], [클라우드 서비스][2], [가상 컴퓨터][3] 등 웹 응용 프로그램을 호스트하는 여러 가지 방법을 제공합니다. 이러한 여러 옵션을 살펴본 후에도 어떤 옵션이 요구에 가장 맞는지 잘 모르겠거나 IaaS 및 PaaS와 같은 개념이 명확하지 않을 수 있습니다. 이 문서에서는 옵션을 이해하고 웹 시나리오에 가장 적합한 옵션을 선택할 수 있도록 도와줍니다. 세 가지 옵션 모두 Azure에서 고확장성 웹 응용 프로그램을 실행할 수 있게 해 주지만 결정에 도움이 되는 차이점이 있습니다.

대부분의 경우 최상의 옵션은 Azure 웹 사이트입니다. 간단하고 유연한 배포 및 관리 옵션을 제공하며 높은 볼륨의 웹 사이트를 호스트할 수 있습니다. 웹 응용 프로그램 갤러리에서 WordPress 등의 인기 있는 소프트웨어를 사용하여 새로운 웹 사이트를 빠르게 만들거나 기존 웹 사이트를 Azure 웹 사이트로 이동할 수 있습니다. [Azure WebJobs SDK][4](현재 Preview 상태임)를 사용하여 백그라운드 작업 처리를 추가할 수도 있습니다.

Azure 클라우드 서비스 또는 Azure 가상 컴퓨터에 웹 응용 프로그램을 호스트하는 옵션도 있습니다. 이러한 옵션은 웹 계층에 해당 옵션이 제공하는 추가 수준의 제어 및 사용자 지정이 필요한 경우에 적합하지만 제어가 증가하는 만큼 응용 프로그램 생성, 관리 및 배포의 복잡성도 증가합니다. 다음 다이어그램에서는 세 가지 옵션의 장단점을 보여 줍니다.

![ChoicesDiagram](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

웹 사이트는 설정, 관리 및 모니터링이 용이하지만 구성 옵션이 더 적습니다. 요점은 웹 프런트 엔드를 클라우드 서비스나 가상 컴퓨터에 배치할 특별한 이유가 없는 경우 Azure 웹 사이트를 사용하라는 것입니다. 이 문서의 나머지 부분에서는 합리적 결정에 필요한 정보를 제공합니다. 다음 내용이 포함됩니다.

* [시나리오](#scenarios)
* [서비스 요약](#services)
* [기능 비교](#features)

## <a name="scenarios"></a>시나리오

### 작은 비즈니스 소유자가 사이트를 저렴하게 호스트할 방법을 찾고 있지만 미래 성장도 염두에 두고 있는 경우

Azure 웹 사이트(WAWS)는 처음에 무료로 사용한 후 필요할 때 기능을 추가할 수 있으므로 이 시나리오에 적합한 솔루션입니다. 예를 들어 각 무료 웹 사이트에는 Azure에서 제공하는 도메인(*your\_company*.azurewebsites.net)이 있습니다. 고유 도메인을 사용할 준비가 되면 매월 $9.80(2014년 1월 기준)의 저렴한 비용으로 이 기능을 추가할 수 있습니다. 사용자 수요 증가에 따라 사이트를 발전시킬 수 있는 다른 여러 서비스 및 확장 옵션도 있습니다. **Azure 웹 사이트**를 통해 다음을 수행할 수 있습니다.

* 무료 계층으로 시작한 후 필요에 따라 확장할 수 있습니다.
* 응용 프로그램 갤러리를 사용하여 WordPress 등의 인기 있는 웹 응용 프로그램을 빠르게 설정할 수 있습니다.
* 필요에 따라 Azure 서비스와 기능을 응용 프로그램에 추가할 수 있습니다.
* *your_company*.azurewebsites.net 도메인 이름과 함께 제공된 인증서를 사용하여 HTTPS로 웹 사이트 보안을 유지할 수 있습니다.

### 웹 또는 그래픽 디자이너가 고객을 위해 웹 사이트를 디자인 및 구축하려는 경우

웹 개발자에게 Azure 웹 사이트는 정교한 웹 응용 프로그램을 만드는 데 필요한 사항을 제공합니다. 웹 사이트는 Visual Studio, SQL 데이터베이스 등의 도구와 긴밀한 통합을 제공합니다. **웹 사이트**를 통해 개발자는 다음을 수행할 수 있습니다.

* [자동화된 작업][5](영문)을 위해 명령줄 도구를 사용할 수 있습니다.
* [.Net][6], [PHP][7], [Node.js][8], [Python][9] 등의 인기 있는 언어로 작업할 수 있습니다.
* 매우 높은 기능까지 확장하기 위해 세 가지 확장 수준을 선택할 수 있습니다.
* [SQL 데이터베이스][10], [Service Bus][11], [저장소][12] 등의 다른 Azure 서비스 및 MySQL, MongoDB 등 [Azure 스토어][13]의 파트너 제공 항목과 통합할 수 있습니다.
* Visual Studio, Git, WebMatrix, WebDeploy, TFS, FTP 등의 도구와 통합할 수 있습니다.

### 웹 프런트 엔드가 있는 다중 계층 응용 프로그램을 클라우드로 마이그레이션하는 경우

웹 사이트 데이터를 저장 및 검색하기 위해 데이터베이스 서버와 통신하는 웹 서버 등의 다중 계층 응용 프로그램을 실행하는 경우
Azure에서 사용할 수 있는 여러 가지 옵션이 있습니다. 이러한 아키텍처 옵션에는 웹 사이트, 클라우드 서비스 및 가상 컴퓨터가 포함됩니다. 먼저 **웹 사이트**는 솔루션의 웹 계층에 적합한 옵션이며, Azure SQL 데이터베이스와 함께 사용되어 2계층 아키텍처를 만들 수 있습니다. 웹 사이트를 통해 Azure WebJobs SDK Preview를 사용하여 백그라운드 또는 장기 실행 프로세스를 실행할 수도 있습니다. 더 복잡한 아키텍처나 더 유연한 확장 옵션이 필요한 경우 클라우드 서비스나 가상 컴퓨터를 선택하는 것이 좋습니다.

**클라우드 서비스**를 통해 다음을 수행할 수 있습니다.

* 확장 가능한 웹 및 작업자 역할에서 웹, 중간 계층 및 백 엔드 서비스를 호스트할 수 있습니다.
* 작업자 역할에서 중간 계층 및 백 엔드 서비스만 호스트하고 프런트 엔드는 Azure 웹 사이트에 유지할 수 있습니다.
* 프런트 엔드 및 백 엔드 서비스를 독립적으로 확장할 수 있습니다.

**가상 컴퓨터**를 통해 다음을 수행할 수 있습니다.

* 사용자 지정 환경을 가상 컴퓨터 이미지로 쉽게 마이그레이션할 수 있습니다.
* 웹 사이트나 클라우드 서비스에서 구성할 수 없는 소프트웨어 또는 서비스를 실행할 수 있습니다.

### 응용 프로그램이 사용자 지정 Windows 또는 Linux 환경에 종속된 경우

응용 프로그램에 소프트웨어 및 운영 체제의 복잡한 설치나 구성이 필요한 경우 가상 컴퓨터가 최상의 솔루션일 수 있습니다. **가상
컴퓨터**를 통해 다음을 수행할 수 있습니다.

* 가상 컴퓨터 갤러리를 사용하여 Windows, Linux 등의 운영 체제에서 시작한 후 응용 프로그램 요구 사항에 맞게 사용자 지정할 수 있습니다.
* Azure의 가상 컴퓨터에서 실행되도록 기존 온-프레미스 서버의 사용자 지정 이미지를 만들고 업로드할 수 있습니다.

### 사이트가 오픈 소스 소프트웨어를 사용하며 Azure에서 호스트하려는 경우

세 가지 옵션 모두 오픈 소스 언어와 프레임워크를 호스트할 수 있게 해 줍니다. **클라우드 서비스**를 선택하는 경우 시작 작업을 사용하여 Windows에서 실행되는 필수 오픈 소스 소프트웨어를 설치 및 구성해야 합니다. **가상 컴퓨터**를 선택하는 경우 Windows 또는 Linux 기반일 수 있는 컴퓨터 이미지에 소프트웨어를 설치 및 구성합니다. 웹 사이트에서 해당 오픈 소스 프레임워크가 지원되는 경우 응용 프로그램에 필요한 언어와 프레임워크를 사용하여 웹 사이트를 자동으로 구성할 수 있으므로 이러한 유형의 응용 프로그램을 호스트하는 더 간단한 방법입니다. **웹 사이트**를 통해 다음을 수행할 수 있습니다.

* [.NET][6], [PHP][7], [Node.js][8], [Python][9] 등의 여러 인기 있는 오픈 소스 언어를 사용할 수 있습니다.
* WordPress, Drupal, Umbraco, DNN 및 다른 여러 타사 웹 응용 프로그램을 설정할 수 있습니다.
* 기존 응용 프로그램을 마이그레이션하거나 응용 프로그램 갤러리에서 새로 만들 수 있습니다.

### 회사 네트워크에 연결해야 하는 LOB(기간 업무) 애플리케이션이 있는 경우

LOB(기간 업무) 애플리케이션을 만들려는 경우 웹 사이트에 회사 네트워크의 서비스 또는 데이터에 대한 직접 액세스 권한이 필요할 수 있습니다. 이 기능은 **웹 사이트**, **클라우드 서비스** 및 **가상 컴퓨터**에서 가능합니다. 사용하는 접근 방법에서 다음과 같은 차이점이 있습니다.

* 웹 사이트는 서비스 버스 릴레이를 사용하여 온-프레미스 리소스에 안전하게 연결할 수 있습니다. 따라서 회사 네트워크의 서비스가 모든 항목을 클라우드로 이동하거나 가상 네트워크를 설정하지 않고도 웹 응용 프로그램 대신 작업을 수행할 수 있습니다.
* 클라우드 서비스와 가상 컴퓨터는 가상 네트워크를 이용할 수 있습니다. 실제로 가상 네트워크를 사용하면 Azure에서 실행되는 컴퓨터가 온-프레미스 네트워크에 연결할 수 있습니다. Azure는 회사 데이터 센터의 확장이 됩니다.

### 모바일 클라이언트를 위해 REST API 또는 웹 서비스를 호스트하려는 경우

HTTP 기반 웹 서비스를 사용하면 모바일 클라이언트를 포함하여 다양한 클라이언트를 지원할 수 있습니다. ASP.NET Web API와 같은 프레임워크는 REST 서비스를 더 쉽게 만들고 소비할 수 있도록 Visual Studio와 통합됩니다. 이러한 서비스는 웹 끝점에서 노출되므로 Azure의 모든 웹 호스팅 기법을 사용하여 이 시나리오를 지원할 수 있습니다. 그러나 REST API를 호스트하는 데 적합한 옵션은 **웹 사이트**입니다. 웹 사이트를 통해 다음을 수행할 수 있습니다.

* 빠르게 웹 사이트를 만들어 전 세계에 분산된 Azure 데이터 센터 중 하나에서 HTTP 웹 서비스를 호스트할 수 있습니다.
* Visual Studio의 ASP.NET Web API를 이용하여 기존 서비스를 마이그레이션하거나 새로 만들 수 있습니다.
* 단일 인스턴스로 가용성에 대한 SLA를 달성하거나 여러 전용 컴퓨터로 규모를 확장할 수 있습니다.
* 게시된 사이트를 사용하여 모바일 클라이언트를 비롯한 모든 HTTP 클라이언트에 REST API를 제공할 수 있습니다.

## <a name="services"></a>서비스 요약

[Azure 웹 사이트][1]를 사용하면 Azure에서 고확장성 웹 사이트를 빠르게 구축할 수 있습니다. Azure 포털이나 명령줄 도구를 사용하여 .NET, PHP, Node.js, Python 등의 인기 있는 언어로 웹 사이트를 설정할 수 있습니다.
지원되는 프레임워크는 이미 배포되었으며 추가 설치 단계가 필요 없습니다. Azure 웹 사이트 갤러리에는 Drupal, WordPress 등의 여러 타사 응용 프로그램과 Django, CakePHP 등의 개발 프레임워크가 포함되어 있습니다. 사이트를 만든 후 기존 웹 사이트를 마이그레이션하거나 완전히 새로운 웹 사이트를 구축할 수 있습니다. 웹 사이트는 물리적 하드웨어를 관리할 필요가 없으며 여러 가지 확장 옵션도 제공합니다. 공유 다중 테넌트 모델에서 전용 컴퓨터가 들어오는 트래픽을 서비스하는 표준 모드로 이동할 수 있습니다. 웹 사이트를 통해 SQL 데이터베이스, 서비스 버스, 저장소 등의 다른 Azure 서비스와 통합할 수도 있습니다. [Azure WebJobs SDK][4] Preview를 사용하여 후순위 처리를 추가할 수 있습니다. 요약하면 Azure 웹 사이트는 다양한 언어, 오픈 소스 응용 프로그램 및 배포 방법론(FTP, Git, 웹 배포 또는 TFS)을 지원하여 응용 프로그램 개발에 더 쉽게 집중할 수 있게 해 줍니다. 클라우드 서비스나 가상 컴퓨터가 필요한 특수 요구 사항이 없는 경우 대체로 Azure 웹 사이트가 최상의 옵션입니다.

[클라우드 서비스][2]를 사용하면 풍부한 PaaS(Platform as a Service) 환경에서 확장 가능한 고가용성 웹 응용 프로그램을 만들 수 있습니다. 웹 사이트와 달리 클라우드 서비스는 Azure에 배포되기 전에 먼저 Visual Studio 등의 개발 환경에서 만들어집니다. PHP 등의 프레임워크에는 역할 시작 시 프레임워크를 설치하는 사용자 지정 배포 단계나 작업이 필요합니다. 클라우드 서비스의 주요 장점은 더 복잡한 다중 계층 아키텍처를 지원할 수 있다는 것입니다. 단일 클라우드 서비스는 하나의 프런트 엔드 웹 역할과 하나 이상의 작업자 역할로 구성될 수 있습니다. 각 계층을 독립적으로 확장할 수 있습니다. 웹 응용 프로그램 인프라보다 높은 수준의 제어도 있습니다. 예를 들어 역할 인스턴스를 실행하는 컴퓨터에 원격 데스크톱으로 액세스할 수 있습니다. 관리자 제어가 필요한 작업을 포함하여 역할 시작 시 실행되는 고급 IIS 및 컴퓨터 구성 변경을 스크립팅할 수도 있습니다.

[가상 컴퓨터][3]를 사용하면 Azure의 가상 컴퓨터에서 웹 응용 프로그램을 실행할 수 있습니다. 이 기능을 IaaS(Infrastructure as a Service)라고도 합니다. 포털을 통해 새로운 Windows Server 또는 Linux 컴퓨터를 만들거나 기존 가상 컴퓨터 이미지를 업로 합니다. 가상 컴퓨터는 운영 체제, 구성, 설치된 소프트웨어 및 서비스에 대한 가장 강력한 제어를 제공합니다. 컴퓨터 전체를 이동할 수 있기 때문에 이 옵션은 복잡한 온-프레미스 웹 응용 프로그램을 클라우드로 빠르게 마이그레이션하는 데 적합합니다. 가상 네트워크를 사용하여 이러한 가상 컴퓨터를 온-프레미스 회사 네트워크에 연결할 수도 있습니다. 클라우드 서비스와 마찬가지로 이러한 컴퓨터에 원격으로 액세스할 수 있으며 관리자 수준에서 구성을 변경할 수도 있습니다. 그러나 웹 사이트 및 클라우드 서비스와 달리 인프라 수준에서 가상 컴퓨터 이미지와 응용 프로그램 아키텍처를 완전히 관리해야 합니다. 기본적인 예로 운영 체제에 고유한 패치를 적용해야 합니다.

## <a name="features"></a>기능 비교

다음 표에서는 최상의 옵션 선택에 도움이 되도록 웹 사이트, 클라우드 서비스 및 가상 컴퓨터의 기능을 비교합니다. 별표가 있는 상자는 표 뒤에 있는 참고에서 자세히 설명합니다.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">기능</th>
   <th  align="left" valign="middle">웹 사이트</th>
   <th  align="left" valign="middle">클라우드 서비스(웹 역할)</th>
   <th  align="left" valign="middle">가상 컴퓨터</th>
</tr>
<tr>
   <td  valign="middle"><p>서비스 버스, 저장소, SQL 데이터베이스 등의 서비스에 대한 액세스</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>다중 계층 아키텍처의 웹 또는 웹 서비스 계층 호스트</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>다중 계층 아키텍처의 중간 계층 호스트</p></td>
   <td  valign="middle" />
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>통합된 MySQL-as-a-Service 지원</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X <sup>1</sup></td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>ASP.NET, 클래식 ASP, Node.js, PHP, Python 지원</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>다시 배포하지 않고 여러 인스턴스로 규모 확장</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X <sup>2</sup></td>
</tr>
<tr>
   <td  valign="middle"><p>SSL 지원</p></td>
   <td  valign="middle">X <sup>3</sup></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>Visual Studio 통합</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>원격 디버깅</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>TFS를 사용하여 코드 배포</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>GIT, FTP를 사용하여 코드 배포</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>웹 배포를 사용하여 코드 배포</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle"><sup>4</sup></td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>WebMatrix 지원</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>빠른 배포</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
   <td  valign="middle" />
</tr>
<tr>
   <td  valign="middle"><p>인스턴스에서 콘텐츠 및 구성 공유</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
   <td  valign="middle" />
</tr>
<tr>
   <td  valign="middle"><p>다시 배포하지 않고 대규모 컴퓨터로 확장</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
   <td  valign="middle" />
</tr>
<tr>
   <td  valign="middle"><p>여러 배포 환경(프로덕션 및 스테이징)</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
</tr>
<tr>
   <td  valign="middle"><p>Azure 가상 네트워크를 사용한 네트워크 격리</p></td>
   <td  valign="middle" />
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>Azure 트래픽 관리자 지원</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>서버에 대한 원격 데스크톱 액세스</p></td>
   <td  valign="middle" />
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>시작 작업 정의/실행 기능</p></td>
   <td  valign="middle" />
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>자동 OS 업데이트 관리</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
</tr>
<tr>
   <td  valign="middle"><p>통합된 끝점 모니터링</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
</tr>
<tr>
   <td  valign="middle"><p>원활한 플랫폼 전환(32비트/64비트)</p></td>
   <td  valign="middle">X</td>
   <td  valign="middle">X</td>
   <td  valign="middle" />
</tr>
</table>

<sup>1</sup> 웹 또는 작업자 역할은 ClearDB 제공 항목을 통해 MySQL-as-a-Service를 통합할 수 있지만 관리 포털 워크플로에는 포함되지 않습니다.

<sup>2</sup> 가상 컴퓨터는 여러 인스턴스로 규모 확장될 수 있지만 이러한 컴퓨터에서 실행되는 서비스가 이 규모 확장을 처리하도록 작성되어야 합니다. 컴퓨터 간에 요청을 라우팅하기 위해 추가 부하 분산 장치를 구성해야 합니다. 마지막으로, 유지 관리 또는 하드웨어 오류에서 동시에 다시 시작되지 않도록 보호하기 위해 동일한 역할에 참여하는 모든 컴퓨터에 대해 선호도 그룹을 만들어야 합니다.

<sup>3</sup> 웹 사이트의 경우 사용자 지정 도메인 이름에 대한 SSL은 표준 모드에서만 지원됩니다. 웹 사이트에 SSL을 사용하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 SSL 인증서 구성][14](영문)을 참조하십시오.

<sup>4</sup> 웹 배포는 단일 인스턴스 역할에 배포할 때 클라우드 서비스에 대해 지원됩니다. 그러나 프로덕션 역할에서는 여러 인스턴스가 Azure SLA를 충족해야 합니다. 따라서 웹 배포는 프로덕션의 클라우드 서비스에 적합한 배포 메커니즘이 아닙니다.



[1]: http://go.microsoft.com/fwlink/?LinkId=306051
[2]: http://go.microsoft.com/fwlink/?LinkId=306052
[3]: http://go.microsoft.com/fwlink/?LinkID=306053
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[5]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
[6]: http://www.windowsazure.com/en-us/develop/net/
[7]: http://www.windowsazure.com/en-us/develop/php/
[8]: http://www.windowsazure.com/en-us/develop/nodejs/
[9]: http://www.windowsazure.com/en-us/develop/python/
[10]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
[11]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
[12]: http://www.windowsazure.com/en-us/documentation/services/storage/
[13]: http://www.windowsazure.com/en-us/gallery/store/
[14]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/