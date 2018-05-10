---
title: Azure Service Fabric에 컨테이너의 .NET 앱 배포 | Microsoft Docs
description: Docker 컨테이너의 Visual Studio에서 .NET 앱을 패키지하는 방법을 설명합니다. 그런 다음 이 새로운 "컨테이너" 앱은 Service Fabric 클러스터에 배포됩니다.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 04a6fbc56d3c65cfb53339c4178dfa36e2aeb4ea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Azure Service Fabric에 Windows 컨테이너로 .NET 응용 프로그램 배포

이 자습서에서는 기존 ASP.NET 응용 프로그램을 Windows 컨테이너로 배포하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Visual Studio에서 Docker 프로젝트 만들기
> * 기존 응용 프로그램 컨테이너화
> * Visual Studio 및 VSTS와의 연속 통합 설정

## <a name="prerequisites"></a>필수 조건

1. Windows 10에서 컨테이너를 실행할 수 있도록 [Windows용 Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)를 설치합니다.
2. [Windows 10 컨테이너 빠른 시작][link-container-quickstart]을 숙지합니다.
3. [Fabrikam Fiber CallCenter][link-fabrikam-github] 응용 프로그램 예제를 다운로드합니다.
4. [Azure PowerShell][link-azure-powershell-install]을 설치합니다.
5. [Visual Studio 2017용 지속적인 업데이트 도구 확장][link-visualstudio-cd-extension]을 설치합니다.
6. [Azure 구독][link-azure-subscription] 및 [Visual Studio Team Services 계정][link-vsts-account]을 만듭니다. 
7. [Azure에서 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Azure에서 클러스터 만들기
Service Fabric 응용 프로그램은 네트워크에 연결된 가상 머신 또는 물리적 머신의 집합인 클러스터에서 실행됩니다. 응용 프로그램을 만들어서 배포하기 전에 [Azure에서 실행되는 Service Fabric 클러스터를 설정합니다](service-fabric-tutorial-create-vnet-and-windows-cluster.md). 클러스터를 만들 때 컨테이너 실행을 지원하는 SKU를 선택합니다(예: 컨테이너가 있는 Windows Server 2016 Datacenter).

## <a name="containerize-the-application"></a>응용 프로그램 컨테이너화

이제 Azure에서 실행되는 서비스 패브릭 클러스터가 있으니, 컨테이너화된 응용 프로그램을 만들고 배포할 준비가 되었습니다. 컨테이너에서 응용 프로그램 실행을 시작하려면 Visual Studio에서 **Docker 지원**을 프로젝트에 추가해야 합니다. 응용 프로그램에 **Docker 지원**을 추가하면 두 가지 동작이 수행됩니다. 첫째, _Dockerfile_ 파일이 프로젝트에 추가됩니다. 이 새로운 파일은 컨테이너 이미지를 빌드하는 방법을 설명합니다. 둘째, 새로운 _docker-compose_ 프로젝트가 솔루션에 추가됩니다. 새 프로젝트에는 몇 가지 docker-compose 파일이 포함되어 있습니다. Docker-compose 파일을 사용하여 컨테이너가 실행되는 방법을 설명할 수 있습니다.

[Visual Studio 컨테이너 도구][link-visualstudio-container-tools] 작업에 대한 자세한 정보

### <a name="add-docker-support"></a>Docker 지원 추가

Visual Studio에서 [FabrikamFiber.CallCenter.sln][link-fabrikam-github] 파일을 엽니다.

**FabrikamFiber.Web** 프로젝트 > **추가** > **Docker 지원**을 마우스 오른쪽 단추로 클릭합니다.

### <a name="add-support-for-sql"></a>SQL 지원 추가

이 응용 프로그램은 SQL을 데이터 공급자로 사용하므로 응용 프로그램을 실행하려면 SQL Server가 필요합니다. docker-compose.override.yml 파일의 SQL Server 컨테이너 이미지를 참조하세요.

Visual Studio에서 **솔루션 탐색기**를 열고 **docker-compose**를 찾아 **docker-compose.override.yml** 파일을 엽니다.

`services:` 노드로 이동한 다음, 컨테이너에 대한 SQL Server 항목을 정의하는 `db:`라는 노드를 추가합니다.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>호스트에서 연결할 수 있기만 하면 어떤 SQL Server든지 로컬 디버깅에 사용할 수 있습니다. 그러나 **localdb**는 `container -> host` 통신을 지원하지 않습니다.

>[!WARNING]
>컨테이너에서 SQL Server를 실행할 때 데이터가 지속되지 않습니다. 컨테이너를 중지하면 데이터가 지워집니다. 프로덕션 환경에서는 이 구성을 사용하지 마세요.

`fabrikamfiber.web:` 노드로 이동하여 `depends_on:`이라는 자식 노드를 추가합니다. 그러면 `db` 서비스(SQL Server 컨테이너)가 웹 응용 프로그램(fabrikamfiber.web)보다 먼저 시작됩니다.

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>웹 구성 업데이트

**FabrikamFiber.Web** 프로젝트로 돌아가 **web.config** 파일의 연결 문자열이 컨테이너의 SQL Server를 가리키도록 업데이트합니다.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>웹 응용 프로그램의 릴리스 버전을 빌드할 때 다른 SQL Server를 사용하려면 web.release.config 파일에 다른 연결 문자열을 추가합니다.

### <a name="test-your-container"></a>컨테이너 테스트

**F5** 키를 눌러 컨테이너에서 응용 프로그램을 실행하고 디버그합니다.

내부 NAT 네트워크의 컨테이너 IP 주소(일반적으로 172.x.x.x)를 사용하여 응용 프로그램의 정의된 시작 페이지가 Edge에서 열립니다. Visual Studio 2017을 사용하여 컨테이너의 응용 프로그램을 디버그하는 방법에 대한 자세한 내용은 [이 문서][link-debug-container]를 참조하세요.

![컨테이너의 fabrikam 예][image-web-preview]

이제 컨테이너를 빌드하고 Service Fabric 응용 프로그램에 패키지할 준비가 되었습니다. 컴퓨터에서 컨테이너 이미지를 빌드한 후 컨테이너 레지스트리에 푸시하고 실행할 호스트로 끌어올 수 있습니다.

## <a name="get-the-application-ready-for-the-cloud"></a>클라우드에 대해 응용 프로그램 준비

Azure의 Service Fabric에서 응용 프로그램을 실행할 수 있도록 준비하려면 다음 두 단계를 완료해야 합니다.

1. Service Fabric 클러스터에서 웹 응용 프로그램에 연결할 수 있도록 할 포트를 노출합니다.
2. 응용 프로그램을 위해 프로덕션 준비가 된 SQL 데이터베이스를 제공합니다.

### <a name="expose-the-port-for-the-app"></a>앱에 대해 포트 노출
구성한 Service Fabric 클러스터는 기본적으로 Azure Load Balancer에서 포트 *80*을 열어 들어오는 트래픽과 클러스터의 균형을 조정합니다. docker-compose.yml 파일을 통해 이 포트에 컨테이너를 노출시킬 수 있습니다.

Visual Studio에서 **솔루션 탐색기**를 열고 **docker-compose**를 찾아 **docker-compose.yml** 파일을 엽니다.

`fabrikamfiber.web:` 노드를 수정하고 `ports:`라는 자식 노드를 추가합니다.

문자열 항목 `- "80:80"`을 추가합니다. docker-compose.yml 파일은 다음과 같아야 합니다.

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>프로덕션 SQL 데이터베이스 사용
프로덕션 환경에서 실행하는 경우에는 데이터를 데이터베이스에 유지해야 합니다. 현재는 컨테이너에 데이터를 유지할 수 있는 방법이 없으므로 프로덕션 데이터를 컨테이너로 SQL Server에 저장할 수 없습니다.

Azure SQL Database를 활용하는 것이 좋습니다. Azure에서 관리되는 SQL Server를 설정하고 실행하려면 [Azure SQL Database 빠른 시작][link-azure-sql] 문서를 참조하세요.

>[!NOTE]
>**FabrikamFiber.Web** 프로젝트의 **web.release.config** 파일에서 연결 문자열을 SQL Server로 변경해야 합니다.
>
>SQL Database에 연결할 수 없는 경우 이 응용 프로그램은 정상적으로 실패합니다. SQL Server 없이 응용 프로그램을 배포할 수 있습니다.

## <a name="deploy-with-visual-studio-team-services"></a>Visual Studio Team Services를 사용하여 배포

Visual Studio Team Services를 사용하여 배포를 설정하려면 [Visual Studio 2017용 지속적인 업데이트 도구 확장][link-visualstudio-cd-extension]을 설치해야 합니다. Visual Studio Team Services를 구성하여 이 확장을 Azure에 쉽게 배포하고 Service Fabric 클러스터에 앱을 배포할 수 있습니다.

시작하려면 소스 제어에서 코드를 호스트해야 합니다. 이 섹션의 나머지 부분에서는 **git**을 사용 중이라고 가정합니다.

### <a name="set-up-a-vsts-repo"></a>VSTS 리포지토리 설정
Visual Studio의 오른쪽 아래에서 **소스 제어에 추가** > **Git**(또는 원하는 옵션)를 클릭합니다.

![소스 제어 단추를 누릅니다.][image-source-control]

_팀 탐색기_ 창에서 **Git 리포지토리 게시**를 누릅니다.

VSTS 리포지토리 이름을 선택하고 **리포지토리**를 누릅니다.

![VSTS에 리포지토리 게시][image-publish-repo]

이제 코드가 VSTS 소스 리포지토리와 동기화되었으므로 연속 통합 및 지속적인 업데이트를 구성할 수 있습니다.

### <a name="setup-continuous-delivery"></a>지속적인 업데이트 설정

_솔루션 탐색기_에서 **솔루션** > **지속적인 업데이트 구성**을 마우스 오른쪽 단추로 클릭합니다.

Azure 구독을 선택합니다.

**호스트 유형**을 **Service Fabric 클러스터**로 설정합니다.

**대상 호스트**를 이전 섹션에서 만든 Service Fabric 클러스터로 설정합니다.

컨테이너를 게시할 **컨테이너 레지스트리**를 선택합니다.

>[!TIP]
>**편집** 단추를 사용하여 컨테이너 레지스트리를 만듭니다.

**확인**을 누릅니다.

![Service Fabric 연속 통합 설정][image-setup-ci]
   
   구성이 완료되면 컨테이너가 Service Fabric에 배포됩니다. 리포지토리에 업데이트를 푸시할 때마다 새 빌드 및 릴리스가 실행됩니다.
   
   >[!NOTE]
   >컨테이너 이미지를 빌드하는 데는 약 15분이 소요됩니다.
   >Service Fabric 클러스터에 처음 배포하면 기본 Windows Server Core 컨테이너 이미지가 다운로드됩니다. 다운로드를 완료하는 데 5-10분이 더 소요됩니다.

클러스터 url(*http://mycluster.westeurope.cloudapp.azure.com*)을 사용하여 Fabrikam Call Center 응용 프로그램으로 이동합니다.

이제 Fabrikam Call Center 솔루션을 컨테이너화하고 배포했으므로 [Azure Portal][link-azure-portal]을 열고 응용 프로그램이 Service Fabric에서 실행되는 것을 확인할 수 있습니다. 응용 프로그램을 실행하려면 웹 브라우저를 열고 Service Fabric 클러스터의 URL로 이동합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Visual Studio에서 Docker 프로젝트 만들기
> * 기존 응용 프로그램 컨테이너화
> * Visual Studio 및 VSTS와의 연속 통합 설정

자습서의 다음 부분에서는 [컨테이너에 대한 모니터링](service-fabric-tutorial-monitoring-wincontainers.md)을 설정하는 방법을 알아봅니다.

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
